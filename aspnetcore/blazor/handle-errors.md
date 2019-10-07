---
title: Gestione degli errori nelle app ASP.NET Core Blazor
author: guardrex
description: Scopri in che modo ASP.NET Core Blazor gestisce le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/handle-errors
ms.openlocfilehash: de0a2f74df84f41581ac93dbeec7a5c5e90c6fa2
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207181"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Gestione degli errori nelle app ASP.NET Core Blazor

Di [Steve Sanderson](https://github.com/SteveSandersonMS)

Questo articolo descrive come Blazor gestisce le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a>Reazione del Framework Blazor alle eccezioni non gestite

Il Server Blazor è un Framework con stato. Mentre gli utenti interagiscono con un'app, mantengono una connessione al server noto come *circuito*. Il circuito include istanze di componenti attive, oltre a molti altri aspetti dello stato, ad esempio:

* Output più recente sottoposto a rendering dei componenti.
* Set corrente di delegati di gestione degli eventi che possono essere attivati da eventi lato client.

Se un utente apre l'app in più schede del browser, avrà più circuiti indipendenti.

Blazor considera la maggior parte delle eccezioni non gestite come fatali per il circuito in cui si verificano. Se un circuito viene terminato a causa di un'eccezione non gestita, l'utente può continuare a interagire con l'app ricaricando la pagina per creare un nuovo circuito. I circuiti al di fuori di quello terminato, ovvero circuiti per altri utenti o altre schede del browser, non sono interessati. Questo scenario è simile a un'applicazione desktop che arresta&mdash;l'arresto anomalo dell'app arrestata in modo anomalo, ma altre app non sono interessate.

Un circuito viene terminato quando si verifica un'eccezione non gestita per i motivi seguenti:

* Un'eccezione non gestita spesso lascia il circuito in uno stato non definito.
* L'operazione normale dell'app non può essere garantita dopo un'eccezione non gestita.
* Le vulnerabilità di sicurezza possono essere visualizzate nell'app se il circuito continua.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Gestire le eccezioni non gestite nel codice dello sviluppatore

Per continuare l'applicazione dopo un errore, l'app deve avere la logica di gestione degli errori. Le sezioni successive di questo articolo descrivono le potenziali fonti di eccezioni non gestite.

In produzione, non eseguire il rendering dei messaggi di eccezione del Framework o delle analisi dello stack nell'interfaccia utente. Il rendering dei messaggi di eccezione o delle analisi dello stack potrebbe:

* Divulgare informazioni riservate agli utenti finali.
* Aiutare un utente malintenzionato a individuare i punti deboli in un'app che può compromettere la sicurezza dell'app, del server o della rete.

## <a name="log-errors-with-a-persistent-provider"></a>Registrare gli errori con un provider persistente

Se si verifica un'eccezione non gestita, l'eccezione viene registrata <xref:Microsoft.Extensions.Logging.ILogger> nelle istanze configurate nel contenitore dei servizi. Per impostazione predefinita, le app Blaze registrano nell'output della console con il provider di registrazione della console. Prendere in considerazione la registrazione a una posizione più permanente con un provider che gestisce le dimensioni del log e la rotazione del log. Per altre informazioni, vedere <xref:fundamentals/logging/index>.

Durante lo sviluppo, Blazor invia in genere i dettagli completi delle eccezioni alla console del browser per facilitare il debug. In produzione, gli errori dettagliati nella console del browser sono disabilitati per impostazione predefinita, il che significa che gli errori non vengono inviati ai client, ma i dettagli completi dell'eccezione sono ancora registrati sul lato server. Per altre informazioni, vedere <xref:fundamentals/error-handling>.

È necessario decidere quali eventi imprevisti registrare e il livello di gravità degli eventi imprevisti registrati. Gli utenti ostili potrebbero essere in grado di attivare intenzionalmente gli errori. Ad esempio, non registrare un evento imprevisto da un errore in `ProductId` cui viene fornito un oggetto sconosciuto nell'URL di un componente che Visualizza i dettagli del prodotto. Non tutti gli errori devono essere considerati come eventi imprevisti con gravità elevata per la registrazione.

## <a name="places-where-errors-may-occur"></a>Posizioni in cui possono verificarsi errori

Il Framework e il codice dell'app possono attivare eccezioni non gestite in uno dei percorsi seguenti:

* [Creazione di istanze di componenti](#component-instantiation)
* [Metodi del ciclo di vita](#lifecycle-methods)
* [Logica di rendering](#rendering-logic)
* [Gestori eventi](#event-handlers)
* [Eliminazione componenti](#component-disposal)
* [Interoperabilità JavaScript](#javascript-interop)
* [Gestori del circuito](#circuit-handlers)
* [Eliminazione del circuito](#circuit-disposal)
* [Tempistiche](#prerendering)

Le eccezioni non gestite precedenti sono descritte nelle sezioni seguenti di questo articolo.

### <a name="component-instantiation"></a>Creazione di istanze di componenti

Quando Blazor crea un'istanza di un componente:

* Il costruttore del componente viene richiamato.
* Vengono richiamati i costruttori di tutti i servizi non singleton forniti al costruttore del componente tramite [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) la direttiva o l'attributo [[Inject]](xref:blazor/dependency-injection#request-a-service-in-a-component) . 

Un circuito ha esito negativo quando un costruttore eseguito o un `[Inject]` Setter per qualsiasi proprietà genera un'eccezione non gestita. L'eccezione è irreversibile perché il Framework non è in grado di creare un'istanza del componente. Se la logica del costruttore può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.

### <a name="lifecycle-methods"></a>Metodi del ciclo di vita

Durante la durata di un componente, Blazor richiama i metodi del ciclo di vita:

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Se un metodo del ciclo di vita genera un'eccezione, in modo sincrono o asincrono, l'eccezione è fatale per il circuito. Per i componenti che gestiscono gli errori nei metodi del ciclo di vita, aggiungere la logica di gestione degli errori.

Nell'esempio seguente viene `OnParametersSetAsync` chiamato un metodo per ottenere un prodotto:

* Un'eccezione generata nel `ProductRepository.GetProductByIdAsync` metodo viene gestita da un' `try-catch` istruzione.
* Quando il `catch` blocco viene eseguito:
  * `loadFailed`è impostato su `true`, che viene utilizzato per visualizzare un messaggio di errore all'utente.
  * L'errore viene registrato.

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Logica di rendering

Il markup dichiarativo `.razor` in un file componente viene compilato C# in un `BuildRenderTree`metodo denominato. Quando viene eseguito il rendering di `BuildRenderTree` un componente, viene eseguita e compilata una struttura di dati che descrive gli elementi, il testo e i componenti figlio del componente di cui è stato eseguito il rendering.

La logica di rendering può generare un'eccezione. Un esempio di questo scenario si verifica `@someObject.PropertyName` quando viene valutato `@someObject` ma `null`è. Un'eccezione non gestita generata dalla logica di rendering è irreversibile per il circuito.

Per evitare un'eccezione di riferimento null nella logica di rendering, verificare `null` la presenza di un oggetto prima di accedere ai relativi membri. Nell'esempio seguente, `person.Address` non è possibile accedere alle proprietà se `person.Address` è `null`:

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Il codice precedente presuppone che `person` non `null`sia. Spesso, la struttura del codice garantisce la presenza di un oggetto nel momento in cui viene eseguito il rendering del componente. In questi casi, non è necessario verificare la `null` presenza di nella logica di rendering. Nell'esempio precedente, `person` potrebbe essere garantito che esista perché `person` viene creato quando viene creata un'istanza del componente.

### <a name="event-handlers"></a>Gestori eventi

Il codice lato client attiva le chiamate del C# codice quando i gestori eventi vengono creati utilizzando:

* `@onclick`
* `@onchange`
* Altri `@on...` attributi
* `@bind`

Il codice del gestore eventi potrebbe generare un'eccezione non gestita in questi scenari.

Se un gestore eventi genera un'eccezione non gestita, ad esempio una query di database ha esito negativo, l'eccezione è fatale per il circuito. Se l'app chiama il codice che potrebbe non riuscire per motivi esterni, intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.

Se il codice utente non intercetta e gestisce l'eccezione, il Framework registra l'eccezione e termina il circuito.

### <a name="component-disposal"></a>Eliminazione componenti

Un componente può essere rimosso dall'interfaccia utente, ad esempio perché l'utente ha esplorato un'altra pagina. Quando un componente che implementa <xref:System.IDisposable?displayProperty=fullName> viene rimosso dall'interfaccia utente, il Framework chiama il <xref:System.IDisposable.Dispose*> metodo del componente. 

Se il `Dispose` metodo del componente genera un'eccezione non gestita, l'eccezione è fatale per il circuito. Se la logica di eliminazione può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.

Per ulteriori informazioni sull'eliminazione dei componenti, <xref:blazor/components#component-disposal-with-idisposable>vedere.

### <a name="javascript-interop"></a>Interoperabilità JavaScript

`IJSRuntime.InvokeAsync<T>`consente al codice .NET di effettuare chiamate asincrone al runtime JavaScript nel browser dell'utente.

Le condizioni seguenti si applicano alla gestione `InvokeAsync<T>`degli errori con:

* Se una chiamata a `InvokeAsync<T>` ha esito negativo in modo sincrono, si verifica un'eccezione .NET. Una chiamata a `InvokeAsync<T>` My ha esito negativo, ad esempio, perché gli argomenti forniti non possono essere serializzati. Il codice dello sviluppatore deve intercettare l'eccezione. Se il codice dell'app in un gestore eventi o in un metodo del ciclo di vita dei componenti non gestisce un'eccezione, l'eccezione risultante è fatale per il circuito.
* Se una chiamata a `InvokeAsync<T>` ha esito negativo in modo <xref:System.Threading.Tasks.Task> asincrono, .NET ha esito negativo. Una chiamata a `InvokeAsync<T>` potrebbe non riuscire, ad esempio perché il codice sul lato JavaScript genera un'eccezione o restituisce `Promise` un oggetto completato `rejected`come. Il codice dello sviluppatore deve intercettare l'eccezione. Se si usa l'operatore [await](/dotnet/csharp/language-reference/keywords/await) , è consigliabile eseguire il wrapping della chiamata al metodo in un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori. In caso contrario, il codice in errore genera un'eccezione non gestita che è irreversibile per il circuito.
* Per impostazione predefinita, le `InvokeAsync<T>` chiamate a devono essere completate entro un determinato periodo oppure si verifica il timeout della chiamata. Il periodo di timeout predefinito è di un minuto. Il timeout protegge il codice da una perdita di connettività di rete o codice JavaScript che non restituisce mai un messaggio di completamento. Se si verifica il timeout della chiamata, `Task` l'oggetto risultante <xref:System.OperationCanceledException>ha esito negativo con un oggetto. Intercettare ed elaborare l'eccezione con la registrazione.

Analogamente, il codice JavaScript può avviare chiamate a metodi .NET indicati dall' [attributo [JSInvokable]](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions). Se questi metodi .NET generano un'eccezione non gestita:

* L'eccezione non viene trattata come irreversibile per il circuito.
* Il lato `Promise` JavaScript viene rifiutato.

È possibile scegliere di usare il codice di gestione degli errori sul lato .NET o sul lato JavaScript della chiamata al metodo.

Per altre informazioni, vedere <xref:blazor/javascript-interop>.

### <a name="circuit-handlers"></a>Gestori del circuito

Blazor consente al codice di definire un *gestore di circuito*, che riceve le notifiche quando cambia lo stato del circuito di un utente. Vengono utilizzati gli Stati seguenti:

* `initialized`
* `connected`
* `disconnected`
* `disposed`

Le `CircuitHandler` notifiche vengono gestite registrando un servizio di che eredita dalla classe di base astratta.

Se i metodi di un gestore di circuito personalizzato generano un'eccezione non gestita, l'eccezione è fatale per il circuito. Per tollerare le eccezioni nel codice di un gestore o i metodi chiamati, eseguire il wrapping del codice in una o più istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.

### <a name="circuit-disposal"></a>Eliminazione del circuito

Quando un circuito termina perché un utente si è disconnesso e il Framework pulisce lo stato del circuito, il Framework Elimina l'ambito di. Con l'eliminazione dell'ambito vengono eliminati tutti i servizi con ambito DI circuito che <xref:System.IDisposable?displayProperty=fullName>implementano. Se un servizio DI INSERIMENTO DI dipendenze genera un'eccezione non gestita durante l'eliminazione, il Framework registra l'eccezione.

### <a name="prerendering"></a>Tempistiche

È possibile eseguire il prerendering dei componenti di `Html.RenderComponentAsync` Blazor usando in modo che il markup HTML sottoposto a rendering venga restituito come parte della richiesta HTTP iniziale dell'utente. Funziona per:

* Creazione di un nuovo circuito contenente tutti i componenti di cui è stato eseguito il rendering che fanno parte della stessa pagina.
* Generazione del codice HTML iniziale.
* Trattare il circuito come `disconnected` fino a quando il browser dell'utente non stabilisce una connessione SignalR allo stesso server per riprendere l'interattività nel circuito.

Se un componente genera un'eccezione non gestita durante il prerendering, ad esempio durante un metodo del ciclo di vita o nella logica di rendering:

* L'eccezione è fatale per il circuito.
* L'eccezione viene generata dallo stack di chiamate dalla `Html.RenderComponentAsync` chiamata. Pertanto, l'intera richiesta HTTP ha esito negativo a meno che l'eccezione non venga intercettata in modo esplicito dal codice dello sviluppatore.

In circostanze normali, quando si verifica un errore di prerendering, continuare a compilare ed eseguire il rendering del componente non ha senso perché non è possibile eseguire il rendering di un componente funzionante.

Per tollerare gli errori che possono verificarsi durante il prerendering, la logica di gestione degli errori deve essere inserita all'interno di un componente che può generare eccezioni. Usare le istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori. Anziché eseguire il wrapping della chiamata `RenderComponentAsync` a in `try-catch` un'istruzione, inserire la logica di gestione degli errori nel `RenderComponentAsync`componente di cui è stato eseguito il rendering.

## <a name="advanced-scenarios"></a>Scenari avanzati

### <a name="recursive-rendering"></a>Rendering ricorsivo

I componenti possono essere annidati in modo ricorsivo. Questa operazione è utile per la rappresentazione di strutture di dati ricorsive. Un `TreeNode` componente, ad esempio, può eseguire `TreeNode` il rendering di più componenti per ognuno dei figli del nodo.

Quando si esegue il rendering in modo ricorsivo, evitare i modelli di codifica che generano una ricorsione

* Non esegue il rendering in modo ricorsivo di una struttura di dati che contiene un ciclo. Ad esempio, non eseguire il rendering di un nodo dell'albero i cui elementi figlio includono se stesso.
* Non creare una catena di layout che contiene un ciclo. Non creare, ad esempio, un layout il cui layout è a sua volta.
* Non consentire a un utente finale di violare le invarianti di ricorsione (regole) tramite immissione di dati dannosi o chiamate di interoperabilità JavaScript.

Cicli infiniti durante il rendering:

* Causa la continuazione del processo di rendering.
* Equivale alla creazione di un ciclo senza terminazione.

In questi scenari, il circuito interessato si blocca e il thread in genere tenta di:

* Utilizzare la quantità di tempo della CPU consentita dal sistema operativo, per un periodo illimitato.
* Utilizzare una quantità illimitata di memoria del server. L'utilizzo di memoria illimitata equivale allo scenario in cui un ciclo senza terminazione aggiunge voci a una raccolta in ogni iterazione.

Per evitare modelli di ricorsione infinita, verificare che il codice di rendering ricorsivo contenga condizioni di arresto appropriate.

### <a name="custom-render-tree-logic"></a>Logica dell'albero di rendering personalizzata

La maggior parte dei componenti Blazor viene implementata come file con *estensione Razor* e viene compilata `RenderTreeBuilder` per produrre la logica che opera su un per eseguire il rendering dell'output. Uno sviluppatore può implementare `RenderTreeBuilder` manualmente la logica usando il C# codice procedurale. Per altre informazioni, vedere <xref:blazor/components#manual-rendertreebuilder-logic>.

> [!WARNING]
> L'uso della logica del generatore di albero di rendering manuale è considerato uno scenario avanzato e non sicuro, non consigliato per lo sviluppo di componenti generali.

Se `RenderTreeBuilder` il codice viene scritto, lo sviluppatore deve garantire la correttezza del codice. Ad esempio, lo sviluppatore deve garantire quanto segue:

* Le chiamate `OpenElement` a `CloseElement` e sono bilanciate correttamente.
* Gli attributi vengono aggiunti solo nei punti corretti.

La logica del generatore di albero di rendering manuale non corretta può causare un comportamento arbitrario non definito, tra cui arresti anomali, blocchi del server e vulnerabilità della sicurezza.

Prendere in considerazione la logica del generatore di albero di rendering manuale sullo stesso livello di complessità e con lo stesso livello di *rischio* di scrivere manualmente codice assembly o istruzioni MSIL.

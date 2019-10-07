---
title: Interoperabilità JavaScript di ASP.NET Core Blazor
author: guardrex
description: Informazioni su come richiamare funzioni JavaScript da metodi .NET e .NET da JavaScript in app Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/javascript-interop
ms.openlocfilehash: b30bce6ef3ebf1cd2f4f3fe8d046e1db9b6929d5
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924639"
---
# <a name="aspnet-core-blazor-javascript-interop"></a>Interoperabilità JavaScript di ASP.NET Core Blazor

Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Un'app Blazor può richiamare funzioni JavaScript da metodi .NET e .NET dal codice JavaScript.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="invoke-javascript-functions-from-net-methods"></a>Richiamare funzioni JavaScript da metodi .NET

In alcuni casi è necessario che il codice .NET chiami una funzione JavaScript. Ad esempio, una chiamata JavaScript può esporre le funzionalità o le funzionalità del browser da una libreria JavaScript all'app.

Per chiamare JavaScript da .NET, usare l' `IJSRuntime` astrazione. Il `InvokeAsync<T>` metodo accetta un identificatore per la funzione JavaScript che si vuole richiamare insieme a un numero qualsiasi di argomenti serializzabili in JSON. L'identificatore della funzione è relativo all'ambito globale (`window`). Se si desidera chiamare `window.someScope.someFunction`, l'identificatore è. `someScope.someFunction` Non è necessario registrare la funzione prima che venga chiamata. Il tipo `T` restituito deve anche essere serializzabile in JSON.

Per le app del server Blazor:

* Più richieste utente vengono elaborate dall'app Server Blazor. Non chiamare `JSRuntime.Current` in un componente per richiamare funzioni JavaScript.
* Inserire l' `IJSRuntime` astrazione e usare l'oggetto inserito per eseguire chiamate di interoperabilità JavaScript.
* Mentre un'app Blaze sta eseguendo il prerendering, non è possibile chiamare JavaScript perché non è stata stabilita una connessione con il browser. Per altre informazioni, vedere la sezione [rilevare quando un'app Blazor è prerendering](#detect-when-a-blazor-app-is-prerendering) .

L'esempio seguente è basato su [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un decodificatore basato su JavaScript sperimentale. Nell'esempio viene illustrato come richiamare una funzione JavaScript da un C# metodo. La funzione JavaScript accetta una matrice di byte da C# un metodo, decodifica la matrice e restituisce il testo al componente per la visualizzazione.

All'interno dell'elemento `<head>` di *wwwroot/index.html* (Blazor webassembly) o *pages/_Host. cshtml* (server Blazor), fornire una funzione che usa `TextDecoder` per decodificare una matrice passata:

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript (con*estensione js*) con un riferimento al file script:

```html
<script src="exampleJsInterop.js"></script>
```

Il componente seguente:

* Richiama la `ConvertArray` funzione JavaScript usando `JsRuntime` quando viene selezionato un pulsante Component (**Convert Array**).
* Dopo la chiamata della funzione JavaScript, la matrice passata viene convertita in una stringa. La stringa viene restituita al componente per la visualizzazione.

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

Per usare l' `IJSRuntime` astrazione, adottare uno degli approcci seguenti:

* Inserire l' `IJSRuntime` astrazione nel componente Razor (*Razor*):

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* Inserire l' `IJSRuntime` astrazione in una classe ( *. cs*):

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), `[Inject]` usare l'attributo:

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

Nell'app di esempio lato client che accompagna questo argomento sono disponibili due funzioni JavaScript per l'app che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:

* `showPrompt`&ndash; Genera un messaggio di richiesta per accettare l'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.
* `displayWelcome`Assegna un messaggio di benvenuto dal chiamante a un oggetto DOM con un `id` valore di `welcome`. &ndash;

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Inserire il tag `<script>` che fa riferimento al file JavaScript nel file *wwwroot/index.html* (Webassembly Blazor) o nel file *pages/_Host. cshtml* (server Blazor).

*wwwroot/index.html* (Webassembly Blazor):

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

*Pages/_Host. cshtml* (server Blazor):

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

Non inserire un `<script>` tag in un file di componente perché `<script>` il tag non può essere aggiornato dinamicamente.

I metodi .NET interoperano con le funzioni JavaScript nel file *exampleJsInterop. js* chiamando `IJSRuntime.InvokeAsync<T>`.

L' `IJSRuntime` astrazione è asincrona per consentire scenari di server Blazor. Se l'app è un'app webassembly Blazor e si vuole richiamare una funzione JavaScript in modo sincrono, abbattuti `IJSInProcessRuntime` in `Invoke<T>` e chiamare invece. È consigliabile che la maggior parte delle librerie di interoperabilità JavaScript usino le API asincrone per garantire che le librerie siano disponibili in tutti gli scenari.

L'app di esempio include un componente per illustrare l'interoperabilità di JavaScript. Il componente:

* Riceve l'input dell'utente tramite un prompt di JavaScript.
* Restituisce il testo al componente per l'elaborazione.
* Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.

*Pages/JSInterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante di **richiesta trigger JavaScript** del componente, viene `showPrompt` chiamata la funzione JavaScript fornita nel file *wwwroot/exampleJsInterop. js* .
1. La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente. Il componente archivia il nome dell'utente in una variabile locale, `name`.
1. La stringa archiviata `name` in viene incorporata in un messaggio di benvenuto, che viene passato a una `displayWelcome`funzione JavaScript,, che esegue il rendering del messaggio di benvenuto in un tag di intestazione.

## <a name="call-a-void-javascript-function"></a>Chiamare una funzione JavaScript void

Le funzioni JavaScript che restituiscono [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) vengono chiamate con `IJSRuntime.InvokeVoidAsync`.

## <a name="detect-when-a-blazor-app-is-prerendering"></a>Rileva quando è in fase di prerendering un'app Blazor
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Acquisisci riferimenti a elementi

Alcuni scenari di [interoperabilità JavaScript](xref:blazor/javascript-interop) richiedono riferimenti agli elementi HTML. Ad esempio, una libreria dell'interfaccia utente può richiedere un riferimento a un elemento per l'inizializzazione o potrebbe essere necessario chiamare API simili a quelle di un `focus` elemento `play`, ad esempio o.

Acquisire i riferimenti agli elementi HTML in un componente usando l'approccio seguente:

* Aggiungere un `@ref` attributo all'elemento HTML.
* Definire un campo di tipo `ElementReference` il cui nome corrisponde al valore `@ref` dell'attributo.

Nell'esempio seguente viene illustrata l'acquisizione di `username` un riferimento all' `<input>` elemento:

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> **Non** usare i riferimenti agli elementi acquisiti come modo per popolare il Dom. Questa operazione può interferire con il modello di rendering dichiarativo.

Per quanto riguarda il codice .NET, un `ElementReference` è un handle opaco. L' *unica* cosa che è possibile fare `ElementReference` con è passarla al codice JavaScript tramite l'interoperabilità JavaScript. Quando si esegue questa operazione, il codice sul lato JavaScript riceve `HTMLElement` un'istanza, che può essere usata con le API DOM normali.

Il codice seguente, ad esempio, definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Usare `IJSRuntime.InvokeAsync<T>` e chiamare `exampleJsFunctions.focusElement` con un `ElementReference` oggetto per concentrare un elemento:

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Per usare un metodo di estensione per lo stato attivo di un elemento, creare un metodo di `IJSRuntime` estensione statico che riceva l'istanza:

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Il metodo viene chiamato direttamente nell'oggetto. Nell'esempio seguente si presuppone che il `Focus` metodo statico sia disponibile `JsInteropClasses` dallo spazio dei nomi:

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> La `username` variabile viene popolata solo dopo il rendering del componente. Se un oggetto non `ElementReference` popolato viene passato al codice JavaScript, il codice JavaScript riceve un `null`valore di. Per modificare i riferimenti agli elementi dopo che il componente ha terminato il rendering (per impostare lo stato attivo iniziale su `OnAfterRenderAsync` un `OnAfterRender` elemento), usare i metodi del ciclo di vita dei [componenti](xref:blazor/components#lifecycle-methods)o.

## <a name="invoke-net-methods-from-javascript-functions"></a>Richiamare metodi .NET da funzioni JavaScript

### <a name="static-net-method-call"></a>Chiamata al metodo .NET statico

Per richiamare un metodo .NET statico da JavaScript, usare le `DotNet.invokeMethod` funzioni `DotNet.invokeMethodAsync` o. Passare l'identificatore del metodo statico che si desidera chiamare, il nome dell'assembly che contiene la funzione e gli eventuali argomenti. La versione asincrona è preferibile per supportare scenari di server Blazor. Per richiamare un metodo .NET da JavaScript, il metodo .NET deve essere pubblico, statico e avere l' `[JSInvokable]` attributo. Per impostazione predefinita, l'identificatore del metodo è il nome del metodo, ma è possibile specificare un identificatore `JSInvokableAttribute` diverso tramite il costruttore. La chiamata ai metodi generici aperti non è attualmente supportata.

L'app di esempio include C# un metodo per restituire una matrice `int`di s. L' `JSInvokable` attributo viene applicato al metodo.

*Pages/JsInterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

JavaScript servito al client richiama il C# metodo .NET.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Quando si seleziona il pulsante **trigger .net static method ReturnArrayAsync** , esaminare l'output della console negli strumenti di sviluppo Web del browser.

L'output della console è:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Il quarto valore della matrice viene inserito nella matrice (`data.push(4);`) restituito da `ReturnArrayAsync`.

### <a name="instance-method-call"></a>Chiamata al metodo di istanza

È anche possibile chiamare i metodi di istanza .NET da JavaScript. Per richiamare un metodo di istanza .NET da JavaScript:

* Passare l'istanza .NET a JavaScript eseguendone il wrapping in `DotNetObjectReference` un'istanza di. L'istanza .NET viene passata per riferimento a JavaScript.
* Richiamare i metodi di istanza .NET sull'istanza usando `invokeMethod` le `invokeMethodAsync` funzioni o. L'istanza di .NET può essere passata anche come argomento quando si richiamano altri metodi .NET da JavaScript.

> [!NOTE]
> L'app di esempio consente di registrare i messaggi nella console lato client. Per gli esempi seguenti illustrati dall'app di esempio, esaminare l'output della console del browser negli strumenti di sviluppo del browser.

Quando il pulsante del **metodo di istanza .NET del trigger HelloHelper. sayHello** è selezionato, `ExampleJsInterop.CallHelloHelperSayHello` viene chiamato e `Blazor`passa un nome al metodo.

*Pages/JsInterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello`richiama la funzione `sayHello` JavaScript con una nuova istanza di `HelloHelper`.

*JsInteropClasses/ExampleJsInterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Il nome viene passato al `HelloHelper`costruttore, che imposta la `HelloHelper.Name` proprietà. Quando viene eseguita la `sayHello` funzione JavaScript, `HelloHelper.SayHello` restituisce il `Hello, {Name}!` messaggio, che viene scritto nella console dalla funzione JavaScript.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Output della console negli strumenti di sviluppo Web del browser:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>Condividere il codice di interoperabilità in una libreria di classi

Il codice di interoperabilità JavaScript può essere incluso in una libreria di classi, che consente di condividere il codice in un pacchetto NuGet.

La libreria di classi gestisce l'incorporamento delle risorse JavaScript nell'assembly compilato. I file JavaScript vengono inseriti nella cartella *wwwroot* Gli strumenti si occupano di incorporare le risorse quando la libreria viene compilata.

Nel file di progetto dell'app viene fatto riferimento al pacchetto NuGet compilato nello stesso modo in cui viene fatto riferimento a un pacchetto NuGet. Dopo il ripristino del pacchetto, il codice dell'app può chiamare JavaScript come se fosse C#.

Per altre informazioni, vedere <xref:blazor/class-libraries>.

## <a name="harden-js-interop-calls"></a>Chiamate di interoperabilità JS di Harden

L'interoperabilità JS potrebbe non riuscire a causa di errori di rete e deve essere considerata inaffidabile. Per impostazione predefinita, un'app del server Blaze timeout le chiamate di interoperabilità JS nel server dopo un minuto. Se un'app può tollerare un timeout più aggressivo, ad esempio 10 secondi, impostare il timeout usando uno degli approcci seguenti:

* A livello globale `Startup.ConfigureServices`in specificare il timeout:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Per chiamata nel codice componente, una singola chiamata può specificare il timeout:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Per ulteriori informazioni sull'esaurimento delle risorse, <xref:security/blazor/server>vedere.

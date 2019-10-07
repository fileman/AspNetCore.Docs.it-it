---
title: Creare la prima app Blazor
author: guardrex
description: Istruzioni dettagliate per creare un'app Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 10feb5467a6a6b5a43e0df739fa72902af9854da
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168368"
---
# <a name="build-your-first-blazor-app"></a>Creare la prima app Blazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Questa esercitazione mostra come compilare e modificare un'app Blazor.

Seguire le indicazioni contenute nell'articolo <xref:blazor/get-started> per creare un progetto Blazor per questa esercitazione. Denominare il progetto *ToDoList*.

## <a name="build-components"></a>Compilare i componenti

1. Passare a ognuna delle tre pagine dell'app nella cartella *Pages*: Home, Counter e Fetch data (Home, Contatore e Recupera dati). Queste pagine vengono implementate dai file dei componenti Razor *Index.razor*, *Counter.razor* e *FetchData.razor*.

1. Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina. Per incrementare un contatore in una pagina Web è in genere richiesta la scrittura di codice JavaScript, ma Blazor offre un approccio migliore usando C#.

1. Esaminare l'implementazione del componente `Counter` nel file *Counter.razor*.

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   L'interfaccia utente del componente `Counter` viene definita tramite codice HTML. La logica di rendering dinamico (ad esempio, cicli, condizioni, espressioni) viene aggiunta usando una sintassi C# incorporata chiamata [Razor](xref:mvc/views/razor). Il markup HTML e la logica di rendering C# vengono convertiti un una classe del componente in fase di compilazione. Il nome della classe .NET generata corrisponde al nome del file.

   I membri della classe del componente vengono definiti in un blocco `@code`. Nel blocco `@code`, lo stato del componente (proprietà, campi) e i metodi vengono specificati per la gestione degli eventi o per definire la logica di altri componenti. Questi membri vengono quindi usati come parte della logica di rendering del componente e per la gestione degli eventi.

   Quando viene selezionato il pulsante **Click me**:

   * Viene chiamato il gestore `onclick` registrato del componente `Counter` (metodo `IncrementCount`).
   * Il componente `Counter` rigenera il relativo albero di rendering.
   * Il nuovo albero di rendering viene confrontato con quello precedente.
   * Vengono applicate solo le modifiche al modello DOM (Document Object Model). Il conteggio visualizzato viene aggiornato.

1. Modificare la logica C# del componente `Counter` per incrementare il contatore di due unità anziché una.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Ricompilare ed eseguire l'app per visualizzare le modifiche. Selezionare il pulsante **Fare clic qui**. Il contatore viene incrementato di due.

## <a name="use-components"></a>Usare i componenti

Includere un componente in un altro componente usando una sintassi HTML.

1. Aggiungere il componente `Counter` al componente `Index` dell'app aggiungendo un elemento `<Counter />` al componente `Index` (*Index.razor*).

   Se si usa webassembly Blazor per questa esperienza, un `SurveyPrompt` componente viene utilizzato `Index` dal componente. Sostituire l'elemento `<SurveyPrompt>` con un elemento `<Counter />`. Se si usa un'app del server Blazor per questa esperienza, aggiungere l' `<Counter />` elemento `Index` al componente:

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Ricompilare ed eseguire l'app. Il componente `Index` ha il proprio contatore.

## <a name="component-parameters"></a>Parametri del componente

I componenti possono avere anche parametri, I parametri del componente vengono definiti usando proprietà pubbliche nella classe Component con `[Parameter]` l'attributo. Usare gli attributi per specificare gli argomenti per un componente nel markup.

1. Aggiornare il codice C# `@code` del componente:

   * Aggiungere una proprietà `IncrementAmount` pubblica con l' `[Parameter]` attributo.
   * Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Specificare un parametro `IncrementAmount` nell'elemento `<Counter>` del componente `Index` usando un attributo. Impostare il valore per incrementare il contatore di dieci unità.

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Ricaricare il componente `Index`. Il contatore viene incrementato di dieci unità ogni volta che viene selezionato il pulsante **Click me**. Il contatore nel componente `Counter` continua a essere incrementato di uno.

## <a name="route-to-components"></a>Indirizzare le richieste ai componenti

La direttiva `@page` all'inizio del file *Counter.razor* specifica che il componente `Counter` è un endpoint di routing. Il componente `Counter` gestisce le richieste inviate a `/counter`. Senza la direttiva `@page`, un componente non gestisce le richieste instradate, ma può comunque essere usato da altri componenti.

## <a name="dependency-injection"></a>Inserimento di dipendenze

Se si utilizza un'app del server Blazor, `WeatherForecastService` il servizio viene registrato come [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`. Un'istanza del servizio è disponibile in tutte le app tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

La `@inject` direttiva viene utilizzata per inserire l'istanza `WeatherForecastService` del servizio nel `FetchData` componente.

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Il componente `FetchData` usa il servizio inserito, come `ForecastService`, per recuperare una matrice di oggetti `WeatherForecast`:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

Se si usa un'app webassembly Blazor, `HttpClient` viene inserito per ottenere i dati delle previsioni meteo dal file *Weather. JSON* nella cartella *wwwroot/Sample-Data* .

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Viene usato un ciclo [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) per eseguire il rendering di ogni istanza di previsione come riga nella tabella dei dati meteo:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Compilare un elenco attività

Aggiungere all'app un nuovo componente che implementa un semplice elenco attività.

1. Aggiungere un file vuoto denominato *Todo.razor* all'app nella cartella *Pages*:

1. Specificare il markup iniziale per il componente:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. Aggiungere il componente `Todo` alla barra di spostamento.

   Il componente `NavMenu` (*Shared/NavMenu.razor*) viene usato nel layout dell'app. I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.

   Aggiungere un elemento `<NavLink>` per il componente `Todo` aggiungendo il markup seguente per la voce di elenco sotto le voci di elenco esistenti nel file *Shared/NavMenu.razor*:

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Ricompilare ed eseguire l'app. Visitare la nuova pagina Todo per confermare che il collegamento al componente `Todo` funzioni correttamente.

1. Aggiungere un file *TodoItem.cs* nella radice del progetto per contenere una classe per rappresentare un elemento attività. Usare il codice C# seguente per la classe `TodoItem`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Tornare al componente `Todo` (*Pages/Todo.razor*):

   * Aggiungere un campo per le voci todo in un blocco `@code`. Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.
   * Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco. Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Ricompilare ed eseguire l'app. Quando viene selezionato il pulsante **Add todo** non accade nulla perché al pulsante non è associato un gestore eventi.

1. Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`. Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco. Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Ricompilare ed eseguire l'app. Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.

1. Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati. Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`. Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Per verificare che questi valori siano associati, aggiornare l'intestazione `<h1>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Componente `Todo` completato (*Pages/Todo.razor*):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Ricompilare ed eseguire l'app. Aggiungere elementi attività per testare il nuovo codice.

> [!div class="nextstepaction"]
> <xref:blazor/components>

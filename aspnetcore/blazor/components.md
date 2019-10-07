---
title: Creare e usare ASP.NET Core componenti Razor
author: guardrex
description: Informazioni su come creare e usare i componenti Razor, tra cui la modalità di associazione ai dati, la gestione degli eventi e la gestione dei cicli di vita dei componenti.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/30/2019
uid: blazor/components
ms.openlocfilehash: ea216e405e5be52b578e99a529d8c6a726ea9cdd
ms.sourcegitcommit: fe88748b762525cb490f7e39089a4760f6a73a24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71688034"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Creare e usare ASP.NET Core componenti Razor

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

Le app Blazor vengono compilate usando i *componenti*. Un componente è un blocco di interfaccia utente (UI) autonomo, ad esempio una pagina, una finestra di dialogo o un form. Un componente include il markup HTML e la logica di elaborazione necessaria per inserire i dati o rispondere agli eventi dell'interfaccia utente. I componenti sono flessibili e leggeri. Possono essere annidati, riutilizzati e condivisi tra i progetti.

## <a name="component-classes"></a>Classi di componenti

I componenti sono implementati in file di componente [Razor](xref:mvc/views/razor) (*Razor*) usando una C# combinazione di e markup HTML. Un componente in Blazor viene definito formalmente come *componente Razor*.

Il nome di un componente deve iniziare con un carattere maiuscolo. Ad esempio, *MyCoolComponent. Razor* è valido e *MyCoolComponent. Razor* non è valido.

L'interfaccia utente per un componente viene definita utilizzando HTML. La logica di rendering dinamico (ad esempio, cicli, condizioni, espressioni) viene aggiunta usando una sintassi C# incorporata chiamata [Razor](xref:mvc/views/razor). Quando viene compilata un'app, il markup C# HTML e la logica di rendering vengono convertiti in una classe Component. Il nome della classe generata corrisponde al nome del file.

I membri della classe del componente vengono definiti in un blocco `@code`. Nel blocco `@code` , lo stato del componente (proprietà, campi) viene specificato con i metodi per la gestione degli eventi o per la definizione di altre logiche dei componenti. È consentito più di un blocco `@code`.

> [!NOTE]
> Nelle anteprime precedenti di ASP.NET Core 3,0, `@functions` i blocchi venivano usati per lo stesso `@code` scopo dei blocchi nei componenti Razor. `@functions`i blocchi continuano a funzionare nei componenti Razor, ma è consigliabile usare `@code` il blocco in ASP.NET Core 3,0 Preview 6 o versione successiva.

I membri dei componenti possono essere usati come parte della logica di rendering del C# componente usando espressioni che `@`iniziano con. Ad esempio, viene C# eseguito il rendering di un campo `@` anteponendo il nome del campo. Nell'esempio seguente viene valutato ed eseguito il rendering:

* `_headingFontStyle`al valore della proprietà CSS per `font-style`.
* `_headingText`al contenuto dell' `<h1>` elemento.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Una volta eseguito il rendering iniziale del componente, il componente rigenera l'albero di rendering in risposta agli eventi. Blazor confronta quindi il nuovo albero di rendering con quello precedente e applica le modifiche apportate all'Document Object Model (DOM) del browser.

I componenti sono C# classi ordinarie e possono essere inseriti in qualsiasi punto all'interno di un progetto. I componenti che producono pagine Web in genere risiedono nella cartella *pages* . I componenti non di pagina vengono spesso inseriti nella cartella *condivisa* o in una cartella personalizzata aggiunta al progetto. Per usare una cartella personalizzata, aggiungere lo spazio dei nomi della cartella personalizzata al componente padre o al file *_Imports. Razor* dell'app. Lo spazio dei nomi seguente, ad esempio, rende disponibili i componenti in una cartella di *componenti* quando lo `WebApplication`spazio dei nomi radice dell'app è:

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrare i componenti nelle app Razor Pages e MVC

Usare i componenti con le app Razor Pages e MVC esistenti. Non è necessario riscrivere le pagine o le visualizzazioni esistenti per usare i componenti Razor. Quando viene eseguito il rendering della pagina o della vista, i componenti vengono prerenderizzati contemporaneamente.

Per eseguire il rendering di un componente da una pagina o da `RenderComponentAsync<TComponent>` una vista, usare il metodo helper HTML:

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

Mentre le pagine e le visualizzazioni possono usare i componenti, il contrario non è vero. I componenti non possono usare scenari di visualizzazione e di pagina specifici, ad esempio visualizzazioni parziali e sezioni. Per usare la logica dalla visualizzazione parziale in un componente, scomporre la logica di visualizzazione parziale in un componente.

Per altre informazioni su come viene eseguito il rendering dei componenti e lo stato del componente viene gestito nelle app del <xref:blazor/hosting-models> server Blazor, vedere l'articolo.

## <a name="use-components"></a>Usare i componenti

I componenti possono includere altri componenti dichiarando questi ultimi usando la sintassi degli elementi HTML. Il markup per l'uso di un componente è simile a un tag HTML, in cui il nome del tag è il tipo di componente.

L'associazione di attributi distingue tra maiuscole e minuscole. Ad esempio, `@bind` è valido e `@Bind` non è valido.

Il markup seguente in *index. Razor* esegue il rendering `HeadingComponent` di un'istanza:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Components/HeadingComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

Se un componente contiene un elemento HTML con una prima lettera maiuscola che non corrisponde a un nome di componente, viene emesso un avviso che indica che l'elemento ha un nome imprevisto. L'aggiunta `@using` di un'istruzione per lo spazio dei nomi del componente rende disponibile il componente, che rimuove l'avviso.

## <a name="component-parameters"></a>Parametri del componente

I componenti possono avere *parametri del componente*, che vengono definiti usando proprietà pubbliche nella classe Component con `[Parameter]` l'attributo. Usare gli attributi per specificare gli argomenti per un componente nel markup.

*Components/ChildComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

Nell'esempio seguente, `ParentComponent` imposta il valore `Title` della proprietà dell'oggetto `ChildComponent`.

*Pages/ParentComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>Contenuto figlio

I componenti possono impostare il contenuto di un altro componente. Il componente di assegnazione fornisce il contenuto tra i tag che specificano il componente di destinazione.

Nell'esempio seguente, `ChildComponent` ha una `ChildContent` proprietà che rappresenta un oggetto `RenderFragment`, che rappresenta un segmento di interfaccia utente di cui eseguire il rendering. Il valore di `ChildContent` viene posizionato nel markup del componente in cui deve essere eseguito il rendering del contenuto. Il valore di `ChildContent` viene ricevuto dal componente padre e ne viene eseguito il rendering all'interno `panel-body`del pannello bootstrap.

*Components/ChildComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> La proprietà che riceve `RenderFragment` il contenuto deve essere `ChildContent` denominata per convenzione.

Gli elementi `ParentComponent` seguenti possono fornire contenuto per il `ChildComponent` rendering di inserendo il contenuto all' `<ChildComponent>` interno dei tag.

*Pages/ParentComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attributo splatting e parametri arbitrari

I componenti possono acquisire ed eseguire il rendering di attributi aggiuntivi oltre ai parametri dichiarati del componente. È possibile acquisire attributi aggiuntivi in un dizionario e quindi *Splatted* su un elemento quando il componente viene sottoposto a [@attributes](xref:mvc/views/razor#attributes) rendering usando la direttiva Razor. Questo scenario è utile quando si definisce un componente che produce un elemento di markup che supporta un'ampia gamma di personalizzazioni. Ad esempio, può essere noioso definire gli attributi separatamente per un `<input>` che supporta molti parametri.

Nell'esempio seguente `<input>` , il primo elemento (`id="useIndividualParams"`) USA parametri dei singoli componenti, mentre il secondo `<input>` elemento (`id="useAttributesDict"`) usa l'attributo splatting:

```cshtml
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Il tipo del parametro deve implementare `IEnumerable<KeyValuePair<string, object>>` con chiavi di stringa. L' `IReadOnlyDictionary<string, object>` uso di è anche un'opzione in questo scenario.

Gli elementi `<input>` sottoposti a rendering usando entrambi gli approcci sono identici:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Per accettare attributi arbitrari, definire un parametro component usando l' `[Parameter]` attributo con la `CaptureUnmatchedValues` proprietà impostata su `true`:

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

La `CaptureUnmatchedValues` proprietà su `[Parameter]` consente al parametro di trovare la corrispondenza con tutti gli attributi che non corrispondono ad altri parametri. Un componente può definire solo un singolo parametro con `CaptureUnmatchedValues`. Il tipo di proprietà utilizzato `CaptureUnmatchedValues` con deve essere assegnabile `Dictionary<string, object>` da con chiavi di stringa. `IEnumerable<KeyValuePair<string, object>>`o `IReadOnlyDictionary<string, object>` sono anche opzioni in questo scenario.

## <a name="data-binding"></a>Associazione dati

L'associazione dati a entrambi i componenti e gli elementi DOM viene [@bind](xref:mvc/views/razor#bind) eseguita con l'attributo. Nell'esempio seguente il `_italicsCheck` campo viene associato allo stato di selezione della casella di controllo:

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

Quando la casella di controllo è selezionata e deselezionata, il valore della proprietà `true` viene `false`aggiornato rispettivamente a e.

La casella di controllo viene aggiornata nell'interfaccia utente solo quando viene eseguito il rendering del componente, non in risposta alla modifica del valore della proprietà. Poiché i componenti eseguono il rendering dopo l'esecuzione del codice del gestore eventi, gli aggiornamenti delle proprietà vengono in genere riflessi immediatamente nell'interfaccia utente.

L' `@bind` utilizzo di `CurrentValue` con una`<input @bind="CurrentValue" />`proprietà () è essenzialmente equivalente a quanto segue:

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

Quando viene eseguito il rendering del componente `value` , l'oggetto dell'elemento di input `CurrentValue` deriva dalla proprietà. Quando l'utente digita nella casella di testo, l' `onchange` evento viene generato e la `CurrentValue` proprietà viene impostata sul valore modificato. In realtà, la generazione del codice è un po' più complessa `@bind` perché gestisce alcuni casi in cui vengono eseguite le conversioni dei tipi. In linea di `@bind` principio, associa il valore corrente di un'espressione `value` a un attributo e gestisce le modifiche utilizzando il gestore registrato.

`onchange` Oltre a gestire gli eventi con `@bind` la sintassi, una proprietà o un campo può essere associato utilizzando altri eventi specificando un [@bind-value](xref:mvc/views/razor#bind) attributo `event` con un[@bind-value:event](xref:mvc/views/razor#bind)parametro (). Nell'esempio seguente viene associata la `CurrentValue` proprietà per l' `oninput` evento:

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

Diversamente `onchange`da, che viene attivato quando l'elemento perde lo `oninput` stato attivo, viene attivato quando viene modificato il valore della casella di testo.

**Valori non analizzabili**

Quando un utente fornisce un valore non analizzabile a un elemento associato a un oggetto DataBound, il valore non analizzabile viene automaticamente ripristinato al valore precedente quando viene attivato l'evento bind.

Si consideri lo scenario seguente:

* Un `<input>` elemento è associato a un `int` tipo con un valore iniziale di `123`:

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* L'utente aggiorna il valore dell'elemento a `123.45` nella pagina e modifica lo stato attivo dell'elemento.

Nello scenario precedente, il valore dell'elemento viene ripristinato `123`in. Quando il valore `123.45` viene rifiutato a favore del valore originale di `123`, l'utente riconosce che il relativo valore non è stato accettato.

Per impostazione predefinita, l'associazione si applica all' `onchange` evento dell'`@bind="{PROPERTY OR FIELD}"`elemento (). Utilizzare `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` per impostare un evento diverso. Per l' `oninput` evento (`@bind-value:event="oninput"`), la riversione viene eseguita dopo qualsiasi sequenza di tasti che introduce un valore non analizzabile. Quando la destinazione è `oninput` l'evento con `int`un tipo associato a, a un utente viene impedito `.` di digitare un carattere. Un `.` carattere viene immediatamente rimosso, quindi l'utente riceve il feedback immediato che sono consentiti solo numeri interi. Esistono scenari in `oninput` cui il ripristino del valore dell'evento non è ideale, ad esempio quando l'utente deve essere autorizzato a cancellare un `<input>` valore non analizzabile. Le alternative includono:

* Non usare l' `oninput` evento. Usare l'evento `onchange` predefinito (`@bind="{PROPERTY OR FIELD}"`), in cui un valore non valido non viene ripristinato fino a quando l'elemento non perde lo stato attivo.
* Eseguire l'associazione a un tipo nullable, `int?` ad `string`esempio o, e fornire la logica personalizzata per gestire le voci non valide.
* Utilizzare un [componente di convalida del modulo](xref:blazor/forms-validation), `InputNumber` ad `InputDate`esempio o. I componenti di convalida dei moduli includono il supporto predefinito per la gestione di input non validi. Componenti di convalida dei moduli:
  * Consente all'utente di fornire un input non valido e di ricevere errori di `EditContext`convalida nell'oggetto associato.
  * Visualizzare gli errori di convalida nell'interfaccia utente senza interferire con l'utente che immette dati Web Form aggiuntivi.

**Globalizzazione**

`@bind`i valori vengono formattati per la visualizzazione e l'analisi usando le regole delle impostazioni cultura correnti.

È possibile accedere alle impostazioni cultura correnti dalla <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> proprietà.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) viene usato per i tipi di campo seguenti`<input type="{TYPE}" />`():

* `date`
* `number`

I tipi di campo precedenti:

* Vengono visualizzati utilizzando le regole di formattazione appropriate basate su browser.
* Non può contenere testo in formato libero.
* Fornire le caratteristiche di interazione utente in base all'implementazione del browser.

I tipi di campo seguenti hanno requisiti di formattazione specifici e non sono attualmente supportati da Blazor perché non sono supportati da tutti i browser principali:

* `datetime-local`
* `month`
* `week`

`@bind`supporta il `@bind:culture` parametro per fornire un <xref:System.Globalization.CultureInfo?displayProperty=fullName> oggetto per l'analisi e la formattazione di un valore. Non è consigliabile specificare impostazioni cultura quando si `date` usano `number` i tipi di campo e. `date`e `number` hanno il supporto predefinito di Blazor che fornisce le impostazioni cultura richieste.

Per informazioni su come impostare le impostazioni cultura dell'utente, vedere la sezione [localizzazione](#localization) .

**Stringhe di formato**

Il data binding funziona <xref:System.DateTime> con stringhe di [@bind:format](xref:mvc/views/razor#bind)formato usando. Altre espressioni di formato, ad esempio i formati di valuta o numerici, non sono disponibili in questo momento.

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Nel codice precedente, il valore `<input>` predefinito `text`del tipo di campo`type`dell'elemento () è. `@bind:format`è supportato per l'associazione dei tipi .NET seguenti:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

L' `@bind:format` attributo specifica il formato di data da applicare all' `value` oggetto dell' `<input>` elemento. Il formato viene usato anche per analizzare il valore quando si `onchange` verifica un evento.

La specifica di un formato `date` per il tipo di campo non è consigliata perché Blazor dispone del supporto incorporato per formattare le date.

**Parametri dei componenti**

Il binding riconosce i parametri del `@bind-{property}` componente, dove può associare un valore di proprietà tra i componenti.

Il componente figlio seguente (`ChildComponent`) ha un `Year` parametro del componente `YearChanged` e un callback:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>`viene illustrato nella sezione [EventCallback](#eventcallback) .

Il componente padre seguente utilizza `ChildComponent` e associa il `ParentYear` parametro `Year` dall'elemento padre al parametro nel componente figlio:

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Il caricamento `ParentComponent` di produce il markup seguente:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentYear` Se il valore della proprietà viene modificato selezionando il pulsante `ParentComponent`in, viene aggiornata la `Year` proprietà dell'oggetto `ChildComponent` . Viene eseguito il rendering `Year` del nuovo valore di nell'interfaccia utente `ParentComponent` quando viene eseguito il rendering dell'oggetto:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Il `Year` parametro è associabile perché contiene un evento `YearChanged` complementare che `Year` corrisponde al tipo del parametro.

Per convenzione, `<ChildComponent @bind-Year="ParentYear" />` equivale essenzialmente alla scrittura:

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

In generale, una proprietà può essere associata a un gestore eventi corrispondente usando `@bind-property:event` l'attributo. Ad esempio, la proprietà `MyProp` può essere associata a `MyEventHandler` utilizzando i due attributi seguenti:

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>Gestione di eventi

I componenti Razor forniscono funzionalità di gestione degli eventi. Per un attributo dell'elemento HTML `on{event}` denominato (ad esempio `onclick` , `onsubmit`e) con un valore tipizzato da un delegato, i componenti Razor considera il valore dell'attributo come un gestore eventi. Il nome dell'attributo è sempre formattato [ @on{Event}](xref:mvc/views/razor#onevent).

Il codice seguente chiama il `UpdateHeading` metodo quando si seleziona il pulsante nell'interfaccia utente:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Il codice seguente chiama il `CheckChanged` metodo quando la casella di controllo viene modificata nell'interfaccia utente:

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

I gestori di eventi possono anche essere asincroni e restituire <xref:System.Threading.Tasks.Task>un oggetto. Non è necessario chiamare `StateHasChanged()`manualmente. Le eccezioni vengono registrate quando si verificano.

Nell'esempio seguente, `UpdateHeading` viene chiamato in modo asincrono quando si seleziona il pulsante:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>Tipi di argomenti dell'evento

Per alcuni eventi, i tipi di argomento dell'evento sono consentiti. Se l'accesso a uno di questi tipi di evento non è necessario, non è obbligatorio nella chiamata al metodo.

Nella tabella seguente sono riportati i `EventArgs` supportati.

| event | Classe |
| ----- | ----- |
| Appunti        | `ClipboardEventArgs` |
| Trascinare             | `DragEventArgs`e contengono dati di`DataTransferItem` elementi trascinati. &ndash; `DataTransfer` |
| Errore            | `ErrorEventArgs` |
| Stato attivo            | `FocusEventArgs`Non include il supporto `relatedTarget`per. &ndash; |
| Modifica di`<input>` | `ChangeEventArgs` |
| Tastiera         | `KeyboardEventArgs` |
| Mouse            | `MouseEventArgs` |
| Puntatore del mouse    | `PointerEventArgs` |
| Rotellina del mouse      | `WheelEventArgs` |
| Stato         | `ProgressEventArgs` |
| Tocco            | `TouchEventArgs`&ndash; rappresentaunsingolopuntodicontattosuun`TouchPoint` dispositivo sensibile al tocco. |

Per informazioni sulle proprietà e sul comportamento di gestione degli eventi degli eventi nella tabella precedente, vedere le [classi EventArgs nell'origine riferimento (ASPNET/AspNetCore Release/3.0 Branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).

### <a name="lambda-expressions"></a>Espressioni lambda

È inoltre possibile utilizzare le espressioni lambda:

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Spesso è consigliabile chiudere i valori aggiuntivi, ad esempio quando si esegue l'iterazione su un set di elementi. Nell'esempio seguente vengono creati tre pulsanti, ciascuno dei quali `UpdateHeading` chiama il passaggio di un`MouseEventArgs`argomento di evento () e`buttonNumber`il relativo numero di pulsante () quando vengono selezionati nell'interfaccia utente:

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> **Non** usare la variabile di ciclo (`i`) in un `for` ciclo direttamente in un'espressione lambda. In caso contrario, la stessa variabile viene utilizzata da tutte `i`le espressioni lambda causando che il valore di sia lo stesso in tutte le espressioni lambda. Acquisire sempre il relativo valore in una variabile locale`buttonNumber` (nell'esempio precedente) e quindi usarlo.

### <a name="eventcallback"></a>EventCallback

Uno scenario comune con i componenti annidati è la volontà di eseguire il metodo di un componente padre quando si verifica&mdash;un evento del componente figlio `onclick` , ad esempio, quando si verifica un evento nell'elemento figlio. Per esporre gli eventi tra i componenti, `EventCallback`usare un oggetto. Un componente padre può assegnare un metodo di callback a un componente `EventCallback`figlio.

Nell'app di esempio illustra come viene configurato il `onclick` gestore di un pulsante per ricevere un `EventCallback` delegato dall'oggetto dell'esempio `ParentComponent`. `ChildComponent` Viene digitato `MouseEventArgs`con, appropriato per un `onclick` evento da un dispositivo periferico: `EventCallback`

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Imposta l'oggetto `EventCallback<T>` figlio sul relativo `ShowMessage` metodo: `ParentComponent`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

Quando il pulsante è selezionato in `ChildComponent`:

* Viene `ParentComponent`chiamato `ShowMessage` il metodo di. `messageText`viene aggiornato e visualizzato nell'oggetto `ParentComponent`.
* Una chiamata a `StateHasChanged` non è obbligatoria nel metodo del callback (`ShowMessage`). `StateHasChanged`viene chiamato automaticamente per eseguire il rendering `ParentComponent`di, proprio come gli eventi figlio attivano il rendering dei componenti nei gestori eventi che vengono eseguiti all'interno dell'elemento figlio.

`EventCallback`e `EventCallback<T>` consentono delegati asincroni. `EventCallback<T>`è fortemente tipizzato e richiede un tipo di argomento specifico. `EventCallback`è debolmente tipizzato e consente qualsiasi tipo di argomento.

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

`EventCallback` Richiamare o `EventCallback<T>` con e`InvokeAsync` attendere :<xref:System.Threading.Tasks.Task>

```csharp
await callback.InvokeAsync(arg);
```

Usare `EventCallback` e`EventCallback<T>` per la gestione degli eventi e i parametri del componente di associazione.

Preferisci l'oggetto `EventCallback<T>` fortemente `EventCallback`tipizzato. `EventCallback<T>`fornisce un migliore feedback sugli errori agli utenti del componente. Analogamente ad altri gestori di eventi dell'interfaccia utente, la specifica del parametro dell'evento è facoltativa. Usare `EventCallback` quando non è stato passato alcun valore al callback.

## <a name="chained-bind"></a>Binding concatenato

Uno scenario comune consiste nel concatenare un parametro associato a dati a un elemento Page nell'output del componente. Questo scenario è denominato *Binding concatenato* perché si verificano simultaneamente più livelli di associazione.

Un binding concatenato non può essere `@bind` implementato con la sintassi nell'elemento della pagina. Il gestore eventi e il valore devono essere specificati separatamente. Un componente padre, tuttavia, può utilizzare `@bind` la sintassi con il parametro del componente.

Il componente `PasswordField` seguente (*PasswordField. Razor*):

* Imposta il `<input>` valore di un elemento su `Password` una proprietà.
* Espone le modifiche della `Password` proprietà a un componente padre con un [EventCallback](#eventcallback).

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

Il `PasswordField` componente viene usato in un altro componente:

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Per eseguire controlli o intercettare gli errori sulla password nell'esempio precedente:

* Creare un campo di supporto per `Password` (`password` nell'esempio di codice seguente).
* Eseguire i controlli o gli errori di trap `Password` nel Setter.

Nell'esempio seguente viene fornito un feedback immediato all'utente se viene utilizzato uno spazio nel valore della password:

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a>Acquisisci riferimenti ai componenti

I riferimenti ai componenti forniscono un modo per fare riferimento a un'istanza del componente in modo da poter emettere comandi per `Show` tale `Reset`istanza, ad esempio o. Per acquisire un riferimento a un componente:

* Aggiungere un [@ref](xref:mvc/views/razor#ref) attributo al componente figlio.
* Definire un campo con lo stesso tipo del componente figlio.

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Quando viene eseguito il rendering del componente `loginDialog` , il campo viene popolato con l'istanza del `MyLoginDialog` componente figlio. È quindi possibile richiamare i metodi .NET nell'istanza del componente.

> [!IMPORTANT]
> La `loginDialog` variabile viene popolata solo dopo il rendering del componente e l'output include `MyLoginDialog` l'elemento. Fino a quel momento, non c'è niente a cui fare riferimento. Per modificare i riferimenti ai componenti dopo che il componente ha terminato il `OnAfterRenderAsync` rendering `OnAfterRender` , usare i metodi o.

Mentre l'acquisizione di riferimenti ai componenti usa una sintassi simile per l' [acquisizione di riferimenti a elementi](xref:blazor/javascript-interop#capture-references-to-elements), non è una funzionalità di [interoperabilità di JavaScript](xref:blazor/javascript-interop) . I riferimenti ai componenti non vengono passati&mdash;al codice JavaScript che vengono usati solo nel codice .NET.

> [!NOTE]
> **Non** usare i riferimenti ai componenti per mutare lo stato dei componenti figlio. Usare invece i normali parametri dichiarativi per passare i dati ai componenti figlio. L'utilizzo di normali parametri dichiarativi restituisce automaticamente i componenti figlio che eseguono il rendering alle ore corrette.

## <a name="invoke-component-methods-externally-to-update-state"></a>Richiama i metodi del componente esternamente per aggiornare lo stato

Blazor usa un `SynchronizationContext` per applicare un singolo thread logico di esecuzione. I metodi del ciclo di vita di un componente e tutti i callback di evento generati da Blazor vengono `SynchronizationContext`eseguiti in questo oggetto. Nel caso in cui un componente deve essere aggiornato in base a un evento esterno, ad esempio un timer o altre notifiche, `InvokeAsync` usare il metodo, che invierà a Blazor. `SynchronizationContext`

Si consideri ad esempio un *servizio Notifier* che può inviare notifiche a qualsiasi componente in ascolto dello stato aggiornato:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Utilizzo di `NotifierService` per aggiornare un componente:

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

Nell'esempio `NotifierService` precedente richiama il `OnNotify` metodo del componente `SynchronizationContext`all'esterno di Blazor. `InvokeAsync`viene usato per passare al contesto corretto e accodare un rendering.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Usare \@la chiave per controllare la conservazione di elementi e componenti

Quando si esegue il rendering di un elenco di elementi o componenti e gli elementi o i componenti successivamente cambiano, l'algoritmo di differenziazione di Blazor deve decidere quali elementi o componenti precedenti possono essere conservati e come eseguire il mapping degli oggetti modello. In genere, questo processo è automatico e può essere ignorato, ma in alcuni casi potrebbe essere necessario controllare il processo.

Si consideri l'esempio seguente:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Il contenuto della `People` raccolta può cambiare con le voci inserite, eliminate o riordinate. Quando viene eseguito il rendering del componente, `<DetailsEditor>` è possibile che il componente venga `Details` modificato in modo da ricevere valori di parametro diversi. Questo può causare un rirendering più complesso del previsto. In alcuni casi, il rirendering può comportare differenze di comportamento visibili, ad esempio lo stato attivo degli elementi persi.

È possibile controllare il processo di mapping con `@key` l'attributo della direttiva. `@key`fa in modo che l'algoritmo di diffing garantisca la conservazione di elementi o componenti in base al valore della chiave:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Quando la `People` raccolta viene modificata, l'algoritmo diffing mantiene l'associazione tra `<DetailsEditor>` istanze e `person` istanze:

* Se un `Person` oggetto viene eliminato `People` dall'elenco, solo l'istanza `<DetailsEditor>` corrispondente viene rimossa dall'interfaccia utente. Altre istanze rimangono invariate.
* Se un `Person` oggetto viene inserito in una determinata posizione nell'elenco, viene `<DetailsEditor>` inserita una nuova istanza in corrispondenza della posizione corrispondente. Altre istanze rimangono invariate.
* Se `Person` le voci vengono riordinate, le `<DetailsEditor>` istanze corrispondenti vengono mantenute e nuovamente ordinate nell'interfaccia utente.

In alcuni scenari, l'utilizzo `@key` di riduce al minimo la complessità del rendering ed evita potenziali problemi con le parti con stato del DOM che cambiano, ad esempio la posizione dello stato attivo.

> [!IMPORTANT]
> Le chiavi sono locali per ogni elemento contenitore o componente. Le chiavi non vengono confrontate globalmente nell'intero documento.

### <a name="when-to-use-key"></a>Quando usare \@la chiave

In genere, è opportuno usare `@key` ogni volta che viene eseguito il rendering di un elenco (ad esempio, in un `@foreach` blocco) e un `@key`valore appropriato per definire.

È anche possibile usare `@key` per impedire a Blazor di mantenere un sottoalbero di elementi o componenti quando un oggetto viene modificato:

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Se `@currentPerson` viene modificata, `@key` la direttiva attribute impone a Blazor di rimuovere `<div>` l'intero oggetto e i relativi discendenti e di ricompilare il sottoalbero all'interno dell'interfaccia utente con nuovi elementi e componenti. Questo può essere utile se è necessario garantire che lo stato dell'interfaccia utente non venga `@currentPerson` mantenuto quando viene modificato.

### <a name="when-not-to-use-key"></a>Quando non usare \@la chiave

Si verifica un costo in termini di prestazioni quando `@key`si verificano differenze con. Il costo delle prestazioni non è elevato, ma `@key` è sufficiente specificare solo se il controllo delle regole di conservazione degli elementi o dei componenti avvantaggia l'app.

Anche se `@key` non viene usato, Blazor conserva il più possibile le istanze di elementi e componenti figlio. L'unico vantaggio dell'utilizzo `@key` di è il controllo sulla *modalità* di mapping delle istanze del modello alle istanze dei componenti mantenute, anziché sull'algoritmo di diffing che seleziona il mapping.

### <a name="what-values-to-use-for-key"></a>Valori da usare per la \@chiave

In genere, è opportuno fornire uno dei seguenti tipi di valore per `@key`:

* Istanze di oggetti modello (ad esempio, `Person` un'istanza come nell'esempio precedente). In questo modo si garantisce la conservazione in base all'uguaglianza del riferimento all'oggetto.
* Identificatori univoci, ad esempio valori di chiave primaria di `int`tipo `string`, o `Guid`.

Verificare che i valori usati `@key` per non siano in conflitto. Se vengono rilevati valori in conflitto all'interno dello stesso elemento padre, Blazor genera un'eccezione perché non è in grado di eseguire il mapping deterministico di elementi o componenti precedenti a nuovi elementi o componenti. Utilizzare solo valori distinti, ad esempio istanze di oggetti o valori di chiave primaria.

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

`OnInitializedAsync`ed `OnInitialized` eseguono il codice per inizializzare il componente. Per eseguire un'operazione asincrona, usare `OnInitializedAsync` e la `await` parola chiave sull'operazione:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Per un'operazione sincrona, `OnInitialized`usare:

```csharp
protected override void OnInitialized()
{
    ...
}
```

`OnParametersSetAsync`e `OnParametersSet` vengono chiamati quando un componente riceve parametri dal padre e i valori vengono assegnati alle proprietà. Questi metodi vengono eseguiti dopo l'inizializzazione del componente e ogni volta che viene eseguito il rendering del componente:

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync`e `OnAfterRender` vengono chiamati dopo che un componente ha terminato il rendering. I riferimenti a elementi e componenti vengono popolati a questo punto. Usare questa fase per eseguire passaggi di inizializzazione aggiuntivi usando il contenuto sottoposto a rendering, ad esempio l'attivazione di librerie JavaScript di terze parti che operano sugli elementi DOM sottoposti a rendering.

`OnAfterRender`*non viene chiamato quando si esegue il prerendering sul server.*

Il `firstRender` parametro per `OnAfterRenderAsync` e `OnAfterRender` è:

* `true` Impostare la prima volta che l'istanza del componente viene richiamata.
* Garantisce che il lavoro di inizializzazione venga eseguito una sola volta.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>Gestisci azioni asincrone incomplete durante il rendering

Le azioni asincrone eseguite negli eventi del ciclo di vita potrebbero non essere state completate prima del rendering del componente. Gli oggetti possono `null` essere o compilati in modo non completo con i dati mentre è in esecuzione il metodo del ciclo di vita. Fornire la logica di rendering per confermare che gli oggetti vengono inizializzati. Esegue il rendering degli elementi dell'interfaccia utente segnaposto (ad esempio, un `null`messaggio di caricamento) mentre gli oggetti sono.

Nel componente `FetchData` dei `OnInitializedAsync` modelli di Blazor viene eseguito l'override di asincrono Receive forecast data (`forecasts`). Quando `forecasts` è`null`, all'utente viene visualizzato un messaggio di caricamento. Quando l' `Task` oggetto restituito `OnInitializedAsync` da viene completato, viene eseguito il rendering del componente con lo stato aggiornato.

*Pages/FetchData.razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>Esegui codice prima dell'impostazione dei parametri

`SetParameters`è possibile eseguire l'override del codice prima di impostare i parametri:

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Se `base.SetParameters` non viene richiamato, il codice personalizzato può interpretare il valore dei parametri in ingresso in qualsiasi modo necessario. I parametri in ingresso, ad esempio, non devono essere assegnati alle proprietà della classe.

### <a name="suppress-refreshing-of-the-ui"></a>Non aggiornare l'interfaccia utente

`ShouldRender`è possibile eseguire l'override di per non aggiornare l'interfaccia utente. Se l'implementazione restituisce `true`, viene aggiornata l'interfaccia utente. Anche se `ShouldRender` viene sottoposto a override, il componente viene sempre sottoposto a rendering iniziale.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>Eliminazione di componenti con IDisposable

Se un componente implementa <xref:System.IDisposable>, il [metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose) viene chiamato quando il componente viene rimosso dall'interfaccia utente. Il componente seguente utilizza `@implements IDisposable` e il `Dispose` metodo:

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>Routing

Il routing in Blazor viene effettuato fornendo un modello di route a ogni componente accessibile nell'app.

Quando viene compilato un file Razor `@page` con una direttiva, alla classe generata viene assegnato un <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> oggetto che specifica il modello di route. In fase di esecuzione, il router cerca le classi di `RouteAttribute` componenti con un oggetto ed esegue il rendering di qualsiasi componente con un modello di route corrispondente all'URL richiesto.

È possibile applicare più modelli di route a un componente. Il componente seguente risponde alle richieste per `/BlazorRoute` e: `/DifferentBlazorRoute`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Parametri di route

I componenti possono ricevere parametri di route dal modello di route fornito `@page` nella direttiva. Il router usa parametri di route per popolare i parametri del componente corrispondente.

*Componente parametro di route*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

I parametri facoltativi non sono supportati `@page` , quindi vengono applicate due direttive nell'esempio precedente. Il primo consente la navigazione al componente senza un parametro. La seconda `@page` direttiva accetta il `{text}` parametro Route e assegna il valore alla `Text` proprietà.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Ereditarietà della classe base per un'esperienza di "code-behind"

I file dei componenti combinano C# il markup HTML e il codice di elaborazione nello stesso file. La `@inherits` direttiva può essere usata per fornire alle app Blazor un'esperienza di "code-behind" che separa il markup dei componenti dal codice di elaborazione.

L' [app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) Mostra come un componente può ereditare una classe `BlazorRocksBase`base,, per fornire le proprietà e i metodi del componente.

*Pages/BlazorRocks. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

La classe base deve derivare `ComponentBase`da.

## <a name="import-components"></a>Importa componenti

Lo spazio dei nomi di un componente creato con Razor si basa su:

* Oggetto del progetto `RootNamespace`.
* Percorso dalla radice del progetto al componente. Ad esempio, `ComponentsSample/Pages/Index.razor` è nello spazio dei `ComponentsSample.Pages`nomi. I componenti C# seguono le regole di associazione dei nomi. Nel caso di *index. Razor*, tutti i componenti nella stessa cartella, le *pagine*e la cartella padre, *ComponentsSample*, rientrano nell'ambito.

I componenti definiti in uno spazio dei nomi diverso possono essere inseriti nell'ambito usando la direttiva [ \@using](xref:mvc/views/razor#using) di Razor.

Se nella cartella `ComponentsSample/Shared/`è `NavMenu.razor`presente un altro componente, il componente può essere utilizzato in `Index.razor` con l'istruzione seguente `@using` :

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

È anche possibile fare riferimento ai componenti usando i relativi nomi completi, che elimina la necessità della [ \@direttiva using](xref:mvc/views/razor#using) :

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> La `global::` qualificazione non è supportata.
>
> L'importazione di componenti con `using` istruzioni con alias (ad `@using Foo = Bar`esempio,) non è supportata.
>
> I nomi parzialmente qualificati non sono supportati. Ad esempio, l' `@using ComponentsSample` aggiunta e `NavMenu.razor` il `<Shared.NavMenu></Shared.NavMenu>` riferimento a non sono supportate.

## <a name="conditional-html-element-attributes"></a>Attributi dell'elemento HTML condizionale

Gli attributi degli elementi HTML vengono sottoposti a rendering in modo condizionale in base al valore .NET. Se il valore è `false` o `null`, l'attributo non viene sottoposto a rendering. Se il valore è `true`, l'attributo viene sottoposto a rendering ridotto a icona.

Nell'esempio seguente, `IsCompleted` determina se `checked` viene eseguito il rendering nel markup dell'elemento:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Se `IsCompleted` è`true`, viene eseguito il rendering della casella di controllo come segue:

```html
<input type="checkbox" checked />
```

Se `IsCompleted` è`false`, viene eseguito il rendering della casella di controllo come segue:

```html
<input type="checkbox" />
```

Per altre informazioni, vedere <xref:mvc/views/razor>.

> [!WARNING]
> Alcuni attributi HTML, ad esempio [aria-Pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), non funzionano correttamente quando il tipo .NET è `bool`. In questi casi, usare un `string` tipo anziché un. `bool`

## <a name="raw-html"></a>HTML non elaborato

Le stringhe vengono in genere sottoposte a rendering usando i nodi di testo DOM, il che significa che qualsiasi markup che può contenere viene ignorato e considerato come testo letterale. Per eseguire il rendering del codice HTML non elaborato, eseguire `MarkupString` il wrapping del contenuto HTML in un valore. Il valore viene analizzato in formato HTML o SVG e inserito nel DOM.

> [!WARNING]
> Il rendering di codice HTML non elaborato costruito da un'origine non attendibile costituisce un rischio per la **sicurezza** e deve essere evitato.

Nell'esempio seguente viene illustrato l' `MarkupString` utilizzo del tipo per aggiungere un blocco di contenuto HTML statico all'output sottoposto a rendering di un componente:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Componenti basati su modelli

I componenti basati su modelli sono componenti che accettano uno o più modelli di interfaccia utente come parametri, che possono quindi essere usati come parte della logica di rendering del componente. I componenti basati su modelli consentono di creare componenti di livello superiore più riutilizzabili rispetto ai componenti normali. Di seguito sono riportati alcuni esempi:

* Componente della tabella che consente a un utente di specificare i modelli per l'intestazione, le righe e il piè di pagina della tabella.
* Componente di elenco che consente a un utente di specificare un modello per il rendering degli elementi in un elenco.

### <a name="template-parameters"></a>Parametri di modelli

Un componente basato su modelli viene definito specificando uno o più parametri del componente `RenderFragment` di `RenderFragment<T>`tipo o. Un frammento di rendering rappresenta un segmento di interfaccia utente di cui eseguire il rendering. `RenderFragment<T>`accetta un parametro di tipo che può essere specificato quando viene richiamato il frammento di rendering.

`TableTemplate`componente

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

Quando si usa un componente basato su modelli, i parametri del modello possono essere specificati usando gli elementi figlio che corrispondono ai nomi`TableHeader` dei `RowTemplate` parametri (e nell'esempio seguente):

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a>Parametri di contesto del modello

Gli argomenti del componente `RenderFragment<T>` di tipo passati come elementi hanno un parametro `context` implicito denominato (ad esempio, nell'esempio `@context.PetId`di codice precedente,), ma è possibile modificare il `Context` nome del parametro usando l'attributo nell'elemento figlio elemento. Nell'esempio seguente, l' `RowTemplate` `Context` attributo dell'elemento specifica il `pet` parametro:

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

In alternativa, è possibile specificare l' `Context` attributo sull'elemento Component. L'attributo `Context` specificato si applica a tutti i parametri di modello specificati. Questa operazione può essere utile quando si desidera specificare il nome del parametro di contenuto per il contenuto figlio implicito (senza alcun elemento figlio di wrapping). Nell'esempio seguente l' `Context` attributo viene visualizzato `TableTemplate` nell'elemento e si applica a tutti i parametri del modello:

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a>Componenti tipizzati in modo generico

I componenti basati su modelli spesso sono tipizzati in modo generico. È ad esempio possibile utilizzare `ListViewTemplate` un componente generico per eseguire il `IEnumerable<T>` rendering dei valori. Per definire un componente generico, usare la [@typeparam](xref:mvc/views/razor#typeparam) direttiva per specificare i parametri di tipo:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

Quando si usano componenti tipizzati generici, il parametro di tipo viene dedotto, se possibile:

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

In caso contrario, il parametro di tipo deve essere specificato in modo esplicito utilizzando un attributo che corrisponde al nome del parametro di tipo. Nell'esempio seguente viene `TItem="Pet"` specificato il tipo:

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Parametri e valori di propagazione

In alcuni scenari, non è pratico eseguire il flusso dei dati da un componente predecessore a un componente discendente usando i [parametri del componente](#component-parameters), soprattutto quando sono presenti diversi livelli di componenti. I valori e i parametri di propagazione consentono di risolvere questo problema fornendo un modo pratico per un componente predecessore per fornire un valore a tutti i relativi componenti discendenti. I parametri e i valori di propagazione forniscono anche un approccio per la coordinazione dei componenti.

### <a name="theme-example"></a>Esempio di tema

Nell'esempio seguente dall'app di esempio, la `ThemeInfo` classe specifica le informazioni sul tema per scorrere la gerarchia dei componenti in modo che tutti i pulsanti all'interno di una determinata parte dell'app condividono lo stesso stile.

*UIThemeClasses/themeinfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Un componente predecessore può fornire un valore di propagazione utilizzando il componente valore di propagazione. Il `CascadingValue` componente esegue il wrapping di un sottoalbero della gerarchia dei componenti e fornisce un singolo valore a tutti i componenti all'interno di tale sottoalbero.

Ad esempio, l'app di esempio specifica le informazioni`ThemeInfo`sul tema () in uno dei layout dell'app come parametro di propagazione per tutti i componenti che costituiscono il corpo `@Body` del layout della proprietà. `ButtonClass`viene assegnato un valore di `btn-success` nel componente layout. Qualsiasi componente discendente può utilizzare questa proprietà tramite `ThemeInfo` l'oggetto a cascata.

`CascadingValuesParametersLayout`componente

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Per utilizzare i valori di propagazione, i componenti dichiarano i `[CascadingParameter]` parametri di propagazione utilizzando l'attributo. I valori a cascata vengono associati ai parametri di propagazione per tipo.

Nell'app di esempio, il `CascadingValuesParametersTheme` componente associa il `ThemeInfo` valore a cascata a un parametro di propagazione. Il parametro viene usato per impostare la classe CSS per uno dei pulsanti visualizzati dal componente.

`CascadingValuesParametersTheme`componente

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Per eseguire il propagazione di più valori dello stesso tipo all'interno dello stesso `Name` sottoalbero, `CascadingValue` fornire una stringa univoca a ogni componente e la corrispondente `CascadingParameter`. Nell'esempio seguente due `CascadingValue` componenti propagano istanze diverse di `MyCascadingType` per nome:

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

In un componente discendente i parametri a cascata ricevono i rispettivi valori dai valori a cascata corrispondenti nel componente predecessore per nome:

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Esempio di tabulazione

I parametri di propagazione consentono inoltre ai componenti di collaborare attraverso la gerarchia dei componenti. Si consideri, ad esempio, l'esempio di *tabulazione* seguente nell'app di esempio.

L'app di esempio ha `ITab` un'interfaccia implementata dalle schede:

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

Il `CascadingValuesParametersTabSet` componente usa il `TabSet` componente, che contiene diversi `Tab` componenti:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

I componenti `Tab` figlio non vengono passati in modo esplicito come `TabSet`parametri a. Al contrario, i `Tab` componenti figlio fanno parte del contenuto figlio `TabSet`di. Tuttavia, `TabSet` è comunque necessario conoscere ogni `Tab` componente in modo che sia in grado di eseguire il rendering delle intestazioni e della scheda attiva. Per abilitare questo coordinamento senza richiedere codice aggiuntivo, il `TabSet` componente *può fornire se stesso come valore* di propagazione che viene quindi prelevato dai `Tab` componenti discendenti.

`TabSet`componente

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

I componenti `Tab` discendenti acquisiscono `TabSet` l'oggetto che contiene come parametro di propagazione, in modo `TabSet` che i `Tab` componenti si aggiungano alla coordinata e della scheda attiva.

`Tab`componente

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Modelli Razor

I frammenti di rendering possono essere definiti usando la sintassi del modello Razor. I modelli Razor sono un modo per definire un frammento di interfaccia utente e presupporre il formato seguente:

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

Nell'esempio seguente viene illustrato come specificare `RenderFragment` i valori e `RenderFragment<T>` ed eseguire il rendering dei modelli direttamente in un componente. I frammenti di rendering possono anche essere passati come argomenti ai [componenti basati su modelli](#templated-components).

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Output del codice precedente sottoposto a rendering:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>Logica RenderTreeBuilder manuale

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`fornisce metodi per la modifica di componenti ed elementi, inclusa la compilazione manuale C# dei componenti nel codice.

> [!NOTE]
> L'utilizzo `RenderTreeBuilder` di per la creazione di componenti è uno scenario avanzato. Un componente con formato non valido, ad esempio un tag di markup non chiuso, può causare un comportamento indefinito.

Si consideri il componente seguente `PetDetails` , che può essere incorporato manualmente in un altro componente:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Nell'esempio seguente il ciclo nel `CreateComponent` metodo genera tre `PetDetails` componenti. Quando si `RenderTreeBuilder` chiamano i metodi per creare i`OpenComponent` componenti `AddAttribute`(e), i numeri di sequenza sono numeri di riga del codice sorgente. L'algoritmo di differenza Blaze si basa sui numeri di sequenza corrispondenti a righe di codice distinte, non sulle chiamate di chiamata distinti. Quando si crea un componente `RenderTreeBuilder` con i metodi, impostare come hardcoded gli argomenti per i numeri di sequenza. **L'utilizzo di un calcolo o di un contatore per generare il numero di sequenza può causare un calo delle prestazioni.** Per ulteriori informazioni, vedere la sezione [numeri di sequenza correlati ai numeri di riga del codice e non all'ordine di esecuzione](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent`componente

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> ! AVVISO I tipi in `Microsoft.AspNetCore.Components.RenderTree` consentono l'elaborazione dei *risultati* delle operazioni di rendering. Questi sono i dettagli interni dell'implementazione del Framework Blazor. Questi tipi devono essere considerati *instabili* e soggetti a modifiche nelle versioni future.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>I numeri di sequenza sono correlati ai numeri di riga del codice e non all'ordine di esecuzione

`.razor` I file Blazor vengono sempre compilati. Questo è potenzialmente un ottimo vantaggio per `.razor` perché il passaggio di compilazione può essere usato per inserire informazioni che migliorano le prestazioni dell'app in fase di esecuzione.

Un esempio fondamentale di questi miglioramenti riguarda i *numeri di sequenza*. I numeri di sequenza indicano al runtime quali output provengono da righe di codice distinte e ordinate. Il runtime usa queste informazioni per generare differenze di albero efficienti nel tempo lineare, che è molto più veloce rispetto a quanto normalmente è possibile per un algoritmo diff della struttura ad albero generale.

Si consideri `.razor` il seguente file semplice:

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

Il codice precedente viene compilato in un modo simile al seguente:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Quando il codice viene eseguito per la prima volta, se `someFlag` è `true`, il generatore riceve:

| Sequenza | Type      | Data   |
| :------: | --------- | :----: |
| 0        | Nodo testo | Primo  |
| 1        | Nodo testo | Secondo |

Si supponga `someFlag` che `false`diventi e che venga eseguito nuovamente il rendering del markup. Questa volta, il generatore riceve:

| Sequenza | Type       | Data   |
| :------: | ---------- | :----: |
| 1        | Nodo testo  | Secondo |

Quando il runtime esegue una diff, rileva che l'elemento in sequenza `0` è stato rimosso, quindi genera lo script di *modifica*semplice seguente:

* Rimuovere il primo nodo di testo.

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>Cosa accade se si generano numeri di sequenza a livello di codice

Si supponga invece che sia stata scritta la logica del generatore di albero di rendering seguente:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

A questo punto, il primo output è:

| Sequenza | Type      | Data   |
| :------: | --------- | :----: |
| 0        | Nodo testo | Primo  |
| 1        | Nodo testo | Secondo |

Questo risultato è identico al caso precedente, pertanto non esistono problemi negativi. `someFlag`si `false` trova nel secondo rendering e l'output è:

| Sequenza | Type      | Data   |
| :------: | --------- | ------ |
| 0        | Nodo testo | Secondo |

Questa volta, l'algoritmo Diff rileva che si sono verificate *due* modifiche e l'algoritmo genera lo script di modifica seguente:

* Modificare il valore del primo nodo di testo in `Second`.
* Rimuovere il secondo nodo di testo.

La generazione dei numeri di sequenza ha perso tutte le informazioni utili sul `if/else` punto in cui i rami e i cicli erano presenti nel codice originale. In questo modo si ottiene una differenza **due volte più a lungo** .

Questo è un esempio semplice. Nei casi più realistici con strutture complesse e profondamente annidate e soprattutto con i cicli, il costo delle prestazioni è più grave. Invece di identificare immediatamente i blocchi o i rami del ciclo che sono stati inseriti o rimossi, l'algoritmo Diff deve ripresentarsi in modo approfondito negli alberi di rendering e, in genere, compilare script di modifica molto più lunghi perché non è stato informato in modo indesiderato sul modo in cui le nuove strutture relazione tra loro.

#### <a name="guidance-and-conclusions"></a>Linee guida e conclusioni

* Le prestazioni dell'app soffrono se i numeri di sequenza vengono generati dinamicamente.
* Il Framework non è in grado di creare automaticamente i propri numeri di sequenza in fase di esecuzione perché le informazioni necessarie non esistono a meno che non vengano acquisite in fase di compilazione.
* Non scrivere blocchi lunghi di logica implementata `RenderTreeBuilder` manualmente. Preferire `.razor` i file e consentire al compilatore di gestire i numeri di sequenza. Se non si è in grado di `RenderTreeBuilder` evitare la logica manuale, suddividere i blocchi di codice lunghi `OpenRegion` in parti più piccole racchiuse tra / `CloseRegion` le chiamate. Ogni area ha il proprio spazio separato dei numeri di sequenza, quindi è possibile riavviare da zero (o qualsiasi altro numero arbitrario) all'interno di ogni area.
* Se i numeri di sequenza sono hardcoded, l'algoritmo Diff richiede solo che i numeri di sequenza aumentino nel valore. Il valore iniziale e i gap sono irrilevanti. Una delle opzioni legittime consiste nell'usare il numero di riga del codice come numero di sequenza oppure iniziare da zero e aumentare di uno o di centinaia (o qualsiasi intervallo preferito). 
* Blazor usa i numeri di sequenza, mentre altri Framework dell'interfaccia utente con differenze tra gli alberi non li usano. La diffing è molto più veloce quando si usano i numeri di sequenza e Blazor ha il vantaggio di un passaggio di compilazione che riguarda automaticamente i numeri di `.razor` sequenza per gli sviluppatori che creano file.

## <a name="localization"></a>Localizzazione

Le app del server Blazor vengono localizzate usando il [middleware di localizzazione](xref:fundamentals/localization#localization-middleware). Il middleware seleziona le impostazioni cultura appropriate per gli utenti che richiedono risorse dall'app.

Le impostazioni cultura possono essere impostate utilizzando uno degli approcci seguenti:

* [Cookie](#cookies)
* [Fornire l'interfaccia utente per scegliere le impostazioni cultura](#provide-ui-to-choose-the-culture)

Per altre informazioni ed esempi, vedere <xref:fundamentals/localization>.

### <a name="cookies"></a>Cookie

Un cookie di impostazioni cultura di localizzazione può salvare in maniera permanente le impostazioni cultura dell'utente. Il cookie viene creato tramite il `OnGet` metodo della pagina host dell'app (*pages/host. cshtml. cs*). Il middleware di localizzazione legge il cookie nelle richieste successive per impostare le impostazioni cultura dell'utente. 

L'uso di un cookie garantisce che la connessione WebSocket possa propagare correttamente le impostazioni cultura. Se gli schemi di localizzazione sono basati sul percorso dell'URL o sulla stringa di query, lo schema potrebbe non essere in grado di funzionare con WebSocket, quindi non è possibile salvare in modo permanente le impostazioni cultura. Pertanto, l'utilizzo di un cookie di impostazioni cultura di localizzazione è l'approccio consigliato.

Qualsiasi tecnica può essere utilizzata per assegnare impostazioni cultura se le impostazioni cultura vengono rese permanente in un cookie di localizzazione. Se l'app dispone già di uno schema di localizzazione stabilito per ASP.NET Core lato server, continuare a usare l'infrastruttura di localizzazione esistente dell'app e impostare il cookie delle impostazioni cultura di localizzazione nello schema dell'app.

Nell'esempio seguente viene illustrato come impostare le impostazioni cultura correnti in un cookie che può essere letto dal middleware di localizzazione. Creare un file *pages/host. cshtml. cs* con il contenuto seguente nell'app del server Blazor:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

La localizzazione viene gestita nell'app:

1. Il browser invia una richiesta HTTP iniziale all'app.
1. Le impostazioni cultura vengono assegnate dal middleware di localizzazione.
1. Il metodo `OnGet` in *_Host. cshtml. cs* rende permanente le impostazioni cultura di un cookie come parte della risposta.
1. Il browser apre una connessione WebSocket per creare una sessione del server Interactive Blaze.
1. Il middleware di localizzazione legge il cookie e assegna le impostazioni cultura.
1. La sessione del server Blaze inizia con le impostazioni cultura corrette.

## <a name="provide-ui-to-choose-the-culture"></a>Fornire l'interfaccia utente per scegliere le impostazioni cultura

Per consentire a un utente di selezionare le impostazioni cultura, è consigliabile un *approccio basato su Reindirizzamento* . Il processo è simile a quello che accade in un'app Web quando un utente tenta di accedere a una&mdash;risorsa protetta che l'utente viene reindirizzato a una pagina di accesso e quindi viene reindirizzato di nuovo alla risorsa originale. 

L'app rende permanente le impostazioni cultura selezionate dall'utente tramite un reindirizzamento a un controller. Il controller imposta le impostazioni cultura selezionate dall'utente in un cookie e reindirizza di nuovo l'utente all'URI originale.

Stabilire un endpoint HTTP nel server per impostare le impostazioni cultura selezionate dall'utente in un cookie ed eseguire di nuovo il reindirizzamento all'URI originale:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Usare il `LocalRedirect` risultato dell'azione per impedire gli attacchi di reindirizzamento aperti. Per altre informazioni, vedere <xref:security/preventing-open-redirects>.

Il componente seguente mostra un esempio di come eseguire il reindirizzamento iniziale quando l'utente seleziona le impostazioni cultura:

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>Usare scenari di localizzazione .NET in app Blazor

All'interno delle app Blazor sono disponibili i seguenti scenari di globalizzazione e localizzazione di .NET:

* . Sistema di risorse di NET
* Formattazione di numeri e date specifiche delle impostazioni cultura

La funzionalità di `@bind` Blazor esegue la globalizzazione in base alle impostazioni cultura correnti dell'utente. Per ulteriori informazioni, vedere la sezione [Data Binding](#data-binding) .

Sono attualmente supportati un set limitato di scenari di localizzazione di ASP.NET Core:

* `IStringLocalizer<>`*è supportato* nelle app Blazor.
* `IHtmlLocalizer<>`la `IViewLocalizer<>`localizzazione delle annotazioni dei dati, e è ASP.NET Core scenari MVC e **non è supportata** nelle app Blazor.

Per altre informazioni, vedere <xref:fundamentals/localization>.

## <a name="scalable-vector-graphics-svg-images"></a>Immagini SVG (Scalable Vector Graphics)

Poiché Blazor esegue il rendering di HTML, le immagini supportate dal browser, incluse le immagini SVG (Scalable Vector*Graphics),* sono supportate tramite `<img>` il tag:

```html
<img alt="Example image" src="some-image.svg" />
```

Analogamente, le immagini SVG sono supportate nelle regole CSS di un file di foglio di stile (*CSS*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Tuttavia, il markup SVG inline non è supportato in tutti gli scenari. Se si inserisce un `<svg>` tag direttamente in un file di componente (*Razor*), il rendering delle immagini di base è supportato, ma molti scenari avanzati non sono ancora supportati. Ad esempio, `<use>` i tag non sono attualmente rispettati e `@bind` non possono essere usati con alcuni tag svg. Queste limitazioni verranno affrontate in una versione futura.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/blazor/server>&ndash; Include informazioni aggiuntive sulla creazione di app del server Blazor che devono essere confrontate con l'esaurimento delle risorse.

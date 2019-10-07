---
title: Chiamare un'API Web da ASP.NET Core Blazor
author: guardrex
description: Informazioni su come chiamare un'API Web da un'app Blazor usando Helper JSON, inclusa la creazione di richieste di condivisione di risorse tra le origini (CORS).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/call-web-api
ms.openlocfilehash: ea819da57c382b724098c55f2f799d7deea363f2
ms.sourcegitcommit: 4649814d1ae32248419da4e8f8242850fd8679a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975645"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Chiamare un'API Web da ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Le app webassembly Blazor chiamano API Web usando un servizio `HttpClient` preconfigurato. Comporre richieste, che possono includere opzioni [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, usando Helper JSON di Blazor o con <xref:System.Net.Http.HttpRequestMessage>.

Le app del server Blazor chiamano API Web usando le istanze <xref:System.Net.Http.HttpClient> in genere create con <xref:System.Net.Http.IHttpClientFactory>. Per altre informazioni, vedere <xref:fundamentals/http-requests>.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

Per esempi di webassembly Blazor, vedere i componenti seguenti nell'app di esempio:

* Chiama API Web (*pages/CallWebAPI. Razor*)
* Tester richieste HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="httpclient-and-json-helpers"></a>Helper HttpClient e JSON

Nelle app webassembly di Blazor, [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per l'esecuzione delle richieste al server di origine. Per usare gli helper JSON `HttpClient`, aggiungere un riferimento al pacchetto `Microsoft.AspNetCore.Blazor.HttpClient`. gli helper `HttpClient` e JSON vengono usati anche per chiamare endpoint dell'API Web di terze parti. `HttpClient` viene implementato usando l' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) del browser ed è soggetto alle limitazioni, inclusa l'applicazione degli stessi criteri di origine.

L'indirizzo di base del client viene impostato sull'indirizzo del server di origine. Inserire un'istanza `HttpClient` usando la direttiva `@inject`:

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

Negli esempi seguenti, un'API Web todo elabora le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD). Gli esempi sono basati su una classe `TodoItem` che archivia:

* ID (`Id`, `long`) &ndash; ID univoco dell'elemento.
* Nome (`Name`, `string`) &ndash; nome dell'elemento.
* Status (`IsComplete`, `bool`) &ndash; indica se l'elemento todo è terminato.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

I metodi helper JSON inviano richieste a un URI (un'API Web negli esempi seguenti) ed elaborano la risposta:

* `GetJsonAsync` &ndash; invia una richiesta HTTP GET e analizza il corpo della risposta JSON per creare un oggetto.

  Nel codice seguente, il `_todoItems` viene visualizzato dal componente. Il metodo `GetTodoItems` viene attivato quando il componente ha terminato il rendering ([OnInitializedAsync](xref:blazor/components#lifecycle-methods)). Per un esempio completo, vedere l'app di esempio.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash; invia una richiesta HTTP POST, incluso il contenuto con codifica JSON, e analizza il corpo della risposta JSON per creare un oggetto.

  Nel codice seguente, `_newItemName` viene fornito da un elemento associato del componente. Il metodo `AddItem` viene attivato selezionando un elemento `<button>`. Per un esempio completo, vedere l'app di esempio.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* `PutJsonAsync` &ndash; invia una richiesta HTTP PUT, incluso il contenuto con codifica JSON.

  Nel codice seguente, i valori `_editItem` per `Name` e `IsCompleted` sono forniti dagli elementi associati del componente. Il `Id` dell'elemento viene impostato quando l'elemento è selezionato in un'altra parte dell'interfaccia utente e viene chiamato `EditItem`. Il metodo `SaveItem` viene attivato selezionando l'elemento Save `<button>`. Per un esempio completo, vedere l'app di esempio.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http> include metodi di estensione aggiuntivi per l'invio di richieste HTTP e la ricezione di risposte HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) viene usato per inviare una richiesta HTTP DELETE a un'API Web.

Nel codice seguente, l'elemento Delete `<button>` chiama il metodo `DeleteItem`. L'elemento `<input>` associato fornisce il `id` dell'elemento da eliminare. Per un esempio completo, vedere l'app di esempio.

```cshtml
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>Condivisione di risorse tra le origini (CORS)

La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina Web. Questa restrizione è detta *criterio della stessa origine*. Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito. Per eseguire richieste dal browser a un endpoint con un'origine diversa, l' *endpoint* deve abilitare la [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/).

L'app di esempio illustra l'uso di CORS nel componente API Web Call (*pages/CallWebAPI. Razor*).

Per consentire ad altri siti di effettuare richieste di condivisione risorse tra le origini (CORS) per l'app, vedere <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage con le opzioni di richiesta dell'API fetch

Quando si esegue su webassembly in un'app webassembly Blazor, usare [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> per personalizzare le richieste. Ad esempio, è possibile specificare l'URI della richiesta, il metodo HTTP e tutte le intestazioni di richiesta desiderate.

Specificare le opzioni di richiesta per l' [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript sottostante usando la proprietà `WebAssemblyHttpMessageHandler.FetchArgs` della richiesta. Come illustrato nell'esempio seguente, la proprietà `credentials` è impostata su uno dei valori seguenti:

* `FetchCredentialsOption.Include` ("include") &ndash; indica al browser di inviare le credenziali (ad esempio, cookie o intestazioni di autenticazione HTTP) anche per le richieste tra origini. Consentito solo quando il criterio CORS è configurato per consentire le credenziali.
* `FetchCredentialsOption.Omit` ("omette") &ndash; indica al browser di non inviare mai le credenziali (ad esempio, cookie o intestazioni di autenticazione HTTP).
* `FetchCredentialsOption.SameOrigin` ("stessa origine") &ndash; indica al browser di inviare credenziali, ad esempio cookie o intestazioni di autenticazione HTTP, solo se l'URL di destinazione si trova nella stessa origine dell'applicazione chiamante.

```cshtml
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");
        
        requestMessage.Properties[WebAssemblyHttpMessageHandler.FetchArgs] = new
        { 
            credentials = FetchCredentialsOption.Include
        };

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

Per altre informazioni sulle opzioni di recupero delle API, vedere [MDN Web docs: WindowOrWorkerGlobalScope. fetch ():P arameters @ no__t-0.

Quando si inviano le credenziali (cookie/intestazioni di autorizzazione) nelle richieste CORS, l'intestazione `Authorization` deve essere consentita dal criterio CORS.

Il criterio seguente include la configurazione per:

* Origin della richiesta (`http://localhost:5000`, `https://localhost:5001`).
* Qualsiasi metodo (verbo).
* intestazioni `Content-Type` e `Authorization`. Per consentire un'intestazione personalizzata (ad esempio, `x-custom-header`), elencare l'intestazione quando si chiama <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.
* Credenziali impostate dal codice JavaScript lato client (la proprietà `credentials` è impostata su `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Per altre informazioni, vedere <xref:security/cors> e il componente del tester di richiesta HTTP dell'app di esempio (*Components/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configurazione dell'endpoint HTTPS di gheppio](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Condivisione di risorse tra le origini (CORS) in W3C](https://www.w3.org/TR/cors/)

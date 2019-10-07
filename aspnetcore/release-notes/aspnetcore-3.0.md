---
title: Novità di ASP.NET Core 3,0
author: rick-anderson
description: Informazioni sulle nuove funzionalità di ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: aspnetcore-3.0
ms.openlocfilehash: ec3de5b35883752b7b3dbefceccec55da3986f39
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703674"
---
# <a name="whats-new-in-aspnet-core-30"></a>Novità di ASP.NET Core 3,0

In questo articolo vengono evidenziate le modifiche più significative in ASP.NET Core 3,0 con i collegamenti alla documentazione pertinente.

## <a name="blazor"></a>Blazor

Blazor è un nuovo Framework in ASP.NET Core per la creazione di un'interfaccia utente Web interattiva sul lato client con .NET:

* Creare interfacce utente interattive avanzate con C# invece di JavaScript.
* Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.
* Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.

Scenari supportati dal Framework Blazor:

* Componenti dell'interfaccia utente riutilizzabili (componenti Razor)
* Routing lato client
* Layout componenti
* Supporto per l'inserimento di dipendenze
* Moduli e convalida
* Compilare librerie di componenti con le librerie di classi Razor
* Interoperabilità JavaScript

Per altre informazioni, vedere <xref:blazor/index>.

### <a name="blazor-server"></a>Server Blazor

Blazor separa la logica di rendering dei componenti dal modo in cui vengono applicati gli aggiornamenti dell'interfaccia utente. Il server Blazor fornisce il supporto per l'hosting di componenti Razor sul server in un'app ASP.NET Core. Tutti gli aggiornamenti dell'interfaccia utente vengono gestiti tramite una connessione SignalR. Il server Blazor è supportato in ASP.NET Core 3,0.

### <a name="blazor-webassembly-preview"></a>Webassembly Blazor (anteprima)

Le app Blazor possono anche essere eseguite direttamente nel browser usando un Runtime .NET basato su webassembly. Il webassembly Blazor è in anteprima e *non* è supportato in ASP.NET Core 3,0. Il webassembly Blazor sarà supportato in una versione futura di ASP.NET Core.

### <a name="razor-components"></a>Componenti Razor

Le app Blazor sono compilate da componenti. I componenti sono blocchi autonomi dell'interfaccia utente (UI), ad esempio una pagina, una finestra di dialogo o un form. I componenti sono classi .NET normali che definiscono la logica di rendering dell'interfaccia utente e i gestori eventi sul lato client. È possibile creare app Web interattive avanzate senza JavaScript.

I componenti in Blazor vengono in genere creati usando sintassi Razor, una combinazione naturale di HTML C#e. I componenti Razor sono simili alle visualizzazioni Razor Pages e MVC in quanto entrambi utilizzano Razor. Diversamente dalle pagine e dalle viste, basate su un modello di richiesta-risposta, i componenti vengono usati in modo specifico per la gestione della composizione dell'interfaccia utente.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* È un noto Framework RPC (Remote Procedure Call) a prestazioni elevate.
* Offre un approccio di primo contratto per lo sviluppo di API.
* Usa tecnologie moderne, ad esempio:

  * HTTP/2 per il trasporto.
  * Buffer del protocollo come linguaggio di descrizione dell'interfaccia.
  * Formato di serializzazione binario.
* Fornisce funzionalità come:

  * Authentication
  * Flusso bidirezionale e controllo di flusso.
  * Annullamento e timeout.

la funzionalità gRPC in ASP.NET Core 3,0 include:

* [Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; un framework ASP.NET Core per l'hosting di servizi Grpc. gRPC su ASP.NET Core si integra con funzionalità di ASP.NET Core standard come la registrazione, l'inserimento DI dipendenze, l'autenticazione e l'autorizzazione.
* [Grpc .NET. client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Grpc client for .NET Core che si basa sul familiare `HttpClient`.
* [Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; Grpc Client Integration con `HttpClientFactory`.

Per altre informazioni, vedere <xref:grpc/index>.

## <a name="signalr"></a>SignalR

Per istruzioni sulla migrazione, vedere [aggiornare il codice SignalR](xref:migration/22-to-30#signalr) . SignalR ora usa `System.Text.Json` per serializzare/deserializzare i messaggi JSON. Per istruzioni su come ripristinare il `Newtonsoft.Json`serializzatore basato su, vedere [passare a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) .

Nei client JavaScript e .NET per SignalR è stato aggiunto il supporto per la riconnessione automatica. Per impostazione predefinita, il client tenta di riconnettersi immediatamente e riprovare dopo 2, 10 e 30 secondi, se necessario. Se il client si riconnette correttamente, riceve un nuovo ID connessione. La riconnessione automatica è esplicita:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

È possibile specificare gli intervalli di riconnessione passando una matrice di durate basate su millisecondi:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

È possibile passare un'implementazione personalizzata per il controllo completo degli intervalli di riconnessione.

Se la riconnessione non riesce dopo l'ultimo intervallo di riconnessione:

* Il client considera la connessione offline.
* Il client smette di tentare di riconnettersi.

Durante i tentativi di riconnessione, aggiornare l'interfaccia utente dell'app per notificare all'utente che è in corso il tentativo di riconnessione.

Per fornire commenti e suggerimenti dell'interfaccia utente quando la connessione viene interrotta, l'API client SignalR è stata espansa in modo da includere i gestori eventi seguenti:

* `onreconnecting`:  Offre agli sviluppatori la possibilità di disabilitare l'interfaccia utente o di informare gli utenti che l'app è offline.
* `onreconnected`: Offre agli sviluppatori la possibilità di aggiornare l'interfaccia utente una volta ristabilita la connessione.

Il codice seguente usa `onreconnecting` per aggiornare l'interfaccia utente durante il tentativo di connessione:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Il codice seguente usa `onreconnected` per aggiornare l'interfaccia utente nella connessione:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR 3,0 e versioni successive fornisce una risorsa personalizzata ai gestori di autorizzazione quando un metodo Hub richiede l'autorizzazione. La risorsa è un'istanza di `HubInvocationContext`. `HubInvocationContext` Include:

* `HubCallerContext`
* Nome del metodo Hub richiamato.
* Argomenti per il metodo dell'hub.

Si consideri l'esempio seguente di un'app Chat Room che consente l'accesso a più organizzazioni tramite Azure Active Directory. Chiunque disponga di un account Microsoft può accedere a chat, ma solo i membri dell'organizzazione proprietaria possono vietare gli utenti o visualizzare le cronologie della chat degli utenti. L'app potrebbe limitare determinate funzionalità di utenti specifici.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

Nel codice precedente, `DomainRestrictedRequirement` funge da personalizzato. `IAuthorizationRequirement` Poiché il `HubInvocationContext` parametro Resource viene passato, la logica interna può:

* Esaminare il contesto in cui viene chiamato l'hub.
* Prendere decisioni per consentire all'utente di eseguire singoli metodi dell'hub.

I singoli metodi dell'hub possono essere decorati con il nome dei criteri controllati dal codice in fase di esecuzione. Poiché i client tentano di chiamare i singoli metodi `DomainRestrictedRequirement` dell'hub, il gestore esegue e controlla l'accesso ai metodi. In base alla modalità di `DomainRestrictedRequirement` accesso dei controlli:

* Tutti gli utenti connessi possono chiamare il `SendMessage` metodo.
* Solo gli utenti che hanno eseguito l'accesso `@jabbr.net` con un indirizzo di posta elettronica possono visualizzare le cronologie degli utenti.
* È `bob42@jabbr.net` possibile vietare gli utenti solo dalla chat room.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

La creazione `DomainRestricted` dei criteri può implicare:

* In *Startup.cs*aggiungere il nuovo criterio.
* Fornire il requisito `DomainRestrictedRequirement` personalizzato come parametro.
* `DomainRestricted` Registrazione con il middleware di autorizzazione.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

Gli hub SignalR usano il [routing degli endpoint](xref:fundamentals/routing). La connessione all'hub SignalR è stata eseguita in precedenza in modo esplicito:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Nella versione precedente, gli sviluppatori dovevano collegare i controller, le pagine Razor e gli hub in un'ampia gamma di posizioni diverse. La connessione esplicita genera una serie di segmenti di routing quasi identici:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

Gli hub SignalR 3,0 possono essere indirizzati tramite il routing degli endpoint. Con il routing degli endpoint, in genere tutti i routing `UseRouting`possono essere configurati in:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3,0 SignalR aggiunto:

Streaming da client a server. Con il flusso da client a server, i metodi lato server possono utilizzare istanze di `IAsyncEnumerable<T>` o. `ChannelReader<T>` Nell'esempio seguente C# il `UploadStream` metodo nell'hub riceverà un flusso di stringhe dal client:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Le `IAsyncEnumerable<T>` `ChannelReader<T>` `UploadStream` app client .NETpossonopassareun'istanzadiocomeargomentodelmetodohubsoprariportato.`stream`

Al termine `for` del ciclo e alla chiusura della funzione locale, viene inviato il completamento del flusso:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Le app client JavaScript usano il `Subject` SignalR (o un [oggetto RxJS](https://rxjs.dev/api/index/class/Subject)) per `stream` `UploadStream` l'argomento del metodo hub sopra riportato.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Il codice JavaScript può usare il `subject.next` metodo per gestire le stringhe Man mano che vengono acquisite e pronte per essere inviate al server.

```javascript
subject.next("example");
subject.complete();
```

Utilizzando codice come i due frammenti precedenti, è possibile creare esperienze di streaming in tempo reale.

### <a name="new-json-serialization"></a>Nuova serializzazione JSON

ASP.NET Core 3,0 ora usa <xref:System.Text.Json> per impostazione predefinita la serializzazione JSON:

* Legge e scrive JSON in modo asincrono.
* È ottimizzato per il testo UTF-8.
* Prestazioni in genere superiori `Newtonsoft.Json`rispetto a.

Per aggiungere Json.NET a ASP.NET Core 3,0, vedere [aggiungere il supporto per il formato JSON basato su Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Nuove direttive Razor

L'elenco seguente contiene le nuove direttive Razor:

* [@attribute](xref:mvc/views/razor#attribute)&ndash; La`@attribute` direttiva applica l'attributo specificato alla classe della pagina o della visualizzazione generata. Ad esempio `@attribute [Authorize]`.
* [@implements](xref:mvc/views/razor#implements)&ndash; La`@implements` direttiva implementa un'interfaccia per la classe generata. Ad esempio `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 supporta l'autenticazione e l'autorizzazione per le API Web e le Spa

[IdentityServer4](https://identityserver.io) è un Framework di OpenID Connect e OAuth 2,0 per ASP.NET Core 3,0. IdentityServer4 Abilita le funzionalità di sicurezza seguenti:

* Autenticazione come servizio (AaaS)
* Single Sign-on/off (SSO) su più tipi di applicazione
* Controllo di accesso per le API
* Gateway federativo

Per ulteriori informazioni, vedere [Welcome to IdentityServer4](http://docs.identityserver.io/en/latest/index.html).

## <a name="certificate-and-kerberos-authentication"></a>Certificato e autenticazione Kerberos

L'autenticazione del certificato richiede:

* Configurazione del server per l'accettazione dei certificati.
* Aggiunta del middleware di autenticazione `Startup.Configure`in.
* Aggiunta del servizio di autenticazione del `Startup.ConfigureServices`certificato in.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Le opzioni per l'autenticazione dei certificati includono la possibilità di:

* Accettare certificati autofirmati.
* Controllare la revoca del certificato.
* Verificare che nel certificato fornito siano presenti i flag di utilizzo corretti.

Un'entità utente predefinita viene costruita dalle proprietà del certificato. L'entità utente contiene un evento che consente l'aggiunta o la sostituzione dell'entità. Per altre informazioni, vedere <xref:security/authentication/certauth>.

[L'autenticazione di Windows](/windows-server/security/windows-authentication/windows-authentication-overview) è stata estesa in Linux e MacOS. Nelle versioni precedenti, l'autenticazione di Windows era limitata a [IIS](xref:host-and-deploy/iis/index) e a [HttpSys](xref:fundamentals/servers/httpsys). In ASP.NET Core 3,0, [gheppio](xref:fundamentals/servers/kestrel) è in grado di usare Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM in Windows](/windows-server/security/kerberos/ntlm-overview), Linux e MacOS per gli host aggiunti a un dominio Windows. Il supporto di gheppio di questi schemi di autenticazione viene fornito dal pacchetto [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) . Come per gli altri servizi di autenticazione, configurare la larghezza dell'app di autenticazione, quindi configurare il servizio:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Requisiti host:

* Gli host Windows devono disporre di [nomi di entità servizio](/windows/win32/ad/service-principal-names) (SPN) aggiunti all'account utente che ospita l'app.
* I computer Linux e macOS devono essere aggiunti al dominio.
  * È necessario creare nomi SPN per il processo Web.
  * [I file keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) devono essere generati e configurati nel computer host.

Per altre informazioni, vedere <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Modifiche ai modelli

I modelli dell'interfaccia utente Web (Razor Pages, MVC con controller e visualizzazioni) hanno rimosso quanto segue:

* L'interfaccia utente di consenso del cookie non è più inclusa. Per abilitare la funzionalità di consenso dei cookie in un'app generata da un modello <xref:security/gdpr>di ASP.NET Core 3,0, vedere.
* Agli script e agli asset statici correlati viene ora fatto riferimento come file locali invece di usare CDNs. Per altre informazioni, vedere [gli script e gli asset statici correlati a cui viene ora fatto riferimento come file locali invece di usare CDNs in base all'ambiente corrente (ASPNET/AspNetCore. Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).

Modello angolare aggiornato per l'utilizzo di 8 angolare.

Per impostazione predefinita, il modello RCL (Razor Class Library) per impostazione predefinita è lo sviluppo di componenti Razor. Una nuova opzione di modello in Visual Studio fornisce il supporto del modello per le pagine e le visualizzazioni. Quando si crea un RCL dal modello in una shell dei comandi, passare `-support-pages-and-views` l'opzione`dotnet new razorclasslib -support-pages-and-views`().

## <a name="generic-host"></a>Host generico

I modelli di ASP.NET Core 3,0 <xref:fundamentals/host/generic-host>usano. Versioni precedenti utilizzate <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>. L'uso dell'host generico di .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core () offre una migliore integrazione delle app ASP.NET Core con altri scenari server che non sono specifici del Web. Per ulteriori informazioni, vedere [HostBuilder sostituisce WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Configurazione dell'host

Prima del rilascio di ASP.NET Core 3,0, le variabili di ambiente precedute `ASPNETCORE_` da sono state caricate per la configurazione host dell'host Web. In 3,0, `AddEnvironmentVariables` viene usato per caricare le variabili di ambiente con `DOTNET_` prefisso per la configurazione `CreateDefaultBuilder`host con.

### <a name="changes-to-startup-contructor-injection"></a>Modifiche a Startup Costruttore Injection

L'host generico supporta solo i tipi seguenti per `Startup` l'inserimento del costruttore:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Tutti i servizi possono comunque essere inseriti direttamente come argomenti per il `Startup.Configure` metodo. Per ulteriori informazioni, vedere l' [host generico limita l'inserimento del costruttore di avvio (ASPNET/annunci #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* La configurazione di Gheppio è stata aggiornata per la migrazione all'host generico. In 3,0, Gheppio viene configurato nel generatore host Web fornito da `ConfigureWebHostDefaults`.
* Le schede di connessione sono state rimosse da gheppio e sostituite con il middleware di connessione, che è simile al middleware HTTP nella pipeline ASP.NET Core ma per le connessioni di livello inferiore.
* Il livello trasporto gheppio è stato esposto come interfaccia pubblica in `Connections.Abstractions`.
* L'ambiguità tra intestazioni e trailer è stata risolta spostando le intestazioni finali in una nuova raccolta.
* Le API di i/o `HttpRequest.Body.Read`sincrone, ad esempio, sono un'origine comune di inedia dei thread che causa arresti anomali dell'app. In 3,0 `AllowSynchronousIO` è disabilitato per impostazione predefinita.

Per altre informazioni, vedere <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 abilitato per impostazione predefinita

HTTP/2 è abilitato per impostazione predefinita in gheppio per gli endpoint HTTPS. Il supporto HTTP/2 per IIS o HTTP. sys è abilitato quando supportato dal sistema operativo.

## <a name="eventcounters-on-request"></a>EventCounters su richiesta

Il EventSource di hosting `Microsoft.AspNetCore.Hosting`,, emette i nuovi <xref:System.Diagnostics.Tracing.EventCounter> tipi seguenti correlati alle richieste in ingresso:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Routing degli endpoint

Il routing degli endpoint, che consente ai Framework, ad esempio MVC, di funzionare correttamente con il middleware, viene migliorato:

* L'ordine di middleware ed endpoint è configurabile nella pipeline di elaborazione delle richieste `Startup.Configure`di.
* Gli endpoint e il middleware si compongono bene con altre tecnologie basate su ASP.NET Core, ad esempio i controlli di integrità.
* Gli endpoint possono implementare un criterio, ad esempio CORS o Authorization, sia in middleware che in MVC.
* I filtri e gli attributi possono essere inseriti nei metodi dei controller.

Per altre informazioni, vedere <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Controlli di integrità

I controlli di integrità usano il routing degli endpoint con l'host generico. In `Startup.Configure`, chiamare `MapHealthChecks` sul generatore di endpoint con l'URL dell'endpoint o il percorso relativo:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Gli endpoint di controllo integrità possono:

* Specificare uno o più host o porte consentiti.
* Richiedere l'autorizzazione.
* Richiede CORS.

Per altre informazioni, vedere i seguenti articoli:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Pipe in HttpContext

È ora possibile leggere il corpo della richiesta e scrivere il corpo della risposta usando l' <xref:System.IO.Pipelines> API. Alla classe <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`Proprietà che fornisce <xref:System.IO.Pipelines.PipeReader> un oggetto che può essere utilizzato per leggere il corpo della richiesta. Alla classe <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`Proprietà che fornisce <xref:System.IO.Pipelines.PipeWriter> un oggetto che può essere utilizzato per scrivere il corpo della risposta. `HttpRequest.BodyReader`è un analogo del `HttpRequest.Body` flusso. `HttpResponse.BodyWriter`è un analogo del `HttpResponse.Body` flusso.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Segnalazione degli errori migliorata in IIS

Gli errori di avvio quando si ospitano app ASP.NET Core in IIS generano ora dati di diagnostica più completi. Questi errori vengono segnalati al registro eventi di Windows con le tracce dello stack laddove applicabile. Inoltre, tutti gli avvisi, gli errori e le eccezioni non gestite vengono registrati nel registro eventi di Windows.

## <a name="worker-service-and-worker-sdk"></a>Worker Service e worker SDK

.NET Core 3,0 introduce il nuovo modello di app del servizio Worker. Questo modello offre un punto di partenza per la scrittura di servizi con esecuzione prolungata in .NET Core.

Per altre informazioni, vedere:

* [Ruoli di lavoro di .NET Core come servizi Windows](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Miglioramenti del middleware per intestazioni con inoltri

Nelle versioni precedenti di ASP.NET Core, la <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> chiamata <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> di e risultava problematica quando veniva distribuita in un Linux di Azure o dietro qualsiasi proxy inverso diverso da IIS. La correzione per le versioni precedenti è documentata in [inoltro dello schema per i proxy inversi di Linux e non IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Questo scenario è stato risolto in ASP.NET Core 3,0. L'host Abilita il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando la `ASPNETCORE_FORWARDEDHEADERS_ENABLED` variabile di ambiente `true`è impostata su. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`viene impostato su `true` nelle immagini del contenitore.

## <a name="performance-improvements"></a>Miglioramenti delle prestazioni

ASP.NET Core 3,0 include numerosi miglioramenti che consentono di ridurre l'utilizzo della memoria e migliorare la velocità effettiva:

* Riduzione dell'utilizzo della memoria quando si utilizza il contenitore incorporato di inserimento delle dipendenze per i servizi con ambito.
* Riduzione delle allocazioni nel Framework, inclusi gli scenari middleware e il routing.
* Riduzione dell'utilizzo della memoria per le connessioni WebSocket.
* Miglioramenti della velocità effettiva e della riduzione della memoria per le connessioni HTTPS.
* Nuovo serializzatore JSON ottimizzato e completamente asincrono.
* Riduzione dell'utilizzo della memoria e miglioramento della velocità effettiva nell'analisi dei moduli.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3,0 viene eseguito solo in .NET Core 3,0

A partire da ASP.NET Core 3,0, .NET Framework non è più un Framework di destinazione supportato. I progetti destinati a .NET Framework possono continuare in modo completamente supportato con la [versione di .NET Core 2,1 LTS](https://www.microsoft.com/net/download/dotnet-core/2.1). La maggior parte dei pacchetti di ASP.NET Core 2.1. x saranno supportati per un tempo illimitato, oltre il periodo di 3 anni LTS per .NET Core 2,1.

Per informazioni sulla migrazione, vedere [trasferire il codice da .NET Framework a .NET Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Usare il Framework condiviso ASP.NET Core

Il Framework condiviso ASP.NET Core 3,0, contenuto nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), non richiede più un elemento esplicito `<PackageReference />` nel file di progetto. Al Framework condiviso viene fatto automaticamente riferimento quando si usa `Microsoft.NET.Sdk.Web` l'SDK nel file di progetto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Assembly rimossi dal Framework condiviso ASP.NET Core

Gli assembly più rilevanti rimossi dal Framework condiviso ASP.NET Core 3,0 sono:

* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.NET). Per aggiungere Json.NET a ASP.NET Core 3,0, vedere [aggiungere il supporto per il formato JSON basato su Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3,0 introduce `System.Text.Json` per la lettura e la scrittura di JSON. Per ulteriori informazioni, vedere la pagina relativa alla [nuova serializzazione JSON](#new-json-serialization) in questo documento.
* [Entity Framework Core](/ef/core/)

Per un elenco completo degli assembly rimossi dal Framework condiviso, vedere [assembly rimossi da Microsoft. AspNetCore. App 3,0](https://github.com/aspnet/AspNetCore/issues/3755). Per altre informazioni sulla motivazione di questa modifica, vedere [modifiche di rilievo a Microsoft. AspNetCore. app in 3,0](https://github.com/aspnet/Announcements/issues/325) e [una prima occhiata alle modifiche apportate in ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->

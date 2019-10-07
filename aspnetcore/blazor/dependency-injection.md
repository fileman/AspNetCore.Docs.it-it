---
title: Inserimento delle dipendenze di ASP.NET Core Blazor
author: guardrex
description: Scopri in che modo le app Blazor possono inserire i servizi nei componenti.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/dependency-injection
ms.openlocfilehash: 00c874e43496eaad8841db91843f41a299813aec
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207174"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>Inserimento delle dipendenze di ASP.NET Core Blazor

Di [Rainer Stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor supporta l' [inserimento delle dipendenze (di)](xref:fundamentals/dependency-injection). Le app possono usare i servizi predefiniti inserendoli in componenti. Le app possono anche definire e registrare servizi personalizzati e renderli disponibili nell'app tramite DI.

DI è una tecnica per accedere ai servizi configurati in una posizione centrale. Questa operazione può essere utile nelle app Blazor per:

* Condividere una singola istanza di una classe di servizio in molti componenti, noti come un servizio *singleton* .
* Separare i componenti da classi di servizi concrete usando astrazioni di riferimento. Si consideri ad esempio `IDataAccess` un'interfaccia per l'accesso ai dati nell'app. L'interfaccia viene implementata da una `DataAccess` classe concreta e registrata come servizio nel contenitore del servizio dell'app. Quando un componente usa il per ricevere un' `IDataAccess` implementazione di, il componente non è associato al tipo concreto. L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.

## <a name="default-services"></a>Servizi predefiniti

I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.

| Service | Durata | Descrizione |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI. Si noti che questa istanza `HttpClient` di usa il browser per gestire il traffico HTTP in background. [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) viene impostato automaticamente sul prefisso URI di base dell'app. Per altre informazioni, vedere <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton | Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate le chiamate a JavaScript. Per altre informazioni, vedere <xref:blazor/javascript-interop>. |
| `NavigationManager` | Singleton | Contiene gli helper per lavorare con gli URI e lo stato di navigazione. Per ulteriori informazioni, vedere [URI e Helper dello stato di navigazione](xref:blazor/routing#uri-and-navigation-state-helpers). |

Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella. Se si usa un provider di servizi personalizzato e si richiede uno dei servizi indicati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.

## <a name="add-services-to-an-app"></a>Aggiungere servizi a un'app

Dopo aver creato una nuova app, esaminare `Startup.ConfigureServices` il metodo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Al `ConfigureServices` metodo viene passato un <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>oggetto, che è un elenco di oggetti del descrittore del servizio (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). I servizi vengono aggiunti fornendo descrittori del servizio alla raccolta di servizi. Nell'esempio seguente viene illustrato il concetto con `IDataAccess` l'interfaccia e la relativa `DataAccess`implementazione concreta:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

I servizi possono essere configurati con le durate mostrate nella tabella seguente.

| Durata | Descrizione |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Le app webassembly Blazor attualmente non dispongono di un concetto di ambiti di. `Scoped`-i servizi registrati si `Singleton` comportano come servizi. Tuttavia, il modello di hosting del server Blazor `Scoped` supporta il ciclo di vita. Nelle app del server Blazor, una registrazione del servizio con ambito ha come ambito la *connessione*. Per questo motivo, è preferibile usare i servizi con ambito per i servizi che devono avere come ambito l'utente corrente, anche se l'obiettivo corrente è eseguire sul lato client nel browser. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | La creazione di una *singola istanza* del servizio. Tutti i componenti che richiedono `Singleton` un servizio ricevono un'istanza dello stesso servizio. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Ogni volta che un componente ottiene un'istanza di `Transient` un servizio dal contenitore del servizio, riceve una *nuova istanza* del servizio. |

Il sistema DI è basato sul sistema DI ASP.NET Core. Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Richiedere un servizio in un componente

Una volta aggiunti i servizi alla raccolta di servizi, inserire i servizi nei componenti usando la [ \@direttiva Inject](xref:mvc/views/razor#inject) Razor. `@inject`dispone di due parametri:

* Digitare &ndash; il tipo di servizio da inserire.
* Proprietà &ndash; nome della proprietà che riceve il servizio app inserito. La proprietà non richiede la creazione manuale. Il compilatore crea la proprietà.

Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.

Usare più `@inject` istruzioni per inserire servizi diversi.

Nell'esempio riportato di seguito viene illustrato come usare `@inject`. Il servizio che `Services.IDataAccess` implementa viene inserito nella proprietà `DataRepository`del componente. Si noti come il codice usa solo l' `IDataAccess` astrazione:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Internamente, la proprietà generata`DataRepository`() è decorata `InjectAttribute` con l'attributo. In genere, questo attributo non viene utilizzato direttamente. Se è necessaria una classe base per i componenti e le proprietà inserite sono necessarie anche per la classe base, aggiungere `InjectAttribute`manualmente:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Nei componenti derivati dalla classe di base `@inject` , la direttiva non è obbligatoria. La `InjectAttribute` classe della classe base è sufficiente:

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Usare l'inserimento DI dipendenze nei servizi

Servizi complessi potrebbe richiedere servizi aggiuntivi. Nell'esempio precedente, `DataAccess` potrebbe richiedere il `HttpClient` servizio predefinito. `@inject`(o) `InjectAttribute`non è disponibile per l'uso nei servizi. È necessario usare invece l' *inserimento del costruttore* . I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio. Quando si crea il servizio, vengono riconosciuti i servizi richiesti nel costruttore e forniti DI conseguenza.

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Prerequisiti per l'inserimento del costruttore:

* È necessario che esista un costruttore i cui argomenti possono essere tutti soddisfatti da DI. Sono consentiti parametri aggiuntivi non analizzati da DI se specificano i valori predefiniti.
* Il costruttore applicabile deve essere *pubblico*.
* È necessario che esista un costruttore applicabile. In caso di ambiguità, viene generata un'eccezione.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classi di componenti di base dell'utilità per gestire un ambito DI

Nelle app ASP.NET Core, i servizi con ambito hanno in genere come ambito la richiesta corrente. Al termine della richiesta, tutti i servizi con ambito o temporaneo vengono eliminati dal sistema DI. Nelle app del server Blazor, l'ambito della richiesta dura per la durata della connessione client, che può comportare un tempo di permanenza dei servizi temporanei e con ambito più lungo del previsto.

Per definire l'ambito dei servizi per la durata di un componente, `OwningComponentBase` può `OwningComponentBase<TService>` usare le classi di base e. Queste classi di base espongono una `ScopedServices` proprietà di tipo `IServiceProvider` che risolve i servizi che hanno come ambito la durata del componente. Per creare un componente che eredita da una classe di base in Razor, usare `@inherits` la direttiva.

```cshtml
@page "/users"
@attribute [Authorize]
@inherits OwningComponentBase<Data.ApplicationDbContext>

<h1>Users (@Service.Users.Count())</h1>
<ul>
    @foreach (var user in Service.Users)
    {
        <li>@user.UserName</li>
    }
</ul>
```

> [!NOTE]
> I servizi inseriti nel componente usando `@inject` `InjectAttribute` o non vengono creati nell'ambito del componente e sono associati all'ambito della richiesta.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>

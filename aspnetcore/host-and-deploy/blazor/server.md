---
title: Ospitare e distribuire ASP.NET Core Server Blazor
author: guardrex
description: Informazioni su come ospitare e distribuire un'app Server Blazor usando ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: aedef7fe695dd4a0cbf04d3f3e9947f33f7afa40
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211614"
---
# <a name="host-and-deploy-blazor-server"></a>Ospitare e distribuire il server Blazor

Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valori di configurazione dell'host

Le [app del server Blazor](xref:blazor/hosting-models#blazor-server) possono accettare [valori di configurazione host generici](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Distribuzione

Con il [modello di hosting del server Blazor](xref:blazor/hosting-models#blazor-server), Blazor viene eseguito sul server dall'interno di un'app ASP.NET Core. Gli aggiornamenti dell'interfaccia utente, la gestione degli eventi e le chiamate JavaScript vengono gestite tramite una connessione [SignalR](xref:signalr/introduction).

È necessario un server Web in grado di ospitare un'app ASP.NET Core. Visual Studio include il modello di progetto **App server Blazor** (modello `blazorserverside` quando si usa il comando [dotnet new](/dotnet/core/tools/dotnet-new)).

## <a name="scalability"></a>Scalabilità

Pianificare una distribuzione per sfruttare al meglio l'infrastruttura disponibile per un'app del server Blazor. Vedere le risorse seguenti per risolvere la scalabilità delle app del server Blazor:

* [Nozioni fondamentali sulle app del server Blazor](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Server di distribuzione

Quando si considera la scalabilità di un singolo server (scalabilità verticale), la memoria disponibile per un'app è probabilmente la prima risorsa che verrà esaurita dall'app quando le richieste degli utenti aumentano. La memoria disponibile sul server influiscono su:

* Numero di circuiti attivi che un server è in grado di supportare.
* Latenza dell'interfaccia utente nel client.

Per istruzioni sulla creazione di app Server Blazor sicure e scalabili <xref:security/blazor/server>, vedere.

Ogni circuito utilizza circa 250 KB di memoria per un'app di tipo *Hello World*minima. La dimensione di un circuito dipende dal codice dell'app e dai requisiti di manutenzione dello stato associati a ogni componente. Si consiglia di misurare le richieste di risorse durante lo sviluppo dell'applicazione e dell'infrastruttura, ma la linea di base seguente può essere un punto di partenza per la pianificazione della destinazione di distribuzione: Se si prevede che l'app supporti 5.000 utenti simultanei, valutare la possibilità di stabilire un budget di almeno 1,3 GB di memoria del server per l'app (o ~ 273 KB per utente).

### <a name="signalr-configuration"></a>Configurazione di SignalR

Le app del server Blazor usano ASP.NET Core SignalR per comunicare con il browser. [Le condizioni di hosting e scalabilità di SignalR](xref:signalr/publish-to-azure-web-app) si applicano alle app del server Blazor.

Blazor funziona meglio quando si usano WebSocket come trasporto SignalR grazie a latenza, affidabilità e [sicurezza](xref:signalr/security)inferiori. Il polling prolungato viene usato da SignalR quando WebSocket non è disponibile o quando l'app è configurata in modo esplicito per l'uso del polling prolungato. Quando si esegue la distribuzione nel servizio app Azure, configurare l'app per l'uso di WebSocket nelle impostazioni portale di Azure per il servizio. Per informazioni dettagliate sulla configurazione dell'app per il servizio app Azure, vedere le [linee guida sulla pubblicazione di SignalR](xref:signalr/publish-to-azure-web-app).

È consigliabile usare il [servizio Azure SignalR](/azure/azure-signalr) per le app del server Blazor. Il servizio consente la scalabilità verticale di un'app del server Blazor a un numero elevato di connessioni di SignalR simultanee. Inoltre, la portata globale del servizio SignalR e i Data Center a prestazioni elevate consentono di ridurre significativamente la latenza a causa della geografia.

### <a name="measure-network-latency"></a>Misurare la latenza di rete

L' [interoperabilità JS](xref:blazor/javascript-interop) può essere usata per misurare la latenza di rete, come illustrato nell'esempio seguente:

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

Per un'esperienza dell'interfaccia utente ragionevole, è consigliabile una latenza di interfaccia utente prolungata di 250ms o meno.

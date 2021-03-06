---
title: Hosting e scalabilità dell'ambiente di produzione di ASP.NET Core SignalR
author: bradygaster
description: Informazioni su come evitare problemi di prestazioni e scalabilità nelle app che usano ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
ms.openlocfilehash: 26b02cffdd472fc21dc4aee7052a0ba939b82c0f
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211744"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>Hosting e scalabilità di ASP.NET Core SignalR

Di [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),

Questo articolo illustra le considerazioni sull'hosting e la scalabilità per le app con traffico elevato che usano ASP.NET Core SignalR.

## <a name="sticky-sessions"></a>Sessioni permanenti

SignalR richiede che tutte le richieste HTTP per una connessione specifica siano gestite dallo stesso processo server. Quando SignalR viene eseguito in un server farm (più server), è necessario usare "sessioni permanenti". Le "sessioni permanenti" sono definite anche affinità di sessione da parte di alcuni bilanciamenti del carico. Il servizio app Azure usa [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) per indirizzare le richieste. L'abilitazione dell'impostazione "affinità ARR" nel servizio app Azure consentirà di "sessioni permanenti". Le uniche circostanze in cui non sono richieste sessioni permanenti sono:

1. Quando si esegue l'hosting in un singolo server, in un singolo processo.
1. Quando si usa il servizio Azure SignalR.
1. Quando tutti i client sono configurati in modo da usare **solo** WebSocket **e** l' [impostazione SkipNegotiation](xref:signalr/configuration#configure-additional-options) è abilitata nella configurazione client.

In tutti gli altri casi (incluso quando viene usato il backplane Redis), è necessario configurare l'ambiente server per le sessioni permanenti.

Per informazioni sulla configurazione del servizio app Azure per SignalR, <xref:signalr/publish-to-azure-web-app>vedere.

## <a name="tcp-connection-resources"></a>Risorse di connessione TCP

Il numero di connessioni TCP simultanee che un server Web è in grado di supportare è limitato. I client HTTP standard *utilizzano connessioni* temporanee. Queste connessioni possono essere chiuse quando il client diventa inattivo e riaperto in un secondo momento. D'altra parte, una connessione SignalR è *persistente*. Le connessioni SignalR rimaneno aperte anche quando il client diventa inattivo. In un'app a traffico elevato che serve molti client, queste connessioni permanenti possono provocare il raggiungimento del numero massimo di connessioni da parte dei server.

Le connessioni permanenti utilizzano anche una quantità di memoria aggiuntiva per tenere traccia di ogni connessione.

L'uso intensivo di risorse correlate alla connessione da SignalR può influire su altre app Web ospitate nello stesso server. Quando SignalR si apre e contiene le ultime connessioni TCP disponibili, anche altre app Web nello stesso server non dispongono di altre connessioni disponibili.

Se un server esaurisce le connessioni, verranno visualizzati gli errori di socket casuali e gli errori di reimpostazione della connessione. Ad esempio:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Per evitare che l'utilizzo delle risorse SignalR causi errori in altre app Web, eseguire SignalR su server diversi rispetto alle altre app Web.

Per evitare che l'utilizzo delle risorse SignalR causi errori in un'app SignalR, aumentare il numero di connessioni per limitare il numero di connessioni che un server deve gestire.

## <a name="scale-out"></a>Scalare in orizzontale

Un'app che usa SignalR deve tenere traccia di tutte le connessioni, che creano problemi per un server farm. Aggiungere un server e ottenere nuove connessioni che gli altri server non conoscono. Ad esempio, SignalR in ogni server del diagramma seguente non è a conoscenza delle connessioni sugli altri server. Quando SignalR su uno dei server vuole inviare un messaggio a tutti i client, il messaggio passa solo ai client connessi a tale server.

![Scalabilità di SignalR senza backplane](scale/_static/scale-no-backplane.png)

Le opzioni per la risoluzione di questo problema sono il [servizio Azure SignalR](#azure-signalr-service) e il [backplane Redis](#redis-backplane).

## <a name="azure-signalr-service"></a>Servizio Azure SignalR

Il servizio Azure SignalR è un proxy anziché un backplane. Ogni volta che un client avvia una connessione al server, il client viene reindirizzato per connettersi al servizio. Questo processo è illustrato nel diagramma seguente:

![Stabilire una connessione al servizio Azure SignalR](scale/_static/azure-signalr-service-one-connection.png)

Il risultato è che il servizio gestisce tutte le connessioni client, mentre per ogni server è necessario solo un piccolo numero costante di connessioni al servizio, come illustrato nel diagramma seguente:

![Client connessi al servizio, server connessi al servizio](scale/_static/azure-signalr-service-multiple-connections.png)

Questo approccio per la scalabilità orizzontale presenta diversi vantaggi rispetto all'alternativa del backplane redis:

* Le sessioni permanenti, note anche come [affinità client](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), non sono necessarie, perché i client vengono immediatamente reindirizzati al servizio Azure SignalR quando si connettono.
* Un'app SignalR può eseguire la scalabilità orizzontale in base al numero di messaggi inviati, mentre il servizio Azure SignalR viene ridimensionato automaticamente per gestire un numero qualsiasi di connessioni. Ad esempio, potrebbero essere presenti migliaia di client, ma se vengono inviati solo pochi messaggi al secondo, l'app SignalR non dovrà eseguire la scalabilità orizzontale su più server solo per gestire le connessioni stesse.
* Un'app SignalR non userà significativamente più risorse di connessione rispetto a un'app Web senza SignalR.

Per questi motivi, è consigliabile usare il servizio Azure SignalR per tutte le app ASP.NET Core SignalR ospitate in Azure, tra cui il servizio app, le macchine virtuali e i contenitori.

Per altre informazioni, vedere la [documentazione del servizio Azure SignalR](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Backplane Redis

[Redis](https://redis.io/) è un archivio chiave-valore in memoria che supporta un sistema di messaggistica con un modello di pubblicazione/sottoscrizione. Il backplane di redis di SignalR usa la funzionalità pub/sub per inviare messaggi ad altri server. Quando un client effettua una connessione, le informazioni di connessione vengono passate al backplane. Quando un server vuole inviare un messaggio a tutti i client, lo invia al backplane. Il backplane conosce tutti i client connessi e i server in cui si trovano. Invia il messaggio a tutti i client tramite i rispettivi server. Questo processo è illustrato nel diagramma seguente:

![Backplane Redis, messaggio inviato da un server a tutti i client](scale/_static/redis-backplane.png)

Il backplane di redis è l'approccio di scalabilità orizzontale consigliato per le app ospitate nella propria infrastruttura. Il servizio Azure SignalR non è un'opzione pratica per l'uso in ambiente di produzione con le app locali a causa della latenza di connessione tra il data center e un data center di Azure.

I vantaggi del servizio Azure SignalR indicati in precedenza sono gli svantaggi per il backplane di redis:

* Le sessioni permanenti, note anche come [affinità client](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sono obbligatorie. Una volta avviata una connessione in un server, la connessione deve rimanere nel server.
* Un'app SignalR deve essere scalata in base al numero di client anche se sono stati inviati pochi messaggi.
* Un'app SignalR USA significativamente più risorse di connessione rispetto a un'app Web senza SignalR.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Documentazione del servizio Azure SignalR](/azure/azure-signalr/signalr-overview)
* [Configurare un backplane Redis](xref:signalr/redis-backplane)

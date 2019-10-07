---
title: Debug ASP.NET Core Blazor
author: guardrex
description: Informazioni su come eseguire il debug di app blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/debug
ms.openlocfilehash: 3519479d8058f013de23cc9cfa0f5574cd158053
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207204"
---
# <a name="debug-aspnet-core-blazor"></a>Debug ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Il supporto *anticipato* è disponibile per il debug di app webassembly in esecuzione su webassembly in Chrome.

Le funzionalità del debugger sono limitate. Gli scenari disponibili includono:

* Impostare e rimuovere punti di interruzione.
* Singolo passaggio (`F10`) tramite il codice o l'esecuzione del`F8`codice Resume ().
* Nella visualizzazione variabili *locali* osservare i valori delle variabili locali di tipo `int`, `string`e `bool`.
* Vedere lo stack di chiamate, incluse le catene di chiamate che passano da JavaScript a .NET e da .NET a JavaScript.

*Non è possibile*:

* Osservare i valori delle variabili locali che non sono `int`, `string`o `bool`.
* Osservare i valori di qualsiasi proprietà o campo di una classe.
* Passare il mouse sulle variabili per visualizzarne i valori.
* Valutare le espressioni nella console.
* Eseguire un passaggio tra le chiamate asincrone.
* Eseguire la maggior parte degli altri scenari di debug comuni.

Lo sviluppo di altri scenari di debug è un'attività in corso del team di progettazione.

## <a name="prerequisites"></a>Prerequisiti

Il debug richiede uno dei seguenti browser:

* Google Chrome (versione 70 o successiva)
* Anteprima di Microsoft Edge ([canale di sviluppo Edge](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>Routine

1. Eseguire un'app webassembly Blazor in `Debug` Configuration. Passare l' `--configuration Debug` opzione al comando [DotNet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.
1. Accedere all'app nel browser.
1. Posizionare lo stato attivo della tastiera sull'app, non sul pannello strumenti di sviluppo. Il pannello strumenti di sviluppo può essere chiuso al momento dell'avvio del debug.
1. Selezionare il seguente tasto di scelta rapida specifico di Blazor:
   * `Shift+Alt+D`in Windows/Linux
   * `Shift+Cmd+D`in macOS
1. Seguire i passaggi elencati sullo schermo per riavviare il browser con il debug remoto abilitato.
1. Per avviare la sessione di debug, selezionare di nuovo il seguente tasto di scelta rapida specifico di Blazor:
   * `Shift+Alt+D`in Windows/Linux
   * `Shift+Cmd+D`in macOS

## <a name="enable-remote-debugging"></a>Abilita debug remoto

Se il debug remoto è disabilitato, la pagina di errore **di una scheda del browser di cui è possibile eseguire il debug** viene generata da Chrome. La pagina di errore contiene le istruzioni per eseguire Chrome con la porta di debug aperta, in modo che il proxy di debug di Blazor possa connettersi all'app. *Chiudere tutte le istanze di Chrome* e riavviare Chrome come indicato.

## <a name="debug-the-app"></a>Eseguire il debug dell'app

Quando Chrome è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida per il debug apre una nuova scheda del debugger. Dopo qualche istante la scheda **Sources (origini** ) Mostra un elenco degli assembly .NET nell'app. Espandere ogni assembly e trovare i file di origine *. cs*/ *. Razor* disponibili per il debug. Impostare i punti di interruzione, tornare alla scheda dell'app e i punti di interruzione vengono raggiunti quando viene eseguito il codice. Quando viene raggiunto un punto di interruzione, l'esecuzione`F10`del codice a un solo passaggio (`F8`) tramite il codice o riprende () normalmente.

Blazor fornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET. Quando si preme il tasto di scelta rapida per il debug, Blazor punta il DevTools di Chrome sul proxy. Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.

## <a name="browser-source-maps"></a>Mappe di origine del browser

Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client. Tuttavia, Blazor non esegue attualmente C# il mapping direttamente a JavaScript/WASM. Al contrario, Blazor esegue l'interpretazione IL nel browser, quindi le mappe di origine non sono rilevanti.

## <a name="troubleshooting-tip"></a>Suggerimento per la risoluzione dei problemi

Se si verificano errori, è possibile che venga utile il suggerimento seguente:

Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser. Nella console eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.

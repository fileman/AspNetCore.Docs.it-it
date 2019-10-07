---
title: Hosting e distribuzione di ASP.NET Core Blazor
author: guardrex
description: Informazioni su come ospitare e distribuire app Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 1cfe87c7194b34c2461429225c560f9e689168ae
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211694"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hosting e distribuzione di ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Pubblicare l'app

Le app vengono pubblicate per la distribuzione nella configurazione per il rilascio.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selezionare **Compila** > **Pubblica {APPLICAZIONE}** sulla barra di spostamento.
1. Selezionare la *destinazione di pubblicazione*. Per pubblicare in locale, selezionare **Cartella**.
1. Accettare il percorso predefinito nel campo **Scegliere una cartella** oppure specificarne uno diverso. Selezionare il pulsante **Pubblica**.

# <a name="net-core-clitabnetcore-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Usare il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) per pubblicare l'app con una configurazione per il rilascio:

```dotnetcli
dotnet publish -c Release
```

---

La pubblicazione dell'app attiva un [ripristino](/dotnet/core/tools/dotnet-restore) delle dipendenze del progetto e [compila](/dotnet/core/tools/dotnet-build) il progetto prima di creare gli asset per la distribuzione. Come parte del processo di compilazione, vengono rimossi gli assembly e i metodi non usati per ridurre le dimensioni del download e i tempi di caricamento dell'app.

Un'app webassembly Blazor viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/{assembly nome}/dist* . Viene pubblicata un'app del server Blazor nella cartella */bin/Release/{Target Framework}/Publish*

Gli asset nella cartella vengono distribuiti nel server Web. La distribuzione potrebbe essere un processo manuale o automatizzato a seconda degli strumenti di sviluppo in uso.

## <a name="app-base-path"></a>Percorso di base dell'app

Il *percorso di base dell'app* è il percorso dell'URL radice dell'app. Si consideri l'app principale e l'app Blazor seguenti:

* Viene chiamata `MyApp`l'app principale:
  * L'app risiede fisicamente in *\\d: MyApp*.
  * Le richieste vengono ricevute all'indirizzo `https://www.contoso.com/{MYAPP RESOURCE}`.
* Un'app Blazor chiamata `CoolApp` è una sottoapp di: `MyApp`
  * La Sub-App risiede fisicamente in *d:\\MyApp\\CoolApp*.
  * Le richieste vengono ricevute all'indirizzo `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Se non si specifica una `CoolApp`configurazione aggiuntiva per, la sottoapp in questo scenario non è a conoscenza della posizione in cui risiede nel server. Ad esempio, l'app non può costruire URL relativi corretti per le risorse senza sapere che risiede nel percorso `/CoolApp/`URL relativo.

Per fornire la configurazione per il percorso di base `https://www.contoso.com/CoolApp/`dell'app Blazor di `<base>` , l' `href` attributo del tag viene impostato sul percorso radice relativo nel file *wwwroot/index.html* :

```html
<base href="/CoolApp/">
```

Fornendo il percorso URL relativo, un componente che non si trova nella directory radice può costruire URL relativi al percorso radice dell'app. I componenti a livelli diversi della struttura di directory possono creare collegamenti ad altre risorse in posizioni all'interno dell'app. Il percorso di base dell'app viene anche usato per intercettare i clic su collegamenti ipertestuali in cui la destinazione del collegamento `href` si trova all'interno dello spazio URI del percorso di base dell'app. Il router Blazor gestisce la navigazione interna.

In molti scenari di hosting il percorso URL relativo dell'app è la radice dell'app. In questi casi, il percorso di base dell'URL relativo dell'app è una barra`<base href="/" />`(), ovvero la configurazione predefinita per un'app Blazor. In altri scenari di hosting, ad esempio pagine di GitHub e sottoapp di IIS, il percorso di base dell'app deve essere impostato sul percorso URL relativo del server per l'app.

Per impostare il percorso di base dell'app, aggiornare il tag `<base>` all'interno degli elementi del tag `<head>` del file*wwwroot/index.html*. Impostare il `href` valore dell'attributo `/{RELATIVE URL PATH}/` su (la barra finale è obbligatoria), dove `{RELATIVE URL PATH}` è il percorso URL relativo completo dell'app.

Per un'app con un percorso URL relativo non radice (ad esempio, `<base href="/CoolApp/">`), l'app non riesce a trovare le proprie risorse quando viene *eseguita localmente*. Per risolvere questo problema durante sviluppo e test locali, è possibile specificare un argomento *base del percorso* corrispondente al valore `href` del tag `<base>` in fase di esecuzione. Per passare l'argomento di base del percorso quando si esegue l'app in `dotnet run` locale, eseguire il comando dalla directory dell' `--pathbase` app con l'opzione:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Per un'app con un percorso `/CoolApp/` URL relativo (`<base href="/CoolApp/">`), il comando è:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

L'app risponde in locale all'indirizzo `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Distribuzione

Per indicazioni sulla distribuzione, vedere gli argomenti seguenti:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>

---
title: Introduzione a ASP.NET Core Blazer
author: guardrex
description: Inizia a usare Blazer compilando un'app Blazer con gli strumenti che preferisci.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/get-started
ms.openlocfilehash: 1358a2e92af9d9104e565718692b1ca1940b9d9e
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993397"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introduzione a ASP.NET Core Blazer

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Introduzione a Blazer:

1. Installare la versione più recente di [.NET Core 3,0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .

1. Installare i modelli Blazer eseguendo il comando seguente in una shell dei comandi:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview8.19405.7
   ```

1. Seguire le istruzioni per la scelta degli strumenti:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Installare la versione più recente di [Visual Studio Preview](https://visualstudio.com/vs/preview) con il carico di lavoro **sviluppo di ASP.NET e Web** .

   2 \. Creare un nuovo progetto.

   3 \. Selezionare **app Blazer**. Selezionare **Avanti**.

   4 \. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Create**.

   5 \. Per un'esperienza del lato client blazer, scegliere il modello **app Webassembly Blazer** . Per un'esperienza sul lato server di Blazer, scegliere il modello di **app del server Blazer** . Selezionare **Create**. Per informazioni sui due modelli di hosting blazer, lato server e lato client, vedere <xref:blazor/hosting-models>.

   6 \. Premere **F5** per eseguire l'app.

   > [!NOTE]
   > Se è stata installata l'estensione di Visual Studio blazer per una versione di anteprima precedente di ASP.NET Core blazer (anteprima 6 o precedente), è possibile disinstallare l'estensione. L'installazione dei modelli di Blazer in una shell dei comandi è ora sufficiente per esporre i modelli in Visual Studio.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Installare [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Installare la versione più recente [ C# di per l'estensione Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3 \. Per un'esperienza del lato client di Blazer, eseguire il comando seguente in una shell dei comandi:

      ```console
      dotnet new blazorwasm -o WebApplication1
      ```

      Per un'esperienza sul lato server di Blaze, eseguire il comando seguente in una shell dei comandi:

      ```console
      dotnet new blazorserver -o WebApplication1
      ```

      Per informazioni sui due modelli di hosting blazer, lato server e lato client, vedere <xref:blazor/hosting-models>.

   4 \. Aprire la cartella *WebApplication1* in Visual Studio Code.

   5 \. Per un progetto Blazer sul lato server, l'IDE richiede l'aggiunta di asset per compilare ed eseguire il debug del progetto. Selezionare **Sì**.

   6 \. Se si usa un'app del lato server blazer, eseguire l'app usando il debugger Visual Studio Code. Se si usa un'app del lato client blazer, `dotnet run` eseguire dalla cartella del progetto dell'app.

   7 \. In un browser passare a `https://localhost:5001`.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **Blazor Server App** template. For a Blazor client-side experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

   Per un'esperienza del lato client di Blazer, eseguire i comandi seguenti in una shell dei comandi:

   ```console
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per un'esperienza sul lato server di Blazer, eseguire i comandi seguenti in una shell dei comandi:

   ```console
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per informazioni sui due modelli di hosting blazer, lato server e lato client, vedere <xref:blazor/hosting-models>.

   In un browser passare a `https://localhost:5001`.

   ---

Nelle schede della barra laterale sono disponibili più pagine:

* Home
* Counter
* Recuperare i dati

Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina. Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript, ma i componenti Razor offrono C#un approccio migliore con.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Una richiesta `/counter` di nel browser, come specificato `@page` dalla direttiva nella parte superiore, determina il rendering del `Counter` contenuto da parte del componente. I componenti eseguono il rendering in una rappresentazione in memoria della struttura di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.

Ogni volta che viene selezionato il pulsante **Click me** :

* L' `onclick` evento viene generato.
* Viene chiamato il metodo `IncrementCount` .
* `currentCount` Viene incrementato.
* Il rendering del componente viene eseguito nuovamente.

Il runtime confronta il nuovo contenuto con il contenuto precedente e applica solo il contenuto modificato al Document Object Model (DOM).

Aggiungere un componente a un altro componente usando la sintassi HTML. Ad esempio, aggiungere il `Counter` componente alla Home page dell'app aggiungendo un `<Counter />` elemento al `Index` componente.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Eseguire l'app. La Home page presenta il proprio contatore fornito dal `Counter` componente.

I parametri del componente vengono specificati utilizzando attributi o [contenuto figlio](xref:blazor/components#child-content), che consentono di impostare le proprietà per il componente figlio. Per aggiungere un parametro al `Counter` componente, aggiornare il `@code` blocco del componente:

* Aggiungere una proprietà pubblica per `IncrementAmount` con un `[Parameter]` attributo.
* Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

`IncrementAmount` Specificare `<Counter>` nell'elemento del componente usando un attributo. `Index`

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Eseguire l'app. Il `Index` componente dispone di un contatore specifico che aumenta di dieci ogni volta che viene selezionato il pulsante **Click me** . Il `Counter` componente (*Counter. Razor*) a `/counter` continua a incrementare di uno.

## <a name="next-steps"></a>Passaggi successivi

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:signalr/introduction>
---
title: Introduzione a ASP.NET Core Blazor
author: guardrex
description: Inizia a usare Blazor compilando un'app Blazor con gli strumenti che preferisci.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/25/2019
uid: blazor/get-started
ms.openlocfilehash: 5aec91eff7de0732a47fec1aafa5e094c89c37a4
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71295438"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introduzione a ASP.NET Core Blazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Introduzione a Blazor:

1. Installare la versione più recente di [.NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .

1. Installare il modello [Webassembly Blazor](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19465.2
   ```

1. Seguire le istruzioni per la scelta degli strumenti:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Installare la versione più recente di [Visual Studio](https://visualstudio.com/vs/) con il carico di lavoro **sviluppo di ASP.NET e Web** .

   2 \. Creare un nuovo progetto.

   3 \. Selezionare **app Blazor**. Scegliere **Avanti**.

   4 \. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Scegliere **Crea**.

   5 \. Per un'esperienza di webassembly Blazor, scegliere il modello **app Webassembly Blazor** . Per un'esperienza del server Blazor, scegliere il modello di **app del server Blazor** . Scegliere **Crea**. Per informazioni sui due modelli di hosting Blazor, *Server Blazor* e *webassembly Blazor*, vedere <xref:blazor/hosting-models>.

   6 \. Premere **F5** per eseguire l'app.

   > [!NOTE]
   > Se è stata installata l'estensione di Visual Studio Blazor per una versione di anteprima precedente di ASP.NET Core Blazor (anteprima 6 o precedente), è possibile disinstallare l'estensione. L'installazione dei modelli di Blazor in una shell dei comandi è ora sufficiente per esporre i modelli in Visual Studio.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Installare [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Installare la versione più recente [ C# di per l'estensione Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3 \. Per un'esperienza di webassembly Blazor, eseguire il comando seguente in una shell dei comandi:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Per un'esperienza del server Blazor, eseguire il comando seguente in una shell dei comandi:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Per informazioni sui due modelli di hosting Blazor, *Server Blazor* e *webassembly Blazor*, vedere <xref:blazor/hosting-models>.

   4 \. Aprire la cartella *WebApplication1* in Visual Studio Code.

   5 \. Per un progetto server Blazor, l'IDE richiede l'aggiunta di asset per compilare ed eseguire il debug del progetto. Selezionare **Sì**.

   6 \. Se si usa un'app del server Blazor, eseguire l'app usando il debugger Visual Studio Code. Se si usa un'app webassembly Blazor, `dotnet run` eseguire dalla cartella del progetto dell'app.

   7 \. In un browser passare a `https://localhost:5001`.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

   Per un'esperienza di webassembly Blazor, eseguire i comandi seguenti in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per un'esperienza del server Blazor, eseguire i comandi seguenti in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per informazioni sui due modelli di hosting Blazor, *Server Blazor* e *webassembly Blazor*, vedere <xref:blazor/hosting-models>.

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

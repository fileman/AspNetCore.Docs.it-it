---
title: "Esercitazione: Introduzione all'uso di pagine Razor in ASP.NET Core"
author: rick-anderson
description: Questa serie di esercitazioni illustra come usare Razor Pages in ASP.NET Core. Offre informazioni su come creare un modello, generare codice per Razor Pages, usare Entity Framework Core e SQL Server per l'accesso ai dati, aggiungere funzionalità di ricerca, aggiungere la convalida dell'input e usare le migrazioni per aggiornare il modello.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 0cc00cb85b6054752417b82c783cfd4c306aeda5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082568"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Esercitazione: Introduzione all'uso di pagine Razor in ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Questa è la prima esercitazione di una serie che illustra i concetti di base per la creazione di un'app Web ASP.NET Core Razor Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Al termine della serie si otterrà un'app che gestisce un database di film.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Le attività di questa esercitazione sono le seguenti:

> [!div class="checklist"]
> * Creare un'app Web di Razor Pages.
> * Eseguire l'app.
> * Esaminare i file di progetto.

Alla fine di questa esercitazione si avrà un'app Web Razor Pages funzionante, che verrà compilata in esercitazioni successive.

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Creare un'app Web di Razor Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.
* Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti**.
  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Denominare il progetto **RazorPagesMovie**. È importante denominare il progetto *RazorPagesMovie* in modo che gli spazi dei nomi corrispondano nell'operazione copia/incolla del codice.
  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selezionare **ASP.NET Core 3.0** nell'elenco a discesa **Applicazione Web** e quindi selezionare **Crea**.

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Viene creato il progetto iniziale seguente:

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Passare alla directory (`cd`) che conterrà il progetto.

* Eseguire i comandi seguenti:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Il comando `dotnet new` crea un nuovo progetto Razor Pages nella cartella *RazorPagesMovie*.
  * Il comando `code` apre la cartella *RazorPagesMovie* nell'istanza corrente di Visual Studio Code.

* Quando l'icona a forma di fiamma di OmniSharp sulla barra di stato diventa verde, viene visualizzata una finestra di dialogo con la richiesta **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?** (Risorse di compilazione e debug mancanti da "RazorPagesMovie". Aggiungerle?) Selezionare **Sì**.

  Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Selezionare **File** > **Nuova soluzione**.

![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Selezionare **.NET Core** > **App** > **Applicazione Web** > **Avanti**.

  ![Finestra di dialogo Nuovo progetto di macOS](razor-pages-start/_static/webapp.png)

* Nella finestra di dialogo **Configura la nuova API Web ASP.NET Core** impostare **Framework di destinazione** su **.NET Core 3.0**.

  ![Selezione di .NET Core 3.0 per macOS](razor-pages-start/_static/targetframework3.png)

* Denominare il progetto **RazorPagesMovie**, quindi selezionare **Crea**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a>Aprire il progetto

Da Visual Studio, selezionare **File > Apri**, quindi selezionare il file *RazorPagesMovie.csproj*.

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Esecuzione dell'app

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Premere CTRL+F5 per l'esecuzione senza il debugger.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app. La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale. Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Premere **CTRL+F5** per l'esecuzione senza il debugger.

  Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`. La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale.

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Premere **ALT+CMD+INVIO** per l'esecuzione senza il debugger. In alternativa, passare alla barra dei menu e andare a Esegui>Avvia senza eseguire debug.

  Visual Studio avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Esaminare i file di progetto

Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.

### <a name="pages-folder"></a>Cartella Pages

Contiene le pagine Razor e i file di supporto. Ogni pagina Razor è una coppia di file:

* Un file con estensione *cshtml* contenente il markup HTML con codice C# che usa la sintassi Razor.
* Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.

I nomi dei file di supporto iniziano con un carattere di sottolineatura. Ad esempio, il file *_Layout.cshtml* configura elementi dell'interfaccia utente comuni a tutte le pagine. Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina. Per altre informazioni, vedere <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Cartella wwwroot

Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS. Per altre informazioni, vedere <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contiene i dati di configurazione, ad esempio le stringhe di connessione. Per altre informazioni, vedere <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contiene il punto di ingresso per il programma. Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contiene codice che consente di configurare il comportamento dell'app. Per altre informazioni, vedere <xref:fundamentals/startup>.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva della serie:

> [!div class="step-by-step"]
> [Aggiungere un modello](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Questa è la prima esercitazione di una serie. [La serie](xref:tutorials/razor-pages/index) illustra le nozioni di base della creazione di un'app Web ASP.NET Core Razor Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Al termine della serie si otterrà un'app che gestisce un database di film.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Le attività di questa esercitazione sono le seguenti:

> [!div class="checklist"]
> * Creare un'app Web di Razor Pages.
> * Eseguire l'app.
> * Esaminare i file di progetto.

Alla fine di questa esercitazione si avrà un'app Web Razor Pages funzionante, che verrà compilata in esercitazioni successive.

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Creare un'app Web di Razor Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.

* Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti**.

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Denominare il progetto **RazorPagesMovie**. È importante denominare il progetto *RazorPagesMovie* in modo che gli spazi dei nomi corrispondano nell'operazione copia/incolla del codice.

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selezionare **ASP.NET Core 2.2** nell'elenco a discesa **Applicazione Web** e quindi selezionare **Crea**.

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Viene creato il progetto iniziale seguente:

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Passare alla directory (`cd`) che conterrà il progetto.

* Eseguire i comandi seguenti:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Il comando `dotnet new` crea un nuovo progetto Razor Pages nella cartella *RazorPagesMovie*.
  * Il comando `code` apre la cartella *RazorPagesMovie* nell'istanza corrente di Visual Studio Code.

* Quando l'icona a forma di fiamma di OmniSharp sulla barra di stato diventa verde, viene visualizzata una finestra di dialogo con la richiesta **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?** (Risorse di compilazione e debug mancanti da "RazorPagesMovie". Aggiungerle?) Selezionare **Sì**.

  Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Da un terminale eseguire il comando seguente:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

I comandi precedenti usano l'[interfaccia della riga di comando di .NET Core](/dotnet/core/tools/dotnet) per creare un progetto Razor Pages.

## <a name="open-the-project"></a>Aprire il progetto

Da Visual Studio, selezionare **File > Apri**, quindi selezionare il file *RazorPagesMovie.csproj*.

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Esecuzione dell'app

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Premere CTRL+F5 per l'esecuzione senza il debugger.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app. La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale. Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.

* Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.

  Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Premere **CTRL+F5** per l'esecuzione senza il debugger.

  Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`. La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale.

* Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.

  Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Premere **Cmd-Opt-F5** per l'esecuzione senza il debugger.

  Visual Studio avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.

* Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.

  Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Esaminare i file di progetto

Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.

### <a name="pages-folder"></a>Cartella Pages

Contiene le pagine Razor e i file di supporto. Ogni pagina Razor è una coppia di file:

* Un file con estensione *cshtml* contenente il markup HTML con codice C# che usa la sintassi Razor.
* Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.

I nomi dei file di supporto iniziano con un carattere di sottolineatura. Ad esempio, il file *_Layout.cshtml* configura elementi dell'interfaccia utente comuni a tutte le pagine. Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina. Per altre informazioni, vedere <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Cartella wwwroot

Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS. Per altre informazioni, vedere <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contiene i dati di configurazione, ad esempio le stringhe di connessione. Per altre informazioni, vedere <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contiene il punto di ingresso per il programma. Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contiene codice che configura il comportamento dell'app, ad esempio se richiede il consenso per i cookie. Per altre informazioni, vedere <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva della serie:

> [!div class="step-by-step"]
> [Aggiungere un modello](xref:tutorials/razor-pages/model)

::: moniker-end

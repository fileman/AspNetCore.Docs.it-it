---
title: Ospitare e distribuire un webassembly ASP.NET Core Blazor
author: guardrex
description: Informazioni su come ospitare e distribuire un'app Blazor con ASP.NET Core, reti per la distribuzione di contenuti (CDN), file server e pagine di GitHub.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: cdb424137d80b280873347c1352fc43d23b4aec3
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211616"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Ospitare e distribuire un webassembly ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Con il [modello di hosting Webassembly Blazor](xref:blazor/hosting-models#blazor-webassembly):

* L'app Blazor, le relative dipendenze e il runtime .NET vengono scaricati nel browser.
* L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.

Sono supportate le strategie di distribuzione seguenti:

* L'app Blazor viene fornita da un'app ASP.NET Core. Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).
* L'app Blazor viene posizionata in un server o un servizio Web di hosting statico, in cui non viene usato .NET per fornire l'app Blazor. Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni sull'hosting di un'app Webassembly Blazor come applicazione secondaria IIS.

## <a name="rewrite-urls-for-correct-routing"></a>Riscrivere gli URL per il routing corretto

Il routing delle richieste per i componenti della pagina in un'app webassembly Blazor non è semplice come il routing delle richieste in un server Blazor, un'app ospitata. Si consideri un'app webassembly Blazor con due componenti:

* *Main.razor*&ndash; Viene caricato nella radice dell'app e contiene un collegamento al componente `About` (`href="About"`).
* *About.Razor* &ndash; Componente `About`.

Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):

1. Il browser invia una richiesta.
1. Viene restituita la pagina predefinita, di solito *index.html*.
1. *index.html* esegue il bootstrap dell'app.
1. Viene caricato il router di Blazor e viene eseguito il rendering del componente `Main` Razor.

Nella pagina principale, la selezione del collegamento al componente `About` funziona nel client perché il router Blazor impedisce al browser di inviare una richiesta su Internet a `www.contoso.com` per `About` e fornisce il componente `About` sottoposto a rendering. Tutte le richieste per gli endpoint interni *nell'app Webassembly Blazor* funzionano allo stesso modo: Le richieste non attivano richieste basate su browser a risorse ospitate nel server su Internet. Le richieste vengono gestite internamente dal router.

Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo. La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.

Dato che i browser inviano le richieste agli host basati su Internet per le pagine sul lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste per le risorse che non si trovano fisicamente nel server per la pagina *index.html*. Quando viene restituito *index. html* , il router Blazor dell'app accetta e risponde con la risorsa corretta.

Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file *Web. config* pubblicato dall'app. Per ulteriori informazioni, vedere la sezione [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Distribuzione ospitata con ASP.NET Core

Una *distribuzione ospitata* serve l'app Webassembly Blazor nei browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.

L'app Blazor è inclusa con l'app ASP.NET Core nell'output pubblicato in modo che le due app vengano distribuite insieme. È necessario un server Web in grado di ospitare un'app ASP.NET Core. Per una distribuzione ospitata, Visual Studio include il modello di progetto **App WebAssembly Blazor** (modello `blazorwasm` quando si usa il comando [dotnet new](/dotnet/core/tools/dotnet-new)) con l'opzione **Ospitato** selezionata.

Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.

Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Distribuzione autonoma

Una *distribuzione autonoma* serve l'app Webassembly Blazor come un set di file statici richiesti direttamente dai client. Qualsiasi file server statico è in grado di servire l'app Blazor.

Gli asset di distribuzione autonoma vengono pubblicati nella cartella */bin/Release/{FRAMEWORK DI DESTINAZIONE}/publish/{NOME ASSEMBLY}/dist*.

### <a name="iis"></a>IIS

IIS è un file server statico per app Blazor. Per configurare IIS per ospitare Blazor, vedere [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Creare un sito Web statico in IIS).

Gli asset pubblicati vengono creati nella cartella */bin/Release/{FRAMEWORK DESTINAZIONE}/publish*. Ospitare il contenuto della cartella *publish* nel server Web o nel servizio di hosting.

#### <a name="webconfig"></a>web.config

Quando viene pubblicato un progetto Blazor, viene creato un file *web.config* con la configurazione di IIS seguente:

* Vengono impostati tipi MIME per le estensioni di file seguenti:
  * *.dll* &ndash; `application/octet-stream`
  * *.json* &ndash; `application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
* Viene abilitata la compressione HTTP per i tipi MIME seguenti:
  * `application/octet-stream`
  * `application/wasm`
* Vengono stabilite le regole URL Rewrite Module:
  * Specificare la sottodirectory in cui risiedono gli asset statici dell'app ( *{NOME ASSEMBLY}/dist/{PERCORSO RICHIESTO}* ).
  * Creare routing di fallback SPA in modo che le richieste per gli asset non file vengano reindirizzate al documento predefinito dell'app nella relativa cartella degli asset statici ( *{NOME ASSEMBLY}/dist/index.html*).

#### <a name="install-the-url-rewrite-module"></a>Installare URL Rewrite Module

[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL. Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS). Il modulo deve essere scaricato dal sito Web IIS. Usare Installazione guidata piattaforma Web per installare il modulo:

1. In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web. Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.
1. Copiare il programma di installazione nel server. Eseguire il programma di installazione. Selezionare il pulsante **Installa** e accettare le condizioni di licenza. Al termine dell'installazione non è necessario un riavvio del server.

#### <a name="configure-the-website"></a>Configurare il sito Web

Impostare il **percorso fisico** del sito Web sulla cartella dell'app. La cartella contiene:

* Il file *web.config* usato da IIS per configurare il sito Web, inclusi le regole di reindirizzamento richieste e i tipi di contenuto di file.
* Cartella degli asset statici dell'app.

#### <a name="host-as-an-iis-sub-app"></a>Ospitare come applicazione secondaria IIS

Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:

* Disabilitare il gestore del modulo ASP.NET Core ereditato.

  Rimuovere il gestore nel file *Web. config* pubblicato dell'app Blaze aggiungendo una `<handlers>` sezione al file:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* `<system.webServer>` Disabilitare l'ereditarietà della sezione radice (padre) dell'app usando un `<location>` elemento con `inheritInChildApplications` impostato su: `false`

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:host-and-deploy/blazor/index#app-base-path). Impostare il percorso di base dell'app nel file *index.html* dell'app sull'alias IIS usato durante la configurazione della sottoapp in IIS.

#### <a name="troubleshooting"></a>Risoluzione dei problemi

Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module. Quando il modulo non è installato, il file *web.config* non può essere analizzato da IIS. Ciò impedisce a Gestione IIS di caricare la configurazione del sito Web e al sito Web di fornire i file statici di Blazor.

Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Archiviazione di Azure

L'hosting di file statici di [archiviazione di Azure](/azure/storage/) consente l'hosting di app Blazor senza server. Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.

Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:

* Impostare **Nome del documento di indice** su `index.html`.
* Impostare **Percorso del documento di errore** su `index.html`. I componenti di Razor e altri endpoint non basati su file non risiedono in percorsi fisici nel contenuto statico archiviato dal servizio BLOB. Quando viene ricevuta una richiesta per una di queste risorse che deve essere gestita dal router Blazor, l'errore *404 - Non trovato* generato dal servizio BLOB instrada la richiesta al percorso indicato in **Percorso del documento di errore**. Viene restituito il BLOB *index.html* e il router Blazor carica ed elabora il percorso.

Per altre informazioni, vedere [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website) (Hosting di siti Web statici in Archiviazione di Azure).

### <a name="nginx"></a>Nginx

Il file *nginx.conf* seguente è semplificato per mostrare come configurare Nginx per l'invio del file *index.html* ogni volta che non riesce a trovare un file su disco corrispondente.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).

### <a name="nginx-in-docker"></a>Nginx in Docker

Per ospitare Blazor in Docker usando Nginx, configurare il Dockerfile per l'uso dell'immagine di Nginx basata su Alpine. Aggiornare il Dockerfile per copiare il file *nginx.config* nel contenitore.

Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a>Pagine di GitHub

Per gestire le riscritture degli URL, aggiungere un file *404.html* con uno script che gestisce il reindirizzamento della richiesta alla pagina *index.html*. Per un esempio di implementazione fornito dalla community, vedere [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (App a pagina singola per pagine GitHub) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)). È possibile visualizzare un esempio d'uso dell'approccio della community in [blazor-demo/blazor-demo.github.io su GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sito live](https://blazor-demo.github.io/)).

Quando si usa un sito di progetto anziché un sito dell'organizzazione, aggiungere o aggiornare il tag `<base>` in *index.html*. Impostare il valore dell'attributo `href` sul nome del repository GitHub con una barra finale (ad esempio, `my-repository/`.

## <a name="host-configuration-values"></a>Valori di configurazione dell'host

Le [app Webassembly Blazor](xref:blazor/hosting-models#blazor-webassembly) possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo.

### <a name="content-root"></a>Radice del contenuto

L'argomento `--contentroot` imposta il percorso assoluto sulla directory che contiene i file di contenuto dell'app. Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.

* Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi. Dalla directory dell'app, eseguire:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**. Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* In Visual Studio specificare l'argomento in **Proprietà** > **Debug** > **Argomenti applicazione**. Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Base del percorso

L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice ( `<base>` il `href` tag è impostato su un percorso diverso `/` da per la gestione temporanea e la produzione). Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app. Per altre informazioni, vedere [percorso di base dell'app](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`. Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).

* Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi. Dalla directory dell'app, eseguire:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**. Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* In Visual Studio specificare l'argomento in **Proprietà** > **Debug** > **Argomenti applicazione**. Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL

L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.

* Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi. Dalla directory dell'app, eseguire:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**. Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* In Visual Studio specificare l'argomento in **Proprietà** > **Debug** > **Argomenti applicazione**. Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Configurare il linker

Blazor esegue il collegamento di linguaggio intermedio (IL) in ogni build per rimuovere IL non necessario dagli assembly di output. Il collegamento degli assembly può essere controllato in fase di compilazione. Per altre informazioni, vedere <xref:host-and-deploy/blazor/configure-linker>.

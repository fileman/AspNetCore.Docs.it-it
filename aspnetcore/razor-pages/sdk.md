---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Informazioni su come Razor Pages in ASP.NET Core semplifica e rende più produttiva la scrittura di codice incentrata sulle pagine rispetto a MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
uid: razor-pages/sdk
ms.openlocfilehash: 81025c14ba68971ca5d3cfc9387c2f50dd247654
ms.sourcegitcommit: 983b31449fe398e6e922eb13e9eb6f4287ec91e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2019
ms.locfileid: "70017404"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Panoramica

Il [!INCLUDE[](~/includes/2.1-SDK.md)] include il `Microsoft.NET.Sdk.Razor` MSBuild SDK (SDK di Razor). Il Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* È necessario per compilare, creare un pacchetto e pubblicare progetti contenenti file [Razor](xref:mvc/views/razor) per ASP.NET Core progetti basati su MVC o [Blazor](xref:blazor/index) .
* Include un set di destinazioni, proprietà ed elementi predefiniti che consentono di personalizzare la compilazione dei file Razor ( *. cshtml* o *. Razor*).

Razor SDK include `Content` gli elementi con `Include` attributi impostati sui `**\*.cshtml` modelli glob `**\*.razor` e. I file corrispondenti vengono pubblicati.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Consente di standardizzare l'esperienza in termini di compilazione, creazione dei pacchetti e pubblicazione dei progetti contenenti file [Razor](xref:mvc/views/razor) per i progetti ASP.NET Core basati su MVC.
* Include un set di destinazioni, proprietà e di elementi predefiniti che consentono di personalizzare la compilazione dei file Razor.

Razor SDK include un `Content` elemento con un `Include` attributo impostato sul `**\*.cshtml` modello glob. I file corrispondenti vengono pubblicati.

::: moniker-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Usare Razor SDK

La maggior parte delle App web non è necessario fare riferimento esplicitamente al SDK di Razor.

::: moniker range=">= aspnetcore-3.0"

Per usare Razor SDK per compilare librerie di classi contenenti visualizzazioni Razor o Razor Pages, è consigliabile iniziare con il modello di progetto libreria di classi Razor (RCL). Un RCL usato per compilare file Blazor (*Razor*) richiede almeno un riferimento al pacchetto [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Un RCL usato per compilare visualizzazioni o pagine Razor (file con*estensione cshtml* ) richiede almeno la destinazione `netcoreapp3.0` o una versione successiva e dispone di un oggetto `FrameworkReference` nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) nel file di progetto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Per usare il Razor SDK per compilare librerie di classi contenenti visualizzazioni Razor o Razor Pages:

* Usare `Microsoft.NET.Sdk.Razor` anziché `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* In genere, un riferimento al pacchetto `Microsoft.AspNetCore.Mvc` è obbligatoria per ricevere le dipendenze aggiuntive necessarie per generare e compilare le pagine Razor e le visualizzazioni Razor. Come minimo, il progetto deve aggiungere i riferimenti ai pacchetti per:

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  Il `Microsoft.AspNetCore.Razor.Design` pacchetto fornisce l'attività di compilazione Razor e delle destinazioni del progetto.

  I pacchetti precedenti sono inclusi in `Microsoft.AspNetCore.Mvc`. Il markup seguente illustra un file di progetto che usa il SDK di Razor per generare i file Razor per un'app ASP.NET Core Razor Pages:
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Il `Microsoft.AspNetCore.Razor.Design` e `Microsoft.AspNetCore.Mvc.Razor.Extensions` i pacchetti sono inclusi nel [Microsoft.AspNetCore.App metapacchetto](xref:fundamentals/metapackage-app). Tuttavia, la versione l'uzo `Microsoft.AspNetCore.App` riferimento al pacchetto fornisce un metapacchetto all'app che non include la versione più recente di `Microsoft.AspNetCore.Razor.Design`. I progetti devono fare riferimento a una versione coerente del `Microsoft.AspNetCore.Razor.Design` (o `Microsoft.AspNetCore.Mvc`) in modo che siano incluse le correzioni più recenti in fase di compilazione per Razor. Per altre informazioni, vedere [questo problema su GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Proprietà

Le proprietà seguenti controllano il comportamento del Razor SDK come parte della compilazione del progetto:

* `RazorCompileOnBuild` &ndash; Quando `true`, compila e crea l'assembly di Razor come parte della compilazione del progetto. Il valore predefinito è `true`.
* `RazorCompileOnPublish` &ndash; Quando `true`, compila e crea l'assembly di Razor come parte della pubblicazione del progetto. Il valore predefinito è `true`.

Le proprietà e gli elementi nella tabella seguente vengono utilizzati per configurare input e output per il SDK di Razor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> A partire da ASP.NET Core 3,0, le visualizzazioni MVC o Razor Pages non vengono gestite per `RazorCompileOnBuild` impostazione `RazorCompileOnPublish` predefinita se le proprietà o MSBuild nel file di progetto sono disabilitate. Le applicazioni devono aggiungere un riferimento esplicito al pacchetto [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se l'app si basa sulla compilazione di runtime per elaborare i file con *estensione cshtml* .

::: moniker-end

| Elementi | Descrizione |
| ----- | ----------- |
| `RazorGenerate` | Elementi Item (file con*estensione cshtml* ) che sono input per la generazione di codice. |
| `RazorComponent` | Elementi elemento (file*Razor* ) che sono input per la generazione di codice del componente Razor. |
| `RazorCompile` | Elementi Item (file con*estensione cs* ) che sono input per le destinazioni di compilazione Razor. Usare questa `ItemGroup` impostazione per specificare i file aggiuntivi da compilare nell'assembly Razor. |
| `RazorTargetAssemblyAttribute` | Elementi della voce usati per attributi di generazione del codice per l'assembly Razor. Ad esempio:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementi aggiunti come risorse incorporate nell'assembly generato Razor. |

| Proprietà | Descrizione |
| -------- | ----------- |
| `RazorTargetName` | Nome di file (senza estensione) dell'assembly generato da Razor. | 
| `RazorOutputPath` | Directory di output di Razor. |
| `RazorCompileToolset` | Usato per determinare il set di strumenti utilizzato per compilare l'assembly Razor. I valori validi sono `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Il valore predefinito è `true`. Quando `true`, include i file *Web. config*, *. JSON*e *. cshtml* come contenuto nel progetto. Quando viene fatto riferimento tramite `Microsoft.NET.Sdk.Web`, i file sotto *wwwroot* e sono inclusi anche i file di configurazione. |
| `EnableDefaultRazorGenerateItems` | Quando è `true`, include i file *cshtml* degli elementi di `Content` negli elementi di `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, genera una *. cs* file che contiene gli attributi specificati da `RazorAssemblyAttribute` e include il file nell'output di compilazione. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando è `true`, aggiunge un set predefinito di attributi degli assembly a `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, le copie `RazorGenerate` gli elementi (*cshtml*) file alla directory di pubblicazione. In genere, i file Razor non sono necessari per un'app pubblicata se partecipano alla compilazione in fase di compilazione o in fase di pubblicazione. Il valore predefinito è `false`. |
| `CopyRefAssembliesToPublishDirectory` | Quando è `true`, copiare gli elementi dell'assembly di riferimento nella directory di pubblicazione. In genere, gli assembly di riferimento non sono necessari per un'app pubblicata se compilazione Razor si verifica in fase di compilazione o in fase di pubblicazione. Impostare su `true` se l'app pubblicata richiede la compilazione di runtime. Ad esempio, impostare il valore su `true` se l'app modifica *. cshtml* file in fase di esecuzione o utilizza visualizzazioni incorporate. Il valore predefinito è `false`. |
| `IncludeRazorContentInPack` | Quando `true`, tutti gli elementi di contenuto di Razor (*cshtml* file) sono contrassegnati per l'inclusione nel pacchetto NuGet generato. Il valore predefinito è `false`. |
| `EmbedRazorGenerateSources` | Quando è `true`, aggiunge gli elementi (*cshtml*) di RazorGenerate come file incorporati nell'assembly Razor generato. Il valore predefinito è `false`. |
| `UseRazorBuildServer` | Quando è `true`, usa un processo del server di compilazione permanente per ripartire il lavoro di generazione del codice. Valore predefinito è il valore di `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, l'SDK genera attributi aggiuntivi usati da MVC in fase di esecuzione per eseguire l'individuazione delle parti dell'applicazione. |

Per altre informazioni sulle proprietà, vedere [Proprietà di MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Destinazioni

Il Razor SDK definisce due obiettivi principali:

* `RazorGenerate` &ndash; Genera codice *cs* dei file dalla `RazorGenerate` elementi item. Utilizzare la `RazorGenerateDependsOn` proprietà per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.
* `RazorCompile` &ndash; Le compilazioni generate *cs* i file un assembly di Razor. `RazorCompileDependsOn` Usare per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.
* `RazorComponentGenerate`Il codice genera file con *estensione cs* per `RazorComponent` gli elementi Item. &ndash; Utilizzare la `RazorComponentGenerateDependsOn` proprietà per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.

### <a name="runtime-compilation-of-razor-views"></a>Compilazione runtime di visualizzazioni Razor

* Per impostazione predefinita, Razor SDK non pubblica gli assembly di riferimento necessari per eseguire la compilazione runtime. Vengono pertanto generati errori di compilazione nel caso in cui il modello di applicazione si basi su una compilazione runtime&mdash;ad esempio, l'app usa visualizzazioni incorporate o modifica le visualizzazioni dopo aver pubblicato l'app. Impostare `CopyRefAssembliesToPublishDirectory` su `true` per continuare con la pubblicazione degli assembly di riferimento.

* Per un'app web, assicurarsi che l'app è destinata al `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Versione della lingua Razor

Quando la destinazione `Microsoft.NET.Sdk.Web` è SDK, la versione della lingua Razor viene dedotta dalla versione del Framework di destinazione dell'app. Per i progetti destinati all' `Microsoft.NET.Sdk.Razor` SDK o nel raro caso in cui l'app richieda una versione diversa della lingua Razor rispetto al valore derivato, è possibile configurare una versione impostando la `<RazorLangVersion>` proprietà nel file di progetto dell'app:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

La versione del linguaggio Razor è strettamente integrata con la versione del runtime per cui è stata creata. La destinazione di una versione di linguaggio non progettata per il runtime non è supportata e probabilmente genera errori di compilazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Aggiunte al formato csproj per .NET Core](/dotnet/core/tools/csproj)
* [Elementi di progetto MSBuild comuni](/visualstudio/msbuild/common-msbuild-project-items)

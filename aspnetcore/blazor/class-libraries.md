---
title: Librerie di classi dei componenti di ASP.NET Core Razor
author: guardrex
description: Scopri in che modo i componenti possono essere inclusi nelle app Blazor da una libreria di componenti esterna.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/class-libraries
ms.openlocfilehash: d9ef276357e95d97b7d89427c5e237aceea7a0d3
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207109"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>Librerie di classi dei componenti di ASP.NET Core Razor

Di [Simon Timms](https://github.com/stimms)

I componenti possono essere condivisi in una [libreria di classi Razor (RCL)](xref:razor-pages/ui-class) tra i progetti. È possibile includere una *libreria di classi di componenti Razor* da:

* Un altro progetto nella soluzione.
* Un pacchetto NuGet.
* Libreria .NET A cui si fa riferimento.

Così come i componenti sono tipi .NET normali, i componenti forniti da un RCL sono assembly .NET normali.

## <a name="create-an-rcl"></a>Creare un RCL

Per configurare l'ambiente per <xref:blazor/get-started> Blazor, seguire le istruzioni riportate nell'articolo.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Creare un nuovo progetto.
1. Selezionare **libreria di classi Razor**. Scegliere **Avanti**.
1. Nella finestra di dialogo **Crea una nuova libreria di classi Razor** selezionare **Crea**.
1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Gli esempi in questo argomento usano il nome `MyComponentLib1`del progetto. Scegliere **Crea**.
1. Aggiungere RCL a una soluzione:
   1. Fare clic con il pulsante destro del mouse sulla soluzione. Selezionare **Aggiungi** > **progetto esistente**.
   1. Passare al file di progetto di RCL.
   1. Selezionare il file di progetto di RCL (con*estensione csproj*).
1. Aggiungere un riferimento a RCL dall'app:
   1. Fare clic con il pulsante destro del mouse sul progetto app. Selezionare **Aggiungi** > **riferimento**.
   1. Selezionare il progetto RCL. Scegliere **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

1. Usare il modello di **libreria di classi Razor** (`razorclasslib`) con il comando [DotNet New](/dotnet/core/tools/dotnet-new) in una shell dei comandi. Nell'esempio seguente viene creato un RCL denominato `MyComponentLib1`. La cartella che include `MyComponentLib1` viene creata automaticamente quando si esegue il comando:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. Per aggiungere la libreria a un progetto esistente, usare il comando [DotNet Add Reference](/dotnet/core/tools/dotnet-add-reference) in una shell dei comandi. Nell'esempio seguente viene aggiunto RCL all'app. Eseguire il comando seguente dalla cartella del progetto dell'app con il percorso della libreria:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Utilizzare un componente di libreria

Per utilizzare i componenti definiti in una raccolta in un altro progetto, utilizzare uno degli approcci seguenti:

* Usare il nome completo del tipo con lo spazio dei nomi.
* Usare la direttiva [ \@using](xref:mvc/views/razor#using) di Razor. I singoli componenti possono essere aggiunti in base al nome.

Negli esempi `MyComponentLib1` seguenti è una libreria di componenti contenente un `SalesReport` componente di.

È `SalesReport` possibile fare riferimento al componente usando il nome completo del tipo con lo spazio dei nomi:

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

È possibile fare riferimento al componente anche se la libreria viene inserita nell'ambito con una `@using` direttiva:

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Includere la `@using MyComponentLib1` direttiva nel file *_Import. Razor* di primo livello per rendere disponibili i componenti della libreria a un intero progetto. Aggiungere la direttiva a un file *_Import. Razor* a qualsiasi livello per applicare lo spazio dei nomi a una singola pagina o a un set di pagine all'interno di una cartella.

## <a name="build-pack-and-ship-to-nuget"></a>Compilare, comprimere e spedire a NuGet

Poiché le librerie dei componenti sono librerie .NET standard, la creazione di pacchetti e la spedizione a NuGet non è diversa dalla creazione di pacchetti e dalla distribuzione di qualsiasi libreria a NuGet. La creazione di pacchetti viene eseguita usando il comando [DotNet Pack](/dotnet/core/tools/dotnet-pack) in una shell dei comandi:

```dotnetcli
dotnet pack
```

Caricare il pacchetto in NuGet usando il comando [DotNet NuGet Publish](/dotnet/core/tools/dotnet-nuget-push) in una shell dei comandi:

```dotnetcli
dotnet nuget publish
```

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Creare una libreria di classi di componenti Razor con asset statici

Un RCL può includere asset statici. Gli asset statici sono disponibili per qualsiasi app che usa la libreria. Per altre informazioni, vedere <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:razor-pages/ui-class>

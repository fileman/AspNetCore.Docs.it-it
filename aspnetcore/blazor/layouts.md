---
title: Layout di ASP.NET Core Blazor
author: guardrex
description: Informazioni su come creare componenti di layout riutilizzabili per le app Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/layouts
ms.openlocfilehash: 064ffafb8e7f3e76e5bd7bde0e06ef271fe92542
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211578"
---
# <a name="aspnet-core-blazor-layouts"></a>Layout di ASP.NET Core Blazor

Di [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)

Alcuni elementi dell'app, ad esempio i menu, i messaggi di copyright e i logo aziendali, fanno in genere parte del layout generale dell'app e vengono usati da ogni componente nell'app. La copia del codice di questi elementi in tutti i componenti di un'app non è un approccio&mdash;efficace ogni volta che uno degli elementi richiede un aggiornamento, ogni componente deve essere aggiornato. Tale duplicazione è difficile da gestire e può causare contenuti incoerenti nel tempo. I *layout* risolvono questo problema.

Tecnicamente, un layout è semplicemente un altro componente. Un layout è definito in un modello Razor o nel C# codice e può usare [Data Binding](xref:blazor/components#data-binding), l' [inserimento di dipendenze](xref:blazor/dependency-injection)e altri scenari di componenti.

Per trasformare un *componente* in un *layout*, il componente:

* Eredita da `LayoutComponentBase`, che definisce una `Body` proprietà per il contenuto di cui è stato eseguito il rendering all'interno del layout.
* Usa la sintassi Razor `@Body` per specificare il percorso nel markup di layout in cui viene eseguito il rendering del contenuto.

Nell'esempio di codice seguente viene illustrato il modello Razor di un componente di layout, *MainLayout. Razor*. Il layout eredita `LayoutComponentBase` e imposta l' `@Body` oggetto tra la barra di spostamento e il piè di pagina:

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

In un'app basata su uno dei modelli di app Blazor, il `MainLayout` componente (*MainLayout. Razor*) si trova nella cartella *condivisa* dell'app.

## <a name="default-layout"></a>Layout predefinito

Specificare il layout predefinito dell'app nel `Router` componente nel file *app. Razor* dell'app. Il componente `Router` seguente, fornito dai modelli Blazor predefiniti, imposta il layout `MainLayout` predefinito sul componente:

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Per fornire un layout predefinito per `NotFound` il contenuto, specificare `LayoutView` un `NotFound` oggetto per il contenuto:

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Per ulteriori informazioni sul `Router` componente, vedere. <xref:blazor/routing>

La specifica del layout come layout predefinito nel router è una procedura utile perché può essere sottoposta a override in base al singolo componente o alla cartella. Preferisci usare il router per impostare il layout predefinito dell'app perché è la tecnica più generale.

## <a name="specify-a-layout-in-a-component"></a>Specificare un layout in un componente

Usare la direttiva `@layout` Razor per applicare un layout a un componente. Il compilatore esegue `@layout` la conversione `LayoutAttribute`in un oggetto, che viene applicato alla classe Component.

Il contenuto del componente seguente `MasterList` viene inserito `MasterLayout` nella posizione di `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

La specifica del layout direttamente in un componente sostituisce un set di *layout predefinito* nel router o `@layout` una direttiva importata da *_Imports. Razor*.

## <a name="centralized-layout-selection"></a>Selezione layout centralizzata

Ogni cartella di un'app può contenere facoltativamente un file modello denominato *_Imports. Razor*. Il compilatore include le direttive specificate nel file di importazione in tutti i modelli Razor nella stessa cartella e in modo ricorsivo in tutte le relative sottocartelle. Pertanto, un file *_Imports. Razor* contenente `@layout MyCoolLayout` garantisce che tutti i componenti di una cartella usino. `MyCoolLayout` Non è necessario aggiungere `@layout MyCoolLayout` ripetutamente a tutti i file con *estensione Razor* nella cartella e nelle sottocartelle. `@using`le direttive vengono applicate anche ai componenti nello stesso modo.

Il file *_Imports. Razor* seguente importa:

* `MyCoolLayout`.
* Tutti i componenti Razor nella stessa cartella e in tutte le sottocartelle.
* Spazio dei nomi `BlazorApp1.Data` .
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

Il file *_Imports. Razor* è simile al [file _ViewImports. cshtml per le visualizzazioni Razor e le pagine,](xref:mvc/views/layout#importing-shared-directives) ma viene applicato in modo specifico ai file del componente Razor.

Se si specifica un layout in *_Imports. Razor* , viene eseguito l'override di un layout specificato come *layout predefinito*del router.

## <a name="nested-layouts"></a>Layout annidati

Le app possono essere costituite da layout annidati. Un componente può fare riferimento a un layout che a sua volta fa riferimento a un altro layout. Ad esempio, i layout di annidamento vengono usati per creare una struttura di menu a più livelli.

Nell'esempio seguente viene illustrato come utilizzare layout annidati. Il file *EpisodesComponent. Razor* è il componente da visualizzare. Il componente fa riferimento `MasterListLayout`a:

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Il file *MasterListLayout. Razor* fornisce `MasterListLayout`. Il layout fa riferimento a un `MasterLayout`altro layout,, in cui viene eseguito il rendering. `EpisodesComponent`viene sottoposto `@Body` a rendering quando viene visualizzato:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Infine, `MasterLayout` in *MasterLayout. Razor* contiene gli elementi di layout di primo livello, ad esempio l'intestazione, il menu principale e il piè di pagina. `MasterListLayout`con viene `EpisodesComponent` eseguito il rendering `@Body` di quando viene visualizzato:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/layout>

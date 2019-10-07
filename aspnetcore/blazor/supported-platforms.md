---
title: Piattaforme supportate da ASP.NET Core Blazor
author: guardrex
description: Informazioni sulle piattaforme supportate per ASP.NET Core blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/supported-platforms
ms.openlocfilehash: b769ee175cde7c9a613d7fb70949de129ca428d3
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211576"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Piattaforme supportate da ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Requisiti del browser

### <a name="blazor-webassembly"></a>WebAssembly Blazor

| Browser                          | Versione               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Corrente               |
| Mozilla Firefox                  | Corrente               |
| Google Chrome, incluso Android | Corrente               |
| Safari, incluso iOS            | Corrente               |
| Microsoft Internet Explorer      | Non supportato&dagger; |

&dagger;Microsoft Internet Explorer non supporta [webassembly](https://webassembly.org).

### <a name="blazor-server"></a>Server Blazor

| Browser                          | Versione    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Corrente    |
| Mozilla Firefox                  | Corrente    |
| Google Chrome, incluso Android | Corrente    |
| Safari, incluso iOS            | Corrente    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Sono necessari altri riempimenti, ad esempio le promesse possono essere aggiunte tramite un bundle [polyfill.io](https://polyfill.io/v3/) .

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/hosting-models>

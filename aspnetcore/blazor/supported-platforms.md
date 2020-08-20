---
title: Unterstützte Plattformen für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625946"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>Unterstützte Plattformen für ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Browseranforderungen

### Blazor WebAssembly

| Browser                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Aktuell               |
| Mozilla Firefox                  | Aktuell               |
| Google Chrome, einschließlich Android | Aktuell               |
| Safari, einschließlich iOS            | Aktuell               |
| Microsoft Internet Explorer      | Nicht unterstützt&dagger; |

&dagger;Microsoft Internet Explorer unterstützt [WebAssembly](https://webassembly.org) nicht.

### Blazor Server

| Browser                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Aktuell    |
| Mozilla Firefox                  | Aktuell    |
| Google Chrome, einschließlich Android | Aktuell    |
| Safari, einschließlich iOS            | Aktuell    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Zusätzliche Polyfills sind erforderlich (z. B. können Zusagen über ein [`Polyfill.io`](https://polyfill.io/v3/)-Bündel hinzugefügt werden).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/hosting-models>

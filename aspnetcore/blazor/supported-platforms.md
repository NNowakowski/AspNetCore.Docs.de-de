---
title: Unterstützte Plattformen für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013527"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="39016-103">Unterstützte Plattformen für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="39016-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="39016-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="39016-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="39016-105">Browseranforderungen</span><span class="sxs-lookup"><span data-stu-id="39016-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="39016-106">Browser</span><span class="sxs-lookup"><span data-stu-id="39016-106">Browser</span></span>                          | <span data-ttu-id="39016-107">Version</span><span class="sxs-lookup"><span data-stu-id="39016-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="39016-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="39016-108">Microsoft Edge</span></span>                   | <span data-ttu-id="39016-109">Aktuell</span><span class="sxs-lookup"><span data-stu-id="39016-109">Current</span></span>               |
| <span data-ttu-id="39016-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="39016-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="39016-111">Aktuell</span><span class="sxs-lookup"><span data-stu-id="39016-111">Current</span></span>               |
| <span data-ttu-id="39016-112">Google Chrome, einschließlich Android</span><span class="sxs-lookup"><span data-stu-id="39016-112">Google Chrome, including Android</span></span> | <span data-ttu-id="39016-113">Aktuell</span><span class="sxs-lookup"><span data-stu-id="39016-113">Current</span></span>               |
| <span data-ttu-id="39016-114">Safari, einschließlich iOS</span><span class="sxs-lookup"><span data-stu-id="39016-114">Safari, including iOS</span></span>            | <span data-ttu-id="39016-115">Aktuell</span><span class="sxs-lookup"><span data-stu-id="39016-115">Current</span></span>               |
| <span data-ttu-id="39016-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="39016-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="39016-117">Nicht unterstützt&dagger;</span><span class="sxs-lookup"><span data-stu-id="39016-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="39016-118">&dagger;Microsoft Internet Explorer unterstützt [WebAssembly](https://webassembly.org) nicht.</span><span class="sxs-lookup"><span data-stu-id="39016-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="39016-119">Browser</span><span class="sxs-lookup"><span data-stu-id="39016-119">Browser</span></span>                          | <span data-ttu-id="39016-120">Version</span><span class="sxs-lookup"><span data-stu-id="39016-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="39016-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="39016-121">Microsoft Edge</span></span>                   | <span data-ttu-id="39016-122">Aktuell</span><span class="sxs-lookup"><span data-stu-id="39016-122">Current</span></span>    |
| <span data-ttu-id="39016-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="39016-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="39016-124">Aktuell</span><span class="sxs-lookup"><span data-stu-id="39016-124">Current</span></span>    |
| <span data-ttu-id="39016-125">Google Chrome, einschließlich Android</span><span class="sxs-lookup"><span data-stu-id="39016-125">Google Chrome, including Android</span></span> | <span data-ttu-id="39016-126">Aktuell</span><span class="sxs-lookup"><span data-stu-id="39016-126">Current</span></span>    |
| <span data-ttu-id="39016-127">Safari, einschließlich iOS</span><span class="sxs-lookup"><span data-stu-id="39016-127">Safari, including iOS</span></span>            | <span data-ttu-id="39016-128">Aktuell</span><span class="sxs-lookup"><span data-stu-id="39016-128">Current</span></span>    |
| <span data-ttu-id="39016-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="39016-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="39016-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="39016-130">11&dagger;</span></span> |

<span data-ttu-id="39016-131">&dagger;Zusätzliche Polyfills sind erforderlich (z. B. können Zusagen über ein [`Polyfill.io`](https://polyfill.io/v3/)-Bündel hinzugefügt werden).</span><span class="sxs-lookup"><span data-stu-id="39016-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39016-132">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="39016-132">Additional resources</span></span>

* <xref:blazor/hosting-models>

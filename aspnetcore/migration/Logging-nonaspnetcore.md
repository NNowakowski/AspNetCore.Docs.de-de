---
title: Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0
author: pakrym
description: Erfahren Sie, wie Sie eine Non-ASP.net Core-Anwendung, die Microsoft. Extensions. Logging verwendet, von 2,1 zu 2,2 oder 3,0 migrieren.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: edb1c6456d0cbbac57d739f61b4c159f146e4f7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015113"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="c1def-103">Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0</span><span class="sxs-lookup"><span data-stu-id="c1def-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="c1def-104">In diesem Artikel werden die allgemeinen Schritte zum Migrieren einer Non-ASP.net Core-Anwendung beschrieben, die `Microsoft.Extensions.Logging` zwischen 2,1 und 2,2 oder 3,0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1def-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="c1def-105">2.1 zu 2.2</span><span class="sxs-lookup"><span data-stu-id="c1def-105">2.1 to 2.2</span></span>

<span data-ttu-id="c1def-106">Erstellen Sie manuell `ServiceCollection` und rufen Sie auf `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="c1def-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="c1def-107">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c1def-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="c1def-108">2,2 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c1def-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="c1def-109">2,1 bis 3,0</span><span class="sxs-lookup"><span data-stu-id="c1def-109">2.1 to 3.0</span></span>

<span data-ttu-id="c1def-110">Verwenden Sie in 3,0 `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="c1def-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="c1def-111">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c1def-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="c1def-112">3,0 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c1def-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="c1def-113">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c1def-113">Additional resources</span></span>

* <span data-ttu-id="c1def-114">Das [nuget-Paket Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="c1def-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>

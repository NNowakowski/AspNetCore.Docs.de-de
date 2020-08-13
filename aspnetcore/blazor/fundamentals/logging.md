---
title: ASP.NET Core Blazor-Protokollierung
author: guardrex
description: Erfahren Sie mehr über die Protokollierung in Blazor-Apps, einschließlich der Konfiguration auf Protokollebene und des Schreibens von Protokollmeldungen von Razor-Komponenten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: c2e8ee24c135b3c2f9b6be5b0e0efa54750c68b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014358"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="4451d-103">ASP.NET Core Blazor-Protokollierung</span><span class="sxs-lookup"><span data-stu-id="4451d-103">ASP.NET Core Blazor logging</span></span>

## Blazor WebAssembly

<span data-ttu-id="4451d-104">Konfigurieren Sie die Protokollierung in Blazor WebAssembly-Apps mit der `WebAssemblyHostBuilder.Logging`-Eigenschaft in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4451d-104">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="4451d-105">Die `Logging`-Eigenschaft ist vom Typ <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, deshalb sind alle auf <xref:Microsoft.Extensions.Logging.ILoggingBuilder> verfügbaren Erweiterungsmethoden auch auf `Logging` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4451d-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="4451d-106">Die Protokollierungskonfiguration kann aus App-Einstellungsdateien geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4451d-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="4451d-107">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4451d-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## Blazor Server

<span data-ttu-id="4451d-108">Einen allgemeinen Leitfaden zur Protokollierung in ASP.NET Core finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="4451d-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="4451d-109">Blazor WebAssembly SignalR .NET-Clientprotokollierung</span><span class="sxs-lookup"><span data-stu-id="4451d-109">Blazor WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="4451d-110">Fügen Sie eine <xref:Microsoft.Extensions.Logging.ILoggerProvider>-Instanz ein, um `WebAssemblyConsoleLogger` zu den an <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> weitergegebenen Protokollierungsanbietern hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4451d-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="4451d-111">Anders als bei einem herkömmlichen <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> handelt es sich bei `WebAssemblyConsoleLogger` um einen Wrapper um browserspezifische Protokollierungs-APIs (z. B. `console.log`).</span><span class="sxs-lookup"><span data-stu-id="4451d-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="4451d-112">Die Verwendung von `WebAssemblyConsoleLogger` ermöglicht die Protokollierung in Mono in einem Browserkontext.</span><span class="sxs-lookup"><span data-stu-id="4451d-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="4451d-113">Protokoll in Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="4451d-113">Log in Razor components</span></span>

<span data-ttu-id="4451d-114">Protokollierungen beachten die Startkonfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="4451d-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="4451d-115">Die `using`-Direktive für <xref:Microsoft.Extensions.Logging> ist erforderlich, um die IntelliSense-Vervollständigungen für APIs zu unterstützen, zum Beispiel <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> und <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="4451d-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="4451d-116">Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILogger> in Razor-Komponenten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="4451d-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="4451d-117">Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor-Komponenten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="4451d-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="4451d-118">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4451d-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>

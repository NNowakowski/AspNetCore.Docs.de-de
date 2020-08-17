---
title: Debuggen von ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
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
uid: blazor/debug
ms.openlocfilehash: 225916411550cc8e89c604e1426316843bb0ff52
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014541"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="adfd8-103">Debuggen von ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="adfd8-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="adfd8-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="adfd8-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="adfd8-105">Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Edge/Chrome) durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="adfd8-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="adfd8-106">Alternativ können Sie Ihre App mit Visual Studio oder Visual Studio Code debuggen.</span><span class="sxs-lookup"><span data-stu-id="adfd8-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="adfd8-107">Verfügbare Szenarien:</span><span class="sxs-lookup"><span data-stu-id="adfd8-107">Available scenarios include:</span></span>

* <span data-ttu-id="adfd8-108">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="adfd8-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="adfd8-109">Führen Sie die App mit Debugunterstützung in Visual Studio und Visual Studio Code (<kbd>F5</kbd>-Unterstützung) aus.</span><span class="sxs-lookup"><span data-stu-id="adfd8-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="adfd8-110">Führen Sie die einzelnen Schritte (<kbd>F10</kbd>) Ihres Codes aus.</span><span class="sxs-lookup"><span data-stu-id="adfd8-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="adfd8-111">Fahren Sie die Codeausführung mit <kbd>F8</kbd> in einem Browser oder mit <kbd>F5</kbd> in Visual Studio oder Visual Studio Code fort.</span><span class="sxs-lookup"><span data-stu-id="adfd8-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="adfd8-112">Beobachten Sie die Werte der lokalen Variablen in der Anzeige *Lokale Variablen*.</span><span class="sxs-lookup"><span data-stu-id="adfd8-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="adfd8-113">Betrachten Sie die Aufrufliste, einschließlich der Aufrufketten, die von JavaScript zu .NET und von .NET zu JavaScript verlaufen.</span><span class="sxs-lookup"><span data-stu-id="adfd8-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="adfd8-114">Folgendes ist derzeit *nicht möglich*:</span><span class="sxs-lookup"><span data-stu-id="adfd8-114">For now, you *can't*:</span></span>

* <span data-ttu-id="adfd8-115">Halt bei Ausnahmefehlern</span><span class="sxs-lookup"><span data-stu-id="adfd8-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="adfd8-116">Erreichen von Breakpoints beim App-Start</span><span class="sxs-lookup"><span data-stu-id="adfd8-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="adfd8-117">Die Debuggingfunktionen werden in zukünftigen Releases weiter verbessert.</span><span class="sxs-lookup"><span data-stu-id="adfd8-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="adfd8-118">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="adfd8-118">Prerequisites</span></span>

<span data-ttu-id="adfd8-119">Zum Debuggen ist einer der folgenden Browser erforderlich:</span><span class="sxs-lookup"><span data-stu-id="adfd8-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="adfd8-120">Google Chrome (Version 70 oder höher) (Standard)</span><span class="sxs-lookup"><span data-stu-id="adfd8-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="adfd8-121">Microsoft Edge (Version 80 oder höher)</span><span class="sxs-lookup"><span data-stu-id="adfd8-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="adfd8-122">Aktivieren des Debuggens für Visual Studio und Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="adfd8-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="adfd8-123">Zum Aktivieren des Debuggens für eine vorhandene Blazor WebAssembly-App müssen Sie die Datei `launchSettings.json` im Startprojekt aktualisieren, sodass sie die folgende `inspectUri`-Eigenschaft in allen Startprofilen enthält:</span><span class="sxs-lookup"><span data-stu-id="adfd8-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="adfd8-124">Nach dem Aktualisieren sollte die Datei `launchSettings.json` dem folgenden Beispiel ähneln:</span><span class="sxs-lookup"><span data-stu-id="adfd8-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="adfd8-125">Die `inspectUri`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="adfd8-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="adfd8-126">ermöglicht der IDE, zu ermitteln, ob es sich bei einer App um eine Blazor WebAssembly-App handelt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="adfd8-127">weist die Skriptdebugginginfrastruktur zum Herstellen einer Verbindung mit dem Browser über den Debugproxy von Blazor an.</span><span class="sxs-lookup"><span data-stu-id="adfd8-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="adfd8-128">Die Platzhalterwerte für das WebSocket-Protokoll (`wsProtocol`), den Host (`url.hostname`), den Port (`url.port`) und den Inspektor-URI im gestarteten Browser (`browserInspectUri`) werden vom Framework bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="adfd8-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="adfd8-129">Visual Studio</span></span>

<span data-ttu-id="adfd8-130">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="adfd8-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="adfd8-131">Erstellen Sie eine neue von ASP.NET Core gehostete Blazor WebAssembly-App.</span><span class="sxs-lookup"><span data-stu-id="adfd8-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="adfd8-132">Drücken Sie <kbd>F5</kbd>, um die App im Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="adfd8-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="adfd8-133">Das **Starten ohne Debuggen** (<kbd>STRG</kbd>+<kbd>F5</kbd>) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-133">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span>

1. <span data-ttu-id="adfd8-134">Legen Sie in `Pages/Counter.razor` einen Breakpoint in der `IncrementCount`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="adfd8-134">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="adfd8-135">Rufen Sie die Registerkarte **`Counter`** auf, und klicken Sie auf die Schaltfläche zum Erreichen des Breakpoints:</span><span class="sxs-lookup"><span data-stu-id="adfd8-135">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![„Counter.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="adfd8-137">Überprüfen Sie den Wert im `currentCount`-Feld im Fenster „Lokale Variablen“:</span><span class="sxs-lookup"><span data-stu-id="adfd8-137">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Lokale Variablen anzeigen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="adfd8-139">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="adfd8-139">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="adfd8-140">Während Sie Ihre Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:</span><span class="sxs-lookup"><span data-stu-id="adfd8-140">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="adfd8-141">Legen Sie einen Breakpoint auf der Seite `Pages/FetchData.razor` in<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> fest.</span><span class="sxs-lookup"><span data-stu-id="adfd8-141">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="adfd8-142">Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.</span><span class="sxs-lookup"><span data-stu-id="adfd8-142">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="adfd8-143">Rufen Sie die Registerkarte **`Fetch Data`** auf, um den ersten Breakpoint in der Komponente `FetchData` zu erreichen, bevor sie eine HTTP-Anforderung an den Server übermittelt:</span><span class="sxs-lookup"><span data-stu-id="adfd8-143">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![„FetchData.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="adfd8-145">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen und dann den Breakpoint in `WeatherForecastController` auf dem Server zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="adfd8-145">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Debugserver](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="adfd8-147">Drücken Sie wieder <kbd>F5</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="adfd8-147">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="adfd8-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="adfd8-148">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="adfd8-149">Debuggen von eigenständiger Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="adfd8-149">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="adfd8-150">Öffnen Sie die eigenständige Blazor WebAssembly-App in VS Code.</span><span class="sxs-lookup"><span data-stu-id="adfd8-150">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="adfd8-151">Möglicherweise erhalten Sie die folgende Benachrichtigung, dass zum Aktivieren des Debuggens ein zusätzliches Setup erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="adfd8-151">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Zusätzliches Setup erforderlich](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="adfd8-153">Wenn Sie diese Benachrichtigung erhalten, führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="adfd8-153">If you receive the notification:</span></span>

   * <span data-ttu-id="adfd8-154">Stellen Sie sicher, dass die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) installiert ist.</span><span class="sxs-lookup"><span data-stu-id="adfd8-154">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="adfd8-155">Öffnen Sie zur Überprüfung der installierten Erweiterungen in der Menüleiste die Option **Ansicht** > **Erweiterungen**, oder klicken Sie in der Seitenleiste **Aktivität** auf das Symbol **Erweiterungen**.</span><span class="sxs-lookup"><span data-stu-id="adfd8-155">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="adfd8-156">Vergewissern Sie sich, dass das JavaScript-Vorschaudebuggen aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="adfd8-156">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="adfd8-157">Öffnen Sie die Einstellungen über die Menüleiste (**Datei** > **Einstellungen** > **Einstellungen**).</span><span class="sxs-lookup"><span data-stu-id="adfd8-157">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="adfd8-158">Führen Sie mithilfe der Schlüsselwörter `debug preview` eine Suche durch.</span><span class="sxs-lookup"><span data-stu-id="adfd8-158">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="adfd8-159">Vergewissern Sie sich in den Suchergebnissen, dass das Kontrollkästchen **Debuggen > JavaScript: Vorschau verwenden** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="adfd8-159">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="adfd8-160">Wenn die Option zum Aktivieren des Vorschaudebuggens nicht vorhanden ist, führen Sie entweder ein Upgrade auf die neueste VS Code-Version durch, oder installieren Sie die [JavaScript-Debuggererweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code-Versionen 1.46 oder früher).</span><span class="sxs-lookup"><span data-stu-id="adfd8-160">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="adfd8-161">Laden Sie das Fenster neu.</span><span class="sxs-lookup"><span data-stu-id="adfd8-161">Reload the window.</span></span>

1. <span data-ttu-id="adfd8-162">Starten Sie das Debuggen mit der Tastenkombination <kbd>F5</kbd> oder dem Menü Element.</span><span class="sxs-lookup"><span data-stu-id="adfd8-162">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="adfd8-163">**Ohne Debuggen ausführen** (<kbd>STRG</kbd>+<kbd>F5</kbd>) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-163">**Run Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span>

1. <span data-ttu-id="adfd8-164">Wenn Sie dazu aufgefordert werden, wählen Sie die Option **Blazor WebAssembly debuggen**, um das Debuggen zu starten.</span><span class="sxs-lookup"><span data-stu-id="adfd8-164">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Liste der verfügbaren Debugoptionen](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="adfd8-166">Die eigenständige App wird gestartet, und es wird ein Debuggingbrowser geöffnet.</span><span class="sxs-lookup"><span data-stu-id="adfd8-166">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="adfd8-167">Legen Sie einen Breakpoint in der `IncrementCount`-Methode der `Counter`-Komponente fest, und klicken Sie dann auf die Schaltfläche, um den Breakpoint zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="adfd8-167">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Counter.razor in VS Code debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="adfd8-169">Debuggen der gehosteten Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="adfd8-169">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="adfd8-170">Öffnen Sie den Projektmappenordner der gehosteten Blazor WebAssembly-App in VS Code.</span><span class="sxs-lookup"><span data-stu-id="adfd8-170">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="adfd8-171">Wenn für das Projekt keine Startkonfiguration eingestellt ist, wird folgende Benachrichtigung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-171">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="adfd8-172">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="adfd8-172">Select **Yes**.</span></span>

   ![Erforderliche Ressourcen hinzufügen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="adfd8-174">Wählen Sie in der Befehlspalette oben im Fenster das Projekt *Server* in der gehosteten Lösung aus.</span><span class="sxs-lookup"><span data-stu-id="adfd8-174">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="adfd8-175">Zum Starten des Debuggers wird eine `launch.json`-Datei mit der Startkonfiguration generiert.</span><span class="sxs-lookup"><span data-stu-id="adfd8-175">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="adfd8-176">Anfügen an eine vorhandene Debugsitzung</span><span class="sxs-lookup"><span data-stu-id="adfd8-176">Attach to an existing debugging session</span></span>

<span data-ttu-id="adfd8-177">Um an eine laufende Blazor-App anzufügen, erstellen Sie eine `launch.json`-Datei mit der folgenden Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="adfd8-177">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="adfd8-178">Das Anfügen an eine Debugsitzung wird nur für eigenständige Apps unterstützt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-178">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="adfd8-179">Um das Full-Stack-Debuggen zu verwenden, muss die App über VS Code gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="adfd8-179">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="adfd8-180">Optionen für die Startkonfiguration</span><span class="sxs-lookup"><span data-stu-id="adfd8-180">Launch configuration options</span></span>

<span data-ttu-id="adfd8-181">Für den Debugtyp `blazorwasm` (`.vscode/launch.json`) werden die folgenden Optionen für die Startkonfiguration unterstützt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-181">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="adfd8-182">Option</span><span class="sxs-lookup"><span data-stu-id="adfd8-182">Option</span></span>    | <span data-ttu-id="adfd8-183">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="adfd8-183">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="adfd8-184">Verwenden Sie `launch`, um eine Debugsitzung zu starten und einer Blazor WebAssembly-App anzufügen oder `attach`, um eine Debugsitzung einer bereits laufenden App anzufügen.</span><span class="sxs-lookup"><span data-stu-id="adfd8-184">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="adfd8-185">Die URL, die beim Debuggen im Browser geöffnet werden soll.</span><span class="sxs-lookup"><span data-stu-id="adfd8-185">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="adfd8-186">Wird standardmäßig auf `https://localhost:5001` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-186">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="adfd8-187">Der Browser, der für die Debugsitzung gestartet werden muss.</span><span class="sxs-lookup"><span data-stu-id="adfd8-187">The browser to launch for the debugging session.</span></span> <span data-ttu-id="adfd8-188">Wird auf `edge` oder `chrome` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-188">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="adfd8-189">Wird standardmäßig auf `chrome` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-189">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="adfd8-190">Wird zum Generieren von Protokollen vom JS-Debugger verwendet.</span><span class="sxs-lookup"><span data-stu-id="adfd8-190">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="adfd8-191">Zum Generieren von Protokollen auf `true` festlegen.</span><span class="sxs-lookup"><span data-stu-id="adfd8-191">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="adfd8-192">Muss auf `true` gesetzt werden, wenn eine gehostete Blazor WebAssembly-App gestartet und debuggt werden soll.</span><span class="sxs-lookup"><span data-stu-id="adfd8-192">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="adfd8-193">Gibt den absoluten Pfad des Webservers an.</span><span class="sxs-lookup"><span data-stu-id="adfd8-193">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="adfd8-194">Muss gesetzt werden, wenn eine App von einer Unterroute bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="adfd8-194">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="adfd8-195">Die Anzahl der Millisekunden, die auf das Anfügen der Debugsitzung gewartet werden soll.</span><span class="sxs-lookup"><span data-stu-id="adfd8-195">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="adfd8-196">Wird standardmäßig auf 30.000 Millisekunden (30 Sekunden) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="adfd8-196">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="adfd8-197">Ein Verweis auf die ausführbare Datei zur Ausführung des Servers der gehosteten App.</span><span class="sxs-lookup"><span data-stu-id="adfd8-197">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="adfd8-198">Muss festgelegt werden, wenn `hosted` den Wert `true` hat.</span><span class="sxs-lookup"><span data-stu-id="adfd8-198">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="adfd8-199">Das Arbeitsverzeichnis, unter dem die App gestartet werden soll.</span><span class="sxs-lookup"><span data-stu-id="adfd8-199">The working directory to launch the app under.</span></span> <span data-ttu-id="adfd8-200">Muss festgelegt werden, wenn `hosted` den Wert `true` hat.</span><span class="sxs-lookup"><span data-stu-id="adfd8-200">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="adfd8-201">Die Umgebungsvariable, die für den gestarteten Prozess bereitgestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="adfd8-201">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="adfd8-202">Trifft nur zu, wenn `hosted` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="adfd8-202">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="adfd8-203">Beispiel für Startkonfigurationen</span><span class="sxs-lookup"><span data-stu-id="adfd8-203">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="adfd8-204">Starten und Debuggen einer eigenständigen Blazor WebAssembly-App</span><span class="sxs-lookup"><span data-stu-id="adfd8-204">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="adfd8-205">Anfügen an eine laufende App unter einer angegebenen URL</span><span class="sxs-lookup"><span data-stu-id="adfd8-205">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="adfd8-206">Starten und Debuggen einer gehosteten Blazor WebAssembly-App mit Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="adfd8-206">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="adfd8-207">Die Browserkonfiguration verwendet standardmäßig Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="adfd8-207">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="adfd8-208">Wenn Sie Microsoft Edge zum Debuggen verwenden möchten, legen Sie `browser` auf `edge` fest.</span><span class="sxs-lookup"><span data-stu-id="adfd8-208">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="adfd8-209">Um Google Chrome zu verwenden, legen Sie die Option `browser` nicht fest, oder legen Sie den Optionswert auf `chrome` fest.</span><span class="sxs-lookup"><span data-stu-id="adfd8-209">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="adfd8-210">Im vorherigen Beispiel ist `MyHostedApp.Server.dll` die Assembly der *Server*-App.</span><span class="sxs-lookup"><span data-stu-id="adfd8-210">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="adfd8-211">Der Ordner `.vscode` befindet sich im Projektmappenordner neben den Ordnern `Client`, `Server` und `Shared`.</span><span class="sxs-lookup"><span data-stu-id="adfd8-211">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="adfd8-212">Debuggen im Browser</span><span class="sxs-lookup"><span data-stu-id="adfd8-212">Debug in the browser</span></span>

1. <span data-ttu-id="adfd8-213">Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.</span><span class="sxs-lookup"><span data-stu-id="adfd8-213">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="adfd8-214">Starten Sie einen Browser, und navigieren Sie zur URL der App (z. B. `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="adfd8-214">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="adfd8-215">Versuchen Sie, das Remotedebuggen im Browser zu starten, indem Sie <kbd>UMSCHALTTASTE</kbd>+<kbd>ALT-TASTE</kbd>+<kbd>D</kbd> drücken.</span><span class="sxs-lookup"><span data-stu-id="adfd8-215">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="adfd8-216">Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden, was nicht das Standardverhalten ist.</span><span class="sxs-lookup"><span data-stu-id="adfd8-216">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="adfd8-217">Wenn das Remotedebuggen deaktiviert ist, wird eine Seite mit der Meldung **Debugfähige Browserregisterkarte nicht gefunden** gerendert, die Anweisungen zum Starten des Browsers mit geöffnetem Debuggingport enthält.</span><span class="sxs-lookup"><span data-stu-id="adfd8-217">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="adfd8-218">Befolgen Sie die für Ihren Browser geltenden Anweisungen. Dadurch wird ein neues Browserfenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="adfd8-218">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="adfd8-219">Schließen Sie das vorherige Browserfenster.</span><span class="sxs-lookup"><span data-stu-id="adfd8-219">Close the previous browser window.</span></span>

1. <span data-ttu-id="adfd8-220">Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnet die Tastenkombination zum Debuggen (<kbd>UMSCHALTTASTE</kbd>+<kbd>ALT-TASTE</kbd>+<kbd>D</kbd>) eine neue Debuggerregisterkarte.</span><span class="sxs-lookup"><span data-stu-id="adfd8-220">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="adfd8-221">Nach einem kurzen Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys der App im Knoten `file://` an.</span><span class="sxs-lookup"><span data-stu-id="adfd8-221">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="adfd8-222">In Komponentencode (`.razor`-Dateien) und C#-Codedateien (`.cs`) werden bei Codeausführung von Ihnen festgelegte Breakpoints erreicht.</span><span class="sxs-lookup"><span data-stu-id="adfd8-222">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="adfd8-223">Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="adfd8-223">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="adfd8-224">Blazor bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert.</span><span class="sxs-lookup"><span data-stu-id="adfd8-224">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="adfd8-225">Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy.</span><span class="sxs-lookup"><span data-stu-id="adfd8-225">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="adfd8-226">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).</span><span class="sxs-lookup"><span data-stu-id="adfd8-226">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="adfd8-227">Browserquellzuordnungen</span><span class="sxs-lookup"><span data-stu-id="adfd8-227">Browser source maps</span></span>

<span data-ttu-id="adfd8-228">Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="adfd8-228">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="adfd8-229">Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu.</span><span class="sxs-lookup"><span data-stu-id="adfd8-229">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="adfd8-230">Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="adfd8-230">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="adfd8-231">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="adfd8-231">Troubleshoot</span></span>

<span data-ttu-id="adfd8-232">Wenn Sie auf Fehler stoßen, könnten die folgenden Tipps helfen:</span><span class="sxs-lookup"><span data-stu-id="adfd8-232">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="adfd8-233">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="adfd8-233">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="adfd8-234">Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="adfd8-234">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="adfd8-235">Vergewissern Sie sich, dass Sie das ASP.NET Core-HTTPS-Entwicklungszertifikat installiert haben und diesem vertrauen.</span><span class="sxs-lookup"><span data-stu-id="adfd8-235">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="adfd8-236">Weitere Informationen finden Sie unter <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="adfd8-236">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="adfd8-237">Visual Studio erfordert die Option **JavaScript-Debugging für ASP.NET aktivieren (Chrome, Microsoft Edge und IE)** in **Tools** > **Optionen** > **Debuggen** > **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="adfd8-237">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="adfd8-238">Dies ist die Standardeinstellung für Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="adfd8-238">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="adfd8-239">Wenn das Debuggen nicht funktioniert, vergewissern Sie sich, dass die Option ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="adfd8-239">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="adfd8-240">In `OnInitialized{Async}` nicht verwendete Breakpoints</span><span class="sxs-lookup"><span data-stu-id="adfd8-240">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="adfd8-241">Der Debugproxy des Blazor-Frameworks benötigt eine kurze Zeit zum Starten, sodass Breakpoints in der [`OnInitialized{Async}`-Lebenszyklusmethode](xref:blazor/components/lifecycle#component-initialization-methods) möglicherweise nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="adfd8-241">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="adfd8-242">Es wird empfohlen, am Anfang des Methodentextes eine Verzögerung hinzuzufügen, um dem Debugproxy etwas Zeit zum Starten zu geben, bevor der Breakpoint erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="adfd8-242">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="adfd8-243">Sie können die Verzögerung auf Grundlage einer [`if`-Compileranweisung einschließen](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if), um sicherzustellen, dass die Verzögerung für einen Releasebuild der App nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="adfd8-243">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="adfd8-244"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="adfd8-244"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="adfd8-245"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="adfd8-245"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

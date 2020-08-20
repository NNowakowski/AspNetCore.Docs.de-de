---
title: Verwenden von Hostingstartassemblys in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.NET Core-App aus einer externen Assembly mithilfe einer Implementierung von IHostingStartup erweitern.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: fadd93cf28603653e20ed6c7dceadcabf0dfb9a5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627519"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="e2f97-103">Verwenden von Hostingstartassemblys in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2f97-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="e2f97-104">Von [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="e2f97-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2f97-105">Mit einer <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assembly aus vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="e2f97-106">Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="e2f97-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2f97-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="e2f97-108">Das Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="e2f97-108">HostingStartup attribute</span></span>

<span data-ttu-id="e2f97-109">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.</span><span class="sxs-lookup"><span data-stu-id="e2f97-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="e2f97-110">Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält.</span><span class="sxs-lookup"><span data-stu-id="e2f97-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e2f97-111">Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="e2f97-112">Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="e2f97-113">Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="e2f97-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="e2f97-114">`StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:</span><span class="sxs-lookup"><span data-stu-id="e2f97-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e2f97-115">Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.</span><span class="sxs-lookup"><span data-stu-id="e2f97-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="e2f97-116">Ermitteln geladener Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="e2f97-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="e2f97-117">Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App.</span><span class="sxs-lookup"><span data-stu-id="e2f97-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="e2f97-118">Fehler, die beim Laden von Assemblys auftreten, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="e2f97-119">Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="e2f97-120">Deaktivieren des automatischen Ladens von Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="e2f97-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="e2f97-121">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="e2f97-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="e2f97-122">Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="e2f97-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="e2f97-123">Hostkonfigurationseinstellung „Verhindern des Hostingstarts“:</span><span class="sxs-lookup"><span data-stu-id="e2f97-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="e2f97-124">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e2f97-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="e2f97-125">Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="e2f97-126">Hostkonfigurationseinstellung „Hostingstartausschlussassemblys“:</span><span class="sxs-lookup"><span data-stu-id="e2f97-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="e2f97-127">Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e2f97-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="e2f97-128">Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="e2f97-129">Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="e2f97-130">Projekt</span><span class="sxs-lookup"><span data-stu-id="e2f97-130">Project</span></span>

<span data-ttu-id="e2f97-131">Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="e2f97-132">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="e2f97-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="e2f97-133">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="e2f97-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="e2f97-134">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="e2f97-134">Class library</span></span>

<span data-ttu-id="e2f97-135">In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="e2f97-136">Die Bibliothek enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="e2f97-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e2f97-137">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="e2f97-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="e2f97-138">Die Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="e2f97-138">The class library:</span></span>

* <span data-ttu-id="e2f97-139">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e2f97-140">`ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2f97-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="e2f97-141">Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="e2f97-142">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der `ServiceKeyInjection`-Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="e2f97-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2f97-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e2f97-144">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="e2f97-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="e2f97-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2f97-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="e2f97-146">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="e2f97-147">Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf.</span><span class="sxs-lookup"><span data-stu-id="e2f97-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="e2f97-148">Das Paket:</span><span class="sxs-lookup"><span data-stu-id="e2f97-148">The package:</span></span>

* <span data-ttu-id="e2f97-149">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e2f97-150">`ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2f97-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="e2f97-151">Enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="e2f97-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e2f97-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2f97-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e2f97-153">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="e2f97-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="e2f97-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2f97-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="e2f97-155">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="e2f97-155">Console app without an entry point</span></span>

<span data-ttu-id="e2f97-156">*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="e2f97-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="e2f97-157">Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="e2f97-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="e2f97-158">Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="e2f97-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="e2f97-159">In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:</span><span class="sxs-lookup"><span data-stu-id="e2f97-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="e2f97-160">Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="e2f97-161">Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="e2f97-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="e2f97-162">Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="e2f97-163">Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e2f97-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="e2f97-164">Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="e2f97-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="e2f97-165">Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="e2f97-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="e2f97-166">Sie enthält ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.</span><span class="sxs-lookup"><span data-stu-id="e2f97-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="e2f97-167">Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="e2f97-168">Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="e2f97-169">Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="e2f97-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="e2f97-170">Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="e2f97-171">Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="e2f97-172">Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="e2f97-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="e2f97-173">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut identifiziert eine Klasse bei der Erstellung von <xref:Microsoft.AspNetCore.Hosting.IWebHost> als eine Implementierung von `IHostingStartup` zum Laden und Ausführen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="e2f97-174">Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="e2f97-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e2f97-175">Eine Klasse implementiert `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e2f97-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="e2f97-176">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="e2f97-177">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="e2f97-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="e2f97-178">Die Datei für die Abhängigkeiten ( *.deps.json*) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin*-Ordner fest:</span><span class="sxs-lookup"><span data-stu-id="e2f97-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="e2f97-179">Nur ein Teil der Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-179">Only part of the file is shown.</span></span> <span data-ttu-id="e2f97-180">`StartupEnhancement` ist der Name der Assembly im Beispiel.</span><span class="sxs-lookup"><span data-stu-id="e2f97-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="e2f97-181">Beim Hostingstart bereitgestellte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="e2f97-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="e2f97-182">Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:</span><span class="sxs-lookup"><span data-stu-id="e2f97-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="e2f97-183">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e2f97-184">Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="e2f97-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="e2f97-185">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e2f97-186">Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="e2f97-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="e2f97-187">Angeben der Hostingstartassembly</span><span class="sxs-lookup"><span data-stu-id="e2f97-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="e2f97-188">Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an.</span><span class="sxs-lookup"><span data-stu-id="e2f97-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="e2f97-189">Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="e2f97-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="e2f97-190">Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht.</span><span class="sxs-lookup"><span data-stu-id="e2f97-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e2f97-191">Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="e2f97-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="e2f97-192">Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung „Hostingstartassemblys“ festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="e2f97-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="e2f97-193">Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="e2f97-194">Aktivierung</span><span class="sxs-lookup"><span data-stu-id="e2f97-194">Activation</span></span>

<span data-ttu-id="e2f97-195">Optionen für die Hostingstartaktivierung:</span><span class="sxs-lookup"><span data-stu-id="e2f97-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="e2f97-196">[Laufzeitspeicher:](#runtime-store) Für die Aktivierung ist kein Kompilierzeitverweis erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e2f97-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="e2f97-197">Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="e2f97-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="e2f97-198">Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="e2f97-199">Kompilierzeitverweis für die Aktivierung erforderlich</span><span class="sxs-lookup"><span data-stu-id="e2f97-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="e2f97-200">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="e2f97-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="e2f97-201">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="e2f97-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="e2f97-202">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="e2f97-202">Runtime store</span></span>

<span data-ttu-id="e2f97-203">Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="e2f97-204">Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="e2f97-205">Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="e2f97-206">In der Beispiel-App (Projekt *RuntimeStore*) wird der folgende Befehl verwendet:</span><span class="sxs-lookup"><span data-stu-id="e2f97-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="e2f97-207">Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="e2f97-208">**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**</span><span class="sxs-lookup"><span data-stu-id="e2f97-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="e2f97-209">Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an.</span><span class="sxs-lookup"><span data-stu-id="e2f97-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="e2f97-210">`additionalDeps` ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e2f97-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="e2f97-211">Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *.deps.json*-Dateien, die beim Start mit der App-eigenen *.deps.json*-Datei zusammengeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="e2f97-212">Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann</span><span class="sxs-lookup"><span data-stu-id="e2f97-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="e2f97-213">Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="e2f97-214">Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="e2f97-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="e2f97-215">Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *.deps.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="e2f97-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="e2f97-216">Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:</span><span class="sxs-lookup"><span data-stu-id="e2f97-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="e2f97-217">Legen Sie die *.deps.json*-Datei an folgendem Speicherort ab:</span><span class="sxs-lookup"><span data-stu-id="e2f97-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="e2f97-218">`{ADDITIONAL DEPENDENCIES PATH}`: Der Speicherort wurde zur Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="e2f97-219">`{SHARED FRAMEWORK NAME}`: Dies ist das freigegebene Framework, das für diese zusätzliche Abhängigkeitsdatei erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e2f97-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="e2f97-220">`{SHARED FRAMEWORK VERSION}`: Dies ist die Mindestversion des freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="e2f97-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="e2f97-221">`{ENHANCEMENT ASSEMBLY NAME}`: Dies ist der Name der Erweiterungsassembly.</span><span class="sxs-lookup"><span data-stu-id="e2f97-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="e2f97-222">In der Beispiel-App (Projekt *RuntimeStore*) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:</span><span class="sxs-lookup"><span data-stu-id="e2f97-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="e2f97-223">Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="e2f97-224">In der Beispiel-App (Projekt *RuntimeStore*) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="e2f97-225">Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e2f97-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e2f97-226">**Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="e2f97-226">**Deployment**</span></span>

<span data-ttu-id="e2f97-227">Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment*-Ordner, der Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="e2f97-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="e2f97-228">Der Laufzeitspeicher des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="e2f97-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="e2f97-229">Die Abhängigkeitendatei des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="e2f97-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="e2f97-230">Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="e2f97-231">Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="e2f97-232">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="e2f97-232">NuGet package</span></span>

<span data-ttu-id="e2f97-233">In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="e2f97-234">Das Paket enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="e2f97-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="e2f97-235">Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="e2f97-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="e2f97-236">Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="e2f97-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="e2f97-237">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e2f97-238">Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.</span><span class="sxs-lookup"><span data-stu-id="e2f97-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="e2f97-239">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="e2f97-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="e2f97-240">Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="e2f97-241">So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools</span><span class="sxs-lookup"><span data-stu-id="e2f97-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="e2f97-242">Veröffentlichen von Paketen</span><span class="sxs-lookup"><span data-stu-id="e2f97-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="e2f97-243">Laufzeitpaketspeicher</span><span class="sxs-lookup"><span data-stu-id="e2f97-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="e2f97-244">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="e2f97-244">Project bin folder</span></span>

<span data-ttu-id="e2f97-245">Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="e2f97-246">Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="e2f97-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="e2f97-247">Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="e2f97-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="e2f97-248">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e2f97-249">Dieser Ansatz gilt, wenn das Bereitstellungsszenario erfordert, dass ein Kompilierzeitverweis auf die Assembly des Hostingstarts (*DLL*-Datei) erstellt und die Assembly in eine der beiden Dateien verschoben wird:</span><span class="sxs-lookup"><span data-stu-id="e2f97-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="e2f97-250">Das verbrauchende Projekt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-250">The consuming project.</span></span>
  * <span data-ttu-id="e2f97-251">Einen Speicherort, auf den das verbrauchende Projekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="e2f97-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="e2f97-252">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="e2f97-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="e2f97-253">Wenn das Ziel .NET Framework ist, kann die Assembly in den Standardladekontext geladen werden, was bei .NET Framework bedeutet, dass sich die Assembly an einem der folgenden Speicherorte befindet:</span><span class="sxs-lookup"><span data-stu-id="e2f97-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="e2f97-254">Anwendungsbasispfad: Dabei handelt es sich um den Ordner *bin*, in dem die ausführbare Datei ( *.exe*) der App gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="e2f97-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="e2f97-255">Globaler Assemblycache (GAC): Der GAC speichert Assemblys, die mehrere .NET Framework-Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="e2f97-256">Weitere Informationen finden Sie unter [Vorgehensweise: Installieren einer Assembly in den globalen Assemblycache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in der .NET Framework-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="e2f97-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e2f97-257">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="e2f97-257">Sample code</span></span>

<span data-ttu-id="e2f97-258">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:</span><span class="sxs-lookup"><span data-stu-id="e2f97-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="e2f97-259">Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:</span><span class="sxs-lookup"><span data-stu-id="e2f97-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="e2f97-260">NuGet-Paket (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="e2f97-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="e2f97-261">Klassenbibliothek (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="e2f97-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="e2f97-262">Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly (*StartupDiagnostics*) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="e2f97-263">Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="e2f97-264">Registrierte Dienste</span><span class="sxs-lookup"><span data-stu-id="e2f97-264">Registered services</span></span>
  * <span data-ttu-id="e2f97-265">Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="e2f97-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="e2f97-266">Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)</span><span class="sxs-lookup"><span data-stu-id="e2f97-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="e2f97-267">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="e2f97-267">Request headers</span></span>
  * <span data-ttu-id="e2f97-268">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="e2f97-268">Environment variables</span></span>

<span data-ttu-id="e2f97-269">So führen Sie das Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="e2f97-269">To run the sample:</span></span>

<span data-ttu-id="e2f97-270">**Aktivierung über ein NuGet-Paket**</span><span class="sxs-lookup"><span data-stu-id="e2f97-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="e2f97-271">Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="e2f97-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="e2f97-272">Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2f97-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e2f97-273">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-273">Compile and run the app.</span></span> <span data-ttu-id="e2f97-274">In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="e2f97-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="e2f97-275">Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts ( *../HostingStartupPackage/bin/Debug*) als Paketquelle an.</span><span class="sxs-lookup"><span data-stu-id="e2f97-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="e2f97-276">Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="e2f97-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e2f97-277">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="e2f97-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e2f97-278">Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache.</span><span class="sxs-lookup"><span data-stu-id="e2f97-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="e2f97-279">Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e2f97-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="e2f97-280">**Aktivierung über eine Klassenbibliothek**</span><span class="sxs-lookup"><span data-stu-id="e2f97-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="e2f97-281">Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="e2f97-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="e2f97-282">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2f97-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e2f97-283">Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.</span><span class="sxs-lookup"><span data-stu-id="e2f97-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="e2f97-284">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-284">Compile and run the app.</span></span> <span data-ttu-id="e2f97-285">Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) – Kompilierzeitverweis.</span><span class="sxs-lookup"><span data-stu-id="e2f97-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="e2f97-286">Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="e2f97-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="e2f97-287">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="e2f97-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e2f97-288">**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**</span><span class="sxs-lookup"><span data-stu-id="e2f97-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="e2f97-289">Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="e2f97-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="e2f97-290">PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="e2f97-291">Im Artikel [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell) erfahren Sie, wie Sie PowerShell auf anderen Plattformen erwerben können.</span><span class="sxs-lookup"><span data-stu-id="e2f97-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="e2f97-292">Führen Sie das Skript *build.ps1* im Ordner *RuntimeStore* aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="e2f97-293">Das Skript:</span><span class="sxs-lookup"><span data-stu-id="e2f97-293">The script:</span></span>
   * <span data-ttu-id="e2f97-294">Generiert das `StartupDiagnostics`-Paket im Ordner *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="e2f97-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="e2f97-295">Generiert den Laufzeitspeicher für `StartupDiagnostics` im Ordner *store*.</span><span class="sxs-lookup"><span data-stu-id="e2f97-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="e2f97-296">Der `dotnet store`-Befehl im Skript verwendet den [Laufzeitbezeichner (RID)](/dotnet/core/rid-catalog) `win7-x64` für einen für Windows bereitgestellten Hostingstart.</span><span class="sxs-lookup"><span data-stu-id="e2f97-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="e2f97-297">Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss in Zeile 37 die entsprechende RID eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="e2f97-298">Der Runtimespeicher für `StartupDiagnostics` würde später in den Runtimespeicher des Benutzers oder Systems auf dem Computer verschoben, auf dem die Assembly verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e2f97-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="e2f97-299">Der Installationsspeicherort für den Benutzerruntimespeicher für die `StartupDiagnostics`-Assembly ist *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="e2f97-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="e2f97-300">Generiert die `additionalDeps` für `StartupDiagnostics` im Ordner *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="e2f97-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="e2f97-301">Die zusätzlichen Abhängigkeiten würden später in die zusätzlichen Abhängigkeiten des Benutzers oder Systems verschoben.</span><span class="sxs-lookup"><span data-stu-id="e2f97-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="e2f97-302">Der Installationsspeicherort der zusätzlichen Abhängigkeiten des Benutzers `StartupDiagnostics` ist *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="e2f97-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="e2f97-303">Legt die Datei *deploy.ps1* im Ordner *deployment* ab.</span><span class="sxs-lookup"><span data-stu-id="e2f97-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="e2f97-304">Führen Sie das Skript *deploy.ps1* im Ordner *deployment* aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="e2f97-305">Das Skript fügt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e2f97-305">The script appends:</span></span>
   * <span data-ttu-id="e2f97-306">`StartupDiagnostics` an die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e2f97-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="e2f97-307">Der Pfad der Hostingstartabhängigkeiten (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_ADDITIONAL_DEPS`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="e2f97-308">Der Pfad des Runtimespeichers (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_SHARED_STORE`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="e2f97-309">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-309">Run the sample app.</span></span>
1. <span data-ttu-id="e2f97-310">Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="e2f97-311">Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e2f97-312">Mit einer <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assembly aus vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="e2f97-313">Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="e2f97-314">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2f97-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="e2f97-315">Das Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="e2f97-315">HostingStartup attribute</span></span>

<span data-ttu-id="e2f97-316">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.</span><span class="sxs-lookup"><span data-stu-id="e2f97-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="e2f97-317">Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält.</span><span class="sxs-lookup"><span data-stu-id="e2f97-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e2f97-318">Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="e2f97-319">Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="e2f97-320">Weitere Informationen finden Sie unter [Webhost: Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies)[Webhost: Auszuschließende Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="e2f97-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="e2f97-321">Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="e2f97-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="e2f97-322">`StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:</span><span class="sxs-lookup"><span data-stu-id="e2f97-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e2f97-323">Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.</span><span class="sxs-lookup"><span data-stu-id="e2f97-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="e2f97-324">Ermitteln geladener Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="e2f97-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="e2f97-325">Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App.</span><span class="sxs-lookup"><span data-stu-id="e2f97-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="e2f97-326">Fehler, die beim Laden von Assemblys auftreten, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="e2f97-327">Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="e2f97-328">Deaktivieren des automatischen Ladens von Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="e2f97-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="e2f97-329">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="e2f97-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="e2f97-330">Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="e2f97-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="e2f97-331">Hostkonfigurationseinstellung [Verhindern des Hostingstarts](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="e2f97-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="e2f97-332">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e2f97-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="e2f97-333">Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="e2f97-334">Hostkonfigurationseinstellung [Hostingstartausschlussassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="e2f97-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="e2f97-335">Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e2f97-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="e2f97-336">Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="e2f97-337">Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="e2f97-338">Projekt</span><span class="sxs-lookup"><span data-stu-id="e2f97-338">Project</span></span>

<span data-ttu-id="e2f97-339">Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="e2f97-340">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="e2f97-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="e2f97-341">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="e2f97-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="e2f97-342">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="e2f97-342">Class library</span></span>

<span data-ttu-id="e2f97-343">In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="e2f97-344">Die Bibliothek enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="e2f97-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e2f97-345">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="e2f97-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="e2f97-346">Die Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="e2f97-346">The class library:</span></span>

* <span data-ttu-id="e2f97-347">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e2f97-348">`ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2f97-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="e2f97-349">Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="e2f97-350">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der `ServiceKeyInjection`-Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="e2f97-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2f97-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e2f97-352">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="e2f97-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="e2f97-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2f97-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="e2f97-354">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="e2f97-355">Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf.</span><span class="sxs-lookup"><span data-stu-id="e2f97-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="e2f97-356">Das Paket:</span><span class="sxs-lookup"><span data-stu-id="e2f97-356">The package:</span></span>

* <span data-ttu-id="e2f97-357">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e2f97-358">`ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2f97-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="e2f97-359">Enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="e2f97-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e2f97-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2f97-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e2f97-361">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="e2f97-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="e2f97-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2f97-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="e2f97-363">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="e2f97-363">Console app without an entry point</span></span>

<span data-ttu-id="e2f97-364">*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="e2f97-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="e2f97-365">Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="e2f97-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="e2f97-366">Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="e2f97-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="e2f97-367">In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:</span><span class="sxs-lookup"><span data-stu-id="e2f97-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="e2f97-368">Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="e2f97-369">Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="e2f97-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="e2f97-370">Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="e2f97-371">Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e2f97-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="e2f97-372">Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="e2f97-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="e2f97-373">Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="e2f97-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="e2f97-374">Sie enthält ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.</span><span class="sxs-lookup"><span data-stu-id="e2f97-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="e2f97-375">Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="e2f97-376">Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="e2f97-377">Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="e2f97-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="e2f97-378">Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="e2f97-379">Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="e2f97-380">Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="e2f97-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="e2f97-381">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut identifiziert eine Klasse bei der Erstellung von <xref:Microsoft.AspNetCore.Hosting.IWebHost> als eine Implementierung von `IHostingStartup` zum Laden und Ausführen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="e2f97-382">Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="e2f97-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e2f97-383">Eine Klasse implementiert `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e2f97-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="e2f97-384">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="e2f97-385">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="e2f97-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="e2f97-386">Die Datei für die Abhängigkeiten ( *.deps.json*) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin*-Ordner fest:</span><span class="sxs-lookup"><span data-stu-id="e2f97-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="e2f97-387">Nur ein Teil der Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-387">Only part of the file is shown.</span></span> <span data-ttu-id="e2f97-388">`StartupEnhancement` ist der Name der Assembly im Beispiel.</span><span class="sxs-lookup"><span data-stu-id="e2f97-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="e2f97-389">Beim Hostingstart bereitgestellte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="e2f97-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="e2f97-390">Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:</span><span class="sxs-lookup"><span data-stu-id="e2f97-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="e2f97-391">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e2f97-392">Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="e2f97-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="e2f97-393">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e2f97-394">Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="e2f97-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="e2f97-395">Angeben der Hostingstartassembly</span><span class="sxs-lookup"><span data-stu-id="e2f97-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="e2f97-396">Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an.</span><span class="sxs-lookup"><span data-stu-id="e2f97-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="e2f97-397">Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="e2f97-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="e2f97-398">Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht.</span><span class="sxs-lookup"><span data-stu-id="e2f97-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e2f97-399">Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="e2f97-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="e2f97-400">Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung [Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="e2f97-401">Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="e2f97-402">Aktivierung</span><span class="sxs-lookup"><span data-stu-id="e2f97-402">Activation</span></span>

<span data-ttu-id="e2f97-403">Optionen für die Hostingstartaktivierung:</span><span class="sxs-lookup"><span data-stu-id="e2f97-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="e2f97-404">[Laufzeitspeicher:](#runtime-store) Für die Aktivierung ist kein Kompilierzeitverweis erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e2f97-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="e2f97-405">Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="e2f97-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="e2f97-406">Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="e2f97-407">Kompilierzeitverweis für die Aktivierung erforderlich</span><span class="sxs-lookup"><span data-stu-id="e2f97-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="e2f97-408">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="e2f97-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="e2f97-409">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="e2f97-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="e2f97-410">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="e2f97-410">Runtime store</span></span>

<span data-ttu-id="e2f97-411">Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="e2f97-412">Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="e2f97-413">Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="e2f97-414">In der Beispiel-App (Projekt *RuntimeStore*) wird der folgende Befehl verwendet:</span><span class="sxs-lookup"><span data-stu-id="e2f97-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="e2f97-415">Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="e2f97-416">**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**</span><span class="sxs-lookup"><span data-stu-id="e2f97-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="e2f97-417">Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an.</span><span class="sxs-lookup"><span data-stu-id="e2f97-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="e2f97-418">`additionalDeps` ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e2f97-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="e2f97-419">Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *.deps.json*-Dateien, die beim Start mit der App-eigenen *.deps.json*-Datei zusammengeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="e2f97-420">Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann</span><span class="sxs-lookup"><span data-stu-id="e2f97-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="e2f97-421">Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="e2f97-422">Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="e2f97-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="e2f97-423">Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *.deps.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="e2f97-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="e2f97-424">Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:</span><span class="sxs-lookup"><span data-stu-id="e2f97-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="e2f97-425">Legen Sie die *.deps.json*-Datei an folgendem Speicherort ab:</span><span class="sxs-lookup"><span data-stu-id="e2f97-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="e2f97-426">`{ADDITIONAL DEPENDENCIES PATH}`: Der Speicherort wurde zur Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="e2f97-427">`{SHARED FRAMEWORK NAME}`: Dies ist das freigegebene Framework, das für diese zusätzliche Abhängigkeitsdatei erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e2f97-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="e2f97-428">`{SHARED FRAMEWORK VERSION}`: Dies ist die Mindestversion des freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="e2f97-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="e2f97-429">`{ENHANCEMENT ASSEMBLY NAME}`: Dies ist der Name der Erweiterungsassembly.</span><span class="sxs-lookup"><span data-stu-id="e2f97-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="e2f97-430">In der Beispiel-App (Projekt *RuntimeStore*) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:</span><span class="sxs-lookup"><span data-stu-id="e2f97-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="e2f97-431">Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="e2f97-432">In der Beispiel-App (Projekt *RuntimeStore*) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="e2f97-433">Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e2f97-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e2f97-434">**Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="e2f97-434">**Deployment**</span></span>

<span data-ttu-id="e2f97-435">Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment*-Ordner, der Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="e2f97-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="e2f97-436">Der Laufzeitspeicher des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="e2f97-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="e2f97-437">Die Abhängigkeitendatei des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="e2f97-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="e2f97-438">Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="e2f97-439">Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="e2f97-440">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="e2f97-440">NuGet package</span></span>

<span data-ttu-id="e2f97-441">In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="e2f97-442">Das Paket enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="e2f97-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="e2f97-443">Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="e2f97-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="e2f97-444">Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="e2f97-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="e2f97-445">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e2f97-446">Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.</span><span class="sxs-lookup"><span data-stu-id="e2f97-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="e2f97-447">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="e2f97-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="e2f97-448">Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="e2f97-449">So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools</span><span class="sxs-lookup"><span data-stu-id="e2f97-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="e2f97-450">Veröffentlichen von Paketen</span><span class="sxs-lookup"><span data-stu-id="e2f97-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="e2f97-451">Laufzeitpaketspeicher</span><span class="sxs-lookup"><span data-stu-id="e2f97-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="e2f97-452">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="e2f97-452">Project bin folder</span></span>

<span data-ttu-id="e2f97-453">Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="e2f97-454">Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="e2f97-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="e2f97-455">Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="e2f97-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="e2f97-456">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e2f97-457">Dieser Ansatz gilt, wenn das Bereitstellungsszenario erfordert, dass ein Kompilierzeitverweis auf die Assembly des Hostingstarts (*DLL*-Datei) erstellt und die Assembly in eine der beiden Dateien verschoben wird:</span><span class="sxs-lookup"><span data-stu-id="e2f97-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="e2f97-458">Das verbrauchende Projekt.</span><span class="sxs-lookup"><span data-stu-id="e2f97-458">The consuming project.</span></span>
  * <span data-ttu-id="e2f97-459">Einen Speicherort, auf den das verbrauchende Projekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="e2f97-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="e2f97-460">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="e2f97-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="e2f97-461">Wenn das Ziel .NET Framework ist, kann die Assembly in den Standardladekontext geladen werden, was bei .NET Framework bedeutet, dass sich die Assembly an einem der folgenden Speicherorte befindet:</span><span class="sxs-lookup"><span data-stu-id="e2f97-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="e2f97-462">Anwendungsbasispfad: Dabei handelt es sich um den Ordner *bin*, in dem die ausführbare Datei ( *.exe*) der App gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="e2f97-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="e2f97-463">Globaler Assemblycache (GAC): Der GAC speichert Assemblys, die mehrere .NET Framework-Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="e2f97-464">Weitere Informationen finden Sie unter [Vorgehensweise: Installieren einer Assembly in den globalen Assemblycache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in der .NET Framework-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="e2f97-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e2f97-465">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="e2f97-465">Sample code</span></span>

<span data-ttu-id="e2f97-466">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:</span><span class="sxs-lookup"><span data-stu-id="e2f97-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="e2f97-467">Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:</span><span class="sxs-lookup"><span data-stu-id="e2f97-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="e2f97-468">NuGet-Paket (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="e2f97-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="e2f97-469">Klassenbibliothek (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="e2f97-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="e2f97-470">Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly (*StartupDiagnostics*) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="e2f97-471">Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="e2f97-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="e2f97-472">Registrierte Dienste</span><span class="sxs-lookup"><span data-stu-id="e2f97-472">Registered services</span></span>
  * <span data-ttu-id="e2f97-473">Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="e2f97-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="e2f97-474">Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)</span><span class="sxs-lookup"><span data-stu-id="e2f97-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="e2f97-475">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="e2f97-475">Request headers</span></span>
  * <span data-ttu-id="e2f97-476">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="e2f97-476">Environment variables</span></span>

<span data-ttu-id="e2f97-477">So führen Sie das Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="e2f97-477">To run the sample:</span></span>

<span data-ttu-id="e2f97-478">**Aktivierung über ein NuGet-Paket**</span><span class="sxs-lookup"><span data-stu-id="e2f97-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="e2f97-479">Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="e2f97-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="e2f97-480">Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2f97-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e2f97-481">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-481">Compile and run the app.</span></span> <span data-ttu-id="e2f97-482">In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="e2f97-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="e2f97-483">Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts ( *../HostingStartupPackage/bin/Debug*) als Paketquelle an.</span><span class="sxs-lookup"><span data-stu-id="e2f97-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="e2f97-484">Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="e2f97-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e2f97-485">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="e2f97-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e2f97-486">Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache.</span><span class="sxs-lookup"><span data-stu-id="e2f97-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="e2f97-487">Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e2f97-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="e2f97-488">**Aktivierung über eine Klassenbibliothek**</span><span class="sxs-lookup"><span data-stu-id="e2f97-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="e2f97-489">Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="e2f97-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="e2f97-490">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2f97-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e2f97-491">Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.</span><span class="sxs-lookup"><span data-stu-id="e2f97-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="e2f97-492">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-492">Compile and run the app.</span></span> <span data-ttu-id="e2f97-493">Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="e2f97-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="e2f97-494">Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="e2f97-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="e2f97-495">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="e2f97-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e2f97-496">**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**</span><span class="sxs-lookup"><span data-stu-id="e2f97-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="e2f97-497">Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="e2f97-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="e2f97-498">PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert.</span><span class="sxs-lookup"><span data-stu-id="e2f97-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="e2f97-499">Im Artikel [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell) erfahren Sie, wie Sie PowerShell auf anderen Plattformen erwerben können.</span><span class="sxs-lookup"><span data-stu-id="e2f97-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="e2f97-500">Führen Sie das Skript *build.ps1* im Ordner *RuntimeStore* aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="e2f97-501">Das Skript:</span><span class="sxs-lookup"><span data-stu-id="e2f97-501">The script:</span></span>
   * <span data-ttu-id="e2f97-502">Generiert das `StartupDiagnostics`-Paket im Ordner *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="e2f97-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="e2f97-503">Generiert den Laufzeitspeicher für `StartupDiagnostics` im Ordner *store*.</span><span class="sxs-lookup"><span data-stu-id="e2f97-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="e2f97-504">Der `dotnet store`-Befehl im Skript verwendet den [Laufzeitbezeichner (RID)](/dotnet/core/rid-catalog) `win7-x64` für einen für Windows bereitgestellten Hostingstart.</span><span class="sxs-lookup"><span data-stu-id="e2f97-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="e2f97-505">Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss in Zeile 37 die entsprechende RID eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e2f97-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="e2f97-506">Der Runtimespeicher für `StartupDiagnostics` würde später in den Runtimespeicher des Benutzers oder Systems auf dem Computer verschoben, auf dem die Assembly verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e2f97-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="e2f97-507">Der Installationsspeicherort für den Benutzerruntimespeicher für die `StartupDiagnostics`-Assembly ist *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="e2f97-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="e2f97-508">Generiert die `additionalDeps` für `StartupDiagnostics` im Ordner *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="e2f97-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="e2f97-509">Die zusätzlichen Abhängigkeiten würden später in die zusätzlichen Abhängigkeiten des Benutzers oder Systems verschoben.</span><span class="sxs-lookup"><span data-stu-id="e2f97-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="e2f97-510">Der Installationsspeicherort der zusätzlichen Abhängigkeiten des Benutzers `StartupDiagnostics` ist *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="e2f97-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="e2f97-511">Legt die Datei *deploy.ps1* im Ordner *deployment* ab.</span><span class="sxs-lookup"><span data-stu-id="e2f97-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="e2f97-512">Führen Sie das Skript *deploy.ps1* im Ordner *deployment* aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="e2f97-513">Das Skript fügt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e2f97-513">The script appends:</span></span>
   * <span data-ttu-id="e2f97-514">`StartupDiagnostics` an die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e2f97-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="e2f97-515">Der Pfad der Hostingstartabhängigkeiten (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_ADDITIONAL_DEPS`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="e2f97-516">Der Pfad des Runtimespeichers (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_SHARED_STORE`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="e2f97-517">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="e2f97-517">Run the sample app.</span></span>
1. <span data-ttu-id="e2f97-518">Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="e2f97-519">Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e2f97-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

---
title: Weitere Informationen zum Migrieren von ASP.NET MVC zu ASP.net Core MVC
author: wadepickett
description: Erfahren Sie, wie Sie mit dem Migrieren eines ASP.NET MVC-Projekts zu ASP.net Core MVC beginnen.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 17f2a2532c58c3796835328260231d63f8fb2e40
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015048"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="eb413-103">Migrating from ASP.NET MVC to ASP.NET Core MVC (Migrieren von ASP.NET MVC zu ASP.NET Core MVC)</span><span class="sxs-lookup"><span data-stu-id="eb413-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb413-104">In diesem Artikel wird gezeigt, wie Sie mit dem Migrieren eines ASP.NET MVC-Projekts zu [ASP.net Core MVC](xref:mvc/overview)beginnen.</span><span class="sxs-lookup"><span data-stu-id="eb413-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="eb413-105">Im Prozess werden verwandte Änderungen von ASP.NET MVC hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="eb413-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="eb413-106">Die Migration von ASP.NET MVC ist ein mehrstufiger Prozess.</span><span class="sxs-lookup"><span data-stu-id="eb413-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="eb413-107">In diesem Artikel wird Folgendes behandelt:</span><span class="sxs-lookup"><span data-stu-id="eb413-107">This article covers:</span></span>

* <span data-ttu-id="eb413-108">Anfängliche Einrichtung.</span><span class="sxs-lookup"><span data-stu-id="eb413-108">Initial setup.</span></span>
* <span data-ttu-id="eb413-109">Grundlegende Controller und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="eb413-109">Basic controllers and views.</span></span>
* <span data-ttu-id="eb413-110">Statischer Inhalt.</span><span class="sxs-lookup"><span data-stu-id="eb413-110">Static content.</span></span>
* <span data-ttu-id="eb413-111">Client seitige Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="eb413-111">Client-side dependencies.</span></span>

<span data-ttu-id="eb413-112">Informationen zum Migrieren von Konfiguration und Identity Code finden [Sie unter Migrieren der Konfiguration zu ASP.net Core](xref:migration/configuration) und [Migrieren der Authentifizierung und Identity ASP.net Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="eb413-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb413-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="eb413-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="eb413-114">Erstellen des MVC-Projekts Starter ASP.net</span><span class="sxs-lookup"><span data-stu-id="eb413-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="eb413-115">Erstellen Sie ein Beispiel ASP.NET MVC-Projekt in Visual Studio für die Migration:</span><span class="sxs-lookup"><span data-stu-id="eb413-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="eb413-116">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="eb413-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="eb413-117">Wählen Sie **ASP.NET-Webanwendung (.NET Framework)** , und klicken Sie dann auf **weiter**.</span><span class="sxs-lookup"><span data-stu-id="eb413-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="eb413-118">Benennen Sie das Projekt *"WebApp1"* damit der Namespace mit dem im nächsten Schritt erstellten ASP.net Core Projekt übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="eb413-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="eb413-119">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="eb413-119">Select **Create**.</span></span>
1. <span data-ttu-id="eb413-120">Wählen Sie **MVC**aus, und wählen Sie dann **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="eb413-121">Erstellen des ASP.net Core Projekts</span><span class="sxs-lookup"><span data-stu-id="eb413-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="eb413-122">Erstellen Sie eine neue Projekt Mappe mit einem neuen ASP.net Core Projekt, zu dem migriert werden soll:</span><span class="sxs-lookup"><span data-stu-id="eb413-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="eb413-123">Starten Sie eine zweite Instanz von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eb413-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="eb413-124">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="eb413-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="eb413-125">Wählen Sie **ASP.net Web Core-Webanwendung** und dann **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-125">Select **ASP.NET Web Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="eb413-126">Benennen Sie im Dialogfeld **Neues Projekt konfigurieren** das Projekt *"WebApp1"*.</span><span class="sxs-lookup"><span data-stu-id="eb413-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="eb413-127">Legen Sie den Speicherort auf ein anderes Verzeichnis als das vorherige Projekt fest, um denselben Projektnamen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb413-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="eb413-128">Wenn Sie den gleichen Namespace verwenden, ist es einfacher, Code zwischen den beiden Projekten zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="eb413-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="eb413-129">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="eb413-129">Select **Create**.</span></span>
1. <span data-ttu-id="eb413-130">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="eb413-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="eb413-131">Wählen Sie die Projektvorlage **Webanwendung (Model-View-Controller)** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="eb413-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="eb413-132">Konfigurieren des ASP.net Core-Standorts für die Verwendung von MVC</span><span class="sxs-lookup"><span data-stu-id="eb413-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="eb413-133">In ASP.net Core 3,0-und späteren Projekten wird .NET Framework nicht mehr als Ziel Framework unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eb413-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="eb413-134">Ihr Projekt muss auf .net Core ausgerichtet sein.</span><span class="sxs-lookup"><span data-stu-id="eb413-134">Your project must target .NET Core.</span></span> <span data-ttu-id="eb413-135">Die ASP.net Core Shared Framework, das MVC umfasst, ist Teil der .net Core-Lauf Zeit Installation.</span><span class="sxs-lookup"><span data-stu-id="eb413-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="eb413-136">Bei Verwendung des `Microsoft.NET.Sdk.Web` SDK in der Projektdatei wird automatisch auf das freigegebene Framework verwiesen:</span><span class="sxs-lookup"><span data-stu-id="eb413-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="eb413-137">Weitere Informationen finden Sie unter [frameworkreferenz](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="eb413-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="eb413-138">In ASP.net Core die- `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="eb413-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="eb413-139">Ersetzt *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="eb413-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="eb413-140">Behandelt alle App-Start Tasks.</span><span class="sxs-lookup"><span data-stu-id="eb413-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="eb413-141">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="eb413-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="eb413-142">Öffnen Sie im Projekt ASP.net Core die Datei *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="eb413-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="eb413-143">ASP.net Core-apps müssen Frameworkfunktionen mit Middleware abonnieren.</span><span class="sxs-lookup"><span data-stu-id="eb413-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="eb413-144">Der vorherige von der Vorlage generierte Code fügt die folgenden Dienste und Middleware hinzu:</span><span class="sxs-lookup"><span data-stu-id="eb413-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="eb413-145">Die <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> Erweiterungsmethode registriert die MVC-Dienst Unterstützung für Controller, API-bezogene Features und Sichten.</span><span class="sxs-lookup"><span data-stu-id="eb413-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="eb413-146">Weitere Informationen zu MVC-Dienst Registrierungs Optionen finden Sie unter [MVC-Dienst Registrierung](xref:migration/22-to-30#mvc-service-registration) .</span><span class="sxs-lookup"><span data-stu-id="eb413-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="eb413-147">Die <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> Erweiterungsmethode fügt den statischen Datei Handler hinzu `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="eb413-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="eb413-148">Die `UseStaticFiles` Erweiterungsmethode muss vor aufgerufen werden `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="eb413-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="eb413-149">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="eb413-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="eb413-150">Die <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> Erweiterungsmethode fügt Routing hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="eb413-151">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="eb413-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="eb413-152">Diese vorhandene Konfiguration umfasst das, was zum Migrieren des MVC-Beispielprojekts erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="eb413-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="eb413-153">Weitere Informationen zu ASP.net Core Middleware-Optionen finden Sie unter <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="eb413-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="eb413-154">Migrieren von Controllern und Ansichten</span><span class="sxs-lookup"><span data-stu-id="eb413-154">Migrate controllers and views</span></span>

<span data-ttu-id="eb413-155">Im ASP.net Core Projekt wird eine neue leere Controller Klasse und Ansichts Klasse hinzugefügt, um als Platzhalter zu fungieren, die dieselben Namen wie die Controller-und Ansichts Klassen in jedem ASP.NET MVC-Projekt für die Migration verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb413-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="eb413-156">Das ASP.net Core *"WebApp1"* -Projekt enthält bereits einen minimalen Beispiel Controller und eine Ansicht mit dem gleichen Namen wie das ASP.NET MVC-Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="eb413-157">Diese dienen als Platzhalter für den ASP.NET MVC-Controller und Sichten, die aus dem ASP.NET MVC *"WebApp1"* -Projekt migriert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="eb413-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="eb413-158">Kopieren Sie die Methoden aus ASP.NET MVC `HomeController` , um die neuen ASP.net Core `HomeController` Methoden zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="eb413-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="eb413-159">Es ist nicht erforderlich, den Rückgabetyp der Aktionsmethoden zu ändern.</span><span class="sxs-lookup"><span data-stu-id="eb413-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="eb413-160">Der Rückgabetyp der Controller Aktionsmethode der integrierten ASP.NET MVC-Vorlage ist " [Action result](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);". in ASP.net Core MVC wird stattdessen die Aktionsmethode zurückgegeben `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="eb413-160">The ASP.NET MVC built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="eb413-161">`ActionResult` implementiert `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="eb413-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="eb413-162">Klicken Sie im ASP.net Core-Projekt mit der rechten Maustaste auf das Verzeichnis *views/Home* , und wählen Sie **Add** > **Vorhandenes Element**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="eb413-163">Navigieren Sie im Dialogfeld **Vorhandenes Element hinzufügen** zum Verzeichnis *views/Home* des ASP.NET MVC *"WebApp1"* -Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="eb413-164">Wählen Sie die Ansichts Dateien *about. cshtml*, *Contact. cshtml*und *Index. cshtml* aus Razor , und klicken Sie dann auf **Hinzufügen**, um die vorhandenen Dateien zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="eb413-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="eb413-165">Weitere Informationen finden Sie unter <xref:mvc/controllers/actions> und <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="eb413-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="eb413-166">Jede Methode testen</span><span class="sxs-lookup"><span data-stu-id="eb413-166">Test each method</span></span>

<span data-ttu-id="eb413-167">Jeder Controller Endpunkt kann getestet werden, aber Layout und Stile werden später im Dokument behandelt.</span><span class="sxs-lookup"><span data-stu-id="eb413-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="eb413-168">Führen Sie die ASP.net Core-App aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="eb413-169">Rufen Sie die gerenderten Sichten aus dem Browser auf der laufenden ASP.net Core-App auf, indem Sie die aktuelle Portnummer durch die im ASP.net Core Projekt verwendete Portnummer ersetzen.</span><span class="sxs-lookup"><span data-stu-id="eb413-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="eb413-170">Beispielsweise `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="eb413-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="eb413-171">Migrieren statischer Inhalte</span><span class="sxs-lookup"><span data-stu-id="eb413-171">Migrate static content</span></span>

<span data-ttu-id="eb413-172">In ASP.NET MVC 5 und früheren Versionen wurde statischer Inhalt aus dem Stammverzeichnis des Webprojekts gehostet und mit serverseitigen Dateien gemischt.</span><span class="sxs-lookup"><span data-stu-id="eb413-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="eb413-173">In ASP.net Core werden statische Dateien im [Webstamm](xref:fundamentals/index#web-root) Verzeichnis des Projekts gespeichert.</span><span class="sxs-lookup"><span data-stu-id="eb413-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="eb413-174">Das Standardverzeichnis ist *{Content root}/wwwroot*, kann jedoch geändert werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="eb413-175">Weitere Informationen finden Sie unter [Statische Dateien in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="eb413-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="eb413-176">Kopieren Sie den statischen Inhalt aus dem ASP.NET MVC *"WebApp1"* -Projekt in das Verzeichnis " *wwwroot* " im Projekt "ASP.net Core *" WebApp1 "* ":</span><span class="sxs-lookup"><span data-stu-id="eb413-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="eb413-177">Klicken Sie im ASP.net Core-Projekt mit der rechten Maustaste auf das Verzeichnis *wwwroot* , und wählen Sie **Add** > **Vorhandenes Element**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="eb413-178">Navigieren Sie im Dialogfeld **Vorhandenes Element hinzufügen** zum ASP.NET MVC *"WebApp1"* -Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="eb413-179">Wählen Sie die Datei *Favicon. ico aus* , und klicken Sie auf **Hinzufügen**, um die vorhandene Datei zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="eb413-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="eb413-180">Migrieren der Layoutdateien</span><span class="sxs-lookup"><span data-stu-id="eb413-180">Migrate the layout files</span></span>

<span data-ttu-id="eb413-181">Kopieren Sie die ASP.NET MVC-projektlayoutdateien in das ASP.net Core Projekt:</span><span class="sxs-lookup"><span data-stu-id="eb413-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="eb413-182">Klicken Sie im ASP.net Core Projekt mit der rechten Maustaste auf das Verzeichnis *Sichten* , und wählen Sie **Add** > **Vorhandenes Element**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="eb413-183">Navigieren Sie im Dialogfeld **Vorhandenes Element hinzufügen** zum Verzeichnis *views* -Verzeichnis des ASP.NET MVC *"WebApp1"* -Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="eb413-184">Wählen Sie die Datei *_ViewStart. cshtml* und dann **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="eb413-185">Kopieren Sie die freigegebenen Layoutdateien des ASP.NET-MVC-Projekts in das ASP.net Core Projekt:</span><span class="sxs-lookup"><span data-stu-id="eb413-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="eb413-186">Klicken Sie im ASP.net Core-Projekt mit der rechten Maustaste auf das Verzeichnis *views/Shared* , und wählen Sie **Add** > **Vorhandenes Element**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="eb413-187">Navigieren Sie im Dialogfeld **Vorhandenes Element hinzufügen** zum Verzeichnis *views/Shared* des ASP.NET MVC *"WebApp1"* -Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="eb413-188">Wählen Sie die Datei *_Layout. cshtml* aus, und klicken Sie dann auf **Hinzufügen**, um die vorhandene Datei zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="eb413-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="eb413-189">Öffnen Sie im Projekt ASP.net Core die Datei *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eb413-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="eb413-190">Nehmen Sie die folgenden Änderungen vor, um den unten gezeigten vollständigen Code abzugleichen:</span><span class="sxs-lookup"><span data-stu-id="eb413-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="eb413-191">Aktualisieren Sie die Bootstrap-CSS-Einbindung, damit Sie mit dem unten stehenden Code identisch ist</span><span class="sxs-lookup"><span data-stu-id="eb413-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="eb413-192">Ersetzen `@Styles.Render("~/Content/css")` Sie dies durch ein- `<link>` Element, das *Bootstrap. CSS* laden soll (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="eb413-193">Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="eb413-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="eb413-194">Das abgeschlossene Ersetzungs Markup für die Bootstrap-CSS-Einbindung:</span><span class="sxs-lookup"><span data-stu-id="eb413-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="eb413-195">Aktualisieren Sie die jQuery-und Bootstrap-JavaScript-Einbindung entsprechend dem unten stehenden Code</span><span class="sxs-lookup"><span data-stu-id="eb413-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="eb413-196">Ersetzen Sie dies `@Scripts.Render("~/bundles/jquery")` durch ein- `<script>` Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="eb413-197">Ersetzen Sie dies `@Scripts.Render("~/bundles/bootstrap")` durch ein- `<script>` Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="eb413-198">Das abgeschlossene Ersetzungs Markup für die jQuery-und Bootstrap-JavaScript-Einbindung:</span><span class="sxs-lookup"><span data-stu-id="eb413-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="eb413-199">Die aktualisierte Datei *_Layout. cshtml* ist unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="eb413-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="eb413-200">Zeigen Sie die Website im Browser an.</span><span class="sxs-lookup"><span data-stu-id="eb413-200">View the site in the browser.</span></span> <span data-ttu-id="eb413-201">Er sollte mit den erwarteten Stilen dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="eb413-202">Konfigurieren der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="eb413-202">Configure bundling and minification</span></span>

<span data-ttu-id="eb413-203">ASP.net Core ist mit mehreren Open-Source-bündeln und [minisierungslösungen wie weboptimizer](https://github.com/ligershark/WebOptimizer) und anderen ähnlichen Bibliotheken kompatibel.</span><span class="sxs-lookup"><span data-stu-id="eb413-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="eb413-204">ASP.net Core bietet keine native Bündelung und Minimierung.</span><span class="sxs-lookup"><span data-stu-id="eb413-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="eb413-205">Weitere Informationen zum Konfigurieren von Bündelung und Minimierung finden Sie unter [Bündelung und Minimierung](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="eb413-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="eb413-206">Beheben von HTTP 500-Fehlern</span><span class="sxs-lookup"><span data-stu-id="eb413-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="eb413-207">Es gibt viele Probleme, die eine HTTP 500-Fehlermeldung verursachen können, die keine Informationen zur Ursache des Problems enthält.</span><span class="sxs-lookup"><span data-stu-id="eb413-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="eb413-208">Wenn die Datei *views/_ViewImports. cshtml* beispielsweise einen Namespace enthält, der im Projekt nicht vorhanden ist, wird ein HTTP 500-Fehler generiert.</span><span class="sxs-lookup"><span data-stu-id="eb413-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="eb413-209">Standardmäßig wird die Erweiterung in ASP.net Core `UseDeveloperExceptionPage` -apps hinzugefügt `IApplicationBuilder` und bei der *Entwicklung*der Umgebung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="eb413-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="eb413-210">Dies wird im folgenden Code ausführlich erläutert:</span><span class="sxs-lookup"><span data-stu-id="eb413-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="eb413-211">ASP.net Core konvertiert nicht behandelte Ausnahmen in HTTP 500-Fehler Antworten.</span><span class="sxs-lookup"><span data-stu-id="eb413-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="eb413-212">Normalerweise werden Fehlerdetails nicht in diese Antworten eingeschlossen, um das Offenlegen von potenziell sensiblen Informationen über den Server zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="eb413-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="eb413-213">Weitere Informationen finden Sie auf der [Seite Entwickler Ausnahme](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="eb413-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="eb413-214">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="eb413-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="eb413-215">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb413-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="eb413-216">In diesem Artikel wird gezeigt, wie Sie mit der Migration eines ASP.NET MVC-Projekts zu [ASP.net Core MVC](xref:mvc/overview) 2,2 beginnen.</span><span class="sxs-lookup"><span data-stu-id="eb413-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="eb413-217">In diesem Prozess werden viele der Dinge hervorgehoben, die sich seit ASP.NET MVC geändert haben.</span><span class="sxs-lookup"><span data-stu-id="eb413-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="eb413-218">Die Migration von ASP.NET MVC ist ein mehrstufiger Prozess.</span><span class="sxs-lookup"><span data-stu-id="eb413-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="eb413-219">In diesem Artikel wird Folgendes behandelt:</span><span class="sxs-lookup"><span data-stu-id="eb413-219">This article covers:</span></span>

* <span data-ttu-id="eb413-220">Erste Einrichtung</span><span class="sxs-lookup"><span data-stu-id="eb413-220">Initial setup</span></span>
* <span data-ttu-id="eb413-221">Grundlegende Controller und Ansichten</span><span class="sxs-lookup"><span data-stu-id="eb413-221">Basic controllers and views</span></span>
* <span data-ttu-id="eb413-222">Statischer Inhalt</span><span class="sxs-lookup"><span data-stu-id="eb413-222">Static content</span></span>
* <span data-ttu-id="eb413-223">Client seitige Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="eb413-223">Client-side dependencies.</span></span>

<span data-ttu-id="eb413-224">Informationen zum Migrieren von Konfiguration und Identity Code finden Sie unter <xref:migration/configuration> und <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="eb413-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="eb413-225">Die Versionsnummern in den Beispielen sind möglicherweise nicht aktuell, aktualisieren Sie die Projekte entsprechend.</span><span class="sxs-lookup"><span data-stu-id="eb413-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="eb413-226">Erstellen des MVC-Projekts Starter ASP.net</span><span class="sxs-lookup"><span data-stu-id="eb413-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="eb413-227">Um das Upgrade zu veranschaulichen, erstellen wir zunächst eine ASP.NET MVC-app.</span><span class="sxs-lookup"><span data-stu-id="eb413-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="eb413-228">Erstellen Sie die Datei mit dem Namen *"WebApp1"* , damit der Namespace mit dem im nächsten Schritt erstellten ASP.net Core Projekt übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="eb413-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio - Dialogfeld „Neues Projekt“](mvc/_static/new-project.png)

![Dialogfeld "neue Webanwendung": MVC-Projektvorlage im Bereich "ASP.net Templates"](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="eb413-231">*Optional:* Ändern Sie den Namen der Projekt Mappe von *"WebApp1"* in *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="eb413-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="eb413-232">Visual Studio zeigt den neuen Projektmappennamen an (*Mvc5*). Dadurch wird das Projekt leichter vom nächsten Projekt aus informiert.</span><span class="sxs-lookup"><span data-stu-id="eb413-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="eb413-233">Erstellen des ASP.net Core Projekts</span><span class="sxs-lookup"><span data-stu-id="eb413-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="eb413-234">Erstellen Sie eine neue *leere* ASP.net Core Web-App mit dem gleichen Namen wie das vorherige Projekt (*"WebApp1"*), damit die Namespaces in den beiden Projekten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="eb413-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="eb413-235">Wenn Sie den gleichen Namespace haben, ist es einfacher, Code zwischen den beiden Projekten zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="eb413-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="eb413-236">Erstellen Sie dieses Projekt in einem anderen Verzeichnis als das vorherige Projekt, um denselben Namen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb413-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Dialogfeld "Neues Projekt"](mvc/_static/new_core.png)

![Dialogfeld "neue ASP.NET Webanwendung": leere Projektvorlage im Bereich "ASP.net Core Vorlagen" ausgewählt](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="eb413-239">*Optional:* Erstellen Sie mithilfe der Projektvorlage für die *Webanwendung* eine neue ASP.net Core-app.</span><span class="sxs-lookup"><span data-stu-id="eb413-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="eb413-240">Nennen Sie das Projekt *"WebApp1"*, und wählen Sie eine Authentifizierungs Option **einzelner Benutzerkonten**aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="eb413-241">Benennen Sie diese APP in *fullaspnetcore*um.</span><span class="sxs-lookup"><span data-stu-id="eb413-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="eb413-242">Wenn Sie dieses Projekt erstellen, sparen Sie Zeit in der Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="eb413-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="eb413-243">Das Endergebnis kann im von der Vorlage generierten Code angezeigt werden, Code kann in das Konvertierungs Projekt kopiert oder mit dem Vorlagen generierten Projekt verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="eb413-244">Konfigurieren des Standorts für die Verwendung von MVC</span><span class="sxs-lookup"><span data-stu-id="eb413-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="eb413-245">Wenn .net Core als Ziel verwendet wird, wird standardmäßig auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) verwiesen.</span><span class="sxs-lookup"><span data-stu-id="eb413-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="eb413-246">Dieses Paket enthält Pakete, die häufig von MVC-Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="eb413-247">Wenn Sie .NET Framework adressieren, müssen Paket Verweise einzeln in der Projektdatei aufgelistet werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="eb413-248">`Microsoft.AspNetCore.Mvc`ist das ASP.net Core MVC-Framework.</span><span class="sxs-lookup"><span data-stu-id="eb413-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="eb413-249">`Microsoft.AspNetCore.StaticFiles`ist der statische Datei Handler.</span><span class="sxs-lookup"><span data-stu-id="eb413-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="eb413-250">ASP.net Core-apps entscheiden sich explizit für Middleware, z. b. für die Betreuung statischer Dateien.</span><span class="sxs-lookup"><span data-stu-id="eb413-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="eb413-251">Weitere Informationen finden Sie im Artikel zu [statischen Dateien](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="eb413-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="eb413-252">Öffnen Sie die Datei *Startup.cs* , und ändern Sie den Code so, dass er dem folgenden entspricht:</span><span class="sxs-lookup"><span data-stu-id="eb413-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="eb413-253">Die <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> Erweiterungsmethode fügt den statischen Datei Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="eb413-254">Weitere Informationen finden Sie unter [starten](xref:fundamentals/startup) und [Routing](xref:fundamentals/routing)von Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="eb413-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="eb413-255">Controller und Ansicht hinzufügen</span><span class="sxs-lookup"><span data-stu-id="eb413-255">Add a controller and view</span></span>

<span data-ttu-id="eb413-256">In diesem Abschnitt werden ein minimaler Controller und eine Ansicht hinzugefügt, die als Platzhalter für den ASP.NET MVC-Controller und im nächsten Abschnitt migrierte Sichten fungieren.</span><span class="sxs-lookup"><span data-stu-id="eb413-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="eb413-257">Fügen Sie ein *Controller* Verzeichnis hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="eb413-258">Fügen Sie dem Verzeichnis *Controller* eine **Controller Klasse** mit dem Namen *HomeController.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="eb413-260">Fügen Sie ein *views* -Verzeichnis hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="eb413-261">Fügen Sie ein *views/Home-* Verzeichnis hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="eb413-262">Fügen Sie dem Verzeichnis *views/Home* eine \*\* Razor Ansicht\*\* mit dem Namen *Index. cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/view.png)

<span data-ttu-id="eb413-264">Die Projektstruktur wird unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="eb413-264">The project structure is shown below:</span></span>

![Projektmappen-Explorer anzeigen von Dateien und Verzeichnissen von "WebApp1"](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="eb413-266">Ersetzen Sie die Inhalte der Datei *Views/HelloWorld/Index.cshtml* durch das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="eb413-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="eb413-267">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-267">Run the app.</span></span>

![Die Web-App wird in Microsoft Edge geöffnet.](mvc/_static/hello-world.png)

<span data-ttu-id="eb413-269">Weitere Informationen finden Sie unter [Controller](xref:mvc/controllers/actions) und [Ansichten](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="eb413-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="eb413-270">Die folgende Funktionalität erfordert eine Migration von dem Beispiel ASP.NET MVC-Projekt zum ASP.net Core Projekt:</span><span class="sxs-lookup"><span data-stu-id="eb413-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="eb413-271">Client seitiger Inhalt (CSS, Schriftarten und Skripts)</span><span class="sxs-lookup"><span data-stu-id="eb413-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="eb413-272">Controller</span><span class="sxs-lookup"><span data-stu-id="eb413-272">controllers</span></span>

* <span data-ttu-id="eb413-273">views</span><span class="sxs-lookup"><span data-stu-id="eb413-273">views</span></span>

* <span data-ttu-id="eb413-274">Modelle</span><span class="sxs-lookup"><span data-stu-id="eb413-274">models</span></span>

* <span data-ttu-id="eb413-275">anbietet</span><span class="sxs-lookup"><span data-stu-id="eb413-275">bundling</span></span>

* <span data-ttu-id="eb413-276">Filter</span><span class="sxs-lookup"><span data-stu-id="eb413-276">filters</span></span>

* <span data-ttu-id="eb413-277">Melden Sie sich an/aus Identity (Dies erfolgt im nächsten Tutorial).</span><span class="sxs-lookup"><span data-stu-id="eb413-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="eb413-278">Controller und Ansichten</span><span class="sxs-lookup"><span data-stu-id="eb413-278">Controllers and views</span></span>

* <span data-ttu-id="eb413-279">Kopieren Sie jede Methode aus ASP.NET MVC `HomeController` in die neue `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="eb413-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="eb413-280">In ASP.NET MVC lautet der Rückgabetyp der Controller Aktionsmethode der integrierten Vorlage " [Action result](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);". in ASP.net Core MVC wird stattdessen die Aktionsmethode zurückgegeben `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="eb413-280">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="eb413-281">`ActionResult`implementiert `IActionResult` , sodass es nicht erforderlich ist, den Rückgabetyp der Aktionsmethoden zu ändern.</span><span class="sxs-lookup"><span data-stu-id="eb413-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="eb413-282">Kopieren Sie die Ansichts Dateien " *about. cshtml*", " *Contact. cshtml*" und " *Index. cshtml* " Razor vom ASP.NET-MVC-Projekt in das ASP.net Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="eb413-283">Jede Methode testen</span><span class="sxs-lookup"><span data-stu-id="eb413-283">Test each method</span></span>

<span data-ttu-id="eb413-284">Die Layoutdatei und-Stile wurden noch nicht migriert, sodass die gerenderten Sichten nur den Inhalt der Ansichts Dateien enthalten.</span><span class="sxs-lookup"><span data-stu-id="eb413-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="eb413-285">Die von der Layoutdatei generierten `About` `Contact` Verknüpfungen für die Ansichten und sind noch nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="eb413-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="eb413-286">Rufen Sie die gerenderten Sichten aus dem Browser auf der laufenden ASP.net Core-App auf, indem Sie die aktuelle Portnummer durch die im ASP.net Core-Projekt verwendete Portnummer ersetzen.</span><span class="sxs-lookup"><span data-stu-id="eb413-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="eb413-287">Beispiel: `https://localhost:44375/home/about`</span><span class="sxs-lookup"><span data-stu-id="eb413-287">For example: `https://localhost:44375/home/about`.</span></span>

![Kontaktseite](mvc/_static/contact-page.png)

<span data-ttu-id="eb413-289">Beachten Sie den Mangel an Formatierungs-und Menü Elementen.</span><span class="sxs-lookup"><span data-stu-id="eb413-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="eb413-290">Die Formatierung wird im nächsten Abschnitt korrigiert.</span><span class="sxs-lookup"><span data-stu-id="eb413-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="eb413-291">Statischer Inhalt</span><span class="sxs-lookup"><span data-stu-id="eb413-291">Static content</span></span>

<span data-ttu-id="eb413-292">In ASP.NET MVC 5 und früheren Versionen wurde statischer Inhalt aus dem Stammverzeichnis des Webprojekts gehostet und mit serverseitigen Dateien gemischt.</span><span class="sxs-lookup"><span data-stu-id="eb413-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="eb413-293">In ASP.net Core wird statischer Inhalt im Verzeichnis " *wwwroot* " gehostet.</span><span class="sxs-lookup"><span data-stu-id="eb413-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="eb413-294">Kopieren Sie den statischen Inhalt aus der ASP.NET MVC-app in das Verzeichnis " *wwwroot* " im ASP.net Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="eb413-295">In dieser Beispiel Konvertierung:</span><span class="sxs-lookup"><span data-stu-id="eb413-295">In this sample conversion:</span></span>

* <span data-ttu-id="eb413-296">Kopieren Sie die Datei " *Favicon. ico* " aus dem ASP.NET-MVC-Projekt in das Verzeichnis " *wwwroot* " im ASP.net Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="eb413-297">Das ASP.NET MVC-Projekt verwendet [Bootstrap](https://getbootstrap.com/) für seine Formatierung und speichert die Bootstrap-Dateien in den Verzeichnissen " *Content* " und " *Scripts* ".</span><span class="sxs-lookup"><span data-stu-id="eb413-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="eb413-298">Die Vorlage, die das ASP.NET MVC-Projekt generiert hat, verweist auf Bootstrap in der Layoutdatei (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="eb413-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="eb413-299">Die Dateien " *bootstrap.js* " und " *Bootstrap. CSS* " konnten aus dem ASP.NET-MVC-Projekt in das Verzeichnis " *wwwroot* " im neuen Projekt kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="eb413-300">Stattdessen bietet dieses Dokument Unterstützung für Bootstrap (und andere Client seitige Bibliotheken) mithilfe von CDNs im nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="eb413-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="eb413-301">Migrieren der Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="eb413-301">Migrate the layout file</span></span>

* <span data-ttu-id="eb413-302">Kopieren Sie die Datei *_ViewStart. cshtml* aus dem Verzeichnis *views* des ASP.NET MVC-Projekts in das Verzeichnis *views* des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="eb413-303">Die Datei *_ViewStart. cshtml* wurde in ASP.net Core MVC nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="eb413-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="eb413-304">Erstellen Sie ein *views/Shared-* Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="eb413-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="eb413-305">*Optional:* Kopieren Sie *_ViewImports. cshtml* aus dem Verzeichnis *views* -Verzeichnis des Projekts " *fullaspnetcore* " in das Verzeichnis " *views* " des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="eb413-306">Entfernen Sie eine beliebige Namespace Deklaration in der Datei *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eb413-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="eb413-307">Die Datei *_ViewImports. cshtml* enthält Namespaces für alle Ansichts Dateien und führt [taghilfsprogramme](xref:mvc/views/tag-helpers/intro)aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="eb413-308">Taghilfsprogramme werden in der neuen Layoutdatei verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb413-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="eb413-309">Die Datei *_ViewImports. cshtml* ist neu für ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="eb413-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="eb413-310">Kopieren Sie die Datei *_Layout. cshtml* aus dem Verzeichnis *views/Shared* des ASP.NET-MVC-Projekts in die *Ansichten/* das freigegebene Verzeichnis des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="eb413-311">Öffnen Sie *_Layout. cshtml* -Datei, und nehmen Sie die folgenden Änderungen vor (der abgeschlossene Code wird unten angezeigt):</span><span class="sxs-lookup"><span data-stu-id="eb413-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="eb413-312">Ersetzen `@Styles.Render("~/Content/css")` Sie dies durch ein- `<link>` Element, das *Bootstrap. CSS* laden soll (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="eb413-313">Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="eb413-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="eb413-314">Kommentieren Sie die `@Html.Partial("_LoginPartial")` Zeile aus (umschließen Sie die Zeile mit `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="eb413-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="eb413-315">Weitere Informationen finden Sie unter [Migrieren der Authentifizierung und Identity zu ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="eb413-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="eb413-316">Ersetzen Sie dies `@Scripts.Render("~/bundles/jquery")` durch ein- `<script>` Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="eb413-317">Ersetzen Sie dies `@Scripts.Render("~/bundles/bootstrap")` durch ein- `<script>` Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="eb413-318">Das Ersetzungs Markup für die Bootstrap-CSS-Einbindung:</span><span class="sxs-lookup"><span data-stu-id="eb413-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="eb413-319">Das Ersetzungs Markup für die jQuery-und Bootstrap-JavaScript-Einbindung:</span><span class="sxs-lookup"><span data-stu-id="eb413-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="eb413-320">Die aktualisierte Datei *_Layout. cshtml* ist unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="eb413-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="eb413-321">Zeigen Sie die Website im Browser an.</span><span class="sxs-lookup"><span data-stu-id="eb413-321">View the site in the browser.</span></span> <span data-ttu-id="eb413-322">Es sollte jetzt ordnungsgemäß geladen werden, wobei die erwarteten Stile vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="eb413-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="eb413-323">*Optional:* Versuchen Sie, die neue Layoutdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb413-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="eb413-324">Kopieren Sie die Layoutdatei aus dem *fullaspnetcore* -Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="eb413-325">Die neue Layoutdatei verwendet [taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und bietet weitere Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="eb413-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="eb413-326">Konfigurieren der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="eb413-326">Configure bundling and minification</span></span>

<span data-ttu-id="eb413-327">Weitere Informationen zum Konfigurieren von Bündelung und Minimierung finden Sie unter [Bündelung und Minimierung](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="eb413-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="eb413-328">Beheben von HTTP 500-Fehlern</span><span class="sxs-lookup"><span data-stu-id="eb413-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="eb413-329">Es gibt viele Probleme, die eine HTTP 500-Fehlermeldung verursachen können, die keine Informationen zur Ursache des Problems enthält.</span><span class="sxs-lookup"><span data-stu-id="eb413-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="eb413-330">Wenn die Datei *views/_ViewImports. cshtml* beispielsweise einen Namespace enthält, der im Projekt nicht vorhanden ist, wird ein HTTP 500-Fehler generiert.</span><span class="sxs-lookup"><span data-stu-id="eb413-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="eb413-331">Standardmäßig wird die Erweiterung in ASP.net Core `UseDeveloperExceptionPage` -apps dem hinzugefügt `IApplicationBuilder` und ausgeführt, wenn die Konfiguration *entwickelt*wird.</span><span class="sxs-lookup"><span data-stu-id="eb413-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="eb413-332">Sehen Sie sich ein Beispiel im folgenden Code an:</span><span class="sxs-lookup"><span data-stu-id="eb413-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="eb413-333">ASP.net Core konvertiert nicht behandelte Ausnahmen in HTTP 500-Fehler Antworten.</span><span class="sxs-lookup"><span data-stu-id="eb413-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="eb413-334">Normalerweise werden Fehlerdetails nicht in diese Antworten eingeschlossen, um das Offenlegen von potenziell sensiblen Informationen über den Server zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="eb413-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="eb413-335">Weitere Informationen finden Sie auf der [Seite Entwickler Ausnahme](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="eb413-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb413-336">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb413-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="eb413-337">In diesem Artikel wird gezeigt, wie Sie mit der Migration eines ASP.NET MVC-Projekts zu [ASP.net Core MVC](xref:mvc/overview) 2,1 beginnen.</span><span class="sxs-lookup"><span data-stu-id="eb413-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="eb413-338">In diesem Prozess werden viele der Dinge hervorgehoben, die sich seit ASP.NET MVC geändert haben.</span><span class="sxs-lookup"><span data-stu-id="eb413-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="eb413-339">Die Migration von ASP.NET MVC ist ein mehrstufiger Prozess.</span><span class="sxs-lookup"><span data-stu-id="eb413-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="eb413-340">In diesem Artikel wird Folgendes behandelt:</span><span class="sxs-lookup"><span data-stu-id="eb413-340">This article covers:</span></span>

* <span data-ttu-id="eb413-341">Erste Einrichtung</span><span class="sxs-lookup"><span data-stu-id="eb413-341">Initial setup</span></span>
* <span data-ttu-id="eb413-342">Grundlegende Controller und Ansichten</span><span class="sxs-lookup"><span data-stu-id="eb413-342">Basic controllers and views</span></span>
* <span data-ttu-id="eb413-343">Statischer Inhalt</span><span class="sxs-lookup"><span data-stu-id="eb413-343">Static content</span></span>
* <span data-ttu-id="eb413-344">Client seitige Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="eb413-344">Client-side dependencies.</span></span>

<span data-ttu-id="eb413-345">Informationen zum Migrieren von Konfiguration und Identity Code finden [Sie unter Migrieren der Konfiguration zu ASP.net Core](xref:migration/configuration) und [Migrieren der Authentifizierung und Identity ASP.net Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="eb413-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="eb413-346">Die Versionsnummern in den Beispielen sind möglicherweise nicht aktuell, aktualisieren Sie die Projekte entsprechend.</span><span class="sxs-lookup"><span data-stu-id="eb413-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="eb413-347">Erstellen des MVC-Projekts Starter ASP.net</span><span class="sxs-lookup"><span data-stu-id="eb413-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="eb413-348">Um das Upgrade zu veranschaulichen, erstellen wir zunächst eine ASP.NET MVC-app.</span><span class="sxs-lookup"><span data-stu-id="eb413-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="eb413-349">Erstellen Sie die Datei mit dem Namen *"WebApp1"* , damit der Namespace mit dem im nächsten Schritt erstellten ASP.net Core Projekt übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="eb413-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio - Dialogfeld „Neues Projekt“](mvc/_static/new-project.png)

![Dialogfeld "neue Webanwendung": MVC-Projektvorlage im Bereich "ASP.net Templates"](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="eb413-352">*Optional:* Ändern Sie den Namen der Projekt Mappe von *"WebApp1"* in *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="eb413-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="eb413-353">Visual Studio zeigt den neuen Projektmappennamen an (*Mvc5*). Dadurch wird das Projekt leichter vom nächsten Projekt aus informiert.</span><span class="sxs-lookup"><span data-stu-id="eb413-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="eb413-354">Erstellen des ASP.net Core Projekts</span><span class="sxs-lookup"><span data-stu-id="eb413-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="eb413-355">Erstellen Sie eine neue *leere* ASP.net Core Web-App mit dem gleichen Namen wie das vorherige Projekt (*"WebApp1"*), damit die Namespaces in den beiden Projekten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="eb413-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="eb413-356">Wenn Sie den gleichen Namespace haben, ist es einfacher, Code zwischen den beiden Projekten zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="eb413-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="eb413-357">Erstellen Sie dieses Projekt in einem anderen Verzeichnis als das vorherige Projekt, um denselben Namen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb413-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Dialogfeld "Neues Projekt"](mvc/_static/new_core.png)

![Dialogfeld "neue ASP.NET Webanwendung": leere Projektvorlage im Bereich "ASP.net Core Vorlagen" ausgewählt](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="eb413-360">*Optional:* Erstellen Sie mithilfe der Projektvorlage für die *Webanwendung* eine neue ASP.net Core-app.</span><span class="sxs-lookup"><span data-stu-id="eb413-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="eb413-361">Nennen Sie das Projekt *"WebApp1"*, und wählen Sie eine Authentifizierungs Option **einzelner Benutzerkonten**aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="eb413-362">Benennen Sie diese APP in *fullaspnetcore*um.</span><span class="sxs-lookup"><span data-stu-id="eb413-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="eb413-363">Wenn Sie dieses Projekt erstellen, sparen Sie Zeit in der Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="eb413-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="eb413-364">Das Endergebnis kann im von der Vorlage generierten Code angezeigt werden, Code kann in das Konvertierungs Projekt kopiert oder mit dem Vorlagen generierten Projekt verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="eb413-365">Konfigurieren des Standorts für die Verwendung von MVC</span><span class="sxs-lookup"><span data-stu-id="eb413-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="eb413-366">Wenn .net Core als Ziel verwendet wird, wird standardmäßig auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) verwiesen.</span><span class="sxs-lookup"><span data-stu-id="eb413-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="eb413-367">Dieses Paket enthält Pakete, die häufig von MVC-Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="eb413-368">Wenn Sie .NET Framework adressieren, müssen Paket Verweise einzeln in der Projektdatei aufgelistet werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="eb413-369">`Microsoft.AspNetCore.Mvc`ist das ASP.net Core MVC-Framework.</span><span class="sxs-lookup"><span data-stu-id="eb413-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="eb413-370">`Microsoft.AspNetCore.StaticFiles`ist der statische Datei Handler.</span><span class="sxs-lookup"><span data-stu-id="eb413-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="eb413-371">ASP.net Core-apps entscheiden sich explizit für Middleware, z. b. für die Betreuung statischer Dateien.</span><span class="sxs-lookup"><span data-stu-id="eb413-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="eb413-372">Weitere Informationen finden Sie im Artikel zu [statischen Dateien](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="eb413-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="eb413-373">Öffnen Sie die Datei *Startup.cs* , und ändern Sie den Code so, dass er dem folgenden entspricht:</span><span class="sxs-lookup"><span data-stu-id="eb413-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="eb413-374">Die <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> Erweiterungsmethode fügt den statischen Datei Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="eb413-375">Die `UseMvc` Erweiterungsmethode fügt Routing hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="eb413-376">Weitere Informationen finden Sie unter [starten](xref:fundamentals/startup) und [Routing](xref:fundamentals/routing)von Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="eb413-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="eb413-377">Controller und Ansicht hinzufügen</span><span class="sxs-lookup"><span data-stu-id="eb413-377">Add a controller and view</span></span>

<span data-ttu-id="eb413-378">In diesem Abschnitt werden ein minimaler Controller und eine Ansicht hinzugefügt, die als Platzhalter für den ASP.NET MVC-Controller und im nächsten Abschnitt migrierte Sichten fungieren.</span><span class="sxs-lookup"><span data-stu-id="eb413-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="eb413-379">Fügen Sie ein *Controller* Verzeichnis hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="eb413-380">Fügen Sie dem Verzeichnis *Controller* eine **Controller Klasse** mit dem Namen *HomeController.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="eb413-382">Fügen Sie ein *views* -Verzeichnis hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="eb413-383">Fügen Sie ein *views/Home-* Verzeichnis hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="eb413-384">Fügen Sie dem Verzeichnis *views/Home* eine \*\* Razor Ansicht\*\* mit dem Namen *Index. cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb413-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/view.png)

<span data-ttu-id="eb413-386">Die Projektstruktur wird unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="eb413-386">The project structure is shown below:</span></span>

![Projektmappen-Explorer anzeigen von Dateien und Verzeichnissen von "WebApp1"](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="eb413-388">Ersetzen Sie die Inhalte der Datei *Views/HelloWorld/Index.cshtml* durch das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="eb413-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="eb413-389">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-389">Run the app.</span></span>

![Die Web-App wird in Microsoft Edge geöffnet.](mvc/_static/hello-world.png)

<span data-ttu-id="eb413-391">Weitere Informationen finden Sie unter [Controller](xref:mvc/controllers/actions) und [Ansichten](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="eb413-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="eb413-392">Die folgende Funktionalität erfordert eine Migration von dem Beispiel ASP.NET MVC-Projekt zum ASP.net Core Projekt:</span><span class="sxs-lookup"><span data-stu-id="eb413-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="eb413-393">Client seitiger Inhalt (CSS, Schriftarten und Skripts)</span><span class="sxs-lookup"><span data-stu-id="eb413-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="eb413-394">Controller</span><span class="sxs-lookup"><span data-stu-id="eb413-394">controllers</span></span>

* <span data-ttu-id="eb413-395">views</span><span class="sxs-lookup"><span data-stu-id="eb413-395">views</span></span>

* <span data-ttu-id="eb413-396">Modelle</span><span class="sxs-lookup"><span data-stu-id="eb413-396">models</span></span>

* <span data-ttu-id="eb413-397">anbietet</span><span class="sxs-lookup"><span data-stu-id="eb413-397">bundling</span></span>

* <span data-ttu-id="eb413-398">Filter</span><span class="sxs-lookup"><span data-stu-id="eb413-398">filters</span></span>

* <span data-ttu-id="eb413-399">Melden Sie sich an/aus Identity (Dies erfolgt im nächsten Tutorial).</span><span class="sxs-lookup"><span data-stu-id="eb413-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="eb413-400">Controller und Ansichten</span><span class="sxs-lookup"><span data-stu-id="eb413-400">Controllers and views</span></span>

* <span data-ttu-id="eb413-401">Kopieren Sie jede Methode aus ASP.NET MVC `HomeController` in die neue `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="eb413-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="eb413-402">In ASP.NET MVC lautet der Rückgabetyp der Controller Aktionsmethode der integrierten Vorlage " [Action result](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);". in ASP.net Core MVC wird stattdessen die Aktionsmethode zurückgegeben `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="eb413-402">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="eb413-403">`ActionResult`implementiert `IActionResult` , sodass es nicht erforderlich ist, den Rückgabetyp der Aktionsmethoden zu ändern.</span><span class="sxs-lookup"><span data-stu-id="eb413-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="eb413-404">Kopieren Sie die Ansichts Dateien " *about. cshtml*", " *Contact. cshtml*" und " *Index. cshtml* " Razor vom ASP.NET-MVC-Projekt in das ASP.net Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="eb413-405">Jede Methode testen</span><span class="sxs-lookup"><span data-stu-id="eb413-405">Test each method</span></span>

<span data-ttu-id="eb413-406">Die Layoutdatei und-Stile wurden noch nicht migriert, sodass die gerenderten Sichten nur den Inhalt der Ansichts Dateien enthalten.</span><span class="sxs-lookup"><span data-stu-id="eb413-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="eb413-407">Die von der Layoutdatei generierten `About` `Contact` Verknüpfungen für die Ansichten und sind noch nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="eb413-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="eb413-408">Rufen Sie die gerenderten Sichten aus dem Browser auf der laufenden ASP.net Core-App auf, indem Sie die aktuelle Portnummer durch die im ASP.net Core-Projekt verwendete Portnummer ersetzen.</span><span class="sxs-lookup"><span data-stu-id="eb413-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="eb413-409">Beispiel: `https://localhost:44375/home/about`</span><span class="sxs-lookup"><span data-stu-id="eb413-409">For example: `https://localhost:44375/home/about`.</span></span>

![Kontaktseite](mvc/_static/contact-page.png)

<span data-ttu-id="eb413-411">Beachten Sie den Mangel an Formatierungs-und Menü Elementen.</span><span class="sxs-lookup"><span data-stu-id="eb413-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="eb413-412">Die Formatierung wird im nächsten Abschnitt korrigiert.</span><span class="sxs-lookup"><span data-stu-id="eb413-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="eb413-413">Statischer Inhalt</span><span class="sxs-lookup"><span data-stu-id="eb413-413">Static content</span></span>

<span data-ttu-id="eb413-414">In ASP.NET MVC 5 und früheren Versionen wurde statischer Inhalt aus dem Stammverzeichnis des Webprojekts gehostet und mit serverseitigen Dateien gemischt.</span><span class="sxs-lookup"><span data-stu-id="eb413-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="eb413-415">In ASP.net Core wird statischer Inhalt im Verzeichnis " *wwwroot* " gehostet.</span><span class="sxs-lookup"><span data-stu-id="eb413-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="eb413-416">Kopieren Sie den statischen Inhalt aus der ASP.NET MVC-app in das Verzeichnis " *wwwroot* " im ASP.net Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="eb413-417">In dieser Beispiel Konvertierung:</span><span class="sxs-lookup"><span data-stu-id="eb413-417">In this sample conversion:</span></span>

* <span data-ttu-id="eb413-418">Kopieren Sie die Datei " *Favicon. ico* " aus dem ASP.NET-MVC-Projekt in das Verzeichnis " *wwwroot* " im ASP.net Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="eb413-419">Das ASP.NET MVC-Projekt verwendet [Bootstrap](https://getbootstrap.com/) für seine Formatierung und speichert die Bootstrap-Dateien in den Verzeichnissen " *Content* " und " *Scripts* ".</span><span class="sxs-lookup"><span data-stu-id="eb413-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="eb413-420">Die Vorlage, die das ASP.NET MVC-Projekt generiert hat, verweist auf Bootstrap in der Layoutdatei (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="eb413-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="eb413-421">Die Dateien " *bootstrap.js* " und " *Bootstrap. CSS* " konnten aus dem ASP.NET-MVC-Projekt in das Verzeichnis " *wwwroot* " im neuen Projekt kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="eb413-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="eb413-422">Stattdessen bietet dieses Dokument Unterstützung für Bootstrap (und andere Client seitige Bibliotheken) mithilfe von CDNs im nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="eb413-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="eb413-423">Migrieren der Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="eb413-423">Migrate the layout file</span></span>

* <span data-ttu-id="eb413-424">Kopieren Sie die Datei *_ViewStart. cshtml* aus dem Verzeichnis *views* des ASP.NET MVC-Projekts in das Verzeichnis *views* des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="eb413-425">Die Datei *_ViewStart. cshtml* wurde in ASP.net Core MVC nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="eb413-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="eb413-426">Erstellen Sie ein *views/Shared-* Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="eb413-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="eb413-427">*Optional:* Kopieren Sie *_ViewImports. cshtml* aus dem Verzeichnis *views* -Verzeichnis des Projekts " *fullaspnetcore* " in das Verzeichnis " *views* " des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="eb413-428">Entfernen Sie eine beliebige Namespace Deklaration in der Datei *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eb413-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="eb413-429">Die Datei *_ViewImports. cshtml* enthält Namespaces für alle Ansichts Dateien und führt [taghilfsprogramme](xref:mvc/views/tag-helpers/intro)aus.</span><span class="sxs-lookup"><span data-stu-id="eb413-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="eb413-430">Taghilfsprogramme werden in der neuen Layoutdatei verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb413-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="eb413-431">Die Datei *_ViewImports. cshtml* ist neu für ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="eb413-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="eb413-432">Kopieren Sie die Datei *_Layout. cshtml* aus dem Verzeichnis *views/Shared* des ASP.NET-MVC-Projekts in die *Ansichten/* das freigegebene Verzeichnis des ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="eb413-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="eb413-433">Öffnen Sie *_Layout. cshtml* -Datei, und nehmen Sie die folgenden Änderungen vor (der abgeschlossene Code wird unten angezeigt):</span><span class="sxs-lookup"><span data-stu-id="eb413-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="eb413-434">Ersetzen `@Styles.Render("~/Content/css")` Sie dies durch ein- `<link>` Element, das *Bootstrap. CSS* laden soll (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="eb413-435">Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="eb413-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="eb413-436">Kommentieren Sie die `@Html.Partial("_LoginPartial")` Zeile aus (umschließen Sie die Zeile mit `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="eb413-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="eb413-437">Weitere Informationen finden Sie unter [Migrieren der Authentifizierung und Identity zu ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="eb413-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="eb413-438">Ersetzen Sie dies `@Scripts.Render("~/bundles/jquery")` durch ein- `<script>` Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="eb413-439">Ersetzen Sie dies `@Scripts.Render("~/bundles/bootstrap")` durch ein- `<script>` Element (siehe unten).</span><span class="sxs-lookup"><span data-stu-id="eb413-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="eb413-440">Das Ersetzungs Markup für die Bootstrap-CSS-Einbindung:</span><span class="sxs-lookup"><span data-stu-id="eb413-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="eb413-441">Das Ersetzungs Markup für die jQuery-und Bootstrap-JavaScript-Einbindung:</span><span class="sxs-lookup"><span data-stu-id="eb413-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="eb413-442">Die aktualisierte Datei *_Layout. cshtml* ist unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="eb413-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="eb413-443">Zeigen Sie die Website im Browser an.</span><span class="sxs-lookup"><span data-stu-id="eb413-443">View the site in the browser.</span></span> <span data-ttu-id="eb413-444">Es sollte jetzt ordnungsgemäß geladen werden, wobei die erwarteten Stile vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="eb413-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="eb413-445">*Optional:* Versuchen Sie, die neue Layoutdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb413-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="eb413-446">Kopieren Sie die Layoutdatei aus dem *fullaspnetcore* -Projekt.</span><span class="sxs-lookup"><span data-stu-id="eb413-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="eb413-447">Die neue Layoutdatei verwendet [taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und bietet weitere Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="eb413-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="eb413-448">Konfigurieren der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="eb413-448">Configure bundling and minification</span></span>

<span data-ttu-id="eb413-449">Weitere Informationen zum Konfigurieren von Bündelung und Minimierung finden Sie unter [Bündelung und Minimierung](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="eb413-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="eb413-450">Beheben von HTTP 500-Fehlern</span><span class="sxs-lookup"><span data-stu-id="eb413-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="eb413-451">Es gibt viele Probleme, die eine HTTP 500-Fehlermeldung verursachen können, die keine Informationen zur Ursache des Problems enthält.</span><span class="sxs-lookup"><span data-stu-id="eb413-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="eb413-452">Wenn die Datei *views/_ViewImports. cshtml* beispielsweise einen Namespace enthält, der im Projekt nicht vorhanden ist, wird ein HTTP 500-Fehler generiert.</span><span class="sxs-lookup"><span data-stu-id="eb413-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="eb413-453">Standardmäßig wird die Erweiterung in ASP.net Core `UseDeveloperExceptionPage` -apps dem hinzugefügt `IApplicationBuilder` und ausgeführt, wenn die Konfiguration *entwickelt*wird.</span><span class="sxs-lookup"><span data-stu-id="eb413-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="eb413-454">Sehen Sie sich ein Beispiel im folgenden Code an:</span><span class="sxs-lookup"><span data-stu-id="eb413-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="eb413-455">ASP.net Core konvertiert nicht behandelte Ausnahmen in HTTP 500-Fehler Antworten.</span><span class="sxs-lookup"><span data-stu-id="eb413-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="eb413-456">Normalerweise werden Fehlerdetails nicht in diese Antworten eingeschlossen, um das Offenlegen von potenziell sensiblen Informationen über den Server zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="eb413-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="eb413-457">Weitere Informationen finden Sie auf der [Seite Entwickler Ausnahme](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="eb413-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb413-458">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb413-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

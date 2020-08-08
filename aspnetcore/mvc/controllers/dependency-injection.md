---
title: Dependency Injection in Controller in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie ASP.NET Core MVC-Controller Abhängigkeiten mit Dependency Injection in ASP.NET Core explizit über Konstruktoren anfordern.
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: aabb7f893fd9650e2e901dcfdfe845faba391435
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019171"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="c9d63-103">Dependency Injection in Controller in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9d63-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9d63-104">Von [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="c9d63-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="c9d63-105">ASP.NET Core MVC-Controller fordern Abhängigkeiten explizit über Konstruktoren an.</span><span class="sxs-lookup"><span data-stu-id="c9d63-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="c9d63-106">ASP.NET Core verfügt über integrierte Unterstützung für [Dependency Injection ( DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c9d63-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c9d63-107">DI erleichtert das Testen und die Wartung von Apps.</span><span class="sxs-lookup"><span data-stu-id="c9d63-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="c9d63-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c9d63-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="c9d63-109">Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="c9d63-109">Constructor Injection</span></span>

<span data-ttu-id="c9d63-110">Dienste werden als Konstruktorparameter hinzugefügt, und die Runtime löst den Dienst aus dem Dienstcontainer auf.</span><span class="sxs-lookup"><span data-stu-id="c9d63-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="c9d63-111">Dienste werden meist mithilfe von Schnittstellen definiert.</span><span class="sxs-lookup"><span data-stu-id="c9d63-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="c9d63-112">Stellen Sich sich z.B. eine App vor, für die die aktuelle Uhrzeit erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c9d63-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="c9d63-113">Die folgende Schnittstelle macht den `IDateTime`-Dienst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c9d63-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="c9d63-114">Im folgenden Code wird die `IDateTime`-Schnittstelle implementiert:</span><span class="sxs-lookup"><span data-stu-id="c9d63-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="c9d63-115">Fügen Sie anschließend dem Dienstcontainer den Dienst hinzu:</span><span class="sxs-lookup"><span data-stu-id="c9d63-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="c9d63-116">Weitere Informationen zu <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> finden Sie unter [Dependency Injection in ASP.NET Core](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="c9d63-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="c9d63-117">Im folgenden Code sehen Sie eine Begrüßung des Benutzers, die sich an der jeweiligen Tageszeit orientiert:</span><span class="sxs-lookup"><span data-stu-id="c9d63-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="c9d63-118">Führen Sie die App aus, und eine Nachricht wird angezeigt, die von der Tageszeit abhängt.</span><span class="sxs-lookup"><span data-stu-id="c9d63-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="c9d63-119">Action Injection mit FromServices</span><span class="sxs-lookup"><span data-stu-id="c9d63-119">Action injection with FromServices</span></span>

<span data-ttu-id="c9d63-120">Mit dem <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> ist es möglich, einen Dienst direkt in eine Aktionsmethode einzufügen, ohne eine Constructor Injection verwenden zu müssen:</span><span class="sxs-lookup"><span data-stu-id="c9d63-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="c9d63-121">Zugreifen auf Einstellungen von einem Controller</span><span class="sxs-lookup"><span data-stu-id="c9d63-121">Access settings from a controller</span></span>

<span data-ttu-id="c9d63-122">Das Zugreifen auf App- oder Konfigurationseinstellungen von einem Controller aus ist ein häufiges Szenario.</span><span class="sxs-lookup"><span data-stu-id="c9d63-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="c9d63-123">Das in <xref:fundamentals/configuration/options> beschriebene *Optionsmuster* ist der bevorzugte Ansatz, um Einstellungen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="c9d63-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="c9d63-124">Fügen Sie generell <xref:Microsoft.Extensions.Configuration.IConfiguration> nicht direkt in einen Controller ein.</span><span class="sxs-lookup"><span data-stu-id="c9d63-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="c9d63-125">Erstellen Sie eine Klasse, die die Optionen darstellt.</span><span class="sxs-lookup"><span data-stu-id="c9d63-125">Create a class that represents the options.</span></span> <span data-ttu-id="c9d63-126">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c9d63-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="c9d63-127">Hinzufügen der Konfigurationsklasse in die Dienstauflistung:</span><span class="sxs-lookup"><span data-stu-id="c9d63-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="c9d63-128">Konfigurieren Sie die App, sodass sie die Einstellungen aus einer Datei mit JSON-Formatierung liest:</span><span class="sxs-lookup"><span data-stu-id="c9d63-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="c9d63-129">Der folgende Code fordert die `IOptions<SampleWebSettings>`-Einstellungen aus dem Dienstcontainer an, und verwendet sie in der `Index`-Methode:</span><span class="sxs-lookup"><span data-stu-id="c9d63-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c9d63-130">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c9d63-130">Additional resources</span></span>

* <span data-ttu-id="c9d63-131">Informationen zum einfacheren Testen von Code mithilfe der expliziten Anforderung von Abhängigkeiten in Controllern finden Sie unter <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="c9d63-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="c9d63-132">Informationen zum [Ersetzen von Standarddienstcontainern](xref:fundamentals/dependency-injection#default-service-container-replacement) mit einer Drittanbieterimplementierung.</span><span class="sxs-lookup"><span data-stu-id="c9d63-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c9d63-133">Von [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="c9d63-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="c9d63-134">ASP.NET Core MVC-Controller fordern Abhängigkeiten explizit über Konstruktoren an.</span><span class="sxs-lookup"><span data-stu-id="c9d63-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="c9d63-135">ASP.NET Core verfügt über integrierte Unterstützung für [Dependency Injection ( DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c9d63-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c9d63-136">DI erleichtert das Testen und die Wartung von Apps.</span><span class="sxs-lookup"><span data-stu-id="c9d63-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="c9d63-137">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c9d63-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="c9d63-138">Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="c9d63-138">Constructor Injection</span></span>

<span data-ttu-id="c9d63-139">Dienste werden als Konstruktorparameter hinzugefügt, und die Runtime löst den Dienst aus dem Dienstcontainer auf.</span><span class="sxs-lookup"><span data-stu-id="c9d63-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="c9d63-140">Dienste werden meist mithilfe von Schnittstellen definiert.</span><span class="sxs-lookup"><span data-stu-id="c9d63-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="c9d63-141">Stellen Sich sich z.B. eine App vor, für die die aktuelle Uhrzeit erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c9d63-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="c9d63-142">Die folgende Schnittstelle macht den `IDateTime`-Dienst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c9d63-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="c9d63-143">Im folgenden Code wird die `IDateTime`-Schnittstelle implementiert:</span><span class="sxs-lookup"><span data-stu-id="c9d63-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="c9d63-144">Fügen Sie anschließend dem Dienstcontainer den Dienst hinzu:</span><span class="sxs-lookup"><span data-stu-id="c9d63-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="c9d63-145">Weitere Informationen zu <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> finden Sie unter [Dependency Injection in ASP.NET Core](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="c9d63-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="c9d63-146">Im folgenden Code sehen Sie eine Begrüßung des Benutzers, die sich an der jeweiligen Tageszeit orientiert:</span><span class="sxs-lookup"><span data-stu-id="c9d63-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="c9d63-147">Führen Sie die App aus, und eine Nachricht wird angezeigt, die von der Tageszeit abhängt.</span><span class="sxs-lookup"><span data-stu-id="c9d63-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="c9d63-148">Action Injection mit FromServices</span><span class="sxs-lookup"><span data-stu-id="c9d63-148">Action injection with FromServices</span></span>

<span data-ttu-id="c9d63-149">Mit dem <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> ist es möglich, einen Dienst direkt in eine Aktionsmethode einzufügen, ohne eine Constructor Injection verwenden zu müssen:</span><span class="sxs-lookup"><span data-stu-id="c9d63-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="c9d63-150">Zugreifen auf Einstellungen von einem Controller</span><span class="sxs-lookup"><span data-stu-id="c9d63-150">Access settings from a controller</span></span>

<span data-ttu-id="c9d63-151">Das Zugreifen auf App- oder Konfigurationseinstellungen von einem Controller aus ist ein häufiges Szenario.</span><span class="sxs-lookup"><span data-stu-id="c9d63-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="c9d63-152">Das in <xref:fundamentals/configuration/options> beschriebene *Optionsmuster* ist der bevorzugte Ansatz, um Einstellungen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="c9d63-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="c9d63-153">Fügen Sie generell <xref:Microsoft.Extensions.Configuration.IConfiguration> nicht direkt in einen Controller ein.</span><span class="sxs-lookup"><span data-stu-id="c9d63-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="c9d63-154">Erstellen Sie eine Klasse, die die Optionen darstellt.</span><span class="sxs-lookup"><span data-stu-id="c9d63-154">Create a class that represents the options.</span></span> <span data-ttu-id="c9d63-155">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c9d63-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="c9d63-156">Hinzufügen der Konfigurationsklasse in die Dienstauflistung:</span><span class="sxs-lookup"><span data-stu-id="c9d63-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="c9d63-157">Konfigurieren Sie die App, sodass sie die Einstellungen aus einer Datei mit JSON-Formatierung liest:</span><span class="sxs-lookup"><span data-stu-id="c9d63-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="c9d63-158">Der folgende Code fordert die `IOptions<SampleWebSettings>`-Einstellungen aus dem Dienstcontainer an, und verwendet sie in der `Index`-Methode:</span><span class="sxs-lookup"><span data-stu-id="c9d63-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c9d63-159">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c9d63-159">Additional resources</span></span>

* <span data-ttu-id="c9d63-160">Informationen zum einfacheren Testen von Code mithilfe der expliziten Anforderung von Abhängigkeiten in Controllern finden Sie unter <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="c9d63-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="c9d63-161">Informationen zum [Ersetzen von Standarddienstcontainern](xref:fundamentals/dependency-injection#default-service-container-replacement) mit einer Drittanbieterimplementierung.</span><span class="sxs-lookup"><span data-stu-id="c9d63-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end
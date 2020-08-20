---
title: Einführung in ASP.NET Core
author: rick-anderson
description: Dieser Artikel enthält eine Einführung in ASP.NET Core, ein plattformübergreifendes, leistungsstarkes Open-Source-Framework für das Erstellen moderner, cloudfähiger Apps, die mit dem Internet verbunden sind.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: f1235337748bacdb6eaa775a5853238bf729e605
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634552"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="5b3d3-103">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b3d3-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="5b3d3-104">Von [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="5b3d3-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5b3d3-105">ASP.NET Core ist ein plattformübergreifendes, leistungsstarkes [Open-Source](https://github.com/dotnet/aspnetcore)-Framework zum Erstellen von modernen, cloudfähigen und mit dem Internet verbundenen Apps.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="5b3d3-106">ASP.NET Core ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="5b3d3-107">Erstellen von Web-Apps und -diensten, [IoT-Apps](https://www.microsoft.com/internet-of-things/) (Internet der Dinge) und mobilen Back-Ends</span><span class="sxs-lookup"><span data-stu-id="5b3d3-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="5b3d3-108">Verwenden Ihrer bevorzugten Entwicklungstools unter Windows, macOS und Linux</span><span class="sxs-lookup"><span data-stu-id="5b3d3-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="5b3d3-109">Bereitstellen in der Cloud oder im lokalen System</span><span class="sxs-lookup"><span data-stu-id="5b3d3-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="5b3d3-110">Ausführen in [.NET Core](/dotnet/core/introduction)</span><span class="sxs-lookup"><span data-stu-id="5b3d3-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="5b3d3-111">Was spricht für ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="5b3d3-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="5b3d3-112">Millionen von Entwicklern verwenden bei der Erstellung von Web-Apps [ASP.NET 4.x](/aspnet/overview) oder haben es verwendet.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="5b3d3-113">Bei ASP.NET Core handelt es sich um eine Neugestaltung von ASP.NET 4.x einschließlich Änderungen an der Architektur, die ein schlankeres Framework mit größerer Modularität ergeben.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="5b3d3-114">Erstellen von Web-APIs und Webbenutzeroberflächen mithilfe von ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5b3d3-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="5b3d3-115">ASP.NET Core MVC bietet Funktionen zum Erstellen von [Web-APIs](xref:tutorials/first-web-api) und [Web-Apps](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="5b3d3-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="5b3d3-116">Das Muster [Model-View-Controller (MVC)](xref:mvc/overview) sorgt dafür, dass Ihre Web-APIs und Web-Apps testfähig sind.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="5b3d3-117">[Razor Pages](xref:razor-pages/index) ist ein seitenbasiertes Programmiermodell, mit dem Sie Webbenutzeroberflächen einfacher und schneller erstellen können.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="5b3d3-118">Das [Razor-Markup](xref:mvc/views/razor) bietet eine produktive Syntax für [Razor Pages](xref:razor-pages/index) und [MVC-Ansichten](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="5b3d3-119">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="5b3d3-120">Die integrierte Unterstützung für [mehrere Datenformate und Inhaltsaushandlung](xref:web-api/advanced/formatting) ermöglicht Ihren Web-APIs das Erreichen einer breiten Palette von Clients, wie z.B. Browser und Mobilgeräte.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="5b3d3-121">Die [Modellbindung](xref:mvc/models/model-binding) ordnet Daten aus HTTP-Anforderungen automatisch Aktionsmethodenparametern zu.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="5b3d3-122">Die [Modellvalidierung](xref:mvc/models/validation) führt automatisch eine client- und serverseitige Validierung aus.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="5b3d3-123">Clientseitige Entwicklung</span><span class="sxs-lookup"><span data-stu-id="5b3d3-123">Client-side development</span></span>

<span data-ttu-id="5b3d3-124">ASP.NET Core integriert sich nahtlos in gängige clientseitige Frameworks und Bibliotheken, einschließlich [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) und [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="5b3d3-125">Weitere Informationen finden Sie unter <xref:blazor/index> und verwandten Themen unter *Clientseitige Entwicklung*.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="5b3d3-126">ASP.NET Core-Zielframeworks</span><span class="sxs-lookup"><span data-stu-id="5b3d3-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="5b3d3-127">ASP.NET Core 3.x und höher können nur .NET Core als Ziel haben.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="5b3d3-128">Allgemein besteht ASP.NET Core aus [.NET Standard](/dotnet/standard/net-standard)-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="5b3d3-129">Bibliotheken, die mit .NET Standard 2.0 geschrieben wurden, werden auf [jeder .NET-Plattform ausgeführt, die .NET Standard 2.0 implementiert](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="5b3d3-130">Das Anzielen auf .NET Core bringt mit jedem Release mehr und mehr Vorteile mit sich.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="5b3d3-131">Einige Vorteile von .NET Core gegenüber .NET Framework sind:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="5b3d3-132">Plattformübergreifend</span><span class="sxs-lookup"><span data-stu-id="5b3d3-132">Cross-platform.</span></span> <span data-ttu-id="5b3d3-133">Die Ausführung unter Windows, macOS und Linux ist möglich.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="5b3d3-134">Leistungssteigerung</span><span class="sxs-lookup"><span data-stu-id="5b3d3-134">Improved performance</span></span>
* [<span data-ttu-id="5b3d3-135">Parallele Versionsverwaltung</span><span class="sxs-lookup"><span data-stu-id="5b3d3-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="5b3d3-136">Neue APIs</span><span class="sxs-lookup"><span data-stu-id="5b3d3-136">New APIs</span></span>
* <span data-ttu-id="5b3d3-137">Quelle öffnen</span><span class="sxs-lookup"><span data-stu-id="5b3d3-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="5b3d3-138">Empfohlener Lernpfad</span><span class="sxs-lookup"><span data-stu-id="5b3d3-138">Recommended learning path</span></span>

<span data-ttu-id="5b3d3-139">Als Einführung in die Entwicklung von ASP.NET Core-Apps empfehlen wir die folgenden Tutorials:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="5b3d3-140">Führen Sie die Schritte eines Tutorials für den App-Typ aus, den Sie entwickeln oder verwalten möchten.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="5b3d3-141">App-Typ</span><span class="sxs-lookup"><span data-stu-id="5b3d3-141">App type</span></span>  |<span data-ttu-id="5b3d3-142">Szenario</span><span class="sxs-lookup"><span data-stu-id="5b3d3-142">Scenario</span></span>  |<span data-ttu-id="5b3d3-143">Lernprogramm</span><span class="sxs-lookup"><span data-stu-id="5b3d3-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="5b3d3-144">Web-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-144">Web app</span></span>                   | <span data-ttu-id="5b3d3-145">Neue serverseitige Webbenutzeroberflächenentwicklung</span><span class="sxs-lookup"><span data-stu-id="5b3d3-145">New server-side web UI development</span></span> |[<span data-ttu-id="5b3d3-146">Erste Schritte mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5b3d3-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="5b3d3-147">Web-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-147">Web app</span></span>                   | <span data-ttu-id="5b3d3-148">Verwalten einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="5b3d3-149">Erste Schritte mit MVC</span><span class="sxs-lookup"><span data-stu-id="5b3d3-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="5b3d3-150">Web-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-150">Web app</span></span>                   | <span data-ttu-id="5b3d3-151">Clientseitige Webbenutzeroberflächenentwicklung</span><span class="sxs-lookup"><span data-stu-id="5b3d3-151">Client-side web UI development</span></span> |[<span data-ttu-id="5b3d3-152">Erste Schritte mit Blazor</span><span class="sxs-lookup"><span data-stu-id="5b3d3-152">Get started with Blazor</span></span>](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |<span data-ttu-id="5b3d3-153">Web-API</span><span class="sxs-lookup"><span data-stu-id="5b3d3-153">Web API</span></span>                   | <span data-ttu-id="5b3d3-154">RESTful-HTTP-Dienste</span><span class="sxs-lookup"><span data-stu-id="5b3d3-154">RESTful HTTP services</span></span> |<span data-ttu-id="5b3d3-155">[Erstellen einer Web-API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="5b3d3-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="5b3d3-156">Apps für Remoteprozeduraufrufe</span><span class="sxs-lookup"><span data-stu-id="5b3d3-156">Remote Procedure Call app</span></span> | <span data-ttu-id="5b3d3-157">Contract-First-Dienste mithilfe von Protokollpuffern</span><span class="sxs-lookup"><span data-stu-id="5b3d3-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="5b3d3-158">Erste Schritte mit dem gRPC-Dienst</span><span class="sxs-lookup"><span data-stu-id="5b3d3-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="5b3d3-159">Echtzeit-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-159">Real-time app</span></span>             | <span data-ttu-id="5b3d3-160">Bidirektionale Kommunikation zwischen Servern und verbundenen Clients</span><span class="sxs-lookup"><span data-stu-id="5b3d3-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="5b3d3-161">Erste Schritte mit SignalR</span><span class="sxs-lookup"><span data-stu-id="5b3d3-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="5b3d3-162">Führen Sie die Schritte eines Tutorials aus, in dem die Grundlagen des Datenzugriffs erläutert werden.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="5b3d3-163">Szenario</span><span class="sxs-lookup"><span data-stu-id="5b3d3-163">Scenario</span></span>  |<span data-ttu-id="5b3d3-164">Lernprogramm</span><span class="sxs-lookup"><span data-stu-id="5b3d3-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="5b3d3-165">Neuentwicklungen</span><span class="sxs-lookup"><span data-stu-id="5b3d3-165">New development</span></span>        |[<span data-ttu-id="5b3d3-166">Razor Pages mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5b3d3-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="5b3d3-167">Verwalten einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="5b3d3-168">MVC mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5b3d3-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="5b3d3-169">Lesen Sie die Übersicht über die ASP.NET Core[-Grundlagen](xref:fundamentals/index), die für alle App-Typen gelten.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="5b3d3-170">Suchen Sie im Inhaltsverzeichnis nach weiteren interessanten Themen.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="5b3d3-171">&dagger;Es gibt auch ein [interaktives Web-API-Tutorial](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="5b3d3-172">Es ist keine lokale Installation von Entwicklertools erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-172">No local installation of development tools is required.</span></span> <span data-ttu-id="5b3d3-173">Der Code wird in [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in Ihrem Browser ausgeführt, und zum Testen wird [curl](https://curl.haxx.se/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="5b3d3-174">Migration von .NET Framework</span><span class="sxs-lookup"><span data-stu-id="5b3d3-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="5b3d3-175">Eine Referenzanleitung für die Migration von ASP.NET 4.x-Apps zu ASP.NET Core finden Sie unter <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5b3d3-176">ASP.NET Core ist ein plattformübergreifendes, leistungsstarkes [Open-Source](https://github.com/dotnet/aspnetcore)-Framework zum Erstellen von modernen, cloudfähigen und mit dem Internet verbundenen Apps.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="5b3d3-177">ASP.NET Core ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="5b3d3-178">Erstellen von Web-Apps und -diensten, [IoT-Apps](https://www.microsoft.com/internet-of-things/) (Internet der Dinge) und mobilen Back-Ends</span><span class="sxs-lookup"><span data-stu-id="5b3d3-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="5b3d3-179">Verwenden Ihrer bevorzugten Entwicklungstools unter Windows, macOS und Linux</span><span class="sxs-lookup"><span data-stu-id="5b3d3-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="5b3d3-180">Bereitstellen in der Cloud oder im lokalen System</span><span class="sxs-lookup"><span data-stu-id="5b3d3-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="5b3d3-181">Ausführen in [.NET Core oder .NET Framework](/dotnet/articles/standard/choosing-core-framework-server)</span><span class="sxs-lookup"><span data-stu-id="5b3d3-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="5b3d3-182">Was spricht für ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="5b3d3-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="5b3d3-183">Millionen von Entwicklern verwenden bei der Erstellung von Web-Apps [ASP.NET 4.x](/aspnet/overview) oder haben es verwendet.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="5b3d3-184">Bei ASP.NET Core handelt es sich um eine Neugestaltung von ASP.NET 4.x mit Änderungen an der Architektur, die ein schlankeres Framework mit größerer Modularität ergeben.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="5b3d3-185">Erstellen von Web-APIs und Webbenutzeroberflächen mithilfe von ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5b3d3-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="5b3d3-186">ASP.NET Core MVC bietet Funktionen zum Erstellen von [Web-APIs](xref:tutorials/first-web-api) und [Web-Apps](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="5b3d3-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="5b3d3-187">Das Muster [Model-View-Controller (MVC)](xref:mvc/overview) sorgt dafür, dass Ihre Web-APIs und Web-Apps testfähig sind.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="5b3d3-188">[Razor Pages](xref:razor-pages/index) ist ein seitenbasiertes Programmiermodell, mit dem Sie Webbenutzeroberflächen einfacher und schneller erstellen können.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="5b3d3-189">Das [Razor-Markup](xref:mvc/views/razor) bietet eine produktive Syntax für [Razor Pages](xref:razor-pages/index) und [MVC-Ansichten](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="5b3d3-190">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="5b3d3-191">Die integrierte Unterstützung für [mehrere Datenformate und Inhaltsaushandlung](xref:web-api/advanced/formatting) ermöglicht Ihren Web-APIs das Erreichen einer breiten Palette von Clients, wie z.B. Browser und Mobilgeräte.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="5b3d3-192">Die [Modellbindung](xref:mvc/models/model-binding) ordnet Daten aus HTTP-Anforderungen automatisch Aktionsmethodenparametern zu.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="5b3d3-193">Die [Modellvalidierung](xref:mvc/models/validation) führt automatisch eine client- und serverseitige Validierung aus.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="5b3d3-194">Clientseitige Entwicklung</span><span class="sxs-lookup"><span data-stu-id="5b3d3-194">Client-side development</span></span>

<span data-ttu-id="5b3d3-195">ASP.NET Core integriert sich nahtlos in gängige clientseitige Frameworks und Bibliotheken, einschließlich [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) und [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="5b3d3-196">Weitere Informationen finden Sie unter <xref:blazor/index> und verwandten Themen unter *Clientseitige Entwicklung*.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="5b3d3-197">ASP.NET Core, das .NET Framework anzielt.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="5b3d3-198">ASP.NET Core 2.x kann für .NET Core oder .NET Framework verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="5b3d3-199">ASP.NET Core-Apps, die .NET Framework anzielen, sind nicht plattformübergreifend, sondern können nur unter Windows ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="5b3d3-200">Für gewöhnlich besteht ASP.NET Core 2.x aus [.NET Standard](/dotnet/standard/net-standard)-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="5b3d3-201">Bibliotheken, die mit .NET Standard 2.0 geschrieben wurden, werden auf [jeder .NET-Plattform ausgeführt, die .NET Standard 2.0 implementiert](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="5b3d3-202">ASP.NET Core 2.x wird unter .NET Framework-Versionen unterstützt, die .NET Standard 2.0 implementieren:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="5b3d3-203">Die neueste Version von .NET Framework wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="5b3d3-204">.NET Framework 4.6.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="5b3d3-205">ASP.NET Core 3.0 und höher kann nur in .NET Core ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="5b3d3-206">Weitere Informationen zu dieser Änderung finden Sie im Blogbeitrag zu [kommenden Änderungen in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="5b3d3-207">Das Anzielen auf .NET Core bringt mit jedem Release mehr und mehr Vorteile mit sich.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="5b3d3-208">Einige Vorteile von .NET Core gegenüber .NET Framework sind:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="5b3d3-209">Plattformübergreifend</span><span class="sxs-lookup"><span data-stu-id="5b3d3-209">Cross-platform.</span></span> <span data-ttu-id="5b3d3-210">Wird unter macOS, Linux und Windows ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="5b3d3-211">Leistungssteigerung</span><span class="sxs-lookup"><span data-stu-id="5b3d3-211">Improved performance</span></span>
* [<span data-ttu-id="5b3d3-212">Parallele Versionsverwaltung</span><span class="sxs-lookup"><span data-stu-id="5b3d3-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="5b3d3-213">Neue APIs</span><span class="sxs-lookup"><span data-stu-id="5b3d3-213">New APIs</span></span>
* <span data-ttu-id="5b3d3-214">Quelle öffnen</span><span class="sxs-lookup"><span data-stu-id="5b3d3-214">Open source</span></span>

<span data-ttu-id="5b3d3-215">Das [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) stellt Tausende nur unter Windows verfügbare APIs in .NET Core zur Verfügung, um die API-Lücke von .NET Framework zu .NET Core zu schließen.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="5b3d3-216">Diese APIs waren in .NET Core 1.x nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="5b3d3-217">Empfohlener Lernpfad</span><span class="sxs-lookup"><span data-stu-id="5b3d3-217">Recommended learning path</span></span>

<span data-ttu-id="5b3d3-218">Als Einführung in die Entwicklung von ASP.NET Core-Apps empfehlen wir die folgenden Tutorials und Artikel:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="5b3d3-219">Führen Sie die Schritte eines Tutorials für den App-Typ aus, den Sie entwickeln oder verwalten möchten.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="5b3d3-220">App-Typ</span><span class="sxs-lookup"><span data-stu-id="5b3d3-220">App type</span></span>  |<span data-ttu-id="5b3d3-221">Szenario</span><span class="sxs-lookup"><span data-stu-id="5b3d3-221">Scenario</span></span>  |<span data-ttu-id="5b3d3-222">Lernprogramm</span><span class="sxs-lookup"><span data-stu-id="5b3d3-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="5b3d3-223">Web-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-223">Web app</span></span>                   | <span data-ttu-id="5b3d3-224">Für Neuentwicklungen</span><span class="sxs-lookup"><span data-stu-id="5b3d3-224">For new development</span></span>        |[<span data-ttu-id="5b3d3-225">Erste Schritte mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5b3d3-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="5b3d3-226">Web-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-226">Web app</span></span>                   | <span data-ttu-id="5b3d3-227">Für die Verwaltung einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="5b3d3-228">Erste Schritte mit MVC</span><span class="sxs-lookup"><span data-stu-id="5b3d3-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="5b3d3-229">Web-API</span><span class="sxs-lookup"><span data-stu-id="5b3d3-229">Web API</span></span>                   |                            |<span data-ttu-id="5b3d3-230">[Erstellen einer Web-API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="5b3d3-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="5b3d3-231">Echtzeit-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-231">Real-time app</span></span>             |                            |[<span data-ttu-id="5b3d3-232">Erste Schritte mit SignalR</span><span class="sxs-lookup"><span data-stu-id="5b3d3-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="5b3d3-233">Führen Sie die Schritte eines Tutorials aus, in dem die Grundlagen des Datenzugriffs erläutert werden.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="5b3d3-234">Szenario</span><span class="sxs-lookup"><span data-stu-id="5b3d3-234">Scenario</span></span>  |<span data-ttu-id="5b3d3-235">Lernprogramm</span><span class="sxs-lookup"><span data-stu-id="5b3d3-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="5b3d3-236">Für Neuentwicklungen</span><span class="sxs-lookup"><span data-stu-id="5b3d3-236">For new development</span></span>        |[<span data-ttu-id="5b3d3-237">Razor Pages mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5b3d3-237">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="5b3d3-238">Für die Verwaltung einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="5b3d3-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="5b3d3-239">MVC mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5b3d3-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="5b3d3-240">Lesen Sie die Übersicht über die ASP.NET Core[-Grundlagen](xref:fundamentals/index), die für alle App-Typen gelten.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="5b3d3-241">Suchen Sie im Inhaltsverzeichnis nach weiteren interessanten Themen.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="5b3d3-242">&dagger;Es gibt auch ein [Web-API-Tutorial, das Sie vollständig im Browser ausführen können](/learn/modules/build-web-api-net-core), ohne dass eine lokale integrierte Entwicklungsumgebung installiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="5b3d3-243">Der Code wird in einer [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) ausgeführt, und zum Testen wird [curl](https://curl.haxx.se/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="5b3d3-244">Migration von .NET Framework</span><span class="sxs-lookup"><span data-stu-id="5b3d3-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="5b3d3-245">Eine Referenzanleitung für die Migration von ASP.NET-Apps zu ASP.NET Core finden Sie unter <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="5b3d3-246">Herunterladen eines Beispiels</span><span class="sxs-lookup"><span data-stu-id="5b3d3-246">How to download a sample</span></span>

<span data-ttu-id="5b3d3-247">Viele der Artikel und Tutorials enthalten Links zu Beispielcode.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="5b3d3-248">[Laden Sie die Zip-Datei des ASP.NET Repositorys herunter](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="5b3d3-249">Entpacken Sie die Datei *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="5b3d3-250">Navigieren Sie mit der URL in der Beispielverknüpfung zum Beispielverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="5b3d3-251">Präprozessoranweisungen in Beispielcode</span><span class="sxs-lookup"><span data-stu-id="5b3d3-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="5b3d3-252">In Beispiel-Apps werden die Präprozessoranweisungen `#define` und `#if-#else/#elif-#endif` zum selektiven Kompilieren und Ausführen unterschiedlicher Abschnitte des Beispielcodes verwendet. So werden verschiedene Szenarios veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="5b3d3-253">Für die Beispiele, die diesen Ansatz verwenden, legen Sie die Anweisung `#define` am Anfang der C#-Dateien auf das Symbol fest, das dem Szenario zugeordnet ist, welches Sie ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="5b3d3-254">Für einige Beispiele müssen Sie möglicherweise das Symbol in mehreren Dateien definieren, um ein Szenario durchführen zu können.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="5b3d3-255">Die folgende `#define`-Symbolliste gibt beispielsweise an, dass vier Szenarios verfügbar sind (ein Szenario pro Symbol).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="5b3d3-256">Die aktuelle Beispielkonfiguration führt das `TemplateCode`-Szenario aus:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="5b3d3-257">Damit das Beispiel das `ExpandDefault`-Szenario ausführt, definieren Sie das `ExpandDefault`-Symbol und lassen Sie die übrigen Symbole auskommentiert:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="5b3d3-258">Weitere Informationen zur Verwendung von [C#-Präprozessoranweisungen](/dotnet/csharp/language-reference/preprocessor-directives/), um selektiv bestimmte Codeabschnitte zu kompilieren, finden Sie unter [#define (C#-Referenz)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) und [#if (C#-Referenz)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="5b3d3-259">Bereiche in Beispielcode</span><span class="sxs-lookup"><span data-stu-id="5b3d3-259">Regions in sample code</span></span>

<span data-ttu-id="5b3d3-260">Einige Beispiel-Apps enthalten Codeabschnitte, die von den C#-Anweisungen [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) und [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) umschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="5b3d3-261">Das Buildsystem der Dokumentation fügt diese Bereiche in den gerenderten Dokumentationsartikel ein.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="5b3d3-262">Namen von Bereichen enthalten oft das Wort „snippet“ (Ausschnitt).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="5b3d3-263">Im folgenden Beispiel ist ein Bereich mit dem Namen `snippet_WebHostDefaults` enthalten:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="5b3d3-264">In der Markdowndatei wird auf den vorherigen C#-Codeausschnitt mit der folgenden Zeile verwiesen:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="5b3d3-265">Sie können die Anweisungen `#region` und `#endregion`, die den Code umschließen, ohne Weiteres ignorieren (oder entfernen).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="5b3d3-266">Ändern Sie jedoch den Code innerhalb dieser Anweisungen nicht, wenn Sie die im Artikel beschriebenen Beispielszenarios durchführen möchten.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="5b3d3-267">Wenn Sie andere Szenarios ausprobieren möchten, können Sie den Code anpassen.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="5b3d3-268">Weitere Informationen finden Sie unter [Contribute to the ASP.NET documentation: Code snippets (Mitwirken an der ASP.NET-Dokumentation: Codeausschnitte)](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="5b3d3-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5b3d3-269">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5b3d3-269">Next steps</span></span>

<span data-ttu-id="5b3d3-270">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="5b3d3-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="5b3d3-271">ASP.NET Core – Grundlagen</span><span class="sxs-lookup"><span data-stu-id="5b3d3-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="5b3d3-272">Im [wöchentlichen ASP.NET Community Standup](https://live.asp.net/) werden die Fortschritte und Pläne des Teams behandelt.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="5b3d3-273">Zudem werden neue Blogs und Drittanbietersoftware vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="5b3d3-273">It features new blogs and third-party software.</span></span>

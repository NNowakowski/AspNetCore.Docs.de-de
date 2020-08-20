---
title: Klassenbibliotheken für ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Komponenten aus einer externen Komponentenbibliothek in Blazor-Apps einbezogen werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: d933a677a063d50fbe708264106e3ce19400a270
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628572"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="39485-103">Klassenbibliotheken für ASP.NET Core-Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="39485-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="39485-104">Von [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="39485-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="39485-105">Komponenten können in einer [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) projektübergreifend gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="39485-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="39485-106">Eine *Klassenbibliothek für Razor-Komponenten* kann wie folgt einbezogen werden:</span><span class="sxs-lookup"><span data-stu-id="39485-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="39485-107">Über ein anderes Projekt in der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="39485-107">Another project in the solution.</span></span>
* <span data-ttu-id="39485-108">Über ein NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="39485-108">A NuGet package.</span></span>
* <span data-ttu-id="39485-109">Über eine referenzierte .NET-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="39485-109">A referenced .NET library.</span></span>

<span data-ttu-id="39485-110">Genauso wie Komponenten normale .NET-Typen sind, sind von der RCL bereitgestellte Komponenten normale .NET-Assemblys.</span><span class="sxs-lookup"><span data-stu-id="39485-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="39485-111">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="39485-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39485-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39485-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="39485-113">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="39485-113">Create a new project.</span></span>
1. <span data-ttu-id="39485-114">Klicken Sie auf **Razor-Klassenbibliothek**.</span><span class="sxs-lookup"><span data-stu-id="39485-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="39485-115">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="39485-115">Select **Next**.</span></span>
1. <span data-ttu-id="39485-116">Klicken Sie im Dialogfeld **Neue Razor-Klassenbibliothek erstellen** auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="39485-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="39485-117">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="39485-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="39485-118">Die Beispiele in diesem Thema verwenden den Projektnamen `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="39485-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="39485-119">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="39485-119">Select **Create**.</span></span>
1. <span data-ttu-id="39485-120">Fügen Sie die RCL zu einer Projektmappe hinzu:</span><span class="sxs-lookup"><span data-stu-id="39485-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="39485-121">Klicken Sie mit der rechten Maustaste auf die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="39485-121">Right-click the solution.</span></span> <span data-ttu-id="39485-122">Wählen Sie **Hinzufügen** > **Vorhandenes Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="39485-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="39485-123">Navigieren Sie zur Projektdatei der RCL.</span><span class="sxs-lookup"><span data-stu-id="39485-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="39485-124">Wählen Sie die Projektdatei (`.csproj`) der RCL aus.</span><span class="sxs-lookup"><span data-stu-id="39485-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="39485-125">Fügen Sie einen Verweis auf die RCL aus der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="39485-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="39485-126">Klicken Sie mit der rechten Maustaste auf das App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="39485-126">Right-click the app project.</span></span> <span data-ttu-id="39485-127">Wählen Sie **Hinzufügen** > **Verweis** aus.</span><span class="sxs-lookup"><span data-stu-id="39485-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="39485-128">Wählen Sie das RCL-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="39485-128">Select the RCL project.</span></span> <span data-ttu-id="39485-129">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="39485-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="39485-130">Wenn das Kontrollkästchen **Seiten und Ansichten unterstützen** beim Erstellen der RCL aus der Vorlage aktiviert ist, fügen Sie auch eine `_Imports.razor`-Datei mit folgendem Inhalt zum Stammverzeichnis des generierten Projekts hinzu, um das Erstellen von Razor-Komponenten zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="39485-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="39485-131">Fügen Sie die Datei manuell dem Stammverzeichnis des generierten Projekts hinzu.</span><span class="sxs-lookup"><span data-stu-id="39485-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="39485-132">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="39485-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="39485-133">Verwenden Sie die Vorlage **Razor-Klassenbibliothek** (`razorclasslib`) mit dem Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="39485-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="39485-134">Im folgenden Beispiel wird eine RCL mit dem Namen `ComponentLibrary` erstellt.</span><span class="sxs-lookup"><span data-stu-id="39485-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="39485-135">Der Ordner, der `ComponentLibrary` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="39485-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="39485-136">Wenn der Switch `-s|--support-pages-and-views` beim Erstellen der RCL aus der Vorlage verwendet wird, fügen Sie auch eine `_Imports.razor`-Datei mit folgendem Inhalt zum Stammverzeichnis des generierten Projekts hinzu, um das Erstellen von Razor-Komponenten zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="39485-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="39485-137">Fügen Sie die Datei manuell dem Stammverzeichnis des generierten Projekts hinzu.</span><span class="sxs-lookup"><span data-stu-id="39485-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="39485-138">Verwenden Sie den Befehl [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell, um die Bibliothek zu einem bestehenden Projekt hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="39485-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="39485-139">Im folgenden Beispiel wird die RCL der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="39485-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="39485-140">Führen Sie den folgenden Befehl aus dem Projektordner der App mit dem Pfad zur Bibliothek aus:</span><span class="sxs-lookup"><span data-stu-id="39485-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="39485-141">Verwenden einer Bibliothekskomponente</span><span class="sxs-lookup"><span data-stu-id="39485-141">Consume a library component</span></span>

<span data-ttu-id="39485-142">Um Komponenten, die in einer Bibliothek definiert sind, in einem anderen Projekt zu verwenden, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="39485-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="39485-143">Verwenden Sie den vollständigen Typnamen mit dem Namespace.</span><span class="sxs-lookup"><span data-stu-id="39485-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="39485-144">Verwenden Sie die Razor-Anweisung [`@using`](xref:mvc/views/razor#using).</span><span class="sxs-lookup"><span data-stu-id="39485-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="39485-145">Einzelne Komponenten können über den Namen hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="39485-145">Individual components can be added by name.</span></span>

<span data-ttu-id="39485-146">In den folgenden Beispielen ist `ComponentLibrary` eine Komponentenbibliothek, die die `Component1`-Komponente (`Component1.razor`) enthält.</span><span class="sxs-lookup"><span data-stu-id="39485-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="39485-147">Die `Component1`-Komponente ist eine Beispielkomponente, die bei der Erstellung der Bibliothek automatisch durch die RCL-Projektvorlage hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="39485-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="39485-148">Verweisen Sie mit dem zugehörigen Namespace auf die `Component1`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="39485-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="39485-149">Alternativ können Sie die Bibliothek mit einer [`@using`](xref:mvc/views/razor#using)-Anweisung in den Gültigkeitsbereich einbeziehen und die Komponente ohne ihren Namespace verwenden:</span><span class="sxs-lookup"><span data-stu-id="39485-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="39485-150">Fügen Sie die `@using ComponentLibrary`-Anweisung optional in die `_Import.razor`-Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein ganzes Projekt zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="39485-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="39485-151">Fügen Sie die Anweisung auf beliebiger Ebene zu einer `_Import.razor`-Datei hinzu, um den Namespace auf eine einzelne Komponente oder eine Reihe von Komponenten innerhalb eines Ordners anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="39485-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="39485-152">Um die `my-component`-CSS-Klasse von `Component1` für die Komponente bereitzustellen, stellen Sie eine Verknüpfung mit dem Stylesheet der Bibliothek unter Verwendung der [`Link`-Komponente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) des Frameworks in `Component1.razor` her:</span><span class="sxs-lookup"><span data-stu-id="39485-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="39485-153">Um das Stylesheet in der gesamten App bereitzustellen, können Sie alternativ eine Verknüpfung mit dem Stylesheet der Bibliothek in der `wwwroot/index.html`-Datei (Blazor WebAssembly) oder `Pages/_Host.cshtml`-Datei (Blazor Server) der App erstellen:</span><span class="sxs-lookup"><span data-stu-id="39485-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="39485-154">Wenn die `Link`-Komponente in einer untergeordneten Komponente verwendet wird, ist die verknüpfte Ressource auch für jede andere untergeordnete Komponente der übergeordneten Komponente verfügbar, solange die untergeordnete Komponente mit der `Link`-Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="39485-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="39485-155">Der Unterschied zwischen der Verwendung der `Link`-Komponente in einer untergeordneten Komponente und dem Platzieren eines `<link>`-HTML-Tags in `wwwroot/index.html` oder `Pages/_Host.cshtml` besteht darin, dass das gerenderte HTML-Tag einer Rahmenkomponente:</span><span class="sxs-lookup"><span data-stu-id="39485-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="39485-156">Nach Anwendungszustand geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="39485-156">Can be modified by application state.</span></span> <span data-ttu-id="39485-157">Ein hartcodiertes `<link>`-HTML-Tag kann nicht nach Anwendungszustand geändert werden.</span><span class="sxs-lookup"><span data-stu-id="39485-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="39485-158">Aus dem HTML-`<head>` entfernt wird, wenn die übergeordnete Komponente nicht mehr gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="39485-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="39485-159">Um die `my-component`-CSS-Klasse von `Component1`bereitzustellen, stellen Sie eine Verknüpfung mit dem Stylesheet der Bibliothek in der Datei `wwwroot/index.html` (Blazor WebAssembly) oder der Datei `Pages/_Host.cshtml` (Blazor Server) der App her:</span><span class="sxs-lookup"><span data-stu-id="39485-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="39485-160">Erstellen einer Klassenbibliothek für Razor-Komponenten mit statischen Objekten</span><span class="sxs-lookup"><span data-stu-id="39485-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="39485-161">Eine RCL kann statische Objekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="39485-161">An RCL can include static assets.</span></span> <span data-ttu-id="39485-162">Die statischen Objekte sind für jede App verfügbar, die die Bibliothek nutzt.</span><span class="sxs-lookup"><span data-stu-id="39485-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="39485-163">Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="39485-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="39485-164">Bereitstellen von Komponenten und statischen Ressourcen für mehrere gehostete Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="39485-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="39485-165">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="39485-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="39485-166">Erstellen, Verpacken und Liefern an NuGet</span><span class="sxs-lookup"><span data-stu-id="39485-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="39485-167">Da es sich bei Komponentenbibliotheken um standardmäßige .NET-Bibliotheken handelt, unterscheidet sich das Verpacken und Liefern an NuGet nicht vom Verpacken und Liefern einer beliebigen Bibliothek an NuGet.</span><span class="sxs-lookup"><span data-stu-id="39485-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="39485-168">Das Verpacken wird mit dem Befehl [`dotnet pack`](/dotnet/core/tools/dotnet-pack) in einer Befehlsshell durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="39485-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="39485-169">Laden Sie das Paket mit dem Befehl [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) in einer Befehlsshell in NuGet hoch.</span><span class="sxs-lookup"><span data-stu-id="39485-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39485-170">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="39485-170">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="39485-171">Hinzufügen einer XML-Linkerkonfigurationsdatei zu einer Bibliothek</span><span class="sxs-lookup"><span data-stu-id="39485-171">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

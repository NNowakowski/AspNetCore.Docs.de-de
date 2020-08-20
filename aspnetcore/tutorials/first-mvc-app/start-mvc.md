---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629989"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="bfdd6-103">Erste Schritte mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="bfdd6-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="bfdd6-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bfdd6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="bfdd6-105">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="bfdd6-106">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="bfdd6-107">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bfdd6-108">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="bfdd6-108">Create a web app.</span></span>
> * <span data-ttu-id="bfdd6-109">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="bfdd6-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="bfdd6-110">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="bfdd6-110">Work with a database.</span></span>
> * <span data-ttu-id="bfdd6-111">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="bfdd6-111">Add search and validation.</span></span>

<span data-ttu-id="bfdd6-112">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="bfdd6-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="bfdd6-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdd6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdd6-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdd6-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdd6-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdd6-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bfdd6-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="bfdd6-117">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="bfdd6-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdd6-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdd6-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bfdd6-119">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="bfdd6-120">Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="bfdd6-122">Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="bfdd6-123">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)

* <span data-ttu-id="bfdd6-125">Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="bfdd6-126">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="bfdd6-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="bfdd6-127">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="bfdd6-128">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="bfdd6-129">Hierbei handelt es sich um ein grundlegendes Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdd6-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdd6-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bfdd6-131">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="bfdd6-132">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="bfdd6-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="bfdd6-133">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="bfdd6-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="bfdd6-134">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="bfdd6-135">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="bfdd6-136">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="bfdd6-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="bfdd6-137">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-137">Select **Yes**</span></span>

  * <span data-ttu-id="bfdd6-138">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="bfdd6-139">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdd6-140">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bfdd6-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bfdd6-141">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-141">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="bfdd6-143">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="bfdd6-144">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="bfdd6-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="bfdd6-146">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="bfdd6-147">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="bfdd6-148">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="bfdd6-149">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-149">Select **Next**.</span></span>

* <span data-ttu-id="bfdd6-150">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="bfdd6-152">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="bfdd6-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdd6-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdd6-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bfdd6-154">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="bfdd6-155">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="bfdd6-156">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bfdd6-157">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="bfdd6-158">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="bfdd6-159">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="bfdd6-160">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="bfdd6-161">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="bfdd6-163">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="bfdd6-165">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-165">The following image shows the app:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdd6-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdd6-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bfdd6-168">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="bfdd6-169">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="bfdd6-170">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="bfdd6-171">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="bfdd6-172">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="bfdd6-173">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="bfdd6-174">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdd6-176">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bfdd6-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bfdd6-177">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="bfdd6-178">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="bfdd6-179">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bfdd6-180">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="bfdd6-181">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="bfdd6-182">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="bfdd6-183">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="bfdd6-184">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-184">The following image shows the app:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="bfdd6-186">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bfdd6-187">Nächste</span><span class="sxs-lookup"><span data-stu-id="bfdd6-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="bfdd6-188">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="bfdd6-189">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="bfdd6-190">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bfdd6-191">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="bfdd6-191">Create a web app.</span></span>
> * <span data-ttu-id="bfdd6-192">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="bfdd6-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="bfdd6-193">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="bfdd6-193">Work with a database.</span></span>
> * <span data-ttu-id="bfdd6-194">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="bfdd6-194">Add search and validation.</span></span>

<span data-ttu-id="bfdd6-195">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="bfdd6-196">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="bfdd6-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdd6-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdd6-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdd6-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdd6-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdd6-199">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bfdd6-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="bfdd6-200">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="bfdd6-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdd6-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdd6-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bfdd6-202">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="bfdd6-203">Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="bfdd6-205">Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="bfdd6-206">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)


* <span data-ttu-id="bfdd6-208">Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="bfdd6-209">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="bfdd6-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="bfdd6-210">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="bfdd6-211">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="bfdd6-212">Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdd6-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdd6-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bfdd6-214">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="bfdd6-215">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="bfdd6-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="bfdd6-216">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="bfdd6-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="bfdd6-217">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="bfdd6-218">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="bfdd6-219">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="bfdd6-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="bfdd6-220">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-220">Select **Yes**</span></span>

  * <span data-ttu-id="bfdd6-221">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="bfdd6-222">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdd6-223">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bfdd6-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bfdd6-224">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-224">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="bfdd6-226">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="bfdd6-227">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="bfdd6-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="bfdd6-228">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="bfdd6-229">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="bfdd6-230">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 2.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="bfdd6-231">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-231">Select **Next**.</span></span>

* <span data-ttu-id="bfdd6-232">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="bfdd6-233">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="bfdd6-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdd6-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdd6-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bfdd6-235">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="bfdd6-236">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="bfdd6-237">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bfdd6-238">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="bfdd6-239">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="bfdd6-240">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="bfdd6-241">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="bfdd6-242">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="bfdd6-244">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="bfdd6-246">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="bfdd6-247">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-247">This app doesn't track personal information.</span></span> <span data-ttu-id="bfdd6-248">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="bfdd6-250">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-250">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdd6-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdd6-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bfdd6-253">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="bfdd6-254">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="bfdd6-255">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="bfdd6-256">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="bfdd6-257">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="bfdd6-258">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="bfdd6-259">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="bfdd6-260">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="bfdd6-261">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-261">This app doesn't track personal information.</span></span> <span data-ttu-id="bfdd6-262">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="bfdd6-264">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-264">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdd6-266">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bfdd6-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bfdd6-267">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="bfdd6-268">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="bfdd6-269">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bfdd6-270">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="bfdd6-271">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="bfdd6-272">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="bfdd6-273">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="bfdd6-274">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="bfdd6-275">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-275">This app doesn't track personal information.</span></span> <span data-ttu-id="bfdd6-276">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="bfdd6-278">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="bfdd6-278">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="bfdd6-280">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bfdd6-281">Nächste</span><span class="sxs-lookup"><span data-stu-id="bfdd6-281">Next</span></span>](adding-controller.md)

::: moniker-end

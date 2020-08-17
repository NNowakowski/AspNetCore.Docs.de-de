---
title: 'Teil 2: Hinzufügen eines Modells zu einer Razor Pages-App in ASP.NET Core'
author: rick-anderson
description: Dies ist Teil 2 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6b50f46863a6dabb01bcf0976a42abb504e6f7b7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020456"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="75923-103">Teil 2: Hinzufügen eines Modells zu einer Razor Pages-App in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75923-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="75923-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="75923-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="75923-105">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="75923-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="75923-106">Die Modellklassen der App verwenden [Entity Framework Core](/ef/core) (EF Core), um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="75923-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="75923-107">EF Core ist ein objektrelationaler Mapper (O/RM), der den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="75923-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="75923-108">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="75923-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="75923-109">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="75923-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="75923-110">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="75923-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75923-112">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** und dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="75923-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="75923-113">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="75923-113">Name the folder *Models*.</span></span>

<span data-ttu-id="75923-114">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="75923-114">Right click the *Models* folder.</span></span> <span data-ttu-id="75923-115">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="75923-116">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="75923-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="75923-118">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="75923-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="75923-119">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="75923-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-120">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="75923-121">Klicken Sie im Lösungspad mit der rechten Maustaste auf das Projekt **RazorPagesMovie**, und wählen Sie dann **Hinzufügen** > **Neuer Ordner...** aus. Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="75923-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="75923-122">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei...** .</span><span class="sxs-lookup"><span data-stu-id="75923-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="75923-123">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="75923-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="75923-124">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="75923-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="75923-125">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="75923-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="75923-126">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="75923-127">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="75923-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="75923-128">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="75923-128">Scaffold the movie model</span></span>

<span data-ttu-id="75923-129">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="75923-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="75923-130">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="75923-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75923-132">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="75923-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="75923-133">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="75923-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="75923-134">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="75923-134">Name the folder *Movies*</span></span>

<span data-ttu-id="75923-135">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="75923-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="75923-137">Klicken Sie im Dialogfeld **Gerüst hinzufügen** auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="75923-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="75923-139">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="75923-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="75923-140">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="75923-141">Klicken Sie in der Zeile **Datenkontextklasse** auf das Pluszeichen **+** , und ändern Sie den generierten Namen von RazorPagesMovie.**Models**.RazorPagesMovieContext in RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="75923-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="75923-142">[Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="75923-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="75923-143">Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.</span><span class="sxs-lookup"><span data-stu-id="75923-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="75923-144">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-144">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/3/arp.png)

<span data-ttu-id="75923-146">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="75923-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="75923-148">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="75923-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="75923-149">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="75923-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="75923-150">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="75923-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="75923-151">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="75923-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-152">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="75923-153">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="75923-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="75923-154">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="75923-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="75923-155">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="75923-155">Name the folder *Movies*</span></span>

<span data-ttu-id="75923-156">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neuer Gerüstbau...** .</span><span class="sxs-lookup"><span data-stu-id="75923-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

<span data-ttu-id="75923-158">Klicken Sie im Dialogfeld **Add New Scaffolding** (Neuen Gerüstbau hinzufügen) auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="75923-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="75923-160">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="75923-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="75923-161">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus, oder geben Sie diesen ein.</span><span class="sxs-lookup"><span data-stu-id="75923-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="75923-162">Geben Sie in der Zeile **Datenkontextklasse** den Namen der neuen Klasse ein: RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="75923-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="75923-163">[Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="75923-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="75923-164">Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.</span><span class="sxs-lookup"><span data-stu-id="75923-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="75923-165">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-165">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arpMac.png)

<span data-ttu-id="75923-167">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="75923-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="75923-168">Hinzufügen von EF-Tools</span><span class="sxs-lookup"><span data-stu-id="75923-168">Add EF tools</span></span>

<span data-ttu-id="75923-169">Führen Sie den folgenden .NET Core-CLI-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="75923-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="75923-170">Der vorangehende Befehl fügt die Entity Framework Core-Tools für die .NET Core-CLI hinzu.</span><span class="sxs-lookup"><span data-stu-id="75923-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="75923-171">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="75923-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75923-173">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="75923-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="75923-174">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="75923-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="75923-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="75923-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="75923-176">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="75923-176">Updated</span></span>

* <span data-ttu-id="75923-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="75923-177">*Startup.cs*</span></span>

<span data-ttu-id="75923-178">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="75923-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-179">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="75923-180">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="75923-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="75923-181">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="75923-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="75923-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="75923-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="75923-183">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="75923-183">Updated</span></span>

* <span data-ttu-id="75923-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="75923-184">*Startup.cs*</span></span>

<span data-ttu-id="75923-185">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="75923-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="75923-187">Der Gerüstbauprozess erstellt die folgenden Dateien:</span><span class="sxs-lookup"><span data-stu-id="75923-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="75923-188">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="75923-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="75923-189">Die erstellten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="75923-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="75923-190">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="75923-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75923-192">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="75923-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="75923-193">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="75923-193">Add an initial migration.</span></span>
* <span data-ttu-id="75923-194">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="75923-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="75923-195">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="75923-197">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="75923-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-199">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="75923-200">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="75923-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="75923-201">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="75923-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="75923-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")</span><span class="sxs-lookup"><span data-stu-id="75923-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="75923-203">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="75923-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="75923-204">Mit dem Migrationsbefehl wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="75923-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="75923-205">Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="75923-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="75923-206">Das Argument `InitialCreate` wird verwendet, um die Migrationen zu benennen.</span><span class="sxs-lookup"><span data-stu-id="75923-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="75923-207">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="75923-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="75923-208">Der `update`-Befehl führt die `Up`-Methode in Migrationen aus, die nicht angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="75923-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="75923-209">In diesem Fall führt `update` die `Up`-Methode in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="75923-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="75923-211">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="75923-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="75923-212">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="75923-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="75923-213">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="75923-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="75923-214">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="75923-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="75923-215">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="75923-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="75923-216">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="75923-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="75923-217">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75923-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="75923-218">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="75923-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="75923-219">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="75923-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="75923-220">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="75923-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="75923-221">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="75923-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="75923-222">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="75923-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="75923-223">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="75923-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="75923-224">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="75923-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="75923-225">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="75923-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="75923-226">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="75923-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="75923-228">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="75923-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-229">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="75923-230">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="75923-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="75923-231">Testen der App</span><span class="sxs-lookup"><span data-stu-id="75923-231">Test the app</span></span>

* <span data-ttu-id="75923-232">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="75923-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="75923-233">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="75923-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="75923-234">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="75923-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="75923-235">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="75923-235">Test the **Create** link.</span></span>

  ![Seite „Create“](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="75923-237">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="75923-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="75923-238">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="75923-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="75923-239">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="75923-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="75923-240">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="75923-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="75923-241">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="75923-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75923-242">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="75923-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="75923-243">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="75923-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="75923-244">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer plattformübergreifenden [SQLite-Datenbank](https://www.sqlite.org/index.html) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="75923-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="75923-245">Apps, die aus einer ASP.NET Core-Vorlage erstellt werden, verwenden eine SQLite-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="75923-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="75923-246">Die Modellklassen der App werden mit [Entity Framework Core (EF Core)](/ef/core) ([SQLite-EF Core-Datenbankanbieter](/ef/core/providers/sqlite)) verwendet, um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="75923-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="75923-247">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="75923-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="75923-248">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="75923-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="75923-249">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="75923-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="75923-250">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="75923-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75923-252">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** und dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="75923-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="75923-253">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="75923-253">Name the folder *Models*.</span></span>

<span data-ttu-id="75923-254">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="75923-254">Right click the *Models* folder.</span></span> <span data-ttu-id="75923-255">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="75923-256">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="75923-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="75923-258">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="75923-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="75923-259">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="75923-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-260">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="75923-261">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **RazorPagesMovie**, und wählen Sie dann **Hinzufügen** > **Neuer Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="75923-262">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="75923-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="75923-263">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="75923-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="75923-264">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="75923-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="75923-265">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="75923-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="75923-266">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="75923-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="75923-267">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="75923-268">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="75923-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="75923-269">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="75923-269">Scaffold the movie model</span></span>

<span data-ttu-id="75923-270">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="75923-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="75923-271">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="75923-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75923-273">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="75923-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="75923-274">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="75923-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="75923-275">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="75923-275">Name the folder *Movies*</span></span>

<span data-ttu-id="75923-276">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="75923-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="75923-278">Klicken Sie im Dialogfeld **Gerüst hinzufügen** auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="75923-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="75923-280">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="75923-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="75923-281">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="75923-282">Klicken Sie in der Zeile **Datenkontextklasse** auf das Pluszeichen **+** , und akzeptieren Sie den generierten Namen **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="75923-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="75923-283">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-283">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arp.png)

<span data-ttu-id="75923-285">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="75923-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="75923-287">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="75923-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="75923-288">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="75923-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="75923-289">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="75923-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-290">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="75923-291">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="75923-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="75923-292">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="75923-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="75923-293">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="75923-293">Name the folder *Movies*</span></span>

<span data-ttu-id="75923-294">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="75923-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

<span data-ttu-id="75923-296">Klicken Sie im Dialogfeld **Add New Scaffolding** (Neuen Gerüstbau hinzufügen) auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="75923-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="75923-298">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="75923-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="75923-299">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film** aus, oder geben Sie ihn ein.</span><span class="sxs-lookup"><span data-stu-id="75923-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="75923-300">Geben Sie in der Zeile **Datenkontextklasse** **RazorPagesMovieContext** ein, und wählen Sie den Eintrag aus. Dadurch wird eine neue DbContext-Klasse mit dem richtigen Namespace erstellt.</span><span class="sxs-lookup"><span data-stu-id="75923-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="75923-301">In diesem Fall ist das **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="75923-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="75923-302">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-302">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arpMac.png)

<span data-ttu-id="75923-304">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="75923-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="75923-305">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="75923-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="75923-306">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="75923-306">Files created</span></span>

* <span data-ttu-id="75923-307">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="75923-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="75923-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="75923-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="75923-309">Datei aktualisiert</span><span class="sxs-lookup"><span data-stu-id="75923-309">File updated</span></span>

* <span data-ttu-id="75923-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="75923-310">*Startup.cs*</span></span>

<span data-ttu-id="75923-311">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="75923-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="75923-312">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="75923-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75923-314">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="75923-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="75923-315">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="75923-315">Add an initial migration.</span></span>
* <span data-ttu-id="75923-316">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="75923-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="75923-317">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="75923-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="75923-319">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="75923-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="75923-320">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="75923-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="75923-321">Das Schema basiert auf dem Modell in `DbContext` (in der Datei *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="75923-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="75923-322">Das Argument `InitialCreate` wird verwendet, um die Migration zu benennen.</span><span class="sxs-lookup"><span data-stu-id="75923-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="75923-323">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="75923-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="75923-324">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="75923-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="75923-325">Der Befehl `Update-Database` führt die Methode `Up` in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus.</span><span class="sxs-lookup"><span data-stu-id="75923-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="75923-326">Die Methode `Up` erstellt die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="75923-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-328">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="75923-329">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: „*Für die Spalte „Preis“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „HasColumnType()“ sämtliche Werte unterstützen kann.* “ Sie können diese Warnung ignorieren, sie wird in einem zukünftigen Tutorial korrigiert.</span><span class="sxs-lookup"><span data-stu-id="75923-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75923-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75923-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="75923-331">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="75923-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="75923-332">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="75923-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="75923-333">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="75923-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="75923-334">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="75923-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="75923-335">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="75923-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="75923-336">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="75923-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="75923-337">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75923-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="75923-338">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="75923-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="75923-339">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="75923-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="75923-340">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="75923-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="75923-341">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="75923-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="75923-342">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="75923-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="75923-343">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="75923-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="75923-344">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="75923-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="75923-345">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="75923-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="75923-346">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="75923-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="75923-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="75923-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="75923-348">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="75923-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75923-349">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="75923-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="75923-350">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="75923-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="75923-351">Testen der App</span><span class="sxs-lookup"><span data-stu-id="75923-351">Test the app</span></span>

* <span data-ttu-id="75923-352">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="75923-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="75923-353">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="75923-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="75923-354">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="75923-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="75923-355">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="75923-355">Test the **Create** link.</span></span>

  ![Seite „Create“](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="75923-357">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="75923-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="75923-358">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="75923-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="75923-359">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="75923-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="75923-360">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="75923-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="75923-361">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="75923-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75923-362">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="75923-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="75923-363">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="75923-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

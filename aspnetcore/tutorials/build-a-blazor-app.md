---
title: Erstellen einer Blazor-App mit einer Aufgabenliste
author: guardrex
description: Erstellen Sie Schritt für Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 42c4f94869586bfcd4e8b27e4bdf4ef226094072
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022497"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="1716e-103">Erstellen einer Blazor-App mit einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="1716e-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="1716e-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1716e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1716e-105">In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern.</span><span class="sxs-lookup"><span data-stu-id="1716e-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="1716e-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1716e-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1716e-107">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="1716e-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="1716e-108">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="1716e-108">Modify Razor components</span></span>
> * <span data-ttu-id="1716e-109">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="1716e-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="1716e-110">Verwenden von Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="1716e-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="1716e-111">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Aufgabenlisten-App.</span><span class="sxs-lookup"><span data-stu-id="1716e-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1716e-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="1716e-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="1716e-113">Erstellen einer Aufgabenlisten-Blazor-App</span><span class="sxs-lookup"><span data-stu-id="1716e-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="1716e-114">Erstellen Sie in einer Befehlsshell eine neue Blazor-App namens `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="1716e-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="1716e-115">Der oben stehende Befehl erstellt einen Ordner namens `TodoList` zum Speichern der App.</span><span class="sxs-lookup"><span data-stu-id="1716e-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="1716e-116">Ändern Sie das Verzeichnis mit dem folgenden Befehl in den Ordner `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="1716e-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="1716e-117">Fügen Sie mit dem folgenden Befehl der App im Ordner `Pages` eine neue `Todo` Razor-Komponente hinzu:</span><span class="sxs-lookup"><span data-stu-id="1716e-117">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="1716e-118">Bei Razor-Komponenten-Dateinamen muss der erste Buchstabe groß geschrieben werden. Vergewissern Sie sich daher, dass der `Todo`-Komponenten-Dateiname mit dem Großbuchstaben `T` beginnt.</span><span class="sxs-lookup"><span data-stu-id="1716e-118">Razor component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="1716e-119">Geben Sie in `Pages/Todo.razor` das ursprüngliche Markup für die Komponente an:</span><span class="sxs-lookup"><span data-stu-id="1716e-119">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="1716e-120">Fügen Sie der Navigationsleiste die `Todo`-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="1716e-120">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="1716e-121">Die Komponente `NavMenu` (`Shared/NavMenu.razor`) wird im Layout der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="1716e-121">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="1716e-122">Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.</span><span class="sxs-lookup"><span data-stu-id="1716e-122">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="1716e-123">Fügen Sie ein `<NavLink>`-Element für die `Todo`-Komponente hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente in der `Shared/NavMenu.razor`-Datei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="1716e-123">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="1716e-124">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="1716e-124">Rebuild and run the app.</span></span> <span data-ttu-id="1716e-125">Besuchen Sie die neue Todo-Seite, um sicherzustellen, dass der Link zur `Todo`-Komponente funktioniert.</span><span class="sxs-lookup"><span data-stu-id="1716e-125">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="1716e-126">Fügen Sie dem Stamm des Projekts eine `TodoItem.cs`-Datei hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="1716e-126">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="1716e-127">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="1716e-127">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="1716e-128">Kehren Sie zur `Todo`-Komponente (`Pages/Todo.razor`) zurück:</span><span class="sxs-lookup"><span data-stu-id="1716e-128">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="1716e-129">Fügen Sie ein Feld für die Aufgabenelemente in einem `@code`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="1716e-129">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="1716e-130">Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="1716e-130">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="1716e-131">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="1716e-131">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="1716e-132">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="1716e-132">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="1716e-133">Fügen Sie eine Texteingabe (`<input>`) und eine Schaltfläche (`<button>`) unterhalb der Liste (`<ul>...</ul>`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="1716e-133">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="1716e-134">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="1716e-134">Rebuild and run the app.</span></span> <span data-ttu-id="1716e-135">Bei Auswahl der Schaltfläche **`Add todo`** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="1716e-135">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="1716e-136">Fügen Sie eine `AddTodo`-Methode zur `Todo`-Komponente hinzu, und registrieren Sie sie mit dem `@onclick`-Attribut für eine Schaltflächenauswahl.</span><span class="sxs-lookup"><span data-stu-id="1716e-136">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="1716e-137">Die C#-Methode `AddTodo` wird aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="1716e-137">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="1716e-138">Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie oben im `@code`-Block ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs im `<input>`-Element an den Wert der Texteingabe:</span><span class="sxs-lookup"><span data-stu-id="1716e-138">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="1716e-139">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="1716e-139">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="1716e-140">Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:</span><span class="sxs-lookup"><span data-stu-id="1716e-140">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="1716e-141">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="1716e-141">Rebuild and run the app.</span></span> <span data-ttu-id="1716e-142">Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="1716e-142">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="1716e-143">Der Titeltext für die einzelnen Aufgabenelemente kann als bearbeitbar festgelegt werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="1716e-143">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="1716e-144">Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="1716e-144">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="1716e-145">Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="1716e-145">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="1716e-146">Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h3>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="1716e-146">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="1716e-147">Die fertige `Todo`-Komponente (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="1716e-147">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="1716e-148">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="1716e-148">Rebuild and run the app.</span></span> <span data-ttu-id="1716e-149">Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="1716e-149">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1716e-150">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="1716e-150">Next steps</span></span>

<span data-ttu-id="1716e-151">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="1716e-151">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1716e-152">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="1716e-152">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="1716e-153">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="1716e-153">Modify Razor components</span></span>
> * <span data-ttu-id="1716e-154">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="1716e-154">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="1716e-155">Verwenden von Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="1716e-155">Use routing in a Blazor app</span></span>

<span data-ttu-id="1716e-156">Informationen über Tools für ASP.NET-Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="1716e-156">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>

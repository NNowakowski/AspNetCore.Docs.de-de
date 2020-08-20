---
title: Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie JavaScript-Funktionen über .NET-Methoden in Blazor-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: e7f23a4b44a0adb1d0b97c88e1d17f96aa2d28bd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625387"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="bd96d-103">Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="bd96d-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="bd96d-104">Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd96d-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd96d-105">Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="bd96d-106">Diese Szenarios werden als *JavaScript-Interoperabilität* (*JS Interop*) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="bd96d-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="bd96d-107">In diesem Artikel wird das Aufrufen von JavaScript-Funktionen über .NET behandelt.</span><span class="sxs-lookup"><span data-stu-id="bd96d-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="bd96d-108">Weitere Informationen zum Aufrufen von .NET-Methoden über JavaScript finden Sie unter <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="bd96d-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="bd96d-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd96d-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bd96d-110">Verwenden Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion, um JavaScript über .NET aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="bd96d-111">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in Ihre Komponente ein, um JS Interop-Aufrufe auszugeben.</span><span class="sxs-lookup"><span data-stu-id="bd96d-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="bd96d-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> akzeptiert einen Bezeichner für die JavaScript-Funktion, die Sie aufrufen möchten, sowie eine beliebige Anzahl JSON-serialisierbarer Argumente.</span><span class="sxs-lookup"><span data-stu-id="bd96d-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="bd96d-113">Der Funktionsbezeichner ist relativ zum globalen Bereich (`window`).</span><span class="sxs-lookup"><span data-stu-id="bd96d-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="bd96d-114">Wenn Sie `window.someScope.someFunction` aufrufen möchten, lautet der Bezeichner `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="bd96d-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="bd96d-115">Die Funktion muss nicht registriert werden, bevor sie aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bd96d-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="bd96d-116">Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein.</span><span class="sxs-lookup"><span data-stu-id="bd96d-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="bd96d-117">`T` sollte mit dem .NET-Typ übereinstimmen, der dem zurückgegebenen JSON-Typ am ehesten entspricht.</span><span class="sxs-lookup"><span data-stu-id="bd96d-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="bd96d-118">Bei Blazor Server-Apps mit aktiviertem Prerendering ist das Aufrufen von JavaScript beim ersten Prerendering nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="bd96d-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="bd96d-119">JavaScript-Interoperabilitätsaufrufe müssen verzögert werden, bis die Verbindung mit dem Browser hergestellt ist.</span><span class="sxs-lookup"><span data-stu-id="bd96d-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="bd96d-120">Weitere Informationen finden Sie im Abschnitt [Erkennen, wenn für eine Blazor Server-App ein Prerendering durchgeführt wird](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="bd96d-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="bd96d-121">Das folgende Beispiel basiert auf [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem auf JavaScript basierenden Decoder.</span><span class="sxs-lookup"><span data-stu-id="bd96d-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="bd96d-122">Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus einer C#-Methode aufgerufen wird, die eine Anforderung aus Entwicklercode in eine vorhandene JavaScript-API auslagert.</span><span class="sxs-lookup"><span data-stu-id="bd96d-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="bd96d-123">Die JavaScript-Funktion akzeptiert ein Bytearray von einer C#-Methode, decodiert das Array und gibt den Text zum Anzeigen an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="bd96d-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="bd96d-124">Geben Sie im `<head>`-Element von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine JavaScript-Funktion an, die `TextDecoder` verwendet, um ein übermitteltes Array zu decodieren und den decodierten Wert zurückzugeben:</span><span class="sxs-lookup"><span data-stu-id="bd96d-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="bd96d-125">JavaScript-Code, z. B. der Code im vorherigen Beispiel, kann auch über eine JavaScript-Datei (`.js`) mit einem Verweis auf die Skriptdatei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="bd96d-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="bd96d-126">Die folgende Komponente führt folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="bd96d-126">The following component:</span></span>

* <span data-ttu-id="bd96d-127">Sie ruft die JavaScript-Funktion `convertArray` mit `JSRuntime` auf, wenn eine Komponentenschaltfläche ( **`Convert Array`** ) ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="bd96d-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="bd96d-128">Nachdem die JavaScript-Funktion aufgerufen wurde, wird das übergebene Array in eine Zeichenfolge konvertiert.</span><span class="sxs-lookup"><span data-stu-id="bd96d-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="bd96d-129">Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bd96d-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="bd96d-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="bd96d-130">IJSRuntime</span></span>

<span data-ttu-id="bd96d-131">Verwenden Sie einen der folgenden Ansätze, um die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="bd96d-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="bd96d-132">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in die Razor-Komponente (`.razor`) ein:</span><span class="sxs-lookup"><span data-stu-id="bd96d-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="bd96d-133">Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine `handleTickerChanged`-JavaScript-Funktion bereit.</span><span class="sxs-lookup"><span data-stu-id="bd96d-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="bd96d-134">Die Funktion wird mit <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> aufgerufen und gibt keinen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="bd96d-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="bd96d-135">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in eine Klasse (`.cs`) ein:</span><span class="sxs-lookup"><span data-stu-id="bd96d-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="bd96d-136">Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine `handleTickerChanged`-JavaScript-Funktion bereit.</span><span class="sxs-lookup"><span data-stu-id="bd96d-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="bd96d-137">Die Funktion wird mit `JSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="bd96d-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="bd96d-138">Verwenden Sie das `[Inject]`-Attribut für die dynamische Inhaltsgenerierung mit [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic):</span><span class="sxs-lookup"><span data-stu-id="bd96d-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="bd96d-139">In der clientseitigen Beispiel-App zu diesem Artikel stehen der App zwei JavaScript-Funktionen zur Verfügung, die mit dem DOM (Dokumentobjektmodell) interagieren, um Benutzereingaben zu empfangen und eine Begrüßungsnachricht anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="bd96d-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="bd96d-140">`showPrompt`: Generiert eine Eingabeaufforderung zum Akzeptieren der Benutzereingaben (dem Namen des Benutzers) und gibt den Namen an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="bd96d-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="bd96d-141">`displayWelcome`: Weist einem DOM-Objekt eine Begrüßungsnachricht vom Aufrufer mit einer `id` mit dem Wert `welcome` zu.</span><span class="sxs-lookup"><span data-stu-id="bd96d-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="bd96d-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="bd96d-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="bd96d-143">Platzieren Sie das `<script>`-Tag, das auf die JavaScript-Datei verweist, in der `wwwroot/index.html`-Datei (Blazor WebAssembly) oder der `Pages/_Host.cshtml`-Datei (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="bd96d-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="bd96d-144">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="bd96d-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="bd96d-145">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="bd96d-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="bd96d-146">Platzieren Sie kein `<script>`-Tag in einer Komponentendatei, weil das `<script>`-Tag nicht dynamisch aktualisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="bd96d-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="bd96d-147">Die Interoperabilität von .NET-Methoden mit den JavaScript-Funktionen in der Datei `exampleJsInterop.js` erfolgt, indem <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bd96d-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="bd96d-148">Die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion ist asynchron, um Blazor Server-Szenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="bd96d-149">Wenn es sich bei der App um eine Blazor WebAssembly-App handelt, sollten Sie die JavaScript-Funktion synchron aufrufen, eine Typumwandlung nach unten in <xref:Microsoft.JSInterop.IJSInProcessRuntime> durchführen und stattdessen <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="bd96d-150">Es wird empfohlen, für die meisten JS Interop-Bibliotheken asynchrone APIs zu verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarios verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="bd96d-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="bd96d-151">Die Beispiel-App enthält eine Komponente zur Veranschaulichung der JavaScript-Interoperabilität.</span><span class="sxs-lookup"><span data-stu-id="bd96d-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="bd96d-152">Die Komponente:</span><span class="sxs-lookup"><span data-stu-id="bd96d-152">The component:</span></span>

* <span data-ttu-id="bd96d-153">empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="bd96d-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="bd96d-154">gibt den Text zur Verarbeitung an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="bd96d-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="bd96d-155">ruft eine zweite JavaScript-Funktion auf, die mit dem DOM interagiert, um eine Begrüßungsnachricht anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="bd96d-156">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="bd96d-156">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="bd96d-157">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="bd96d-157">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="bd96d-158">Wenn `TriggerJsPrompt` ausgeführt wird, indem die Schaltfläche **`Trigger JavaScript Prompt`** der Komponente ausgewählt wird, wird die `showPrompt`-Funktion von JavaScript aufgerufen, die in der `wwwroot/exampleJsInterop.js`-Datei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="bd96d-158">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="bd96d-159">Die `showPrompt`-Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert und an die Komponente zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="bd96d-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="bd96d-160">Die Komponente speichert den Namen des Benutzers in der lokalen Variable `name`.</span><span class="sxs-lookup"><span data-stu-id="bd96d-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="bd96d-161">Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an die JavaScript-Funktion `displayWelcome` übergeben wird, die die Begrüßungsnachricht in ein Überschriftentag rendert.</span><span class="sxs-lookup"><span data-stu-id="bd96d-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="bd96d-162">Aufrufen der JavaScript-Funktion „void“</span><span class="sxs-lookup"><span data-stu-id="bd96d-162">Call a void JavaScript function</span></span>

<span data-ttu-id="bd96d-163">JavaScript-Funktionen, die [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben, werden mit <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="bd96d-164">Erkennen, wenn für eine Blazor Server-App ein Prerendering durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="bd96d-164">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="bd96d-165">Erfassen von Verweisen auf Elemente</span><span class="sxs-lookup"><span data-stu-id="bd96d-165">Capture references to elements</span></span>

<span data-ttu-id="bd96d-166">Einige JS Interop-Szenarios erfordern Verweise auf HTML-Elemente.</span><span class="sxs-lookup"><span data-stu-id="bd96d-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="bd96d-167">Beispielsweise erfordert eine Benutzeroberflächenbibliothek möglicherweise einen Elementverweis für die Initialisierung, oder Sie müssen befehlsähnliche APIs für ein Element aufrufen, z. B. `focus` oder `play`.</span><span class="sxs-lookup"><span data-stu-id="bd96d-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="bd96d-168">Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="bd96d-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="bd96d-169">Fügen Sie ein `@ref`-Attribut zum HTML-Element hinzu.</span><span class="sxs-lookup"><span data-stu-id="bd96d-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="bd96d-170">Definieren Sie ein Feld vom Typ <xref:Microsoft.AspNetCore.Components.ElementReference>, dessen Name mit dem Wert des `@ref`-Attributs übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="bd96d-170">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="bd96d-171">Im folgenden Beispiel wird das Erfassen eines Verweises auf das `username` `<input>`-Element veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="bd96d-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="bd96d-172">Verwenden Sie Elementverweise nur, um die Inhalte eines leeren Elements zu ändern, das nicht mit Blazor interagiert.</span><span class="sxs-lookup"><span data-stu-id="bd96d-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="bd96d-173">Dieses Szenario ist nützlich, wenn eine Drittanbieter-API Inhalt für das Element bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="bd96d-173">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="bd96d-174">Da Blazor nicht mit dem Element interagiert, kann kein Konflikt zwischen der Darstellung von Blazor des Elements und dem DOM auftreten.</span><span class="sxs-lookup"><span data-stu-id="bd96d-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="bd96d-175">Im folgenden Beispiel ist es *gefährlich*, die Inhalte der unsortierten Liste (`ul`) zu ändern, weil Blazor mit dem DOM interagiert, um die Listenelemente (`<li>`) dieses Elements aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="bd96d-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="bd96d-176">Wenn die Inhalte des Elements `MyList` durch JS Interop geändert werden und Blazor versucht, diffs-Algorithmen auf das Element anzuwenden, entsprechen diese nicht dem DOM.</span><span class="sxs-lookup"><span data-stu-id="bd96d-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="bd96d-177">Bei .NET-Code handelt es sich bei <xref:Microsoft.AspNetCore.Components.ElementReference> um einen nicht transparenten Handle.</span><span class="sxs-lookup"><span data-stu-id="bd96d-177">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="bd96d-178">Das *Einzige*, was Sie mit <xref:Microsoft.AspNetCore.Components.ElementReference> tun können, ist, es per JS Interop an JavaScript-Code zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="bd96d-178">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="bd96d-179">Dadurch empfängt der JavaScript-seitige Code eine `HTMLElement`-Instanz, die er mit normalen DOM-APIs verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="bd96d-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="bd96d-180">Im folgenden Code wird beispielsweise eine .NET-Erweiterungsmethode definiert, die das Festlegen des Fokus auf ein Element ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="bd96d-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="bd96d-181">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="bd96d-181">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="bd96d-182">Verwenden Sie <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>, um eine JavaScript-Funktion aufzurufen, die keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="bd96d-182">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="bd96d-183">Mit dem folgenden Code wird der Fokus auf die Eingabe des Benutzernamens festgelegt, indem die vorherige JavaScript-Funktion mit dem erfassten <xref:Microsoft.AspNetCore.Components.ElementReference>-Wert aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="bd96d-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="bd96d-184">Erstellen Sie eine statische Erweiterungsmethode, die die <xref:Microsoft.JSInterop.IJSRuntime>-Instanz empfängt, um eine Erweiterungsmethode zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="bd96d-184">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="bd96d-185">Die `Focus`-Methode wird direkt für das Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="bd96d-186">Im folgenden Beispiel wird davon ausgegangen, dass die `Focus`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="bd96d-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="bd96d-187">Die `username`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde.</span><span class="sxs-lookup"><span data-stu-id="bd96d-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="bd96d-188">Wenn ein nicht aufgefüllter <xref:Microsoft.AspNetCore.Components.ElementReference>-Wert an JavaScript-Code übergeben wird, empfängt der JavaScript-Code den Wert `null`.</span><span class="sxs-lookup"><span data-stu-id="bd96d-188">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="bd96d-189">Um Elementverweise zu ändern, nachdem die Komponente gerendert wurde (um den anfänglichen Fokus für ein Element festzulegen), verwenden Sie die [`OnAfterRenderAsync`- oder `OnAfterRender`-Komponentenlebenszyklus-Methoden ](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="bd96d-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="bd96d-190">Verwenden Sie <xref:System.Threading.Tasks.ValueTask%601>, wenn Sie mit generischen Typen arbeiten und einen Wert zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="bd96d-190">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="bd96d-191">`GenericMethod` wird direkt für das Objekt mit einem Typ aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="bd96d-192">Im folgenden Beispiel wird davon ausgegangen, dass die `GenericMethod`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="bd96d-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="bd96d-193">Komponentenübergreifende Verweiselemente</span><span class="sxs-lookup"><span data-stu-id="bd96d-193">Reference elements across components</span></span>

<span data-ttu-id="bd96d-194"><xref:Microsoft.AspNetCore.Components.ElementReference> ist nur in der <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>-Methode einer Komponente garantiert gültig (und ein Elementverweis ist ein `struct`), daher kann ein Elementverweis nicht zwischen Komponenten übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="bd96d-194">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="bd96d-195">Damit eine übergeordnete Komponente einen Elementverweis anderen Komponenten zur Verfügung stellen kann, kann die übergeordnete Komponente:</span><span class="sxs-lookup"><span data-stu-id="bd96d-195">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="bd96d-196">zulassen, dass untergeordnete Komponenten Rückrufe registrieren.</span><span class="sxs-lookup"><span data-stu-id="bd96d-196">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="bd96d-197">die registrierten Rückrufe während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>-Ereignisses mit dem übergebenen Elementverweis aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bd96d-197">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="bd96d-198">Dieser Ansatz ermöglicht untergeordneten Komponenten indirekt die Interaktion mit dem Elementverweis des übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="bd96d-198">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="bd96d-199">Dieser Ansatz wird anhand des folgenden Blazor WebAssembly-Beispiels veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="bd96d-199">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="bd96d-200">Im `<head>` von `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="bd96d-200">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="bd96d-201">Im `<body>` von `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="bd96d-201">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="bd96d-202">`Pages/Index.razor` (übergeordnete Komponente):</span><span class="sxs-lookup"><span data-stu-id="bd96d-202">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="bd96d-203">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="bd96d-203">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="bd96d-204">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="bd96d-204">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="bd96d-205">`Shared/SurveyPrompt.razor` (untergeordnete Komponente):</span><span class="sxs-lookup"><span data-stu-id="bd96d-205">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="bd96d-206">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="bd96d-206">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="bd96d-207">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="bd96d-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="bd96d-208">Festschreiben von JS Interop-Aufrufen</span><span class="sxs-lookup"><span data-stu-id="bd96d-208">Harden JS interop calls</span></span>

<span data-ttu-id="bd96d-209">JS Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="bd96d-209">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="bd96d-210">Standardmäßig weisen Blazor Server-Apps einen Timeout für JS Interop-Aufrufe an den Server nach einer Minute auf.</span><span class="sxs-lookup"><span data-stu-id="bd96d-210">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="bd96d-211">Wenn eine App ein engeres Timeout tolerieren kann, legen Sie das Timeout mit einem der folgenden Ansätze fest:</span><span class="sxs-lookup"><span data-stu-id="bd96d-211">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="bd96d-212">Legen Sie das Timeout global in `Startup.ConfigureServices` fest:</span><span class="sxs-lookup"><span data-stu-id="bd96d-212">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="bd96d-213">Pro Aufruf im Komponentencode kann ein einzelner Aufruf das Timeout festlegen:</span><span class="sxs-lookup"><span data-stu-id="bd96d-213">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="bd96d-214">Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="bd96d-214">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="bd96d-215">Vermeiden von Objektzirkelbezügen</span><span class="sxs-lookup"><span data-stu-id="bd96d-215">Avoid circular object references</span></span>

<span data-ttu-id="bd96d-216">Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:</span><span class="sxs-lookup"><span data-stu-id="bd96d-216">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="bd96d-217">.NET-Methodenaufrufe.</span><span class="sxs-lookup"><span data-stu-id="bd96d-217">.NET method calls.</span></span>
* <span data-ttu-id="bd96d-218">JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.</span><span class="sxs-lookup"><span data-stu-id="bd96d-218">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="bd96d-219">Weitere Informationen finden Sie unter den folgenden Problemen:</span><span class="sxs-lookup"><span data-stu-id="bd96d-219">For more information, see the following issues:</span></span>

* [<span data-ttu-id="bd96d-220">Zirkelbezüge werden nicht unterstützt, die Zweite (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="bd96d-220">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="bd96d-221">Vorschlag: Hinzufügen eines Mechanismus zur Verarbeitung von Zirkelbezügen bei der Serialisierung (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="bd96d-221">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="bd96d-222">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bd96d-222">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="bd96d-223">InteropComponent.razor-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)</span><span class="sxs-lookup"><span data-stu-id="bd96d-223">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="bd96d-224">Ausführen umfangreicher Datenübertragungen in Blazor Server-Apps</span><span class="sxs-lookup"><span data-stu-id="bd96d-224">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)

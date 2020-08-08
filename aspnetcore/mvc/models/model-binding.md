---
title: Modellbindung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Modellbindung in ASP.NET Core funktioniert und wie Sie ihr Verhalten anpassen.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 6ec531a04a220f75f5793cb2c7b5232908dbd883
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019156"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="ce401-103">Modellbindung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce401-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce401-104">In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="ce401-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="ce401-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ce401-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="ce401-106">Was ist Modellbindung?</span><span class="sxs-lookup"><span data-stu-id="ce401-106">What is Model binding</span></span>

<span data-ttu-id="ce401-107">Controller und Razor Seiten arbeiten mit Daten, die aus HTTP-Anforderungen stammen.</span><span class="sxs-lookup"><span data-stu-id="ce401-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="ce401-108">Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="ce401-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="ce401-109">Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig.</span><span class="sxs-lookup"><span data-stu-id="ce401-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="ce401-110">Modellbindung automatisiert diesen Vorgang.</span><span class="sxs-lookup"><span data-stu-id="ce401-110">Model binding automates this process.</span></span> <span data-ttu-id="ce401-111">Das Modellbindungssystem:</span><span class="sxs-lookup"><span data-stu-id="ce401-111">The model binding system:</span></span>

* <span data-ttu-id="ce401-112">Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="ce401-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="ce401-113">Stellt die Daten für Controller und Razor Seiten in Methoden Parametern und öffentlichen Eigenschaften bereit.</span><span class="sxs-lookup"><span data-stu-id="ce401-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="ce401-114">Konvertiert Zeichenfolgendaten in .NET-Typen.</span><span class="sxs-lookup"><span data-stu-id="ce401-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="ce401-115">Aktualisiert Eigenschaften komplexer Typen.</span><span class="sxs-lookup"><span data-stu-id="ce401-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="ce401-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="ce401-116">Example</span></span>

<span data-ttu-id="ce401-117">Angenommen Sie haben die folgende Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="ce401-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="ce401-118">Und die App empfängt eine Anforderung mit dieser URL:</span><span class="sxs-lookup"><span data-stu-id="ce401-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="ce401-119">Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:</span><span class="sxs-lookup"><span data-stu-id="ce401-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="ce401-120">Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.</span><span class="sxs-lookup"><span data-stu-id="ce401-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="ce401-121">Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.</span><span class="sxs-lookup"><span data-stu-id="ce401-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="ce401-122">Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.</span><span class="sxs-lookup"><span data-stu-id="ce401-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="ce401-123">Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="ce401-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="ce401-124">Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="ce401-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="ce401-125">Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="ce401-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="ce401-126">Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.</span><span class="sxs-lookup"><span data-stu-id="ce401-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="ce401-127">Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="ce401-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="ce401-128">Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind.</span><span class="sxs-lookup"><span data-stu-id="ce401-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="ce401-129">Ziele können aber auch die Eigenschaften eines komplexen Typs sein.</span><span class="sxs-lookup"><span data-stu-id="ce401-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="ce401-130">Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ce401-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="ce401-131">Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="ce401-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="ce401-132">Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="ce401-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="ce401-133">Ziele</span><span class="sxs-lookup"><span data-stu-id="ce401-133">Targets</span></span>

<span data-ttu-id="ce401-134">Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:</span><span class="sxs-lookup"><span data-stu-id="ce401-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="ce401-135">Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="ce401-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="ce401-136">Parameter der Razor pages-Handlermethode, an die eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="ce401-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="ce401-137">Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.</span><span class="sxs-lookup"><span data-stu-id="ce401-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="ce401-138">[BindProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-138">[BindProperty] attribute</span></span>

<span data-ttu-id="ce401-139">Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="ce401-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="ce401-140">[BindProperties]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-140">[BindProperties] attribute</span></span>

<span data-ttu-id="ce401-141">Verfügbar in ASP.NET Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="ce401-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="ce401-142">Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="ce401-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="ce401-143">Modellbindung für HTTP-GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="ce401-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="ce401-144">Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden.</span><span class="sxs-lookup"><span data-stu-id="ce401-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="ce401-145">In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter.</span><span class="sxs-lookup"><span data-stu-id="ce401-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="ce401-146">Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen.</span><span class="sxs-lookup"><span data-stu-id="ce401-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="ce401-147">Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält.</span><span class="sxs-lookup"><span data-stu-id="ce401-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="ce401-148">In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="ce401-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="ce401-149">Quellen</span><span class="sxs-lookup"><span data-stu-id="ce401-149">Sources</span></span>

<span data-ttu-id="ce401-150">Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="ce401-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="ce401-151">Formularfelder</span><span class="sxs-lookup"><span data-stu-id="ce401-151">Form fields</span></span>
1. <span data-ttu-id="ce401-152">Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))</span><span class="sxs-lookup"><span data-stu-id="ce401-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="ce401-153">Routendaten</span><span class="sxs-lookup"><span data-stu-id="ce401-153">Route data</span></span>
1. <span data-ttu-id="ce401-154">Abfragezeichenfolge-Parameter</span><span class="sxs-lookup"><span data-stu-id="ce401-154">Query string parameters</span></span>
1. <span data-ttu-id="ce401-155">Hochgeladene Dateien</span><span class="sxs-lookup"><span data-stu-id="ce401-155">Uploaded files</span></span>

<span data-ttu-id="ce401-156">Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen nach der oben aufgeführten Reihenfolge überprüft.</span><span class="sxs-lookup"><span data-stu-id="ce401-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="ce401-157">Es gibt ein paar Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="ce401-157">There are a few exceptions:</span></span>

* <span data-ttu-id="ce401-158">Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.</span><span class="sxs-lookup"><span data-stu-id="ce401-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="ce401-159">Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.</span><span class="sxs-lookup"><span data-stu-id="ce401-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="ce401-160">Wenn die Standardquelle nicht richtig ist, verwenden Sie eines der folgenden Attribute zum Festlegen der Quelle:</span><span class="sxs-lookup"><span data-stu-id="ce401-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="ce401-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Ruft Werte aus der Abfrage Zeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="ce401-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Ruft Werte aus Routendaten ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="ce401-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Ruft Werte aus den Feldern für gepostete Formulare ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="ce401-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-Ruft Werte aus dem Anforderungs Text ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="ce401-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-Ruft Werte aus HTTP-Headern ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="ce401-166">Diese Attribute:</span><span class="sxs-lookup"><span data-stu-id="ce401-166">These attributes:</span></span>

* <span data-ttu-id="ce401-167">Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="ce401-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="ce401-168">Akzeptieren optional einen Modellnamenswert im Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="ce401-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="ce401-169">Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="ce401-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="ce401-170">Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="ce401-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="ce401-171">[FromBody]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-171">[FromBody] attribute</span></span>

<span data-ttu-id="ce401-172">Wenden Sie das `[FromBody]`-Attribut auf einen Parameter an, um dessen Eigenschaften über den Text einer HTTP-Anforderung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="ce401-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="ce401-173">Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Texts an einen Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="ce401-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="ce401-174">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="ce401-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="ce401-175">Wenn `[FromBody]` auf einen komplexen Typparameter angewendet wird, werden alle Bindungsquellenattribute ignoriert, die auf die Eigenschaften angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="ce401-176">Die folgende `Create`-Aktion legt beispielsweise fest, dass der `pet`-Parameter mithilfe des Texts aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="ce401-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="ce401-177">Die `Pet`-Klasse legt fest, dass ihre `Breed`-Eigenschaft mithilfe eines Abfragezeichenfolgenparameters aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="ce401-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="ce401-178">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-178">In the preceding example:</span></span>

* <span data-ttu-id="ce401-179">Das `[FromQuery]`-Attribut wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="ce401-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="ce401-180">Die `Breed`-Eigenschaft wird nicht mithilfe eines Abfragezeichenfolgenparameters aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="ce401-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="ce401-181">Eingabeformatierer lesen nur den Text und verstehen Bindungsquellenattribute nicht.</span><span class="sxs-lookup"><span data-stu-id="ce401-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="ce401-182">Wenn ein geeigneter Wert im Text gefunden wird, wird dieser Wert zum Auffüllen der `Breed`-Eigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="ce401-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="ce401-183">Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an.</span><span class="sxs-lookup"><span data-stu-id="ce401-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="ce401-184">Sobald der Anforderungsdatenstrom von einem Eingabeformatierer gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter nochmal gelesen zu werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="ce401-185">Zusätzliche Quellen</span><span class="sxs-lookup"><span data-stu-id="ce401-185">Additional sources</span></span>

<span data-ttu-id="ce401-186">Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ce401-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="ce401-187">Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen.</span><span class="sxs-lookup"><span data-stu-id="ce401-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="ce401-188">Angenommen, Sie möchten Daten aus cookie s oder dem Sitzungszustand.</span><span class="sxs-lookup"><span data-stu-id="ce401-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="ce401-189">So rufen Sie Daten aus einer neuen Quelle ab</span><span class="sxs-lookup"><span data-stu-id="ce401-189">To get data from a new source:</span></span>

* <span data-ttu-id="ce401-190">Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="ce401-191">Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="ce401-192">Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ce401-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="ce401-193">Die Beispiel-app enthält einen [Wert Anbieter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) und ein [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) -Beispiel, mit dem Werte von s abgerufen werden cookie .</span><span class="sxs-lookup"><span data-stu-id="ce401-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="ce401-194">Dies ist der Registrierungscode in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ce401-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="ce401-195">Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.</span><span class="sxs-lookup"><span data-stu-id="ce401-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="ce401-196">Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.</span><span class="sxs-lookup"><span data-stu-id="ce401-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="ce401-197">Keine Quelle für eine Modelleigenschaft</span><span class="sxs-lookup"><span data-stu-id="ce401-197">No source for a model property</span></span>

<span data-ttu-id="ce401-198">Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="ce401-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="ce401-199">Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="ce401-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="ce401-200">Einfache Nullable-Typen werden auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ce401-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="ce401-201">Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ce401-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="ce401-202">Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ce401-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="ce401-203">Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ce401-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="ce401-204">Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="ce401-205">Wenn der Modell Zustand für eine Modell Eigenschaft ungültig gemacht werden soll, wenn nichts gefunden wird, verwenden Sie das- [`[BindRequired]`](#bindrequired-attribute) Attribut.</span><span class="sxs-lookup"><span data-stu-id="ce401-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="ce401-206">Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="ce401-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="ce401-207">Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="ce401-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="ce401-208">Typkonvertierungsfehler</span><span class="sxs-lookup"><span data-stu-id="ce401-208">Type conversion errors</span></span>

<span data-ttu-id="ce401-209">Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="ce401-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="ce401-210">Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.</span><span class="sxs-lookup"><span data-stu-id="ce401-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="ce401-211">In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.</span><span class="sxs-lookup"><span data-stu-id="ce401-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="ce401-212">Zeigen Sie auf einer Razor Seite die Seite erneut mit einer Fehlermeldung an:</span><span class="sxs-lookup"><span data-stu-id="ce401-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="ce401-213">Bei der Client seitigen Validierung werden die meisten ungültigen Daten abgefangen, die andernfalls an ein Seiten Formular übermittelt werden Razor .</span><span class="sxs-lookup"><span data-stu-id="ce401-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="ce401-214">Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen.</span><span class="sxs-lookup"><span data-stu-id="ce401-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="ce401-215">Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet.</span><span class="sxs-lookup"><span data-stu-id="ce401-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="ce401-216">Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="ce401-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="ce401-217">Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ce401-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="ce401-218">Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="ce401-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="ce401-219">Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ce401-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="ce401-220">Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="ce401-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="ce401-221">Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen.</span><span class="sxs-lookup"><span data-stu-id="ce401-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="ce401-222">In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="ce401-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="ce401-223">Einfache Typen</span><span class="sxs-lookup"><span data-stu-id="ce401-223">Simple types</span></span>

<span data-ttu-id="ce401-224">Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="ce401-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="ce401-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="ce401-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="ce401-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="ce401-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="ce401-227">Char</span><span class="sxs-lookup"><span data-stu-id="ce401-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="ce401-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="ce401-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="ce401-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ce401-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="ce401-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="ce401-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="ce401-231">Double</span><span class="sxs-lookup"><span data-stu-id="ce401-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="ce401-232">Enumeration</span><span class="sxs-lookup"><span data-stu-id="ce401-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="ce401-233">GUID</span><span class="sxs-lookup"><span data-stu-id="ce401-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="ce401-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="ce401-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="ce401-235">Single</span><span class="sxs-lookup"><span data-stu-id="ce401-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="ce401-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="ce401-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="ce401-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="ce401-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="ce401-238">URI</span><span class="sxs-lookup"><span data-stu-id="ce401-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="ce401-239">Version</span><span class="sxs-lookup"><span data-stu-id="ce401-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="ce401-240">Komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="ce401-240">Complex types</span></span>

<span data-ttu-id="ce401-241">Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können.</span><span class="sxs-lookup"><span data-stu-id="ce401-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="ce401-242">Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="ce401-243">Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="ce401-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="ce401-244">Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="ce401-245">Beim Binden an einen Parameter ist das Präfix der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="ce401-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="ce401-246">Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ce401-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="ce401-247">Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.</span><span class="sxs-lookup"><span data-stu-id="ce401-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="ce401-248">Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="ce401-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="ce401-249">Präfix = Parametername</span><span class="sxs-lookup"><span data-stu-id="ce401-249">Prefix = parameter name</span></span>

<span data-ttu-id="ce401-250">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:</span><span class="sxs-lookup"><span data-stu-id="ce401-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="ce401-251">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="ce401-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="ce401-252">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="ce401-253">Präfix = Name der Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="ce401-253">Prefix = property name</span></span>

<span data-ttu-id="ce401-254">Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:</span><span class="sxs-lookup"><span data-stu-id="ce401-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="ce401-255">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="ce401-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ce401-256">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="ce401-257">Benutzerdefiniertes Präfix</span><span class="sxs-lookup"><span data-stu-id="ce401-257">Custom prefix</span></span>

<span data-ttu-id="ce401-258">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:</span><span class="sxs-lookup"><span data-stu-id="ce401-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="ce401-259">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="ce401-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ce401-260">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="ce401-261">Attribute für Ziele komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="ce401-261">Attributes for complex type targets</span></span>

<span data-ttu-id="ce401-262">Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="ce401-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="ce401-263">Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind.</span><span class="sxs-lookup"><span data-stu-id="ce401-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="ce401-264">Sie haben keine Auswirkungen auf Eingabeformatierer, die bereitgestellte JSON- und XML-Anforderungstexte verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ce401-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="ce401-265">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="ce401-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="ce401-266">Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="ce401-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="ce401-267">[BindRequired]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-267">[BindRequired] attribute</span></span>

<span data-ttu-id="ce401-268">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="ce401-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ce401-269">Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="ce401-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="ce401-270">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="ce401-271">[BindNever]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-271">[BindNever] attribute</span></span>

<span data-ttu-id="ce401-272">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="ce401-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ce401-273">Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt.</span><span class="sxs-lookup"><span data-stu-id="ce401-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="ce401-274">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="ce401-275">[Bind]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-275">[Bind] attribute</span></span>

<span data-ttu-id="ce401-276">Kann auf eine Klasse oder einen Methodenparameter angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="ce401-277">Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ce401-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="ce401-278">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="ce401-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="ce401-279">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="ce401-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="ce401-280">Das `[Bind]`-Attribut kann zum Schutz vor Overposting in *Erstellungs*szenarien (create) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="ce401-281">Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="ce401-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="ce401-282">Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="ce401-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="ce401-283">Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="ce401-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="ce401-284">Sammlungen</span><span class="sxs-lookup"><span data-stu-id="ce401-284">Collections</span></span>

<span data-ttu-id="ce401-285">Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="ce401-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ce401-286">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ce401-287">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-287">For example:</span></span>

* <span data-ttu-id="ce401-288">Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ce401-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="ce401-289">Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:</span><span class="sxs-lookup"><span data-stu-id="ce401-289">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="ce401-290">Das folgende Format wird nur für Formulardaten unterstützt:</span><span class="sxs-lookup"><span data-stu-id="ce401-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="ce401-291">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="ce401-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ce401-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="ce401-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="ce401-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="ce401-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="ce401-294">Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null).</span><span class="sxs-lookup"><span data-stu-id="ce401-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="ce401-295">Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert.</span><span class="sxs-lookup"><span data-stu-id="ce401-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="ce401-296">Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.</span><span class="sxs-lookup"><span data-stu-id="ce401-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="ce401-297">Wörterbücher</span><span class="sxs-lookup"><span data-stu-id="ce401-297">Dictionaries</span></span>

<span data-ttu-id="ce401-298">Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="ce401-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ce401-299">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ce401-300">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-300">For example:</span></span>

* <span data-ttu-id="ce401-301">Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ce401-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="ce401-302">Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:</span><span class="sxs-lookup"><span data-stu-id="ce401-302">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="ce401-303">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="ce401-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ce401-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="ce401-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="ce401-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="ce401-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="ce401-306">Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="ce401-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="ce401-307">Der ASP.NET Core-Routenwertanbieter und der Abfragezeichenfolgenwert-Anbieter:</span><span class="sxs-lookup"><span data-stu-id="ce401-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="ce401-308">behandeln Werte als invariante Kulturen.</span><span class="sxs-lookup"><span data-stu-id="ce401-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="ce401-309">erwarten, dass URLs kulturinvariant sind.</span><span class="sxs-lookup"><span data-stu-id="ce401-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="ce401-310">Im Gegensatz dazu durchlaufen Werte, die aus Formulardaten stammen, eine kulturabhängige Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="ce401-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="ce401-311">Dies ist beabsichtigt, damit URLs zwischen Gebietsschemas freigegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="ce401-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="ce401-312">So lassen Sie den ASP.NET Core-Routenwertanbieter und den Abfragezeichenfolgenwert-Anbieter eine kulturabhängige Konvertierung durchlaufen:</span><span class="sxs-lookup"><span data-stu-id="ce401-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="ce401-313">Sie erben von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="ce401-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="ce401-314">Kopieren Sie den Code aus [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) oder [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="ce401-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="ce401-315">Ersetzen Sie den an den Wertanbieterkonstruktor übergebenen [Culture-Wert](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) durch [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="ce401-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="ce401-316">Ersetzen Sie die Standardwertanbieter-Zuordnungsinstanz in den MVC-Optionen durch Ihre neue:</span><span class="sxs-lookup"><span data-stu-id="ce401-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="ce401-317">Spezielle Datentypen</span><span class="sxs-lookup"><span data-stu-id="ce401-317">Special data types</span></span>

<span data-ttu-id="ce401-318">Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="ce401-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="ce401-319">„IFormFile“ und „IFormFileCollection“</span><span class="sxs-lookup"><span data-stu-id="ce401-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="ce401-320">Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="ce401-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="ce401-321">Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ce401-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="ce401-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="ce401-322">CancellationToken</span></span>

<span data-ttu-id="ce401-323">Wird verwendet, um die Aktivität in asynchronen Controllern zu beenden.</span><span class="sxs-lookup"><span data-stu-id="ce401-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="ce401-324">„FormCollection“</span><span class="sxs-lookup"><span data-stu-id="ce401-324">FormCollection</span></span>

<span data-ttu-id="ce401-325">Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="ce401-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="ce401-326">Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="ce401-326">Input formatters</span></span>

<span data-ttu-id="ce401-327">Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein.</span><span class="sxs-lookup"><span data-stu-id="ce401-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="ce401-328">Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="ce401-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="ce401-329">ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten.</span><span class="sxs-lookup"><span data-stu-id="ce401-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="ce401-330">Sie können andere Formatierer für andere Inhaltstypen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="ce401-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="ce401-331">ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus.</span><span class="sxs-lookup"><span data-stu-id="ce401-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="ce401-332">Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="ce401-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="ce401-333">So verwenden Sie die integrierte XML-Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="ce401-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="ce401-334">Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="ce401-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="ce401-335">In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.</span><span class="sxs-lookup"><span data-stu-id="ce401-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="ce401-336">Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.</span><span class="sxs-lookup"><span data-stu-id="ce401-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="ce401-337">Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="ce401-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="ce401-338">Anpassen der Modellbindung mit Eingabeformatierern</span><span class="sxs-lookup"><span data-stu-id="ce401-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="ce401-339">Ein Eingabeformatierer ist in vollem Umfang für das Lesen von Daten aus dem Anforderungstext verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="ce401-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="ce401-340">Um diesen Prozess anzupassen, konfigurieren Sie die APIs, die vom Eingabeformatierer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="ce401-341">In diesem Abschnitt wird beschrieben, wie Sie den auf `System.Text.Json` basierenden Eingabeformatierer so anpassen, dass er einen benutzerdefinierten Typ mit dem Namen `ObjectId` versteht.</span><span class="sxs-lookup"><span data-stu-id="ce401-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="ce401-342">Betrachten Sie das folgende Modell, das eine benutzerdefinierte `ObjectId`-Eigenschaft mit dem Namen `Id` enthält:</span><span class="sxs-lookup"><span data-stu-id="ce401-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="ce401-343">Um den Modellbindungsprozess bei der Verwendung von `System.Text.Json`anzupassen, erstellen Sie eine aus <xref:System.Text.Json.Serialization.JsonConverter%601> abgeleitete Klasse:</span><span class="sxs-lookup"><span data-stu-id="ce401-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="ce401-344">Um einen benutzerdefinierten Konverter zu verwenden, wenden Sie das <xref:System.Text.Json.Serialization.JsonConverterAttribute>-Attribut auf den Typ an.</span><span class="sxs-lookup"><span data-stu-id="ce401-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="ce401-345">Im folgenden Beispiel wird der Typ `ObjectId` mit `ObjectIdConverter` als seinem benutzerdefinierten Konverter konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="ce401-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="ce401-346">Weitere Informationen finden Sie unter [Vorgehensweise: Schreiben benutzerdefinierter Konverter](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="ce401-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="ce401-347">Ausschließen angegebener Typen aus der Modellbindung</span><span class="sxs-lookup"><span data-stu-id="ce401-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="ce401-348">Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert.</span><span class="sxs-lookup"><span data-stu-id="ce401-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="ce401-349">Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="ce401-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="ce401-350">Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="ce401-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="ce401-351">Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce401-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ce401-352">Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="ce401-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="ce401-353">Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce401-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ce401-354">Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="ce401-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="ce401-355">Benutzerdefinierte Modellbindungen</span><span class="sxs-lookup"><span data-stu-id="ce401-355">Custom model binders</span></span>

<span data-ttu-id="ce401-356">Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="ce401-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="ce401-357">Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="ce401-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="ce401-358">Manuelle Modellbindung</span><span class="sxs-lookup"><span data-stu-id="ce401-358">Manual model binding</span></span> 

<span data-ttu-id="ce401-359">Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="ce401-360">Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="ce401-361">Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ce401-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="ce401-362">Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="ce401-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="ce401-363">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="ce401-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> verwendet Wertanbieter, um Daten aus dem Formulartext, der Abfragezeichenfolge und den Routendaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="ce401-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="ce401-365">`TryUpdateModelAsync` wird normalerweise so verwendet:</span><span class="sxs-lookup"><span data-stu-id="ce401-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="ce401-366">Wird mit Razor Seiten und MVC-Apps verwendet, die Controller und Ansichten verwenden, um eine Übertragung zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="ce401-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="ce401-367">Nicht zusammen mit einer Web-API, es sei denn, sie wird von Formulardaten, Abfragezeichenfolgen und Routendaten konsumiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="ce401-368">Web-API-Endpunkte, die JSON nutzen, verwenden [Eingabeformatierer](#input-formatters), um den Anforderungstext in ein-Objekt zu deserialisieren.</span><span class="sxs-lookup"><span data-stu-id="ce401-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="ce401-369">Weitere Informationen finden Sie unter [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="ce401-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="ce401-370">[FromServices]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-370">[FromServices] attribute</span></span>

<span data-ttu-id="ce401-371">Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben.</span><span class="sxs-lookup"><span data-stu-id="ce401-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="ce401-372">Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht.</span><span class="sxs-lookup"><span data-stu-id="ce401-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="ce401-373">Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ce401-374">Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ce401-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce401-375">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ce401-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ce401-376">In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="ce401-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="ce401-377">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ce401-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="ce401-378">Was ist Modellbindung?</span><span class="sxs-lookup"><span data-stu-id="ce401-378">What is Model binding</span></span>

<span data-ttu-id="ce401-379">Controller und Razor Seiten arbeiten mit Daten, die aus HTTP-Anforderungen stammen.</span><span class="sxs-lookup"><span data-stu-id="ce401-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="ce401-380">Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="ce401-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="ce401-381">Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig.</span><span class="sxs-lookup"><span data-stu-id="ce401-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="ce401-382">Modellbindung automatisiert diesen Vorgang.</span><span class="sxs-lookup"><span data-stu-id="ce401-382">Model binding automates this process.</span></span> <span data-ttu-id="ce401-383">Das Modellbindungssystem:</span><span class="sxs-lookup"><span data-stu-id="ce401-383">The model binding system:</span></span>

* <span data-ttu-id="ce401-384">Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="ce401-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="ce401-385">Stellt die Daten für Controller und Razor Seiten in Methoden Parametern und öffentlichen Eigenschaften bereit.</span><span class="sxs-lookup"><span data-stu-id="ce401-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="ce401-386">Konvertiert Zeichenfolgendaten in .NET-Typen.</span><span class="sxs-lookup"><span data-stu-id="ce401-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="ce401-387">Aktualisiert Eigenschaften komplexer Typen.</span><span class="sxs-lookup"><span data-stu-id="ce401-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="ce401-388">Beispiel</span><span class="sxs-lookup"><span data-stu-id="ce401-388">Example</span></span>

<span data-ttu-id="ce401-389">Angenommen Sie haben die folgende Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="ce401-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="ce401-390">Und die App empfängt eine Anforderung mit dieser URL:</span><span class="sxs-lookup"><span data-stu-id="ce401-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="ce401-391">Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:</span><span class="sxs-lookup"><span data-stu-id="ce401-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="ce401-392">Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.</span><span class="sxs-lookup"><span data-stu-id="ce401-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="ce401-393">Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.</span><span class="sxs-lookup"><span data-stu-id="ce401-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="ce401-394">Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.</span><span class="sxs-lookup"><span data-stu-id="ce401-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="ce401-395">Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="ce401-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="ce401-396">Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="ce401-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="ce401-397">Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="ce401-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="ce401-398">Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.</span><span class="sxs-lookup"><span data-stu-id="ce401-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="ce401-399">Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="ce401-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="ce401-400">Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind.</span><span class="sxs-lookup"><span data-stu-id="ce401-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="ce401-401">Ziele können aber auch die Eigenschaften eines komplexen Typs sein.</span><span class="sxs-lookup"><span data-stu-id="ce401-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="ce401-402">Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ce401-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="ce401-403">Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="ce401-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="ce401-404">Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="ce401-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="ce401-405">Ziele</span><span class="sxs-lookup"><span data-stu-id="ce401-405">Targets</span></span>

<span data-ttu-id="ce401-406">Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:</span><span class="sxs-lookup"><span data-stu-id="ce401-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="ce401-407">Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="ce401-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="ce401-408">Parameter der Razor pages-Handlermethode, an die eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="ce401-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="ce401-409">Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.</span><span class="sxs-lookup"><span data-stu-id="ce401-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="ce401-410">[BindProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-410">[BindProperty] attribute</span></span>

<span data-ttu-id="ce401-411">Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="ce401-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="ce401-412">[BindProperties]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-412">[BindProperties] attribute</span></span>

<span data-ttu-id="ce401-413">Verfügbar in ASP.NET Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="ce401-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="ce401-414">Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="ce401-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="ce401-415">Modellbindung für HTTP-GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="ce401-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="ce401-416">Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden.</span><span class="sxs-lookup"><span data-stu-id="ce401-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="ce401-417">In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter.</span><span class="sxs-lookup"><span data-stu-id="ce401-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="ce401-418">Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen.</span><span class="sxs-lookup"><span data-stu-id="ce401-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="ce401-419">Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält.</span><span class="sxs-lookup"><span data-stu-id="ce401-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="ce401-420">In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="ce401-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="ce401-421">Quellen</span><span class="sxs-lookup"><span data-stu-id="ce401-421">Sources</span></span>

<span data-ttu-id="ce401-422">Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="ce401-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="ce401-423">Formularfelder</span><span class="sxs-lookup"><span data-stu-id="ce401-423">Form fields</span></span>
1. <span data-ttu-id="ce401-424">Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))</span><span class="sxs-lookup"><span data-stu-id="ce401-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="ce401-425">Routendaten</span><span class="sxs-lookup"><span data-stu-id="ce401-425">Route data</span></span>
1. <span data-ttu-id="ce401-426">Abfragezeichenfolge-Parameter</span><span class="sxs-lookup"><span data-stu-id="ce401-426">Query string parameters</span></span>
1. <span data-ttu-id="ce401-427">Hochgeladene Dateien</span><span class="sxs-lookup"><span data-stu-id="ce401-427">Uploaded files</span></span>

<span data-ttu-id="ce401-428">Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen nach der oben aufgeführten Reihenfolge überprüft.</span><span class="sxs-lookup"><span data-stu-id="ce401-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="ce401-429">Es gibt ein paar Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="ce401-429">There are a few exceptions:</span></span>

* <span data-ttu-id="ce401-430">Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.</span><span class="sxs-lookup"><span data-stu-id="ce401-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="ce401-431">Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.</span><span class="sxs-lookup"><span data-stu-id="ce401-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="ce401-432">Wenn die Standardquelle nicht richtig ist, verwenden Sie eines der folgenden Attribute zum Festlegen der Quelle:</span><span class="sxs-lookup"><span data-stu-id="ce401-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="ce401-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Ruft Werte aus der Abfrage Zeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="ce401-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Ruft Werte aus Routendaten ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="ce401-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Ruft Werte aus den Feldern für gepostete Formulare ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="ce401-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-Ruft Werte aus dem Anforderungs Text ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="ce401-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-Ruft Werte aus HTTP-Headern ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="ce401-438">Diese Attribute:</span><span class="sxs-lookup"><span data-stu-id="ce401-438">These attributes:</span></span>

* <span data-ttu-id="ce401-439">Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="ce401-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="ce401-440">Akzeptieren optional einen Modellnamenswert im Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="ce401-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="ce401-441">Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="ce401-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="ce401-442">Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="ce401-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="ce401-443">[FromBody]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-443">[FromBody] attribute</span></span>

<span data-ttu-id="ce401-444">Wenden Sie das `[FromBody]`-Attribut auf einen Parameter an, um dessen Eigenschaften über den Text einer HTTP-Anforderung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="ce401-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="ce401-445">Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Texts an einen Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="ce401-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="ce401-446">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="ce401-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="ce401-447">Wenn `[FromBody]` auf einen komplexen Typparameter angewendet wird, werden alle Bindungsquellenattribute ignoriert, die auf die Eigenschaften angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="ce401-448">Die folgende `Create`-Aktion legt beispielsweise fest, dass der `pet`-Parameter mithilfe des Texts aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="ce401-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="ce401-449">Die `Pet`-Klasse legt fest, dass ihre `Breed`-Eigenschaft mithilfe eines Abfragezeichenfolgenparameters aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="ce401-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="ce401-450">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-450">In the preceding example:</span></span>

* <span data-ttu-id="ce401-451">Das `[FromQuery]`-Attribut wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="ce401-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="ce401-452">Die `Breed`-Eigenschaft wird nicht mithilfe eines Abfragezeichenfolgenparameters aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="ce401-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="ce401-453">Eingabeformatierer lesen nur den Text und verstehen Bindungsquellenattribute nicht.</span><span class="sxs-lookup"><span data-stu-id="ce401-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="ce401-454">Wenn ein geeigneter Wert im Text gefunden wird, wird dieser Wert zum Auffüllen der `Breed`-Eigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="ce401-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="ce401-455">Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an.</span><span class="sxs-lookup"><span data-stu-id="ce401-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="ce401-456">Sobald der Anforderungsdatenstrom von einem Eingabeformatierer gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter nochmal gelesen zu werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="ce401-457">Zusätzliche Quellen</span><span class="sxs-lookup"><span data-stu-id="ce401-457">Additional sources</span></span>

<span data-ttu-id="ce401-458">Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ce401-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="ce401-459">Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen.</span><span class="sxs-lookup"><span data-stu-id="ce401-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="ce401-460">Angenommen, Sie möchten Daten aus cookie s oder dem Sitzungszustand.</span><span class="sxs-lookup"><span data-stu-id="ce401-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="ce401-461">So rufen Sie Daten aus einer neuen Quelle ab</span><span class="sxs-lookup"><span data-stu-id="ce401-461">To get data from a new source:</span></span>

* <span data-ttu-id="ce401-462">Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="ce401-463">Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="ce401-464">Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ce401-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="ce401-465">Die Beispiel-app enthält einen [Wert Anbieter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) und ein [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) -Beispiel, mit dem Werte von s abgerufen werden cookie .</span><span class="sxs-lookup"><span data-stu-id="ce401-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="ce401-466">Dies ist der Registrierungscode in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ce401-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="ce401-467">Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.</span><span class="sxs-lookup"><span data-stu-id="ce401-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="ce401-468">Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.</span><span class="sxs-lookup"><span data-stu-id="ce401-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="ce401-469">Keine Quelle für eine Modelleigenschaft</span><span class="sxs-lookup"><span data-stu-id="ce401-469">No source for a model property</span></span>

<span data-ttu-id="ce401-470">Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="ce401-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="ce401-471">Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="ce401-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="ce401-472">Einfache Nullable-Typen werden auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ce401-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="ce401-473">Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ce401-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="ce401-474">Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ce401-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="ce401-475">Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ce401-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="ce401-476">Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="ce401-477">Wenn der Modell Zustand für eine Modell Eigenschaft ungültig gemacht werden soll, wenn nichts gefunden wird, verwenden Sie das- [`[BindRequired]`](#bindrequired-attribute) Attribut.</span><span class="sxs-lookup"><span data-stu-id="ce401-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="ce401-478">Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="ce401-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="ce401-479">Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="ce401-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="ce401-480">Typkonvertierungsfehler</span><span class="sxs-lookup"><span data-stu-id="ce401-480">Type conversion errors</span></span>

<span data-ttu-id="ce401-481">Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="ce401-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="ce401-482">Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.</span><span class="sxs-lookup"><span data-stu-id="ce401-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="ce401-483">In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.</span><span class="sxs-lookup"><span data-stu-id="ce401-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="ce401-484">Zeigen Sie auf einer Razor Seite die Seite erneut mit einer Fehlermeldung an:</span><span class="sxs-lookup"><span data-stu-id="ce401-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="ce401-485">Bei der Client seitigen Validierung werden die meisten ungültigen Daten abgefangen, die andernfalls an ein Seiten Formular übermittelt werden Razor .</span><span class="sxs-lookup"><span data-stu-id="ce401-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="ce401-486">Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen.</span><span class="sxs-lookup"><span data-stu-id="ce401-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="ce401-487">Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet.</span><span class="sxs-lookup"><span data-stu-id="ce401-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="ce401-488">Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="ce401-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="ce401-489">Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ce401-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="ce401-490">Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="ce401-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="ce401-491">Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ce401-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="ce401-492">Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="ce401-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="ce401-493">Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen.</span><span class="sxs-lookup"><span data-stu-id="ce401-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="ce401-494">In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="ce401-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="ce401-495">Einfache Typen</span><span class="sxs-lookup"><span data-stu-id="ce401-495">Simple types</span></span>

<span data-ttu-id="ce401-496">Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="ce401-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="ce401-497">Boolean</span><span class="sxs-lookup"><span data-stu-id="ce401-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="ce401-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="ce401-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="ce401-499">Char</span><span class="sxs-lookup"><span data-stu-id="ce401-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="ce401-500">DateTime</span><span class="sxs-lookup"><span data-stu-id="ce401-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="ce401-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ce401-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="ce401-502">Decimal</span><span class="sxs-lookup"><span data-stu-id="ce401-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="ce401-503">Double</span><span class="sxs-lookup"><span data-stu-id="ce401-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="ce401-504">Enumeration</span><span class="sxs-lookup"><span data-stu-id="ce401-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="ce401-505">GUID</span><span class="sxs-lookup"><span data-stu-id="ce401-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="ce401-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="ce401-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="ce401-507">Single</span><span class="sxs-lookup"><span data-stu-id="ce401-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="ce401-508">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="ce401-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="ce401-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="ce401-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="ce401-510">URI</span><span class="sxs-lookup"><span data-stu-id="ce401-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="ce401-511">Version</span><span class="sxs-lookup"><span data-stu-id="ce401-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="ce401-512">Komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="ce401-512">Complex types</span></span>

<span data-ttu-id="ce401-513">Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können.</span><span class="sxs-lookup"><span data-stu-id="ce401-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="ce401-514">Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="ce401-515">Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="ce401-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="ce401-516">Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="ce401-517">Beim Binden an einen Parameter ist das Präfix der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="ce401-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="ce401-518">Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ce401-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="ce401-519">Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.</span><span class="sxs-lookup"><span data-stu-id="ce401-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="ce401-520">Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="ce401-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="ce401-521">Präfix = Parametername</span><span class="sxs-lookup"><span data-stu-id="ce401-521">Prefix = parameter name</span></span>

<span data-ttu-id="ce401-522">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:</span><span class="sxs-lookup"><span data-stu-id="ce401-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="ce401-523">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="ce401-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="ce401-524">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="ce401-525">Präfix = Name der Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="ce401-525">Prefix = property name</span></span>

<span data-ttu-id="ce401-526">Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:</span><span class="sxs-lookup"><span data-stu-id="ce401-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="ce401-527">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="ce401-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ce401-528">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="ce401-529">Benutzerdefiniertes Präfix</span><span class="sxs-lookup"><span data-stu-id="ce401-529">Custom prefix</span></span>

<span data-ttu-id="ce401-530">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:</span><span class="sxs-lookup"><span data-stu-id="ce401-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="ce401-531">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="ce401-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ce401-532">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="ce401-533">Attribute für Ziele komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="ce401-533">Attributes for complex type targets</span></span>

<span data-ttu-id="ce401-534">Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="ce401-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="ce401-535">Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind.</span><span class="sxs-lookup"><span data-stu-id="ce401-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="ce401-536">Sie haben keine Auswirkungen auf Eingabeformatierer, die bereitgestellte JSON- und XML-Anforderungstexte verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ce401-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="ce401-537">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="ce401-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="ce401-538">Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="ce401-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="ce401-539">[BindRequired]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-539">[BindRequired] attribute</span></span>

<span data-ttu-id="ce401-540">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="ce401-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ce401-541">Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="ce401-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="ce401-542">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="ce401-543">[BindNever]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-543">[BindNever] attribute</span></span>

<span data-ttu-id="ce401-544">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="ce401-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ce401-545">Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt.</span><span class="sxs-lookup"><span data-stu-id="ce401-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="ce401-546">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="ce401-547">[Bind]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-547">[Bind] attribute</span></span>

<span data-ttu-id="ce401-548">Kann auf eine Klasse oder einen Methodenparameter angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="ce401-549">Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ce401-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="ce401-550">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="ce401-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="ce401-551">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="ce401-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="ce401-552">Das `[Bind]`-Attribut kann zum Schutz vor Overposting in *Erstellungs*szenarien (create) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="ce401-553">Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="ce401-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="ce401-554">Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="ce401-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="ce401-555">Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="ce401-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="ce401-556">Sammlungen</span><span class="sxs-lookup"><span data-stu-id="ce401-556">Collections</span></span>

<span data-ttu-id="ce401-557">Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="ce401-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ce401-558">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ce401-559">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-559">For example:</span></span>

* <span data-ttu-id="ce401-560">Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ce401-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="ce401-561">Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:</span><span class="sxs-lookup"><span data-stu-id="ce401-561">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="ce401-562">Das folgende Format wird nur für Formulardaten unterstützt:</span><span class="sxs-lookup"><span data-stu-id="ce401-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="ce401-563">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="ce401-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ce401-564">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="ce401-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="ce401-565">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="ce401-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="ce401-566">Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null).</span><span class="sxs-lookup"><span data-stu-id="ce401-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="ce401-567">Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert.</span><span class="sxs-lookup"><span data-stu-id="ce401-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="ce401-568">Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.</span><span class="sxs-lookup"><span data-stu-id="ce401-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="ce401-569">Wörterbücher</span><span class="sxs-lookup"><span data-stu-id="ce401-569">Dictionaries</span></span>

<span data-ttu-id="ce401-570">Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="ce401-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ce401-571">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="ce401-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ce401-572">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-572">For example:</span></span>

* <span data-ttu-id="ce401-573">Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ce401-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="ce401-574">Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:</span><span class="sxs-lookup"><span data-stu-id="ce401-574">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="ce401-575">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="ce401-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ce401-576">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="ce401-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="ce401-577">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="ce401-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="ce401-578">Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="ce401-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="ce401-579">Der ASP.NET Core-Routenwertanbieter und der Abfragezeichenfolgenwert-Anbieter:</span><span class="sxs-lookup"><span data-stu-id="ce401-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="ce401-580">behandeln Werte als invariante Kulturen.</span><span class="sxs-lookup"><span data-stu-id="ce401-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="ce401-581">erwarten, dass URLs kulturinvariant sind.</span><span class="sxs-lookup"><span data-stu-id="ce401-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="ce401-582">Im Gegensatz dazu durchlaufen Werte, die aus Formulardaten stammen, eine kulturabhängige Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="ce401-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="ce401-583">Dies ist beabsichtigt, damit URLs zwischen Gebietsschemas freigegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="ce401-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="ce401-584">So lassen Sie den ASP.NET Core-Routenwertanbieter und den Abfragezeichenfolgenwert-Anbieter eine kulturabhängige Konvertierung durchlaufen:</span><span class="sxs-lookup"><span data-stu-id="ce401-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="ce401-585">Sie erben von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="ce401-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="ce401-586">Kopieren Sie den Code aus [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) oder [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="ce401-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="ce401-587">Ersetzen Sie den an den Wertanbieterkonstruktor übergebenen [Culture-Wert](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) durch [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="ce401-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="ce401-588">Ersetzen Sie die Standardwertanbieter-Zuordnungsinstanz in den MVC-Optionen durch Ihre neue:</span><span class="sxs-lookup"><span data-stu-id="ce401-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="ce401-589">Spezielle Datentypen</span><span class="sxs-lookup"><span data-stu-id="ce401-589">Special data types</span></span>

<span data-ttu-id="ce401-590">Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="ce401-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="ce401-591">„IFormFile“ und „IFormFileCollection“</span><span class="sxs-lookup"><span data-stu-id="ce401-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="ce401-592">Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="ce401-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="ce401-593">Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ce401-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="ce401-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="ce401-594">CancellationToken</span></span>

<span data-ttu-id="ce401-595">Wird verwendet, um die Aktivität in asynchronen Controllern zu beenden.</span><span class="sxs-lookup"><span data-stu-id="ce401-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="ce401-596">„FormCollection“</span><span class="sxs-lookup"><span data-stu-id="ce401-596">FormCollection</span></span>

<span data-ttu-id="ce401-597">Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="ce401-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="ce401-598">Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="ce401-598">Input formatters</span></span>

<span data-ttu-id="ce401-599">Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein.</span><span class="sxs-lookup"><span data-stu-id="ce401-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="ce401-600">Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="ce401-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="ce401-601">ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten.</span><span class="sxs-lookup"><span data-stu-id="ce401-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="ce401-602">Sie können andere Formatierer für andere Inhaltstypen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="ce401-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="ce401-603">ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus.</span><span class="sxs-lookup"><span data-stu-id="ce401-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="ce401-604">Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="ce401-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="ce401-605">So verwenden Sie die integrierte XML-Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="ce401-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="ce401-606">Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="ce401-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="ce401-607">In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.</span><span class="sxs-lookup"><span data-stu-id="ce401-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="ce401-608">Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.</span><span class="sxs-lookup"><span data-stu-id="ce401-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="ce401-609">Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="ce401-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="ce401-610">Ausschließen angegebener Typen aus der Modellbindung</span><span class="sxs-lookup"><span data-stu-id="ce401-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="ce401-611">Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert.</span><span class="sxs-lookup"><span data-stu-id="ce401-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="ce401-612">Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="ce401-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="ce401-613">Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="ce401-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="ce401-614">Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce401-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ce401-615">Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="ce401-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="ce401-616">Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce401-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ce401-617">Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="ce401-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="ce401-618">Benutzerdefinierte Modellbindungen</span><span class="sxs-lookup"><span data-stu-id="ce401-618">Custom model binders</span></span>

<span data-ttu-id="ce401-619">Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="ce401-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="ce401-620">Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="ce401-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="ce401-621">Manuelle Modellbindung</span><span class="sxs-lookup"><span data-stu-id="ce401-621">Manual model binding</span></span>

<span data-ttu-id="ce401-622">Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="ce401-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="ce401-623">Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="ce401-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="ce401-624">Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ce401-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="ce401-625">Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="ce401-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="ce401-626">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce401-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="ce401-627">[FromServices]-Attribut</span><span class="sxs-lookup"><span data-stu-id="ce401-627">[FromServices] attribute</span></span>

<span data-ttu-id="ce401-628">Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben.</span><span class="sxs-lookup"><span data-stu-id="ce401-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="ce401-629">Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht.</span><span class="sxs-lookup"><span data-stu-id="ce401-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="ce401-630">Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab.</span><span class="sxs-lookup"><span data-stu-id="ce401-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ce401-631">Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ce401-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce401-632">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ce401-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

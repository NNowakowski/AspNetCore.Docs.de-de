---
title: Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core
author: stevejgordon
description: Erfahren Sie mehr über die Verwendung der IHttpClientFactory-Schnittstelle, um logische HttpClient-Instanzen in ASP.NET Core zu verwalten.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
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
uid: fundamentals/http-requests
ms.openlocfilehash: ef6ed0911c2615ea543b1c2cea31a9a453f0c604
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017232"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="af535-103">Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af535-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af535-104">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="af535-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="af535-105"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="af535-106">`IHttpClientFactory` bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="af535-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="af535-107">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="af535-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="af535-108">Ein Client namens *github* kann beispielsweise registriert und für den Zugriff auf [GitHub](https://github.com/) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="af535-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="af535-109">Ein Standardclient kann für den allgemeinen Zugriff registriert werden.</span><span class="sxs-lookup"><span data-stu-id="af535-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="af535-110">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt.</span><span class="sxs-lookup"><span data-stu-id="af535-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="af535-111">Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, um die delegierenden Handler in `HttpClient` zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="af535-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="af535-112">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet.</span><span class="sxs-lookup"><span data-stu-id="af535-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="af535-113">Durch die automatische Verwaltung werden allgemeine DNS-Probleme (Domain Name System) vermieden, die bei der manuellen Verwaltung der Lebensdauer von `HttpClient` auftreten.</span><span class="sxs-lookup"><span data-stu-id="af535-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="af535-114">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="af535-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="af535-115">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="af535-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="af535-116">Der Beispielcode in dieser Version des Themas verwendet <xref:System.Text.Json>, um JSON-Inhalte zu deserialisieren, die in HTTP-Antworten zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="af535-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="af535-117">Verwenden Sie bei Beispielen, die `Json.NET` und `ReadAsAsync<T>` verwenden, die Versionsauswahl, um eine 2.x-Version dieses Themas auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="af535-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="af535-118">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="af535-118">Consumption patterns</span></span>

<span data-ttu-id="af535-119">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="af535-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="af535-120">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="af535-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="af535-121">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="af535-122">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="af535-123">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="af535-124">Der beste Ansatz richtet sich nach den Anforderungen der App.</span><span class="sxs-lookup"><span data-stu-id="af535-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="af535-125">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="af535-125">Basic usage</span></span>

<span data-ttu-id="af535-126">`IHttpClientFactory` kann durch Aufrufen von `AddHttpClient` registriert werden:</span><span class="sxs-lookup"><span data-stu-id="af535-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="af535-127">Eine `IHttpClientFactory`-Schnittstelle kann mithilfe der [Dependency Injection (DI)](xref:fundamentals/dependency-injection) angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="af535-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="af535-128">Im folgenden Code wird `IHttpClientFactory` verwendet, um eine `HttpClient`-Instanz zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="af535-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="af535-129">`IHttpClientFactory` wie im vorhergehenden Beispiel zu verwenden ist eine gute Möglichkeit zum Umgestalten einer vorhandene App.</span><span class="sxs-lookup"><span data-stu-id="af535-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="af535-130">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="af535-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="af535-131">An Stellen, an denen `HttpClient`-Instanzen in einer vorhandenen App erstellt werden, können Sie diese Ereignisse mit Aufrufen von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="af535-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="af535-132">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-132">Named clients</span></span>

<span data-ttu-id="af535-133">Benannte Clients sind in folgenden Fällen eine gute Wahl:</span><span class="sxs-lookup"><span data-stu-id="af535-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="af535-134">Die App erfordert viele verschiedene Verwendungen von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="af535-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="af535-135">Viele `HttpClient`s verfügen über unterschiedliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="af535-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="af535-136">Die Konfiguration eines benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="af535-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="af535-137">Im vorangehenden Code wird der Client mit Folgendem konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="af535-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="af535-138">der Basisadresse `https://api.github.com/`</span><span class="sxs-lookup"><span data-stu-id="af535-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="af535-139">zwei Header, die für die Arbeit mit der GitHub-API erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="af535-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="af535-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="af535-140">CreateClient</span></span>

<span data-ttu-id="af535-141">Jedes Mal, wenn <xref:System.Net.Http.IHttpClientFactory.CreateClient*> aufgerufen wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="af535-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="af535-142">Eine neue Instanz von `HttpClient` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="af535-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="af535-143">Die Konfigurationsaktion wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="af535-143">The configuration action is called.</span></span>

<span data-ttu-id="af535-144">Übergeben Sie für die Erstellung eines benannten Clients dessen Namen an `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="af535-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="af535-145">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="af535-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="af535-146">Der Code muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="af535-147">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-147">Typed clients</span></span>

<span data-ttu-id="af535-148">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="af535-148">Typed clients:</span></span>

* <span data-ttu-id="af535-149">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="af535-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="af535-150">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="af535-151">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="af535-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="af535-152">Beispielsweise kann ein einzelner typisierter Client für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="af535-153">für einen einzelnen Back-End-Endpunkt</span><span class="sxs-lookup"><span data-stu-id="af535-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="af535-154">um die gesamte Logik zu kapseln, die den Endpunkt behandelt</span><span class="sxs-lookup"><span data-stu-id="af535-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="af535-155">Funktionieren mit DI und können überall in der App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="af535-156">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="af535-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="af535-157">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="af535-157">In the preceding code:</span></span>

* <span data-ttu-id="af535-158">Die Konfiguration wird in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="af535-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="af535-159">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="af535-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="af535-160">Es können API-spezifische Methoden erstellt werden, die die `HttpClient`-Funktionalität verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="af535-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="af535-161">Die `GetAspNetDocsIssues`-Methode kapselt z. B. Code zum Abrufen offener Probleme.</span><span class="sxs-lookup"><span data-stu-id="af535-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="af535-162">Der folgende Code ruft <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` auf, um eine typisierte Clientklasse zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="af535-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="af535-163">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="af535-164">Im vorherigen Code registriert `AddHttpClient` `GitHubService` als vorübergehenden Dienst.</span><span class="sxs-lookup"><span data-stu-id="af535-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="af535-165">Diese Registrierung verwendet eine Factorymethode für folgende Aktionen:</span><span class="sxs-lookup"><span data-stu-id="af535-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="af535-166">Erstellen Sie eine Instanz von `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="af535-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="af535-167">Erstellen einer `GitHubService`-Instanz, wobei die Instanz von `HttpClient` an ihren Konstrukt übergeben wird</span><span class="sxs-lookup"><span data-stu-id="af535-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="af535-168">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="af535-169">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients:</span><span class="sxs-lookup"><span data-stu-id="af535-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="af535-170">`HttpClient` kann in einem typisierten Client gekapselt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="af535-171">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, definieren Sie eine Methode, die die `HttpClient`-Instanz intern aufruft:</span><span class="sxs-lookup"><span data-stu-id="af535-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="af535-172">Im vorangehenden Code wird `HttpClient` in einem privaten Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="af535-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="af535-173">Der Zugriff auf `HttpClient` erfolgt durch die öffentliche `GetRepos`-Methode.</span><span class="sxs-lookup"><span data-stu-id="af535-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="af535-174">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-174">Generated clients</span></span>

<span data-ttu-id="af535-175">`IHttpClientFactory` kann in Verbindung mit Bibliotheken von Drittanbietern verwendet werden, z. B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="af535-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="af535-176">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="af535-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="af535-177">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="af535-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="af535-178">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="af535-179">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="af535-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="af535-180">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="af535-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="af535-181">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="af535-182">Stellen von POST-, PUT- und DELETE-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="af535-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="af535-183">In den vorangehenden Beispielen verwenden alle Anforderungen das GET-HTTP-Verb.</span><span class="sxs-lookup"><span data-stu-id="af535-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="af535-184">`HttpClient` unterstützt ebenso andere HTTP-Verben, einschließlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="af535-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="af535-185">POST</span><span class="sxs-lookup"><span data-stu-id="af535-185">POST</span></span>
* <span data-ttu-id="af535-186">PUT</span><span class="sxs-lookup"><span data-stu-id="af535-186">PUT</span></span>
* <span data-ttu-id="af535-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="af535-187">DELETE</span></span>
* <span data-ttu-id="af535-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="af535-188">PATCH</span></span>

<span data-ttu-id="af535-189">Eine komplette Liste der unterstützten HTTP-Verben finden Sie unter <xref:System.Net.Http.HttpMethod>.</span><span class="sxs-lookup"><span data-stu-id="af535-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="af535-190">Im folgenden Beispiel wird gezeigt, wie Sie eine HTTP-POST-Anforderung stellen:</span><span class="sxs-lookup"><span data-stu-id="af535-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="af535-191">Für die `CreateItemAsync`-Methoden im Code oben gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="af535-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="af535-192">Sie serialisiert den `TodoItem`-Parameter mithilfe von `System.Text.Json` in JSON.</span><span class="sxs-lookup"><span data-stu-id="af535-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="af535-193">Dabei wird eine Instanz von <xref:System.Text.Json.JsonSerializerOptions> verwenden, um den Serialisierungsprozess zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="af535-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="af535-194">Sie erstellt eine Instanz von <xref:System.Net.Http.StringContent>, um den serialisierten JSON-Code zum Senden im HTTP-Anforderungstext zu packen.</span><span class="sxs-lookup"><span data-stu-id="af535-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="af535-195">Die Methode ruft <xref:System.Net.Http.HttpClient.PostAsync%2A> auf, um den JSON-Inhalt an die angegebene URL zu senden.</span><span class="sxs-lookup"><span data-stu-id="af535-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="af535-196">Dies ist eine relative URL, die zu [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="af535-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="af535-197">Sie ruft <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> auf, um eine Ausnahme auszulösen, wenn der Antwortstatuscode nicht auf Erfolg hinweist.</span><span class="sxs-lookup"><span data-stu-id="af535-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="af535-198">`HttpClient` unterstützt auch andere Inhaltstypen.</span><span class="sxs-lookup"><span data-stu-id="af535-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="af535-199">Beispiel: <xref:System.Net.Http.MultipartContent> und <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="af535-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="af535-200">Eine komplette Liste der unterstützten Inhaltstypen finden Sie unter <xref:System.Net.Http.HttpContent>.</span><span class="sxs-lookup"><span data-stu-id="af535-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="af535-201">Das folgende Beispiel zeigt eine HTTP-PUT-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="af535-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="af535-202">Der vorangehende Code ist dem POST-Beispiel sehr ähnlich.</span><span class="sxs-lookup"><span data-stu-id="af535-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="af535-203">Die `SaveItemAsync`-Methode ruft <xref:System.Net.Http.HttpClient.PutAsync%2A> anstelle von `PostAsync` auf.</span><span class="sxs-lookup"><span data-stu-id="af535-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="af535-204">Das folgende Beispiel zeigt eine HTTP-DELETE-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="af535-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="af535-205">Im vorangehenden Code ruft die `DeleteItemAsync`-Methode <xref:System.Net.Http.HttpClient.DeleteAsync%2A> auf.</span><span class="sxs-lookup"><span data-stu-id="af535-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="af535-206">Da HTTP-DELETE-Anforderungen normalerweise keinen Text enthalten, stellt die `DeleteAsync`-Methode keine Überladung bereit, die eine Instanz von `HttpContent` akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="af535-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="af535-207">Weitere Informationen zur Verwendung unterschiedlicher HTTP-Verben mit `HttpClient` finden Sie unter <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="af535-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="af535-208">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="af535-208">Outgoing request middleware</span></span>

<span data-ttu-id="af535-209">`HttpClient` enthält das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="af535-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="af535-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="af535-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="af535-211">Erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden</span><span class="sxs-lookup"><span data-stu-id="af535-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="af535-212">Unterstützt die Registrierung und Verkettung von mehreren Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="af535-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="af535-213">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="af535-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="af535-214">Dieses Muster:</span><span class="sxs-lookup"><span data-stu-id="af535-214">This pattern:</span></span>

  * <span data-ttu-id="af535-215">ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af535-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="af535-216">bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, z. B.:</span><span class="sxs-lookup"><span data-stu-id="af535-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="af535-217">Zwischenspeicherung</span><span class="sxs-lookup"><span data-stu-id="af535-217">caching</span></span>
    * <span data-ttu-id="af535-218">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="af535-218">error handling</span></span>
    * <span data-ttu-id="af535-219">Serialisierung</span><span class="sxs-lookup"><span data-stu-id="af535-219">serialization</span></span>
    * <span data-ttu-id="af535-220">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="af535-220">logging</span></span>

<span data-ttu-id="af535-221">So erstellen Sie einen delegierenden Handler:</span><span class="sxs-lookup"><span data-stu-id="af535-221">To create a delegating handler:</span></span>

* <span data-ttu-id="af535-222">Leiten Sie von <xref:System.Net.Http.DelegatingHandler> ab.</span><span class="sxs-lookup"><span data-stu-id="af535-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="af535-223">Überschreiben Sie <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="af535-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="af535-224">Führen Sie Code aus, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="af535-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="af535-225">Der vorangehende Code überprüft, ob die Anforderung einen `X-API-KEY`-Header enthält.</span><span class="sxs-lookup"><span data-stu-id="af535-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="af535-226">Wenn `X-API-KEY` fehlt, wird <xref:System.Net.HttpStatusCode.BadRequest> zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="af535-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="af535-227">Für eine `HttpClient`-Klasse mit <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> kann mehr als ein Handler zur Konfiguration hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="af535-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="af535-228">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="af535-229">Die `IHttpClientFactory` erstellt einen separaten DI-Bereich für jeden Handler.</span><span class="sxs-lookup"><span data-stu-id="af535-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="af535-230">Handler können von Diensten eines beliebigen Bereichs abhängen.</span><span class="sxs-lookup"><span data-stu-id="af535-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="af535-231">Dienste, von denen Handler abhängig sind, werden verworfen, wenn der Handler verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="af535-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="af535-232">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, was den Typ für den Handler übergibt.</span><span class="sxs-lookup"><span data-stu-id="af535-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="af535-233">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="af535-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="af535-234">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="af535-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="af535-235">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="af535-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="af535-236">Übergeben Sie mithilfe von [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) Daten an den Handler.</span><span class="sxs-lookup"><span data-stu-id="af535-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="af535-237">Greifen Sie mit <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="af535-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="af535-238">Erstellen Sie ein benutzerdefiniertes <xref:System.Threading.AsyncLocal`1>-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="af535-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="af535-239">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="af535-239">Use Polly-based handlers</span></span>

<span data-ttu-id="af535-240">`IHttpClientFactory` ist mit der Drittanbieterbibliothek [Polly](https://github.com/App-vNext/Polly) integriert.</span><span class="sxs-lookup"><span data-stu-id="af535-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="af535-241">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="af535-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="af535-242">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="af535-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="af535-243">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="af535-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="af535-244">Die Polly-Erweiterungen unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="af535-245">Polly erfordert das [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="af535-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="af535-246">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="af535-246">Handle transient faults</span></span>

<span data-ttu-id="af535-247">Fehler treten normalerweise auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="af535-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="af535-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> ermöglicht die Definition einer Richtlinie, um vorübergehende Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="af535-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="af535-249">Mit `AddTransientHttpErrorPolicy` konfigurierte Richtlinien behandeln die folgenden Antworten:</span><span class="sxs-lookup"><span data-stu-id="af535-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="af535-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="af535-250">HTTP 5xx</span></span>
* <span data-ttu-id="af535-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="af535-251">HTTP 408</span></span>

<span data-ttu-id="af535-252">`AddTransientHttpErrorPolicy` ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="af535-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="af535-253">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="af535-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="af535-254">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="af535-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="af535-255">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="af535-255">Dynamically select policies</span></span>

<span data-ttu-id="af535-256">Erweiterungsmethoden werden zum Hinzufügen von Polly-basierten Handlern bereitgestellt, z. B. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="af535-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="af535-257">Der folgende `AddPolicyHandler`-Überladung prüft die Anforderung, um zu entscheiden, welche Richtlinie angewendet werden soll:</span><span class="sxs-lookup"><span data-stu-id="af535-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="af535-258">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="af535-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="af535-259">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="af535-260">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="af535-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="af535-261">Es ist üblich, Polly-Richtlinien zu schachteln:</span><span class="sxs-lookup"><span data-stu-id="af535-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="af535-262">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="af535-262">In the preceding example:</span></span>

* <span data-ttu-id="af535-263">Zwei Handler werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="af535-263">Two handlers are added.</span></span>
* <span data-ttu-id="af535-264">Der erste Handler verwendet <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="af535-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="af535-265">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="af535-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="af535-266">Der zweite `AddTransientHttpErrorPolicy`-Aufruf fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="af535-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="af535-267">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="af535-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="af535-268">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="af535-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="af535-269">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="af535-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="af535-270">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="af535-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="af535-271">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="af535-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="af535-272">Im folgenden Code wird Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="af535-272">In the following code:</span></span>

* <span data-ttu-id="af535-273">Die Richtlinien „regular“ und „long“ werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="af535-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="af535-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> fügt die Richtlinien „regular“ und „long“ aus der Registrierung hinzu.</span><span class="sxs-lookup"><span data-stu-id="af535-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="af535-275">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly-Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="af535-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="af535-276">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="af535-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="af535-277">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="af535-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="af535-278">Pro benannter Client wird ein <xref:System.Net.Http.HttpMessageHandler> erstellt.</span><span class="sxs-lookup"><span data-stu-id="af535-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="af535-279">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="af535-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="af535-280">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="af535-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="af535-281">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="af535-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="af535-282">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="af535-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="af535-283">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="af535-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="af535-284">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen (Domain Name System) reagiert.</span><span class="sxs-lookup"><span data-stu-id="af535-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="af535-285">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="af535-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="af535-286">Der Standardwert kann für jeden benannten Client überschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="af535-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="af535-287">`HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die **nicht** verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="af535-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="af535-288">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="af535-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="af535-289">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="af535-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="af535-290">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="af535-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="af535-291">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="af535-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="af535-292">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="af535-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="af535-293">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="af535-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="af535-294">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="af535-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="af535-295">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="af535-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="af535-296">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="af535-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="af535-297">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="af535-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="af535-298">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="af535-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="af535-299">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="af535-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="af535-300">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="af535-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="af535-301">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="af535-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="af535-302">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="af535-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="af535-303">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="af535-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="af535-304">Cookie</span><span class="sxs-lookup"><span data-stu-id="af535-304">Cookies</span></span>

<span data-ttu-id="af535-305">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="af535-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="af535-306">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="af535-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="af535-307">Ziehen Sie für Apps, die cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="af535-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="af535-308">Deaktivieren der automatischen cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="af535-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="af535-309">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="af535-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="af535-310">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="af535-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="af535-311">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="af535-311">Logging</span></span>

<span data-ttu-id="af535-312">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="af535-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="af535-313">Aktivieren Sie die entsprechende Informationsebene in der Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="af535-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="af535-314">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="af535-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="af535-315">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="af535-316">Ein Client namens *MyNamedClient* protokolliert z. B. Nachrichten mit der Kategorie „System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler“.</span><span class="sxs-lookup"><span data-stu-id="af535-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="af535-317">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="af535-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="af535-318">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="af535-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="af535-319">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="af535-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="af535-320">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="af535-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="af535-321">Im Beispiel von *MyNamedClient* werden diese Nachrichten mit der Protokollkategorie „System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler“ protokolliert.</span><span class="sxs-lookup"><span data-stu-id="af535-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="af535-322">Für die Anforderung erfolgt dies, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="af535-323">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="af535-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="af535-324">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="af535-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="af535-325">Dies kann die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="af535-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="af535-326">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="af535-327">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="af535-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="af535-328">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="af535-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="af535-329">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="af535-330">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="af535-331">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="af535-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="af535-332">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="af535-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="af535-333">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="af535-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="af535-334">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="af535-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="af535-335">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="af535-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="af535-336">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="af535-336">In the following example:</span></span>

* <span data-ttu-id="af535-337"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="af535-338">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="af535-339">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="af535-340">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="af535-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="af535-341">Middleware für Headerweitergabe</span><span class="sxs-lookup"><span data-stu-id="af535-341">Header propagation middleware</span></span>

<span data-ttu-id="af535-342">Für die Headerweitergabe wird eine ASP.NET Core-Middleware verwendet, um HTTP-Header von eingehenden Anforderungen an ausgehende HTTP-Clientanforderungen weiterzugeben.</span><span class="sxs-lookup"><span data-stu-id="af535-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="af535-343">So verwenden Sie die Headerweitergabe:</span><span class="sxs-lookup"><span data-stu-id="af535-343">To use header propagation:</span></span>

* <span data-ttu-id="af535-344">Erstellen Sie einen Verweis auf das [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)-Paket.</span><span class="sxs-lookup"><span data-stu-id="af535-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="af535-345">Konfigurieren Sie in `Startup` die Middleware und `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="af535-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="af535-346">Der Client schließt die konfigurierten Header für ausgehende Anforderungen ein:</span><span class="sxs-lookup"><span data-stu-id="af535-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="af535-347">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="af535-347">Additional resources</span></span>

* [<span data-ttu-id="af535-348">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="af535-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="af535-349">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="af535-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="af535-350">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="af535-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="af535-351">Serialisieren und Deserialisieren von JSON-Daten in .NET</span><span class="sxs-lookup"><span data-stu-id="af535-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="af535-352">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), und [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="af535-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="af535-353"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="af535-354">Dies hat folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="af535-354">It offers the following benefits:</span></span>

* <span data-ttu-id="af535-355">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="af535-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="af535-356">Ein *GitHub*-Client kann beispielsweise registriert werden und so konfiguriert werden, um auf [GitHub](https://github.com/) zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="af535-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="af535-357">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="af535-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="af535-358">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt. Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, die dies nutzen.</span><span class="sxs-lookup"><span data-stu-id="af535-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="af535-359">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="af535-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="af535-360">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="af535-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="af535-361">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af535-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="af535-362">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="af535-362">Consumption patterns</span></span>

<span data-ttu-id="af535-363">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="af535-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="af535-364">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="af535-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="af535-365">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="af535-366">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="af535-367">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="af535-368">Keine dieser Möglichkeiten ist einer anderen überlegen.</span><span class="sxs-lookup"><span data-stu-id="af535-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="af535-369">Der beste Ansatz richtet sich nach den Einschränkungen der App.</span><span class="sxs-lookup"><span data-stu-id="af535-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="af535-370">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="af535-370">Basic usage</span></span>

<span data-ttu-id="af535-371">`IHttpClientFactory` kann durch Aufrufen der Erweiterungsmethode `AddHttpClient` auf `IServiceCollection` in der Methode `Startup.ConfigureServices` registriert werden.</span><span class="sxs-lookup"><span data-stu-id="af535-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="af535-372">Nach der Registrierung kann Code `IHttpClientFactory` überall akzeptieren, wo Dienste mithilfe der [Dependency Injection](xref:fundamentals/dependency-injection) (DI) eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="af535-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="af535-373">`IHttpClientFactory` kann zum Erstellen einer `HttpClient`-Instanz verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="af535-374">`IHttpClientFactory` auf diese Weise zu verwenden, ist eine gute Möglichkeit zum Umgestalten einer vorhandenen App.</span><span class="sxs-lookup"><span data-stu-id="af535-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="af535-375">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="af535-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="af535-376">An Stellen, in denen `HttpClient`-Instanzen derzeit erstellt werden, können Sie diese Ereignisse mit einem Aufruf von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="af535-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="af535-377">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-377">Named clients</span></span>

<span data-ttu-id="af535-378">Wenn eine App mehrere verschiedene Verwendungen von `HttpClient` mit jeweils unterschiedlicher Konfiguration erfordert, ist die Verwendung von **benannten Clients** eine Option.</span><span class="sxs-lookup"><span data-stu-id="af535-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="af535-379">Die Konfiguration einer benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="af535-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="af535-380">Im oben stehenden Code wird `AddHttpClient` aufgerufen, und dabei wird der Name *github* angegeben.</span><span class="sxs-lookup"><span data-stu-id="af535-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="af535-381">Für diesen Client wird Standardkonfiguration angewendet, d.h. die Basisadresse und zwei Header, die erforderlich sind, um mit der GitHub-API zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="af535-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="af535-382">Wenn `CreateClient` aufgerufen wird, wird jedes Mal eine neue Instanz von `HttpClient` erstellt und die Konfigurationsaktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="af535-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="af535-383">Zum Verarbeiten eines benannten Clients kann ein Zeichenfolgenparameter an `CreateClient` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="af535-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="af535-384">Geben Sie den Namen des zu erstellenden Clients an:</span><span class="sxs-lookup"><span data-stu-id="af535-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="af535-385">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="af535-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="af535-386">Sie muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="af535-387">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-387">Typed clients</span></span>

<span data-ttu-id="af535-388">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="af535-388">Typed clients:</span></span>

* <span data-ttu-id="af535-389">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="af535-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="af535-390">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="af535-391">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="af535-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="af535-392">Zum Beispiel kann ein einzelner typisierter Client für einen einzelnen Back-End-Endpunkt verwendet werden, der jegliche Logik kapselt, die mit diesem Endpunkt interagiert.</span><span class="sxs-lookup"><span data-stu-id="af535-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="af535-393">Funktionieren mit DI und können überall in Ihrer App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="af535-394">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="af535-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="af535-395">Im vorangehenden Code wird die Konfiguration in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="af535-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="af535-396">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="af535-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="af535-397">Es ist möglich, API-spezifische Methoden zu definieren, die die `HttpClient`-Funktionalität zur Verfügung stellen.</span><span class="sxs-lookup"><span data-stu-id="af535-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="af535-398">Die Methode `GetAspNetDocsIssues` kapselt den Code, der für die Abfrage und Analyse des neuesten offenen Problems aus dem GitHub-Repository erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="af535-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="af535-399">Zum Registrieren eines typisierten Clients kann die generische Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse angeben:</span><span class="sxs-lookup"><span data-stu-id="af535-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="af535-400">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="af535-401">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="af535-402">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients, falls dies bevorzugt wird:</span><span class="sxs-lookup"><span data-stu-id="af535-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="af535-403">Es ist möglich, `HttpClient` vollständig in einem typisierten Client zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="af535-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="af535-404">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, können öffentliche Methoden bereitgestellt werden, die die `HttpClient`-Instanz intern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="af535-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="af535-405">Im vorangehenden Code wird `HttpClient` als ein privates Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="af535-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="af535-406">Alle Zugriffe, die externe Aufrufe durchführen, durchlaufen die Methode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="af535-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="af535-407">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-407">Generated clients</span></span>

<span data-ttu-id="af535-408">`IHttpClientFactory` kann in Verbindung mit anderen Bibliotheken von Drittanbietern verwendet werden, z.B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="af535-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="af535-409">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="af535-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="af535-410">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="af535-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="af535-411">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="af535-412">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="af535-412">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="af535-413">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="af535-413">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="af535-414">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="af535-415">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="af535-415">Outgoing request middleware</span></span>

<span data-ttu-id="af535-416">`HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="af535-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="af535-417">`IHttpClientFactory` erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="af535-418">Die Registrierung und Verkettung von mehreren Handlern wird unterstützt, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="af535-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="af535-419">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="af535-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="af535-420">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af535-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="af535-421">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="af535-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="af535-422">Definieren Sie zum Erstellen eines Handlers eine von <xref:System.Net.Http.DelegatingHandler> abgeleitete Klasse.</span><span class="sxs-lookup"><span data-stu-id="af535-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="af535-423">Überschreiben Sie die Methode `SendAsync`, um Code auszuführen, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="af535-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="af535-424">Der vorangehende Code definiert einen einfachen Handler.</span><span class="sxs-lookup"><span data-stu-id="af535-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="af535-425">Er überprüft, ob ein `X-API-KEY`-Header in die Anforderung eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="af535-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="af535-426">Wenn der Header fehlt, kann er den HTTP-Aufruf vermeiden und eine geeignete Antwort zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="af535-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="af535-427">Während der Registrierung kann mindestens ein Handler der Konfiguration eines `HttpClient` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="af535-428">Dieser Vorgang wird über die Erweiterungsmethoden von <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="af535-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="af535-429">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="af535-430">Die `IHttpClientFactory` erstellt einen separaten DI-Bereich für jeden Handler.</span><span class="sxs-lookup"><span data-stu-id="af535-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="af535-431">Handler können von Diensten eines beliebigen Bereichs abhängen.</span><span class="sxs-lookup"><span data-stu-id="af535-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="af535-432">Dienste, von denen Handler abhängig sind, werden verworfen, wenn der Handler verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="af535-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="af535-433">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, was den Typ für den Handler übergibt.</span><span class="sxs-lookup"><span data-stu-id="af535-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="af535-434">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="af535-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="af535-435">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="af535-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="af535-436">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="af535-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="af535-437">Übergeben Sie Daten mit `HttpRequestMessage.Properties` an den Handler.</span><span class="sxs-lookup"><span data-stu-id="af535-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="af535-438">Greifen Sie mit `IHttpContextAccessor` auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="af535-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="af535-439">Erstellen Sie ein benutzerdefiniertes `AsyncLocal`-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="af535-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="af535-440">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="af535-440">Use Polly-based handlers</span></span>

<span data-ttu-id="af535-441">`IHttpClientFactory` integriert mit der beliebten Drittanbieter-Bibliothek namens [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="af535-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="af535-442">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="af535-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="af535-443">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="af535-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="af535-444">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="af535-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="af535-445">Die Polly-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="af535-445">The Polly extensions:</span></span>

* <span data-ttu-id="af535-446">Unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="af535-447">Können nach Installation des [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Pakets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="af535-448">Das Paket ist nicht im freigegebenen ASP.NET Core-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="af535-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="af535-449">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="af535-449">Handle transient faults</span></span>

<span data-ttu-id="af535-450">Am häufigsten treten Fehler auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="af535-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="af535-451">Eine praktische Erweiterungsmethode namens `AddTransientHttpErrorPolicy` ist enthalten. Sie ermöglicht das Definieren von Richtlinien zum Behandeln vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="af535-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="af535-452">Richtlinien, die mit dieser Erweiterungsmethode konfiguriert wurden, behandeln `HttpRequestException`, HTTP 5xx-Antworten und HTTP 408-Antworten.</span><span class="sxs-lookup"><span data-stu-id="af535-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="af535-453">Die `AddTransientHttpErrorPolicy`-Erweiterung kann in `Startup.ConfigureServices` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="af535-454">Die Erweiterung ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="af535-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="af535-455">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="af535-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="af535-456">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="af535-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="af535-457">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="af535-457">Dynamically select policies</span></span>

<span data-ttu-id="af535-458">Es gibt zusätzliche Erweiterungsmethoden, die zum Hinzufügen von Polly-basierten Handlern verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="af535-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="af535-459">Eine dieser Erweiterungen ist `AddPolicyHandler`, die über mehrere Überladungen verfügt.</span><span class="sxs-lookup"><span data-stu-id="af535-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="af535-460">Eine Überladung ermöglicht, dass die Anforderung beim Definieren der zu verwendenden Richtlinie überprüft werden kann:</span><span class="sxs-lookup"><span data-stu-id="af535-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="af535-461">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="af535-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="af535-462">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="af535-463">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="af535-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="af535-464">Es ist üblich, Polly-Richtlinien zu schachteln, um erweiterte Funktionen bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="af535-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="af535-465">Im vorangehenden Beispiel werden zwei Handler hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="af535-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="af535-466">Der Erste verwendet die `AddTransientHttpErrorPolicy`-Erweiterung, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="af535-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="af535-467">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="af535-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="af535-468">Der zweite Aufruf von `AddTransientHttpErrorPolicy` fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="af535-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="af535-469">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="af535-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="af535-470">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="af535-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="af535-471">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="af535-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="af535-472">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="af535-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="af535-473">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="af535-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="af535-474">Eine Erweiterungsmethode wird bereitgestellt, die einem Handler ermöglicht, mithilfe einer Richtlinie aus der Registrierung hinzugefügt zu werden:</span><span class="sxs-lookup"><span data-stu-id="af535-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="af535-475">Im oben stehenden Code werden zwei Richtlinien registriert, wenn `PolicyRegistry` zu `ServiceCollection` hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="af535-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="af535-476">Damit eine Richtlinie aus der Registrierung verwendet werden kann, wird die Methode `AddPolicyHandlerFromRegistry` verwendet. Dabei wird der Name der anzuwendenden Richtlinie übergeben.</span><span class="sxs-lookup"><span data-stu-id="af535-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="af535-477">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="af535-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="af535-478">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="af535-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="af535-479">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="af535-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="af535-480">Es gibt einen <xref:System.Net.Http.HttpMessageHandler> pro benanntem Client.</span><span class="sxs-lookup"><span data-stu-id="af535-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="af535-481">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="af535-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="af535-482">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="af535-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="af535-483">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="af535-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="af535-484">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="af535-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="af535-485">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="af535-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="af535-486">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="af535-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="af535-487">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="af535-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="af535-488">Der Standardwert kann für jeden benannten Client überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="af535-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="af535-489">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> auf dem bei der Erstellung des Clients zurückgegebenen `IHttpClientBuilder` auf, um den Wert zu überschreiben:</span><span class="sxs-lookup"><span data-stu-id="af535-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="af535-490">Das Verwerfen des Client ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="af535-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="af535-491">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="af535-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="af535-492">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="af535-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="af535-493">Die `HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die nicht verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="af535-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="af535-494">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="af535-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="af535-495">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="af535-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="af535-496">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="af535-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="af535-497">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="af535-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="af535-498">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="af535-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="af535-499">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="af535-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="af535-500">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="af535-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="af535-501">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="af535-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="af535-502">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="af535-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="af535-503">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="af535-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="af535-504">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="af535-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="af535-505">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="af535-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="af535-506">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="af535-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="af535-507">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="af535-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="af535-508">Cookie</span><span class="sxs-lookup"><span data-stu-id="af535-508">Cookies</span></span>

<span data-ttu-id="af535-509">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="af535-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="af535-510">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="af535-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="af535-511">Ziehen Sie für Apps, die cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="af535-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="af535-512">Deaktivieren der automatischen cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="af535-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="af535-513">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="af535-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="af535-514">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="af535-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="af535-515">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="af535-515">Logging</span></span>

<span data-ttu-id="af535-516">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="af535-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="af535-517">Aktivieren Sie die entsprechende Informationsebene in Ihrer Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="af535-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="af535-518">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="af535-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="af535-519">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="af535-520">Ein Client namens *MyNamedClient* protokolliert beispielsweise Meldungen mit der Kategorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="af535-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="af535-521">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="af535-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="af535-522">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="af535-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="af535-523">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="af535-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="af535-524">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="af535-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="af535-525">Im Beispiel *MyNamedClient* werden diese Meldungen für die Protokollkategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` protokolliert.</span><span class="sxs-lookup"><span data-stu-id="af535-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="af535-526">Dies findet für die Anforderung statt, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung an das Netzwerk gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="af535-527">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="af535-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="af535-528">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="af535-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="af535-529">Dies kann beispielsweise die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="af535-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="af535-530">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert bei Bedarf die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="af535-531">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="af535-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="af535-532">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="af535-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="af535-533">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="af535-534">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="af535-535">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="af535-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="af535-536">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="af535-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="af535-537">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="af535-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="af535-538">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="af535-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="af535-539">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="af535-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="af535-540">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="af535-540">In the following example:</span></span>

* <span data-ttu-id="af535-541"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="af535-542">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="af535-543">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="af535-544">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="af535-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="af535-545">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="af535-545">Additional resources</span></span>

* [<span data-ttu-id="af535-546">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="af535-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="af535-547">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="af535-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="af535-548">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="af535-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="af535-549">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), und [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="af535-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="af535-550"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="af535-551">Dies hat folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="af535-551">It offers the following benefits:</span></span>

* <span data-ttu-id="af535-552">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="af535-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="af535-553">Ein *GitHub*-Client kann beispielsweise registriert werden und so konfiguriert werden, um auf [GitHub](https://github.com/) zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="af535-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="af535-554">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="af535-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="af535-555">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt. Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, die dies nutzen.</span><span class="sxs-lookup"><span data-stu-id="af535-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="af535-556">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="af535-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="af535-557">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="af535-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="af535-558">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af535-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="af535-559">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="af535-559">Prerequisites</span></span>

<span data-ttu-id="af535-560">Für Projekte mit der Zielplattform .NET Framework muss das NuGet-Paket [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) installiert werden.</span><span class="sxs-lookup"><span data-stu-id="af535-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="af535-561">Projekte für .NET Core, die auf das [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) verweisen, enthalten bereits das `Microsoft.Extensions.Http`-Paket.</span><span class="sxs-lookup"><span data-stu-id="af535-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="af535-562">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="af535-562">Consumption patterns</span></span>

<span data-ttu-id="af535-563">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="af535-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="af535-564">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="af535-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="af535-565">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="af535-566">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="af535-567">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="af535-568">Keine dieser Möglichkeiten ist einer anderen überlegen.</span><span class="sxs-lookup"><span data-stu-id="af535-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="af535-569">Der beste Ansatz richtet sich nach den Einschränkungen der App.</span><span class="sxs-lookup"><span data-stu-id="af535-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="af535-570">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="af535-570">Basic usage</span></span>

<span data-ttu-id="af535-571">`IHttpClientFactory` kann durch Aufrufen der Erweiterungsmethode `AddHttpClient` auf `IServiceCollection` in der Methode `Startup.ConfigureServices` registriert werden.</span><span class="sxs-lookup"><span data-stu-id="af535-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="af535-572">Nach der Registrierung kann Code `IHttpClientFactory` überall akzeptieren, wo Dienste mithilfe der [Dependency Injection](xref:fundamentals/dependency-injection) (DI) eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="af535-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="af535-573">`IHttpClientFactory` kann zum Erstellen einer `HttpClient`-Instanz verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="af535-574">`IHttpClientFactory` auf diese Weise zu verwenden, ist eine gute Möglichkeit zum Umgestalten einer vorhandenen App.</span><span class="sxs-lookup"><span data-stu-id="af535-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="af535-575">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="af535-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="af535-576">An Stellen, in denen `HttpClient`-Instanzen derzeit erstellt werden, können Sie diese Ereignisse mit einem Aufruf von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="af535-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="af535-577">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-577">Named clients</span></span>

<span data-ttu-id="af535-578">Wenn eine App mehrere verschiedene Verwendungen von `HttpClient` mit jeweils unterschiedlicher Konfiguration erfordert, ist die Verwendung von **benannten Clients** eine Option.</span><span class="sxs-lookup"><span data-stu-id="af535-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="af535-579">Die Konfiguration einer benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="af535-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="af535-580">Im oben stehenden Code wird `AddHttpClient` aufgerufen, und dabei wird der Name *github* angegeben.</span><span class="sxs-lookup"><span data-stu-id="af535-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="af535-581">Für diesen Client wird Standardkonfiguration angewendet, d.h. die Basisadresse und zwei Header, die erforderlich sind, um mit der GitHub-API zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="af535-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="af535-582">Wenn `CreateClient` aufgerufen wird, wird jedes Mal eine neue Instanz von `HttpClient` erstellt und die Konfigurationsaktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="af535-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="af535-583">Zum Verarbeiten eines benannten Clients kann ein Zeichenfolgenparameter an `CreateClient` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="af535-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="af535-584">Geben Sie den Namen des zu erstellenden Clients an:</span><span class="sxs-lookup"><span data-stu-id="af535-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="af535-585">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="af535-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="af535-586">Sie muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="af535-587">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-587">Typed clients</span></span>

<span data-ttu-id="af535-588">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="af535-588">Typed clients:</span></span>

* <span data-ttu-id="af535-589">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="af535-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="af535-590">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="af535-591">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="af535-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="af535-592">Zum Beispiel kann ein einzelner typisierter Client für einen einzelnen Back-End-Endpunkt verwendet werden, der jegliche Logik kapselt, die mit diesem Endpunkt interagiert.</span><span class="sxs-lookup"><span data-stu-id="af535-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="af535-593">Funktionieren mit DI und können überall in Ihrer App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="af535-594">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="af535-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="af535-595">Im vorangehenden Code wird die Konfiguration in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="af535-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="af535-596">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="af535-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="af535-597">Es ist möglich, API-spezifische Methoden zu definieren, die die `HttpClient`-Funktionalität zur Verfügung stellen.</span><span class="sxs-lookup"><span data-stu-id="af535-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="af535-598">Die Methode `GetAspNetDocsIssues` kapselt den Code, der für die Abfrage und Analyse des neuesten offenen Problems aus dem GitHub-Repository erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="af535-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="af535-599">Zum Registrieren eines typisierten Clients kann die generische Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse angeben:</span><span class="sxs-lookup"><span data-stu-id="af535-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="af535-600">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="af535-601">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="af535-602">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients, falls dies bevorzugt wird:</span><span class="sxs-lookup"><span data-stu-id="af535-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="af535-603">Es ist möglich, `HttpClient` vollständig in einem typisierten Client zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="af535-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="af535-604">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, können öffentliche Methoden bereitgestellt werden, die die `HttpClient`-Instanz intern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="af535-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="af535-605">Im vorangehenden Code wird `HttpClient` als ein privates Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="af535-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="af535-606">Alle Zugriffe, die externe Aufrufe durchführen, durchlaufen die Methode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="af535-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="af535-607">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="af535-607">Generated clients</span></span>

<span data-ttu-id="af535-608">`IHttpClientFactory` kann in Verbindung mit anderen Bibliotheken von Drittanbietern verwendet werden, z.B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="af535-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="af535-609">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="af535-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="af535-610">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="af535-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="af535-611">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="af535-612">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="af535-612">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="af535-613">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="af535-613">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="af535-614">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="af535-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="af535-615">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="af535-615">Outgoing request middleware</span></span>

<span data-ttu-id="af535-616">`HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="af535-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="af535-617">`IHttpClientFactory` erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="af535-618">Die Registrierung und Verkettung von mehreren Handlern wird unterstützt, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="af535-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="af535-619">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="af535-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="af535-620">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af535-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="af535-621">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="af535-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="af535-622">Definieren Sie zum Erstellen eines Handlers eine von <xref:System.Net.Http.DelegatingHandler> abgeleitete Klasse.</span><span class="sxs-lookup"><span data-stu-id="af535-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="af535-623">Überschreiben Sie die Methode `SendAsync`, um Code auszuführen, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="af535-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="af535-624">Der vorangehende Code definiert einen einfachen Handler.</span><span class="sxs-lookup"><span data-stu-id="af535-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="af535-625">Er überprüft, ob ein `X-API-KEY`-Header in die Anforderung eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="af535-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="af535-626">Wenn der Header fehlt, kann er den HTTP-Aufruf vermeiden und eine geeignete Antwort zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="af535-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="af535-627">Während der Registrierung kann mindestens ein Handler der Konfiguration eines `HttpClient` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="af535-628">Dieser Vorgang wird über die Erweiterungsmethoden von <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="af535-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="af535-629">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="af535-630">Der Handler **muss** in DI als vorübergehender Dienst registriert sein, niemals bereichsbezogen.</span><span class="sxs-lookup"><span data-stu-id="af535-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="af535-631">Wenn der Handler als bereichsbezogener Dienst registriert ist und alle Dienste, von denen der Handler abhängig ist, gelöscht werden können:</span><span class="sxs-lookup"><span data-stu-id="af535-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="af535-632">Die Dienste des Handlers können verworfen werden, bevor der Handler den Gültigkeitsbereich verlässt.</span><span class="sxs-lookup"><span data-stu-id="af535-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="af535-633">Der verworfenen Handlerdienst bewirken, dass der Handler fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="af535-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="af535-634">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, wobei der Typ für den Handler übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="af535-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="af535-635">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="af535-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="af535-636">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="af535-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="af535-637">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="af535-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="af535-638">Übergeben Sie Daten mit `HttpRequestMessage.Properties` an den Handler.</span><span class="sxs-lookup"><span data-stu-id="af535-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="af535-639">Greifen Sie mit `IHttpContextAccessor` auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="af535-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="af535-640">Erstellen Sie ein benutzerdefiniertes `AsyncLocal`-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="af535-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="af535-641">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="af535-641">Use Polly-based handlers</span></span>

<span data-ttu-id="af535-642">`IHttpClientFactory` integriert mit der beliebten Drittanbieter-Bibliothek namens [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="af535-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="af535-643">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="af535-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="af535-644">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="af535-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="af535-645">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="af535-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="af535-646">Die Polly-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="af535-646">The Polly extensions:</span></span>

* <span data-ttu-id="af535-647">Unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="af535-648">Können nach Installation des [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Pakets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="af535-649">Das Paket ist nicht im freigegebenen ASP.NET Core-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="af535-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="af535-650">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="af535-650">Handle transient faults</span></span>

<span data-ttu-id="af535-651">Am häufigsten treten Fehler auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="af535-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="af535-652">Eine praktische Erweiterungsmethode namens `AddTransientHttpErrorPolicy` ist enthalten. Sie ermöglicht das Definieren von Richtlinien zum Behandeln vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="af535-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="af535-653">Richtlinien, die mit dieser Erweiterungsmethode konfiguriert wurden, behandeln `HttpRequestException`, HTTP 5xx-Antworten und HTTP 408-Antworten.</span><span class="sxs-lookup"><span data-stu-id="af535-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="af535-654">Die `AddTransientHttpErrorPolicy`-Erweiterung kann in `Startup.ConfigureServices` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="af535-655">Die Erweiterung ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="af535-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="af535-656">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="af535-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="af535-657">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="af535-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="af535-658">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="af535-658">Dynamically select policies</span></span>

<span data-ttu-id="af535-659">Es gibt zusätzliche Erweiterungsmethoden, die zum Hinzufügen von Polly-basierten Handlern verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="af535-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="af535-660">Eine dieser Erweiterungen ist `AddPolicyHandler`, die über mehrere Überladungen verfügt.</span><span class="sxs-lookup"><span data-stu-id="af535-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="af535-661">Eine Überladung ermöglicht, dass die Anforderung beim Definieren der zu verwendenden Richtlinie überprüft werden kann:</span><span class="sxs-lookup"><span data-stu-id="af535-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="af535-662">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="af535-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="af535-663">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="af535-664">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="af535-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="af535-665">Es ist üblich, Polly-Richtlinien zu schachteln, um erweiterte Funktionen bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="af535-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="af535-666">Im vorangehenden Beispiel werden zwei Handler hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="af535-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="af535-667">Der Erste verwendet die `AddTransientHttpErrorPolicy`-Erweiterung, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="af535-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="af535-668">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="af535-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="af535-669">Der zweite Aufruf von `AddTransientHttpErrorPolicy` fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="af535-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="af535-670">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="af535-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="af535-671">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="af535-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="af535-672">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="af535-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="af535-673">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="af535-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="af535-674">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="af535-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="af535-675">Eine Erweiterungsmethode wird bereitgestellt, die einem Handler ermöglicht, mithilfe einer Richtlinie aus der Registrierung hinzugefügt zu werden:</span><span class="sxs-lookup"><span data-stu-id="af535-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="af535-676">Im oben stehenden Code werden zwei Richtlinien registriert, wenn `PolicyRegistry` zu `ServiceCollection` hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="af535-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="af535-677">Damit eine Richtlinie aus der Registrierung verwendet werden kann, wird die Methode `AddPolicyHandlerFromRegistry` verwendet. Dabei wird der Name der anzuwendenden Richtlinie übergeben.</span><span class="sxs-lookup"><span data-stu-id="af535-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="af535-678">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="af535-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="af535-679">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="af535-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="af535-680">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="af535-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="af535-681">Es gibt einen <xref:System.Net.Http.HttpMessageHandler> pro benanntem Client.</span><span class="sxs-lookup"><span data-stu-id="af535-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="af535-682">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="af535-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="af535-683">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="af535-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="af535-684">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="af535-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="af535-685">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="af535-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="af535-686">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="af535-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="af535-687">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="af535-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="af535-688">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="af535-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="af535-689">Der Standardwert kann für jeden benannten Client überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="af535-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="af535-690">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> auf dem bei der Erstellung des Clients zurückgegebenen `IHttpClientBuilder` auf, um den Wert zu überschreiben:</span><span class="sxs-lookup"><span data-stu-id="af535-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="af535-691">Das Verwerfen des Client ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="af535-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="af535-692">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="af535-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="af535-693">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="af535-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="af535-694">Die `HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die nicht verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="af535-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="af535-695">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="af535-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="af535-696">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="af535-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="af535-697">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="af535-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="af535-698">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="af535-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="af535-699">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="af535-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="af535-700">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="af535-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="af535-701">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="af535-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="af535-702">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="af535-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="af535-703">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="af535-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="af535-704">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="af535-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="af535-705">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="af535-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="af535-706">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="af535-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="af535-707">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="af535-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="af535-708">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="af535-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="af535-709">Cookie</span><span class="sxs-lookup"><span data-stu-id="af535-709">Cookies</span></span>

<span data-ttu-id="af535-710">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="af535-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="af535-711">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="af535-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="af535-712">Ziehen Sie für Apps, die cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="af535-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="af535-713">Deaktivieren der automatischen cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="af535-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="af535-714">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="af535-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="af535-715">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="af535-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="af535-716">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="af535-716">Logging</span></span>

<span data-ttu-id="af535-717">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="af535-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="af535-718">Aktivieren Sie die entsprechende Informationsebene in Ihrer Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="af535-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="af535-719">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="af535-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="af535-720">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="af535-721">Ein Client namens *MyNamedClient* protokolliert beispielsweise Meldungen mit der Kategorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="af535-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="af535-722">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="af535-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="af535-723">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="af535-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="af535-724">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="af535-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="af535-725">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="af535-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="af535-726">Im Beispiel *MyNamedClient* werden diese Meldungen für die Protokollkategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` protokolliert.</span><span class="sxs-lookup"><span data-stu-id="af535-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="af535-727">Dies findet für die Anforderung statt, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung an das Netzwerk gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="af535-728">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="af535-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="af535-729">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="af535-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="af535-730">Dies kann beispielsweise die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="af535-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="af535-731">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert bei Bedarf die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="af535-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="af535-732">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="af535-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="af535-733">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="af535-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="af535-734">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="af535-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="af535-735">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af535-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="af535-736">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="af535-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="af535-737">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="af535-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="af535-738">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="af535-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="af535-739">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="af535-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="af535-740">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="af535-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="af535-741">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="af535-741">In the following example:</span></span>

* <span data-ttu-id="af535-742"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="af535-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="af535-743">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="af535-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="af535-744">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="af535-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="af535-745">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="af535-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="af535-746">Middleware für Headerweitergabe</span><span class="sxs-lookup"><span data-stu-id="af535-746">Header propagation middleware</span></span>

<span data-ttu-id="af535-747">Für die Headerweitergabe wird eine von der Community unterstützte Middleware verwendet, um HTTP-Header von eingehenden Anforderungen an ausgehende HTTP-Clientanforderungen weiterzugeben.</span><span class="sxs-lookup"><span data-stu-id="af535-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="af535-748">So verwenden Sie die Headerweitergabe:</span><span class="sxs-lookup"><span data-stu-id="af535-748">To use header propagation:</span></span>

* <span data-ttu-id="af535-749">Erstellen Sie einen Verweis auf das von der Community unterstützte [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)-Paket.</span><span class="sxs-lookup"><span data-stu-id="af535-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="af535-750">ASp.NET Core 3.1 und höher unterstützen [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="af535-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="af535-751">Konfigurieren Sie in `Startup` die Middleware und `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="af535-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="af535-752">Der Client schließt die konfigurierten Header für ausgehende Anforderungen ein:</span><span class="sxs-lookup"><span data-stu-id="af535-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="af535-753">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="af535-753">Additional resources</span></span>

* [<span data-ttu-id="af535-754">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="af535-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="af535-755">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="af535-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="af535-756">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="af535-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

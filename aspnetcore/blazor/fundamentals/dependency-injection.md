---
title: Abhängigkeitsinjektion in ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Blazor-Apps Dienste in Komponenten einfügen können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: c8209b9374b448562c173f9f879e75a5da5f2301
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014411"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="9f8bd-103">Abhängigkeitsinjektion in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9f8bd-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="9f8bd-104">Von [Rainer Stropek](https://www.timecockpit.com) und [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="9f8bd-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="9f8bd-105">Blazor unterstützt die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI).</span><span class="sxs-lookup"><span data-stu-id="9f8bd-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9f8bd-106">Apps können integrierte Dienste verwenden, indem Sie sie in Komponenten einfügen.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="9f8bd-107">Apps können auch benutzerdefinierte Dienste definieren und registrieren und sie über die Abhängigkeitsinjektion in der gesamten App verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="9f8bd-108">Die Abhängigkeitsinjektion ist ein Verfahren für den Zugriff auf Dienste, die an einer zentralen Stelle konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="9f8bd-109">Dies kann in Blazor-Apps für Folgendes nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="9f8bd-110">Freigabe einer einzelnen Instanz einer Dienstklasse über viele Komponenten hinweg, bekannt als *Singleton*-Dienst.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="9f8bd-111">Entkoppeln von Komponenten von konkreten Dienstklassen durch Verwendung von Referenzabstraktionen.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="9f8bd-112">Betrachten Sie zum Beispiel eine `IDataAccess`-Schnittstelle für den Zugriff auf Daten in der App.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="9f8bd-113">Die Schnittstelle wird durch eine konkrete `DataAccess`-Klasse implementiert und als Dienst im Dienstcontainer der App registriert.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="9f8bd-114">Wenn eine Komponente die Abhängigkeitsinjektion verwendet, um eine `IDataAccess`-Implementierung zu erhalten, ist die Komponente nicht an den konkreten Typ gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="9f8bd-115">Die Implementierung kann ausgetauscht werden, z. B. gegen eine Pseudoimplementierung in Komponententests.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="9f8bd-116">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="9f8bd-116">Default services</span></span>

<span data-ttu-id="9f8bd-117">Standarddienste werden automatisch zur Dienstsammlung der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="9f8bd-118">Dienst</span><span class="sxs-lookup"><span data-stu-id="9f8bd-118">Service</span></span> | <span data-ttu-id="9f8bd-119">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="9f8bd-119">Lifetime</span></span> | <span data-ttu-id="9f8bd-120">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9f8bd-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="9f8bd-121">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="9f8bd-121">Scoped</span></span> | <span data-ttu-id="9f8bd-122">Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource bereit, die von einem URI identifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="9f8bd-123">Die Instanz von <xref:System.Net.Http.HttpClient> in einer Blazor WebAssembly-App verwendet den Browser für die Behandlung des HTTP-Datenverkehrs im Hintergrund.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="9f8bd-124">Blazor Server-Apps enthalten standardmäßig keinen <xref:System.Net.Http.HttpClient>, der als Dienst konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="9f8bd-125">Stellen Sie einen <xref:System.Net.Http.HttpClient> für eine Blazor Server-App bereit.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="9f8bd-126">Weitere Informationen finden Sie unter <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="9f8bd-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="9f8bd-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="9f8bd-128">Bereichsbezogen (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="9f8bd-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="9f8bd-129">Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe verteilt werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="9f8bd-130">Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="9f8bd-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="9f8bd-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="9f8bd-132">Bereichsbezogen (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="9f8bd-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="9f8bd-133">Enthält Hilfsprogramme für die Arbeit mit URIs und dem Navigationszustand.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="9f8bd-134">Weitere Informationen finden Sie unter [Hilfsprogramme für URI und Navigationszustand](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="9f8bd-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="9f8bd-135">Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="9f8bd-136">Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="9f8bd-137">Hinzufügen von Diensten zu einer App</span><span class="sxs-lookup"><span data-stu-id="9f8bd-137">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="9f8bd-138">Konfigurieren Sie Dienste für die Dienstsammlung der App in der `Main`-Methode von `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-138">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="9f8bd-139">Im folgenden Beispiel ist die `MyDependency`-Implementierung für `IMyDependency` registriert:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-139">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="9f8bd-140">Nachdem der Host erstellt wurde, kann auf die Dienste vom Stammbereich der Abhängigkeitsinjektion aus zugegriffen werden, bevor irgendwelche Komponenten bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-140">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="9f8bd-141">Dies kann für die Ausführung der Initialisierungslogik vor dem Rendern von Inhalten nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-141">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="9f8bd-142">Der Host stellt auch eine zentrale Konfigurationsinstanz für die App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-142">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="9f8bd-143">Ausgehend vom vorhergehenden Beispiel wird die URL des Wetterdiensts von einer Standardkonfigurationsquelle (z. B. `appsettings.json`) an `InitializeWeatherAsync` übergeben:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-143">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="9f8bd-144">Nachdem Sie eine neue App erstellt haben, untersuchen Sie die `Startup.ConfigureServices`-Methode:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-144">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="9f8bd-145">Der Methode <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> übergeben, das eine Liste von Dienstdeskriptorobjekten (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) ist.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-145">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="9f8bd-146">Dienste werden in der `ConfigureServices`-Methode durch Bereitstellung von Dienstdeskriptoren in der Dienstsammlung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-146">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="9f8bd-147">Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess`-Schnittstelle und seiner konkreten Implementierung `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-147">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="9f8bd-148">Lebensdauer von Diensten</span><span class="sxs-lookup"><span data-stu-id="9f8bd-148">Service lifetime</span></span>

<span data-ttu-id="9f8bd-149">Die Dienste können mit den in der folgenden Tabelle angegebenen Lebensdauern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-149">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="9f8bd-150">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="9f8bd-150">Lifetime</span></span> | <span data-ttu-id="9f8bd-151">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9f8bd-151">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="9f8bd-152">Blazor WebAssembly-Apps verfügen derzeit nicht über ein Konzept für Bereiche von Abhängigkeitsinjektionen.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-152">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="9f8bd-153">`Scoped`-registrierte Dienste verhalten sich wie `Singleton`-Dienste.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-153">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="9f8bd-154">Das Blazor Server-Hostingmodell unterstützt jedoch die Lebensdauer `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-154">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="9f8bd-155">In Blazor Server-Apps wird die Registrierung eines bereichsbezogenen Diensts der *Verbindung* zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-155">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="9f8bd-156">Aus diesem Grund wird die Verwendung von bereichsbezogenen Diensten für Dienste bevorzugt, die dem aktuellen Benutzer zugeordnet werden sollen, auch wenn die aktuelle Absicht darin besteht, clientseitig im Browser ausgeführt zu werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-156">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="9f8bd-157">Die Abhängigkeitsinjektion erstellt eine *Einzelinstanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-157">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="9f8bd-158">Alle Komponenten, die einen `Singleton`-Dienst erfordern, erhalten eine Instanz desselben Diensts.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-158">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="9f8bd-159">Immer wenn eine Komponente eine Instanz eines `Transient`-Diensts aus dem Dienstcontainer erhält, erhält sie eine *neue Instanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-159">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="9f8bd-160">Das Abhängigkeitsinjektionssystem basiert auf dem Abhängigkeitsinjektionssystem in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-160">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="9f8bd-161">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-161">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="9f8bd-162">Anfordern eines Diensts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="9f8bd-162">Request a service in a component</span></span>

<span data-ttu-id="9f8bd-163">Nachdem die Dienste der Dienstsammlung hinzugefügt wurden, fügen Sie die Dienste mit der Razor-Anweisung [\@inject](xref:mvc/views/razor#inject) in die Komponenten ein.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-163">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="9f8bd-164">[`@inject`](xref:mvc/views/razor#inject) besitzt zwei Parameter:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-164">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="9f8bd-165">Typ: Der Typ des einzufügenden Diensts.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="9f8bd-166">Eigenschaft: Der Name der Eigenschaft, die den eingefügten App-Dienst erhält.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="9f8bd-167">Die Eigenschaft erfordert keine manuelle Erstellung.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="9f8bd-168">Der Compiler erstellt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-168">The compiler creates the property.</span></span>

<span data-ttu-id="9f8bd-169">Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="9f8bd-170">Verwenden Sie mehrere [`@inject`](xref:mvc/views/razor#inject)-Anweisungen, um verschiedene Dienste einzufügen.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="9f8bd-171">Das folgende Beispiel veranschaulicht die Verwendung von [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="9f8bd-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="9f8bd-172">Der Dienst, der `Services.IDataAccess` implementiert, wird in die Eigenschaft `DataRepository` der Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="9f8bd-173">Beachten Sie, dass der Code nur die Abstraktion `IDataAccess` verwendet:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="9f8bd-174">Intern verwendet die generierte Eigenschaft (`DataRepository`) das Attribut [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="9f8bd-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="9f8bd-175">In der Regel wird dieses Attribut nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="9f8bd-176">Wenn eine Basisklasse für Komponenten erforderlich ist und die eingefügten Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut manuell hinzu:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="9f8bd-177">In Komponenten, die von der Basisklasse abgeleitet sind, ist die [`@inject`](xref:mvc/views/razor#inject)-Direktive nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="9f8bd-178">Das <xref:Microsoft.AspNetCore.Components.InjectAttribute> der Basisklasse ist ausreichend:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="9f8bd-179">Verwenden der Abhängigkeitsinjektion in Diensten</span><span class="sxs-lookup"><span data-stu-id="9f8bd-179">Use DI in services</span></span>

<span data-ttu-id="9f8bd-180">Komplexe Dienste können zusätzliche Dienste erfordern.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-180">Complex services might require additional services.</span></span> <span data-ttu-id="9f8bd-181">Im vorherigen Beispiel könnte `DataAccess` den Standarddienst <xref:System.Net.Http.HttpClient> erfordern.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-181">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="9f8bd-182">[`@inject`](xref:mvc/views/razor#inject) (oder das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut) ist nicht für die Verwendung in Diensten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="9f8bd-183">Stattdessen muss die *Constructor Injection* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="9f8bd-184">Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="9f8bd-185">Wenn die Abhängigkeitsinjektion den Dienst erstellt, erkennt sie die erforderlichen Dienste im Konstruktor und stellt sie entsprechend zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="9f8bd-186">Im folgenden Beispiel empfängt der Konstruktor einen <xref:System.Net.Http.HttpClient> über die Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="9f8bd-187"><xref:System.Net.Http.HttpClient> ist ein Standarddienst.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="9f8bd-188">Voraussetzungen für die Constructor Injection:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="9f8bd-189">Es muss einen Konstruktor geben, dessen Argumente alle von der Abhängigkeitsinjektion erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="9f8bd-190">Zusätzliche, nicht durch die Abhängigkeitsinjektion abgedeckte Parameter sind zulässig, wenn sie Standardwerte angeben.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="9f8bd-191">Der anwendbare Konstruktor muss `public` sein.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="9f8bd-192">Es muss ein anwendbarer Konstruktor vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-192">One applicable constructor must exist.</span></span> <span data-ttu-id="9f8bd-193">Im Falle einer Mehrdeutigkeit löst die Abhängigkeitsinjektion eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="9f8bd-194">Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="9f8bd-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="9f8bd-195">In ASP.NET Core-Apps werden bereichsbezogene Dienste in der Regel der aktuellen Anforderung zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="9f8bd-196">Nachdem die Anforderung abgeschlossen ist, werden alle bereichsbezogenen oder vorübergehenden Dienste vom Abhängigkeitsinjektionssystem entsorgt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="9f8bd-197">In Blazor Server-Apps besteht der Anforderungsbereich für die Dauer der Clientverbindung, was dazu führen kann, dass vorübergehende und bereichsbezogene Dienste viel länger als erwartet leben.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="9f8bd-198">In Blazor WebAssembly-Apps werden Dienste, die mit einer bereichsbezogenen Lebensdauer registriert sind, als Singletons behandelt, sodass sie länger leben als bereichsbezogene Dienste in typischen ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="9f8bd-199">Informationen zum Erkennen verwerfbarer vorübergehender Dienste in einer App finden Sie im Abschnitt [Erkennen vorübergehender verwerfbarer Objekte](#detect-transient-disposables).</span><span class="sxs-lookup"><span data-stu-id="9f8bd-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="9f8bd-200">Ein Ansatz, der die Lebensdauer eines Diensts in Blazor-Apps begrenzt, ist die Verwendung des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="9f8bd-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstrakter, von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleiteter Typ, der einen der Lebensdauer der Komponente entsprechenden Bereich für die Abhängigkeitsinjektion erstellt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="9f8bd-202">Mit diesem Bereich ist es möglich, Abhängigkeitsinjektionsdienste mit einer bereichsbezogenen Lebensdauer zu nutzen und sie so lange wie die Komponente zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="9f8bd-203">Wenn die Komponente zerstört wird, werden auch die Dienste des bereichsbezogenen Dienstanbieters der Komponente entsorgt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="9f8bd-204">Dies kann für Dienste nützlich sein, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-204">This can be useful for services that:</span></span>

* <span data-ttu-id="9f8bd-205">Sie sollten innerhalb einer Komponente wiederverwendet werden, da die vorübergehende Lebensdauer unangemessen ist.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="9f8bd-206">Sie sollten nicht komponentenübergreifend freigegeben werden, da die Singleton-Lebensdauer unangemessen ist.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="9f8bd-207">Zwei Versionen des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase> sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="9f8bd-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstraktes, verwerfbares, untergeordnetes Element vom Typ <xref:Microsoft.AspNetCore.Components.ComponentBase> mit einer geschützten <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Eigenschaft vom Typ <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="9f8bd-209">Dieser Anbieter kann zur Auflösung von Diensten verwendet werden, die der Lebensdauer der Komponente zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="9f8bd-210">Abhängigkeitsinjektionsdienste, die mit [`@inject`](xref:mvc/views/razor#inject) oder dem [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut in die Komponente eingefügt werden, werden nicht im Gültigkeitsbereich der Komponente erstellt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="9f8bd-211">Um den Bereich der Komponente zu verwenden, müssen die Dienste mit <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> oder <xref:System.IServiceProvider.GetService%2A> aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="9f8bd-212">Allen Diensten, die unter Verwendung des <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Anbieters aufgelöst werden, werden ihre Abhängigkeiten aus demselben Bereich bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="9f8bd-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> ist abgeleitet von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> und fügt eine Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> hinzu, die eine Instanz von `T` vom bereichsbezogenen Abhängigkeitsinjektionsanbieter zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="9f8bd-214">Dieser Typ ist eine komfortable Methode für den Zugriff auf bereichsbezogene Dienste, ohne eine Instanz von <xref:System.IServiceProvider> zu verwenden, wenn es einen primären Dienst gibt, den die Anwendung aus dem Abhängigkeitsinjektionscontainer unter Verwendung des Bereichs der Komponente benötigt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="9f8bd-215">Die Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> ist verfügbar, sodass die App bei Bedarf auch Dienste mit anderen Typen erhalten kann.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="9f8bd-216">Verwenden von Entity Framework-DbContext von der Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="9f8bd-216">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="9f8bd-217">Ein allgemeiner Diensttyp zum Abrufen der Abhängigkeitsinjektion in Web-Apps sind EF-<xref:Microsoft.EntityFrameworkCore.DbContext>-Objekte (Entity Framework).</span><span class="sxs-lookup"><span data-stu-id="9f8bd-217">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="9f8bd-218">Beim Registrieren von EF-Diensten mithilfe von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> wird <xref:Microsoft.EntityFrameworkCore.DbContext> standardmäßig als bereichsbezogener Dienst hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-218">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="9f8bd-219">Die Registrierung als bereichsbezogener Dienst kann zu Problemen in Blazor-Apps führen, da sie dazu führt, dass <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanzen langlebig sind und in der gesamten App gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-219">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="9f8bd-220"><xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und darf nicht gleichzeitig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-220"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="9f8bd-221">Je nach App kann die Verwendung von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> zur Begrenzung des Bereichs von <xref:Microsoft.EntityFrameworkCore.DbContext> auf eine einzelne Komponente das Problem *möglicherweise* lösen.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-221">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="9f8bd-222">Wenn eine Komponente nicht parallel ein <xref:Microsoft.EntityFrameworkCore.DbContext> verwendet, reicht es aus, die Komponente aus <xref:Microsoft.AspNetCore.Components.OwningComponentBase> abzuleiten und den <xref:Microsoft.EntityFrameworkCore.DbContext> aus <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> abzurufen, da Folgendes sichergestellt wird:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-222">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="9f8bd-223">Separate Komponenten teilen sich kein <xref:Microsoft.EntityFrameworkCore.DbContext>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-223">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="9f8bd-224">Der <xref:Microsoft.EntityFrameworkCore.DbContext> lebt nur so lange, wie die von ihm abhängige Komponente.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-224">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="9f8bd-225">Wenn eine einzelne Komponente gleichzeitig einen <xref:Microsoft.EntityFrameworkCore.DbContext> verwenden könnte (z. B. jedes Mal, wenn ein Benutzer eine Schaltfläche auswählt), vermeidet selbst die Verwendung von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> die Probleme mit gleichzeitigen EF-Vorgängen nicht.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-225">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="9f8bd-226">Verwenden Sie in diesem Fall für jeden logischen EF-Vorgang einen anderen <xref:Microsoft.EntityFrameworkCore.DbContext>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-226">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="9f8bd-227">Verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-227">Use either of the following approaches:</span></span>

* <span data-ttu-id="9f8bd-228">Erstellen Sie den <xref:Microsoft.EntityFrameworkCore.DbContext> direkt mit <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> als Argument, das von der Abhängigkeitsinjektion abgerufen werden kann und threadsicher ist.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-228">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="9f8bd-229">Registrieren Sie den <xref:Microsoft.EntityFrameworkCore.DbContext> im Dienstcontainer mit einer vorübergehenden Lebensdauer:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-229">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="9f8bd-230">Verwenden Sie bei der Registrierung des Kontexts <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-230">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9f8bd-231">Die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterungsmethode nimmt zwei optionale Parameter vom Typ <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> an.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-231">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="9f8bd-232">Um diesen Ansatz zu verwenden, muss nur der Parameter `contextLifetime` entsprechend <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> sein.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-232">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9f8bd-233">`optionsLifetime` kann seinen Standardwert von <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> beibehalten.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-233">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="9f8bd-234">Der vorübergehende <xref:Microsoft.EntityFrameworkCore.DbContext> kann wie üblich (mit [`@inject`](xref:mvc/views/razor#inject)) in Komponenten eingefügt werden, die nicht mehrere EF-Vorgänge parallel ausführen werden.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-234">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="9f8bd-235">Diejenigen, die mehrere EF-Vorgänge gleichzeitig durchführen können, können mit <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> separate <xref:Microsoft.EntityFrameworkCore.DbContext>-Objekte für jeden parallelen Vorgang anfordern.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-235">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="detect-transient-disposables"></a><span data-ttu-id="9f8bd-236">Erkennen vorübergehender verwerfbarer Dienste</span><span class="sxs-lookup"><span data-stu-id="9f8bd-236">Detect transient disposables</span></span>

<span data-ttu-id="9f8bd-237">In den folgenden Beispielen wird veranschaulicht, wie verwerfbare vorübergehende Dienste in einer App erkannt werden, die <xref:Microsoft.AspNetCore.Components.OwningComponentBase> verwenden sollte.</span><span class="sxs-lookup"><span data-stu-id="9f8bd-237">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="9f8bd-238">Weitere Informationen finden Sie im Abschnitt [Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion](#utility-base-component-classes-to-manage-a-di-scope).</span><span class="sxs-lookup"><span data-stu-id="9f8bd-238">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="9f8bd-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="9f8bd-240">Im folgenden Beispiel wird `TransientDisposable` erkannt (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="9f8bd-240">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

<span data-ttu-id="9f8bd-241">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-241">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="9f8bd-242">`Program`:</span><span class="sxs-lookup"><span data-stu-id="9f8bd-242">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="9f8bd-243">Im folgenden Beispiel wird `TransientDependency` erkannt (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="9f8bd-243">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="9f8bd-244">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9f8bd-244">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="9f8bd-245">`IDisposable`-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="9f8bd-245">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>

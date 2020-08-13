---
title: Aufrufen von gRPC-Diensten mithilfe eines .NET-Clients
author: jamesnk
description: Hier erfahren Sie, wie Sie gRPC-Dienste mit dem .NET-gRPC-Client aufrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 5aca81da34e5ed51b2dc4f404c1ba4d7377a422f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016244"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="8e5f4-103">Aufrufen von gRPC-Diensten mithilfe eines .NET-Clients</span><span class="sxs-lookup"><span data-stu-id="8e5f4-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="8e5f4-104">Im NuGet-Paket [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) ist eine .NET-gRPC-Clientbibliothek verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="8e5f4-105">Im vorliegenden Dokument wird Folgendes erläutert:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-105">This document explains how to:</span></span>

* <span data-ttu-id="8e5f4-106">Konfigurieren eines gRPC-Clients, um gRPC-Dienste aufzurufen</span><span class="sxs-lookup"><span data-stu-id="8e5f4-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="8e5f4-107">Durchführen von gRPC-Aufrufe von unären, Serverstreaming- und Clientstreamingmethoden sowie von Methoden für bidirektionales Streaming.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="8e5f4-108">Konfigurieren eines gRPC-Clients</span><span class="sxs-lookup"><span data-stu-id="8e5f4-108">Configure gRPC client</span></span>

<span data-ttu-id="8e5f4-109">gRPC-Clients sind konkrete Clienttypen, [die aus *\*.proto*Dateien generiert werden](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="8e5f4-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="8e5f4-110">Der konkrete gRPC-Client verfügt über Methoden, die in den gRPC-Dienst in der *\*.proto*-Datei übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="8e5f4-111">Ein gRPC-Client wird aus einem Kanal erstellt.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="8e5f4-112">Starten Sie das Erstellen eines Kanals mithilfe von `GrpcChannel.ForAddress`, und verwenden Sie dann den Kanal, um einen gRPC-Client zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="8e5f4-113">Ein Kanal steht für eine langlebige Verbindung zu einem gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="8e5f4-114">Wenn ein Kanal erstellt wird, wird er mit Optionen zum Aufrufen eines Diensts konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="8e5f4-115">Die für Aufrufe verwendete Klasse `HttpClient`, die maximale Größe für gesendete und empfange Nachrichten sowie die Protokollierung können in der Klasse `GrpcChannelOptions` angegeben und mit `GrpcChannel.ForAddress` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="8e5f4-116">Eine vollständige Liste der Optionen finden Sie unter [Konfigurieren von Clientoptionen](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="8e5f4-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="8e5f4-117">Konfigurieren von TLS</span><span class="sxs-lookup"><span data-stu-id="8e5f4-117">Configure TLS</span></span>

<span data-ttu-id="8e5f4-118">Die Sicherheit auf Verbindungsebene von gRPC-Client und dem aufgerufenen Dienst muss übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="8e5f4-119">Beim Erstellen des gRPC-Kanals wird für den gRPC-Client die Verwendung von Transport Layer Security (TLS) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="8e5f4-120">Ein gRPC-Client löst einen Fehler aus, wenn beim Aufrufen eines Diensts die Sicherheit auf Verbindungsebene von Kanal und nicht Dienst nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="8e5f4-121">Stellen Sie beim Konfigurieren eines gRPC-Kanals für die Verwendung von TLS sicher, dass die Serveradresse mit `https` beginnt.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="8e5f4-122">`GrpcChannel.ForAddress("https://localhost:5001")` verwendet beispielsweise das HTTPS-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="8e5f4-123">Der gRPC-Kanal handelt automatisch eine mit TLS gesicherte Verbindung aus und verwendet für gRPC-Aufrufe eine sichere Verbindung.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="8e5f4-124">gRPC unterstützt die Authentifizierung mit Clientzertifikaten über TLS.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="8e5f4-125">Informationen zum Konfigurieren von Clientzertifikaten mit einem gRPC-Kanal finden Sie unter <xref:grpc/authn-and-authz#client-certificate-authentication>.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="8e5f4-126">Stellen Sie zum Aufrufen von unsicheren gRPC-Diensten sicher, dass die Serveradresse mit `http` beginnt.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="8e5f4-127">`GrpcChannel.ForAddress("http://localhost:5000")` verwendet beispielsweise das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="8e5f4-128">Wenn Sie [unsichere gRPC-Dienste mit dem .NET-Client aufrufen](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client) möchten, sind ab .NET Core 3.1 weitere Konfigurationen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="8e5f4-129">Clientleistung</span><span class="sxs-lookup"><span data-stu-id="8e5f4-129">Client performance</span></span>

<span data-ttu-id="8e5f4-130">Kanal- und Clientleistung und -nutzung:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="8e5f4-131">Das Erstellen eines Kanals kann ein kostspieliger Vorgang sein.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="8e5f4-132">Wenn ein Kanal für gRPC-Aufrufe wiederverwendet wird, stehen Leistungsvorteile zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="8e5f4-133">gRPC-Clients werden mit Kanälen erstellt.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="8e5f4-134">Bei gRPC-Clients handelt es sich um Lightweightobjekte. Sie müssen nicht zwischengespeichert oder wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="8e5f4-135">Aus einem Kanal können mehrere gRPC-Clients erstellt werden, einschließlich verschiedener Clienttypen.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="8e5f4-136">Ein Kanal und Clients, die aus dem Kanal erstellt wurden, können sicher von mehreren Threads verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="8e5f4-137">Clients, die aus dem Kanal erstellt wurden, können mehrere gleichzeitige Aufrufe durchführen.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="8e5f4-138">`GrpcChannel.ForAddress` ist nicht die einzige Option für das Erstellen eines gRPC-Clients.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="8e5f4-139">Wenn Sie gRPC-Dienste in einer ASP.NET Core-App aufrufen, sollten Sie die [Integration der gRPC-Clientfactory](xref:grpc/clientfactory) in Erwägung ziehen.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="8e5f4-140">Die gRPC-Integration mit `HttpClientFactory` bietet eine zentralisierte Alternative zur Erstellung von gRPC-Clients.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="8e5f4-141">Das Aufrufen von gRPC über HTTP/2 mit `Grpc.Net.Client` wird in Xamarin aktuell nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="8e5f4-142">Die HTTP/2-Unterstützung soll jedoch in einem zukünftigen Release von Xamarin optimiert werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="8e5f4-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) und [gRPC-Web](xref:grpc/browser) sind funktionsfähige Alternativen, die aktuell verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="8e5f4-144">Durchführen von gRPC-Aufrufen</span><span class="sxs-lookup"><span data-stu-id="8e5f4-144">Make gRPC calls</span></span>

<span data-ttu-id="8e5f4-145">Ein gRPC-Aufruf wird initiiert, indem eine Methode auf dem Client aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="8e5f4-146">Der gRPC-Client verarbeitet die Nachrichtenserialisierung und leitet den gRPC-Aufruf an den entsprechenden Dienst weiter.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="8e5f4-147">Bei gRPC stehen verschiedene Methodentypen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-147">gRPC has different types of methods.</span></span> <span data-ttu-id="8e5f4-148">Wie der Client verwendet wird, um einen gRPC-Aufruf durchzuführen, hängt vom Typ der aufgerufenen Methode ab.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="8e5f4-149">Es gibt die folgenden gRPC-Methodentypen:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-149">The gRPC method types are:</span></span>

* <span data-ttu-id="8e5f4-150">Unär</span><span class="sxs-lookup"><span data-stu-id="8e5f4-150">Unary</span></span>
* <span data-ttu-id="8e5f4-151">Serverstreaming</span><span class="sxs-lookup"><span data-stu-id="8e5f4-151">Server streaming</span></span>
* <span data-ttu-id="8e5f4-152">Clientstreaming</span><span class="sxs-lookup"><span data-stu-id="8e5f4-152">Client streaming</span></span>
* <span data-ttu-id="8e5f4-153">Bidirektionales Streaming</span><span class="sxs-lookup"><span data-stu-id="8e5f4-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="8e5f4-154">Unärer Aufruf</span><span class="sxs-lookup"><span data-stu-id="8e5f4-154">Unary call</span></span>

<span data-ttu-id="8e5f4-155">Ein unärer Aufruf beginnt damit, dass der Client eine Anforderungsnachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="8e5f4-156">Sobald der Dienst abgeschlossen ist, wird eine Antwortnachricht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="8e5f4-157">Alle unären Dienstmethoden in der Datei *\*.proto* führen zu zwei .NET-Methoden auf dem konkreten gRPC-Clienttyp, um die Methode aufzurufen: eine asynchrone Methode und eine blockierende Methode.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="8e5f4-158">Bei `GreeterClient` gibt es beispielsweise zwei Möglichkeiten, `SayHello` aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="8e5f4-159">`GreeterClient.SayHelloAsync` ruft den `Greeter.SayHello`-Dienst asynchron auf und</span><span class="sxs-lookup"><span data-stu-id="8e5f4-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="8e5f4-160">kann erwartet werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-160">Can be awaited.</span></span>
* <span data-ttu-id="8e5f4-161">`GreeterClient.SayHello` ruft den `Greeter.SayHello`-Dienst auf und blockiert, bis der Vorgang abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="8e5f4-162">Verwenden Sie diese Möglichkeit nicht für asynchronen Code.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="8e5f4-163">Serverstreamingaufruf</span><span class="sxs-lookup"><span data-stu-id="8e5f4-163">Server streaming call</span></span>

<span data-ttu-id="8e5f4-164">Ein Serverstreamingaufruf beginnt damit, dass der Client eine Anforderungsnachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="8e5f4-165">`ResponseStream.MoveNext()` liest Nachrichten, die vom Dienst gestreamt werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="8e5f4-166">Der Serverstreamingaufruf ist abgeschlossen, wenn `ResponseStream.MoveNext()` `false` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="8e5f4-167">Wenn Sie C# 8 oder höher verwenden, kann die `await foreach`-Syntax verwendet werden, um Nachrichten zu lesen.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="8e5f4-168">Die `IAsyncStreamReader<T>.ReadAllAsync()`-Erweiterungsmethode liest alle Nachrichten aus dem Antwortstream:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="8e5f4-169">Clientstreamingaufruf</span><span class="sxs-lookup"><span data-stu-id="8e5f4-169">Client streaming call</span></span>

<span data-ttu-id="8e5f4-170">Ein Clientstreamingaufruf beginnt, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="8e5f4-171">Der Client kann Nachrichten mit `RequestStream.WriteAsync` senden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="8e5f4-172">Wenn der Client das Senden von Nachrichten abgeschlossen hat, sollte `RequestStream.CompleteAsync` aufgerufen werden, damit der Dienst benachrichtigt wird.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-172">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="8e5f4-173">Der Aufruf ist abgeschlossen, wenn der Dienst eine Antwortnachricht zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-173">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="8e5f4-174">Aufruf von bidirektionalem Streaming</span><span class="sxs-lookup"><span data-stu-id="8e5f4-174">Bi-directional streaming call</span></span>

<span data-ttu-id="8e5f4-175">Ein Aufruf für bidirektionales Streaming beginnt, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="8e5f4-176">Der Client kann Nachrichten mit `RequestStream.WriteAsync` senden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="8e5f4-177">Auf vom Dienst gestreamte Nachrichten kann mit `ResponseStream.MoveNext()` oder `ResponseStream.ReadAllAsync()` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="8e5f4-178">Der Aufruf für bidirektionales Streaming ist abgeschlossen, wenn `ResponseStream` keine weiteren Nachrichten mehr hat.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="8e5f4-179">Während des Aufrufs von bidirektionalem Streaming können sich Client und Dienst jederzeit gegenseitig Nachrichten senden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-179">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="8e5f4-180">Die beste Clientlogik für die Interaktion mit einem bidirektionalem Aufruf variiert je nach Dienstlogik.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-180">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="8e5f4-181">Zugreifen auf gRPC-Nachspanne</span><span class="sxs-lookup"><span data-stu-id="8e5f4-181">Access gRPC trailers</span></span>

<span data-ttu-id="8e5f4-182">gRPC-Aufrufe können gRPC-Nachspanne zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-182">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="8e5f4-183">gRPC-Nachspanne werden verwendet, um Name/Wert-Metadaten zu einem Aufruf bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-183">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="8e5f4-184">Nachspanne stellen eine ähnliche Funktionalität wie HTTP-Header bereit, werden aber am Ende des Aufrufs empfangen.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-184">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="8e5f4-185">Auf gRPC-Nachspanne kann über `GetTrailers()` zugegriffen werden, die eine Sammlung von Metadaten zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-185">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="8e5f4-186">Nachspanne werden nach Abschluss der Antwort zurückgegeben, weshalb Sie erst alle Antwortnachrichten abwarten müssen, bevor Sie auf die Nachspanne zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-186">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="8e5f4-187">Unäre und Clientstreamingaufrufe müssen auf `ResponseAsync` warten, bevor sie `GetTrailers()` aufrufen können:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-187">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="8e5f4-188">Server- und bidirektionale Streamingaufrufe müssen zuerst vollständig den Antwortdatenstrom abwarten, bevor sie `GetTrailers()` aufrufen können:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-188">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="8e5f4-189">Auf gRPC-Nachspanne kann auch über `RpcException` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-189">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="8e5f4-190">Ein Dienst kann Nachspanne eventuell zusammen mit einem gRPC-Status von „nicht in Ordnung“ zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="8e5f4-190">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="8e5f4-191">In dieser Situation werden die Nachspanne vom gRPC-Client aus der ausgelösten Ausnahme abgerufen:</span><span class="sxs-lookup"><span data-stu-id="8e5f4-191">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="8e5f4-192">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8e5f4-192">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>

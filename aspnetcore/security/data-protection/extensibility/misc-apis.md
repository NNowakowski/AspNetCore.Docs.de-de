---
title: Sonstige ASP.net Core Datenschutz-APIs
author: rick-anderson
description: Erfahren Sie mehr über die isecret-Schnittstelle für ASP.net Core Datenschutz.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 947c6eb62550d325492365ece84d45a14845888f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630912"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="e18a9-103">Sonstige ASP.net Core Datenschutz-APIs</span><span class="sxs-lookup"><span data-stu-id="e18a9-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="e18a9-104">Typen, die eine der folgenden Schnittstellen implementieren, sollten für mehrere Aufrufer Thread sicher sein.</span><span class="sxs-lookup"><span data-stu-id="e18a9-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="e18a9-105">Isecret</span><span class="sxs-lookup"><span data-stu-id="e18a9-105">ISecret</span></span>

<span data-ttu-id="e18a9-106">Die- `ISecret` Schnittstelle stellt einen geheimen Wert dar, z. b. Material für kryptografische Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="e18a9-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="e18a9-107">Es enthält die folgende API-Oberfläche:</span><span class="sxs-lookup"><span data-stu-id="e18a9-107">It contains the following API surface:</span></span>

* <span data-ttu-id="e18a9-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="e18a9-108">`Length`: `int`</span></span>

* <span data-ttu-id="e18a9-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="e18a9-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="e18a9-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="e18a9-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="e18a9-111">Die- `WriteSecretIntoBuffer` Methode füllt den angegebenen Puffer mit dem unformatierten geheimen Wert auf.</span><span class="sxs-lookup"><span data-stu-id="e18a9-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="e18a9-112">Der Grund, warum diese API den Puffer als Parameter annimmt, anstatt einen `byte[]` direkt zurückzugeben, besteht darin, dass der Aufrufer die Möglichkeit erhält, das Puffer Objekt anzuheften, wodurch die geheime Gefährdung des verwalteten Garbage Collector beschränkt wird.</span><span class="sxs-lookup"><span data-stu-id="e18a9-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="e18a9-113">Der- `Secret` Typ ist eine konkrete Implementierung von, `ISecret` bei der der geheime Wert im Prozess internen Speicher gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="e18a9-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="e18a9-114">Auf Windows-Plattformen wird der geheime Wert über " [cryptprotectmemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)" verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="e18a9-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>

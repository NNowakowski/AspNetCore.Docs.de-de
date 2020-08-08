---
title: Entwickeln von ASP.NET Core-Apps mit OpenAPI
author: ryanbrandenburg
description: Hier wird veranschaulicht, wie Sie das Tool „Microsoft.dotnet-openapi“ verwenden, um Verweise zu OpenAPI-Dateien hinzuzufügen.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 6a9b80e868a54bd76503a6421c34ae159421699b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022237"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="52211-103">Entwickeln von ASP.NET Core-Apps mit OpenAPI-Tools</span><span class="sxs-lookup"><span data-stu-id="52211-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="52211-104">Von Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="52211-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="52211-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) ist ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools) zum Verwalten von [OpenAPI](https://github.com/OAI/OpenAPI-Specification)-Verweisen innerhalb eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="52211-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="52211-106">Installation</span><span class="sxs-lookup"><span data-stu-id="52211-106">Installation</span></span>

<span data-ttu-id="52211-107">Führen Sie den folgenden Befehl aus, um `Microsoft.dotnet-openapi` zu installieren:</span><span class="sxs-lookup"><span data-stu-id="52211-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="52211-108">Hinzufügen</span><span class="sxs-lookup"><span data-stu-id="52211-108">Add</span></span>

<span data-ttu-id="52211-109">Wenn Sie mit einem der Befehle auf dieser Seite einen OpenAPI-Verweis hinzufügen, wird ein `<OpenApiReference />`-Element ähnlich dem folgenden zur *CSPROJ*-Datei hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="52211-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="52211-110">Der oben genannte Verweis ist erforderlich, damit die App den generierten Clientcode aufrufen kann.</span><span class="sxs-lookup"><span data-stu-id="52211-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="52211-111">Datei hinzufügen</span><span class="sxs-lookup"><span data-stu-id="52211-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="52211-112">Optionen</span><span class="sxs-lookup"><span data-stu-id="52211-112">Options</span></span>

| <span data-ttu-id="52211-113">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="52211-113">Short option</span></span>| <span data-ttu-id="52211-114">Lange Option</span><span class="sxs-lookup"><span data-stu-id="52211-114">Long option</span></span>| <span data-ttu-id="52211-115">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="52211-115">Description</span></span> | <span data-ttu-id="52211-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="52211-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="52211-117">-p</span><span class="sxs-lookup"><span data-stu-id="52211-117">-p</span></span>|<span data-ttu-id="52211-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="52211-118">--updateProject</span></span> | <span data-ttu-id="52211-119">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-119">The project to operate on.</span></span> |<span data-ttu-id="52211-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="52211-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="52211-121">-c</span><span class="sxs-lookup"><span data-stu-id="52211-121">-c</span></span>|<span data-ttu-id="52211-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="52211-122">--code-generator</span></span>| <span data-ttu-id="52211-123">Der Codegenerator, der auf den Verweis angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="52211-124">Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="52211-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="52211-125">Wenn `--code-generator` nicht angegeben ist, werden standardmäßig `NSwagCSharp`-Tools verwendet.</span><span class="sxs-lookup"><span data-stu-id="52211-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="52211-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="52211-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="52211-127">-H</span><span class="sxs-lookup"><span data-stu-id="52211-127">-h</span></span>|<span data-ttu-id="52211-128">--help</span><span class="sxs-lookup"><span data-stu-id="52211-128">--help</span></span>|<span data-ttu-id="52211-129">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="52211-129">Show help information</span></span>|<span data-ttu-id="52211-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="52211-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="52211-131">Argumente</span><span class="sxs-lookup"><span data-stu-id="52211-131">Arguments</span></span>

|  <span data-ttu-id="52211-132">Argument</span><span class="sxs-lookup"><span data-stu-id="52211-132">Argument</span></span>  | <span data-ttu-id="52211-133">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="52211-133">Description</span></span> | <span data-ttu-id="52211-134">Beispiel</span><span class="sxs-lookup"><span data-stu-id="52211-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="52211-135">source-file</span><span class="sxs-lookup"><span data-stu-id="52211-135">source-file</span></span> | <span data-ttu-id="52211-136">Die Quelle, aus der ein Verweis erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-136">The source to create a reference from.</span></span> <span data-ttu-id="52211-137">Es muss sich um eine OpenAPI-Datei handeln.</span><span class="sxs-lookup"><span data-stu-id="52211-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="52211-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="52211-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="52211-139">URL hinzufügen</span><span class="sxs-lookup"><span data-stu-id="52211-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="52211-140">Optionen</span><span class="sxs-lookup"><span data-stu-id="52211-140">Options</span></span>

| <span data-ttu-id="52211-141">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="52211-141">Short option</span></span>| <span data-ttu-id="52211-142">Lange Option</span><span class="sxs-lookup"><span data-stu-id="52211-142">Long option</span></span>| <span data-ttu-id="52211-143">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="52211-143">Description</span></span> | <span data-ttu-id="52211-144">Beispiel</span><span class="sxs-lookup"><span data-stu-id="52211-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="52211-145">-p</span><span class="sxs-lookup"><span data-stu-id="52211-145">-p</span></span>|<span data-ttu-id="52211-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="52211-146">--updateProject</span></span> | <span data-ttu-id="52211-147">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-147">The project to operate on.</span></span> |<span data-ttu-id="52211-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="52211-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="52211-149">-o</span><span class="sxs-lookup"><span data-stu-id="52211-149">-o</span></span>|<span data-ttu-id="52211-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="52211-150">--output-file</span></span> | <span data-ttu-id="52211-151">Speicherort für die lokale Kopie der OpenAPI-Datei.</span><span class="sxs-lookup"><span data-stu-id="52211-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="52211-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="52211-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="52211-153">-c</span><span class="sxs-lookup"><span data-stu-id="52211-153">-c</span></span>|<span data-ttu-id="52211-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="52211-154">--code-generator</span></span>| <span data-ttu-id="52211-155">Der Codegenerator, der auf den Verweis angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="52211-156">Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="52211-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="52211-157">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="52211-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="52211-158">-H</span><span class="sxs-lookup"><span data-stu-id="52211-158">-h</span></span>|<span data-ttu-id="52211-159">--help</span><span class="sxs-lookup"><span data-stu-id="52211-159">--help</span></span>|<span data-ttu-id="52211-160">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="52211-160">Show help information</span></span>|<span data-ttu-id="52211-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="52211-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="52211-162">Argumente</span><span class="sxs-lookup"><span data-stu-id="52211-162">Arguments</span></span>

|  <span data-ttu-id="52211-163">Argument</span><span class="sxs-lookup"><span data-stu-id="52211-163">Argument</span></span>  | <span data-ttu-id="52211-164">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="52211-164">Description</span></span> | <span data-ttu-id="52211-165">Beispiel</span><span class="sxs-lookup"><span data-stu-id="52211-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="52211-166">source-URL</span><span class="sxs-lookup"><span data-stu-id="52211-166">source-URL</span></span> | <span data-ttu-id="52211-167">Die Quelle, aus der ein Verweis erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-167">The source to create a reference from.</span></span> <span data-ttu-id="52211-168">Es muss sich um eine URL handeln.</span><span class="sxs-lookup"><span data-stu-id="52211-168">Must be a URL.</span></span> |<span data-ttu-id="52211-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="52211-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="52211-170">Entfernen</span><span class="sxs-lookup"><span data-stu-id="52211-170">Remove</span></span>

<span data-ttu-id="52211-171">Entfernt den OpenAPI-Verweis, der mit dem angegebenen Dateinamen übereinstimmt, aus der *CSPROJ*-Datei.</span><span class="sxs-lookup"><span data-stu-id="52211-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="52211-172">Wenn der OpenAPI-Verweis entfernt wird, werden keine Clients generiert.</span><span class="sxs-lookup"><span data-stu-id="52211-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="52211-173">Lokale *JSON*- und *YAML*-Dateien werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="52211-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="52211-174">Optionen</span><span class="sxs-lookup"><span data-stu-id="52211-174">Options</span></span>

| <span data-ttu-id="52211-175">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="52211-175">Short option</span></span>| <span data-ttu-id="52211-176">Lange Option</span><span class="sxs-lookup"><span data-stu-id="52211-176">Long option</span></span>| <span data-ttu-id="52211-177">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="52211-177">Description</span></span>| <span data-ttu-id="52211-178">Beispiel</span><span class="sxs-lookup"><span data-stu-id="52211-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="52211-179">-p</span><span class="sxs-lookup"><span data-stu-id="52211-179">-p</span></span>|<span data-ttu-id="52211-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="52211-180">--updateProject</span></span> | <span data-ttu-id="52211-181">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-181">The project to operate on.</span></span> |<span data-ttu-id="52211-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="52211-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="52211-183">-H</span><span class="sxs-lookup"><span data-stu-id="52211-183">-h</span></span>|<span data-ttu-id="52211-184">--help</span><span class="sxs-lookup"><span data-stu-id="52211-184">--help</span></span>|<span data-ttu-id="52211-185">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="52211-185">Show help information</span></span>|<span data-ttu-id="52211-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="52211-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="52211-187">Argumente</span><span class="sxs-lookup"><span data-stu-id="52211-187">Arguments</span></span>

|  <span data-ttu-id="52211-188">Argument</span><span class="sxs-lookup"><span data-stu-id="52211-188">Argument</span></span>  | <span data-ttu-id="52211-189">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="52211-189">Description</span></span>| <span data-ttu-id="52211-190">Beispiel</span><span class="sxs-lookup"><span data-stu-id="52211-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="52211-191">source-file</span><span class="sxs-lookup"><span data-stu-id="52211-191">source-file</span></span> | <span data-ttu-id="52211-192">Die Quelle, aus der der Verweis entfernt werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-192">The source to remove the reference to.</span></span> |<span data-ttu-id="52211-193">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="52211-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="52211-194">Aktualisieren</span><span class="sxs-lookup"><span data-stu-id="52211-194">Refresh</span></span>

<span data-ttu-id="52211-195">Aktualisiert die lokale Version einer Datei, die unter Verwendung der neuesten Download-URL heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="52211-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="52211-196">Optionen</span><span class="sxs-lookup"><span data-stu-id="52211-196">Options</span></span>

| <span data-ttu-id="52211-197">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="52211-197">Short option</span></span>| <span data-ttu-id="52211-198">Lange Option</span><span class="sxs-lookup"><span data-stu-id="52211-198">Long option</span></span>| <span data-ttu-id="52211-199">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="52211-199">Description</span></span> | <span data-ttu-id="52211-200">Beispiel</span><span class="sxs-lookup"><span data-stu-id="52211-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="52211-201">-p</span><span class="sxs-lookup"><span data-stu-id="52211-201">-p</span></span>|<span data-ttu-id="52211-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="52211-202">--updateProject</span></span> | <span data-ttu-id="52211-203">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-203">The project to operate on.</span></span> | <span data-ttu-id="52211-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="52211-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="52211-205">-H</span><span class="sxs-lookup"><span data-stu-id="52211-205">-h</span></span>|<span data-ttu-id="52211-206">--help</span><span class="sxs-lookup"><span data-stu-id="52211-206">--help</span></span>|<span data-ttu-id="52211-207">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="52211-207">Show help information</span></span>|<span data-ttu-id="52211-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="52211-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="52211-209">Argumente</span><span class="sxs-lookup"><span data-stu-id="52211-209">Arguments</span></span>

|  <span data-ttu-id="52211-210">Argument</span><span class="sxs-lookup"><span data-stu-id="52211-210">Argument</span></span>  | <span data-ttu-id="52211-211">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="52211-211">Description</span></span> | <span data-ttu-id="52211-212">Beispiel</span><span class="sxs-lookup"><span data-stu-id="52211-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="52211-213">source-URL</span><span class="sxs-lookup"><span data-stu-id="52211-213">source-URL</span></span> | <span data-ttu-id="52211-214">Die URL, aus der der Verweis aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="52211-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="52211-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="52211-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |

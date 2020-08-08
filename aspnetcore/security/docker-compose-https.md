---
title: Hosting ASP.net Core Abbilds in Containern mithilfe von Docker Compose mit HTTPS
author: ravipal
description: Erfahren Sie, wie Sie ASP.net Core Images mit docker Compose über HTTPS hosten.
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: c3b627cdc74f1b40611d84bc3419e678e2dfbba4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022458"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="98719-103">Hosting ASP.net Core Images mit docker Compose über HTTPS</span><span class="sxs-lookup"><span data-stu-id="98719-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="98719-104">ASP.net Core verwendet [standardmäßig HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="98719-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="98719-105">[Https](https://en.wikipedia.org/wiki/HTTPS) stützt sich auf [Zertifikate](https://en.wikipedia.org/wiki/Public_key_certificate) für Vertrauenswürdigkeit, Identität und Verschlüsselung.</span><span class="sxs-lookup"><span data-stu-id="98719-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="98719-106">In diesem Dokument wird erläutert, wie vorgefertigte Container Images mit HTTPS ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="98719-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="98719-107">Weitere Informationen finden Sie [unter Entwickeln von ASP.net Core Anwendungen mit docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) für Entwicklungsszenarien.</span><span class="sxs-lookup"><span data-stu-id="98719-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="98719-108">Für dieses Beispiel ist [docker 17,06](https://docs.docker.com/release-notes/docker-ce) oder höher des [docker-Clients](https://www.docker.com/products/docker)erforderlich.</span><span class="sxs-lookup"><span data-stu-id="98719-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="98719-109">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="98719-109">Prerequisites</span></span>

<span data-ttu-id="98719-110">Für einige der Anweisungen in diesem Dokument ist das [.net Core 2,2 SDK](https://dotnet.microsoft.com/download) oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="98719-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="98719-111">Zertifikate</span><span class="sxs-lookup"><span data-stu-id="98719-111">Certificates</span></span>

<span data-ttu-id="98719-112">Ein Zertifikat von einer [Zertifizierungs](https://wikipedia.org/wiki/Certificate_authority) Stelle ist für das [Produktions Hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) für eine Domäne erforderlich.</span><span class="sxs-lookup"><span data-stu-id="98719-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="98719-113">[Let's Encrypt](https://letsencrypt.org/)ist eine Zertifizierungsstelle, die kostenlose Zertifikate anbietet.</span><span class="sxs-lookup"><span data-stu-id="98719-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="98719-114">In diesem Dokument werden [selbst signierte Entwicklungs Zertifikate](https://wikipedia.org/wiki/Self-signed_certificate) für das Hosting von vordefinierten Images verwendet `localhost` .</span><span class="sxs-lookup"><span data-stu-id="98719-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="98719-115">Die Anweisungen ähneln der Verwendung von Produktions Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="98719-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="98719-116">Für Produktions Zertifikate:</span><span class="sxs-lookup"><span data-stu-id="98719-116">For production certificates:</span></span>

* <span data-ttu-id="98719-117">Das `dotnet dev-certs` Tool ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="98719-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="98719-118">Zertifikate müssen nicht an dem Speicherort gespeichert werden, der in den Anweisungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="98719-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="98719-119">Speichern Sie die Zertifikate an einem beliebigen Speicherort außerhalb des Website Verzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="98719-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="98719-120">Die Anweisungen im folgenden Abschnitt enthalten ein Volume zum Einbinden von Zertifikaten in Containern mithilfe der- `volumes` Eigenschaft in *docker-Compose. yml.*</span><span class="sxs-lookup"><span data-stu-id="98719-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="98719-121">Sie können Zertifikate in Container Images mit einem `COPY` Befehl in einer *dockerfile-Datei*hinzufügen, dies wird jedoch nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="98719-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="98719-122">Das Kopieren von Zertifikaten in ein Abbild wird aus den folgenden Gründen nicht empfohlen:</span><span class="sxs-lookup"><span data-stu-id="98719-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="98719-123">Es ist schwierig, dasselbe Image für Tests mit Entwickler Zertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="98719-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="98719-124">Es ist schwierig, das gleiche Image für das Hosting mit Produktions Zertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="98719-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="98719-125">Es besteht ein erhebliches Risiko für die Offenlegung von Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="98719-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="98719-126">Starten eines Containers mit HTTPS-Unterstützung mithilfe von Docker Compose</span><span class="sxs-lookup"><span data-stu-id="98719-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="98719-127">Verwenden Sie die folgenden Anweisungen für die Betriebssystem Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="98719-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="98719-128">Windows mithilfe von Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="98719-128">Windows using Linux containers</span></span>

<span data-ttu-id="98719-129">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="98719-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="98719-130">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="98719-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="98719-131">Erstellen Sie eine _docker-Compose. Debug. yml_ -Datei mit folgendem Inhalt:</span><span class="sxs-lookup"><span data-stu-id="98719-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="98719-132">Das in der Docker Compose-Datei angegebene Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="98719-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="98719-133">Starten Sie den Container mit ASP.net Core, die für HTTPS konfiguriert sind:</span><span class="sxs-lookup"><span data-stu-id="98719-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="98719-134">macOS oder Linux</span><span class="sxs-lookup"><span data-stu-id="98719-134">macOS or Linux</span></span>

<span data-ttu-id="98719-135">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="98719-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="98719-136">`dotnet dev-certs https --trust`wird nur unter macOS und Windows unterstützt.</span><span class="sxs-lookup"><span data-stu-id="98719-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="98719-137">Sie müssen Zertifikate unter Linux auf die gleiche Weise als vertrauenswürdig einstufen, die von Ihrer Distribution unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="98719-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="98719-138">Es ist wahrscheinlich, dass Sie das Zertifikat in Ihrem Browser als vertrauenswürdig einstufen müssen.</span><span class="sxs-lookup"><span data-stu-id="98719-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="98719-139">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="98719-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="98719-140">Erstellen Sie eine _docker-Compose. Debug. yml_ -Datei mit folgendem Inhalt:</span><span class="sxs-lookup"><span data-stu-id="98719-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="98719-141">Das in der Docker Compose-Datei angegebene Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="98719-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="98719-142">Starten Sie den Container mit ASP.net Core, die für HTTPS konfiguriert sind:</span><span class="sxs-lookup"><span data-stu-id="98719-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="98719-143">Windows mithilfe von Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="98719-143">Windows using Windows containers</span></span>

<span data-ttu-id="98719-144">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="98719-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="98719-145">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="98719-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="98719-146">Erstellen Sie eine _docker-Compose. Debug. yml_ -Datei mit folgendem Inhalt:</span><span class="sxs-lookup"><span data-stu-id="98719-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="98719-147">Das in der Docker Compose-Datei angegebene Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="98719-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="98719-148">Starten Sie den Container mit ASP.net Core, die für HTTPS konfiguriert sind:</span><span class="sxs-lookup"><span data-stu-id="98719-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

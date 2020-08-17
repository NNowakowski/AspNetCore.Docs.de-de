---
title: Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: fa73075650a23d90e2e546335c06c4d50a29d34a
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130261"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="56d4b-102">Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="56d4b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="56d4b-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="56d4b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="56d4b-104">Für das Erstellen einer [eigenständigen Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly), die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="56d4b-104">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="56d4b-105">[Erstellen Sie einen AAD-Mandanten und eine Webanwendung:](/azure/active-directory/develop/v2-overview)</span><span class="sxs-lookup"><span data-stu-id="56d4b-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="56d4b-106">Registrieren Sie eine AAD-App im Bereich **Azure Active Directory** > **App-Registrierungen** des Azure-Portals:</span><span class="sxs-lookup"><span data-stu-id="56d4b-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="56d4b-107">Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD**).</span><span class="sxs-lookup"><span data-stu-id="56d4b-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="56d4b-108">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="56d4b-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="56d4b-109">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** auswählen.</span><span class="sxs-lookup"><span data-stu-id="56d4b-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="56d4b-110">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="56d4b-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="56d4b-111">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="56d4b-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="56d4b-112">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="56d4b-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="56d4b-113">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="56d4b-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="56d4b-114">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="56d4b-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="56d4b-115">Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="56d4b-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="56d4b-116">Deaktivieren Sie das Kontrollkästchen bei **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="56d4b-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="56d4b-117">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="56d4b-117">Select **Register**.</span></span>

<span data-ttu-id="56d4b-118">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="56d4b-118">Record the following information:</span></span>

* <span data-ttu-id="56d4b-119">Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="56d4b-119">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="56d4b-120">Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="56d4b-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="56d4b-121">Gehen Sie unter **Authentifizierung** > **Plattformkonfigurationen** > **Web** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="56d4b-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="56d4b-122">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="56d4b-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="56d4b-123">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="56d4b-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="56d4b-124">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="56d4b-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="56d4b-125">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="56d4b-125">Select the **Save** button.</span></span>

<span data-ttu-id="56d4b-126">Erstellen Sie die App in einem leeren Ordner.</span><span class="sxs-lookup"><span data-stu-id="56d4b-126">Create the app in an empty folder.</span></span> <span data-ttu-id="56d4b-127">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="56d4b-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="56d4b-128">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="56d4b-128">Placeholder</span></span>   | <span data-ttu-id="56d4b-129">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="56d4b-129">Azure portal name</span></span>       | <span data-ttu-id="56d4b-130">Beispiel</span><span class="sxs-lookup"><span data-stu-id="56d4b-130">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="56d4b-131">Anwendungs-ID (Client)</span><span class="sxs-lookup"><span data-stu-id="56d4b-131">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="56d4b-132">Verzeichnis-ID (Mandant)</span><span class="sxs-lookup"><span data-stu-id="56d4b-132">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="56d4b-133">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="56d4b-133">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="56d4b-134">Im Azure-Portal ist der **Authentifizierung** > **Plattformkonfigurationen** > **Web** > **Umleitungs-URI** der App für Apps für Port 5001 konfiguriert, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="56d4b-134">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="56d4b-135">Wenn die App auf einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="56d4b-135">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="56d4b-136">Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="56d4b-136">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="56d4b-137">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen durchführen können:</span><span class="sxs-lookup"><span data-stu-id="56d4b-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="56d4b-138">Anmelden bei der App mit einem AAD-Benutzerkonto</span><span class="sxs-lookup"><span data-stu-id="56d4b-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="56d4b-139">Anfordern von Zugriffstoken für Microsoft-APIs</span><span class="sxs-lookup"><span data-stu-id="56d4b-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="56d4b-140">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="56d4b-140">For more information, see:</span></span>
  * [<span data-ttu-id="56d4b-141">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="56d4b-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="56d4b-142">[Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="56d4b-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="56d4b-143">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="56d4b-143">Authentication package</span></span>

<span data-ttu-id="56d4b-144">Wenn eine App zum Verwenden von Geschäfts-, Schul- oder Unikonten (`SingleOrg`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="56d4b-144">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="56d4b-145">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="56d4b-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="56d4b-146">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="56d4b-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="56d4b-147">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="56d4b-147">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="56d4b-148">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="56d4b-148">Authentication service support</span></span>

<span data-ttu-id="56d4b-149">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="56d4b-149">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="56d4b-150">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="56d4b-150">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="56d4b-151">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="56d4b-151">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="56d4b-152">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="56d4b-152">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="56d4b-153">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="56d4b-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="56d4b-154">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="56d4b-154">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="56d4b-155">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="56d4b-155">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="56d4b-156">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="56d4b-156">Access token scopes</span></span>

<span data-ttu-id="56d4b-157">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="56d4b-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="56d4b-158">Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standard-Zugriffstokenbereichen von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> hinzu:</span><span class="sxs-lookup"><span data-stu-id="56d4b-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="56d4b-159">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="56d4b-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="56d4b-160">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="56d4b-160">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="56d4b-161">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="56d4b-161">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="56d4b-162">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="56d4b-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="56d4b-163">Indexseite</span><span class="sxs-lookup"><span data-stu-id="56d4b-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="56d4b-164">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="56d4b-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="56d4b-165">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="56d4b-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="56d4b-166">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="56d4b-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="56d4b-167">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="56d4b-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="56d4b-168">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="56d4b-168">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="56d4b-169">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="56d4b-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="56d4b-170">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="56d4b-170">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

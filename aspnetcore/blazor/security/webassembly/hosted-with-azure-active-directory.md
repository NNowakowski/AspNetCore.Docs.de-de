---
title: Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 08e202f0876d01a98c099424fd65b06cbe82a3b2
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130339"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="a2278-102">Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="a2278-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="a2278-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a2278-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a2278-104">In diesem Artikel wird beschrieben, wie Sie eine [gehostete Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="a2278-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="a2278-105">Registrieren von Apps in AAD und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="a2278-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a2278-106">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="a2278-106">Create a tenant</span></span>

<span data-ttu-id="a2278-107">Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines Mandanten](/azure/active-directory/develop/quickstart-create-new-tenant), um einen Mandanten in AAD zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a2278-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a2278-108">Registrieren einer Server-API-App</span><span class="sxs-lookup"><span data-stu-id="a2278-108">Register a server API app</span></span>

<span data-ttu-id="a2278-109">Befolgen Sie die Anweisungen unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und in den anschließenden Azure AAD-Themen, um eine AAD-App für die *Server-API-App* zu registrieren, und tun Sie danach Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a2278-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="a2278-110">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="a2278-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a2278-111">Geben Sie einen **Namen** für die App an (z. B. **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="a2278-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a2278-112">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="a2278-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="a2278-113">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="a2278-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a2278-114">Die *Server-API-App* erfordert in diesem Szenario keinen **Umleitungs-URI**. Belassen Sie daher die Dropdownauswahl bei **Web**, und geben Sie keinen Umleitungs-URI ein.</span><span class="sxs-lookup"><span data-stu-id="a2278-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a2278-115">Deaktivieren Sie das Kontrollkästchen bei **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="a2278-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a2278-116">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="a2278-116">Select **Register**.</span></span>

<span data-ttu-id="a2278-117">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="a2278-117">Record the following information:</span></span>

* <span data-ttu-id="a2278-118">Anwendungs-ID (Client-ID) der *Server-API-App* (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="a2278-118">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="a2278-119">Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="a2278-119">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="a2278-120">Primäre, Herausgeber- oder Mandantendomäne für AAD (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a2278-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="a2278-121">Entfernen Sie unter **API-Berechtigungen** die Berechtigung **Microsoft Graph** > **User.Read**, da die App keine Anmeldung oder Zugriff auf Benutzerprofile benötigt.</span><span class="sxs-lookup"><span data-stu-id="a2278-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="a2278-122">Gehen Sie unter **Eine API verfügbar machen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="a2278-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="a2278-123">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="a2278-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a2278-124">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="a2278-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a2278-125">Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a2278-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a2278-126">Geben Sie einen **Anzeigenamen der Administratorzustimmung** an (z. B. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="a2278-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a2278-127">Geben Sie eine **Beschreibung der Administratorzustimmung** an (z. B. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="a2278-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a2278-128">Vergewissern Sie sich, dass **Zustand** auf **Aktiviert** gesetzt ist.</span><span class="sxs-lookup"><span data-stu-id="a2278-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a2278-129">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a2278-129">Select **Add scope**.</span></span>

<span data-ttu-id="a2278-130">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="a2278-130">Record the following information:</span></span>

* <span data-ttu-id="a2278-131">App-ID-URI (z. B. `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="a2278-131">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="a2278-132">Standardbereich (z. B. `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="a2278-132">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="a2278-133">Für den App-ID-URI ist möglicherweise eine bestimmte Konfiguration in der Client-App erforderlich, die im Abschnitt [Zugriffstokenbereiche](#access-token-scopes) weiter unten in diesem Thema beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="a2278-133">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a2278-134">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="a2278-134">Register a client app</span></span>

<span data-ttu-id="a2278-135">Befolgen Sie die Anweisungen unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und in den anschließenden Azure AAD-Themen, um eine AAD-App für die *Client-App* zu registrieren, und tun Sie danach Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a2278-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="a2278-136">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="a2278-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a2278-137">Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD**).</span><span class="sxs-lookup"><span data-stu-id="a2278-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="a2278-138">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="a2278-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="a2278-139">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="a2278-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a2278-140">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="a2278-140">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a2278-141">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="a2278-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a2278-142">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="a2278-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a2278-143">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der Server-App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="a2278-143">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a2278-144">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="a2278-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a2278-145">Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="a2278-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a2278-146">Deaktivieren Sie das Kontrollkästchen bei **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="a2278-146">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a2278-147">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="a2278-147">Select **Register**.</span></span>

<span data-ttu-id="a2278-148">Notieren Sie sich die Anwendungs-ID (Client-ID) der *Client-App* (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="a2278-148">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a2278-149">Gehen Sie unter **Authentifizierung** > **Plattformkonfigurationen** > **Web** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="a2278-149">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a2278-150">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a2278-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a2278-151">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="a2278-151">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a2278-152">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="a2278-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a2278-153">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a2278-153">Select the **Save** button.</span></span>

<span data-ttu-id="a2278-154">Gehen Sie unter **API-Berechtigungen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="a2278-154">In **API permissions**:</span></span>

1. <span data-ttu-id="a2278-155">Sorgen Sie dafür, dass die App über die Berechtigung **Microsoft Graph** > **User.Read** verfügt.</span><span class="sxs-lookup"><span data-stu-id="a2278-155">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a2278-156">Klicken Sie auf **Berechtigung hinzufügen** und dann auf **Meine APIs**.</span><span class="sxs-lookup"><span data-stu-id="a2278-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a2278-157">Wählen Sie die *Server-API-App* aus der Spalte **Name** aus (z. B. **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="a2278-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a2278-158">Öffnen Sie die **API**-Liste.</span><span class="sxs-lookup"><span data-stu-id="a2278-158">Open the **API** list.</span></span>
1. <span data-ttu-id="a2278-159">Ermöglichen Sie den Zugriff auf die API (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a2278-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a2278-160">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a2278-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a2278-161">Klicken Sie auf die Schaltfläche **Administratoreinwilligung für {MANDANTENNAME} erteilen**.</span><span class="sxs-lookup"><span data-stu-id="a2278-161">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="a2278-162">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="a2278-162">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a2278-163">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="a2278-163">Create the app</span></span>

<span data-ttu-id="a2278-164">Verwenden Sie einen leeren Ordner, ersetzen Sie im folgenden Befehl die Platzhalter durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="a2278-164">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="a2278-165">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="a2278-165">Placeholder</span></span>                  | <span data-ttu-id="a2278-166">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="a2278-166">Azure portal name</span></span>                                     | <span data-ttu-id="a2278-167">Beispiel</span><span class="sxs-lookup"><span data-stu-id="a2278-167">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="a2278-168">Anwendungs-ID (Client-ID) für die *Client-App*</span><span class="sxs-lookup"><span data-stu-id="a2278-168">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="a2278-169">Bereichsname</span><span class="sxs-lookup"><span data-stu-id="a2278-169">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="a2278-170">Anwendungs-ID (Client-ID) für die *Server-API-App*</span><span class="sxs-lookup"><span data-stu-id="a2278-170">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="a2278-171">Anwendungs-ID-URI ([siehe Hinweis](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="a2278-171">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="a2278-172">Primäre, Herausgeber- oder Mandantendomäne</span><span class="sxs-lookup"><span data-stu-id="a2278-172">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="a2278-173">Verzeichnis-ID (Mandant)</span><span class="sxs-lookup"><span data-stu-id="a2278-173">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="a2278-174">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="a2278-174">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a2278-175">Übergeben Sie den App-ID-URI an die Option `app-id-uri`. Beachten Sie jedoch, dass eine Änderung der Konfiguration in der Client-App erforderlich sein könnte, die im Abschnitt [Zugriffstokenbereiche](#access-token-scopes) beschrieben ist.</span><span class="sxs-lookup"><span data-stu-id="a2278-175">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="a2278-176">Im Azure-Portal ist der **Authentifizierung** > **Plattformkonfigurationen** > **Web** > **Umleitungs-URI** der *Client-App* für Apps für Port 5001 konfiguriert, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a2278-176">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a2278-177">Wenn die *Client-App* an einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der *Server-API-App* im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="a2278-177">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a2278-178">Wenn der Port nicht zuvor mit dem bekannten Port der *Client-App* konfiguriert wurde, kehren Sie zur *Client-App-Registrierung* im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="a2278-178">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a2278-179">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a2278-179">Server app configuration</span></span>

<span data-ttu-id="a2278-180">*Dieser Abschnitt bezieht sich auf die **`Server`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="a2278-180">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a2278-181">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="a2278-181">Authentication package</span></span>

<span data-ttu-id="a2278-182">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs wird vom Paket [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="a2278-182">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="a2278-183">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="a2278-183">Authentication service support</span></span>

<span data-ttu-id="a2278-184">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="a2278-184">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a2278-185">Die <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>-Methode richtet die spezifischen Parameter im JWT-Bearerhandler ein, die zum Überprüfen der von Azure Active Directory ausgegebenen Token erforderlich sind:</span><span class="sxs-lookup"><span data-stu-id="a2278-185">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="a2278-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> stellen Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="a2278-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="a2278-187">Die App versucht, Token auf eingehende Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="a2278-187">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a2278-188">Jede Anforderung, die versucht, ohne die richtigen Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="a2278-188">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="a2278-189">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="a2278-189">User.Identity.Name</span></span>

<span data-ttu-id="a2278-190">Standardmäßig füllt die Server-App-API `User.Identity.Name` mit dem Wert aus dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`-Anspruchstyp (z. B. `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) auf.</span><span class="sxs-lookup"><span data-stu-id="a2278-190">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="a2278-191">Konfigurieren Sie <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`, um die App so zu konfigurieren, dass sie den Wert aus dem `name`-Anspruchstyp empfängt:</span><span class="sxs-lookup"><span data-stu-id="a2278-191">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a2278-192">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="a2278-192">App settings</span></span>

<span data-ttu-id="a2278-193">Die Datei `appsettings.json` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="a2278-193">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="a2278-194">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a2278-194">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="a2278-195">WeatherForecast-Controller</span><span class="sxs-lookup"><span data-stu-id="a2278-195">WeatherForecast controller</span></span>

<span data-ttu-id="a2278-196">Der WeatherForecast-Controller (*Controllers/WeatherForecastController.cs*) macht eine geschützte API mit auf den Controller angewendetem [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a2278-196">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="a2278-197">Es ist **wichtig**, Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="a2278-197">It's **important** to understand that:</span></span>

* <span data-ttu-id="a2278-198">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="a2278-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a2278-199">Das in der Blazor WebAssembly-App verwendete [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut dient nur als Hinweis für die App, dass der Benutzer autorisiert sein sollte, damit die App richtig funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a2278-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="a2278-200">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a2278-200">Client app configuration</span></span>

<span data-ttu-id="a2278-201">*Dieser Abschnitt bezieht sich auf die **`Client`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="a2278-201">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a2278-202">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="a2278-202">Authentication package</span></span>

<span data-ttu-id="a2278-203">Wenn eine App zum Verwenden von Geschäfts-, Schul- oder Unikonten (`SingleOrg`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="a2278-203">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="a2278-204">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="a2278-204">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a2278-205">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="a2278-205">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="a2278-206">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="a2278-206">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a2278-207">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="a2278-207">Authentication service support</span></span>

<span data-ttu-id="a2278-208">Es wird Unterstützung für <xref:System.Net.Http.HttpClient>-Instanzen hinzugefügt, die bei Anforderungen an das Serverprojekt Zugriffstoken einschließen.</span><span class="sxs-lookup"><span data-stu-id="a2278-208">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a2278-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2278-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a2278-210">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="a2278-210">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="a2278-211">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="a2278-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="a2278-212">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="a2278-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a2278-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2278-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a2278-214">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="a2278-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a2278-215">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration im Azure-Portal beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a2278-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a2278-216">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="a2278-216">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a2278-217">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a2278-217">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a2278-218">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="a2278-218">Access token scopes</span></span>

<span data-ttu-id="a2278-219">Zu den Standard-Zugriffstokenbereichen zählen die Zugriffstokenbereiche, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="a2278-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a2278-220">standardmäßig in der Anmeldeanforderung enthalten</span><span class="sxs-lookup"><span data-stu-id="a2278-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a2278-221">zum Bereitstellen eines Zugriffstokens direkt nach der Authentifizierung verwendet</span><span class="sxs-lookup"><span data-stu-id="a2278-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a2278-222">Alle Bereiche müssen gemäß den Azure Active Directory-Regeln zur selben App gehören.</span><span class="sxs-lookup"><span data-stu-id="a2278-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a2278-223">Zusätzliche Bereiche können nach Bedarf für zusätzliche API-Apps hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="a2278-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="a2278-224">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="a2278-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a2278-225">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="a2278-225">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a2278-226">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="a2278-226">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="a2278-227">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="a2278-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a2278-228">Indexseite</span><span class="sxs-lookup"><span data-stu-id="a2278-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a2278-229">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="a2278-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a2278-230">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="a2278-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a2278-231">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="a2278-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a2278-232">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="a2278-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a2278-233">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="a2278-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a2278-234">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="a2278-234">Run the app</span></span>

<span data-ttu-id="a2278-235">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="a2278-235">Run the app from the Server project.</span></span> <span data-ttu-id="a2278-236">Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="a2278-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a2278-237">Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="a2278-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a2278-238">Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="a2278-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a2278-239">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a2278-239">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a2278-240">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="a2278-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="a2278-241">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="a2278-241">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

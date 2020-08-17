---
title: Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 8b8cf77cde281b8ea6a09d8edaec6606383a4c04
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130456"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="685cc-102">Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="685cc-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="685cc-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="685cc-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="685cc-104">In diesem Artikel wird beschrieben, wie Sie eine [gehostete Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory B2C (AAD)](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="685cc-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="685cc-105">Registrieren von Apps in AAD B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="685cc-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="685cc-106">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="685cc-106">Create a tenant</span></span>

<span data-ttu-id="685cc-107">Befolgen Sie die Anweisungen unter [Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten](/azure/active-directory-b2c/tutorial-create-tenant), um einen AAD B2C-Mandanten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="685cc-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="685cc-108">Notieren Sie sich die AAD B2C-Instanz (z. B. `https://contoso.b2clogin.com/`, wobei der nachgestellte Schrägstrich enthalten ist).</span><span class="sxs-lookup"><span data-stu-id="685cc-108">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="685cc-109">Die Instanz ist das Schema und der Host einer Azure-B2B-App-Registrierung, die Sie im Fenster **Endpunkte** auf der Seite **App-Registrierungen** im Azure-Portal finden.</span><span class="sxs-lookup"><span data-stu-id="685cc-109">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="685cc-110">Registrieren einer Server-API-App</span><span class="sxs-lookup"><span data-stu-id="685cc-110">Register a server API app</span></span>

<span data-ttu-id="685cc-111">Befolgen Sie die Anweisungen unter [Tutorial: Registrieren einer Webanwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications), um eine AAD-App für die *Server-API-App* zu registrieren, und gehen Sie dann wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="685cc-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="685cc-112">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="685cc-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="685cc-113">Geben Sie einen **Namen** für die App an (z. B. **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="685cc-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="685cc-114">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisationsverzeichnis oder ein beliebiger Identitätsanbieter Wählen Sie diese Option aus, um Benutzer bei Azure AD B2C zu authentifizieren.**</span><span class="sxs-lookup"><span data-stu-id="685cc-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="685cc-115">Die *Server-API-App* erfordert in diesem Szenario keinen **Umleitungs-URI**. Belassen Sie daher die Dropdownauswahl bei **Web**, und geben Sie keinen Umleitungs-URI ein.</span><span class="sxs-lookup"><span data-stu-id="685cc-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="685cc-116">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="685cc-116">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="685cc-117">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="685cc-117">Select **Register**.</span></span>

<span data-ttu-id="685cc-118">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="685cc-118">Record the following information:</span></span>

* <span data-ttu-id="685cc-119">Anwendungs-ID (Client-ID) der *Server-API-App* (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="685cc-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="685cc-120">Primäre, Herausgeber- oder Mandantendomäne für AAD (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="685cc-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="685cc-121">Gehen Sie unter **Eine API verfügbar machen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="685cc-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="685cc-122">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="685cc-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="685cc-123">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="685cc-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="685cc-124">Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="685cc-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="685cc-125">Geben Sie einen **Anzeigenamen der Administratorzustimmung** an (z. B. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="685cc-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="685cc-126">Geben Sie eine **Beschreibung der Administratorzustimmung** an (z. B. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="685cc-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="685cc-127">Vergewissern Sie sich, dass **Zustand** auf **Aktiviert** gesetzt ist.</span><span class="sxs-lookup"><span data-stu-id="685cc-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="685cc-128">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="685cc-128">Select **Add scope**.</span></span>

<span data-ttu-id="685cc-129">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="685cc-129">Record the following information:</span></span>

* <span data-ttu-id="685cc-130">App-ID-URI (z. B. `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="685cc-130">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="685cc-131">Standardbereich (z. B. `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="685cc-131">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="685cc-132">Für den App-ID-URI ist möglicherweise eine bestimmte Konfiguration in der Client-App erforderlich, die im Abschnitt [Zugriffstokenbereiche](#access-token-scopes) weiter unten in diesem Thema beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="685cc-132">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="685cc-133">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="685cc-133">Register a client app</span></span>

<span data-ttu-id="685cc-134">Befolgen Sie die Anweisungen unter [Tutorial: Registrieren einer Webanwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) noch mal, um eine AAD-App für die *Client-App* zu registrieren, und gehen Sie dann wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="685cc-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="685cc-135">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="685cc-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="685cc-136">Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="685cc-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="685cc-137">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisationsverzeichnis oder ein beliebiger Identitätsanbieter Wählen Sie diese Option aus, um Benutzer bei Azure AD B2C zu authentifizieren.**</span><span class="sxs-lookup"><span data-stu-id="685cc-137">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="685cc-138">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="685cc-138">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="685cc-139">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="685cc-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="685cc-140">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="685cc-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="685cc-141">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der Server-App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="685cc-141">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="685cc-142">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="685cc-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="685cc-143">Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="685cc-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="685cc-144">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="685cc-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="685cc-145">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="685cc-145">Select **Register**.</span></span>

<span data-ttu-id="685cc-146">Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="685cc-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="685cc-147">Gehen Sie unter **Authentifizierung** > **Plattformkonfigurationen** > **Web** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="685cc-147">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="685cc-148">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="685cc-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="685cc-149">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="685cc-149">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="685cc-150">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="685cc-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="685cc-151">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="685cc-151">Select the **Save** button.</span></span>

<span data-ttu-id="685cc-152">Gehen Sie unter **API-Berechtigungen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="685cc-152">In **API permissions**:</span></span>

1. <span data-ttu-id="685cc-153">Klicken Sie auf **Berechtigung hinzufügen** und dann auf **Meine APIs**.</span><span class="sxs-lookup"><span data-stu-id="685cc-153">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="685cc-154">Wählen Sie die *Server-API-App* aus der Spalte **Name** aus (z. B. **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="685cc-154">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="685cc-155">Öffnen Sie die **API**-Liste.</span><span class="sxs-lookup"><span data-stu-id="685cc-155">Open the **API** list.</span></span>
1. <span data-ttu-id="685cc-156">Ermöglichen Sie den Zugriff auf die API (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="685cc-156">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="685cc-157">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="685cc-157">Select **Add permissions**.</span></span>
1. <span data-ttu-id="685cc-158">Klicken Sie auf die Schaltfläche **Administratoreinwilligung für {MANDANTENNAME} erteilen**.</span><span class="sxs-lookup"><span data-stu-id="685cc-158">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="685cc-159">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="685cc-159">Select **Yes** to confirm.</span></span>

<span data-ttu-id="685cc-160">Gehen Sie unter **Startseite** > **Azure AD B2C** > **Benutzerflows** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="685cc-160">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="685cc-161">Erstellen Sie einen Benutzerflow für die Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="685cc-161">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="685cc-162">Wählen Sie mindestens das Benutzerattribut **Anwendungsansprüche** > **Anzeigename** aus, um `context.User.Identity.Name` in der `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) auszufüllen.</span><span class="sxs-lookup"><span data-stu-id="685cc-162">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="685cc-163">Notieren Sie sich den Namen des für die App erstellten Benutzerflows für die Registrierung und Anmeldung (z. B. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="685cc-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="685cc-164">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="685cc-164">Create the app</span></span>

<span data-ttu-id="685cc-165">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="685cc-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="685cc-166">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="685cc-166">Placeholder</span></span>                   | <span data-ttu-id="685cc-167">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="685cc-167">Azure portal name</span></span>                                     | <span data-ttu-id="685cc-168">Beispiel</span><span class="sxs-lookup"><span data-stu-id="685cc-168">Example</span></span>                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="685cc-169">Instanz</span><span class="sxs-lookup"><span data-stu-id="685cc-169">Instance</span></span>                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="685cc-170">Anwendungs-ID (Client-ID) für die *Client-App*</span><span class="sxs-lookup"><span data-stu-id="685cc-170">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="685cc-171">Bereichsname</span><span class="sxs-lookup"><span data-stu-id="685cc-171">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="685cc-172">Anwendungs-ID (Client-ID) für die *Server-API-App*</span><span class="sxs-lookup"><span data-stu-id="685cc-172">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="685cc-173">Anwendungs-ID-URI ([siehe Hinweis](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="685cc-173">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="685cc-174">Benutzerflow für die Registrierung oder Anmeldung</span><span class="sxs-lookup"><span data-stu-id="685cc-174">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="685cc-175">Primäre, Herausgeber- oder Mandantendomäne</span><span class="sxs-lookup"><span data-stu-id="685cc-175">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |

<span data-ttu-id="685cc-176">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="685cc-176">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="685cc-177">Übergeben Sie den App-ID-URI an die Option `app-id-uri`. Beachten Sie jedoch, dass eine Änderung der Konfiguration in der Client-App erforderlich sein könnte, die im Abschnitt [Zugriffstokenbereiche](#access-token-scopes) beschrieben ist.</span><span class="sxs-lookup"><span data-stu-id="685cc-177">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="685cc-178">Außerdem könnte der von der gehosteten Blazor-Vorlage festgelegte Bereich den Host des App-ID-URI wiederholt haben.</span><span class="sxs-lookup"><span data-stu-id="685cc-178">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="685cc-179">Vergewissern Sie sich, dass der für die `DefaultAccessTokenScopes`-Sammlung konfigurierte Bereich in `Program.Main` (`Program.cs`) der *Client-App* korrekt ist.</span><span class="sxs-lookup"><span data-stu-id="685cc-179">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="685cc-180">Im Azure-Portal ist der **Authentifizierung** > **Plattformkonfigurationen** > **Web** > **Umleitungs-URI** der *Client-App* für Apps für Port 5001 konfiguriert, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="685cc-180">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="685cc-181">Wenn die *Client-App* an einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der *Server-API-App* im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="685cc-181">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="685cc-182">Wenn der Port nicht zuvor mit dem bekannten Port der *Client-App* konfiguriert wurde, kehren Sie zur *Client-App-Registrierung* im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="685cc-182">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="685cc-183">Server-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="685cc-183">Server app configuration</span></span>

<span data-ttu-id="685cc-184">*Dieser Abschnitt bezieht sich auf die **`Server`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="685cc-184">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="685cc-185">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="685cc-185">Authentication package</span></span>

<span data-ttu-id="685cc-186">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs wird vom Paket [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="685cc-186">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="685cc-187">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="685cc-187">Authentication service support</span></span>

<span data-ttu-id="685cc-188">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="685cc-188">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="685cc-189">Die <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>-Methode richtet die spezifischen Parameter im JWT-Bearerhandler ein, die zum Überprüfen der von Azure Active Directory B2C ausgegebenen Token erforderlich sind:</span><span class="sxs-lookup"><span data-stu-id="685cc-189">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="685cc-190"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> stellen Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="685cc-190"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="685cc-191">Die App versucht, Token auf eingehende Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="685cc-191">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="685cc-192">Jede Anforderung, die versucht, ohne die richtigen Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="685cc-192">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="685cc-193">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="685cc-193">User.Identity.Name</span></span>

<span data-ttu-id="685cc-194">`User.Identity.Name` wird standardmäßig nicht ausgefüllt.</span><span class="sxs-lookup"><span data-stu-id="685cc-194">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="685cc-195">Konfigurieren Sie <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`, um die App so zu konfigurieren, dass sie den Wert aus dem `name`-Anspruchstyp empfängt:</span><span class="sxs-lookup"><span data-stu-id="685cc-195">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="685cc-196">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="685cc-196">App settings</span></span>

<span data-ttu-id="685cc-197">Die Datei `appsettings.json` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="685cc-197">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="685cc-198">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="685cc-198">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="685cc-199">WeatherForecast-Controller</span><span class="sxs-lookup"><span data-stu-id="685cc-199">WeatherForecast controller</span></span>

<span data-ttu-id="685cc-200">Der WeatherForecast-Controller (*Controllers/WeatherForecastController.cs*) macht eine geschützte API mit auf den Controller angewendetem [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut verfügbar.</span><span class="sxs-lookup"><span data-stu-id="685cc-200">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="685cc-201">Es ist **wichtig**, Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="685cc-201">It's **important** to understand that:</span></span>

* <span data-ttu-id="685cc-202">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="685cc-202">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="685cc-203">Das in der Blazor WebAssembly-App verwendete [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut dient nur als Hinweis für die App, dass der Benutzer autorisiert sein sollte, damit die App richtig funktioniert.</span><span class="sxs-lookup"><span data-stu-id="685cc-203">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="685cc-204">Client-App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="685cc-204">Client app configuration</span></span>

<span data-ttu-id="685cc-205">*Dieser Abschnitt bezieht sich auf die **`Client`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="685cc-205">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="685cc-206">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="685cc-206">Authentication package</span></span>

<span data-ttu-id="685cc-207">Wenn eine App zum Verwenden eines B2C-Einzelkontos (`IndividualB2C`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="685cc-207">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="685cc-208">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="685cc-208">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="685cc-209">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="685cc-209">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="685cc-210">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="685cc-210">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="685cc-211">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="685cc-211">Authentication service support</span></span>

<span data-ttu-id="685cc-212">Es wird Unterstützung für <xref:System.Net.Http.HttpClient>-Instanzen hinzugefügt, die bei Anforderungen an das Serverprojekt Zugriffstoken einschließen.</span><span class="sxs-lookup"><span data-stu-id="685cc-212">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="685cc-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="685cc-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="685cc-214">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="685cc-214">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="685cc-215">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="685cc-215">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="685cc-216">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="685cc-216">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="685cc-217">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="685cc-217">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="685cc-218">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="685cc-218">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="685cc-219">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration im Azure-Portal beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="685cc-219">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="685cc-220">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="685cc-220">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="685cc-221">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="685cc-221">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="685cc-222">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="685cc-222">Access token scopes</span></span>

<span data-ttu-id="685cc-223">Zu den Standard-Zugriffstokenbereichen zählen die Zugriffstokenbereiche, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="685cc-223">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="685cc-224">standardmäßig in der Anmeldeanforderung enthalten</span><span class="sxs-lookup"><span data-stu-id="685cc-224">Included by default in the sign in request.</span></span>
* <span data-ttu-id="685cc-225">zum Bereitstellen eines Zugriffstokens direkt nach der Authentifizierung verwendet</span><span class="sxs-lookup"><span data-stu-id="685cc-225">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="685cc-226">Alle Bereiche müssen gemäß den Azure Active Directory-Regeln zur selben App gehören.</span><span class="sxs-lookup"><span data-stu-id="685cc-226">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="685cc-227">Zusätzliche Bereiche können nach Bedarf für zusätzliche API-Apps hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="685cc-227">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="685cc-228">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="685cc-228">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="685cc-229">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="685cc-229">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="685cc-230">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="685cc-230">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="685cc-231">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="685cc-231">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="685cc-232">Indexseite</span><span class="sxs-lookup"><span data-stu-id="685cc-232">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="685cc-233">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="685cc-233">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="685cc-234">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="685cc-234">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="685cc-235">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="685cc-235">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="685cc-236">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="685cc-236">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="685cc-237">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="685cc-237">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="685cc-238">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="685cc-238">Run the app</span></span>

<span data-ttu-id="685cc-239">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="685cc-239">Run the app from the Server project.</span></span> <span data-ttu-id="685cc-240">Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="685cc-240">When using Visual Studio, either:</span></span>

* <span data-ttu-id="685cc-241">Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="685cc-241">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="685cc-242">Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="685cc-242">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="685cc-243">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="685cc-243">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="685cc-244">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="685cc-244">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="685cc-245">Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="685cc-245">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="685cc-246">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="685cc-246">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

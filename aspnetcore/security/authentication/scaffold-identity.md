---
title: Gerüst Identity in ASP.net Core Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie Identity ein Gerüst in einem ASP.net Core Projekt aufbauen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 09535f41d15b90fa5e50eb1f22f6aecef0530f0c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629560"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="b2482-103">Gerüst Identity in ASP.net Core Projekten</span><span class="sxs-lookup"><span data-stu-id="b2482-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="b2482-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b2482-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2482-105">ASP.net Core [ASP.NET Core Identity](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="b2482-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b2482-106">Anwendungen, die einschließen, Identity können das Gerüsten anwenden, um den Quellcode, der in der Identity Razor Klassenbibliothek (RCL) enthalten ist, selektiv hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b2482-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="b2482-107">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="b2482-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b2482-108">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="b2482-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b2482-109">Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek Identity.</span><span class="sxs-lookup"><span data-stu-id="b2482-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="b2482-110">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen der vollständigen Identity Benutzeroberflächen Quelle](#full).</span><span class="sxs-lookup"><span data-stu-id="b2482-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="b2482-111">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL- Identity Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="b2482-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="b2482-112">Sie können Code der Klassenbibliothek Identity auswählen, der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="b2482-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="b2482-113">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="b2482-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="b2482-114">In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines Identity Gerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="b2482-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="b2482-115">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="b2482-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b2482-116">Überprüfen Sie die Änderungen, nachdem Sie das Gerüst ausgeführt haben Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="b2482-117">Dienste sind erforderlich, wenn [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)und andere Sicherheitsfunktionen mit verwendet werden Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="b2482-118">Dienste oder Service-stubdienste werden beim Gerüstbau nicht generiert Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="b2482-119">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b2482-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b2482-120">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="b2482-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="b2482-121">Beim Gerüstbau Identity mit einem neuen Datenkontext in einem Projekt mit vorhandenen individuellen Konten:</span><span class="sxs-lookup"><span data-stu-id="b2482-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="b2482-122">`Startup.ConfigureServices`Entfernen Sie in die Aufrufe von:</span><span class="sxs-lookup"><span data-stu-id="b2482-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="b2482-123">Beispielsweise `AddDbContext` werden und `AddDefaultIdentity` im folgenden Code auskommentiert:</span><span class="sxs-lookup"><span data-stu-id="b2482-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="b2482-124">Der vorangehende Code kommentiert den Code, der in *Bereichen/ Identity / Identity HostingStartup.cs* dupliziert wird.</span><span class="sxs-lookup"><span data-stu-id="b2482-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="b2482-125">Apps, die mit einzelnen Konten erstellt wurden, sollten in der Regel ***keinen*** neuen Datenkontext erstellen.</span><span class="sxs-lookup"><span data-stu-id="b2482-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="b2482-126">Gerüst Identity in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="b2482-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b2482-127">Aktualisieren Sie die- `Startup` Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="b2482-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="b2482-128">Gerüst Identity in ein Razor Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-128">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b2482-129">Identitywird in *Areas/ Identity / Identity HostingStartup.cs*konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2482-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b2482-130">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b2482-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b2482-131">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="b2482-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b2482-132">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="b2482-132">Enable authentication</span></span>

<span data-ttu-id="b2482-133">Aktualisieren Sie die- `Startup` Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="b2482-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b2482-134">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="b2482-134">Layout changes</span></span>

<span data-ttu-id="b2482-135">Optional: Fügen Sie den Anmelde Namen partiell ( `_LoginPartial` ) der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="b2482-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="b2482-136">Gerüst Identity in ein Razor Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-136">Scaffold Identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b2482-137">Einige Identity Optionen sind in " *Bereiche/ Identity / Identity HostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2482-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b2482-138">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b2482-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b2482-139">Gerüst für Identity ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-139">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b2482-140">Optional: Fügen Sie der `_LoginPartial` Datei *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:</span><span class="sxs-lookup"><span data-stu-id="b2482-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="b2482-141">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b2482-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="b2482-142">Identitywird in *Areas/ Identity / Identity HostingStartup.cs*konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2482-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b2482-143">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="b2482-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b2482-144">Aktualisieren Sie die- `Startup` Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="b2482-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b2482-145">Gerüst für Identity ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="b2482-146">Gerüst Identity in ein Blazor Server Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b2482-147">Identitywird in *Areas/ Identity / Identity HostingStartup.cs*konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2482-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b2482-148">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b2482-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="b2482-149">Migrationen</span><span class="sxs-lookup"><span data-stu-id="b2482-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="b2482-150">Übergeben Sie ein XSRF-Token an die app.</span><span class="sxs-lookup"><span data-stu-id="b2482-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="b2482-151">Token können an-Komponenten übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="b2482-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="b2482-152">Wenn Authentifizierungs Token bereitgestellt und in der Authentifizierung gespeichert werden cookie , können Sie an-Komponenten übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="b2482-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="b2482-153">Razor Komponenten können nicht `HttpContext` direkt verwenden. Daher gibt es keine Möglichkeit, ein [Anti-Request-fälschungstoken (XSRF)](xref:security/anti-request-forgery) zu erhalten, um an Identity den Abmelde Endpunkt in bereitzustellen `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="b2482-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="b2482-154">Ein XSRF-Token kann an-Komponenten übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="b2482-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="b2482-155">Weitere Informationen finden Sie unter <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="b2482-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="b2482-156">Stellen Sie in der Datei *pages/_Host. cshtml* das Token her, nachdem Sie es der `InitialApplicationState` -Klasse und der-Klasse hinzugefügt haben `TokenProvider` :</span><span class="sxs-lookup"><span data-stu-id="b2482-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="b2482-157">Aktualisieren `App` Sie die Komponente (*app. Razor*), um Folgendes zuzuweisen `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="b2482-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="b2482-158">Der `TokenProvider` im Thema gezeigte Dienst wird in der `LoginDisplay` Komponente im folgenden Abschnitt Änderungen des [Layouts und des Authentifizierungs Flusses](#layout-and-authentication-flow-changes) verwendet.</span><span class="sxs-lookup"><span data-stu-id="b2482-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="b2482-159">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="b2482-159">Enable authentication</span></span>

<span data-ttu-id="b2482-160">In der- `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="b2482-160">In the `Startup` class:</span></span>

* <span data-ttu-id="b2482-161">Vergewissern Sie sich, dass Razor Seiten Dienste in hinzugefügt wurden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b2482-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="b2482-162">Wenn Sie den-Registrierungs [Anbieter](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)verwenden, registrieren Sie den Dienst.</span><span class="sxs-lookup"><span data-stu-id="b2482-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="b2482-163">Ruft `UseDatabaseErrorPage` für den Anwendungs-Generator in `Startup.Configure` für die Entwicklungsumgebung auf.</span><span class="sxs-lookup"><span data-stu-id="b2482-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="b2482-164">`UseAuthentication`Und `UseAuthorization` nach `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="b2482-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="b2482-165">Fügen Sie einen Endpunkt für Razor Seiten hinzu.</span><span class="sxs-lookup"><span data-stu-id="b2482-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="b2482-166">Änderungen beim Layout und bei der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b2482-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="b2482-167">Fügen Sie `RedirectToLogin` dem frei *gegebenen* Ordner der APP im Projektstamm eine Komponente (*redirecttologin. Razor*) hinzu:</span><span class="sxs-lookup"><span data-stu-id="b2482-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Fügen Sie `LoginDisplay` dem frei *gegebenen* Ordner der App eine Komponente (*logindisplay. Razor*) hinzu. <span data-ttu-id="b2482-169">Der [TokenProvider-Dienst](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) stellt das XSRF-Token für das HTML-Formular bereit, das in den Identity Abmelde-Endpunkt von Beiträgen sendet:</span><span class="sxs-lookup"><span data-stu-id="b2482-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="b2482-170">`MainLayout`Fügen Sie die Komponente in der-Komponente (*Shared/MainLayout. Razor*) dem `LoginDisplay` Inhalt des Elements der obersten Zeile hinzu `<div>` :</span><span class="sxs-lookup"><span data-stu-id="b2482-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="b2482-171">Endpunkte für die Stil Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b2482-171">Style authentication endpoints</span></span>

<span data-ttu-id="b2482-172">Da Blazor Server Razor Seiten Identity Seiten verwendet, ändert sich das Formatieren der Benutzeroberfläche, wenn ein Besucher zwischen Identity Seiten und Komponenten navigiert.</span><span class="sxs-lookup"><span data-stu-id="b2482-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="b2482-173">Sie haben zwei Möglichkeiten, die nicht passenden Stile zu berücksichtigen:</span><span class="sxs-lookup"><span data-stu-id="b2482-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="b2482-174">IdentityBuildkomponenten</span><span class="sxs-lookup"><span data-stu-id="b2482-174">Build Identity components</span></span>

<span data-ttu-id="b2482-175">Ein Ansatz zur Verwendung von Komponenten für Identity anstelle von Seiten besteht darin, Komponenten zu erstellen Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="b2482-176">Da `SignInManager` und `UserManager` in-Komponenten nicht unterstützt Razor werden, verwenden Sie API-Endpunkte in der Blazor Server app, um Benutzerkonto Aktionen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b2482-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="b2482-177">Verwenden eines benutzerdefinierten Layouts mit Blazor App-Stilen</span><span class="sxs-lookup"><span data-stu-id="b2482-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="b2482-178">Das Identity Seitenlayout und die Stile können so geändert werden, dass Seiten erzeugt werden, die das Standarddesign verwenden Blazor .</span><span class="sxs-lookup"><span data-stu-id="b2482-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="b2482-179">Das Beispiel in diesem Abschnitt stellt lediglich einen Ausgangspunkt für die Anpassung dar.</span><span class="sxs-lookup"><span data-stu-id="b2482-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="b2482-180">Es ist wahrscheinlich zusätzlicher Aufwand erforderlich, um die bestmögliche Benutzer Leistung zu erzielen.</span><span class="sxs-lookup"><span data-stu-id="b2482-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="b2482-181">Erstellen Sie eine neue `NavMenu_IdentityLayout` Komponente (*Shared/NavMenu_ Identity Layout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="b2482-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="b2482-182">Verwenden Sie für das Markup und den Code der Komponente denselben Inhalt wie die Komponente der APP `NavMenu` (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="b2482-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="b2482-183">Entfernen Sie alle- `NavLink` Komponenten, die nicht anonym erreicht werden können, da die automatische Umleitung in der `RedirectToLogin` Komponente für Komponenten fehlschlägt, die eine Authentifizierung oder Autorisierung erfordern.</span><span class="sxs-lookup"><span data-stu-id="b2482-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="b2482-184">Nehmen Sie in der Datei *pages/Shared/Layout. cshtml* die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="b2482-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="b2482-185">Fügen Sie am Razor Anfang der Datei Direktiven hinzu, um taghilfsprogramme und die Komponenten der APP im frei *gegebenen* Ordner zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="b2482-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="b2482-186">Ersetzen Sie dies `{APPLICATION ASSEMBLY}` durch den Assemblynamen der app.</span><span class="sxs-lookup"><span data-stu-id="b2482-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="b2482-187">Fügen Sie `<base>` dem Inhalt ein Tag und ein Blazor Stylesheet hinzu `<link>` `<head>` :</span><span class="sxs-lookup"><span data-stu-id="b2482-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="b2482-188">Ändern Sie den Inhalt des- `<body>` Tags wie folgt:</span><span class="sxs-lookup"><span data-stu-id="b2482-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="b2482-189">Gerüst Identity in ein Blazor Server Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b2482-190">Einige Identity Optionen sind in " *Bereiche/ Identity / Identity HostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2482-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b2482-191">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b2482-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="b2482-192">Vollständige Identity Benutzeroberflächen Quelle erstellen</span><span class="sxs-lookup"><span data-stu-id="b2482-192">Create full Identity UI source</span></span>

<span data-ttu-id="b2482-193">Um die vollständige Kontrolle über die Identity Benutzeroberfläche zu behalten, führen Sie das Gerüst aus, Identity und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="b2482-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="b2482-194">Der folgende markierte Code zeigt die Änderungen zum Ersetzen der Standard Identity Benutzeroberfläche durch Identity in einer ASP.net Core 2,1-Web-App.</span><span class="sxs-lookup"><span data-stu-id="b2482-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b2482-195">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identity Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="b2482-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b2482-196">Der Standardwert Identity wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="b2482-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b2482-197">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="b2482-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b2482-198">Registrieren Sie eine `IEmailSender` Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="b2482-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="b2482-199">Kenn Wort Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b2482-199">Password configuration</span></span>

<span data-ttu-id="b2482-200">Wenn <xref:Microsoft.AspNetCore.Identity.PasswordOptions> in konfiguriert `Startup.ConfigureServices` ist, ist möglicherweise die- [ `[StringLength]` Attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) Konfiguration für die- `Password` Eigenschaft auf den Seiten mit einem Gerüst erforderlich Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-200">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="b2482-201">`InputModel``Password`die Eigenschaften werden in den folgenden Dateien gefunden:</span><span class="sxs-lookup"><span data-stu-id="b2482-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="b2482-202">Seite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="b2482-202">Disable a page</span></span>

<span data-ttu-id="b2482-203">In diesem Abschnitt wird gezeigt, wie Sie die Registerseite deaktivieren, aber der Ansatz kann verwendet werden, um eine beliebige Seite zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="b2482-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="b2482-204">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="b2482-204">To disable user registration:</span></span>

* <span data-ttu-id="b2482-205">Gerüstbau Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-205">Scaffold Identity.</span></span> <span data-ttu-id="b2482-206">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="b2482-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b2482-207">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b2482-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b2482-208">Update *Areas/ Identity /pages/Account/Register.cshtml.cs* so können sich Benutzer nicht bei diesem Endpunkt registrieren:</span><span class="sxs-lookup"><span data-stu-id="b2482-208">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b2482-209">Aktualisieren Sie *Bereiche/ Identity /pages/Account/Register.cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="b2482-209">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b2482-210">Kommentieren Sie den Registrierungs Link aus *Bereichen/ Identity /pages/Account/Login.cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="b2482-210">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="b2482-211">Aktualisieren Sie die Seite " *Bereiche/ Identity /pages/Account/RegisterConfirmation* ".</span><span class="sxs-lookup"><span data-stu-id="b2482-211">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b2482-212">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="b2482-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b2482-213">Entfernen Sie den Bestätigungscode aus dem `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b2482-213">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b2482-214">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="b2482-214">Use another app to add users</span></span>

<span data-ttu-id="b2482-215">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="b2482-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b2482-216">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="b2482-216">Options to add users include:</span></span>

* <span data-ttu-id="b2482-217">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="b2482-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="b2482-218">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="b2482-218">A console app.</span></span>

<span data-ttu-id="b2482-219">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="b2482-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b2482-220">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="b2482-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="b2482-221">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="b2482-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b2482-222">Der Benutzer wird der-Datenbank hinzugefügt Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-222">The user is added to the Identity database.</span></span>
* <span data-ttu-id="b2482-223">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="b2482-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b2482-224">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="b2482-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b2482-225">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="b2482-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="b2482-226">Veröffentlichen statischer Identity Assets verhindern</span><span class="sxs-lookup"><span data-stu-id="b2482-226">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="b2482-227">Informationen zum Verhindern der Veröffentlichung statischer Identity Assets im Webstamm finden Sie unter <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="b2482-227">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2482-228">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b2482-228">Additional resources</span></span>

* [<span data-ttu-id="b2482-229">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="b2482-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2482-230">ASP.net Core 2,1 und höher stellt [ASP.NET Core Identity](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="b2482-230">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b2482-231">Anwendungen, die einschließen, Identity können das Gerüsten anwenden, um den Quellcode, der in der Identity Razor Klassenbibliothek (RCL) enthalten ist, selektiv hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b2482-231">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="b2482-232">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="b2482-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b2482-233">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="b2482-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b2482-234">Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek Identity.</span><span class="sxs-lookup"><span data-stu-id="b2482-234">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="b2482-235">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.</span><span class="sxs-lookup"><span data-stu-id="b2482-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="b2482-236">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL- Identity Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="b2482-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="b2482-237">Sie können Code der Klassenbibliothek Identity auswählen, der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="b2482-237">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="b2482-238">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="b2482-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="b2482-239">In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines Identity Gerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="b2482-239">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="b2482-240">Wenn das Identity Gerüst ausgeführt wird, wird eine *ScaffoldingReadme.txt* -Datei im Projektverzeichnis erstellt.</span><span class="sxs-lookup"><span data-stu-id="b2482-240">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="b2482-241">Die *ScaffoldingReadme.txt* -Datei enthält allgemeine Anweisungen dazu, was erforderlich ist, um das Identity Gerüstbau Update abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="b2482-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="b2482-242">Dieses Dokument enthält ausführlichere Anweisungen als die *ScaffoldingReadme.txt* Datei.</span><span class="sxs-lookup"><span data-stu-id="b2482-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="b2482-243">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="b2482-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b2482-244">Überprüfen Sie die Änderungen, nachdem Sie das Gerüst ausgeführt haben Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-244">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="b2482-245">Dienste sind erforderlich, wenn [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)und andere Sicherheitsfunktionen mit verwendet werden Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="b2482-246">Dienste oder Service-stubdienste werden beim Gerüstbau nicht generiert Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-246">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="b2482-247">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b2482-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b2482-248">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="b2482-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="b2482-249">Gerüst Identity in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="b2482-249">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b2482-250">Fügen Sie die folgenden markierten Aufrufe der- `Startup` Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="b2482-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="b2482-251">Gerüst Identity in ein Razor Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-251">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b2482-252">Identitywird in *Areas/ Identity / Identity HostingStartup.cs*konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2482-252">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b2482-253">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b2482-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b2482-254">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="b2482-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b2482-255">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="b2482-255">Enable authentication</span></span>

<span data-ttu-id="b2482-256">In der- `Configure` Methode der- `Startup` Klasse wird [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach folgenden Aktionen aufgerufen `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="b2482-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b2482-257">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="b2482-257">Layout changes</span></span>

<span data-ttu-id="b2482-258">Optional: Fügen Sie den Anmelde Namen partiell ( `_LoginPartial` ) der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="b2482-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="b2482-259">Gerüst Identity in ein Razor Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-259">Scaffold Identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b2482-260">Einige Identity Optionen sind in " *Bereiche/ Identity / Identity HostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2482-260">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b2482-261">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b2482-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b2482-262">Gerüst für Identity ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-262">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b2482-263">Optional: Fügen Sie der `_LoginPartial` Datei *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:</span><span class="sxs-lookup"><span data-stu-id="b2482-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="b2482-264">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b2482-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="b2482-265">Identitywird in *Areas/ Identity / Identity HostingStartup.cs*konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b2482-265">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b2482-266">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="b2482-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b2482-267">[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach folgenden Aktionen abrufen `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="b2482-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b2482-268">Gerüst für Identity ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="b2482-268">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b2482-269">Löschen Sie die *Seiten/* den freigegebenen Ordner und die Dateien in diesem Ordner.</span><span class="sxs-lookup"><span data-stu-id="b2482-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="b2482-270">Vollständige Identity Benutzeroberflächen Quelle erstellen</span><span class="sxs-lookup"><span data-stu-id="b2482-270">Create full Identity UI source</span></span>

<span data-ttu-id="b2482-271">Um die vollständige Kontrolle über die Identity Benutzeroberfläche zu behalten, führen Sie das Gerüst aus, Identity und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="b2482-271">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="b2482-272">Der folgende markierte Code zeigt die Änderungen zum Ersetzen der Standard Identity Benutzeroberfläche durch Identity in einer ASP.net Core 2,1-Web-App.</span><span class="sxs-lookup"><span data-stu-id="b2482-272">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b2482-273">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identity Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="b2482-273">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b2482-274">Der Standardwert Identity wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="b2482-274">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b2482-275">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="b2482-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b2482-276">Registrieren Sie eine `IEmailSender` Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="b2482-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="b2482-277">Kenn Wort Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b2482-277">Password configuration</span></span>

<span data-ttu-id="b2482-278">Wenn <xref:Microsoft.AspNetCore.Identity.PasswordOptions> in konfiguriert `Startup.ConfigureServices` ist, ist möglicherweise die- [ `[StringLength]` Attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) Konfiguration für die- `Password` Eigenschaft auf den Seiten mit einem Gerüst erforderlich Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-278">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="b2482-279">`InputModel``Password`die Eigenschaften werden in den folgenden Dateien gefunden:</span><span class="sxs-lookup"><span data-stu-id="b2482-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="b2482-280">Registerseite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="b2482-280">Disable register page</span></span>

<span data-ttu-id="b2482-281">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="b2482-281">To disable user registration:</span></span>

* <span data-ttu-id="b2482-282">Gerüstbau Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-282">Scaffold Identity.</span></span> <span data-ttu-id="b2482-283">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="b2482-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b2482-284">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b2482-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b2482-285">Update *Areas/ Identity /pages/Account/Register.cshtml.cs* so können sich Benutzer nicht bei diesem Endpunkt registrieren:</span><span class="sxs-lookup"><span data-stu-id="b2482-285">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b2482-286">Aktualisieren Sie *Bereiche/ Identity /pages/Account/Register.cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="b2482-286">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b2482-287">Kommentieren Sie den Registrierungs Link aus *Bereichen/ Identity /pages/Account/Login.cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="b2482-287">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="b2482-288">Aktualisieren Sie die Seite " *Bereiche/ Identity /pages/Account/RegisterConfirmation* ".</span><span class="sxs-lookup"><span data-stu-id="b2482-288">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b2482-289">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="b2482-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b2482-290">Entfernen Sie den Bestätigungscode aus dem `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b2482-290">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b2482-291">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="b2482-291">Use another app to add users</span></span>

<span data-ttu-id="b2482-292">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="b2482-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b2482-293">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="b2482-293">Options to add users include:</span></span>

* <span data-ttu-id="b2482-294">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="b2482-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="b2482-295">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="b2482-295">A console app.</span></span>

<span data-ttu-id="b2482-296">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="b2482-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b2482-297">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="b2482-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="b2482-298">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="b2482-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b2482-299">Der Benutzer wird der-Datenbank hinzugefügt Identity .</span><span class="sxs-lookup"><span data-stu-id="b2482-299">The user is added to the Identity database.</span></span>
* <span data-ttu-id="b2482-300">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="b2482-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b2482-301">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="b2482-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b2482-302">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="b2482-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2482-303">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b2482-303">Additional resources</span></span>

* [<span data-ttu-id="b2482-304">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="b2482-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

---
title: Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.net Core-Web-App mit von der Autorisierung geschützten Benutzerdaten erstellen. Umfasst HTTPS, Authentifizierung, Sicherheit, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: 5f86e514ee6339888171d83ab3117e9b3fcf107e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627818"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="78ed9-104">Erstellen einer ASP.net Core-Web-App mit von der Autorisierung geschützten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="78ed9-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="78ed9-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="78ed9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="78ed9-106">[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen</span><span class="sxs-lookup"><span data-stu-id="78ed9-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78ed9-107">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="78ed9-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="78ed9-108">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="78ed9-109">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-109">There are three security groups:</span></span>

* <span data-ttu-id="78ed9-110">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="78ed9-111">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="78ed9-112">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="78ed9-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="78ed9-113">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="78ed9-114">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="78ed9-115">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="78ed9-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="78ed9-116">Rick kann nur genehmigte Kontakte anzeigen und **Edit** / **Löschen**bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="78ed9-117">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="78ed9-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="78ed9-118">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="78ed9-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="78ed9-120">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="78ed9-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="78ed9-122">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="78ed9-122">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="78ed9-124">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="78ed9-125">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="78ed9-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="78ed9-127">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-127">The administrator has all privileges.</span></span> <span data-ttu-id="78ed9-128">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="78ed9-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="78ed9-129">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="78ed9-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="78ed9-130">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="78ed9-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="78ed9-131">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="78ed9-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="78ed9-132">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="78ed9-133">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78ed9-134">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="78ed9-134">Prerequisites</span></span>

<span data-ttu-id="78ed9-135">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="78ed9-135">This tutorial is advanced.</span></span> <span data-ttu-id="78ed9-136">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-136">You should be familiar with:</span></span>

* [<span data-ttu-id="78ed9-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78ed9-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="78ed9-138">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="78ed9-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="78ed9-139">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="78ed9-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="78ed9-140">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="78ed9-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="78ed9-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="78ed9-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="78ed9-142">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="78ed9-142">The starter and completed app</span></span>

<span data-ttu-id="78ed9-143">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="78ed9-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="78ed9-144">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="78ed9-145">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="78ed9-145">The starter app</span></span>

<span data-ttu-id="78ed9-146">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="78ed9-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="78ed9-147">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="78ed9-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="78ed9-148">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="78ed9-148">Secure user data</span></span>

<span data-ttu-id="78ed9-149">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="78ed9-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="78ed9-150">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="78ed9-151">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="78ed9-151">Tie the contact data to the user</span></span>

<span data-ttu-id="78ed9-152">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="78ed9-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="78ed9-153">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="78ed9-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="78ed9-154">`OwnerID` die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="78ed9-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="78ed9-155">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="78ed9-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="78ed9-156">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="78ed9-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="78ed9-157">Rollen Dienste hinzufügen zu Identity</span><span class="sxs-lookup"><span data-stu-id="78ed9-157">Add Role services to Identity</span></span>

<span data-ttu-id="78ed9-158">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="78ed9-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="78ed9-159">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="78ed9-159">Require authenticated users</span></span>

<span data-ttu-id="78ed9-160">Legen Sie die Fall Back Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="78ed9-161">Der obige markierte Code legt die [Fall Back Authentifizierungs Richtlinie](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)fest.</span><span class="sxs-lookup"><span data-stu-id="78ed9-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="78ed9-162">Die Fall Back Authentifizierungs Richtlinie erfordert, dass ***alle*** Benutzer authentifiziert werden, mit Ausnahme der Razor Seiten, Controller oder Aktionsmethoden mit einem Authentifizierungs Attribut.</span><span class="sxs-lookup"><span data-stu-id="78ed9-162">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="78ed9-163">Beispielsweise werden Razor Seiten, Controller oder Aktionsmethoden mit `[AllowAnonymous]` oder `[Authorize(PolicyName="MyPolicy")]` das angewendete Authentifizierungs Attribut anstelle der Fall Back Authentifizierungs Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="78ed9-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="78ed9-164">Die Richtlinie für die Fall Back Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="78ed9-164">The fallback authentication policy:</span></span>

* <span data-ttu-id="78ed9-165">Wird auf alle Anforderungen angewendet, die nicht explizit eine Authentifizierungs Richtlinie angeben.</span><span class="sxs-lookup"><span data-stu-id="78ed9-165">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="78ed9-166">Bei Anforderungen, die von der Endpunkt Weiterleitung verarbeitet werden, würde dies alle Endpunkte einschließen, für die kein Autorisierungs Attribut angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="78ed9-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="78ed9-167">Bei Anforderungen, die von einer anderen Middleware nach der Autorisierungs Middleware, wie z. b. [statischen Dateien](xref:fundamentals/static-files), verarbeitet werden, wird die Richtlinie auf alle Anforderungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="78ed9-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="78ed9-168">Das Festlegen der Fall Back Authentifizierungs Richtlinie, damit Benutzer authentifiziert werden müssen, schützt neu hinzugefügte Razor Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="78ed9-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="78ed9-169">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="78ed9-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="78ed9-170">Die- <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> Klasse enthält ebenfalls <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="78ed9-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="78ed9-171">`DefaultPolicy`Ist die Richtlinie, die mit dem-Attribut verwendet wird, `[Authorize]` Wenn keine Richtlinie angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="78ed9-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="78ed9-172">`[Authorize]` enthält im Gegensatz zu keine benannte Richtlinie `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="78ed9-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="78ed9-173">Weitere Informationen zu Richtlinien finden Sie unter <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="78ed9-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="78ed9-174">Eine alternative Möglichkeit für MVC-Controller und- Razor Seiten, dass alle Benutzer authentifiziert werden müssen, besteht darin, einen Autorisierungs Filter hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="78ed9-175">Im vorangehenden Code wird ein Autorisierungs Filter verwendet, bei dem die Fall Back Richtlinie das Endpunkt Routing verwendet.</span><span class="sxs-lookup"><span data-stu-id="78ed9-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="78ed9-176">Das Festlegen der Fall Back Richtlinie ist die bevorzugte Methode, um alle Benutzer zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="78ed9-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="78ed9-177">Fügen Sie den Seiten und die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) hinzu, `Index` `Privacy` damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können:</span><span class="sxs-lookup"><span data-stu-id="78ed9-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="78ed9-178">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="78ed9-178">Configure the test account</span></span>

<span data-ttu-id="78ed9-179">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="78ed9-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="78ed9-180">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="78ed9-181">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="78ed9-181">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="78ed9-182">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="78ed9-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="78ed9-183">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="78ed9-184">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="78ed9-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="78ed9-185">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="78ed9-186">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="78ed9-187">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="78ed9-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="78ed9-188">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="78ed9-189">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="78ed9-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="78ed9-190">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="78ed9-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="78ed9-191">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="78ed9-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="78ed9-192">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="78ed9-193">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="78ed9-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="78ed9-194">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="78ed9-195">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="78ed9-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="78ed9-196">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="78ed9-197">`Task.CompletedTask` ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="78ed9-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="78ed9-198">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="78ed9-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="78ed9-199">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="78ed9-200">`ContactIsOwnerAuthorizationHandler` der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="78ed9-201">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="78ed9-201">Create a manager authorization handler</span></span>

<span data-ttu-id="78ed9-202">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="78ed9-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="78ed9-203">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="78ed9-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="78ed9-204">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="78ed9-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="78ed9-205">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="78ed9-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="78ed9-206">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="78ed9-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="78ed9-207">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="78ed9-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="78ed9-208">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="78ed9-209">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="78ed9-209">Register the authorization handlers</span></span>

<span data-ttu-id="78ed9-210">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="78ed9-211">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="78ed9-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="78ed9-212">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="78ed9-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="78ed9-213">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78ed9-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="78ed9-214">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="78ed9-215">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="78ed9-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="78ed9-216">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="78ed9-216">Support authorization</span></span>

<span data-ttu-id="78ed9-217">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="78ed9-218">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="78ed9-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="78ed9-219">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="78ed9-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="78ed9-220">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="78ed9-221">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="78ed9-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="78ed9-222">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="78ed9-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="78ed9-223">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="78ed9-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="78ed9-224">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="78ed9-224">The preceding code:</span></span>

* <span data-ttu-id="78ed9-225">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="78ed9-226">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="78ed9-227">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="78ed9-228">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="78ed9-228">Update the CreateModel</span></span>

<span data-ttu-id="78ed9-229">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="78ed9-230">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="78ed9-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="78ed9-231">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="78ed9-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="78ed9-232">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="78ed9-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="78ed9-233">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="78ed9-233">Update the IndexModel</span></span>

<span data-ttu-id="78ed9-234">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="78ed9-235">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="78ed9-235">Update the EditModel</span></span>

<span data-ttu-id="78ed9-236">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="78ed9-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="78ed9-237">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="78ed9-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="78ed9-238">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="78ed9-239">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="78ed9-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="78ed9-240">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="78ed9-241">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="78ed9-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="78ed9-242">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="78ed9-242">Update the DeleteModel</span></span>

<span data-ttu-id="78ed9-243">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="78ed9-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="78ed9-244">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="78ed9-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="78ed9-245">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="78ed9-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="78ed9-246">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="78ed9-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="78ed9-247">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="78ed9-248">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="78ed9-249">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="78ed9-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="78ed9-250">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="78ed9-251">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="78ed9-252">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="78ed9-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="78ed9-253">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="78ed9-253">Update Details</span></span>

<span data-ttu-id="78ed9-254">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="78ed9-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="78ed9-255">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="78ed9-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="78ed9-256">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="78ed9-256">Add or remove a user to a role</span></span>

<span data-ttu-id="78ed9-257">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="78ed9-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="78ed9-258">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="78ed9-258">Removing privileges from a user.</span></span> <span data-ttu-id="78ed9-259">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="78ed9-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="78ed9-260">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="78ed9-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="78ed9-261">Unterschiede zwischen Challenge und verboten</span><span class="sxs-lookup"><span data-stu-id="78ed9-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="78ed9-262">Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#rau)sind.</span><span class="sxs-lookup"><span data-stu-id="78ed9-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="78ed9-263">Der folgende Code ermöglicht anonyme Benutzer.</span><span class="sxs-lookup"><span data-stu-id="78ed9-263">The following code allows anonymous users.</span></span> <span data-ttu-id="78ed9-264">Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="78ed9-265">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="78ed9-265">In the preceding code:</span></span>

* <span data-ttu-id="78ed9-266">Wenn der Benutzer **nicht** authentifiziert ist, `ChallengeResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78ed9-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="78ed9-267">Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="78ed9-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="78ed9-268">Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, `ForbidResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78ed9-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="78ed9-269">Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite Zugriff verweigert umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="78ed9-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="78ed9-270">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="78ed9-270">Test the completed app</span></span>

<span data-ttu-id="78ed9-271">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="78ed9-272">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="78ed9-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="78ed9-273">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="78ed9-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="78ed9-274">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="78ed9-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="78ed9-275">Wenn die APP Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="78ed9-275">If the app has contacts:</span></span>

* <span data-ttu-id="78ed9-276">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="78ed9-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="78ed9-277">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="78ed9-278">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="78ed9-279">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="78ed9-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="78ed9-280">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="78ed9-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="78ed9-281">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="78ed9-281">Verify the following operations:</span></span>

* <span data-ttu-id="78ed9-282">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="78ed9-283">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="78ed9-284">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="78ed9-285">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="78ed9-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="78ed9-286">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="78ed9-287">Benutzer</span><span class="sxs-lookup"><span data-stu-id="78ed9-287">User</span></span>                | <span data-ttu-id="78ed9-288">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="78ed9-288">Seeded by the app</span></span> | <span data-ttu-id="78ed9-289">Optionen</span><span class="sxs-lookup"><span data-stu-id="78ed9-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="78ed9-290">Nein</span><span class="sxs-lookup"><span data-stu-id="78ed9-290">No</span></span>                | <span data-ttu-id="78ed9-291">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="78ed9-292">Ja</span><span class="sxs-lookup"><span data-stu-id="78ed9-292">Yes</span></span>               | <span data-ttu-id="78ed9-293">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="78ed9-294">Ja</span><span class="sxs-lookup"><span data-stu-id="78ed9-294">Yes</span></span>               | <span data-ttu-id="78ed9-295">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="78ed9-296">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="78ed9-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="78ed9-297">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="78ed9-298">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="78ed9-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="78ed9-299">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="78ed9-299">Create the starter app</span></span>

* <span data-ttu-id="78ed9-300">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="78ed9-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="78ed9-301">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="78ed9-301">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="78ed9-302">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="78ed9-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="78ed9-303">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="78ed9-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="78ed9-304">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-304">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="78ed9-305">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="78ed9-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="78ed9-306">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="78ed9-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="78ed9-307">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="78ed9-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="78ed9-308">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="78ed9-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="78ed9-309">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="78ed9-310">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="78ed9-310">Seed the database</span></span>

<span data-ttu-id="78ed9-311">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="78ed9-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="78ed9-312">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="78ed9-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="78ed9-313">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="78ed9-313">Test that the app seeded the database.</span></span> <span data-ttu-id="78ed9-314">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="78ed9-315">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="78ed9-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="78ed9-316">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="78ed9-317">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-317">There are three security groups:</span></span>

* <span data-ttu-id="78ed9-318">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="78ed9-319">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="78ed9-320">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="78ed9-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="78ed9-321">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="78ed9-322">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="78ed9-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="78ed9-323">Rick kann nur genehmigte Kontakte anzeigen und **Edit** / **Löschen**bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="78ed9-324">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="78ed9-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="78ed9-325">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="78ed9-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="78ed9-327">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="78ed9-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="78ed9-329">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="78ed9-329">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="78ed9-331">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="78ed9-332">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="78ed9-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="78ed9-334">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-334">The administrator has all privileges.</span></span> <span data-ttu-id="78ed9-335">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="78ed9-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="78ed9-336">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="78ed9-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="78ed9-337">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="78ed9-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="78ed9-338">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="78ed9-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="78ed9-339">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="78ed9-340">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78ed9-341">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="78ed9-341">Prerequisites</span></span>

<span data-ttu-id="78ed9-342">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="78ed9-342">This tutorial is advanced.</span></span> <span data-ttu-id="78ed9-343">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-343">You should be familiar with:</span></span>

* [<span data-ttu-id="78ed9-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78ed9-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="78ed9-345">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="78ed9-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="78ed9-346">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="78ed9-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="78ed9-347">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="78ed9-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="78ed9-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="78ed9-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="78ed9-349">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="78ed9-349">The starter and completed app</span></span>

<span data-ttu-id="78ed9-350">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="78ed9-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="78ed9-351">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="78ed9-352">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="78ed9-352">The starter app</span></span>

<span data-ttu-id="78ed9-353">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="78ed9-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="78ed9-354">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="78ed9-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="78ed9-355">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="78ed9-355">Secure user data</span></span>

<span data-ttu-id="78ed9-356">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="78ed9-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="78ed9-357">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="78ed9-358">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="78ed9-358">Tie the contact data to the user</span></span>

<span data-ttu-id="78ed9-359">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="78ed9-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="78ed9-360">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="78ed9-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="78ed9-361">`OwnerID` die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="78ed9-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="78ed9-362">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="78ed9-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="78ed9-363">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="78ed9-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="78ed9-364">Rollen Dienste hinzufügen zu Identity</span><span class="sxs-lookup"><span data-stu-id="78ed9-364">Add Role services to Identity</span></span>

<span data-ttu-id="78ed9-365">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="78ed9-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="78ed9-366">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="78ed9-366">Require authenticated users</span></span>

<span data-ttu-id="78ed9-367">Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="78ed9-368">Sie können die Authentifizierung auf Razor Seiten-, Controller-oder Aktionsmethoden Ebene mit dem- `[AllowAnonymous]` Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="78ed9-369">Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Seiten und Controller geschützt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="78ed9-370">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="78ed9-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="78ed9-371">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="78ed9-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="78ed9-372">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="78ed9-372">Configure the test account</span></span>

<span data-ttu-id="78ed9-373">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="78ed9-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="78ed9-374">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="78ed9-375">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="78ed9-375">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="78ed9-376">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="78ed9-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="78ed9-377">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="78ed9-378">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="78ed9-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="78ed9-379">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="78ed9-380">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="78ed9-381">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="78ed9-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="78ed9-382">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="78ed9-383">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="78ed9-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="78ed9-384">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="78ed9-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="78ed9-385">Erstellen Sie einen *Autorisierungs* Ordner, und erstellen `ContactIsOwnerAuthorizationHandler` Sie eine Klasse darin.</span><span class="sxs-lookup"><span data-stu-id="78ed9-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="78ed9-386">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="78ed9-387">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="78ed9-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="78ed9-388">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="78ed9-389">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="78ed9-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="78ed9-390">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="78ed9-391">`Task.CompletedTask` ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="78ed9-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="78ed9-392">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="78ed9-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="78ed9-393">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="78ed9-394">`ContactIsOwnerAuthorizationHandler` der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="78ed9-395">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="78ed9-395">Create a manager authorization handler</span></span>

<span data-ttu-id="78ed9-396">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="78ed9-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="78ed9-397">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="78ed9-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="78ed9-398">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="78ed9-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="78ed9-399">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="78ed9-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="78ed9-400">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="78ed9-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="78ed9-401">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="78ed9-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="78ed9-402">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="78ed9-403">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="78ed9-403">Register the authorization handlers</span></span>

<span data-ttu-id="78ed9-404">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="78ed9-405">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="78ed9-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="78ed9-406">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="78ed9-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="78ed9-407">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78ed9-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="78ed9-408">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="78ed9-409">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="78ed9-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="78ed9-410">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="78ed9-410">Support authorization</span></span>

<span data-ttu-id="78ed9-411">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="78ed9-412">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="78ed9-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="78ed9-413">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="78ed9-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="78ed9-414">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="78ed9-415">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="78ed9-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="78ed9-416">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="78ed9-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="78ed9-417">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="78ed9-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="78ed9-418">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="78ed9-418">The preceding code:</span></span>

* <span data-ttu-id="78ed9-419">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="78ed9-420">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="78ed9-421">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="78ed9-422">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="78ed9-422">Update the CreateModel</span></span>

<span data-ttu-id="78ed9-423">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="78ed9-424">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="78ed9-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="78ed9-425">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="78ed9-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="78ed9-426">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="78ed9-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="78ed9-427">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="78ed9-427">Update the IndexModel</span></span>

<span data-ttu-id="78ed9-428">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="78ed9-429">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="78ed9-429">Update the EditModel</span></span>

<span data-ttu-id="78ed9-430">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="78ed9-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="78ed9-431">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="78ed9-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="78ed9-432">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="78ed9-433">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="78ed9-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="78ed9-434">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="78ed9-435">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="78ed9-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="78ed9-436">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="78ed9-436">Update the DeleteModel</span></span>

<span data-ttu-id="78ed9-437">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="78ed9-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="78ed9-438">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="78ed9-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="78ed9-439">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="78ed9-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="78ed9-440">Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="78ed9-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="78ed9-441">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="78ed9-442">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="78ed9-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="78ed9-443">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="78ed9-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="78ed9-444">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="78ed9-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="78ed9-445">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="78ed9-446">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="78ed9-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="78ed9-447">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="78ed9-447">Update Details</span></span>

<span data-ttu-id="78ed9-448">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="78ed9-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="78ed9-449">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="78ed9-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="78ed9-450">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="78ed9-450">Add or remove a user to a role</span></span>

<span data-ttu-id="78ed9-451">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="78ed9-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="78ed9-452">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="78ed9-452">Removing privileges from a user.</span></span> <span data-ttu-id="78ed9-453">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="78ed9-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="78ed9-454">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="78ed9-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="78ed9-455">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="78ed9-455">Test the completed app</span></span>

<span data-ttu-id="78ed9-456">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="78ed9-457">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="78ed9-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="78ed9-458">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="78ed9-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="78ed9-459">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="78ed9-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="78ed9-460">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="78ed9-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="78ed9-461">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="78ed9-462">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="78ed9-463">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="78ed9-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="78ed9-464">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="78ed9-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="78ed9-465">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="78ed9-465">Verify the following operations:</span></span>

* <span data-ttu-id="78ed9-466">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="78ed9-467">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="78ed9-468">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="78ed9-469">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="78ed9-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="78ed9-470">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="78ed9-471">Benutzer</span><span class="sxs-lookup"><span data-stu-id="78ed9-471">User</span></span>                | <span data-ttu-id="78ed9-472">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="78ed9-472">Seeded by the app</span></span> | <span data-ttu-id="78ed9-473">Optionen</span><span class="sxs-lookup"><span data-stu-id="78ed9-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="78ed9-474">Nein</span><span class="sxs-lookup"><span data-stu-id="78ed9-474">No</span></span>                | <span data-ttu-id="78ed9-475">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="78ed9-476">Ja</span><span class="sxs-lookup"><span data-stu-id="78ed9-476">Yes</span></span>               | <span data-ttu-id="78ed9-477">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="78ed9-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="78ed9-478">Ja</span><span class="sxs-lookup"><span data-stu-id="78ed9-478">Yes</span></span>               | <span data-ttu-id="78ed9-479">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="78ed9-480">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="78ed9-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="78ed9-481">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="78ed9-482">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="78ed9-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="78ed9-483">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="78ed9-483">Create the starter app</span></span>

* <span data-ttu-id="78ed9-484">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="78ed9-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="78ed9-485">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="78ed9-485">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="78ed9-486">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="78ed9-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="78ed9-487">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="78ed9-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="78ed9-488">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="78ed9-488">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="78ed9-489">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="78ed9-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="78ed9-490">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="78ed9-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="78ed9-491">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="78ed9-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="78ed9-492">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="78ed9-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="78ed9-493">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="78ed9-493">Seed the database</span></span>

<span data-ttu-id="78ed9-494">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ed9-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="78ed9-495">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="78ed9-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="78ed9-496">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="78ed9-496">Test that the app seeded the database.</span></span> <span data-ttu-id="78ed9-497">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="78ed9-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="78ed9-498">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="78ed9-498">Additional resources</span></span>

* [<span data-ttu-id="78ed9-499">Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="78ed9-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="78ed9-500">[ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="78ed9-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="78ed9-501">In dieser Übungseinheit werden die in diesem Tutorial vorgestellten Sicherheitsfeatures ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="78ed9-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="78ed9-502">Benutzerdefinierte Richtlinien basierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="78ed9-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

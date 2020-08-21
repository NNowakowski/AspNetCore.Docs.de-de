---
title: Hochladen von Dateien in ASP.NET Core
author: rick-anderson
description: Verwenden von Modellbindung und Streaming zum Hochladen von Dateien in ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 6ff78b26e8e2363cf6c54ebb2a392f390fb2995c
ms.sourcegitcommit: cd412a44f26cb416ceb348fc0a1ccc9a6e9ca73e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720278"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="f97eb-103">Hochladen von Dateien in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f97eb-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="f97eb-104">Von [Steve Smith](https://ardalis.com/) und [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="f97eb-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f97eb-105">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f97eb-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f97eb-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f97eb-107">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-107">Security considerations</span></span>

<span data-ttu-id="f97eb-108">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f97eb-109">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f97eb-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="f97eb-110">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="f97eb-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f97eb-111">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="f97eb-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="f97eb-112">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="f97eb-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f97eb-113">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="f97eb-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f97eb-114">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="f97eb-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="f97eb-115">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f97eb-116">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f97eb-117">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f97eb-118">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f97eb-119">Verwenden Sie keinen Dateinamen, der vom Benutzer bereitgestellt wird, oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei. &dagger; Der HTML-Code codiert den nicht vertrauenswürdigen Dateinamen, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="f97eb-120">Beispielsweise die Protokollierung des Datei namens oder die Anzeige auf der Benutzeroberfläche ( Razor Automatisches HTML-codieren der Ausgabe).</span><span class="sxs-lookup"><span data-stu-id="f97eb-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="f97eb-121">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="f97eb-122">Überprüfen Sie, ob Client seitige Überprüfungen auf dem Server ausgeführt werden. &dagger; Client seitige Überprüfungen können problemlos umgangen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f97eb-123">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="f97eb-124">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="f97eb-125">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f97eb-126">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f97eb-127">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-128">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f97eb-129">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="f97eb-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f97eb-130">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="f97eb-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f97eb-131">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="f97eb-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="f97eb-132">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="f97eb-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f97eb-133">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="f97eb-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f97eb-134">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="f97eb-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="f97eb-135">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen gelten, wenn Dateien vom Benutzer akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="f97eb-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f97eb-136">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="f97eb-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f97eb-137">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="f97eb-137">Storage scenarios</span></span>

<span data-ttu-id="f97eb-138">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="f97eb-138">Common storage options for files include:</span></span>

* <span data-ttu-id="f97eb-139">Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-139">Database</span></span>

  * <span data-ttu-id="f97eb-140">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="f97eb-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f97eb-141">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="f97eb-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f97eb-142">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="f97eb-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f97eb-143">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="f97eb-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f97eb-144">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="f97eb-144">For large file uploads:</span></span>
    * <span data-ttu-id="f97eb-145">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="f97eb-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f97eb-146">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f97eb-147">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="f97eb-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f97eb-148">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f97eb-149">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f97eb-150">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="f97eb-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f97eb-151">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f97eb-152">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="f97eb-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f97eb-153">Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .net zum Erstellen eines BLOBs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="f97eb-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f97eb-154">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="f97eb-154">File upload scenarios</span></span>

<span data-ttu-id="f97eb-155">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="f97eb-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f97eb-156">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="f97eb-156">**Buffering**</span></span>

<span data-ttu-id="f97eb-157">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f97eb-158">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="f97eb-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f97eb-159">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="f97eb-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f97eb-160">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="f97eb-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f97eb-161">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f97eb-162">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f97eb-163">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="f97eb-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f97eb-164">Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f97eb-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="f97eb-165">**Streaming**</span></span>

<span data-ttu-id="f97eb-166">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f97eb-167">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="f97eb-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f97eb-168">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="f97eb-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f97eb-169">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f97eb-170">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="f97eb-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f97eb-171">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f97eb-172">Das folgende Beispiel veranschaulicht die Verwendung eines Razor pages-Formulars zum Hochladen einer einzelnen Datei (*pages/bufferedsinglefileuploadphysical. cshtml* in der Beispiel-APP):</span><span class="sxs-lookup"><span data-stu-id="f97eb-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="f97eb-173">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="f97eb-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f97eb-174">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f97eb-175">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="f97eb-176">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="f97eb-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f97eb-177">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="f97eb-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f97eb-178">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f97eb-179">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="f97eb-180">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f97eb-181">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f97eb-182">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f97eb-183">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="f97eb-184">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="f97eb-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="f97eb-185">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="f97eb-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="f97eb-186">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="f97eb-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="f97eb-187">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-187">Applications should:</span></span>
>
> * <span data-ttu-id="f97eb-188">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="f97eb-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="f97eb-189">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="f97eb-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="f97eb-190">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="f97eb-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="f97eb-191">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="f97eb-192">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f97eb-193">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f97eb-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f97eb-194">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f97eb-195">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="f97eb-195">Validation</span></span>](#validation)

<span data-ttu-id="f97eb-196">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="f97eb-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f97eb-197">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f97eb-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f97eb-198">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f97eb-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f97eb-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f97eb-200">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-200">Binding matches form files by name.</span></span> <span data-ttu-id="f97eb-201">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="f97eb-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f97eb-202">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="f97eb-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f97eb-203">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f97eb-203">The following example:</span></span>

* <span data-ttu-id="f97eb-204">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f97eb-205">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f97eb-206">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f97eb-207">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="f97eb-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="f97eb-208">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="f97eb-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f97eb-209">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="f97eb-210">Der an  übergebene Pfad <xref:System.IO.FileStream> *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="f97eb-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f97eb-211">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="f97eb-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f97eb-212">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f97eb-213">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f97eb-214">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="f97eb-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f97eb-215">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="f97eb-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f97eb-217">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="f97eb-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f97eb-218">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f97eb-219">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="f97eb-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f97eb-220">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f97eb-221">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="f97eb-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f97eb-222">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="f97eb-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="f97eb-223"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f97eb-224">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f97eb-225">Die `FileUpload` wird im Seiten Format verwendet Razor :</span><span class="sxs-lookup"><span data-stu-id="f97eb-225">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="f97eb-226">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f97eb-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f97eb-227">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="f97eb-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="f97eb-228">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="f97eb-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f97eb-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f97eb-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f97eb-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f97eb-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-231">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="f97eb-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f97eb-232">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="f97eb-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f97eb-233">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f97eb-234">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f97eb-235">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f97eb-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f97eb-236">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f97eb-237">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="f97eb-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f97eb-238">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="f97eb-238">Upload large files with streaming</span></span>

<span data-ttu-id="f97eb-239">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f97eb-240">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f97eb-241">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f97eb-242">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f97eb-243">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="f97eb-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f97eb-244">Die erste Seiten Antwort lädt das Formular und speichert ein antifälschungstoken in einem cookie (über das- `GenerateAntiforgeryTokenCookieAttribute` Attribut).</span><span class="sxs-lookup"><span data-stu-id="f97eb-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f97eb-245">Das-Attribut verwendet die integrierte [antifälschungs Unterstützung](xref:security/anti-request-forgery) von ASP.net Core, um eine cookie mit einem Anforderungs Token festzulegen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f97eb-246">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f97eb-247">In der Beispiel-APP `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` werden und als Filter auf die Seiten Anwendungsmodelle von `/StreamedSingleFileUploadDb` und in der Verwendung von `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Seiten Konventionen](xref:razor-pages/razor-pages-conventions)angewendet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="f97eb-248">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="f97eb-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f97eb-249">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f97eb-250">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f97eb-251">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f97eb-252">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f97eb-253">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="f97eb-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f97eb-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f97eb-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f97eb-255">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="f97eb-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f97eb-256">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f97eb-257">Überprüfen</span><span class="sxs-lookup"><span data-stu-id="f97eb-257">Validation</span></span>

<span data-ttu-id="f97eb-258">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="f97eb-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f97eb-259">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="f97eb-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f97eb-260">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-261">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f97eb-262">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f97eb-263">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="f97eb-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f97eb-264">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="f97eb-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f97eb-265">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="f97eb-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f97eb-266">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f97eb-267">Validierung von Inhalten</span><span class="sxs-lookup"><span data-stu-id="f97eb-267">Content validation</span></span>

<span data-ttu-id="f97eb-268">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f97eb-269">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f97eb-270">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f97eb-271">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="f97eb-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f97eb-272">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f97eb-273">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f97eb-274">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f97eb-275">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-275">File extension validation</span></span>

<span data-ttu-id="f97eb-276">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f97eb-277">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f97eb-278">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="f97eb-278">File signature validation</span></span>

<span data-ttu-id="f97eb-279">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f97eb-280">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="f97eb-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f97eb-281">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f97eb-282">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="f97eb-283">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f97eb-284">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="f97eb-284">File name security</span></span>

<span data-ttu-id="f97eb-285">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f97eb-286">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f97eb-287">Razor automatisch HTML-codiert Eigenschaftswerte für die Anzeige.</span><span class="sxs-lookup"><span data-stu-id="f97eb-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f97eb-288">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="f97eb-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f97eb-289">Außerhalb von Razor ist immer der <xref:System.Net.WebUtility.HtmlEncode*> Name Inhalt aus der Anforderung eines Benutzers.</span><span class="sxs-lookup"><span data-stu-id="f97eb-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f97eb-290">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f97eb-291">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f97eb-292">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="f97eb-292">Size validation</span></span>

<span data-ttu-id="f97eb-293">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f97eb-294">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="f97eb-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f97eb-295">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="f97eb-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f97eb-296">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="f97eb-297">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f97eb-298">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="f97eb-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f97eb-299">In nicht- Razor Formularen, die Formulardaten veröffentlichen oder direkt JavaScript verwenden `FormData` , muss der im-Element des Formulars angegebene Name dem `FormData` Namen des Parameters in der Aktion des Controllers entsprechen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f97eb-300">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-300">In the following example:</span></span>

* <span data-ttu-id="f97eb-301">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f97eb-302">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f97eb-303">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="f97eb-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f97eb-304">Für eine Razor Seiten Handler-Methode mit dem Namen `Upload` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f97eb-305">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="f97eb-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f97eb-306">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="f97eb-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f97eb-307">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="f97eb-307">Multipart body length limit</span></span>

<span data-ttu-id="f97eb-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="f97eb-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f97eb-309">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="f97eb-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f97eb-310">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="f97eb-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f97eb-311">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="f97eb-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="f97eb-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f97eb-313">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="f97eb-314">RazorWenden Sie in einer Pages-APP oder einer MVC-App den Filter auf das Seiten Modell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f97eb-315">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="f97eb-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="f97eb-316">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f97eb-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f97eb-317">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="f97eb-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="f97eb-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f97eb-319">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="f97eb-320">RazorWenden Sie den Filter in einer Pages-APP oder einer MVC-App auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="f97eb-321">`RequestSizeLimitAttribute`Kann auch mit der- [`@attribute`](xref:mvc/views/razor#attribute) Razor Direktive angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="f97eb-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f97eb-322">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="f97eb-322">Other Kestrel limits</span></span>

<span data-ttu-id="f97eb-323">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="f97eb-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f97eb-324">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f97eb-325">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="f97eb-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="f97eb-326">IIS</span><span class="sxs-lookup"><span data-stu-id="f97eb-326">IIS</span></span>

<span data-ttu-id="f97eb-327">Das Standard Anforderungs Limit ( `maxAllowedContentLength` ) beträgt 30 Millionen bytes, was etwa 28,6 MB beträgt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f97eb-328">Passen Sie den Grenzwert in der `web.config` Datei an.</span><span class="sxs-lookup"><span data-stu-id="f97eb-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="f97eb-329">Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="f97eb-330">Die `maxAllowedContentLength` Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="f97eb-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="f97eb-331">Weitere Informationen finden Sie unter [Anforderungs Limits `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="f97eb-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f97eb-332">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="f97eb-332">Troubleshoot</span></span>

<span data-ttu-id="f97eb-333">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="f97eb-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f97eb-334">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="f97eb-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f97eb-335">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f97eb-336">Weitere Informationen finden Sie im Abschnitt [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="f97eb-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f97eb-337">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="f97eb-337">Connection failure</span></span>

<span data-ttu-id="f97eb-338">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f97eb-339">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="f97eb-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f97eb-340">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f97eb-341">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="f97eb-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f97eb-342">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f97eb-343">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f97eb-344">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="f97eb-345">Stream war zu lang.</span><span class="sxs-lookup"><span data-stu-id="f97eb-345">Stream was too long</span></span>

<span data-ttu-id="f97eb-346">Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="f97eb-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="f97eb-347">Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="f97eb-348">Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="f97eb-349">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f97eb-350">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f97eb-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f97eb-351">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-351">Security considerations</span></span>

<span data-ttu-id="f97eb-352">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f97eb-353">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f97eb-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="f97eb-354">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="f97eb-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f97eb-355">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="f97eb-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="f97eb-356">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="f97eb-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f97eb-357">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="f97eb-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f97eb-358">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="f97eb-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="f97eb-359">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f97eb-360">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f97eb-361">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f97eb-362">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f97eb-363">Verwenden Sie keinen Dateinamen, der vom Benutzer bereitgestellt wird, oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei. &dagger; Der HTML-Code codiert den nicht vertrauenswürdigen Dateinamen, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="f97eb-364">Beispielsweise die Protokollierung des Datei namens oder die Anzeige auf der Benutzeroberfläche ( Razor Automatisches HTML-codieren der Ausgabe).</span><span class="sxs-lookup"><span data-stu-id="f97eb-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="f97eb-365">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="f97eb-366">Überprüfen Sie, ob Client seitige Überprüfungen auf dem Server ausgeführt werden. &dagger; Client seitige Überprüfungen können problemlos umgangen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f97eb-367">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="f97eb-368">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="f97eb-369">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f97eb-370">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f97eb-371">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-372">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f97eb-373">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="f97eb-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f97eb-374">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="f97eb-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f97eb-375">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="f97eb-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="f97eb-376">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="f97eb-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f97eb-377">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="f97eb-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f97eb-378">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="f97eb-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="f97eb-379">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen gelten, wenn Dateien vom Benutzer akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="f97eb-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f97eb-380">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="f97eb-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f97eb-381">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="f97eb-381">Storage scenarios</span></span>

<span data-ttu-id="f97eb-382">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="f97eb-382">Common storage options for files include:</span></span>

* <span data-ttu-id="f97eb-383">Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-383">Database</span></span>

  * <span data-ttu-id="f97eb-384">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="f97eb-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f97eb-385">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="f97eb-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f97eb-386">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="f97eb-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f97eb-387">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="f97eb-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f97eb-388">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="f97eb-388">For large file uploads:</span></span>
    * <span data-ttu-id="f97eb-389">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="f97eb-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f97eb-390">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f97eb-391">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="f97eb-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f97eb-392">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f97eb-393">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f97eb-394">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="f97eb-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f97eb-395">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f97eb-396">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="f97eb-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f97eb-397">Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .net zum Erstellen eines BLOBs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="f97eb-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f97eb-398">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="f97eb-398">File upload scenarios</span></span>

<span data-ttu-id="f97eb-399">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="f97eb-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f97eb-400">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="f97eb-400">**Buffering**</span></span>

<span data-ttu-id="f97eb-401">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f97eb-402">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="f97eb-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f97eb-403">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="f97eb-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f97eb-404">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="f97eb-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f97eb-405">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f97eb-406">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f97eb-407">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="f97eb-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f97eb-408">Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f97eb-409">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="f97eb-409">**Streaming**</span></span>

<span data-ttu-id="f97eb-410">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f97eb-411">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="f97eb-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f97eb-412">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="f97eb-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f97eb-413">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f97eb-414">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="f97eb-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f97eb-415">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f97eb-416">Das folgende Beispiel veranschaulicht die Verwendung eines Razor pages-Formulars zum Hochladen einer einzelnen Datei (*pages/bufferedsinglefileuploadphysical. cshtml* in der Beispiel-APP):</span><span class="sxs-lookup"><span data-stu-id="f97eb-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="f97eb-417">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="f97eb-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f97eb-418">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f97eb-419">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-419">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="f97eb-420">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="f97eb-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f97eb-421">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="f97eb-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f97eb-422">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f97eb-423">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-423">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="f97eb-424">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f97eb-425">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f97eb-426">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f97eb-427">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="f97eb-428">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="f97eb-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="f97eb-429">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="f97eb-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="f97eb-430">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="f97eb-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="f97eb-431">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-431">Applications should:</span></span>
>
> * <span data-ttu-id="f97eb-432">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="f97eb-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="f97eb-433">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="f97eb-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="f97eb-434">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="f97eb-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="f97eb-435">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="f97eb-436">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f97eb-437">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f97eb-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f97eb-438">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f97eb-439">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="f97eb-439">Validation</span></span>](#validation)

<span data-ttu-id="f97eb-440">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="f97eb-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f97eb-441">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f97eb-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f97eb-442">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f97eb-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f97eb-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f97eb-444">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-444">Binding matches form files by name.</span></span> <span data-ttu-id="f97eb-445">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="f97eb-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f97eb-446">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="f97eb-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f97eb-447">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f97eb-447">The following example:</span></span>

* <span data-ttu-id="f97eb-448">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f97eb-449">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f97eb-450">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f97eb-451">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="f97eb-451">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="f97eb-452">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="f97eb-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f97eb-453">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-453">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="f97eb-454">Der an  übergebene Pfad <xref:System.IO.FileStream> *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="f97eb-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f97eb-455">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="f97eb-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f97eb-456">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f97eb-457">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f97eb-458">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="f97eb-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f97eb-459">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="f97eb-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f97eb-461">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="f97eb-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f97eb-462">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f97eb-463">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="f97eb-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f97eb-464">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f97eb-465">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="f97eb-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f97eb-466">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="f97eb-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="f97eb-467"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f97eb-468">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f97eb-469">Die `FileUpload` wird im Seiten Format verwendet Razor :</span><span class="sxs-lookup"><span data-stu-id="f97eb-469">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="f97eb-470">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f97eb-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f97eb-471">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="f97eb-471">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="f97eb-472">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="f97eb-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f97eb-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f97eb-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f97eb-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f97eb-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-475">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="f97eb-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f97eb-476">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="f97eb-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f97eb-477">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f97eb-478">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f97eb-479">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f97eb-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f97eb-480">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f97eb-481">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="f97eb-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f97eb-482">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="f97eb-482">Upload large files with streaming</span></span>

<span data-ttu-id="f97eb-483">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f97eb-484">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f97eb-485">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f97eb-486">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f97eb-487">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="f97eb-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f97eb-488">Die erste Seiten Antwort lädt das Formular und speichert ein antifälschungstoken in einem cookie (über das- `GenerateAntiforgeryTokenCookieAttribute` Attribut).</span><span class="sxs-lookup"><span data-stu-id="f97eb-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f97eb-489">Das-Attribut verwendet die integrierte [antifälschungs Unterstützung](xref:security/anti-request-forgery) von ASP.net Core, um eine cookie mit einem Anforderungs Token festzulegen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f97eb-490">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f97eb-491">In der Beispiel-APP `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` werden und als Filter auf die Seiten Anwendungsmodelle von `/StreamedSingleFileUploadDb` und in der Verwendung von `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Seiten Konventionen](xref:razor-pages/razor-pages-conventions)angewendet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="f97eb-492">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="f97eb-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f97eb-493">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f97eb-494">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f97eb-495">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f97eb-496">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f97eb-497">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="f97eb-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f97eb-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f97eb-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f97eb-499">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="f97eb-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f97eb-500">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f97eb-501">Überprüfen</span><span class="sxs-lookup"><span data-stu-id="f97eb-501">Validation</span></span>

<span data-ttu-id="f97eb-502">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="f97eb-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f97eb-503">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="f97eb-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f97eb-504">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-505">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f97eb-506">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f97eb-507">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="f97eb-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f97eb-508">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="f97eb-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f97eb-509">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="f97eb-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f97eb-510">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f97eb-511">Validierung von Inhalten</span><span class="sxs-lookup"><span data-stu-id="f97eb-511">Content validation</span></span>

<span data-ttu-id="f97eb-512">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f97eb-513">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f97eb-514">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f97eb-515">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="f97eb-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f97eb-516">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f97eb-517">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f97eb-518">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f97eb-519">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-519">File extension validation</span></span>

<span data-ttu-id="f97eb-520">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f97eb-521">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f97eb-522">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="f97eb-522">File signature validation</span></span>

<span data-ttu-id="f97eb-523">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f97eb-524">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="f97eb-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f97eb-525">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f97eb-526">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="f97eb-527">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f97eb-528">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="f97eb-528">File name security</span></span>

<span data-ttu-id="f97eb-529">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f97eb-530">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f97eb-531">Razor automatisch HTML-codiert Eigenschaftswerte für die Anzeige.</span><span class="sxs-lookup"><span data-stu-id="f97eb-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f97eb-532">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="f97eb-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f97eb-533">Außerhalb von Razor ist immer der <xref:System.Net.WebUtility.HtmlEncode*> Name Inhalt aus der Anforderung eines Benutzers.</span><span class="sxs-lookup"><span data-stu-id="f97eb-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f97eb-534">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f97eb-535">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f97eb-536">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="f97eb-536">Size validation</span></span>

<span data-ttu-id="f97eb-537">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f97eb-538">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="f97eb-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f97eb-539">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="f97eb-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f97eb-540">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="f97eb-541">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f97eb-542">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="f97eb-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f97eb-543">In nicht- Razor Formularen, die Formulardaten veröffentlichen oder direkt JavaScript verwenden `FormData` , muss der im-Element des Formulars angegebene Name dem `FormData` Namen des Parameters in der Aktion des Controllers entsprechen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f97eb-544">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-544">In the following example:</span></span>

* <span data-ttu-id="f97eb-545">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f97eb-546">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f97eb-547">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="f97eb-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f97eb-548">Für eine Razor Seiten Handler-Methode mit dem Namen `Upload` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f97eb-549">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="f97eb-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f97eb-550">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="f97eb-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f97eb-551">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="f97eb-551">Multipart body length limit</span></span>

<span data-ttu-id="f97eb-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="f97eb-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f97eb-553">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="f97eb-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f97eb-554">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="f97eb-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f97eb-555">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="f97eb-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="f97eb-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f97eb-557">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="f97eb-558">RazorWenden Sie in einer Pages-APP oder einer MVC-App den Filter auf das Seiten Modell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f97eb-559">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="f97eb-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="f97eb-560">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f97eb-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f97eb-561">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="f97eb-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="f97eb-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f97eb-563">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="f97eb-564">RazorWenden Sie den Filter in einer Pages-APP oder einer MVC-App auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="f97eb-565">`RequestSizeLimitAttribute`Kann auch mit der- [`@attribute`](xref:mvc/views/razor#attribute) Razor Direktive angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="f97eb-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f97eb-566">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="f97eb-566">Other Kestrel limits</span></span>

<span data-ttu-id="f97eb-567">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="f97eb-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f97eb-568">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f97eb-569">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="f97eb-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="f97eb-570">IIS</span><span class="sxs-lookup"><span data-stu-id="f97eb-570">IIS</span></span>

<span data-ttu-id="f97eb-571">Das Standard Anforderungs Limit ( `maxAllowedContentLength` ) beträgt 30 Millionen bytes, was etwa 28,6 MB beträgt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f97eb-572">Passen Sie den Grenzwert in der `web.config` Datei an.</span><span class="sxs-lookup"><span data-stu-id="f97eb-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="f97eb-573">Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="f97eb-574">Die `maxAllowedContentLength` Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="f97eb-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="f97eb-575">Weitere Informationen finden Sie unter [Anforderungs Limits `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="f97eb-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f97eb-576">Vergrößern Sie die maximale Anforderungs Text Größe für die HTTP-Anforderung, indem Sie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in festlegen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f97eb-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f97eb-577">Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="f97eb-578">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="f97eb-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f97eb-579">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="f97eb-579">Troubleshoot</span></span>

<span data-ttu-id="f97eb-580">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="f97eb-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f97eb-581">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="f97eb-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f97eb-582">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f97eb-583">Weitere Informationen finden Sie im Abschnitt [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="f97eb-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f97eb-584">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="f97eb-584">Connection failure</span></span>

<span data-ttu-id="f97eb-585">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f97eb-586">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="f97eb-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f97eb-587">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f97eb-588">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="f97eb-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f97eb-589">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f97eb-590">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f97eb-591">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="f97eb-592">Stream war zu lang.</span><span class="sxs-lookup"><span data-stu-id="f97eb-592">Stream was too long</span></span>

<span data-ttu-id="f97eb-593">Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="f97eb-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="f97eb-594">Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="f97eb-595">Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f97eb-596">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f97eb-597">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f97eb-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f97eb-598">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-598">Security considerations</span></span>

<span data-ttu-id="f97eb-599">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f97eb-600">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f97eb-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="f97eb-601">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="f97eb-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f97eb-602">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="f97eb-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="f97eb-603">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="f97eb-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f97eb-604">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="f97eb-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f97eb-605">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="f97eb-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="f97eb-606">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f97eb-607">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f97eb-608">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f97eb-609">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f97eb-610">Verwenden Sie keinen Dateinamen, der vom Benutzer bereitgestellt wird, oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei. &dagger; Der HTML-Code codiert den nicht vertrauenswürdigen Dateinamen, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="f97eb-611">Beispielsweise die Protokollierung des Datei namens oder die Anzeige auf der Benutzeroberfläche ( Razor Automatisches HTML-codieren der Ausgabe).</span><span class="sxs-lookup"><span data-stu-id="f97eb-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="f97eb-612">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="f97eb-613">Überprüfen Sie, ob Client seitige Überprüfungen auf dem Server ausgeführt werden. &dagger; Client seitige Überprüfungen können problemlos umgangen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f97eb-614">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="f97eb-615">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f97eb-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="f97eb-616">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f97eb-617">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f97eb-618">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-619">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f97eb-620">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="f97eb-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f97eb-621">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="f97eb-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f97eb-622">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="f97eb-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="f97eb-623">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="f97eb-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f97eb-624">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="f97eb-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f97eb-625">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="f97eb-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="f97eb-626">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen gelten, wenn Dateien vom Benutzer akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="f97eb-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f97eb-627">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="f97eb-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f97eb-628">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="f97eb-628">Storage scenarios</span></span>

<span data-ttu-id="f97eb-629">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="f97eb-629">Common storage options for files include:</span></span>

* <span data-ttu-id="f97eb-630">Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-630">Database</span></span>

  * <span data-ttu-id="f97eb-631">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="f97eb-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f97eb-632">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="f97eb-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f97eb-633">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="f97eb-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f97eb-634">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="f97eb-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f97eb-635">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="f97eb-635">For large file uploads:</span></span>
    * <span data-ttu-id="f97eb-636">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="f97eb-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f97eb-637">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f97eb-638">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="f97eb-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f97eb-639">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f97eb-640">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f97eb-641">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="f97eb-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f97eb-642">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f97eb-643">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="f97eb-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f97eb-644">Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .net zum Erstellen eines BLOBs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="f97eb-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="f97eb-645">Das Thema veranschaulicht <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, aber <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> kann verwendet werden, um einen <xref:System.IO.FileStream> in Blobspeicher zu speichern, wenn ein <xref:System.IO.Stream> verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f97eb-646">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="f97eb-646">File upload scenarios</span></span>

<span data-ttu-id="f97eb-647">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="f97eb-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f97eb-648">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="f97eb-648">**Buffering**</span></span>

<span data-ttu-id="f97eb-649">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f97eb-650">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="f97eb-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f97eb-651">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="f97eb-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f97eb-652">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="f97eb-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f97eb-653">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f97eb-654">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f97eb-655">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="f97eb-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f97eb-656">Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f97eb-657">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="f97eb-657">**Streaming**</span></span>

<span data-ttu-id="f97eb-658">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f97eb-659">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="f97eb-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f97eb-660">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="f97eb-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f97eb-661">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f97eb-662">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="f97eb-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f97eb-663">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f97eb-664">Das folgende Beispiel veranschaulicht die Verwendung eines Razor pages-Formulars zum Hochladen einer einzelnen Datei (*pages/bufferedsinglefileuploadphysical. cshtml* in der Beispiel-APP):</span><span class="sxs-lookup"><span data-stu-id="f97eb-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="f97eb-665">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="f97eb-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f97eb-666">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f97eb-667">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-667">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="f97eb-668">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="f97eb-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f97eb-669">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="f97eb-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f97eb-670">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f97eb-671">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-671">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="f97eb-672">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f97eb-673">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f97eb-674">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f97eb-675">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="f97eb-676">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="f97eb-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="f97eb-677">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="f97eb-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="f97eb-678">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="f97eb-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="f97eb-679">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-679">Applications should:</span></span>
>
> * <span data-ttu-id="f97eb-680">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="f97eb-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="f97eb-681">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="f97eb-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="f97eb-682">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="f97eb-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="f97eb-683">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="f97eb-684">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f97eb-685">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f97eb-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f97eb-686">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f97eb-687">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="f97eb-687">Validation</span></span>](#validation)

<span data-ttu-id="f97eb-688">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="f97eb-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f97eb-689">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f97eb-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f97eb-690">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f97eb-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f97eb-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f97eb-692">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-692">Binding matches form files by name.</span></span> <span data-ttu-id="f97eb-693">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="f97eb-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f97eb-694">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="f97eb-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f97eb-695">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f97eb-695">The following example:</span></span>

* <span data-ttu-id="f97eb-696">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f97eb-697">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f97eb-698">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f97eb-699">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="f97eb-699">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="f97eb-700">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="f97eb-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f97eb-701">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-701">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="f97eb-702">Der an  übergebene Pfad <xref:System.IO.FileStream> *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="f97eb-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f97eb-703">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="f97eb-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f97eb-704">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f97eb-705">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f97eb-706">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="f97eb-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f97eb-707">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="f97eb-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f97eb-709">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="f97eb-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f97eb-710">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f97eb-711">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="f97eb-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f97eb-712">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="f97eb-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f97eb-713">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="f97eb-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f97eb-714">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="f97eb-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="f97eb-715"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f97eb-716">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f97eb-717">Die `FileUpload` wird im Seiten Format verwendet Razor :</span><span class="sxs-lookup"><span data-stu-id="f97eb-717">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="f97eb-718">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f97eb-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f97eb-719">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="f97eb-719">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="f97eb-720">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="f97eb-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f97eb-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f97eb-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f97eb-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f97eb-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-723">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="f97eb-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f97eb-724">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="f97eb-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f97eb-725">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f97eb-726">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f97eb-727">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f97eb-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f97eb-728">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f97eb-729">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="f97eb-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f97eb-730">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="f97eb-730">Upload large files with streaming</span></span>

<span data-ttu-id="f97eb-731">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f97eb-732">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f97eb-733">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f97eb-734">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f97eb-735">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="f97eb-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f97eb-736">Die erste Seiten Antwort lädt das Formular und speichert ein antifälschungstoken in einem cookie (über das- `GenerateAntiforgeryTokenCookieAttribute` Attribut).</span><span class="sxs-lookup"><span data-stu-id="f97eb-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f97eb-737">Das-Attribut verwendet die integrierte [antifälschungs Unterstützung](xref:security/anti-request-forgery) von ASP.net Core, um eine cookie mit einem Anforderungs Token festzulegen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f97eb-738">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f97eb-739">In der Beispiel-APP `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` werden und als Filter auf die Seiten Anwendungsmodelle von `/StreamedSingleFileUploadDb` und in der Verwendung von `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Seiten Konventionen](xref:razor-pages/razor-pages-conventions)angewendet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="f97eb-740">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="f97eb-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f97eb-741">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f97eb-742">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f97eb-743">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f97eb-744">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f97eb-745">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="f97eb-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f97eb-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f97eb-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f97eb-747">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="f97eb-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f97eb-748">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f97eb-749">Überprüfen</span><span class="sxs-lookup"><span data-stu-id="f97eb-749">Validation</span></span>

<span data-ttu-id="f97eb-750">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="f97eb-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f97eb-751">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="f97eb-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f97eb-752">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="f97eb-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f97eb-753">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f97eb-754">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f97eb-755">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="f97eb-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f97eb-756">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="f97eb-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f97eb-757">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="f97eb-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f97eb-758">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f97eb-759">Validierung von Inhalten</span><span class="sxs-lookup"><span data-stu-id="f97eb-759">Content validation</span></span>

<span data-ttu-id="f97eb-760">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="f97eb-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f97eb-761">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f97eb-762">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f97eb-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f97eb-763">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="f97eb-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f97eb-764">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f97eb-765">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f97eb-766">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f97eb-767">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-767">File extension validation</span></span>

<span data-ttu-id="f97eb-768">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f97eb-769">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f97eb-770">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="f97eb-770">File signature validation</span></span>

<span data-ttu-id="f97eb-771">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f97eb-772">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="f97eb-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f97eb-773">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f97eb-774">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="f97eb-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="f97eb-775">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f97eb-776">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="f97eb-776">File name security</span></span>

<span data-ttu-id="f97eb-777">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="f97eb-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f97eb-778">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f97eb-779">Razor automatisch HTML-codiert Eigenschaftswerte für die Anzeige.</span><span class="sxs-lookup"><span data-stu-id="f97eb-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f97eb-780">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="f97eb-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f97eb-781">Außerhalb von Razor ist immer der <xref:System.Net.WebUtility.HtmlEncode*> Name Inhalt aus der Anforderung eines Benutzers.</span><span class="sxs-lookup"><span data-stu-id="f97eb-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f97eb-782">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="f97eb-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f97eb-783">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f97eb-784">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="f97eb-784">Size validation</span></span>

<span data-ttu-id="f97eb-785">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="f97eb-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f97eb-786">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="f97eb-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f97eb-787">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="f97eb-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f97eb-788">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="f97eb-789">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f97eb-790">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="f97eb-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f97eb-791">In nicht- Razor Formularen, die Formulardaten veröffentlichen oder direkt JavaScript verwenden `FormData` , muss der im-Element des Formulars angegebene Name dem `FormData` Namen des Parameters in der Aktion des Controllers entsprechen.</span><span class="sxs-lookup"><span data-stu-id="f97eb-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f97eb-792">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f97eb-792">In the following example:</span></span>

* <span data-ttu-id="f97eb-793">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f97eb-794">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f97eb-795">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="f97eb-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f97eb-796">Für eine Razor Seiten Handler-Methode mit dem Namen `Upload` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f97eb-797">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="f97eb-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f97eb-798">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="f97eb-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f97eb-799">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="f97eb-799">Multipart body length limit</span></span>

<span data-ttu-id="f97eb-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="f97eb-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f97eb-801">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="f97eb-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f97eb-802">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="f97eb-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f97eb-803">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="f97eb-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="f97eb-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f97eb-805">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="f97eb-806">RazorWenden Sie in einer Pages-APP oder einer MVC-App den Filter auf das Seiten Modell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f97eb-807">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="f97eb-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="f97eb-808">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f97eb-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f97eb-809">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="f97eb-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="f97eb-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f97eb-811">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f97eb-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="f97eb-812">RazorWenden Sie den Filter in einer Pages-APP oder einer MVC-App auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="f97eb-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f97eb-813">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="f97eb-813">Other Kestrel limits</span></span>

<span data-ttu-id="f97eb-814">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="f97eb-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f97eb-815">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f97eb-816">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="f97eb-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="f97eb-817">IIS</span><span class="sxs-lookup"><span data-stu-id="f97eb-817">IIS</span></span>

<span data-ttu-id="f97eb-818">Das Standard Anforderungs Limit ( `maxAllowedContentLength` ) beträgt 30 Millionen bytes, was etwa 28,6 MB beträgt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f97eb-819">Passen Sie den Grenzwert in der `web.config` Datei an.</span><span class="sxs-lookup"><span data-stu-id="f97eb-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="f97eb-820">Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="f97eb-821">Die `maxAllowedContentLength` Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="f97eb-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="f97eb-822">Weitere Informationen finden Sie unter [Anforderungs Limits `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="f97eb-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f97eb-823">Vergrößern Sie die maximale Anforderungs Text Größe für die HTTP-Anforderung, indem Sie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in festlegen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f97eb-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f97eb-824">Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f97eb-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="f97eb-825">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="f97eb-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f97eb-826">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="f97eb-826">Troubleshoot</span></span>

<span data-ttu-id="f97eb-827">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="f97eb-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f97eb-828">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="f97eb-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f97eb-829">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="f97eb-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f97eb-830">Weitere Informationen finden Sie im Abschnitt [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="f97eb-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f97eb-831">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="f97eb-831">Connection failure</span></span>

<span data-ttu-id="f97eb-832">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="f97eb-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f97eb-833">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="f97eb-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f97eb-834">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="f97eb-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f97eb-835">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="f97eb-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f97eb-836">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f97eb-837">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f97eb-838">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="f97eb-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="f97eb-839">Stream war zu lang.</span><span class="sxs-lookup"><span data-stu-id="f97eb-839">Stream was too long</span></span>

<span data-ttu-id="f97eb-840">Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="f97eb-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="f97eb-841">Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="f97eb-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="f97eb-842">Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.</span><span class="sxs-lookup"><span data-stu-id="f97eb-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="f97eb-843">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f97eb-843">Additional resources</span></span>

* [<span data-ttu-id="f97eb-844">HTTP-Verbindungs Anforderungs Ableitung</span><span class="sxs-lookup"><span data-stu-id="f97eb-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="f97eb-845">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="f97eb-845">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="f97eb-846">Azure-Sicherheit: Sicherheitsrahmen: Eingabevalidierung | Entschärfungen</span><span class="sxs-lookup"><span data-stu-id="f97eb-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="f97eb-847">Azure Cloud Design Patterns: Valet-Schlüssel Muster</span><span class="sxs-lookup"><span data-stu-id="f97eb-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)

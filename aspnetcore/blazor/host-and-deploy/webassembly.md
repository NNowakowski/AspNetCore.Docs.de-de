---
title: Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor-App mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 06059e0f9ff6a3f4073d8d01d1ac541c30ad1ab1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014190"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) und [Safia Abdalla](https://safia.rocks)

Mit dem [Blazor WebAssembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly):

* Die Blazor-App, ihre Abhängigkeiten und die .NET-Runtime werden parallel im Browser heruntergeladen.
* Die App wird direkt im UI-Thread des Browsers ausgeführt.

Die folgenden Bereitstellungsstrategien werden unterstützt:

* Die Blazor-App wird von einer ASP.NET Core-App unterstützt. Diese Strategie wird im Abschnitt [Gehostete Bereitstellung mit ASP.NET Core](#hosted-deployment-with-aspnet-core) behandelt.
* Die Blazor-App wird auf einem Webserver oder Webservice für statisches Hosting abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird. Diese Strategie wird im Abschnitt [Eigenständige Bereitstellung](#standalone-deployment) behandelt, der Informationen zum Hosten einer Blazor WebAssembly-App als untergeordnete IIS-App enthält.

## <a name="compression"></a>Komprimierung

Wenn eine Blazor WebAssembly-App veröffentlicht wird, wird die Ausgabe bei der Veröffentlichung statisch komprimiert, um die App-Größe zu verringern und den Aufwand für eine Laufzeitkomprimierung zu beseitigen. Die folgenden Komprimierungsalgorithmen werden verwendet:

* [Brotli](https://tools.ietf.org/html/rfc7932) (höchste Stufe)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor basiert auf dem Host, um die entsprechenden komprimierten Dateien bereitzustellen. Wenn Sie ein mit ASP.NET Core gehostetes Projekt verwenden, kann das Hostprojekt die Inhaltsaushandlung durchführen und die statisch komprimierten Dateien bereitstellen. Beim Hosten einer eigenständigen Blazor WebAssembly-App sind möglicherweise zusätzliche Schritte erforderlich, um sicherzustellen, dass die statisch komprimierten Dateien bereitgestellt werden:

* Informationen zur Komprimierungskonfiguration der Datei `web.config` von IIS finden Sie im Abschnitt [IIS: Brotli- und Gzip-Komprimierung](#brotli-and-gzip-compression). 
* Beim Hosten über statische Hostinglösungen, die die Inhaltsaushandlung für statisch komprimierte Dateien nicht unterstützen (z. B. GitHub-Seiten), sollten Sie die App so konfigurieren, dass sie die in das Brotli-Format komprimierten Dateien abruft und decodiert:

  * Rufen Sie den JavaScript Brotli-Decoder aus dem [GitHub-Repository „google/brotli“](https://github.com/google/brotli) ab. Ab Juli 2020 heißt die Decoderdatei `decode.min.js` und befindet sich im [Ordner `js`](https://github.com/google/brotli/tree/master/js) des Repositorys.
  * Aktualisieren Sie die App zur Verwendung des Decoders. Ändern Sie das Markup innerhalb des schließenden `<body>`-Tags in der Datei `wwwroot/index.html` wie folgt:
  
    ```html
    <script src="decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
Um die Komprimierung zu deaktivieren, fügen Sie der Projektdatei der App die `BlazorEnableCompression` MSBuild-Eigenschaft hinzu, und setzen Sie den Wert auf `false`:

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a>Erneutes Generieren von URLs für ein ordnungsgemäßes Routing

Routinganforderungen für Seitenkomponenten in einer Blazor WebAssembly-App sind nicht so unkompliziert wie Routinganforderungen in einer gehosteten Blazor Server-App. Betrachten wir eine Blazor WebAssembly-App mit zwei Komponenten:

* `Main.razor`: Wird im Stammverzeichnis der App geladen und enthält einen Link zur `About`-Komponente (`href="About"`).
* `About.razor`: `About`-Komponente.

Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:

1. Der Browser sendet eine Anforderung.
1. Die Standardseite wird zurückgegeben, in der Regel `index.html`.
1. `index.html` startet die App.
1. Der Blazor-Router wird geladen, und die Razor-Komponente `Main` wird gerendert.

Auf der Seite „Main“ kann der Link zur `About`-Komponente auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die gerenderte `About`-Komponente selbst bereitstellt. Alle Anforderungen von internen Endpunkten *innerhalb der Blazor WebAssembly-App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst. Der Router verarbeitet Anforderungen intern.

Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf. Diese Ressource ist im Internethost der App nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.

Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite `index.html` erneut generieren. Wenn `index.html` zurückgegeben wird, übernimmt der Blazor-Router der App und antwortet mit der richtigen Ressource.

Wenn Sie die Bereitstellung auf einem IIS-Server durchführen, können Sie das URL-Rewrite-Modul mit der veröffentlichten `web.config`-Datei der App verwenden. Weitere Informationen finden Sie im Abschnitt [IIS](#iis).

## <a name="hosted-deployment-with-aspnet-core"></a>Gehostete Bereitstellung mit ASP.NET Core

Mit einer *gehosteten Bereitstellung* wird die Blazor WebAssembly-App über eine auf einem Webserver ausgeführte [ASP.NET Core-App](xref:index) für Browser bereitgestellt.

Die Blazor WebAssembly-Client-App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht. Die beiden Apps werden zusammen bereitgestellt. Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann. Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor WebAssembly-App**-Projektvorlage (`blazorwasm`-Vorlage bei Verwendung des Befehls [`dotnet new`](/dotnet/core/tools/dotnet-new)) mit der ausgewählten Option **`Hosted`** (`-ho|--hosted`, wenn Sie den Befehl `dotnet new` verwenden).

Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.

Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>Gehostete Bereitstellung mit mehreren Blazor WebAssembly-Apps

### <a name="app-configuration"></a>App-Konfiguration

So konfigurieren Sie eine gehostete Blazor-Lösung für mehrere Blazor WebAssembly-Apps:

* Verwenden Sie eine vorhandene gehostete Blazor-Lösung, oder erstellen Sie eine neue Projektmappe aus der Projektvorlage „Blazor Hosted“.

* Fügen Sie in der Projektdatei der Client-App `<PropertyGroup>` eine `<StaticWebAssetBasePath>`-Eigenschaft mit dem Wert `FirstApp` hinzu, um den Basispfad für die statischen Ressourcen des Projekts festzulegen:

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* Fügen Sie der Projektmappe eine zweite Client-App hinzu:

  * Fügen Sie dem Ordner der Projektmappe einen Ordner mit dem Namen `SecondClient` hinzu.
  * Erstellen Sie eine Blazor WebAssembly-App mit dem Namen `SecondBlazorApp.Client` im Ordner `SecondClient` aus der Blazor WebAssembly-Projektvorlage.
  * In der Projektdatei der App:

    * Fügen Sie `<PropertyGroup>` eine `<StaticWebAssetBasePath>`-Eigenschaft mit dem Wert `SecondApp` hinzu:

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * Fügt einen Projektverweis auf das `Shared`-Projekt hinzu:

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      Der Platzhalter `{SOLUTION NAME}` ist der Name der Projektmappe.

* Erstellen Sie in der Projektdatei der Server-App einen Projektverweis für die hinzugefügte Client-App:

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* Konfigurieren Sie in der Datei `Properties/launchSettings.json` der Server-App die `applicationUrl` des Kestrel-Profils (`{SOLUTION NAME}.Server`) für den Zugriff auf Client-Apps an den Ports 5001 und 5002:

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* Entfernen Sie in der `Startup.Configure`-Methode (`Startup.cs`) der Server-App die folgenden Zeilen nach dem Aufruf von <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  Fügen Sie Middleware hinzu, die Anforderungen den Client-Apps zuordnet. Im folgenden Beispiel wird die Middleware so konfiguriert, dass Sie unter den folgenden Umständen ausgeführt wird:

  * Der Anforderungsport ist entweder 5001 für die ursprüngliche Client-App oder 5002 für die hinzugefügte Client-App.
  * Der Anforderungshost ist entweder `firstapp.com` für die ursprüngliche Client-App oder `secondapp.com` für die hinzugefügte Client-App.

    > [!NOTE]
    > Das in diesem Abschnitt gezeigte Beispiel erfordert zusätzliche Konfiguration:
    >
    > * Für dasZugreifen auf die Apps auf den Beispielhostdomänen: `firstapp.com` und `secondapp.com`.
    > * Zertifikate für die Client-Apps zum Aktivieren von TLS-Sicherheit (HTTPS).
    >
    > Die erforderliche Konfiguration sprengt den Rahmen dieses Artikels und ist davon abhängig, wie die Lösung gehostet wird. Weitere Informationen finden Sie in den Artikeln zum [Hosten und Bereitstellen](xref:host-and-deploy/index).

  Platzieren Sie den folgenden Code dort, wo zuvor Zeilen entfernt wurden:

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* Ersetzen Sie im Wettervorhersagecontroller (`Controllers/WeatherForecastController.cs`) der Server-App die vorhandene Route (`[Route("[controller]")]`) zu `WeatherForecastController` durch die folgenden Routen:

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  Die Middleware, die der `Startup.Configure`-Methode der Server-App hinzugefügt wurde, ändert eingehende Anforderungen an `/WeatherForecast` abhängig vom Port (5001/5002) oder der Domäne (`firstapp.com`/`secondapp.com`) in `/FirstApp/WeatherForecast` oder `/SecondApp/WeatherForecast`. Die vorherigen Controllerrouten sind erforderlich, um Wetterdaten von der Server-App an die Client-Apps zurückzugeben.

### <a name="static-assets-and-class-libraries"></a>Statische Ressourcen und Klassenbibliotheken

Verwenden Sie die folgenden Verfahren für statische Ressourcen:

* Wenn sich die Ressource im Ordner `wwwroot` der Client-App befindet, geben Sie Pfade normal an:

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* Wenn sich die Ressource im Ordner `wwwroot` einer [Razor-Klassenbibliothek (RCL)](xref:blazor/components/class-libraries) befindet, verweisen Sie in der Client-App gemäß der Anleitung im [RCL-Artikel](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl) auf die statische Ressource:

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

Auf Komponenten, die für eine Client-App durch eine Klassenbibliothek bereitgestellt werden, wird normal verwiesen. Wenn für Komponenten Stylesheets oder JavaScript-Dateien erforderlich sind, verwenden Sie einen der folgenden Ansätze, um die statischen Ressourcen abzurufen:

* Die Datei `wwwroot/index.html` der Client-App kann mit den statischen Ressourcen verknüpft werden (`<link>`).
* Die Komponente kann die [`Link`-Komponente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) des Frameworks verwenden, um die statischen Ressourcen abzurufen.

Die oben aufgeführten Konzepte werden in den folgenden Beispielen gezeigt.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Auf Komponenten, die für eine Client-App durch eine Klassenbibliothek bereitgestellt werden, wird normal verwiesen. Wenn für Komponenten Stylesheets oder JavaScript-Dateien erforderlich sind, muss die Datei `wwwroot/index.html` der Client-App die richtigen Links der statischen Ressourcen enthalten. Diese Konzepte werden in den folgenden Beispielen gezeigt.

::: moniker-end

Fügen Sie einer der Client-Apps die folgende `Jeep`-Komponente hinzu. Die `Jeep`-Komponente verwendet:

* Ein Bild aus dem Ordner `wwwroot` der Client-App (`jeep-cj.png`).
* Ein Bild aus einem Ordner `wwwroot` (`jeep-yj.png`) einer [hinzugefügten Razor Komponentenbibliothek](xref:blazor/components/class-libraries) (`JeepImage`).
* Die Beispielkomponente (`Component1`) wird von der RCL-Projektvorlage automatisch erstellt, wenn die `JeepImage`-Bibliothek der Projektmappe hinzugefügt wird.

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> Veröffentlichen Sie **keine** Bilder von Fahrzeugen, es sei denn, Sie sind der Eigentümer der Bilder. Andernfalls riskieren Sie Urheberrechtsverletzungen.

::: moniker range=">= aspnetcore-5.0"

Das Bild `jeep-yj.png` der Bibliothek kann auch der `Component1`-Komponente (`Component1.razor`) der Bibliothek hinzugefügt werden. Wenn Sie die `my-component`-CSS-Klasse auf der Seite der Client-App bereitstellen möchten, verknüpfen Sie das Stylesheet der Bibliothek mithilfe der [`Link`-Komponente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) des Frameworks:

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

Eine Alternative zur Verwendung der [`Link`-Komponente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) besteht darin, das Stylesheet aus der Datei `wwwroot/index.html` der Client-App zu laden. Bei diesem Ansatz ist das Stylesheet für alle Komponenten in der Client-App verfügbar:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Das Bild `jeep-yj.png`der Bibliothek kann auch der `Component1`-Komponente der Bibliothek (`Component1.razor`) hinzugefügt werden:

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

Die Datei `wwwroot/index.html` der Client-App fordert das Stylesheet der Bibliothek mit dem folgenden hinzugefügten `<link>`-Tag an:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

Fügen Sie der `Jeep`-Komponente in der `NavMenu`-Komponente der Client-App (`Shared/NavMenu.razor`) Navigation hinzu:

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

Weitere Informationen zu RCLs finden Sie hier:

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>Eigenständige Bereitstellung

Bei einer *eigenständigen Bereitstellung* wird die Blazor WebAssembly-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden. Jeder Server für statische Dateien kann die Blazor-App bedienen.

Eigenständige Bereitstellungsressourcen werden im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` veröffentlicht.

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssembly-Apps können in Azure App Service unter Windows bereitgestellt werden. Dort wird die App auf [IIS](#iis) gehostet.

Die Bereitstellung einer eigenständigen Blazor WebAssembly-App in Azure App Service für Linux wird derzeit nicht unterstützt. Ein Linux-Serverimage zum Hosten der App ist derzeit nicht verfügbar. Wir arbeiten daran, sodass dieses Szenario bald unterstützt werden kann.

### <a name="iis"></a>IIS

IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps. Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Erstellen einer statischen Website unter IIS).

Veröffentlichte Ressourcen werden im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish` erstellt. Die Inhalte des Ordners `publish` werden auf dem Webserver oder über den Hostingdienst gehostet.

#### <a name="webconfig"></a>web.config

Beim Veröffentlichen eines Blazor-Projekts wird eine `web.config`-Datei mit der folgenden IIS-Konfiguration erstellt:

* Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:
  * `application/octet-stream`
  * `application/wasm`
* URL-Rewrite-Modul-Regeln werden eingerichtet:
  * Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden (`wwwroot/{PATH REQUESTED}`).
  * Richten Sie ein SPA-Fallbackrouting ein, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte (`wwwroot/index.html`) umgeleitet werden.
  
#### <a name="use-a-custom-webconfig"></a>Verwenden einer benutzerdefinierten Datei web.config

Um eine benutzerdefinierte Datei `web.config` zu verwenden, speichern Sie die benutzerdefinierte Datei `web.config` im Stammverzeichnis des Projektordners und veröffentlichen das Projekt.

#### <a name="install-the-url-rewrite-module"></a>Installieren des URL-Rewrite-Moduls

Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt. Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar. Das Modul muss von der IIS-Website heruntergeladen werden. Verwenden Sie den Webplattform-Installer zur Installation des Moduls:

1. Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus. Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.
1. Kopieren Sie den Installer auf den Server. Führen Sie den Installer aus. Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu. Der Server muss nach Abschluss der Installation nicht neu gestartet werden.

#### <a name="configure-the-website"></a>Konfigurieren der Website

Legen Sie für den **physischen Pfad** der Website den Ordner der App fest. Der Ordner enthält Folgendes:

* Die Datei `web.config`, die von IIS zum Konfigurieren der Website verwendet wird, einschließlich der erforderlichen Umleitungsregeln und Dateiinhaltstypen
* Den Ordner der App für statische Objekte

#### <a name="host-as-an-iis-sub-app"></a>Hosten als IIS-untergeordnete App

Wenn eine eigenständige App als IIS-untergeordnete App gehostet wird, führen Sie einen der folgenden Schritte aus:

* Deaktivieren Sie den vererbten ASP.NET Core Module-Handler.

  Entfernen Sie den Handler in der veröffentlichen Datei `web.config` der Blazor-App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Deaktivieren Sie die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung), indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zur [Konfiguration des Basispfads der App](xref:blazor/host-and-deploy/index#app-base-path) durchgeführt. Legen Sie den Basispfad der App in der Datei `index.html` der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.

#### <a name="brotli-and-gzip-compression"></a>Brotli- und Gzip-Komprimierung

IIS kann über `web.config` so konfiguriert werden, dass Brotli- oder Gzip-komprimierte Blazor-Ressourcen bereitgestellt werden. Eine Beispielkonfiguration finden Sie unter [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Problembehandlung

Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist. Wenn das Modul nicht installiert ist, kann die Datei `web.config` nicht von IIS analysiert werden. Dadurch wird verhindert, dass die Konfiguration der Website vom IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.

Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Azure Storage

Das Hosten statischer Dateien von [Azure Storage](/azure/storage/) ermöglicht das serverlose Blazor-App-Hosting. Benutzerdefinierte Domänennamen, das Azure Content Delivery Network (CDN) und HTTPS werden unterstützt.

Wenn der Blobdienst für das Hosten von statischen Websites in einem Speicherkonto aktiviert ist, gehen Sie folgendermaßen vor:

* Legen Sie den **Namen des Indexdokuments** auf `index.html` fest.
* Legen Sie den **Pfad des Fehlerdokuments** auf `index.html` fest. Razor-Komponenten und andere Endpunkte, bei denen es sich nicht um Dateien handelt, befinden sich nicht in physischen Pfaden in dem statischen Inhalt, der vom Blobdienst gespeichert wird. Wenn eine Anforderung für eine dieser Ressourcen empfangen wird, die vom Blazor-Router verarbeitet werden soll, leitet der vom Blob-Dienst generierte *Fehler 404 – Nicht gefunden* die Anforderung an den **Pfad des Fehlerdokuments** weiter. Das Blob `index.html` wird zurückgegeben, und der Blazor-Router lädt und verarbeitet den Pfad.

Wenn Dateien aufgrund ungeeigneter MIME-Typen im `Content-Type`-Headern der Dateien nicht zur Laufzeit geladen werden, führen Sie eine der folgenden Aktionen durch:

* Konfigurieren Sie die Tools so, dass beim Bereitstellen der Dateien die richtigen MIME-Typen (`Content-Type`-Header) bereitgestellt werden.
* Ändern Sie nach Bereitstellung der App die MIME-Typen (`Content-Type`-Header) für die Dateien.

  Führen Sie im Storage-Explorer (Azure-Portal) für jede Datei folgende Schritte aus:
  
  1. Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften** aus.
  1. Legen Sie **ContentType-** fest, und klicken Sie auf die Schaltfläche **Speichern**

Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Die folgende Datei `nginx.conf` wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei `index.html` gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Wenn Sie [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) für das [NGINX-Burstratenlimit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) festlegen, erfordern Blazor WebAssembly-Apps möglicherweise einen größeren `burst`-Parameterwert, um die relativ große Anzahl an Anforderungen zu erfüllen, die eine App stellen kann. Legen Sie den Wert zunächst auf 60 fest:

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Erhöhen Sie den Wert, wenn Browserentwicklertools oder Netzwerkdatenverkehrstools angeben, dass für Anforderungen der Statuscode *503 – Dienst nicht verfügbar* zurückgegeben wird.

Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx in Docker

Richten Sie zum Hosten von Blazor in Docker mithilfe von NGINX das Dockerfile für die Verwendung des auf Alpine basierenden NGINX-Images ein. Aktualisieren Sie die Dockerfile zum Kopieren der Datei `nginx.config` in den Container.

Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

So stellen Sie eine Blazor WebAssembly-App für CentOS 7 oder höher bereit:

1. Erstellen Sie die Apache-Konfigurationsdatei. Das folgenden Beispiel zeigt eine vereinfachte Konfigurationsdatei (`blazorapp.config`):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Platzieren Sie die Apache-Konfigurationsdatei in das `/etc/httpd/conf.d/`-Verzeichnis, das das standardmäßige Apache-Konfigurationsverzeichnis in CentOS 7 ist.

1. Platzieren Sie die Dateien der App in das `/var/www/blazorapp`-Verzeichnis (den Speicherort, der für `DocumentRoot` in der Konfigurationsdatei angegeben ist).

1. Starten Sie den Apache-Dienst neu.

Weitere Informationen finden Sie unter [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) und [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub-Seiten

Fügen Sie zum Verarbeiten von URL-Umschreibungen eine `wwwroot/404.html`-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite `index.html` verarbeitet wird. Ein Beispiel finden Sie im GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [Livesite](https://stevesandersonms.github.io/BlazorOnGitHubPages/))

Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, aktualisieren Sie das `<base>`-Tag in `wwwroot/index.html`. Legen Sie den Wert des `href`-Attributs auf den Namen des GitHub-Repositorys mit einem nachfolgenden Schrägstrich fest (z. B. `/my-repository/`). Im GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) wird die Basis `href` bei der Veröffentlichung durch die [ Konfigurationsdatei `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml) aktualisiert.

> [!NOTE]
> Das GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) befindet sich nicht im Besitz der .NET Foundation oder von Microsoft und wird auch nicht von diesen verwaltet oder unterstützt.

## <a name="host-configuration-values"></a>Hostkonfigurationswerte

[Blazor WebAssembly-Apps](xref:blazor/hosting-models#blazor-webassembly) können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Runtime in der Entwicklungsumgebung akzeptieren.

### <a name="content-root"></a>Inhaltsstammverzeichnis

Mit dem Argument `--contentroot` wird der absolute Pfad zum Verzeichnis festgelegt, das die Inhaltsdateien (das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root)) der App enthält. In den folgenden Beispielen ist `/content-root-path` der Stammpfad für Inhalte der App.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Basispfad

Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem relativen URL-Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt). In den folgenden Beispielen ist `/relative-URL-path` die Pfadbasis der App. Weitere Informationen finden Sie unter [Basispfad einer App](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt. Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/">` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URLs

Mit dem Argument `--urls` werden die IP-oder Hostadressen mit Ports und Protokollen festgelegt, die auf Anforderungen lauschen sollen.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurieren des Linkers

Blazor führt auf jedem Releasebuild eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys zu entfernen. Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Benutzerdefiniertes Laden von Startressourcen

Eine Blazor WebAssembly-App kann mit der Funktion `loadBootResource` initialisiert werden, um den integrierten Mechanismus zum Laden von Startressourcen zu überschreiben. Verwenden Sie `loadBootResource` für die folgenden Szenarien:

* Benutzern erlauben, statische Ressourcen wie Zeitzonendaten oder `dotnet.wasm` aus einem CDN zu laden.
* Komprimierte Assemblys über eine HTTP-Anforderung laden und auf dem Client für Hosts dekomprimieren, die das Abrufen komprimierter Inhalte vom Server nicht unterstützen.
* Ressourcen per Alias an einen anderen Namen umleiten, indem jede `fetch`-Anforderung an einen neuen Namen umgeleitet wird.

`loadBootResource`-Parameter sind in der folgenden Tabelle aufgeführt.

| Parameter    | Beschreibung |
| ------------ | ----------- |
| `type`       | Der Typ der Ressource. Zulässige Typen: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` |
| `name`       | Der Name der Ressource. |
| `defaultUri` | Der relative oder absolute URI der Ressource. |
| `integrity`  | Die Integritätszeichenfolge, die den erwarteten Inhalt in der Antwort darstellt. |

`loadBootResource` gibt eines der folgenden Elemente zurück, um den Ladevorgang außer Kraft zu setzen:

* URI-Zeichenfolge. Im Beispiel unten (`wwwroot/index.html`) werden die folgenden Dateien von einem CDN unter `https://my-awesome-cdn.com/` bereitgestellt:

  * `dotnet.*.js`
  * `dotnet.wasm`
  * Zeitzonendaten

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Übergeben Sie den Parameter `integrity` in einem Header, um das Standardverhalten bei der Integritätsprüfung beizubehalten.

  Das folgende Beispiel (`wwwroot/index.html`) fügt einen benutzerdefinierten HTTP-Header den ausgehenden Anforderungen hinzu und übergibt den Parameter `integrity` an den Aufruf von `fetch`:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, was zum standardmäßigen Ladeverhalten führt.

Externe Quellen müssen die erforderlichen CORS-Header für Browser zurückgeben, um das ursprungsübergreifende Laden von Ressourcen zu ermöglichen. CDNs stellen in der Regel die erforderlichen Header standardmäßig zur Verfügung.

Sie müssen nur für benutzerdefinierte Verhaltensmuster Typen angeben. Nicht mit `loadBootResource` angegebene Typen werden vom Framework entsprechend ihres Standardladeverhaltens geladen.

## <a name="change-the-filename-extension-of-dll-files"></a>Ändern der Dateinamenerweiterung von DLL-Dateien

Falls Sie die Dateinamenerweiterungen der veröffentlichten `.dll`-Dateien der App ändern müssen, befolgen Sie die Anleitung in diesem Abschnitt.

Verwenden Sie nach Veröffentlichen der App ein Shellskript oder eine DevOps-Buildpipeline, um `.dll`-Dateien umzubenennen und eine andere Dateierweiterung zu verwenden. Verwenden Sie die `.dll`-Dateien im `wwwroot`-Verzeichnis der veröffentlichten Ausgabe der App (z. B. `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).

In den folgenden Beispielen werden `.dll`-Dateien so umbenannt, dass die Dateierweiterung `.bin` verwendet wird.

Unter Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

Unter Linux oder macOS:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Um eine andere Dateierweiterung als `.bin` zu verwenden, ersetzen Sie `.bin` in den vorherigen Befehlen.

Um die komprimierten Dateien `blazor.boot.json.gz` und `blazor.boot.json.br` zu verarbeiten, wenden Sie einen der folgenden Ansätze an:

* Entfernen Sie die komprimierten Dateien `blazor.boot.json.gz` und `blazor.boot.json.br`. Bei diesem Ansatz ist die Komprimierung deaktiviert.
* Komprimieren Sie die aktualisierte Datei `blazor.boot.json` erneut.

Die vorherige Anleitung gilt auch, wenn Service Worker-Assets verwendet werden. Entfernen Sie die Dateien `wwwroot/service-worker-assets.js.br` und `wwwroot/service-worker-assets.js.gz`, oder komprimieren Sie diese erneut. Andernfalls treten bei Dateiintegritätsprüfungen im Browser Fehler auf.

Im folgenden Windows-Beispiel wird ein PowerShell-Skript verwendet, das sich im Stammverzeichnis des Projekts befindet.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

In der Projektdatei wird das Skript nach Veröffentlichung der App ausgeführt:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

Wenn Sie Feedback geben möchten, besuchen Sie [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).

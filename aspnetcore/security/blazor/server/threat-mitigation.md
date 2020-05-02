---
title: Leitfaden zur Bedrohungsminderung Blazor für ASP.net Core Server
author: guardrex
description: Erfahren Sie, wie Sie Sicherheitsbedrohungen Blazor für Server-apps mindern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/threat-mitigation
ms.openlocfilehash: 9a5e313153e5c5c17fc723cc9768c49ffd828007
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206341"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a>Leitfaden zur Bedrohungsminderung für ASP.net Core blazor-Server

Von [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor-Server-apps übernehmen ein *Zustands* behaftetes Datenverarbeitungs Modell, bei dem der Server und der Client eine langlebige Beziehung aufrechterhalten. Der persistente Status wird durch eine Verbindung verwaltet, die auch Verbindungen umfassen [kann, die](xref:blazor/state-management)potenziell langlebig sind.

Wenn ein Benutzer eine blazor-Serversite besucht, erstellt der Server eine Verbindung im Arbeitsspeicher des Servers. Die Verbindung zeigt dem Browser an, welche Inhalte gerbt werden sollen, und antwortet auf Ereignisse, z. b. wenn der Benutzer eine Schaltfläche in der Benutzeroberfläche auswählt. Um diese Aktionen auszuführen, ruft eine Verbindung JavaScript-Funktionen in den Browser-und .NET-Methoden des Benutzers auf dem Server auf. Diese bidirektionale JavaScript-basierte Interaktion wird als [JavaScript-Interop (JS-Interop)](xref:blazor/call-javascript-from-dotnet)bezeichnet.

Da die JS-Interop über das Internet erfolgt und der Client einen Remote Browser verwendet, haben blazor-Server-Apps die meisten Probleme mit der Sicherheit der Web-App. In diesem Thema werden häufige Bedrohungen für blazor-Server-apps beschrieben und Richtlinien zur Bedrohungsminderung behandelt, die sich auf apps mit Internet Zugriff

In eingeschränkten Umgebungen, wie z. b. innerhalb von Unternehmensnetzwerken oder Intranets, sind einige der Hinweise zur Entschärfung:

* Gilt nicht für die eingeschränkte Umgebung.
* Die Implementierung der Kosten ist nicht sinnvoll, da das Sicherheitsrisiko in einer eingeschränkten Umgebung gering ist.

## <a name="resource-exhaustion"></a>Ressourcenauslastung

Die Ressourcenauslastung kann auftreten, wenn ein Client mit dem Server interagiert und der Server übermäßige Ressourcen beansprucht. Übermäßiger Ressourcenverbrauch hat hauptsächlich folgende Auswirkungen:

* [CPU](#cpu)
* [Memory](#memory)
* [Client Verbindungen](#client-connections)

DOS-Angriffe (Denial of Service) suchen in der Regel nach dem ausschöpfen der Ressourcen einer APP oder eines Servers. Die Ressourcenauslastung ist jedoch nicht notwendigerweise das Ergebnis eines Angriffs auf das System. Endliche Ressourcen können z. b. aufgrund einer hohen Benutzer Nachfrage aufgebraucht werden. DOS wird weiter unten im Abschnitt [Denial of Service (DOS)-Angriffe](#denial-of-service-dos-attacks) behandelt.

Ressourcen, die sich außerhalb des blazor-Frameworks befinden, z. b. Datenbanken und Datei Handles (die zum Lesen und Schreiben von Dateien verwendet werden), können auch die Ressourcenauslastung Weitere Informationen finden Sie unter <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

Die CPU-Auslastung kann auftreten, wenn ein oder mehrere Clients eine intensive CPU-Arbeit durch den Server erzwingen.

Nehmen wir beispielsweise eine blazor-Server-APP, die eine " *fbonnacci*"-Zahl berechnet. Eine "fbonnacci"-Zahl wird aus einer "fbonnacci"-Sequenz erstellt, wobei jede Zahl in der Sequenz die Summe der beiden vorangehenden Zahlen ist. Der Arbeitsaufwand, der erforderlich ist, um die Antwort zu erreichen, hängt von der Länge der Sequenz und der Größe des Anfangs Werts ab. Wenn die APP keine Grenzwerte für die Anforderung eines Clients eingibt, können die CPU-intensiven Berechnungen die CPU-Zeit dominieren und die Leistung anderer Tasks verringern. Übermäßiger Ressourcenverbrauch ist ein Sicherheitsproblem, das sich auf die Verfügbarkeit auswirkt

Die CPU-Auslastung ist für alle öffentlich ausgerichteten apps von Bedeutung. In regulären Web-Apps stellen Anforderungen und Verbindungen als Schutzzeit ein Timeout dar, aber blazor-Server-Apps bieten nicht die gleichen Sicherheitsvorkehrungen. Blazor-Server-apps müssen entsprechende Überprüfungen und Beschränkungen enthalten, bevor Sie potenziell CPU-intensive Aufgaben durchführen können.

### <a name="memory"></a>Arbeitsspeicher

Die Speicherauslastung kann auftreten, wenn ein oder mehrere Clients den Server zwingen, eine große Menge an Arbeitsspeicher zu belegen.

Stellen Sie sich beispielsweise eine blazor-Server-App mit einer Komponente vor, von der eine Liste von Elementen akzeptiert und angezeigt wird. Wenn die blazor-App keine Grenzwerte für die Anzahl zulässiger Elemente oder die Anzahl der Elemente enthält, die auf dem Client gerendert werden, kann die speicherintensive Verarbeitung und das Rendering den Speicher des Servers bis zu dem Punkt, an dem die Leistung des Servers leidet, dominieren. Der Server stürzt möglicherweise ab oder stürzt ab, wenn er abgestürzt ist.

Sehen Sie sich das folgende Szenario für die Wartung und Anzeige einer Liste von Elementen an, die zu einem potenziellen Speicher Auslastungs Szenario auf dem Server gehören:

* Die Elemente in einer `List<MyItem>` Eigenschaft oder einem Feld verwenden den Arbeitsspeicher des Servers. Wenn die APP zulässt, dass die Liste der Elemente unbegrenzt wächst, besteht das Risiko, dass der Server nicht über genügend Arbeitsspeicher verfügt. Wenn nicht genügend Arbeitsspeicher verfügbar ist, wird die aktuelle Sitzung beendet (Absturz), und alle gleichzeitigen Sitzungen in dieser Serverinstanz erhalten eine Ausnahme aufgrund ungenügenden Arbeitsspeichers. Um zu verhindern, dass dieses Szenario auftritt, muss die APP eine Datenstruktur verwenden, die ein Element Limit für gleichzeitige Benutzer erzwingt.
* Wenn ein pagingschema nicht zum Rendern verwendet wird, verwendet der Server zusätzlichen Arbeitsspeicher für Objekte, die nicht auf der Benutzeroberfläche sichtbar sind. Ohne eine Beschränkung der Anzahl von Elementen können Speicheranforderungen den verfügbaren Server Arbeitsspeicher ausschöpfen. Um dieses Szenario zu verhindern, verwenden Sie einen der folgenden Ansätze:
  * Verwenden Sie beim Rendern paginierte Listen.
  * Zeigen Sie nur die ersten 100 bis 1.000 Elemente an, und fordern Sie den Benutzer auf, Suchkriterien einzugeben, um Elemente außerhalb der angezeigten Elemente zu suchen.
  * Implementieren Sie für ein erweitertes renderingszenario Listen oder Raster, die *Virtualisierung*unterstützen. Mit der Virtualisierung wird nur eine Teilmenge der Elemente, die derzeit für den Benutzer sichtbar sind, von Listen dargestellt. Wenn der Benutzer mit der Scrollleiste in der Benutzeroberfläche interagiert, rendert die Komponente nur die Elemente, die für die Anzeige benötigt werden. Die Elemente, die derzeit nicht für die Anzeige erforderlich sind, können im sekundären Speicher gespeichert werden. Dies ist der ideale Ansatz. Nicht angezeigte Elemente können auch im Arbeitsspeicher gespeichert werden, was weniger ideal ist.

Blazor-Server-Apps bieten ein ähnliches Programmiermodell für andere Benutzeroberflächen-Frameworks für Zustands behaftete apps, z. b. WPF, Windows Forms oder blazor Webassembly. Der Hauptunterschied besteht darin, dass in mehreren Benutzeroberflächen-Frameworks der von der APP genutzte Arbeitsspeicher zum Client gehört und nur den einzelnen Client beeinträchtigt. Beispielsweise wird eine blazor Webassembly-App vollständig auf dem Client ausgeführt und verwendet nur Client Speicherressourcen. Im Szenario des blazor-Servers gehört der von der APP belegte Arbeitsspeicher zum Server und wird von Clients auf der Server Instanz gemeinsam genutzt.

Serverseitige Speicheranforderungen sind bei allen blazor-Server-apps zu berücksichtigen. Die meisten Web-Apps sind jedoch zustandslos, und der während der Verarbeitung einer Anforderung verwendete Arbeitsspeicher wird freigegeben, wenn die Antwort zurückgegeben wird. Als allgemeine Empfehlung können Sie Clients nicht gestatten, eine nicht gebundene Speichermenge wie in einer anderen serverseitigen App zuzuordnen, die Clientverbindungen beibehält. Der von einer blazor-Server-App genutzte Arbeitsspeicher wird für einen längeren Zeitraum als eine einzelne Anforderung beibehalten.

> [!NOTE]
> Während der Entwicklung kann ein Profiler verwendet oder eine Ablauf Verfolgung erfasst werden, um die Speicheranforderungen von Clients zu bewerten. Ein Profiler oder eine Ablauf Verfolgung erfasst nicht den Arbeitsspeicher, der einem bestimmten Client zugeordnet ist. Um die Arbeitsspeicher Nutzung eines bestimmten Clients während der Entwicklung zu erfassen, erfassen Sie einen Dump, und untersuchen Sie die Speichernachfrage aller Objekte, die auf die Verbindung eines Benutzers basieren.

### <a name="client-connections"></a>Clientverbindungen

Die Verbindungs Erschöpfung kann auftreten, wenn ein oder mehrere Clients zu viele gleichzeitige Verbindungen mit dem Server öffnen, wodurch verhindert wird, dass andere Clients neue Verbindungen herstellen.

Blazor-Clients stellen eine einzelne Verbindung pro Sitzung her und lassen die Verbindung geöffnet, solange das Browserfenster geöffnet ist. Die Anforderungen auf dem Server, die alle Verbindungen aufrechtzuerhalten, sind nicht spezifisch für blazor-apps. Aufgrund der permanenten Natur der Verbindungen und der Zustands behafteten Natur von blazor-Server-Apps ist die Verbindungs Erschöpfung ein höheres Risiko für die Verfügbarkeit der app.

Standardmäßig gibt es keine Beschränkung für die Anzahl der Verbindungen pro Benutzer für eine blazor-Server-app. Wenn die APP eine Verbindungs Beschränkung erfordert, sollten Sie einen oder mehrere der folgenden Ansätze verwenden:

* Fordern Sie eine Authentifizierung an, die auf natürliche Weise die Fähigkeit von nicht autorisierten Benutzern für die Verbindung mit der APP einschränkt. Damit dieses Szenario effektiv ist, müssen Benutzer daran gehindert werden, neue Benutzer bei der Bereitstellung zu erhalten.
* Beschränken Sie die Anzahl der Verbindungen pro Benutzer. Das Einschränken von Verbindungen kann über die folgenden Ansätze erreicht werden. Achten Sie darauf, dass berechtigte Benutzer auf die App zugreifen können (z. b. Wenn ein Verbindungs Limit basierend auf der IP-Adresse des Clients hergestellt wird).
  * Auf App-Ebene:
    * Erweiterbarkeit des Endpunkt Routings.
    * Fordern Sie eine Authentifizierung zum Herstellen einer Verbindung mit der APP an, und verfolgen Sie die aktiven Sitzungen pro Benutzer nach.
    * Ablehnen neuer Sitzungen, wenn ein Grenzwert erreicht wird.
    * Proxys können mithilfe eines Proxys, z. b. mit dem [Azure signalr-Dienst](/azure/azure-signalr/signalr-overview) , der Verbindungen von Clients zu einer APP Multiplexing, WebSocket-Proxys mit einer APP Dadurch wird eine APP mit größerer Verbindungskapazität bereitgestellt, als von einem einzelnen Client festgelegt werden kann. Dadurch wird verhindert, dass ein Client die Verbindungen mit dem Server erschöpft.
  * Auf Serverebene: Verwenden Sie vor der APP einen Proxy/Gateway. Beispielsweise können Sie mit der [Azure-Front-Door](/azure/frontdoor/front-door-overview) das globale Routing von Webdatenverkehr in eine APP definieren, verwalten und überwachen.

## <a name="denial-of-service-dos-attacks"></a>DOS-Angriffe (Denial of Service)

DOS-Angriffe (Denial of Service) beinhalten einen Client, der bewirkt, dass der Server eine oder mehrere seiner Ressourcen abwehrt, wodurch die APP nicht mehr verfügbar ist. Blazor-Server-Apps enthalten einige Standard Limits und basieren auf anderen ASP.net Core-und signalr-Limits zum Schutz vor DoS-Angriffen:

| App-Limit für blazor-Server                            | BESCHREIBUNG | Standard |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Maximale Anzahl von Verbindungen, die von einem bestimmten Server gleichzeitig im Arbeitsspeicher enthalten sind. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Maximale Zeitspanne, für die eine getrennte Verbindung im Arbeitsspeicher gehalten wird, bevor Sie abgebrochen wird. | 3 Minuten |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Maximale Zeitspanne, die der Server wartet, bevor ein Timeout für einen asynchronen JavaScript-Funktionsaufruf auftritt. | 1 Minute |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Maximale Anzahl nicht bestätigter renderbatches, die der Server zu einem gegebenen Zeitpunkt pro Verbindung aufbewahrt, um eine robuste erneute Verbindung zu unterstützen. Nach Erreichen des Limits beendet der Server die Erstellung neuer Rendering-Batches, bis mindestens ein Stapel vom Client bestätigt wurde. | 10 |


| Signalr-und ASP.net Core Limit             | BESCHREIBUNG | Standard |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Nachrichtengröße für eine einzelne Nachricht. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interaktionen mit dem Browser (Client)

Ein Client interagiert mit dem Server über die JS-Interop-Ereignis Verteilung und-Rendering. Die Kommunikation zwischen js und Interop erfolgt in beide Richtungen zwischen JavaScript und .net:

* Browser Ereignisse werden asynchron vom Client an den Server gesendet.
* Der Server antwortet, dass die Benutzeroberfläche bei Bedarf asynchron erneut ausgeführt wird.

### <a name="javascript-functions-invoked-from-net"></a>Aus .net aufgerufene JavaScript-Funktionen

Für Aufrufe von .NET-Methoden an JavaScript:

* Alle Aufrufe haben einen konfigurierbaren Timeout, nach dem Sie fehlschlagen, <xref:System.OperationCanceledException> und geben einen an den Aufrufer zurück.
  * Es gibt ein Standard Timeout für die Aufrufe (`CircuitOptions.JSInteropDefaultCallTimeout`) von einer Minute. Informationen zum Konfigurieren dieses Limits finden <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>Sie unter.
  * Es kann ein Abbruch Token bereitgestellt werden, um den Abbruch pro Abruf zu steuern. Verlassen Sie sich nach Möglichkeit auf das standardmäßige Aufruf Timeout und einen zeitgebundenen Aufruf an den Client, wenn ein Abbruch Token bereitgestellt wird.
* Das Ergebnis eines JavaScript-Aufrufes kann nicht vertrauenswürdig sein. Der Blazor App-Client, der im Browser ausgeführt wird, sucht die JavaScript-Funktion, die aufgerufen werden soll. Die-Funktion wird aufgerufen, und entweder wird das Ergebnis oder ein Fehler erzeugt. Ein böswilliger Client kann versuchen, Folgendes zu tun:
  * Ein Problem in der APP wird verursacht, indem ein Fehler von der JavaScript-Funktion zurückgegeben wird.
  * Bewirken Sie ein unbeabsichtigtes Verhalten auf dem Server, indem Sie ein unerwartetes Ergebnis der JavaScript-Funktion zurückgeben.

Treffen Sie die folgenden Vorsichtsmaßnahmen, um vor den vorangehenden Szenarien zu schützen:

* Umschließen von JS-Interop-aufrufen innerhalb von [try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Anweisungen, um Fehler zu berücksichtigen, die während der Aufrufe auftreten können. Weitere Informationen finden Sie unter <xref:blazor/handle-errors#javascript-interop>.
* Überprüfen Sie die von den js-Interop-Aufrufen zurückgegebenen Daten, einschließlich der Fehlermeldungen, bevor Sie eine Aktion ausführen.

### <a name="net-methods-invoked-from-the-browser"></a>Vom Browser aufgerufene .NET-Methoden

Keine Vertrauenswürdigkeit von Aufrufen von JavaScript zu .NET-Methoden. Wenn eine .NET-Methode für JavaScript verfügbar gemacht wird, sollten Sie Bedenken, wie die .NET-Methode aufgerufen wird:

* Behandeln Sie jede .NET-Methode, die für JavaScript verfügbar gemacht wird, wie ein öffentlicher Endpunkt der app.
  * Überprüfen der Eingabe.
    * Stellen Sie sicher, dass die Werte in den erwarteten Bereichen liegen
    * Stellen Sie sicher, dass der Benutzer über die Berechtigung verfügt, die angeforderte Aktion auszuführen.
  * Weisen Sie im Rahmen des .NET-Methoden Auffüllens keine übermäßige Menge von Ressourcen zu. Führen Sie z. b. Prüfungen durch und platzieren Sie Grenzwerte für CPU-und Speicherauslastung.
  * Berücksichtigen Sie, dass statische Methoden und Instanzmethoden für JavaScript-Clients verfügbar gemacht werden können. Vermeiden Sie das Freigeben des Zustands Sitzungs übergreifend, es sei denn, der Entwurf erfordert den Freigabe Zustand mit entsprechenden Einschränkungen.
    * Für Instanzmethoden, `DotNetReference` die durch Objekte verfügbar gemacht werden, die ursprünglich über Abhängigkeitsinjektion (di) erstellt wurden, sollten die Objekte als Bereichs bezogene Objekte registriert werden. Dies gilt für jeden di-Dienst, Blazor der von der Server-App verwendet wird.
    * Vermeiden Sie bei statischen Methoden das Einrichten des Zustands, der nicht auf den Client beschränkt werden kann, es sei denn, die APP teilt den Status nicht explizit für alle Benutzer einer Serverinstanz frei.
  * Vermeiden Sie das Übergeben von benutzerdefinierten Daten in Parameter an JavaScript-Aufrufe. Wenn die Übergabe von Daten in Parametern wirklich erforderlich ist, müssen Sie sicherstellen, dass der JavaScript-Code die Übergabe der Daten ohne die Einführung von [XSS-Sicherheitsrisiken (Cross-Site Scripting](#cross-site-scripting-xss) Schreiben Sie z. b. keine vom Benutzer bereitgestellten Daten in die Dokumentobjektmodell (DOM) `innerHTML` , indem Sie die-Eigenschaft eines-Elements festlegen. Verwenden Sie die [Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) , um und andere unsichere JavaScript-Primitive zu deaktivieren `eval` .
* Vermeiden Sie die Implementierung der benutzerdefinierten Verteilung von .net-aufrufen zusätzlich zur Verteiler Implementierung des Frameworks. Das verfügbar machen von .NET-Methoden für den Browser ist ein erweitertes Szenario, Blazor das für die allgemeine Entwicklung nicht empfohlen wird.

### <a name="events"></a>Events

Ereignisse stellen einen Einstiegspunkt für eine Blazor Server-App bereit. Die gleichen Regeln für das Schützen von Endpunkten in Web-Apps gelten für Blazor die Ereignis Behandlung in Server-apps. Ein böswilliger Client kann alle Daten senden, die er als Nutzlast für ein Ereignis senden möchte.

Zum Beispiel:

* Ein Änderungs Ereignis für einen `<select>` kann einen Wert senden, der nicht innerhalb der Optionen liegt, die die APP dem Client vorgelegt hat.
* Ein `<input>` kann beliebige Textdaten an den Server senden, wobei die Client seitige Validierung umgangen wird.

Die APP muss die Daten für jedes Ereignis validieren, das die APP behandelt. Die Blazor Framework [Forms-Komponenten](xref:blazor/forms-validation) führen grundlegende Überprüfungen durch. Wenn die APP benutzerdefinierte Formular Komponenten verwendet, muss benutzerdefinierter Code geschrieben werden, um die Ereignisdaten entsprechend zu validieren.

BlazorServer Ereignisse sind asynchron, sodass mehrere Ereignisse an den Server weitergeleitet werden können, bevor die app durch die Erstellung eines neuen Rendering Zeit zum reagieren hat. Dies hat einige Sicherheitsaspekte zu bedenken. Das Einschränken von Client Aktionen in der APP muss innerhalb von Ereignis Handlern erfolgen und ist nicht vom aktuellen gerenderten Ansichts Zustand abhängig.

Stellen Sie sich eine-Counter-Komponente vor, die es einem Benutzer ermöglicht, einen-Wert maximal dreimal zu erhöhen. Die Schaltfläche zum Erhöhen des Zählers basiert bedingt auf dem Wert von `count`:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Ein Client kann ein oder mehrere Inkrement-Ereignisse verteilen, bevor das Framework ein neues Rendering dieser Komponente erzeugt. Das Ergebnis ist, dass `count` der durch den Benutzer *über drei Male* erhöht werden kann, da die Schaltfläche nicht schnell genug von der Benutzeroberfläche entfernt wird. Im folgenden Beispiel wird die richtige Methode zum erreichen `count` des Limits von drei Inkrementen angezeigt:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Durch Hinzufügen `if (count < 3) { ... }` der Überprüfung innerhalb des Handlers basiert die Entscheidung `count` für die Inkrement auf dem aktuellen App-Zustand. Die Entscheidung basiert nicht auf dem Status der Benutzeroberfläche, wie es im vorherigen Beispiel der Fall war, das möglicherweise vorübergehend veraltet ist.

### <a name="guard-against-multiple-dispatches"></a>Schutz vor mehreren sendet

Wenn ein Ereignis Rückruf einen Vorgang mit langer Ausführungszeit asynchron aufruft (z. b. das Abrufen von Daten aus einem externen Dienst oder einer externen Datenbank), sollten Sie einen Wächter verwenden. Der Wächter kann verhindern, dass der Benutzer mehrere Vorgänge in die Warteschlange eingereiht, während der Vorgang durch visuelles Feedback ausgeführt wird. Mit dem folgenden Komponenten Code `isLoading` wird `true` auf `GetForecastAsync` festgelegt, während Daten vom Server abgerufen werden. Wenn `isLoading` gleich `true`ist, wird die Schaltfläche in der Benutzeroberfläche deaktiviert:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Das im vorangehenden Beispiel gezeigte Wächter Muster funktioniert, wenn der Hintergrund Vorgang mit dem `async` - `await` Muster asynchron ausgeführt wird.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Früh abbrechen und Verwendung nach dem verwerfen vermeiden

Zusätzlich zur Verwendung eines Wächter, wie im Abschnitt [Schutz vor mehreren](#guard-against-multiple-dispatches) Verteilungen beschrieben, empfiehlt es sich, <xref:System.Threading.CancellationToken> mithilfe von einen Vorgang mit langer Ausführungszeit abzubrechen, wenn die Komponente verworfen wird. Dieser Ansatz bietet den zusätzlichen Vorteil, dass die Verwendung von " *Use-After-* verwerfen" in Komponenten vermieden wird:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Vermeiden von Ereignissen, die große Datenmengen verursachen

Einige DOM-Ereignisse, wie `oninput` z `onscroll`. b. oder, können eine große Datenmenge verursachen. Vermeiden Sie die Verwendung dieser Blazor Ereignisse in Server-apps.

## <a name="additional-security-guidance"></a>Zusätzliche Sicherheits Leit Fäden

Die Richtlinien zum Sichern von ASP.net Core- Blazor apps gelten für Server-apps und werden in den folgenden Abschnitten behandelt:

* [Protokollierung und sensible Daten](#logging-and-sensitive-data)
* [Schützen von Informationen während der Übertragung mit HTTPS](#protect-information-in-transit-with-https)
* [Cross-Site Scripting (XSS)](#cross-site-scripting-xss))
* [Cross-Origin-Schutz](#cross-origin-protection)
* [Click-Jacking](#click-jacking)
* [Offene Umleitungen](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Protokollierung und sensible Daten

Die JS-Interop-Interaktionen zwischen Client und Server werden in den Protokollen des Servers <xref:Microsoft.Extensions.Logging.ILogger> mit-Instanzen aufgezeichnet. Blazorvermeidet das Protokollieren sensibler Informationen, z. b. tatsächlicher Ereignisse oder JS-Interop-Eingaben und-Ausgaben

Wenn auf dem Server ein Fehler auftritt, wird der Client vom Framework benachrichtigt, und die Sitzung wird abgebrochen. Standardmäßig erhält der Client eine allgemeine Fehlermeldung, die in den Entwicklertools des Browsers angezeigt wird.

Der Client seitige Fehler enthält nicht die Aufruf Liste und bietet keine Details zur Ursache des Fehlers, aber Server Protokolle enthalten diese Informationen. Zu Entwicklungszwecken können dem Client vertrauliche Fehlerinformationen zur Verfügung gestellt werden, indem ausführliche Fehler aktiviert werden.

Ausführliche Fehler ermöglichen:

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`Konfigurationsschlüssel. Legen Sie beispielsweise die `ASPNETCORE_DETAILEDERRORS` -Umgebungsvariable auf den Wert `true`fest.

> [!WARNING]
> Das verfügbar machen von Fehlerinformationen für Clients im Internet stellt ein Sicherheitsrisiko dar, das immer vermieden werden sollte.

### <a name="protect-information-in-transit-with-https"></a>Schützen von Informationen während der Übertragung mit HTTPS

BlazorDer Server SignalR verwendet für die Kommunikation zwischen dem Client und dem Server. BlazorDer Server verwendet normalerweise den SignalR Transport, der in der Regel websockets aushandiert.

BlazorDer Server gewährleistet nicht die Integrität und Vertraulichkeit der Daten, die zwischen dem Server und dem Client gesendet werden. Verwenden Sie immer HTTPS.

### <a name="cross-site-scripting-xss"></a>Cross-Site Scripting (XSS)

Cross-Site Scripting (XSS) ermöglicht es einer nicht autorisierten Partei, beliebige Logik im Kontext des Browsers auszuführen. Eine kompromittierte app könnte ggf. beliebigen Code auf dem Client ausführen. Das Sicherheitsrisiko kann verwendet werden, um potenziell eine Reihe schädlicher Aktionen für den Server auszuführen:

* Senden Sie gefälschte/ungültige Ereignisse an den Server.
* Dispatchvervollständigung/ungültige Rendering-Vervollständigung.
* Vermeiden Sie das Verteilen von Rendering-Vervollständigungen.
* Dispatchinterop-Aufrufe von JavaScript an .net.
* Ändern Sie die Antwort von Interop-Aufrufen von .net auf JavaScript.
* Vermeiden Sie die Verteilung von .net an JS-Interop-Ergebnisse.

Das Blazor Server Framework führt Maßnahmen zum Schutz vor einigen der vorangegangenen Bedrohungen aus:

* Beendet das Generieren neuer UI-Updates, wenn der Client keine Rendering-Batches anerkennt. Konfiguriert mit `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.
* Gibt ein Timeout für einen beliebigen .net-zu-JavaScript-Aufrufe nach einer Minute aus, ohne eine Antwort vom Client zu erhalten Konfiguriert mit `CircuitOptions.JSInteropDefaultCallTimeout`.
* Führt bei der JS-Interop eine grundlegende Validierung für alle Eingaben aus, die vom Browser stammen:
  * .Net-Verweise sind gültig und vom Typ, der von der .NET-Methode erwartet wird.
  * Die Daten sind nicht falsch formatiert.
  * Die richtige Anzahl von Argumenten für die-Methode ist in der Nutzlast vorhanden.
  * Die Argumente oder das Ergebnis können ordnungsgemäß deserialisiert werden, bevor die-Methode aufgerufen wird.
* Führt eine grundlegende Validierung in allen Eingaben aus, die aus dem Browser von verteilten Ereignissen stammen:
  * Das Ereignis hat einen gültigen Typ.
  * Die Daten für das Ereignis können deserialisiert werden.
  * Dem Ereignis ist ein Ereignishandler zugeordnet.

Zusätzlich zu den Sicherheitsvorkehrungen, die das Framework implementiert, muss die APP vom Entwickler codiert werden, um Bedrohungen vor Bedrohungen zu schützen und geeignete Maßnahmen zu ergreifen:

* Validieren Sie Daten immer, wenn Ereignisse behandelt werden.
* Ergreifen Sie die entsprechende Aktion, wenn Sie ungültige Daten empfangen
  * Ignorieren Sie die Daten, und geben Sie zurück. Dadurch kann die APP die Verarbeitung von Anforderungen fortsetzen.
  * Wenn die APP ermittelt, dass die Eingabe unrechtmäßig ist und nicht von einem legitimen Client erstellt werden konnte, lösen Sie eine Ausnahme aus. Wenn eine Ausnahme ausgelöst wird, wird die Verbindung abgebrochen, und die Sitzung wird beendet.
* Vertrauen Sie nicht der Fehlermeldung, die von den in den Protokollen enthaltenen Rendering-Batch-Vervollständigungen bereitgestellt wird Der Fehler wird vom Client bereitgestellt und kann im Allgemeinen nicht als vertrauenswürdig eingestuft werden, da der Client möglicherweise gefährdet ist.
* Vertrauen Sie der Eingabe für JS-Interop-Aufrufe in keiner Richtung zwischen den JavaScript-und .NET-Methoden.
* Die APP ist dafür verantwortlich, zu überprüfen, ob der Inhalt von Argumenten und Ergebnissen gültig ist, auch wenn die Argumente oder Ergebnisse ordnungsgemäß deserialisiert werden.

Damit ein XSS-Sicherheitsrisiko vorhanden ist, muss die APP Benutzereingaben auf der gerenderten Seite einbinden. BlazorServer Komponenten führen einen Kompilierzeit Schritt aus, bei dem das Markup in einer *Razor* -Datei in prozedurale c#-Logik transformiert wird. Zur Laufzeit erstellt die c#-Logik eine *renderbaum* Struktur, die die Elemente, den Text und die untergeordneten Komponenten beschreibt. Dies wird über eine Sequenz von JavaScript-Anweisungen auf das DOM des Browsers angewendet (oder wird bei der vorab Generierung in HTML serialisiert):

* Benutzereingaben, die über normale Razor-Syntax gerendert `@someStringValue`werden (z. b.), machen keine XSS-Sicherheits Anfälligkeit verfügbar, da die Razor-Syntax dem DOM über Befehle hinzugefügt wird, die nur Text schreiben können. Auch wenn der Wert HTML-Markup enthält, wird der Wert als statischer Text angezeigt. Bei der vorab Generierung ist die Ausgabe HTML-codiert, wodurch der Inhalt auch als statischer Text angezeigt wird.
* Skript Tags sind nicht zulässig und sollten nicht in die Komponenten-Rendering-Struktur der APP eingeschlossen werden. Wenn ein Skripttag im Markup einer Komponente enthalten ist, wird ein Kompilierzeitfehler generiert.
* Komponenten Autoren können Komponenten in c# erstellen, ohne Razor zu verwenden. Der Komponenten Autor ist für die Verwendung der richtigen APIs verantwortlich, wenn die Ausgabe ausgegeben wird. Verwenden `builder.AddContent(0, someUserSuppliedString)` Sie z. b. und *nicht* `builder.AddMarkupContent(0, someUserSuppliedString)`, da letztere ein XSS-Sicherheitsrisiko erzeugen könnte.

Im Rahmen des Schutzes vor XSS-Angriffen sollten Sie die Implementierung von XSS-entschärfungen in Erwägung gezogen werden, z. b. [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)

Weitere Informationen finden Sie unter <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Cross-Origin-Schutz

Cross-Origin-Angriffe betreffen einen Client von einem anderen Ursprung, der eine Aktion auf dem Server ausführt. Bei der bösartigen Aktion handelt es sich in der Regel um eine GET-Anforderung oder eine Formular Bereitstellung (Website übergreifende Anforderungs Fälschung, CSRF), aber auch das Öffnen eines bösartigen websockets ist möglich. BlazorServer-Apps bieten [dieselbe Garantie, dass jede SignalR andere APP, die das Hub-Protokoll verwendet,](xref:signalr/security)Folgendes bietet:

* BlazorDer Zugriff auf Server-apps kann über-und Ursprungs übergreifend erfolgen, es sei denn, es werden weitere Measures zum verhindern Wenn Sie den Ursprungs übergreifenden Zugriff deaktivieren möchten, deaktivieren Sie cors im Endpunkt, indem Sie die cors-Middleware zur Pipeline hinzu `DisableCorsAttribute` fügen und Blazor den Endpunkt Metadaten hinzufügen oder den Satz zulässiger Ursprünge einschränken, indem Sie [für die Ursprungs übergreifende Ressourcen Freigabe konfigurieren SignalR ](xref:signalr/security#cross-origin-resource-sharing).
* Wenn cors aktiviert ist, sind möglicherweise zusätzliche Schritte erforderlich, um die APP abhängig von der cors-Konfiguration zu schützen. Wenn cors global aktiviert ist, kann cors für den Blazor serverhub deaktiviert werden, indem `DisableCorsAttribute` die Metadaten nach dem Aufruf `hub.MapBlazorHub()`von den Endpunkt Metadaten hinzugefügt werden.

Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Click-Jacking

Click-Jacking umfasst das Rendern einer Website `<iframe>` als innerhalb einer Site von einem anderen Ursprung aus, um den Benutzer zur Durchführung von Aktionen auf der Site zu bringen, die angegriffen wird.

Um eine APP vor dem Rendering innerhalb einer `<iframe>`zu schützen, verwenden Sie die [Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) und den `X-Frame-Options` -Header. Weitere Informationen finden Sie unter [MDN-Webdokumentation: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Offene Umleitungen

Wenn eine Blazor Server-App-Sitzung gestartet wird, führt der Server eine grundlegende Überprüfung der URLs aus, die als Teil des Starts der Sitzung gesendet werden. Das Framework überprüft, ob die Basis-URL ein übergeordnetes Element der aktuellen URL ist, bevor die Verbindung hergestellt wird. Vom Framework werden keine weiteren Überprüfungen durchgeführt.

Wenn ein Benutzer einen Link auf dem Client auswählt, wird die URL für den Link an den Server gesendet, der bestimmt, welche Aktion ausgeführt werden soll. Die APP kann z. b. eine Client seitige Navigation ausführen oder im Browser angeben, dass Sie zum neuen Speicherort wechseln soll.

Komponenten können Navigationsanforderungen auch Programm gesteuert durch die Verwendung von `NavigationManager`auslöst. In solchen Szenarien führt die APP möglicherweise eine Client seitige Navigation aus oder zeigt dem Browser an, dass Sie zum neuen Speicherort wechseln soll.

Komponenten müssen Folgendes ausführen:

* Vermeiden Sie die Verwendung von Benutzereingaben als Teil der navigationcallgumente.
* Überprüfen Sie die Argumente, um sicherzustellen, dass das Ziel von der APP zugelassen wird.

Andernfalls kann ein böswilliger Benutzer erzwingen, dass der Browser an eine von Angreifern gesteuerte Website weitergeleitet wird. In diesem Szenario macht der Angreifer eine Tricks für die APP aus, um einige Benutzereingaben als Teil des Aufruf der `NavigationManager.Navigate` Methode zu verwenden.

Diese Empfehlung gilt auch für das Rendern von Verknüpfungen als Teil der APP:

* Verwenden Sie nach Möglichkeit relative Links.
* Überprüfen Sie, ob absolute Linkziele gültig sind, bevor Sie Sie in eine Seite einschließen.

Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.

## <a name="security-checklist"></a>Checkliste für die Sicherheit

Die folgende Liste mit Sicherheitsüberlegungen ist nicht umfassend:

* Überprüfen Sie die Argumente von Ereignissen.
* Überprüfen von Eingaben und Ergebnissen aus js-Interop-aufrufen.
* Vermeiden Sie die Verwendung der Benutzereingaben für .net-und JS-Interop-Aufrufe (oder überprüfen Sie vorher)
* Verhindern, dass der Client eine nicht gebundene Arbeitsspeicher Menge zuordnet.
  * Daten innerhalb der Komponente.
  * `DotNetObject`Verweise, die an den Client zurückgegeben werden.
* Schutz vor mehreren Dispatches.
* Abbrechen von Vorgängen mit langer Ausführungszeit, wenn die Komponente verworfen wird.
* Vermeiden Sie Ereignisse, die große Datenmengen verursachen.
* Vermeiden Sie die Verwendung von Benutzereingaben als Teil `NavigationManager.Navigate` von Aufrufen von, und überprüfen Sie Benutzereingaben für URLs mit einem Satz zulässiger Ursprünge zuerst, wenn dies unvermeidlich ist.
* Treffen Sie keine Autorisierungs Entscheidungen auf Grundlage des Status der Benutzeroberfläche, sondern nur aus dem Komponenten Zustand.
* Verwenden Sie die [Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) zum Schutz vor XSS-Angriffen.
* Verwenden Sie ggf. CSP und [X-Frame-Optionen](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) zum Schutz vor Click-Jacking.
* Stellen Sie sicher, dass cors-Einstellungen geeignet sind, wenn Sie cors Blazor aktivieren oder cors für apps explizit deaktivieren.
* Testen Sie, um sicherzustellen, dass die serverseitigen Blazor Grenzwerte für die APP eine akzeptable Benutzerfunktion ohne akzeptable Risikostufen bieten.
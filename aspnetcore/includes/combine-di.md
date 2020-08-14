<a name="csc"></a>

## <a name="combining-service-collection"></a>Kombinieren von Dienstsammlungen

Die `ConfigureServices`-Methode enthält mehrere Dienstsammlungen:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Ähnliche Registrierungsgruppen können in eine Erweiterungsmethode verschoben werden, um Dienste zu registrieren. Die Konfigurationsdienste werden beispielsweise folgender Klasse hinzugefügt:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

Die verbleibenden Dienste werden in einer ähnlichen Klasse registriert. Die folgende `ConfigureServices`-Methode verwendet die neuen Erweiterungsmethoden, um die Dienste zu registrieren:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Hinweis***: Jede `services.Add{SERVICE_NAME}`-Erweiterungsmethode fügt Dienste hinzu und konfiguriert diese möglicherweise. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> fügt beispielsweise die Dienste hinzu, die MVC-Controller mit Ansichten benötigen. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> fügt die Dienste hinzu, die Razor Pages benötigt. Es wird empfohlen, dass Apps dieser Namenskonvention folgen. Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.
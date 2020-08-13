---
title: ASP.NET Core-Web-API-Hilfeseiten mit Swagger/OpenAPI
author: RicoSuter
description: Dieses Tutorial enthält eine exemplarische Vorgehensweise für das Hinzufügen von Swagger, um Dokumentationen und Hilfeseiten für eine Web-API-App zu generieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: a3b8efcdb1f196fc878e0ad29580d305bb24f50a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021977"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>ASP.NET Core-Web-API-Hilfeseiten mit Swagger/OpenAPI

Von [Christoph Nienaber](https://twitter.com/zuckerthoben) und [Rico Suter](https://blog.rsuter.com/)

Beim Verwenden einer Web-API ist es für einen Entwickler oft nicht einfach, die zahlreichen verschiedenen Methoden zu verstehen. Das Generieren von nützlichen Dokumentationen und Hilfeseiten für Web-APIs kann ein Problem darstellen. Dieses lässt sich mit [Swagger](https://swagger.io/) lösen – auch als [OpenAPI](https://www.openapis.org/) bekannt. Das Programm bietet Vorteile wie die interaktive Dokumentation, die Generierung von Client SDKs und die Erkennbarkeit von APIs.

In diesem Artikel werden die Swagger-Implementierungen [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) und [NSwag](https://github.com/RicoSuter/NSwag) von .NET veranschaulicht:

* **Swashbuckle.AspNetCore** ist ein Open Source-Projekt zum Generieren von Swagger-Dokumenten für Web-APIs von ASP.NET Core.

* **NSwag** ist ein weiteres Open-Source-Projekt zum Generieren von Swagger-Dokumenten und Integrieren von [Swagger-Benutzeroberfläche](https://swagger.io/swagger-ui/) oder [ReDoc](https://github.com/Rebilly/ReDoc) in ASP.NET Core-Web-APIs. Außerdem bietet NSwag Verfahren, um C#- und TypeScript-Clientcode für Ihre API zu generieren.

## <a name="what-is-swagger--openapi"></a>Was ist Swagger bzw. OpenAPI?

Bei Swagger handelt es sich um eine sprachunabhängige Spezifikation für das Beschreiben von [REST-APIs](https://en.wikipedia.org/wiki/Representational_state_transfer). Das Swagger-Projekt wurde an die [OpenAPI Initiative](https://www.openapis.org/) übergeben. Dort wird es nun als „OpenAPI“ bezeichnet. Beide Namen werden austauschbar verwendet, jedoch wird „OpenAPI“ bevorzugt. Dadurch können Computer und Benutzer die Funktionen eines Diensts ohne direkten Zugriff auf die Implementierung (Quellcode, Netzwerkzugriff, Dokumentation) nachvollziehen. Ein Ziel besteht im Minimieren des Arbeitsaufwands, der zum Verbinden von getrennten Diensten erforderlich ist. Ein weiteres Ziel besteht darin, den Zeitaufwand zu verringern, der für die genaue Dokumentation eines Diensts erforderlich ist.

## <a name="openapi-specification-openapijson"></a>OpenAPI-Spezifikation (openapi.json)

Im Zentrum des OpenAPI-Flows steht die Spezifikation – diese ist standardmäßig ein Dokument namens *openapi.json*. Je nachdem, welchen Dienst Sie verwenden, wird dieses Dokument von der OpenAPI-Toolkette oder von einer Drittanbieterimplementierung dieser Kette generiert. Es beschreibt die Funktionen Ihrer API und wie auf diese mit HTTP zugegriffen werden kann. Es führt die Swagger-Benutzeroberfläche aus und wird von der Toolkette verwendet, um die Ermittlung und die Generierung von Clientcode zu aktivieren. Hier finden Sie ein Beispiel der OpenAPI-Spezifikation, das aus Gründen der Übersichtlichkeit reduziert wurde:

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a>Swagger-Benutzeroberfläche

Die [Swagger-Benutzeroberfläche](https://swagger.io/swagger-ui/) ist eine webbasierte Benutzeroberfläche, die anhand der generierten OpenAPI-Spezifikation Informationen über den Dienst bereitstellt. Swashbuckle und NSwag enthalten eine eingebettete Version der Swagger-Benutzeroberfläche, sodass diese in Ihrer ASP.NET Core-App mithilfe eines Registrierungsaufrufs für die Middleware gehostet werden kann. Die Webbenutzeroberfläche sieht folgendermaßen aus:

![Swagger-Benutzeroberfläche](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Jede öffentliche Aktionsmethode in Ihren Controllern kann über die Benutzeroberfläche getestet werden. Klicken Sie auf einen Methodennamen, um den Abschnitt zu erweitern. Fügen Sie die erforderlichen Parameter hinzu, und klicken Sie auf **Probieren Sie es aus!** .

![Beispiel für einen Swagger-GET-Test](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Die für den Screenshot verwendete Version der Swagger-Benutzeroberfläche ist Version 2. Ein Beispiel für Version 3 finden Sie unter [Pet store example (Beispiel für eine Tierhandlung)](https://petstore.swagger.io/).

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Erste Schritte mit NSwag](xref:tutorials/get-started-with-nswag)

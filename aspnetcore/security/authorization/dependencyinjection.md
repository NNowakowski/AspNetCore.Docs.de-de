---
title: Abhängigkeitsinjektion in Anforderungs Handlern in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungs Anforderungs Handler mithilfe von Abhängigkeitsinjektion in eine ASP.net Core-App einfügen.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 4bc7eb38262c8a94a84aacc978737a778bfd71a1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632563"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Abhängigkeitsinjektion in Anforderungs Handlern in ASP.net Core

<a name="security-authorization-di"></a>

[Autorisierungs Handler müssen](xref:security/authorization/policies#handler-registration) während der Konfiguration in der Dienst Auflistung registriert werden (mithilfe von [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)).

Angenommen, Sie hatten ein Repository von Regeln, die Sie innerhalb eines Autorisierungs Handlers auswerten wollten, und dieses Repository wurde in der Dienst Sammlung registriert. Die Autorisierung wird aufgelöst und in den Konstruktor eingefügt.

Wenn Sie z. b. asp verwenden möchten. Die Protokollierungs Infrastruktur von NET, die Sie `ILoggerFactory` in ihren Handler einfügen möchten. Ein solcher Handler könnte wie folgt aussehen:

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

Sie registrieren den Handler wie folgt `services.AddSingleton()` :

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Eine Instanz des-Handlers wird erstellt, wenn die Anwendung gestartet wird, und di fügt die registrierte `ILoggerFactory` in ihren Konstruktor ein.

> [!NOTE]
> Handler, die Entity Framework verwenden, sollten nicht als Singletons registriert werden.

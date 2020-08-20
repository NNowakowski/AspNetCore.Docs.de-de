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
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="de208-103">Abhängigkeitsinjektion in Anforderungs Handlern in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="de208-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="de208-104">[Autorisierungs Handler müssen](xref:security/authorization/policies#handler-registration) während der Konfiguration in der Dienst Auflistung registriert werden (mithilfe von [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="de208-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="de208-105">Angenommen, Sie hatten ein Repository von Regeln, die Sie innerhalb eines Autorisierungs Handlers auswerten wollten, und dieses Repository wurde in der Dienst Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="de208-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="de208-106">Die Autorisierung wird aufgelöst und in den Konstruktor eingefügt.</span><span class="sxs-lookup"><span data-stu-id="de208-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="de208-107">Wenn Sie z. b. asp verwenden möchten. Die Protokollierungs Infrastruktur von NET, die Sie `ILoggerFactory` in ihren Handler einfügen möchten.</span><span class="sxs-lookup"><span data-stu-id="de208-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="de208-108">Ein solcher Handler könnte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="de208-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="de208-109">Sie registrieren den Handler wie folgt `services.AddSingleton()` :</span><span class="sxs-lookup"><span data-stu-id="de208-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="de208-110">Eine Instanz des-Handlers wird erstellt, wenn die Anwendung gestartet wird, und di fügt die registrierte `ILoggerFactory` in ihren Konstruktor ein.</span><span class="sxs-lookup"><span data-stu-id="de208-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="de208-111">Handler, die Entity Framework verwenden, sollten nicht als Singletons registriert werden.</span><span class="sxs-lookup"><span data-stu-id="de208-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>

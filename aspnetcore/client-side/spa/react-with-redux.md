---
title: Verwenden der React-Redux-Projektvorlage mit ASP.NET Core
author: SteveSandersonMS
description: Erfahren Sie, wie Sie sich mit der Projektvorlage für die Einzelseitenanwendung (Single-Page Application, SPA) von ASP.NET Core für React-Redux und create-react-app vertraut machen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
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
uid: spa/react-with-redux
ms.openlocfilehash: ac5b5d7161e79f133a1f107251fa6707b752c568
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628728"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="664b3-103">Verwenden der React-Redux-Projektvorlage mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="664b3-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="664b3-104">Die aktualisierte React-Redux-Projektvorlage stellt einen geeigneten Anfangspunkt für ASP.NET Core-Apps dar, die React-, Redux- und CRA-Konventionen ([create-react-app](https://github.com/facebookincubator/create-react-app)) für die Implementierung einer umfangreichen, clientseitigen Benutzerschnittstelle (User Interface, UI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="664b3-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="664b3-105">Mit Ausnahme des Befehls für die Projekterstellung sind sämtliche Informationen zur React-Redux-Vorlage mit denen zur React-Vorlage identisch.</span><span class="sxs-lookup"><span data-stu-id="664b3-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="664b3-106">Führen Sie zum Erstellen dieses Projekttyps `dotnet new reactredux` anstelle von `dotnet new react` aus.</span><span class="sxs-lookup"><span data-stu-id="664b3-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="664b3-107">Weitere Informationen zu den Funktionen beider React-basierten Vorlagen finden Sie in der [Dokumentation zu React-Vorlagen](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="664b3-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="664b3-108">Informationen zum Konfigurieren einer untergeordneten React-Redux-Anwendung in IIS finden Sie unter [ReactRedux-Vorlage 2.1: SPA konnte nicht unter IIS verwendet werden (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="664b3-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>

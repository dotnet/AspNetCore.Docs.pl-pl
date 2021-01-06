---
title: Użyj szablonu projektu reakcję na Redux z ASP.NET Core
author: SteveSandersonMS
description: Dowiedz się, jak rozpocząć pracę z szablonem projektu aplikacji jednostronicowej (SPA, ASP.NET Core single page) w celu reagowania na aplikacje Redux i Create-reaguje na.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
no-loc:
- appsettings.json
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
ms.openlocfilehash: 9ae96b14f3f50d4079933bbd893ff95fa677d273
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054505"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="71c63-103">Użyj szablonu projektu reakcję na Redux z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71c63-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="71c63-104">Zaktualizowany szablon projektu reagowanie na Redux zapewnia wygodny punkt początkowy dla aplikacji ASP.NET Core przy użyciu konwencji reagowania, Redux i Create-resideing [-App](https://github.com/facebookincubator/create-react-app) (CRA) w celu zaimplementowania rozbudowanego interfejsu użytkownika po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="71c63-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="71c63-105">Z wyjątkiem polecenia tworzenia projektu wszystkie informacje o szablonie reakcję na Redux są takie same jak szablon reakcję.</span><span class="sxs-lookup"><span data-stu-id="71c63-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="71c63-106">Aby utworzyć ten typ projektu, uruchom polecenie `dotnet new reactredux` zamiast `dotnet new react` .</span><span class="sxs-lookup"><span data-stu-id="71c63-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="71c63-107">Aby uzyskać więcej informacji o funkcjach wspólnych dla obu szablonów opartych na reakcji, zobacz [dokumentację szablonu](xref:spa/react)dotyczącej reakcji.</span><span class="sxs-lookup"><span data-stu-id="71c63-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="71c63-108">Aby uzyskać informacje na temat konfigurowania aplikacji podrzędnej reagowanie na Redux w usługach IIS, zobacz [ReactRedux Template 2,1: nie można użyć Spa na serwerze IIS (ASPNET/tworzenia szablonów &num; 555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="71c63-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>

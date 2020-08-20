---
title: Co nowego w ASP.NET Core 1,1
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 1,1.
ms.author: riande
ms.date: 12/18/2018
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
uid: aspnetcore-1.1
ms.openlocfilehash: 1068307dc792ffb8def4f0652de497f666d4318d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634790"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="9b054-103">Co nowego w ASP.NET Core 1,1</span><span class="sxs-lookup"><span data-stu-id="9b054-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="9b054-104">ASP.NET Core 1,1 zawiera następujące nowe funkcje:</span><span class="sxs-lookup"><span data-stu-id="9b054-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="9b054-105">Oprogramowanie pośredniczące ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="9b054-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="9b054-106">Oprogramowanie pośredniczące buforowania odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="9b054-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="9b054-107">Wyświetlanie składników jako pomocników tagów</span><span class="sxs-lookup"><span data-stu-id="9b054-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="9b054-108">Oprogramowanie pośredniczące jako filtry MVC</span><span class="sxs-lookup"><span data-stu-id="9b054-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="9b054-109">CookieDostawca TempData oparty na programie</span><span class="sxs-lookup"><span data-stu-id="9b054-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="9b054-110">Dostawca rejestrowania Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9b054-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="9b054-111">Dostawca konfiguracji Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9b054-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="9b054-112">Repozytoria kluczy ochrony danych magazynu platformy Azure i usługi Redis</span><span class="sxs-lookup"><span data-stu-id="9b054-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="9b054-113">Serwer weblisten dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9b054-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="9b054-114">Obsługa obiektów WebSockets</span><span class="sxs-lookup"><span data-stu-id="9b054-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="9b054-115">Wybieranie między wersjami 1,0 i 1,1 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b054-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="9b054-116">ASP.NET Core 1,1 ma więcej funkcji niż ASP.NET Core 1,0.</span><span class="sxs-lookup"><span data-stu-id="9b054-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="9b054-117">Ogólnie rzecz biorąc zalecamy korzystanie z najnowszej wersji.</span><span class="sxs-lookup"><span data-stu-id="9b054-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="9b054-118">Dodatkowe informacje</span><span class="sxs-lookup"><span data-stu-id="9b054-118">Additional Information</span></span>

- [<span data-ttu-id="9b054-119">Informacje o wersji ASP.NET Core 1.1.0</span><span class="sxs-lookup"><span data-stu-id="9b054-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="9b054-120">Aby nawiązać połączenie z postępem i planami zespołu deweloperów ASP.NET Core, Dostosuj do [ASP.NET Community standup](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="9b054-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>

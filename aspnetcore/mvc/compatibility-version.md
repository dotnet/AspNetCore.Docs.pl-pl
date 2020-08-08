---
title: Wersja zgodności dla ASP.NET Core MVC
author: rick-anderson
description: Odkryj, jak Klasa startowa w ASP.NET Core konfiguruje usługi i potok żądań aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
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
uid: mvc/compatibility-version
ms.openlocfilehash: 3e705a43b263fcb0867f096e6062023ce10d5ad8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019875"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="1bc14-103">Wersja zgodności dla ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1bc14-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="1bc14-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1bc14-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bc14-105"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Metoda to no-op dla aplikacji ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="1bc14-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="1bc14-106">Oznacza to, że wywołanie `SetCompatibilityVersion` z jakąkolwiek wartością nie <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> ma wpływu na aplikację.</span><span class="sxs-lookup"><span data-stu-id="1bc14-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="1bc14-107">Następna wersja pomocnicza ASP.NET Core może stanowić nową `CompatibilityVersion` wartość.</span><span class="sxs-lookup"><span data-stu-id="1bc14-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="1bc14-108">`CompatibilityVersion`wartości `Version_2_0` przez `Version_2_2` są oznaczone `[Obsolete(...)]` .</span><span class="sxs-lookup"><span data-stu-id="1bc14-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="1bc14-109">Zobacz [przerywanie zmian interfejsu API w ramach funkcji "antysfałszowanych", mechanizmu CORS, diagnostyki, MVC i routingu](https://github.com/aspnet/Announcements/issues/387).</span><span class="sxs-lookup"><span data-stu-id="1bc14-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="1bc14-110">Ta lista zawiera istotne zmiany dotyczące przełączników zgodności.</span><span class="sxs-lookup"><span data-stu-id="1bc14-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="1bc14-111">Aby dowiedzieć się `SetCompatibilityVersion` , jak działa z aplikacjami ASP.NET Core 2. x, wybierz [wersję ASP.NET Core 2,2 tego artykułu](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="1bc14-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bc14-112"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Metoda zezwala aplikacji ASP.NET Core 2. x na zgodę lub rezygnację z ewentualnych zmian w zachowaniu, które wprowadzono w ASP.NET Core MVC 2,1 lub 2,2.</span><span class="sxs-lookup"><span data-stu-id="1bc14-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="1bc14-113">Te potencjalnie nieprzerwane zmiany zachowania są zazwyczaj sposobem zachowania podsystemu MVC i sposobu wywoływania **kodu** przez środowisko uruchomieniowe.</span><span class="sxs-lookup"><span data-stu-id="1bc14-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="1bc14-114">Dzięki wykorzystaniu z programu można uzyskać najnowsze zachowanie i długoterminowe zachowanie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bc14-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="1bc14-115">Poniższy kod ustawia tryb zgodności na ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="1bc14-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="1bc14-116">Zalecamy przetestowanie aplikacji przy użyciu najnowszej wersji ( `CompatibilityVersion.Latest` ).</span><span class="sxs-lookup"><span data-stu-id="1bc14-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="1bc14-117">Przewidujemy, że większość aplikacji nie będzie miała wpływu na zmiany zachowań przy użyciu najnowszej wersji.</span><span class="sxs-lookup"><span data-stu-id="1bc14-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="1bc14-118">Aplikacje, które `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` są wywoływane, są chronione przed potencjalnie niepodzielnymi zmianami zachowania wprowadzonymi w wersjach ASP.NET Core 2.1/2.2 MVC.</span><span class="sxs-lookup"><span data-stu-id="1bc14-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="1bc14-119">Ta ochrona:</span><span class="sxs-lookup"><span data-stu-id="1bc14-119">This protection:</span></span>

* <span data-ttu-id="1bc14-120">Nie ma zastosowania do wszystkich 2,1 i późniejszych zmian, jest to konieczne do potencjalnego zakłócenia zmian zachowania środowiska uruchomieniowego ASP.NET Core w podsystemie MVC.</span><span class="sxs-lookup"><span data-stu-id="1bc14-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="1bc14-121">Nie rozszerzy do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="1bc14-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="1bc14-122">Domyślna zgodność dla aplikacji ASP.NET Core 2,1 i 2,2, które **nie** są wywoływane, `SetCompatibilityVersion` to 2,0 zgodności.</span><span class="sxs-lookup"><span data-stu-id="1bc14-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="1bc14-123">Oznacza to, że wywołanie `SetCompatibilityVersion` jest takie samo jak wywołanie metody `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` .</span><span class="sxs-lookup"><span data-stu-id="1bc14-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="1bc14-124">Poniższy kod ustawia tryb zgodności na ASP.NET Core 2,2, z wyjątkiem następujących zachowań:</span><span class="sxs-lookup"><span data-stu-id="1bc14-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="1bc14-125">W przypadku aplikacji, które napotykają zmiany zachowania podczas przerwania, przy użyciu odpowiednich przełączników zgodności:</span><span class="sxs-lookup"><span data-stu-id="1bc14-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="1bc14-126">Umożliwia korzystanie z najnowszej wersji i rezygnację z określonych zmian w zachowaniu.</span><span class="sxs-lookup"><span data-stu-id="1bc14-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="1bc14-127">Zapewnia czas na zaktualizowanie aplikacji, aby działała z najnowszymi zmianami.</span><span class="sxs-lookup"><span data-stu-id="1bc14-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="1bc14-128"><xref:Microsoft.AspNetCore.Mvc.MvcOptions>Dokumentacja jest dobrym wyjaśnieniem, co zmieniło się i dlaczego zmiany są poprawiane dla większości użytkowników.</span><span class="sxs-lookup"><span data-stu-id="1bc14-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="1bc14-129">W ASP.NET Core 3,0 stare zachowania obsługiwane przez przełączniki zgodności zostały usunięte.</span><span class="sxs-lookup"><span data-stu-id="1bc14-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="1bc14-130">Te zmiany są pozytywne, korzystając niemal wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="1bc14-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="1bc14-131">Wprowadzając te zmiany w 2,1 i 2,2, większość aplikacji może korzystać z zalet, podczas gdy inne mają czas na aktualizację.</span><span class="sxs-lookup"><span data-stu-id="1bc14-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end

---
title: Lista bezpiecznych adresów IP klienta dla ASP.NET Core
author: damienbod
description: Dowiedz się, jak zapisywać programy pośredniczące lub filtry akcji w celu sprawdzania poprawności zdalnych adresów IP względem listy zatwierdzonych adresów IP.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471799"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="58135-103">Lista bezpiecznych adresów IP klienta dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58135-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="58135-104">Przez [Damien Bowden](https://twitter.com/damien_bod) i [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="58135-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="58135-105">W tym artykule przedstawiono trzy sposoby implementacji listy bezpiecznych adresów IP (znanej również jako lista dozwolonych) w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58135-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="58135-106">Towarzysząca przykładowa aplikacja pokazuje wszystkie trzy podejścia.</span><span class="sxs-lookup"><span data-stu-id="58135-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="58135-107">Możesz użyć:</span><span class="sxs-lookup"><span data-stu-id="58135-107">You can use:</span></span>

* <span data-ttu-id="58135-108">Oprogramowanie pośredniczące, aby sprawdzić zdalny adres IP każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="58135-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="58135-109">Filtry akcji MVC w celu sprawdzenia zdalnego adresu IP żądań dla określonych kontrolerów lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="58135-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="58135-110">Razor Pages filtruje, aby sprawdzić zdalny adres IP żądań stron Razor.</span><span class="sxs-lookup"><span data-stu-id="58135-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="58135-111">W każdym przypadku ciąg zawierający zatwierdzone adresy IP klienta jest przechowywany w ustawieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58135-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="58135-112">Oprogramowanie pośredniczące lub filtr:</span><span class="sxs-lookup"><span data-stu-id="58135-112">The middleware or filter:</span></span>

* <span data-ttu-id="58135-113">Analizuje ciąg do tablicy.</span><span class="sxs-lookup"><span data-stu-id="58135-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="58135-114">Sprawdza, czy zdalny adres IP istnieje w tablicy.</span><span class="sxs-lookup"><span data-stu-id="58135-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="58135-115">Dostęp jest dozwolony, jeśli tablica zawiera adres IP.</span><span class="sxs-lookup"><span data-stu-id="58135-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="58135-116">W przeciwnym razie zwracany jest kod stanu niedozwolony HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="58135-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="58135-117">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="58135-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="58135-118">Lista bezpiecznych adresów IP</span><span class="sxs-lookup"><span data-stu-id="58135-118">IP address safelist</span></span>

<span data-ttu-id="58135-119">W przykładowej aplikacji lista bezpiecznych adresów IP to:</span><span class="sxs-lookup"><span data-stu-id="58135-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="58135-120">Zdefiniowane `AdminSafeList` przez właściwość w pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="58135-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="58135-121">Ciąg rozdzielany średnikami, który może zawierać zarówno [adresy Protokołu Internetowego w wersji 4 (IPv4),](https://wikipedia.org/wiki/IPv4) jak i [protokołu internetowego w wersji 6 (IPv6).](https://wikipedia.org/wiki/IPv6)</span><span class="sxs-lookup"><span data-stu-id="58135-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="58135-122">W poprzednim przykładzie adresy `127.0.0.1` IPv4 `192.168.1.5` i adres zwrotny `::1` IPv6 (format `0:0:0:0:0:0:0:1`skompresowany dla) są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="58135-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="58135-123">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="58135-123">Middleware</span></span>

<span data-ttu-id="58135-124">Metoda `Startup.Configure` dodaje typ `AdminSafeListMiddleware` niestandardowego oprogramowania pośredniczącego do potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58135-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="58135-125">Safelist jest pobierany z dostawcą konfiguracji .NET Core i jest przekazywany jako parametr konstruktora.</span><span class="sxs-lookup"><span data-stu-id="58135-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="58135-126">Oprogramowanie pośredniczące analizuje ciąg w tablicy i wyszukuje zdalny adres IP w tablicy.</span><span class="sxs-lookup"><span data-stu-id="58135-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="58135-127">Jeśli zdalny adres IP nie zostanie znaleziony, oprogramowanie pośredniczące zwraca protokół HTTP 403 Jest zabroniony.</span><span class="sxs-lookup"><span data-stu-id="58135-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="58135-128">Ten proces sprawdzania poprawności jest pomijany dla żądań HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="58135-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="58135-129">Filtr akcji</span><span class="sxs-lookup"><span data-stu-id="58135-129">Action filter</span></span>

<span data-ttu-id="58135-130">Jeśli chcesz safelist-driven kontroli dostępu dla określonych kontrolerów MVC lub metody akcji, należy użyć filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="58135-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="58135-131">Przykład:</span><span class="sxs-lookup"><span data-stu-id="58135-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="58135-132">W `Startup.ConfigureServices`, dodaj filtr akcji do kolekcji filtrów MVC.</span><span class="sxs-lookup"><span data-stu-id="58135-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="58135-133">W poniższym przykładzie jest dodawany `ClientIpCheckActionFilter` filtr akcji.</span><span class="sxs-lookup"><span data-stu-id="58135-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="58135-134">Safelist i wystąpienie rejestratora konsoli są przekazywane jako parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="58135-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="58135-135">Filtr akcji można następnie zastosować do kontrolera lub metody akcji za pomocą atrybutu [[ServiceFilter]:](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)</span><span class="sxs-lookup"><span data-stu-id="58135-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="58135-136">W przykładowej aplikacji filtr akcji jest stosowany do `Get` metody akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="58135-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="58135-137">Podczas testowania aplikacji przez wysłanie:</span><span class="sxs-lookup"><span data-stu-id="58135-137">When you test the app by sending:</span></span>

* <span data-ttu-id="58135-138">Żądanie HTTP GET, `[ServiceFilter]` atrybut sprawdza poprawność adresu IP klienta.</span><span class="sxs-lookup"><span data-stu-id="58135-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="58135-139">Jeśli dostęp do metody `Get` akcji jest dozwolony, odmiana następujących danych wyjściowych konsoli jest wytwarzana przez filtr akcji i metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="58135-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="58135-140">Zlecenie żądania HTTP inne niż `AdminSafeListMiddleware` GET, oprogramowanie pośredniczące sprawdza poprawność adresu IP klienta.</span><span class="sxs-lookup"><span data-stu-id="58135-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="58135-141">Filtr stron maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="58135-141">Razor Pages filter</span></span>

<span data-ttu-id="58135-142">Jeśli chcesz kontrolować dostęp do aplikacji Razor Pages opartej na bezpiecznej liście, użyj filtru Strony Razor.</span><span class="sxs-lookup"><span data-stu-id="58135-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="58135-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="58135-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="58135-144">W `Startup.ConfigureServices`obszarze włącz filtr Strony Razor, dodając go do kolekcji filtrów MVC.</span><span class="sxs-lookup"><span data-stu-id="58135-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="58135-145">W poniższym przykładzie `ClientIpCheckPageFilter` dodano filtr Strony razor.</span><span class="sxs-lookup"><span data-stu-id="58135-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="58135-146">Safelist i wystąpienie rejestratora konsoli są przekazywane jako parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="58135-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="58135-147">Gdy żądana jest strona *Typerka Razor indeksu,* filtr Razor Pages sprawdza poprawność adresu IP klienta.</span><span class="sxs-lookup"><span data-stu-id="58135-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="58135-148">Filtr tworzy odmianę następujących danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="58135-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="58135-149">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="58135-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="58135-150">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="58135-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>

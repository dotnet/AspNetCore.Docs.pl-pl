---
title: Safelist IP klienta dla ASP.NET Core
author: damienbod
description: Dowiedz się, jak napisać oprogramowanie pośredniczące lub filtry akcji, aby zweryfikować zdalne adresy IP w odniesieniu do listy zatwierdzonych adresów IP.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
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
uid: security/ip-safelist
ms.openlocfilehash: 75c1ea3a6087f89a200d1f73b1ff65080c819ccd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021773"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="b7ac2-103">Safelist IP klienta dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7ac2-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="b7ac2-104">Autorzy [Damien Bowden](https://twitter.com/damien_bod) i [Tomasz Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b7ac2-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="b7ac2-105">W tym artykule przedstawiono trzy sposoby implementacji adresu IP Safelist (znanego również jako lista dozwolonych) w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="b7ac2-106">Dołączona przykładowa aplikacja prezentuje wszystkie trzy podejścia.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="b7ac2-107">Możesz użyć:</span><span class="sxs-lookup"><span data-stu-id="b7ac2-107">You can use:</span></span>

* <span data-ttu-id="b7ac2-108">Oprogramowanie pośredniczące do sprawdzenia zdalnego adresu IP każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="b7ac2-109">Filtry akcji MVC do sprawdzenia zdalnego adresu IP żądań określonych kontrolerów lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="b7ac2-110">RazorStrony filtrują, aby sprawdzić zdalny adres IP żądań Razor stron.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="b7ac2-111">W każdym przypadku ciąg zawierający zatwierdzone adresy IP klienta jest przechowywany w ustawieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="b7ac2-112">Oprogramowanie pośredniczące lub filtr:</span><span class="sxs-lookup"><span data-stu-id="b7ac2-112">The middleware or filter:</span></span>

* <span data-ttu-id="b7ac2-113">Analizuje ciąg w tablicy.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="b7ac2-114">Sprawdza, czy zdalny adres IP istnieje w tablicy.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="b7ac2-115">Dostęp jest dozwolony, jeśli tablica zawiera adres IP.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="b7ac2-116">W przeciwnym razie zwracany jest kod stanu zabroniony protokołu HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="b7ac2-117">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7ac2-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="b7ac2-118">Adres IP Safelist</span><span class="sxs-lookup"><span data-stu-id="b7ac2-118">IP address safelist</span></span>

<span data-ttu-id="b7ac2-119">W przykładowej aplikacji adres IP Safelist:</span><span class="sxs-lookup"><span data-stu-id="b7ac2-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="b7ac2-120">Zdefiniowane przez `AdminSafeList` Właściwość w *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="b7ac2-121">Rozdzielany średnikami ciąg zawierający adresy [protokołu internetowego w wersji 4 (IPv4)](https://wikipedia.org/wiki/IPv4) i [protokołu internetowego w wersji 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .</span><span class="sxs-lookup"><span data-stu-id="b7ac2-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="b7ac2-122">W poprzednim przykładzie `127.0.0.1` dozwolone są adresy IPv4 i `192.168.1.5` adres sprzężenia zwrotnego IPv6 `::1` (format skompresowany dla `0:0:0:0:0:0:0:1` ).</span><span class="sxs-lookup"><span data-stu-id="b7ac2-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="b7ac2-123">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="b7ac2-123">Middleware</span></span>

<span data-ttu-id="b7ac2-124">`Startup.Configure`Metoda dodaje niestandardowy `AdminSafeListMiddleware` Typ oprogramowania pośredniczącego do potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="b7ac2-125">Safelist jest pobierany z dostawcą konfiguracji platformy .NET Core i jest przenoszona jako parametr konstruktora.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="b7ac2-126">Oprogramowanie pośredniczące analizuje ciąg w tablicę i wyszukuje zdalny adres IP w tablicy.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="b7ac2-127">Jeśli zdalny adres IP nie zostanie znaleziony, oprogramowanie pośredniczące zwróci niedozwolony protokół HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="b7ac2-128">Ten proces sprawdzania poprawności jest pomijany dla żądań HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="b7ac2-129">Filtr akcji</span><span class="sxs-lookup"><span data-stu-id="b7ac2-129">Action filter</span></span>

<span data-ttu-id="b7ac2-130">Jeśli potrzebujesz kontroli dostępu opartej na Safelist dla określonych kontrolerów MVC lub metod akcji, Użyj filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="b7ac2-131">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b7ac2-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="b7ac2-132">W programie `Startup.ConfigureServices` Dodaj filtr akcji do kolekcji filtrów MVC.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="b7ac2-133">W poniższym przykładzie `ClientIpCheckActionFilter` zostanie dodany filtr akcji.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="b7ac2-134">Safelist i wystąpienie rejestratora konsoli są przesyłane jako parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="b7ac2-135">Filtr akcji można następnie zastosować do kontrolera lub metody akcji z atrybutem [[servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :</span><span class="sxs-lookup"><span data-stu-id="b7ac2-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="b7ac2-136">W przykładowej aplikacji filtr akcji jest stosowany do `Get` metody akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="b7ac2-137">Podczas testowania aplikacji, wysyłając:</span><span class="sxs-lookup"><span data-stu-id="b7ac2-137">When you test the app by sending:</span></span>

* <span data-ttu-id="b7ac2-138">Żądanie HTTP GET, ten `[ServiceFilter]` atrybut sprawdza poprawność adresu IP klienta.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="b7ac2-139">Jeśli dostęp jest dozwolony do `Get` metody akcji, zmiana następujących danych wyjściowych konsoli jest generowana przez filtr akcji i metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="b7ac2-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="b7ac2-140">Zlecenie żądania HTTP inne niż GET, `AdminSafeListMiddleware` oprogramowanie pośredniczące sprawdza adres IP klienta.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="no-locrazor-pages-filter"></a><span data-ttu-id="b7ac2-141">RazorFiltr stron</span><span class="sxs-lookup"><span data-stu-id="b7ac2-141">Razor Pages filter</span></span>

<span data-ttu-id="b7ac2-142">Jeśli chcesz, aby dla aplikacji stron była oparta safelista kontrola dostępu Razor , użyj Razor filtru stron.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="b7ac2-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b7ac2-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="b7ac2-144">W programie `Startup.ConfigureServices` Włącz Razor Filtr stron, dodając go do kolekcji filtrów MVC.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="b7ac2-145">W poniższym przykładzie `ClientIpCheckPageFilter` Razor zostanie dodany filtr stron.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="b7ac2-146">Safelist i wystąpienie rejestratora konsoli są przesyłane jako parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="b7ac2-147">Po zażądaniu strony *indeksu* przykładowej aplikacji Razor Razor filtrowanie stron sprawdza poprawność adresu IP klienta.</span><span class="sxs-lookup"><span data-stu-id="b7ac2-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="b7ac2-148">Filtr tworzy odmianę następujących danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="b7ac2-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="b7ac2-149">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b7ac2-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="b7ac2-150">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="b7ac2-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>

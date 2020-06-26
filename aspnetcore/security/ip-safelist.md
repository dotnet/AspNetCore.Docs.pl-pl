---
title: Safelist IP klienta dla ASP.NET Core
author: damienbod
description: Dowiedz się, jak napisać oprogramowanie pośredniczące lub filtry akcji, aby zweryfikować zdalne adresy IP w odniesieniu do listy zatwierdzonych adresów IP.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 5b74205bc7b17d61edbb73cf309f6e24e4318391
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409010"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Safelist IP klienta dla ASP.NET Core

Autorzy [Damien Bowden](https://twitter.com/damien_bod) i [Tomasz Dykstra](https://github.com/tdykstra)
 
W tym artykule przedstawiono trzy sposoby implementacji adresu IP Safelist (znanego również jako lista dozwolonych) w aplikacji ASP.NET Core. Dołączona przykładowa aplikacja prezentuje wszystkie trzy podejścia. Możesz użyć:

* Oprogramowanie pośredniczące do sprawdzenia zdalnego adresu IP każdego żądania.
* Filtry akcji MVC do sprawdzenia zdalnego adresu IP żądań określonych kontrolerów lub metod akcji.
* RazorStrony filtrują, aby sprawdzić zdalny adres IP żądań Razor stron.

W każdym przypadku ciąg zawierający zatwierdzone adresy IP klienta jest przechowywany w ustawieniu aplikacji. Oprogramowanie pośredniczące lub filtr:

* Analizuje ciąg w tablicy. 
* Sprawdza, czy zdalny adres IP istnieje w tablicy.

Dostęp jest dozwolony, jeśli tablica zawiera adres IP. W przeciwnym razie zwracany jest kod stanu zabroniony protokołu HTTP 403.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Adres IP Safelist

W przykładowej aplikacji adres IP Safelist:

* Zdefiniowane przez `AdminSafeList` Właściwość w *appsettings.js* pliku.
* Rozdzielany średnikami ciąg zawierający adresy [protokołu internetowego w wersji 4 (IPv4)](https://wikipedia.org/wiki/IPv4) i [protokołu internetowego w wersji 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

W poprzednim przykładzie `127.0.0.1` dozwolone są adresy IPv4 i `192.168.1.5` adres sprzężenia zwrotnego IPv6 `::1` (format skompresowany dla `0:0:0:0:0:0:0:1` ).

## <a name="middleware"></a>Oprogramowanie pośredniczące

`Startup.Configure`Metoda dodaje niestandardowy `AdminSafeListMiddleware` Typ oprogramowania pośredniczącego do potoku żądania aplikacji. Safelist jest pobierany z dostawcą konfiguracji platformy .NET Core i jest przenoszona jako parametr konstruktora.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Oprogramowanie pośredniczące analizuje ciąg w tablicę i wyszukuje zdalny adres IP w tablicy. Jeśli zdalny adres IP nie zostanie znaleziony, oprogramowanie pośredniczące zwróci niedozwolony protokół HTTP 403. Ten proces sprawdzania poprawności jest pomijany dla żądań HTTP GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtr akcji

Jeśli potrzebujesz kontroli dostępu opartej na Safelist dla określonych kontrolerów MVC lub metod akcji, Użyj filtru akcji. Na przykład:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

W programie `Startup.ConfigureServices` Dodaj filtr akcji do kolekcji filtrów MVC. W poniższym przykładzie `ClientIpCheckActionFilter` zostanie dodany filtr akcji. Safelist i wystąpienie rejestratora konsoli są przesyłane jako parametry konstruktora.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Filtr akcji można następnie zastosować do kontrolera lub metody akcji z atrybutem [[servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

W przykładowej aplikacji filtr akcji jest stosowany do `Get` metody akcji kontrolera. Podczas testowania aplikacji, wysyłając:

* Żądanie HTTP GET, ten `[ServiceFilter]` atrybut sprawdza poprawność adresu IP klienta. Jeśli dostęp jest dozwolony do `Get` metody akcji, zmiana następujących danych wyjściowych konsoli jest generowana przez filtr akcji i metodę akcji:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Zlecenie żądania HTTP inne niż GET, `AdminSafeListMiddleware` oprogramowanie pośredniczące sprawdza adres IP klienta.

## <a name="razor-pages-filter"></a>RazorFiltr stron

Jeśli chcesz, aby dla aplikacji stron była oparta safelista kontrola dostępu Razor , użyj Razor filtru stron. Na przykład:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

W programie `Startup.ConfigureServices` Włącz Razor Filtr stron, dodając go do kolekcji filtrów MVC. W poniższym przykładzie `ClientIpCheckPageFilter` Razor zostanie dodany filtr stron. Safelist i wystąpienie rejestratora konsoli są przesyłane jako parametry konstruktora.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Po zażądaniu strony *indeksu* przykładowej aplikacji Razor Razor filtrowanie stron sprawdza poprawność adresu IP klienta. Filtr tworzy odmianę następujących danych wyjściowych konsoli:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/middleware/index>
* [Filtry akcji](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>

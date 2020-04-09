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
# <a name="client-ip-safelist-for-aspnet-core"></a>Lista bezpiecznych adresów IP klienta dla ASP.NET Core

Przez [Damien Bowden](https://twitter.com/damien_bod) i [Tom Dykstra](https://github.com/tdykstra)
 
W tym artykule przedstawiono trzy sposoby implementacji listy bezpiecznych adresów IP (znanej również jako lista dozwolonych) w aplikacji ASP.NET Core. Towarzysząca przykładowa aplikacja pokazuje wszystkie trzy podejścia. Możesz użyć:

* Oprogramowanie pośredniczące, aby sprawdzić zdalny adres IP każdego żądania.
* Filtry akcji MVC w celu sprawdzenia zdalnego adresu IP żądań dla określonych kontrolerów lub metod akcji.
* Razor Pages filtruje, aby sprawdzić zdalny adres IP żądań stron Razor.

W każdym przypadku ciąg zawierający zatwierdzone adresy IP klienta jest przechowywany w ustawieniu aplikacji. Oprogramowanie pośredniczące lub filtr:

* Analizuje ciąg do tablicy. 
* Sprawdza, czy zdalny adres IP istnieje w tablicy.

Dostęp jest dozwolony, jeśli tablica zawiera adres IP. W przeciwnym razie zwracany jest kod stanu niedozwolony HTTP 403.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Lista bezpiecznych adresów IP

W przykładowej aplikacji lista bezpiecznych adresów IP to:

* Zdefiniowane `AdminSafeList` przez właściwość w pliku *appsettings.json.*
* Ciąg rozdzielany średnikami, który może zawierać zarówno [adresy Protokołu Internetowego w wersji 4 (IPv4),](https://wikipedia.org/wiki/IPv4) jak i [protokołu internetowego w wersji 6 (IPv6).](https://wikipedia.org/wiki/IPv6)

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

W poprzednim przykładzie adresy `127.0.0.1` IPv4 `192.168.1.5` i adres zwrotny `::1` IPv6 (format `0:0:0:0:0:0:0:1`skompresowany dla) są dozwolone.

## <a name="middleware"></a>Oprogramowanie pośredniczące

Metoda `Startup.Configure` dodaje typ `AdminSafeListMiddleware` niestandardowego oprogramowania pośredniczącego do potoku żądania aplikacji. Safelist jest pobierany z dostawcą konfiguracji .NET Core i jest przekazywany jako parametr konstruktora.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Oprogramowanie pośredniczące analizuje ciąg w tablicy i wyszukuje zdalny adres IP w tablicy. Jeśli zdalny adres IP nie zostanie znaleziony, oprogramowanie pośredniczące zwraca protokół HTTP 403 Jest zabroniony. Ten proces sprawdzania poprawności jest pomijany dla żądań HTTP GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtr akcji

Jeśli chcesz safelist-driven kontroli dostępu dla określonych kontrolerów MVC lub metody akcji, należy użyć filtru akcji. Przykład:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

W `Startup.ConfigureServices`, dodaj filtr akcji do kolekcji filtrów MVC. W poniższym przykładzie jest dodawany `ClientIpCheckActionFilter` filtr akcji. Safelist i wystąpienie rejestratora konsoli są przekazywane jako parametry konstruktora.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Filtr akcji można następnie zastosować do kontrolera lub metody akcji za pomocą atrybutu [[ServiceFilter]:](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

W przykładowej aplikacji filtr akcji jest stosowany do `Get` metody akcji kontrolera. Podczas testowania aplikacji przez wysłanie:

* Żądanie HTTP GET, `[ServiceFilter]` atrybut sprawdza poprawność adresu IP klienta. Jeśli dostęp do metody `Get` akcji jest dozwolony, odmiana następujących danych wyjściowych konsoli jest wytwarzana przez filtr akcji i metodę akcji:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Zlecenie żądania HTTP inne niż `AdminSafeListMiddleware` GET, oprogramowanie pośredniczące sprawdza poprawność adresu IP klienta.

## <a name="razor-pages-filter"></a>Filtr stron maszynki do golenia

Jeśli chcesz kontrolować dostęp do aplikacji Razor Pages opartej na bezpiecznej liście, użyj filtru Strony Razor. Przykład:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

W `Startup.ConfigureServices`obszarze włącz filtr Strony Razor, dodając go do kolekcji filtrów MVC. W poniższym przykładzie `ClientIpCheckPageFilter` dodano filtr Strony razor. Safelist i wystąpienie rejestratora konsoli są przekazywane jako parametry konstruktora.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Gdy żądana jest strona *Typerka Razor indeksu,* filtr Razor Pages sprawdza poprawność adresu IP klienta. Filtr tworzy odmianę następujących danych wyjściowych konsoli:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/middleware/index>
* [Filtry akcji](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>

---
title: Wstrzykiwanie zależności do widoków w ASP.NET Core
author: ardalis
description: Dowiedz się, jak ASP.NET Core obsługuje iniekcję zależności w widokach MVC.
ms.author: riande
ms.date: 10/14/2016
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
uid: mvc/views/dependency-injection
ms.openlocfilehash: 9d4920a7b572163b048bfb06029d913cee3f9e5a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587257"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a>Wstrzykiwanie zależności do widoków w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

ASP.NET Core obsługuje [iniekcję zależności](xref:fundamentals/dependency-injection) w widokach. Może to być przydatne w przypadku usług specyficznych dla widoku, takich jak lokalizacja lub dane wymagane tylko do wypełniania elementów widoku. Należy spróbować zachować [rozdzielenie problemów](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) między kontrolerami i widokami. Większość danych wyświetlanych w widokach powinna zostać przeniesiona z kontrolera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="configuration-injection"></a>Iniekcja konfiguracji

*appsettings.json* wartości można wstrzyknąć bezpośrednio do widoku.

Przykład *appsettings.json* pliku:

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

Składnia dla `@inject` : `@inject <type> <name>`

Przykład przy użyciu `@inject` :

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a>Wstrzykiwanie usługi

Usługę można wstrzyknąć do widoku za pomocą `@inject` dyrektywy. Można traktować `@inject` jako dodanie właściwości do widoku i wypełnianie właściwości przy użyciu di.

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

Ten widok przedstawia listę `ToDoItem` wystąpień wraz z podsumowaniem pokazującym ogólną statystykę. Podsumowanie jest wypełniane przez wstrzykiwaną `StatisticsService` . Ta usługa jest zarejestrowana na potrzeby iniekcji zależności w `ConfigureServices` programie *Startup.cs*:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

`StatisticsService`Wykonuje pewne obliczenia dotyczące zestawu `ToDoItem` wystąpień, do którego uzyskuje dostęp za pośrednictwem repozytorium:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

Przykładowe repozytorium używa kolekcji w pamięci. Implementacja pokazana powyżej (która operuje na wszystkich danych w pamięci) nie jest zalecana w przypadku dużych, zdalnych dostępnych zestawów danych.

Przykład wyświetla dane z modelu powiązanego z widokiem, a usługa została wprowadzona do widoku:

![Aby wyświetlić listę wszystkich elementów, elementów zakończonych, średni priorytet i listę zadań z poziomami priorytetów i wartościami logicznymi wskazującymi na zakończenie.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>Wypełnianie danych wyszukiwania

Iniekcja widoku może być przydatna do wypełniania opcji elementów interfejsu użytkownika, takich jak listy rozwijane. Rozważ użycie formularza profilu użytkownika, który zawiera opcje określania płci, stanu i innych preferencji. Renderowanie takiego formularza przy użyciu standardowego podejścia MVC wymagało, aby kontroler zażądał usług dostępu do danych dla każdego z tych zestawów opcji, a następnie wypełnia model lub `ViewBag` z każdym zestawem opcji, które mają być powiązane.

Alternatywna metoda powoduje wstrzyknięcie usług bezpośrednio do widoku w celu uzyskania opcji. Pozwala to zminimalizować ilość kodu wymaganego przez kontroler, przenosząc tę logikę konstrukcyjną tego elementu widoku do samego widoku. Akcja kontrolera, aby wyświetlić formularz edycji profilu, musi jedynie przekazać formularz wystąpienia profilu:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

Formularz HTML służący do aktualizowania tych preferencji zawiera listę rozwijaną dla trzech właściwości:

![Aktualizacja widoku profilu z formularzem umożliwiającym wprowadzanie nazwy, płci, stanu i koloru ulubionego.](dependency-injection/_static/updateprofile.png)

Te listy są wypełniane przez usługę, która została wprowadzona do widoku:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

`ProfileOptionsService`Jest to usługa poziomu interfejsu użytkownika zaprojektowana w celu zapewnienia tylko danych wymaganych dla tego formularza:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> Nie zapomnij zarejestrować typów, które zażądasz poprzez iniekcję zależności w programie `Startup.ConfigureServices` . Wyrejestrowanie typu zgłasza wyjątek w czasie wykonywania, ponieważ dostawca usług jest wewnętrznie zapytań za pośrednictwem [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).

## <a name="overriding-services"></a>Zastępowanie usług

Oprócz wstrzykiwania nowych usług ta technika może być również używana do przesłonięcia wcześniej wprowadzonych usług na stronie. Na poniższej ilustracji przedstawiono wszystkie pola dostępne na stronie, które są używane w pierwszym przykładzie:

![Menu kontekstowe IntelliSense dla wpisanego znaku @ symbol z listą pól HTML, składników, StatsService i adresów URL](dependency-injection/_static/razor-fields.png)

Jak widać, pola domyślne obejmują `Html` , `Component` , i `Url` (jak również `StatsService` wprowadzone). Jeśli na przykład chcesz zastąpić domyślne pomocników HTML własnymi, możesz łatwo to zrobić przy użyciu `@inject` :

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

Jeśli chcesz rozłożyć istniejące usługi, możesz po prostu użyć tej techniki podczas dziedziczenia lub otaczania istniejącej implementacji własnymi.

## <a name="see-also"></a>Zobacz też

* Blog Simon Timms: [pobieranie danych wyszukiwania do widoku](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)

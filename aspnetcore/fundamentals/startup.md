---
title: Uruchamianie aplikacji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak klasa Uruchamianie w ASP.NET Core konfiguruje usługi i potok żądań aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/startup
ms.openlocfilehash: e3249df4b7388beeff13fe4b4e0ff481c35725c5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667658"
---
# <a name="app-startup-in-aspnet-core"></a>Uruchamianie aplikacji w ASP.NET Core

Rick [Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)i [Steve Smith](https://ardalis.com)

Klasa `Startup` konfiguruje usługi i potok żądań aplikacji.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Klasa Startup

ASP.NET aplikacje Core `Startup` używają klasy, `Startup` która jest nazwana zgodnie z konwencją. Klasa `Startup`:

* Opcjonalnie zawiera <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> metodę konfigurowania *usług*aplikacji . Usługa jest składnikiem wielokrotnego użytku, który zapewnia funkcjonalność aplikacji. Usługi *registered* są `ConfigureServices` rejestrowane i używane w całej aplikacji za <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>pomocą [iniekcji zależności (DI)](xref:fundamentals/dependency-injection) lub .
* Zawiera <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> metodę tworzenia potoku przetwarzania żądań aplikacji.

`ConfigureServices`i `Configure` są wywoływane przez ASP.NET Core środowiska uruchomieniowego po uruchomieniu aplikacji:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Poprzednia próbka dotyczy [stron Razor;](xref:razor-pages/index) wersja MVC jest podobna.


Klasa `Startup` jest określona podczas [budowy hosta](xref:fundamentals/index#host) aplikacji. Klasa `Startup` jest zazwyczaj określona przez wywołanie [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) metody w konstruktorze hosta:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Host udostępnia usługi, które `Startup` są dostępne dla konstruktora klasy. Aplikacja dodaje dodatkowe `ConfigureServices`usługi za pośrednictwem . Zarówno host, jak i `Configure` usługi aplikacji są dostępne w aplikacji i w całej aplikacji.

Tylko następujące typy usług mogą `Startup` być wstrzykiwane do konstruktora podczas korzystania z [hosta ogólnego](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Większość usług nie są `Configure` dostępne, dopóki metoda jest wywoływana.

### <a name="multiple-startup"></a>Wielokrotne uruchamianie

Gdy aplikacja definiuje `Startup` oddzielne klasy dla różnych środowisk `StartupDevelopment`(na `Startup` przykład), odpowiednia klasa jest wybierana w czasie wykonywania. Klasa, której sufiks nazwy pasuje do bieżącego środowiska, jest traktowana priorytetowo. Jeśli aplikacja jest uruchamiana w środowisku `Startup` deweloperskim `StartupDevelopment` i `StartupDevelopment` zawiera zarówno klasę, jak i klasę, klasa jest używana. Aby uzyskać więcej informacji, zobacz [Używanie wielu środowisk](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Zobacz [Host, aby](xref:fundamentals/index#host) uzyskać więcej informacji na temat hosta. Aby uzyskać informacje na temat obsługi błędów podczas [uruchamiania, zobacz Obsługa wyjątków uruchamiania](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> jest:

* Element opcjonalny.
* Wywoływane przez hosta przed `Configure` metodą konfigurowania usług aplikacji.
* Gdzie [opcje konfiguracji](xref:fundamentals/configuration/index) są ustawiane przez konwencję.

Host może skonfigurować niektóre `Startup` usługi przed wywoływaniem metod. Aby uzyskać więcej informacji, zobacz [Host](xref:fundamentals/index#host).

W przypadku funkcji wymagających `Add{Service}` znacznej <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>konfiguracji dostępne są metody rozszerzenia . Na przykład **Dodaj**DbContext, **Dodaj**domyślnąnietołowienie, **Dodaj**EntityFrameworkStores i **Dodaj**RazorPages:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

Dodawanie usług do kontenera usługi udostępnia je `Configure` w aplikacji i w metodzie. Usługi są rozwiązywane za pomocą <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> [iniekcji zależności](xref:fundamentals/dependency-injection) lub z .

## <a name="the-configure-method"></a>Metoda Konfiguruj

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> służy do określania, jak aplikacja reaguje na żądania HTTP. Potok żądań jest konfigurowany przez <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> dodanie składników [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) do wystąpienia. `IApplicationBuilder`jest dostępna `Configure` dla metody, ale nie jest zarejestrowana w kontenerze usługi. Hosting tworzy `IApplicationBuilder` i przekazuje go `Configure`bezpośrednio do .

[Szablony ASP.NET Core konfigurują](/dotnet/core/tools/dotnet-new) potok z obsługą:

* [Strona wyjątku dla deweloperów](xref:fundamentals/error-handling#developer-exception-page)
* [Obsługa wyjątków](xref:fundamentals/error-handling#exception-handler-page)
* [Ścisłe bezpieczeństwo transportu HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Przekierowanie HTTPS](xref:security/enforcing-ssl)
* [Pliki statyczne](xref:fundamentals/static-files)
* ASP.NET [podstawowych stron](xref:razor-pages/index) [MVC](xref:mvc/overview) i razor


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Poprzednia próbka dotyczy [stron Razor;](xref:razor-pages/index) wersja MVC jest podobna.

Każda `Use` metoda rozszerzenia dodaje jeden lub więcej składników oprogramowania pośredniczącego do potoku żądań. Na przykład <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> konfiguruje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) do obsługi [plików statycznych](xref:fundamentals/static-files).

Każdy składnik oprogramowania pośredniczącego w potoku żądań jest odpowiedzialny za wywoływanie następnego składnika w potoku lub zwarcie łańcucha, jeśli to konieczne.

Dodatkowe usługi, `IWebHostEnvironment`takie `ILoggerFactory`jak , `ConfigureServices`lub cokolwiek zdefiniowanego w , można określić w podpisie `Configure` metody. Te usługi są wstrzykiwane, jeśli są one dostępne.

Aby uzyskać więcej informacji `IApplicationBuilder` na temat sposobu korzystania <xref:fundamentals/middleware/index>z oprogramowania pośredniczącego i kolejności przetwarzania oprogramowania pośredniczącego, zobacz .

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurowanie usług bez uruchamiania

Aby skonfigurować usługi i potok przetwarzania `Startup` żądań `ConfigureServices` `Configure` bez użycia klasy, wywołania i wygody metod na konstruktora hosta. Wiele wywołań do `ConfigureServices` dołączenia do siebie. Jeśli `Configure` istnieje wiele wywołań `Configure` metody, używane jest ostatnie wywołanie.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Rozszerzanie uruchamiania za pomocą filtrów startowych

Zastosowanie: <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>

* Aby skonfigurować oprogramowanie pośredniczące na początku lub na końcu potoku [Konfigurowanie](#the-configure-method) oprogramowania pośredniczącego aplikacji bez wyraźnego wywołania `Use{Middleware}`. `IStartupFilter`jest używany przez ASP.NET Core, aby dodać wartości domyślne na początku potoku bez konieczności jawnego rejestrowania domyślnego oprogramowania pośredniczącego przez autora aplikacji. `IStartupFilter`umożliwia inne wywołanie `Use{Middleware}` składnika w imieniu autora aplikacji.
* Aby utworzyć potok `Configure` metod. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) można ustawić oprogramowanie pośredniczące do uruchamiania przed lub po oprogramowania pośredniczącego dodane przez biblioteki.

`IStartupFilter`implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, który odbiera `Action<IApplicationBuilder>`i zwraca . Definiuje <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> klasę, aby skonfigurować potok żądania aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie potoku oprogramowania pośredniczącego za pomocą aplikacji IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Każdy `IStartupFilter` można dodać jeden lub więcej oprogramowania pośredniczącego w potoku żądania. Filtry są wywoływane w kolejności, w jakiej zostały dodane do kontenera usługi. Filtry mogą dodawać oprogramowanie pośredniczące przed lub po przekazaniu formantu do następnego filtru, w związku z czym dołączają do początku lub końca potoku aplikacji.

W poniższym przykładzie pokazano, jak `IStartupFilter`zarejestrować oprogramowanie pośredniczące w pliku . Oprogramowanie `RequestSetOptionsMiddleware` pośredniczące ustawia wartość opcji z parametru ciągu zapytania:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

Jest `RequestSetOptionsMiddleware` skonfigurowany w `RequestSetOptionsStartupFilter` klasie:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Jest `IStartupFilter` zarejestrowany w kontenerze <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>serwisowym w .

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Po podaniu `option` parametru ciągu zapytania oprogramowanie pośredniczące przetwarza przypisanie wartości, zanim oprogramowanie pośredniczące ASP.NET Core renderuje odpowiedź.

Zlecenie wykonania oprogramowania pośredniczącego `IStartupFilter` jest ustawiane według kolejności rejestracji:

* Wiele `IStartupFilter` implementacji może wchodzić w interakcje z tymi samymi obiektami. Jeśli zamawianie jest ważne, zamów ich `IStartupFilter` rejestracje usługi, aby dopasować kolejność, że ich oprogramowanie pośredniczące powinny działać.
* Biblioteki mogą dodawać oprogramowanie `IStartupFilter` pośredniczące z jedną lub kilkoma implementacjami, które są uruchamiane przed lub po zarejestrowaniu `IStartupFilter`innego oprogramowania pośredniczącego aplikacji . Aby wywołać `IStartupFilter` oprogramowanie pośredniczące przed oprogramowaniem `IStartupFilter`pośredniczącym dodanym przez bibliotekę:

  * Umieść rejestrację usługi przed dodaniem biblioteki do kontenera usługi.
  * Aby wywołać później, umieść rejestrację usługi po dodaniu biblioteki.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Dodawanie konfiguracji przy uruchamianiu z zestawu zewnętrznego

Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania `Startup` z zewnętrznego zestawu poza klasą aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Klasa Startup

ASP.NET aplikacje Core `Startup` używają klasy, `Startup` która jest nazwana zgodnie z konwencją. Klasa `Startup`:

* Opcjonalnie zawiera <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> metodę konfigurowania *usług*aplikacji . Usługa jest składnikiem wielokrotnego użytku, który zapewnia funkcjonalność aplikacji. Usługi *registered* są `ConfigureServices` rejestrowane i używane w całej aplikacji za <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>pomocą [iniekcji zależności (DI)](xref:fundamentals/dependency-injection) lub .
* Zawiera <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> metodę tworzenia potoku przetwarzania żądań aplikacji.

`ConfigureServices`i `Configure` są wywoływane przez ASP.NET Core środowiska uruchomieniowego po uruchomieniu aplikacji:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

Klasa `Startup` jest określona podczas [budowy hosta](xref:fundamentals/index#host) aplikacji. Klasa `Startup` jest zazwyczaj określona przez wywołanie [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) metody w konstruktorze hosta:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

Host udostępnia usługi, które `Startup` są dostępne dla konstruktora klasy. Aplikacja dodaje dodatkowe `ConfigureServices`usługi za pośrednictwem . Usługi hosta i aplikacji są `Configure` następnie dostępne w aplikacji i w całej aplikacji.

Częstym zastosowaniem [iniekcji zależności](xref:fundamentals/dependency-injection) do `Startup` klasy jest wstrzyknięcie:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>, aby skonfigurować usługi według środowiska.
* <xref:Microsoft.Extensions.Configuration.IConfiguration>, aby odczytać konfigurację.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory>, aby utworzyć `Startup.ConfigureServices`rejestrator w pliku .

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Większość usług nie są `Configure` dostępne, dopóki metoda jest wywoływana.

### <a name="multiple-startup"></a>Wielokrotne uruchamianie

Gdy aplikacja definiuje `Startup` oddzielne klasy dla różnych środowisk `StartupDevelopment`(na `Startup` przykład), odpowiednia klasa jest wybierana w czasie wykonywania. Klasa, której sufiks nazwy pasuje do bieżącego środowiska, jest traktowana priorytetowo. Jeśli aplikacja jest uruchamiana w środowisku `Startup` deweloperskim `StartupDevelopment` i `StartupDevelopment` zawiera zarówno klasę, jak i klasę, klasa jest używana. Aby uzyskać więcej informacji, zobacz [Używanie wielu środowisk](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Zobacz [Host, aby](xref:fundamentals/index#host) uzyskać więcej informacji na temat hosta. Aby uzyskać informacje na temat obsługi błędów podczas [uruchamiania, zobacz Obsługa wyjątków uruchamiania](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> jest:

* Element opcjonalny.
* Wywoływane przez hosta przed `Configure` metodą konfigurowania usług aplikacji.
* Gdzie [opcje konfiguracji](xref:fundamentals/configuration/index) są ustawiane przez konwencję.

Host może skonfigurować niektóre `Startup` usługi przed wywoływaniem metod. Aby uzyskać więcej informacji, zobacz [Host](xref:fundamentals/index#host).

W przypadku funkcji wymagających `Add{Service}` znacznej <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>konfiguracji dostępne są metody rozszerzenia . Na przykład **Dodaj**DbContext, **Dodaj**domyślnąnietołowienie, **Dodaj**EntityFrameworkStores i **Dodaj**RazorPages:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Dodawanie usług do kontenera usługi udostępnia je `Configure` w aplikacji i w metodzie. Usługi są rozwiązywane za pomocą <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> [iniekcji zależności](xref:fundamentals/dependency-injection) lub z .

Aby [SetCompatibilityVersion](xref:mvc/compatibility-version) uzyskać więcej informacji na `SetCompatibilityVersion`temat temat .

## <a name="the-configure-method"></a>Metoda Konfiguruj

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> służy do określania, jak aplikacja reaguje na żądania HTTP. Potok żądań jest konfigurowany przez <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> dodanie składników [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) do wystąpienia. `IApplicationBuilder`jest dostępna `Configure` dla metody, ale nie jest zarejestrowana w kontenerze usługi. Hosting tworzy `IApplicationBuilder` i przekazuje go `Configure`bezpośrednio do .

[Szablony ASP.NET Core konfigurują](/dotnet/core/tools/dotnet-new) potok z obsługą:

* [Strona wyjątku dla deweloperów](xref:fundamentals/error-handling#developer-exception-page)
* [Obsługa wyjątków](xref:fundamentals/error-handling#exception-handler-page)
* [Ścisłe bezpieczeństwo transportu HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Przekierowanie HTTPS](xref:security/enforcing-ssl)
* [Pliki statyczne](xref:fundamentals/static-files)
* ASP.NET [podstawowych stron](xref:razor-pages/index) [MVC](xref:mvc/overview) i razor
* [Ogólne rozporządzenie o ochronie danych (RODO)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Każda `Use` metoda rozszerzenia dodaje jeden lub więcej składników oprogramowania pośredniczącego do potoku żądań. Na przykład <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> konfiguruje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) do obsługi [plików statycznych](xref:fundamentals/static-files).

Każdy składnik oprogramowania pośredniczącego w potoku żądań jest odpowiedzialny za wywoływanie następnego składnika w potoku lub zwarcie łańcucha, jeśli to konieczne.

Dodatkowe usługi, `IHostingEnvironment` takie `ILoggerFactory`jak i `ConfigureServices`, lub cokolwiek `Configure` zdefiniowanego w , można określić w podpisie metody. Te usługi są wstrzykiwane, jeśli są one dostępne.

Aby uzyskać więcej informacji `IApplicationBuilder` na temat sposobu korzystania <xref:fundamentals/middleware/index>z oprogramowania pośredniczącego i kolejności przetwarzania oprogramowania pośredniczącego, zobacz .

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurowanie usług bez uruchamiania

Aby skonfigurować usługi i potok przetwarzania `Startup` żądań `ConfigureServices` `Configure` bez użycia klasy, wywołania i wygody metod na konstruktora hosta. Wiele wywołań do `ConfigureServices` dołączenia do siebie. Jeśli `Configure` istnieje wiele wywołań `Configure` metody, używane jest ostatnie wywołanie.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Rozszerzanie uruchamiania za pomocą filtrów startowych

Zastosowanie: <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>

* Aby skonfigurować oprogramowanie pośredniczące na początku lub na końcu potoku [Konfigurowanie](#the-configure-method) oprogramowania pośredniczącego aplikacji bez wyraźnego wywołania `Use{Middleware}`. `IStartupFilter`jest używany przez ASP.NET Core, aby dodać wartości domyślne na początku potoku bez konieczności jawnego rejestrowania domyślnego oprogramowania pośredniczącego przez autora aplikacji. `IStartupFilter`umożliwia inne wywołanie `Use{Middleware}` składnika w imieniu autora aplikacji.
* Aby utworzyć potok `Configure` metod. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) można ustawić oprogramowanie pośredniczące do uruchamiania przed lub po oprogramowania pośredniczącego dodane przez biblioteki.

`IStartupFilter`implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, który odbiera `Action<IApplicationBuilder>`i zwraca . Definiuje <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> klasę, aby skonfigurować potok żądania aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie potoku oprogramowania pośredniczącego za pomocą aplikacji IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Każdy `IStartupFilter` można dodać jeden lub więcej oprogramowania pośredniczącego w potoku żądania. Filtry są wywoływane w kolejności, w jakiej zostały dodane do kontenera usługi. Filtry mogą dodawać oprogramowanie pośredniczące przed lub po przekazaniu formantu do następnego filtru, w związku z czym dołączają do początku lub końca potoku aplikacji.

W poniższym przykładzie pokazano, jak `IStartupFilter`zarejestrować oprogramowanie pośredniczące w pliku . Oprogramowanie `RequestSetOptionsMiddleware` pośredniczące ustawia wartość opcji z parametru ciągu zapytania:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

Jest `RequestSetOptionsMiddleware` skonfigurowany w `RequestSetOptionsStartupFilter` klasie:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Jest `IStartupFilter` zarejestrowany w kontenerze <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>serwisowym w .

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Po podaniu `option` parametru ciągu zapytania oprogramowanie pośredniczące przetwarza przypisanie wartości, zanim oprogramowanie pośredniczące ASP.NET Core renderuje odpowiedź.

Zlecenie wykonania oprogramowania pośredniczącego `IStartupFilter` jest ustawiane według kolejności rejestracji:

* Wiele `IStartupFilter` implementacji może wchodzić w interakcje z tymi samymi obiektami. Jeśli zamawianie jest ważne, zamów ich `IStartupFilter` rejestracje usługi, aby dopasować kolejność, że ich oprogramowanie pośredniczące powinny działać.
* Biblioteki mogą dodawać oprogramowanie `IStartupFilter` pośredniczące z jedną lub kilkoma implementacjami, które są uruchamiane przed lub po zarejestrowaniu `IStartupFilter`innego oprogramowania pośredniczącego aplikacji . Aby wywołać `IStartupFilter` oprogramowanie pośredniczące przed oprogramowaniem `IStartupFilter`pośredniczącym dodanym przez bibliotekę:

  * Umieść rejestrację usługi przed dodaniem biblioteki do kontenera usługi.
  * Aby wywołać później, umieść rejestrację usługi po dodaniu biblioteki.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Dodawanie konfiguracji przy uruchamianiu z zestawu zewnętrznego

Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania `Startup` z zewnętrznego zestawu poza klasą aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end
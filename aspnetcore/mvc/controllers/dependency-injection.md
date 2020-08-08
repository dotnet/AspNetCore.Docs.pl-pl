---
title: Wstrzykiwanie zależności do kontrolerów w ASP.NET Core
author: ardalis
description: Odkryj, jak ASP.NET Core kontrolery MVC żądają swoich zależności jawnie za pośrednictwem konstruktorów z iniekcją zależności w ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: aabb7f893fd9650e2e901dcfdfe845faba391435
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019175"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Wstrzykiwanie zależności do kontrolerów w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autorzy [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Steve Smith](https://github.com/ardalis)

ASP.NET Core kontrolery MVC żądają zależności jawnie za pośrednictwem konstruktorów. ASP.NET Core ma wbudowaną obsługę [iniekcji zależności (di)](xref:fundamentals/dependency-injection). Dzięki temu aplikacje są łatwiejsze do testowania i konserwowania.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Iniekcja konstruktora

Usługi są dodawane jako parametr konstruktora, a środowisko uruchomieniowe rozwiązuje usługę z kontenera usług. Usługi są zwykle definiowane przy użyciu interfejsów. Rozważmy na przykład aplikację, która wymaga bieżącego czasu. Następujący interfejs uwidacznia `IDateTime` usługę:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Poniższy kod implementuje `IDateTime` Interfejs:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Dodaj usługę do kontenera usługi:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Aby uzyskać więcej informacji na temat <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> , zobacz [di okresy istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).

Poniższy kod wyświetla powitanie użytkownika w oparciu o godzinę:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Uruchom aplikację i zostanie wyświetlony komunikat z uwzględnieniem czasu.

## <a name="action-injection-with-fromservices"></a>Iniekcja akcji przy użyciu FromServices

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>Umożliwia wstrzyknięcie usługi bezpośrednio do metody akcji bez użycia iniekcji konstruktora:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Ustawienia dostępu z kontrolera

Dostęp do ustawień aplikacji lub konfiguracji z poziomu kontrolera jest typowym wzorcem. *Wzorzec opcji* opisany w <xref:fundamentals/configuration/options> jest preferowanym podejściem do zarządzania ustawieniami. Ogólnie rzecz biorąc nie należy wprowadzać bezpośrednio <xref:Microsoft.Extensions.Configuration.IConfiguration> do kontrolera.

Utwórz klasę, która reprezentuje opcje. Przykład:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Dodaj klasę konfiguracji do kolekcji usług:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Skonfiguruj aplikację do odczytywania ustawień z pliku w formacie JSON:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Poniższy kod żąda `IOptions<SampleWebSettings>` ustawień z kontenera usługi i używa ich w `Index` metodzie:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* Zobacz <xref:mvc/controllers/testing> , aby dowiedzieć się, jak ułatwić przetestowanie kodu przez jawne żądanie zależności w kontrolerach.

* [Zastąp domyślny kontener iniekcji zależności z implementacją innej firmy](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autorzy [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Steve Smith](https://github.com/ardalis)

ASP.NET Core kontrolery MVC żądają zależności jawnie za pośrednictwem konstruktorów. ASP.NET Core ma wbudowaną obsługę [iniekcji zależności (di)](xref:fundamentals/dependency-injection). Dzięki temu aplikacje są łatwiejsze do testowania i konserwowania.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Iniekcja konstruktora

Usługi są dodawane jako parametr konstruktora, a środowisko uruchomieniowe rozwiązuje usługę z kontenera usług. Usługi są zwykle definiowane przy użyciu interfejsów. Rozważmy na przykład aplikację, która wymaga bieżącego czasu. Następujący interfejs uwidacznia `IDateTime` usługę:

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Poniższy kod implementuje `IDateTime` Interfejs:

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Dodaj usługę do kontenera usługi:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Aby uzyskać więcej informacji na temat <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> , zobacz [di okresy istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).

Poniższy kod wyświetla powitanie użytkownika w oparciu o godzinę:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Uruchom aplikację i zostanie wyświetlony komunikat z uwzględnieniem czasu.

## <a name="action-injection-with-fromservices"></a>Iniekcja akcji przy użyciu FromServices

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>Umożliwia wstrzyknięcie usługi bezpośrednio do metody akcji bez użycia iniekcji konstruktora:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Ustawienia dostępu z kontrolera

Dostęp do ustawień aplikacji lub konfiguracji z poziomu kontrolera jest typowym wzorcem. *Wzorzec opcji* opisany w <xref:fundamentals/configuration/options> jest preferowanym podejściem do zarządzania ustawieniami. Ogólnie rzecz biorąc nie należy wprowadzać bezpośrednio <xref:Microsoft.Extensions.Configuration.IConfiguration> do kontrolera.

Utwórz klasę, która reprezentuje opcje. Przykład:

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Dodaj klasę konfiguracji do kolekcji usług:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Skonfiguruj aplikację do odczytywania ustawień z pliku w formacie JSON:

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Poniższy kod żąda `IOptions<SampleWebSettings>` ustawień z kontenera usługi i używa ich w `Index` metodzie:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* Zobacz <xref:mvc/controllers/testing> , aby dowiedzieć się, jak ułatwić przetestowanie kodu przez jawne żądanie zależności w kontrolerach.

* [Zastąp domyślny kontener iniekcji zależności z implementacją innej firmy](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end
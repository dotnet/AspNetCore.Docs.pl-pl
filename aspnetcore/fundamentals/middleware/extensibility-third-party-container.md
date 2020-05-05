---
title: Aktywacja oprogramowania pośredniczącego za pomocą kontenera innej firmy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać silnego typu oprogramowania pośredniczącego z aktywacją opartą na fabryce i kontenerem innej firmy w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 9a551777c58ba8eae1cf04758e1631710ff16e51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774473"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>Aktywacja oprogramowania pośredniczącego za pomocą kontenera innej firmy w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

W tym artykule przedstawiono sposób użycia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> i <xref:Microsoft.AspNetCore.Http.IMiddleware> jako punktu rozszerzalności aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) przy użyciu kontenera innej firmy. Aby uzyskać informacje wprowadzające `IMiddleware`w systemach <xref:fundamentals/middleware/extensibility> `IMiddlewareFactory` i, zobacz.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja pokazuje aktywację oprogramowania pośredniczącego przez `IMiddlewareFactory` implementację `SimpleInjectorMiddlewareFactory`. Przykład używa elementu prostego wtrysku zależności [iniektora](https://simpleinjector.org) (di).

Implementacja przykładowego oprogramowania pośredniczącego rejestruje wartość podaną przez parametr ciągu zapytania (`key`). Oprogramowanie pośredniczące używa wstrzykniętego kontekstu bazy danych (usługi w zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.

> [!NOTE]
> Przykładowa aplikacja używa [prostego iniektora](https://github.com/simpleinjector/SimpleInjector) wyłącznie w celach demonstracyjnych. Użycie prostego iniektora nie jest adnotacją. Metody aktywacji oprogramowania pośredniczącego opisane w temacie prosta dokumentacja iniektora i problemy z usługą GitHub są zalecane przez utrzymujący prostego iniektora. Aby uzyskać więcej informacji, zobacz [prostą dokumentację iniektora](https://simpleinjector.readthedocs.io/en/latest/index.html) i [prosty repozytorium GitHub](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>zapewnia metody tworzenia oprogramowania pośredniczącego.

W przykładowej aplikacji jest zaimplementowana fabryka oprogramowania pośredniczącego w celu `SimpleInjectorActivatedMiddleware` utworzenia wystąpienia. Fabryka programów pośredniczących używa prostego kontenera iniektora do rozpoznawania oprogramowania pośredniczącego:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.

Oprogramowanie pośredniczące aktywowane przez `IMiddlewareFactory` implementację (*oprogramowanie pośredniczące/SimpleInjectorActivatedMiddleware. cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Utworzono rozszerzenie dla oprogramowania pośredniczącego (*oprogramowanie pośredniczące/MiddlewareExtensions. cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`należy wykonać kilka zadań:

* Skonfiguruj prosty kontener iniektora.
* Zarejestruj fabrykę i oprogramowanie pośredniczące.
* Udostępnienie kontekstu bazy danych aplikacji z prostego kontenera iniektora.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Oprogramowanie pośredniczące jest zarejestrowane w potoku przetwarzania żądań w `Startup.Configure`:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule przedstawiono sposób użycia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> i <xref:Microsoft.AspNetCore.Http.IMiddleware> jako punktu rozszerzalności aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) przy użyciu kontenera innej firmy. Aby uzyskać informacje wprowadzające `IMiddleware`w systemach <xref:fundamentals/middleware/extensibility> `IMiddlewareFactory` i, zobacz.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja pokazuje aktywację oprogramowania pośredniczącego przez `IMiddlewareFactory` implementację `SimpleInjectorMiddlewareFactory`. Przykład używa elementu prostego wtrysku zależności [iniektora](https://simpleinjector.org) (di).

Implementacja przykładowego oprogramowania pośredniczącego rejestruje wartość podaną przez parametr ciągu zapytania (`key`). Oprogramowanie pośredniczące używa wstrzykniętego kontekstu bazy danych (usługi w zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.

> [!NOTE]
> Przykładowa aplikacja używa [prostego iniektora](https://github.com/simpleinjector/SimpleInjector) wyłącznie w celach demonstracyjnych. Użycie prostego iniektora nie jest adnotacją. Metody aktywacji oprogramowania pośredniczącego opisane w temacie prosta dokumentacja iniektora i problemy z usługą GitHub są zalecane przez utrzymujący prostego iniektora. Aby uzyskać więcej informacji, zobacz [prostą dokumentację iniektora](https://simpleinjector.readthedocs.io/en/latest/index.html) i [prosty repozytorium GitHub](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>zapewnia metody tworzenia oprogramowania pośredniczącego.

W przykładowej aplikacji jest zaimplementowana fabryka oprogramowania pośredniczącego w celu `SimpleInjectorActivatedMiddleware` utworzenia wystąpienia. Fabryka programów pośredniczących używa prostego kontenera iniektora do rozpoznawania oprogramowania pośredniczącego:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.

Oprogramowanie pośredniczące aktywowane przez `IMiddlewareFactory` implementację (*oprogramowanie pośredniczące/SimpleInjectorActivatedMiddleware. cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Utworzono rozszerzenie dla oprogramowania pośredniczącego (*oprogramowanie pośredniczące/MiddlewareExtensions. cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`należy wykonać kilka zadań:

* Skonfiguruj prosty kontener iniektora.
* Zarejestruj fabrykę i oprogramowanie pośredniczące.
* Udostępnienie kontekstu bazy danych aplikacji z prostego kontenera iniektora.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Oprogramowanie pośredniczące jest zarejestrowane w potoku przetwarzania żądań w `Startup.Configure`:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Aktywacja oprogramowania pośredniczącego oparta na fabryce](xref:fundamentals/middleware/extensibility)
* [Proste repozytorium GitHub iniektora](https://github.com/simpleinjector/SimpleInjector)
* [Prosta dokumentacja iniektora](https://simpleinjector.readthedocs.io/en/latest/index.html)

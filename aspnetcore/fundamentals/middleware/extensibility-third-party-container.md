---
title: Aktywacja oprogramowania pośredniczącego za pomocą kontenera innej firmy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać silnie typizowanego oprogramowania pośredniczącego z aktywacją fabryczną i kontenerem innej firmy w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: a5c5bf6dff6ef795add075df932dd625129ef793
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663136"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>Aktywacja oprogramowania pośredniczącego za pomocą kontenera innej firmy w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

W tym artykule <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> pokazano, jak używać i <xref:Microsoft.AspNetCore.Http.IMiddleware> jako punkt rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) w kontenerze innej firmy. Aby uzyskać informacje `IMiddlewareFactory` wprowadzające na i `IMiddleware`, zobacz <xref:fundamentals/middleware/extensibility>.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja pokazuje aktywację `IMiddlewareFactory` oprogramowania `SimpleInjectorMiddlewareFactory`pośredniczącego przez implementację, . Próbka używa kontenera zależności prostego [wtryskiwacza](https://simpleinjector.org) (DI).

Implementacja oprogramowania pośredniczącego w przykładzie rejestruje`key`wartość dostarczoną przez parametr ciągu zapytania ( ). Oprogramowanie pośredniczące używa kontekstu wstrzykniętej bazy danych (usługa o określonym zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.

> [!NOTE]
> Przykładowa aplikacja używa [prostego wtryskiwacza](https://github.com/simpleinjector/SimpleInjector) wyłącznie do celów demonstracyjnych. Korzystanie z Prostego wtryskiwacza nie jest potwierdzeniem. Metody aktywacji oprogramowania pośredniczącego opisane w dokumentacji prostego wtryskiwacza i problemów z githubem są zalecane przez opiekunów simple injector. Aby uzyskać więcej informacji, zobacz [dokumentację prostego wtryskiwacza](https://simpleinjector.readthedocs.io/en/latest/index.html) i [repozytor github prostego wtryskiwacza](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory (IMiddlewareFactory)

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>udostępnia metody tworzenia oprogramowania pośredniczącego.

W przykładowej aplikacji fabryka oprogramowania pośredniczącego jest implementowana w celu utworzenia `SimpleInjectorActivatedMiddleware` wystąpienia. Fabryka oprogramowania pośredniczącego używa kontenera Prostego wtryskiwacza do rozwiązania oprogramowania pośredniczącego:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware ( IMiddleware )

<xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.

Oprogramowanie pośredniczące `IMiddlewareFactory` aktywowane przez implementację (*Oprogramowanie pośredniczące / SimpleInjectorActivatedMiddleware.cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Rozszerzenie jest tworzone dla oprogramowania pośredniczącego *(Oprogramowanie pośredniczące / MiddlewareExtensions.cs):*

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`musi wykonywać kilka zadań:

* Skonfiguruj pojemnik prostego wtryskiwacza.
* Zarejestruj fabrykę i oprogramowanie pośredniczące.
* Udostępnij kontekst bazy danych aplikacji z kontenera Prostego wtryskiwacza.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Oprogramowanie pośredniczące jest zarejestrowane `Startup.Configure`w potoku przetwarzania żądań w:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> pokazano, jak używać i <xref:Microsoft.AspNetCore.Http.IMiddleware> jako punkt rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) w kontenerze innej firmy. Aby uzyskać informacje `IMiddlewareFactory` wprowadzające na i `IMiddleware`, zobacz <xref:fundamentals/middleware/extensibility>.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja pokazuje aktywację `IMiddlewareFactory` oprogramowania `SimpleInjectorMiddlewareFactory`pośredniczącego przez implementację, . Próbka używa kontenera zależności prostego [wtryskiwacza](https://simpleinjector.org) (DI).

Implementacja oprogramowania pośredniczącego w przykładzie rejestruje`key`wartość dostarczoną przez parametr ciągu zapytania ( ). Oprogramowanie pośredniczące używa kontekstu wstrzykniętej bazy danych (usługa o określonym zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.

> [!NOTE]
> Przykładowa aplikacja używa [prostego wtryskiwacza](https://github.com/simpleinjector/SimpleInjector) wyłącznie do celów demonstracyjnych. Korzystanie z Prostego wtryskiwacza nie jest potwierdzeniem. Metody aktywacji oprogramowania pośredniczącego opisane w dokumentacji prostego wtryskiwacza i problemów z githubem są zalecane przez opiekunów simple injector. Aby uzyskać więcej informacji, zobacz [dokumentację prostego wtryskiwacza](https://simpleinjector.readthedocs.io/en/latest/index.html) i [repozytor github prostego wtryskiwacza](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory (IMiddlewareFactory)

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>udostępnia metody tworzenia oprogramowania pośredniczącego.

W przykładowej aplikacji fabryka oprogramowania pośredniczącego jest implementowana w celu utworzenia `SimpleInjectorActivatedMiddleware` wystąpienia. Fabryka oprogramowania pośredniczącego używa kontenera Prostego wtryskiwacza do rozwiązania oprogramowania pośredniczącego:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware ( IMiddleware )

<xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.

Oprogramowanie pośredniczące `IMiddlewareFactory` aktywowane przez implementację (*Oprogramowanie pośredniczące / SimpleInjectorActivatedMiddleware.cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Rozszerzenie jest tworzone dla oprogramowania pośredniczącego *(Oprogramowanie pośredniczące / MiddlewareExtensions.cs):*

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`musi wykonywać kilka zadań:

* Skonfiguruj pojemnik prostego wtryskiwacza.
* Zarejestruj fabrykę i oprogramowanie pośredniczące.
* Udostępnij kontekst bazy danych aplikacji z kontenera Prostego wtryskiwacza.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Oprogramowanie pośredniczące jest zarejestrowane `Startup.Configure`w potoku przetwarzania żądań w:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Fabryczna aktywacja oprogramowania pośredniczącego](xref:fundamentals/middleware/extensibility)
* [Proste repozytor GitHub wtryskiwacza](https://github.com/simpleinjector/SimpleInjector)
* [Prosta dokumentacja wtryskiwacza](https://simpleinjector.readthedocs.io/en/latest/index.html)

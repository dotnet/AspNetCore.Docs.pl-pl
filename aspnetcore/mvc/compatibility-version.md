---
title: Wersja zgodności dla ASP.NET Core MVC
author: rick-anderson
description: Odkryj, jak Klasa startowa w ASP.NET Core konfiguruje usługi i potok żądań aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/compatibility-version
ms.openlocfilehash: 45eca0bedc2e4e5c74936ae5d1bf525774467b2a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774213"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>Wersja zgodności dla ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda to no-op dla aplikacji ASP.NET Core 3,0. Oznacza to, że `SetCompatibilityVersion` wywołanie z jakąkolwiek wartością <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> nie ma wpływu na aplikację.

* Następna wersja pomocnicza ASP.NET Core może stanowić nową `CompatibilityVersion` wartość.
* `CompatibilityVersion`wartości `Version_2_0` przez `Version_2_2` są oznaczone `[Obsolete(...)]`.
* Zobacz [przerywanie zmian interfejsu API w ramach funkcji "antysfałszowanych", mechanizmu CORS, diagnostyki, MVC i routingu](https://github.com/aspnet/Announcements/issues/387). Ta lista zawiera istotne zmiany dotyczące przełączników zgodności.

Aby dowiedzieć `SetCompatibilityVersion` się, jak działa z aplikacjami ASP.NET Core 2. x, wybierz [wersję ASP.NET Core 2,2 tego artykułu](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda zezwala aplikacji ASP.NET Core 2. x na zgodę lub rezygnację z ewentualnych zmian w zachowaniu, które wprowadzono w ASP.NET Core MVC 2,1 lub 2,2. Te potencjalnie nieprzerwane zmiany zachowania są zazwyczaj sposobem zachowania podsystemu MVC i sposobu wywoływania **kodu** przez środowisko uruchomieniowe. Dzięki wykorzystaniu z programu można uzyskać najnowsze zachowanie i długoterminowe zachowanie ASP.NET Core.

Poniższy kod ustawia tryb zgodności na ASP.NET Core 2,2:

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

Zalecamy przetestowanie aplikacji przy użyciu najnowszej wersji (`CompatibilityVersion.Latest`). Przewidujemy, że większość aplikacji nie będzie miała wpływu na zmiany zachowań przy użyciu najnowszej wersji.

Aplikacje, które `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` są wywoływane, są chronione przed potencjalnie niepodzielnymi zmianami zachowania wprowadzonymi w wersjach ASP.NET Core 2.1/2.2 MVC. Ta ochrona:

* Nie ma zastosowania do wszystkich 2,1 i późniejszych zmian, jest to konieczne do potencjalnego zakłócenia zmian zachowania środowiska uruchomieniowego ASP.NET Core w podsystemie MVC.
* Nie rozszerzy do ASP.NET Core 3,0.

Domyślna zgodność dla aplikacji ASP.NET Core 2,1 i 2,2, które **nie** są wywoływane `SetCompatibilityVersion` , to 2,0 zgodności. Oznacza to, że wywołanie `SetCompatibilityVersion` jest takie samo jak wywołanie `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`metody.

Poniższy kod ustawia tryb zgodności na ASP.NET Core 2,2, z wyjątkiem następujących zachowań:

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

W przypadku aplikacji, które napotykają zmiany zachowania podczas przerwania, przy użyciu odpowiednich przełączników zgodności:

* Umożliwia korzystanie z najnowszej wersji i rezygnację z określonych zmian w zachowaniu.
* Zapewnia czas na zaktualizowanie aplikacji, aby działała z najnowszymi zmianami.

<xref:Microsoft.AspNetCore.Mvc.MvcOptions> Dokumentacja jest dobrym wyjaśnieniem, co zmieniło się i dlaczego zmiany są poprawiane dla większości użytkowników.

W ASP.NET Core 3,0 stare zachowania obsługiwane przez przełączniki zgodności zostały usunięte. Te zmiany są pozytywne, korzystając niemal wszystkich użytkowników. Wprowadzając te zmiany w 2,1 i 2,2, większość aplikacji może korzystać z zalet, podczas gdy inne mają czas na aktualizację.
::: moniker-end

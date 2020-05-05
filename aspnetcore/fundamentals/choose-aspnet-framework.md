---
title: Wybierz między ASP.NET 4. x i ASP.NET Core
author: rick-anderson
description: Wyjaśnia ASP.NET Core a ASP.NET 4. x oraz jak wybierać między nimi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 1fb81d5a54cf332ca473af8fbe1841813a127be7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775885"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Wybierz między ASP.NET 4. x i ASP.NET Core

ASP.NET Core jest przeprojektowana ASP.NET 4. x. W tym artykule wymieniono różnice między nimi.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core to międzyplatformowa platforma służąca do tworzenia nowoczesnych, opartych na chmurze aplikacji sieci Web w systemie Windows, macOS lub Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4. x to przedwczesne środowisko, które zapewnia usługi, które są konieczne do tworzenia aplikacji sieci Web klasy korporacyjnej opartych na serwerze w systemie Windows.

## <a name="framework-selection"></a>Wybór struktury

Poniższa tabela zawiera porównanie ASP.NET Core z ASP.NET 4. x.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Kompilacja dla systemu Windows, macOS lub Linux|Kompiluj dla systemu Windows|
|Strony to zalecane podejście do tworzenia interfejsu użytkownika sieci Web w ASP.NET Core 2. x. [ Razor ](xref:razor-pages/index) Zobacz również [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api)i [SignalR](xref:signalr/introduction).|Korzystanie z [formularzy sieci Web](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), internetowego [interfejsu API](/aspnet/web-api/), elementów [webhook](/aspnet/webhooks/)lub [stron sieci Web](/aspnet/web-pages)|
|Wiele wersji na maszynę|Jedna wersja na maszynę|
|Programowanie za pomocą [programu Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/)lub [Visual Studio Code](https://code.visualstudio.com/) przy użyciu języka C# lub F #|Programowanie za pomocą [programu Visual Studio](https://visualstudio.microsoft.com/vs/) przy użyciu języka C#, VB lub F #|
|Wyższa wydajność niż ASP.NET 4. x|Dobra wydajność|
|[Korzystanie z środowiska uruchomieniowego platformy .NET Core](/dotnet/standard/choosing-core-framework-server)|Użyj środowiska uruchomieniowego .NET Framework|

Aby uzyskać informacje na temat obsługi programu ASP.NET Core 2. x w systemie .NET Framework, zobacz [ASP.NET Core określania celu .NET Framework](xref:index#target-framework) .

## <a name="aspnet-core-scenarios"></a>Scenariusze ASP.NET Core

* [Witryny internetowe](xref:tutorials/first-mvc-app/index)
* [Interfejsy API](xref:tutorials/first-web-api)
* [Przesyłanie w czasie rzeczywistym](xref:signalr/introduction)
* [Wdrażanie aplikacji platformy ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>Scenariusze ASP.NET 4. x

* [Witryny internetowe](/aspnet/mvc)
* [Interfejsy API](/aspnet/web-api)
* [Przesyłanie w czasie rzeczywistym](/aspnet/signalr)
* [Tworzenie aplikacji sieci Web ASP.NET 4. x na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wprowadzenie do platformy ASP.NET](/aspnet/overview)
* [Wprowadzenie do programu ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>

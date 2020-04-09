---
title: Wybierz między ASP.NET 4.x a ASP.NET Core
author: rick-anderson
description: W tym artykule wyjaśniono, ASP.NET Core a ASP.NET 4.x i jak wybrać między nimi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665243"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Wybierz między ASP.NET 4.x a ASP.NET Core

ASP.NET Core to przeprojektowanie ASP.NET 4.x. W tym artykule wymieniono różnice między nimi.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core to wieloplatformowa struktura open source do tworzenia nowoczesnych, opartych na chmurze aplikacji internetowych w systemach Windows, macOS lub Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x to dojrzała struktura, która zapewnia usługi potrzebne do tworzenia aplikacji sieci Web klasy korporacyjnej opartych na serwerze w systemie Windows.

## <a name="framework-selection"></a>Wybór struktury

W poniższej tabeli porównano ASP.NET Core z ASP.NET 4.x.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Kompilacja dla systemu Windows, macOS lub Linux|Kompilacja dla systemu Windows|
|[Razor Pages](xref:razor-pages/index) to zalecane podejście do tworzenia interfejsu użytkownika sieci Web od ASP.NET Core 2.x. Zobacz też [MVC](xref:mvc/overview), [SignalR](xref:signalr/introduction)Web [API](xref:tutorials/first-web-api), i .|Używanie [formularzy sieci Web](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [interfejsu API sieci Web,](/aspnet/web-api/) [elementów WebHook](/aspnet/webhooks/)lub [stron sieci Web](/aspnet/web-pages)|
|Wiele wersji na maszynę|Jedna wersja na maszynę|
|Tworzenie za pomocą [programu Visual Studio,](https://visualstudio.microsoft.com/vs/) [programu Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/)lub programu Visual Studio [Code](https://code.visualstudio.com/) przy użyciu języka C# lub F #|Tworzenie za pomocą [programu Visual Studio](https://visualstudio.microsoft.com/vs/) przy użyciu języka C#, VB lub F #|
|Wyższa wydajność niż ASP.NET 4.x|Dobra wydajność|
|[Korzystanie z core.NET środowiska uruchomieniowego](/dotnet/standard/choosing-core-framework-server)|Korzystanie ze środowiska uruchomieniowego programu .NET Framework|

Zobacz [ASP.NET Core targeting .NET Framework, aby](xref:index#target-framework) uzyskać informacje na temat obsługi ASP.NET Core 2.x w programie .NET Framework.

## <a name="aspnet-core-scenarios"></a>ASP.NET Podstawowe scenariusze

* [Witryny internetowe](xref:tutorials/first-mvc-app/index)
* [Interfejsy API](xref:tutorials/first-web-api)
* [Przesyłanie w czasie rzeczywistym](xref:signalr/introduction)
* [Wdrażanie aplikacji platformy ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET scenariusze 4.x

* [Witryny internetowe](/aspnet/mvc)
* [Interfejsy API](/aspnet/web-api)
* [Przesyłanie w czasie rzeczywistym](/aspnet/signalr)
* [Tworzenie aplikacji sieci Web ASP.NET 4.x na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wprowadzenie do platformy ASP.NET](/aspnet/overview)
* [Wprowadzenie do programu ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>

---
title: Używanie protokołu HTTP/2 z serwerem sieci Web ASP.NET Core Kestrel
author: rick-anderson
description: Dowiedz się więcej na temat używania protokołu HTTP/2 z Kestrel, międzyplatformowego serwera sieci Web dla ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253983"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>Używanie protokołu HTTP/2 z serwerem sieci Web ASP.NET Core Kestrel

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:

* System operacyjny&dagger;
  * Windows Server 2016/Windows 10 lub nowszy&Dagger;
  * Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)
* Platforma docelowa: .NET Core 2,2 lub nowszy
* Połączenie [negocjowania protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3)
* Połączenie TLS 1,2 lub nowsze

&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.
&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1. Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona. Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.

W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) `HTTP/2` .

Począwszy od platformy .NET Core 3,0, protokół HTTP/2 jest domyślnie włączony. Aby uzyskać więcej informacji na temat konfiguracji, zobacz sekcję [limity protokołu HTTP/2 Kestrel](xref:fundamentals/servers/kestrel/options#http2-limits) i [ListenOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) .

## <a name="advanced-http2-features"></a>Zaawansowane funkcje protokołu HTTP/2

Dodatkowe funkcje protokołu HTTP/2 w programie Kestrel obsługują gRPC, w tym obsługę przyczep z odpowiedziami i wysyłanie ramek resetowania.

### <a name="trailers"></a>Przyczep

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]

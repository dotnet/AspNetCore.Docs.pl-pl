---
title: Używanie ASP.NET Core z protokołem HTTP/2 w usługach IIS
author: rick-anderson
description: Dowiedz się, jak używać funkcji protokołu HTTP/2 z usługami IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654378"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>Używanie ASP.NET Core z protokołem HTTP/2 w usługach IIS

Autor [Justin Kotalik](https://github.com/jkotalik)

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania usług IIS:

* System Windows Server 2016 lub nowszy/Windows 10 lub nowszy
* Program IIS 10 lub nowszy
* Połączenie TLS 1,2 lub nowsze
* Podczas [hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model): połączenia z serwerem Edge dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotnego serwera proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) korzysta z protokołu HTTP/1.1.

W przypadku wdrożenia w procesie podczas ustanawiania połączenia HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Raporty `HTTP/2` . W przypadku wdrożenia poza procesem w przypadku ustanowienia połączenia HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Raporty `HTTP/1.1` .

Aby uzyskać więcej informacji na temat modeli hostingu w procesie i poza procesami, zobacz <xref:host-and-deploy/aspnet-core-module> .

Protokół HTTP/2 jest domyślnie włączony dla połączeń HTTPS/TLS. Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione. Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="advanced-http2-features-to-support-grpc"></a>Zaawansowane funkcje protokołu HTTP/2 do obsługi gRPC

Dodatkowe funkcje protokołu HTTP/2 w usługach IIS obsługują gRPC, w tym obsługę przyczep z odpowiedziami i wysyłanie ramek resetowania.

Wymagania dotyczące uruchamiania gRPC w usługach IIS:

* Hosting w procesie.
* Windows 10, kompilacja systemu operacyjnego 20300,1000 lub nowsza. Może wymagać użycia kompilacji niejawnego programu testów systemu Windows.
* Połączenie TLS 1,2 lub nowsze

### <a name="trailers"></a>Przyczep

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]

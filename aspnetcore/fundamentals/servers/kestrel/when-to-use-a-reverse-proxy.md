---
title: Kiedy używać zwrotnego serwera proxy z serwerem sieci Web ASP.NET Core Kestrel
author: rick-anderson
description: Informacje o tym, kiedy używać zwrotnego serwera proxy przed Kestrel, międzyplatformowego serwera sieci Web dla ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253971"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kiedy używać Kestrel z zwrotnym serwerem proxy

Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/). Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.

Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](_static/kestrel-to-internet2.png)

Kestrel używany w konfiguracji zwrotnego serwera proxy:

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](_static/kestrel-to-internet.png)

Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.

Gdy Kestrel jest używany jako serwer graniczny bez serwera proxy zwrotnego, udostępnianie tego samego adresu IP i portu między wieloma procesami jest nieobsługiwane. Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na `Host` nagłówki żądań. Zwrotny serwer proxy, który może udostępniać porty, może przesyłać żądania do Kestrel na unikatowym adresie IP i porcie.

Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.

Zwrotny serwer proxy:

* Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.
* Zapewnienie dodatkowej warstwy konfiguracji i obrony.
* Integracja z istniejącą infrastrukturą może być lepsza.
* Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS). Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.

> [!WARNING]
> Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](xref:fundamentals/servers/kestrel/host-filtering).

## <a name="additional-resources"></a>Dodatkowe zasoby

<xref:host-and-deploy/proxy-load-balancer>


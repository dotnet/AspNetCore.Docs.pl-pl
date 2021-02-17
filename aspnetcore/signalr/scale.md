---
title: ASP.NET Core SignalR hosting i skalowanie produkcji
author: bradygaster
description: Dowiedz się, jak uniknąć problemów z wydajnością i skalowaniem w aplikacjach korzystających z ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564015"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.NET Core SignalR hosting i skalowanie

Autorzy [Andrew Stanton-pielęgniarki](https://twitter.com/anurse), [Brady Gastera](https://twitter.com/bradygaster)i [Tomasz Dykstra](https://github.com/tdykstra)

W tym artykule wyjaśniono zagadnienia dotyczące hostingu i skalowania dla aplikacji o dużym natężeniu ruchu, które używają ASP.NET Core SignalR .

## <a name="sticky-sessions"></a>Sesje programu Sticky

SignalR wymaga, aby wszystkie żądania HTTP dotyczące określonego połączenia były obsługiwane przez ten sam proces serwera. Gdy SignalR program jest uruchomiony w farmie serwerów (na wielu serwerach), należy użyć "sesji programu Sticky Notes". "Sesje programu Sticky Notes" są również nazywane koligacją sesji przez niektóre moduły równoważenia obciążenia. Azure App Service używa [routingu żądań aplikacji](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) do przesyłania żądań. Włączenie ustawienia "koligacja ARR" w Azure App Service spowoduje włączenie "sesji programu Sticky Notes". Jedyną sytuacją, w której nie są wymagane sesje programu Sticky, są:

1. W przypadku hostowania na jednym serwerze w ramach jednego procesu.
1. W przypadku korzystania z SignalR usługi platformy Azure.
1. Gdy wszyscy klienci są skonfigurowani do korzystania **tylko** z obiektów WebSockets, **a** [ustawienie SkipNegotiation](xref:signalr/configuration#configure-additional-options) jest włączone w konfiguracji klienta.

We wszystkich innych przypadkach (w tym gdy jest używany plan Redis), środowisko serwera musi być skonfigurowane dla sesji programu Sticky Notes.

Aby uzyskać wskazówki dotyczące konfigurowania Azure App Service dla programu SignalR , zobacz <xref:signalr/publish-to-azure-web-app> .

## <a name="tcp-connection-resources"></a>Zasoby połączenia TCP

Liczba współbieżnych połączeń TCP, które może obsługiwać serwer sieci Web, jest ograniczona. Klienci standardowi HTTP korzystają z połączeń *tymczasowych* . Te połączenia można zamknąć, gdy klient przechodzi w stan bezczynności i zostanie otwarty ponownie później. Z drugiej strony SignalR połączenie jest *trwałe*. SignalR połączenia pozostają otwarte nawet wtedy, gdy klient przejdzie w stan bezczynności. W aplikacji o dużym natężeniu ruchu, która obsługuje wielu klientów, te trwałe połączenia mogą spowodować, że serwery osiągnął maksymalną liczbę połączeń.

Połączenia trwałe zużywają także dodatkową pamięć, aby śledzić każde połączenie.

Duże wykorzystanie zasobów związanych z połączeniami przez SignalR program może mieć wpływ na inne aplikacje sieci Web, które są hostowane na tym samym serwerze. W przypadku SignalR otwarcia i przechowywania ostatnich dostępnych połączeń TCP inne aplikacje sieci Web na tym samym serwerze również nie będą miały dostępnych połączeń.

Jeśli na serwerze wykorzystano połączenia, zobaczysz losowe błędy gniazda i błędy resetowania połączenia. Na przykład:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Aby zapobiec SignalR wykorzystaniu przez zasoby błędów w innych aplikacjach sieci Web, uruchom SignalR na różnych serwerach niż inne aplikacje sieci Web.

Aby zapobiec SignalR wykorzystaniu przez zasoby błędów w SignalR aplikacji, Przeskaluj w poziomie, aby ograniczyć liczbę połączeń, które serwer musi obsłużyć.

## <a name="scale-out"></a>Skalowanie w poziomie

Aplikacja, która korzysta SignalR z programu, musi śledzić wszystkie połączenia, które tworzą problemy dla farmy serwerów. Dodaj serwer i pobiera nowe połączenia, o których nie wie inne serwery. Na przykład SignalR na każdym serwerze na poniższym diagramie nie można wypróbować połączeń na innych serwerach. Gdy SignalR na jednym z serwerów chce wysłać komunikat do wszystkich klientów, komunikat zostanie przekierowany do klientów podłączonych do tego serwera.

![Skalowanie::: No-Loc (sygnalizujący)::: bez planu](scale/_static/scale-no-backplane.png)

Opcjami rozwiązywania tego problemu jest [ SignalR usługa platformy Azure](#azure-signalr-service) i [Redis plan](#redis-backplane).

## <a name="azure-signalr-service"></a>Usługa platformy Azure SignalR

Usługa platformy Azure SignalR to serwer proxy, a nie plan. Za każdym razem, gdy klient inicjuje połączenie z serwerem, klient zostaje przekierowany do programu w celu nawiązania połączenia z usługą. Ten proces przedstawiono na poniższym diagramie:

![Nawiązywanie połączenia z platformą Azure::: No-Loc (Sygnalizującer)::: Service](scale/_static/azure-signalr-service-one-connection.png)

Wynika to z tego, że usługa zarządza wszystkimi połączeniami klientów, natomiast każdy serwer potrzebuje tylko małej stałej liczby połączeń z usługą, jak pokazano na poniższym diagramie:

![Klienci połączeni z usługą, serwery połączone z usługą](scale/_static/azure-signalr-service-multiple-connections.png)

Takie podejście do skalowania w poziomie ma kilka korzyści w porównaniu z Redisą dla planu wieloplanowego:

* Sesje programu Sticky Notes, nazywane również [koligacją klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), nie są wymagane, ponieważ klienci są natychmiast przekierowywani do SignalR usługi platformy Azure po nawiązaniu połączenia.
* SignalRAplikacja może skalować w poziomie na podstawie liczby wysłanych komunikatów, natomiast SignalR usługa Azure skaluje się do obsługi dowolnej liczby połączeń. Na przykład mogą istnieć tysiące klientów, ale jeśli są wysyłane tylko kilka komunikatów na sekundę, SignalR aplikacja nie będzie musiała skalować w poziomie do wielu serwerów, aby obsługiwać same połączenia.
* SignalRAplikacja nie będzie używać znacznie większej liczby zasobów połączenia niż aplikacja internetowa bez SignalR .

Z tego względu zalecamy korzystanie z usługi platformy Azure SignalR dla wszystkich ASP.NET Core SignalR aplikacji hostowanych na platformie Azure, w tym App Service, maszyn wirtualnych i kontenerów.

Aby uzyskać więcej informacji, [Zobacz SignalR dokumentację usługi platformy Azure](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Płyta montażowa Redis

[Redis](https://redis.io/) to magazyn kluczy w pamięci, który obsługuje system obsługi komunikatów z modelem publikowania/subskrybowania. SignalRPlan Redis używa funkcji pub/sub do przesyłania dalej komunikatów do innych serwerów. Gdy klient nawiązuje połączenie, informacje o połączeniu są przesyłane do planu. Gdy serwer chce wysłać komunikat do wszystkich klientów, wysyła do planu. W planie dla wszystkich podłączonych klientów i serwerów, na których się znajdują. Wysyła komunikat do wszystkich klientów za pośrednictwem odpowiednich serwerów. Ten proces przedstawiono na poniższym diagramie:

![Redis, wiadomości wysyłane z jednego serwera do wszystkich klientów](scale/_static/redis-backplane.png)

Plan Redis to zalecane podejście skalowalne w poziomie dla aplikacji hostowanych we własnej infrastrukturze. Jeśli istnieje duże opóźnienie połączenia między centrum danych i centrum danych platformy Azure, SignalR usługa Azure może nie być praktyczną opcją dla aplikacji lokalnych z małymi opóźnieniami lub z wymaganiami dotyczącymi dużej przepływności.

SignalRWymienione wcześniej zalety usługi platformy Azure są niekorzystne dla planu Redis:

* Wymagane są sesje programu Sticky Notes, znane także jako [koligacja klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), z wyjątkiem sytuacji, gdy spełnione są **obie** następujące sytuacje:
  * Wszyscy klienci są skonfigurowani do korzystania **tylko** z obiektów WebSockets.
  * [Ustawienie SkipNegotiation](xref:signalr/configuration#configure-additional-options) jest włączone w konfiguracji klienta. 
   Po zainicjowaniu połączenia na serwerze połączenie musi pozostać na tym serwerze.
* SignalRAplikacja musi być skalowana w poziomie na podstawie liczby klientów, nawet jeśli jest wysyłanych kilka komunikatów.
* SignalRAplikacja używa znacznie większej liczby zasobów połączenia niż aplikacja internetowa bez SignalR .

## <a name="iis-limitations-on-windows-client-os"></a>Ograniczenia usług IIS w systemie operacyjnym Windows Client

Windows 10 i Windows 8. x są systemami operacyjnymi klienta. Program IIS w systemach operacyjnych klienta ma limit 10 współbieżnych połączeń. SignalRpołączenia:

* Przejściowe i często ponownie nawiązane.
* **Nie** usunięto natychmiast, gdy nie jest już używane.

Powyższe warunki mogą spowodować osiągnięcie 10 limitów połączeń w systemie operacyjnym klienta. Gdy system operacyjny klienta jest używany do programowania, zalecamy:

* Należy unikać usług IIS.
* Użyj Kestrel lub IIS Express jako celów wdrożenia.

## <a name="linux-with-nginx"></a>System Linux z serwerem Nginx

Poniżej znajdują się minimalne wymagane ustawienia umożliwiające włączenie obiektów WebSockets, ServerSentEvents i LongPolling dla SignalR :

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

Gdy używane są wiele serwerów zaplecza, należy dodać sesje programu Sticky, aby uniemożliwić nawiązywanie SignalR połączeń z serwerów podczas nawiązywania połączenia. Istnieje wiele sposobów dodawania sesji programu Sticky Nginx. Poniżej przedstawiono dwa podejścia w zależności od tego, co jest dostępne.

Dodano następujące elementy oprócz poprzedniej konfiguracji. W poniższych przykładach `backend` jest nazwą grupy serwerów.

W programie [Nginx Open Source](https://nginx.org/en/)Użyj metody `ip_hash` do kierowania połączeń do serwera na podstawie adresu IP klienta:

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

Za pomocą programu [Nginx Plus](https://www.nginx.com/products/nginx) `sticky` Dodaj cookie do żądania i Przypnij żądania użytkownika do serwera:

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

Na koniec przejdź `proxy_pass http://localhost:5000` `server` do sekcji do `proxy_pass http://backend` .

Aby uzyskać więcej informacji na temat obiektów WebSockets za pośrednictwem usługi Nginx, zobacz [Nginx jako serwer proxy protokołu WebSocket](https://www.nginx.com/blog/websocket-nginx).

Aby uzyskać więcej informacji o równoważeniu obciążenia i sesjach Nginx, zobacz [równoważenie obciążenia sieciowego](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).

Aby uzyskać więcej informacji na temat ASP.NET Core za pomocą Nginx, zobacz następujący artykuł:
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a>Dostawcy rozwiązań innych firm SignalR

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Orleans](https://github.com/OrleansContrib/SignalR.Orleans)
* [Rebus](https://github.com/rebus-org/Rebus.SignalR)

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

* [SignalRDokumentacja usługi platformy Azure](/azure/azure-signalr/signalr-overview)
* [Konfigurowanie planu Redis](xref:signalr/redis-backplane)

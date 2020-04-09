---
title: Hostuj i wdrażaj Blazor ASP.NET serwer podstawowy
author: guardrex
description: Dowiedz się, jak Blazor hostować i wdrażać aplikację serwera przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025423"
---
# <a name="host-and-deploy-opno-locblazor-server"></a>Hostuj Blazor i wdrażaj serwer

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

Aplikacje serwera mogą akceptować [wartości konfiguracji hosta ogólnego](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Wdrożenie

Za pomocą modelu Blazor [ Blazor hostingu serwera](xref:blazor/hosting-models#blazor-server), jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania [SignalR](xref:signalr/introduction) JavaScript są obsługiwane przez połączenie.

Wymagany jest serwer www obsługujący aplikację ASP.NET Core. Program Visual Studio zawiera szablon`blazorserverside` projektu aplikacji ** Blazor serwera** (szablon podczas korzystania z nowego polecenia [dotnet).](/dotnet/core/tools/dotnet-new)

## <a name="scalability"></a>Skalowalność

Zaplanuj wdrożenie, aby jak najlepiej Blazor wykorzystać dostępną infrastrukturę dla aplikacji Server. Zobacz następujące zasoby, Blazor aby rozwiązać skalowalność aplikacji serwera:

* [Podstawy aplikacji Blazor serwera](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Serwer wdrażania

Biorąc pod uwagę skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który aplikacja wyczerpie wraz ze wzrostem wymagań użytkownika. Dostępna pamięć na serwerze wpływa na:

* Liczba aktywnych obwodów obsługiwanych przez serwer.
* Opóźnienie interfejsu użytkownika na kliencie.

Aby uzyskać wskazówki dotyczące Blazor tworzenia bezpiecznych i skalowalnych aplikacji serwerowych, zobacz <xref:security/blazor/server>.

Każdy obwód używa około 250 KB pamięci dla minimalnej aplikacji *Hello World*-style. Rozmiar obwodu zależy od kodu aplikacji i wymagań konserwacji stanu skojarzonych z każdym składnikiem. Zaleca się, aby mierzyć zapotrzebowanie na zasoby podczas tworzenia aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem wyjścia w planowaniu miejsca docelowego wdrożenia: Jeśli oczekujesz, że aplikacja obsługuje 5000 równoczesnych użytkowników, należy wziąć pod uwagę budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~273 KB na użytkownika).

### <a name="opno-locsignalr-configuration"></a>SignalRKonfiguracji

BlazorAplikacje serwerowe używają SignalR ASP.NET Core do komunikowania się z przeglądarką. ['s hosting i skalowanie warunki mają zastosowanie do aplikacji serwera. SignalR](xref:signalr/publish-to-azure-web-app) Blazor

Blazordziała najlepiej, gdy używasz SignalR WebSockets jako transportu ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo.](xref:signalr/security) Długie sondowanie SignalR jest używany przez gdy WebSockets nie jest dostępny lub gdy aplikacja jest jawnie skonfigurowany do korzystania z długiego sondowania. Podczas wdrażania w usłudze Azure App Service należy skonfigurować aplikację do używania websockets w ustawieniach witryny Azure portal dla usługi. Aby uzyskać szczegółowe informacje na temat konfigurowania aplikacji dla usługi Azure App Service, zobacz [ SignalR wskazówki dotyczące publikowania](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-opno-locsignalr-service"></a>Usługa SignalR platformy Azure

Zalecamy korzystanie z Blazor [usługi Azure SignalR ](/azure/azure-signalr) dla aplikacji serwera. Usługa umożliwia skalowanie aplikacji Blazor serwera do dużej liczby równoczesnych SignalR połączeń. Ponadto globalny SignalR zasięg usługi i wysokowydajne centra danych znacznie pomagają w zmniejszaniu opóźnień ze względu na lokalizację geograficzną. Aby skonfigurować aplikację (i opcjonalnie SignalR aprowizować) usługę Azure:

1. Włącz usługę do obsługi *sesji przyklejonych,* gdzie klienci są [przekierowywane z powrotem do tego samego serwera podczas prerendering](xref:blazor/hosting-models#connection-to-the-server). Ustaw `ServerStickyMode` opcję lub wartość `Required`konfiguracj na . Zazwyczaj aplikacja tworzy konfigurację przy użyciu **jednego** z następujących metod:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Konfiguracja (użyj **jednego** z następujących podejść):
  
     * *appsettings.json*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * **Ustawienia aplikacji** **konfiguracji** > usługi aplikacji w witrynie `Azure:SignalR:ServerStickyMode`Azure portal (**Nazwa**: , **Wartość**: `Required`).

1. Utwórz profil publikowania usługi Azure Blazor Apps w programie Visual Studio dla aplikacji server.
1. Dodaj zależność **usługi Azure SignalR ** do profilu. Jeśli subskrypcja platformy Azure nie ma SignalR wcześniej istniejącego wystąpienia usługi Azure do przypisania do aplikacji, wybierz pozycję **Utwórz nowe wystąpienie SignalR usługi Azure,** aby aprowizować nowe wystąpienie usługi.
1. Publikowanie aplikacji na platformie Azure

#### <a name="iis"></a>IIS

Podczas korzystania z usługi IIS włącz:

* [WebSockets na IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sesje przyklejone z routingiem żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Tworzenie definicji transferu ruchu przychodzącego z [następującymi adnotacjami Kubernetes dla sesji przyklejonych:](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>System Linux z serwerem Nginx

Aby SignalR zestawy WebSockets działały poprawnie, upewnij `Upgrade` `Connection` się, że serwer proxy i `$connection_upgrade` nagłówki są ustawione na następujące wartości i które są mapowane na:

* Wartość nagłówka uaktualnienia domyślnie.
* `close`gdy brakuje nagłówka uaktualnienia lub jest on pusty.

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [NGINX jako serwer proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxy websocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Mierzenie opóźnienia sieci

[JS interop](xref:blazor/call-javascript-from-dotnet) może służyć do pomiaru opóźnienia sieci, jak pokazano w poniższym przykładzie:

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

Aby uzyskać odpowiednie środowisko interfejsu użytkownika, zaleca się stałe opóźnienie interfejsu użytkownika 250ms lub mniej.

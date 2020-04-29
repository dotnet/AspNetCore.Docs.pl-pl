---
title: Hostowanie i wdrażanie Blazor serwera ASP.NET Core
author: guardrex
description: Dowiedz się, jak hostować Blazor i wdrażać aplikację serwera przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 380bbab8898b4fbeab4efa514b17b807accbb1ac
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205868"
---
# <a name="host-and-deploy-blazor-server"></a>Hostowanie i Blazor Wdrażanie serwera

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

Aplikacje serwera mogą akceptować [ogólne wartości konfiguracji hosta](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Wdrożenie

Przy użyciu [ Blazor modelu hostingu serwera](xref:blazor/hosting-models#blazor-server)program Blazor jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.

Wymagany jest serwer sieci Web obsługujący aplikację ASP.NET Core. Program Visual Studio zawiera szablon projektu ** Blazor aplikacji serwera** (`blazorserverside` szablon, który jest używany przez polecenie [dotnet New](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Skalowalność

Zaplanuj wdrożenie, aby najlepiej wykorzystać dostępną infrastrukturę dla aplikacji Blazor serwerowej. Zobacz następujące zasoby, aby rozwiązać Blazor skalowalność aplikacji serwera:

* [Podstawowe aplikacje Blazor serwera](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a>Serwer wdrażania

Gdy rozważasz skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który zostanie wyczerpany przez aplikację w miarę wzrostu wymagań użytkownika. Dostępna pamięć na serwerze ma wpływ na:

* Liczba aktywnych obwodów obsługiwanych przez serwer.
* Opóźnienie interfejsu użytkownika na kliencie.

Aby uzyskać wskazówki dotyczące tworzenia bezpiecznych i Blazor skalowalnych aplikacji serwerowych, zobacz <xref:security/blazor/server/threat-mitigation>.

Każdy obwód wykorzystuje około 250 KB pamięci w przypadku aplikacji o minimalnej *Hello World*. Rozmiar obwodu zależy od kodu aplikacji i wymagań dotyczących konserwacji stanu związanych z poszczególnymi składnikami. Zalecamy mierzenie wymagań dotyczących zasobów podczas opracowywania aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem początkowym w planowaniu celu wdrożenia: jeśli oczekujesz, że aplikacja będzie obsługiwać 5 000 współbieżnych użytkowników, należy rozważyć budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~ 273 KB na użytkownika).

### <a name="signalr-configuration"></a>SignalRskonfigurować

BlazorAplikacje serwera używają ASP.NET Core SignalR do komunikowania się z przeglądarką. warunki hostingu i skalowania dotyczą aplikacji Blazor serwerowych. [ SignalR](xref:signalr/publish-to-azure-web-app)

Blazornajlepiej sprawdza się w przypadku korzystania z usługi WebSockets jako SignalR transportu ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo](xref:signalr/security). Długie sondowanie jest używane przez SignalR , gdy obiekty WebSockets nie są dostępne lub gdy aplikacja jest jawnie skonfigurowana do korzystania z długotrwałego sondowania. Podczas wdrażania programu w celu Azure App Service Skonfiguruj aplikację do używania obiektów WebSockets w ustawieniach Azure Portal dla usługi. Aby uzyskać szczegółowe informacje dotyczące konfigurowania aplikacji na potrzeby Azure App Service, zobacz [ SignalR wskazówki dotyczące publikowania](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>Usługa SignalR platformy Azure

Zalecamy korzystanie z [usługi platformy SignalR Azure](/azure/azure-signalr) dla Blazor aplikacji serwerowych. Usługa umożliwia skalowanie aplikacji Blazor serwera do dużej liczby jednoczesnych SignalR połączeń. Ponadto globalne zasięgi SignalR i wysokiej wydajności centra danych usługi znacznie ułatwiają zredukowanie opóźnień ze względu na lokalizację geograficzną. Aby skonfigurować aplikację (i opcjonalnie zainicjować obsługę administracyjną) SignalR usługi platformy Azure:

1. Włącz obsługę *sesji usługi Sticky Notes*, w przypadku których klienci są [przekierowywani z powrotem do tego samego serwera podczas renderowania](xref:blazor/hosting-models#connection-to-the-server). Ustaw `ServerStickyMode` opcję lub wartość konfiguracji na `Required`. Zazwyczaj aplikacja tworzy konfigurację przy użyciu **jednej** z następujących metod:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Konfiguracja (Użyj **jednej** z następujących metod):
  
     * *appSettings. JSON*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * **Ustawienia aplikacji** **konfiguracji** > usługi App Service w Azure Portal (**Nazwa**: `Azure:SignalR:ServerStickyMode`, **wartość**: `Required`).

1. Utwórz profil publikowania aplikacji platformy Azure w programie Visual Studio dla Blazor aplikacji serwerowej.
1. Dodaj zależność **usługi SignalR platformy Azure** do profilu. Jeśli subskrypcja platformy Azure nie ma istniejącego wystąpienia usługi platformy Azure SignalR , które ma zostać przypisane do aplikacji, wybierz opcję **Utwórz nowe wystąpienie SignalR usługi platformy Azure** , aby udostępnić nowe wystąpienie usługi.
1. Publikowanie aplikacji na platformie Azure

#### <a name="iis"></a>IIS

W przypadku korzystania z usług IIS Włącz:

* Obiekty [WebSockets w usługach IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sesje programu Sticky w ramach routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Utwórz definicję transferu danych przychodzących z następującymi [adnotacjami Kubernetes dla sesji programu Sticky Notes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

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

SignalR Aby elementy WebSockets działały prawidłowo, upewnij się, że `Upgrade` na `Connection` serwerze proxy i w nagłówkach zostały ustawione następujące `$connection_upgrade` wartości, które są mapowane na:

* Domyślnie wartość nagłówka uaktualnienia.
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

* [NGINX jako serwer proxy protokołu WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Serwer proxy protokołu WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Mierzenie opóźnienia sieci

Przy użyciu kodu [js Interop](xref:blazor/call-javascript-from-dotnet) można mierzyć opóźnienia sieci, jak pokazano w poniższym przykładzie:

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

W celu uzyskania odpowiedniego środowiska interfejsu użytkownika zalecamy opóźnienia interfejsu użytkownika 250ms lub mniej.

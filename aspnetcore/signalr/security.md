---
title: Zagadnienia dotyczące zabezpieczeń w ASP.NET CoreSignalR
author: bradygaster
description: Dowiedz się, jak używać uwierzytelniania i autoryzacji SignalRw programie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/security
ms.openlocfilehash: 2b049d9d8131c6c95b2f768620c984d0f67f92cc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775326"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Zagadnienia dotyczące zabezpieczeń w ASP.NET CoreSignalR

Według [Andrew Stanton-pielęgniarki](https://twitter.com/anurse)

Ten artykuł zawiera informacje dotyczące zabezpieczania SignalR.

## <a name="cross-origin-resource-sharing"></a>Współużytkowanie zasobów między źródłami

[Współużytkowanie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/) może służyć do zezwalania na SignalR połączenia między źródłami w przeglądarce. Jeśli kod JavaScript jest hostowany w innej domenie z SignalR aplikacji, należy włączyć [oprogramowanie pośredniczące CORS](xref:security/cors) , aby umożliwić programowi JavaScript łączenie się z SignalR aplikacją. Zezwalaj na żądania między źródłami tylko z domen, które ufają lub kontrolują. Przykład:

* Twoja witryna jest hostowana`http://www.example.com`
* Twoja SignalR aplikacja jest hostowana`http://signalr.example.com`

Funkcję CORS należy skonfigurować w SignalR aplikacji tak, aby zezwalała na `www.example.com`źródło.

Aby uzyskać więcej informacji na temat konfigurowania mechanizmu CORS, zobacz [Włączanie żądań między źródłami (CORS)](xref:security/cors). SignalR**wymaga** następujących zasad CORS:

* Zezwalaj na określone oczekiwane źródła. Umożliwienie dowolnego pochodzenia jest możliwe, ale **nie** jest bezpieczne lub zalecane.
* Metody `GET` http i `POST` muszą być dozwolone.
* Poświadczenia muszą być dozwolone w celu poprawnego działania sesji programu Sticky plików cookie. Muszą być włączone, nawet jeśli uwierzytelnianie nie jest używane.

::: moniker range=">= aspnetcore-5.0"

Jednak w 5,0 podano opcję w kliencie języka TypeScript, aby nie używać poświadczeń.
Opcja nieużywania poświadczeń powinna być używana tylko wtedy, gdy użytkownik wie o 100%, że poświadczenia, takie jak pliki cookie, nie są potrzebne w aplikacji (pliki cookie są używane przez usługę Azure App Service w przypadku używania wielu serwerów do sesji programu Sticky Notes).

::: moniker-end

Na przykład następujące zasady CORS umożliwiają klientowi w SignalR przeglądarce hostowanemu w `https://example.com` celu uzyskania dostępu SignalR do aplikacji hostowanej w `https://signalr.example.com`:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a>Ograniczenie pochodzenia obiektu WebSocket

::: moniker range=">= aspnetcore-2.2"

Ochrona zapewniana przez mechanizm CORS nie ma zastosowania do obiektów WebSockets. W przypadku ograniczenia pochodzenia dla obiektów WebSockets Odczytaj [ograniczenie pochodzenia elementu WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ochrona zapewniana przez mechanizm CORS nie ma zastosowania do obiektów WebSockets. Przeglądarki **nie**:

* Wykonaj żądania funkcji CORS przed inspekcją.
* Przestrzeganie ograniczeń określonych w `Access-Control` nagłówkach podczas wykonywania żądań WebSocket.

Jednak przeglądarki wysyłają `Origin` nagłówek podczas wystawiania żądań protokołu WebSocket. Aplikacje powinny być skonfigurowane do sprawdzania poprawności tych nagłówków, aby upewnić się, że dozwolone są tylko usługi WebSockets pochodzące z oczekiwanych źródeł.

W ASP.NET Core 2,1 i nowszych walidacji nagłówka można osiągnąć przy użyciu niestandardowego oprogramowania pośredniczącego umieszczonego **przed `UseSignalR`i uwierzytelniania oprogramowania pośredniczącego** w programie `Configure`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> `Origin` Nagłówek jest kontrolowany przez klienta i, podobnie jak `Referer` nagłówek, może być sfałszowany. Tych nagłówków **nie** należy używać jako mechanizmu uwierzytelniania.

::: moniker-end

## <a name="connectionid"></a>ConnectionId

Uwidacznianie `ConnectionId` może prowadzić do złośliwej personifikacji, SignalR Jeśli wersja serwera lub klienta jest ASP.NET Core 2,2 lub wcześniejsza. Jeśli wersja SignalR serwera i klienta jest ASP.NET Core 3,0 lub nowsza, `ConnectionToken` a nie `ConnectionId` musi być zachowana jako wpis tajny. Nie `ConnectionToken` jest to cel niewidoczny w żadnym interfejsie API.  Może być trudne, aby upewnić się SignalR , że starsi klienci nie będą łączyć się z serwerem, SignalR więc nawet jeśli wersja serwera ASP.NET Core 3,0 lub nowsza, nie `ConnectionId` powinno być ujawnione.

## <a name="access-token-logging"></a>Rejestrowanie tokenu dostępu

W przypadku korzystania z usługi WebSockets lub zdarzeń wysyłanych przez serwer klient przeglądarki wysyła token dostępu w ciągu zapytania. Uzyskiwanie tokenu dostępu za pośrednictwem ciągu zapytania jest zazwyczaj bezpieczne przy użyciu `Authorization` standardowego nagłówka. Zawsze używaj protokołu HTTPS, aby zapewnić bezpieczne połączenie między klientem a serwerem. Wiele serwerów sieci Web rejestruje adres URL dla każdego żądania, w tym ciąg zapytania. Rejestrowanie adresów URL może rejestrować token dostępu. ASP.NET Core domyślnie rejestruje adres URL dla każdego żądania, który będzie zawierać ciąg zapytania. Przykład:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Jeśli masz problemy z rejestrowaniem tych danych w dziennikach serwera, możesz wyłączyć to rejestrowanie całkowicie przez skonfigurowanie `Microsoft.AspNetCore.Hosting` rejestratora na `Warning` poziomie lub wyższym (te komunikaty są zapisywane na `Info` poziomie). Aby uzyskać więcej informacji, zobacz [filtrowanie dzienników](xref:fundamentals/logging/index#log-filtering) , aby uzyskać więcej informacji. Jeśli nadal chcesz rejestrować pewne informacje o żądaniu, możesz [napisać oprogramowanie pośredniczące](xref:fundamentals/middleware/write) w celu zarejestrowania wymaganych danych i odfiltrować wartość ciągu `access_token` zapytania (jeśli istnieje).

## <a name="exceptions"></a>Wyjątki

Komunikaty o wyjątkach są zwykle uznawane za dane poufne, które nie powinny być ujawnione dla klienta. Domyślnie program SignalR nie wysyła szczegółów dotyczących wyjątku zgłoszonego przez metodę centrum do klienta programu. Zamiast tego klient otrzymuje komunikat ogólny informujący o wystąpieniu błędu. Dostarczenie komunikatu o wyjątku do klienta może zostać zastąpione (na przykład w przypadku programowania lub testowania) za pomocą [EnableDetailedErrors](xref:signalr/configuration#configure-server-options). Komunikaty o wyjątkach nie powinny być udostępniane klientowi w aplikacjach produkcyjnych.

## <a name="buffer-management"></a>Zarządzanie buforem

SignalRużywa buforów dla połączeń przychodzących i wychodzących. Domyślnie program SignalR ogranicza te bufory do 32 KB. Największym komunikatem, który klient lub serwer może wysłać, to 32 KB. Maksymalna ilość pamięci zużywanej przez połączenie dla komunikatów to 32 KB. Jeśli komunikaty są zawsze mniejsze niż 32 KB, można zmniejszyć limit, który:

* Uniemożliwia klientowi wysyłanie większej wiadomości.
* Serwer nigdy nie będzie musiał przydzielić dużych buforów, aby akceptować komunikaty.

Jeśli rozmiar komunikatów przekracza 32 KB, można zwiększyć limit. Zwiększenie tego limitu oznacza:

* Klient może spowodować, że serwer przydzieli bufory dużych pamięci.
* Alokacja serwerów dużych buforów może obniżyć liczbę jednoczesnych połączeń.

Istnieją limity komunikatów przychodzących i wychodzących, obie można skonfigurować w obiekcie [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) skonfigurowanym w `MapHub`:

* `ApplicationMaxBufferSize`reprezentuje maksymalną liczbę bajtów od klienta, które buforuje serwer. Jeśli klient próbuje wysłać komunikat przekraczający ten limit, połączenie może być zamknięte.
* `TransportMaxBufferSize`reprezentuje maksymalną liczbę bajtów, które może wysłać serwer. Jeśli serwer próbuje wysłać komunikat (uwzględniając wartości zwracane z metod centralnych) większy niż ten limit, zostanie zgłoszony wyjątek.

Ustawienie limitu powoduje `0` wyłączenie limitu. Usunięcie limitu umożliwia klientowi wysłanie komunikatu o dowolnym rozmiarze. Złośliwi klienci wysyłający duże wiadomości mogą spowodować przydzielenie nadmiernej ilości pamięci. Nadmierne użycie pamięci może znacznie zmniejszyć liczbę jednoczesnych połączeń.

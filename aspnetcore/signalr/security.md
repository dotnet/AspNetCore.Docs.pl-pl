---
title: Zagadnienia dotyczące zabezpieczeń w ASP.NET CoreSignalR
author: bradygaster
description: Dowiedz się, jak używać uwierzytelniania i autoryzacji w programie ASP.NET Core SignalR .
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
ms.openlocfilehash: b80ece8c74c0f1d4d8518f0da16a91db9687336c
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755890"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="677c4-103">Zagadnienia dotyczące zabezpieczeń w ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="677c4-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="677c4-104">Według [Andrew Stanton-pielęgniarki](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="677c4-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="677c4-105">Ten artykuł zawiera informacje dotyczące zabezpieczania SignalR .</span><span class="sxs-lookup"><span data-stu-id="677c4-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="677c4-106">Współużytkowanie zasobów między źródłami</span><span class="sxs-lookup"><span data-stu-id="677c4-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="677c4-107">[Współużytkowanie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/) może służyć do zezwalania na połączenia między źródłami SignalR w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="677c4-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="677c4-108">Jeśli kod JavaScript jest hostowany w innej domenie z SignalR aplikacji, należy włączyć [oprogramowanie pośredniczące CORS](xref:security/cors) , aby umożliwić programowi JavaScript łączenie się z SignalR aplikacją.</span><span class="sxs-lookup"><span data-stu-id="677c4-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="677c4-109">Zezwalaj na żądania między źródłami tylko z domen, które ufają lub kontrolują.</span><span class="sxs-lookup"><span data-stu-id="677c4-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="677c4-110">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="677c4-110">For example:</span></span>

* <span data-ttu-id="677c4-111">Twoja witryna jest hostowana`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="677c4-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="677c4-112">Twoja SignalR aplikacja jest hostowana`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="677c4-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="677c4-113">Funkcję CORS należy skonfigurować w aplikacji tak, SignalR aby zezwalała na `www.example.com` Źródło.</span><span class="sxs-lookup"><span data-stu-id="677c4-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="677c4-114">Aby uzyskać więcej informacji na temat konfigurowania mechanizmu CORS, zobacz [Włączanie żądań między źródłami (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="677c4-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="677c4-115">**wymaga** następujących zasad CORS:</span><span class="sxs-lookup"><span data-stu-id="677c4-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="677c4-116">Zezwalaj na określone oczekiwane źródła.</span><span class="sxs-lookup"><span data-stu-id="677c4-116">Allow the specific expected origins.</span></span> <span data-ttu-id="677c4-117">Umożliwienie dowolnego pochodzenia jest możliwe, ale **nie** jest bezpieczne lub zalecane.</span><span class="sxs-lookup"><span data-stu-id="677c4-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="677c4-118">Metody HTTP `GET` i `POST` muszą być dozwolone.</span><span class="sxs-lookup"><span data-stu-id="677c4-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="677c4-119">Poświadczenia muszą być dozwolone w celu poprawnego działania sesji programu Sticky plików cookie.</span><span class="sxs-lookup"><span data-stu-id="677c4-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="677c4-120">Muszą być włączone, nawet jeśli uwierzytelnianie nie jest używane.</span><span class="sxs-lookup"><span data-stu-id="677c4-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="677c4-121">Jednak w 5,0 podano opcję w kliencie języka TypeScript, aby nie używać poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="677c4-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="677c4-122">Opcja nieużywania poświadczeń powinna być używana tylko wtedy, gdy użytkownik wie o 100%, że poświadczenia, takie jak pliki cookie, nie są potrzebne w aplikacji (pliki cookie są używane przez usługę Azure App Service w przypadku używania wielu serwerów do sesji programu Sticky Notes).</span><span class="sxs-lookup"><span data-stu-id="677c4-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="677c4-123">Na przykład następujące zasady CORS umożliwiają SignalR klientowi w przeglądarce hostowanemu w `https://example.com` celu uzyskania dostępu do SignalR aplikacji hostowanej w `https://signalr.example.com` :</span><span class="sxs-lookup"><span data-stu-id="677c4-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

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
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="677c4-124">Ograniczenie pochodzenia obiektu WebSocket</span><span class="sxs-lookup"><span data-stu-id="677c4-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="677c4-125">Ochrona zapewniana przez mechanizm CORS nie ma zastosowania do obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="677c4-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="677c4-126">W przypadku ograniczenia pochodzenia dla obiektów WebSockets Odczytaj [ograniczenie pochodzenia elementu WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="677c4-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="677c4-127">Ochrona zapewniana przez mechanizm CORS nie ma zastosowania do obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="677c4-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="677c4-128">Przeglądarki **nie**:</span><span class="sxs-lookup"><span data-stu-id="677c4-128">Browsers do **not**:</span></span>

* <span data-ttu-id="677c4-129">Wykonaj żądania funkcji CORS przed inspekcją.</span><span class="sxs-lookup"><span data-stu-id="677c4-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="677c4-130">Przestrzeganie ograniczeń określonych w `Access-Control` nagłówkach podczas wykonywania żądań WebSocket.</span><span class="sxs-lookup"><span data-stu-id="677c4-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="677c4-131">Jednak przeglądarki wysyłają `Origin` nagłówek podczas wystawiania żądań protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="677c4-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="677c4-132">Aplikacje powinny być skonfigurowane do sprawdzania poprawności tych nagłówków, aby upewnić się, że dozwolone są tylko usługi WebSockets pochodzące z oczekiwanych źródeł.</span><span class="sxs-lookup"><span data-stu-id="677c4-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="677c4-133">W ASP.NET Core 2,1 i nowszych walidacji nagłówka można osiągnąć przy użyciu niestandardowego oprogramowania pośredniczącego umieszczonego **przed `UseSignalR` i uwierzytelniania oprogramowania pośredniczącego** w programie `Configure` :</span><span class="sxs-lookup"><span data-stu-id="677c4-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="677c4-134">`Origin`Nagłówek jest kontrolowany przez klienta i, podobnie jak `Referer` nagłówek, może być sfałszowany.</span><span class="sxs-lookup"><span data-stu-id="677c4-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="677c4-135">Tych nagłówków **nie** należy używać jako mechanizmu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="677c4-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="677c4-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="677c4-136">ConnectionId</span></span>

<span data-ttu-id="677c4-137">Uwidacznianie `ConnectionId` może prowadzić do złośliwej personifikacji, jeśli SignalR wersja serwera lub klienta jest ASP.NET Core 2,2 lub wcześniejsza.</span><span class="sxs-lookup"><span data-stu-id="677c4-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="677c4-138">Jeśli SignalR wersja serwera i klienta jest ASP.NET Core 3,0 lub nowsza, `ConnectionToken` a nie `ConnectionId` musi być zachowana jako wpis tajny.</span><span class="sxs-lookup"><span data-stu-id="677c4-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="677c4-139">`ConnectionToken`Nie jest to cel niewidoczny w żadnym interfejsie API.</span><span class="sxs-lookup"><span data-stu-id="677c4-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="677c4-140">Może być trudne, aby upewnić się, że starsi SignalR klienci nie będą łączyć się z serwerem, więc nawet jeśli SignalR wersja serwera ASP.NET Core 3,0 lub nowsza, nie `ConnectionId` powinno być ujawnione.</span><span class="sxs-lookup"><span data-stu-id="677c4-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="677c4-141">Rejestrowanie tokenu dostępu</span><span class="sxs-lookup"><span data-stu-id="677c4-141">Access token logging</span></span>

<span data-ttu-id="677c4-142">W przypadku korzystania z usługi WebSockets lub zdarzeń wysyłanych przez serwer klient przeglądarki wysyła token dostępu w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="677c4-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="677c4-143">Uzyskiwanie tokenu dostępu za pośrednictwem ciągu zapytania jest zazwyczaj bezpieczne przy użyciu standardowego `Authorization` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="677c4-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="677c4-144">Zawsze używaj protokołu HTTPS, aby zapewnić bezpieczne połączenie między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="677c4-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="677c4-145">Wiele serwerów sieci Web rejestruje adres URL dla każdego żądania, w tym ciąg zapytania.</span><span class="sxs-lookup"><span data-stu-id="677c4-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="677c4-146">Rejestrowanie adresów URL może rejestrować token dostępu.</span><span class="sxs-lookup"><span data-stu-id="677c4-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="677c4-147">ASP.NET Core domyślnie rejestruje adres URL dla każdego żądania, który będzie zawierać ciąg zapytania.</span><span class="sxs-lookup"><span data-stu-id="677c4-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="677c4-148">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="677c4-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

<span data-ttu-id="677c4-149">Jeśli masz problemy z rejestrowaniem tych danych w dziennikach serwera, możesz wyłączyć to rejestrowanie całkowicie przez skonfigurowanie `Microsoft.AspNetCore.Hosting` rejestratora na `Warning` poziomie lub wyższym (te komunikaty są zapisywane na `Info` poziomie).</span><span class="sxs-lookup"><span data-stu-id="677c4-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="677c4-150">Aby uzyskać więcej informacji, zobacz [filtrowanie dzienników](xref:fundamentals/logging/index#log-filtering) , aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="677c4-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="677c4-151">Jeśli nadal chcesz rejestrować pewne informacje o żądaniu, możesz [napisać oprogramowanie pośredniczące](xref:fundamentals/middleware/write) w celu zarejestrowania wymaganych danych i odfiltrować `access_token` wartość ciągu zapytania (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="677c4-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="677c4-152">Wyjątki</span><span class="sxs-lookup"><span data-stu-id="677c4-152">Exceptions</span></span>

<span data-ttu-id="677c4-153">Komunikaty o wyjątkach są zwykle uznawane za dane poufne, które nie powinny być ujawnione dla klienta.</span><span class="sxs-lookup"><span data-stu-id="677c4-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="677c4-154">Domyślnie program SignalR nie wysyła szczegółów dotyczących wyjątku zgłoszonego przez metodę centrum do klienta programu.</span><span class="sxs-lookup"><span data-stu-id="677c4-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="677c4-155">Zamiast tego klient otrzymuje komunikat ogólny informujący o wystąpieniu błędu.</span><span class="sxs-lookup"><span data-stu-id="677c4-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="677c4-156">Dostarczenie komunikatu o wyjątku do klienta może zostać zastąpione (na przykład w przypadku programowania lub testowania) za pomocą [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="677c4-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="677c4-157">Komunikaty o wyjątkach nie powinny być udostępniane klientowi w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="677c4-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="677c4-158">Zarządzanie buforem</span><span class="sxs-lookup"><span data-stu-id="677c4-158">Buffer management</span></span>

SignalR<span data-ttu-id="677c4-159">używa buforów dla połączeń przychodzących i wychodzących.</span><span class="sxs-lookup"><span data-stu-id="677c4-159"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="677c4-160">Domyślnie program SignalR ogranicza te bufory do 32 KB.</span><span class="sxs-lookup"><span data-stu-id="677c4-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="677c4-161">Największym komunikatem, który klient lub serwer może wysłać, to 32 KB.</span><span class="sxs-lookup"><span data-stu-id="677c4-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="677c4-162">Maksymalna ilość pamięci zużywanej przez połączenie dla komunikatów to 32 KB.</span><span class="sxs-lookup"><span data-stu-id="677c4-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="677c4-163">Jeśli komunikaty są zawsze mniejsze niż 32 KB, można zmniejszyć limit, który:</span><span class="sxs-lookup"><span data-stu-id="677c4-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="677c4-164">Uniemożliwia klientowi wysyłanie większej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="677c4-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="677c4-165">Serwer nigdy nie będzie musiał przydzielić dużych buforów, aby akceptować komunikaty.</span><span class="sxs-lookup"><span data-stu-id="677c4-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="677c4-166">Jeśli rozmiar komunikatów przekracza 32 KB, można zwiększyć limit.</span><span class="sxs-lookup"><span data-stu-id="677c4-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="677c4-167">Zwiększenie tego limitu oznacza:</span><span class="sxs-lookup"><span data-stu-id="677c4-167">Increasing this limit means:</span></span>

* <span data-ttu-id="677c4-168">Klient może spowodować, że serwer przydzieli bufory dużych pamięci.</span><span class="sxs-lookup"><span data-stu-id="677c4-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="677c4-169">Alokacja serwerów dużych buforów może obniżyć liczbę jednoczesnych połączeń.</span><span class="sxs-lookup"><span data-stu-id="677c4-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="677c4-170">Istnieją limity komunikatów przychodzących i wychodzących, obie można skonfigurować w obiekcie [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) skonfigurowanym w `MapHub` :</span><span class="sxs-lookup"><span data-stu-id="677c4-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="677c4-171">`ApplicationMaxBufferSize`reprezentuje maksymalną liczbę bajtów od klienta, które buforuje serwer.</span><span class="sxs-lookup"><span data-stu-id="677c4-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="677c4-172">Jeśli klient próbuje wysłać komunikat przekraczający ten limit, połączenie może być zamknięte.</span><span class="sxs-lookup"><span data-stu-id="677c4-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="677c4-173">`TransportMaxBufferSize`reprezentuje maksymalną liczbę bajtów, które może wysłać serwer.</span><span class="sxs-lookup"><span data-stu-id="677c4-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="677c4-174">Jeśli serwer próbuje wysłać komunikat (uwzględniając wartości zwracane z metod centralnych) większy niż ten limit, zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="677c4-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="677c4-175">Ustawienie limitu powoduje `0` wyłączenie limitu.</span><span class="sxs-lookup"><span data-stu-id="677c4-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="677c4-176">Usunięcie limitu umożliwia klientowi wysłanie komunikatu o dowolnym rozmiarze.</span><span class="sxs-lookup"><span data-stu-id="677c4-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="677c4-177">Złośliwi klienci wysyłający duże wiadomości mogą spowodować przydzielenie nadmiernej ilości pamięci.</span><span class="sxs-lookup"><span data-stu-id="677c4-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="677c4-178">Nadmierne użycie pamięci może znacznie zmniejszyć liczbę jednoczesnych połączeń.</span><span class="sxs-lookup"><span data-stu-id="677c4-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>

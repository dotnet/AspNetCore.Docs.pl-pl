---
title: Uwierzytelnianie i autoryzacja w ASP.NET CoreSignalR
author: bradygaster
description: Dowiedz się, jak używać uwierzytelniania i autoryzacji w programie ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 1e022c510dda3e39dd02d607f1d9c493aecdeb5a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021565"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a>Uwierzytelnianie i autoryzacja w ASP.NET CoreSignalR

Według [Andrew Stanton-pielęgniarki](https://twitter.com/anurse)

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a>Uwierzytelnianie użytkowników łączących się z SignalR centrum

SignalRmożna go używać z [uwierzytelnianiem ASP.NET Core](xref:security/authentication/identity) , aby skojarzyć użytkownika z każdym połączeniem. W centrum dane uwierzytelniania są dostępne z poziomu właściwości [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . Uwierzytelnianie umożliwia centrum wywoływanie metod we wszystkich połączeniach skojarzonych z użytkownikiem. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i grupami w programie SignalR ](xref:signalr/groups). Wiele połączeń może być skojarzonych z pojedynczym użytkownikiem.

Poniżej przedstawiono przykład `Startup.Configure` użycia SignalR i ASP.NET Core uwierzytelniania:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> Kolejność, w której rejestrowane są SignalR zagadnienia dotyczące oprogramowania pośredniczącego uwierzytelniania i ASP.NET Core. Zawsze wywołuj `UseAuthentication` przed `UseSignalR` tym, że SignalR ma użytkownika na `HttpContext` .

::: moniker-end

### <a name="no-loccookie-authentication"></a>Cookieponowne

W aplikacji opartej na przeglądarce cookie uwierzytelnianie umożliwia istniejące poświadczenia użytkownika w celu automatycznego przechodzenia do SignalR połączeń. W przypadku korzystania z klienta przeglądarki nie jest wymagana dodatkowa konfiguracja. Jeśli użytkownik jest zalogowany do aplikacji, SignalR połączenie automatycznie dziedziczy to uwierzytelnianie.

Cookies to specyficzny dla przeglądarki sposób wysyłania tokenów dostępu, ale klienci nie korzystający z przeglądarki mogą wysyłać je. W przypadku korzystania z [klienta platformy .NET](xref:signalr/dotnet-client) `Cookies` Właściwość można skonfigurować w wywołaniu, `.WithUrl` Aby zapewnić cookie . Jednak używanie cookie uwierzytelniania z poziomu klienta .NET wymaga, aby aplikacja zapewniała interfejs API do danych uwierzytelniania programu Exchange cookie .

### <a name="bearer-token-authentication"></a>Uwierzytelnianie tokenu okaziciela

Klient może dostarczyć token dostępu zamiast używać cookie . Serwer sprawdza token i używa go do identyfikowania użytkownika. Sprawdzanie poprawności jest wykonywane tylko wtedy, gdy połączenie zostało nawiązane. W trakcie trwania połączenia serwer nie zostanie automatycznie ponownie sprawdzony w celu sprawdzenia odwołania tokenu.

Na serwerze uwierzytelnianie tokenu okaziciela jest konfigurowane przy użyciu [oprogramowania pośredniczącego okaziciela JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

W kliencie JavaScript token może być dostarczony przy użyciu opcji [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

W kliencie .NET jest podobna Właściwość [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) , która może służyć do konfigurowania tokenu:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Funkcja tokenu dostępu dostarczana jest wywoływana przed **każdym** żądaniem http wykonywanym przez SignalR . Jeśli musisz odnowić token, aby zachować aktywne połączenie (ponieważ może ono wygasnąć podczas połączenia), zrób to w ramach tej funkcji i zwróć zaktualizowany token.

W standardowym interfejsie API sieci Web tokeny okaziciela są wysyłane w nagłówku HTTP. Jednak program SignalR nie może ustawić tych nagłówków w przeglądarkach w przypadku korzystania z niektórych transportów. W przypadku korzystania z obiektów WebSockets i zdarzeń wysyłanych przez serwer token jest przekazywany jako parametr ciągu zapytania. Aby można było obsługiwać ten serwer na serwerze, wymagana jest dodatkowa konfiguracja:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> Ciąg zapytania jest używany w przeglądarkach w przypadku nawiązywania połączenia z usługą WebSockets i zdarzeniami wysłanymi przez serwer z powodu ograniczeń interfejsu API przeglądarki. W przypadku korzystania z protokołu HTTPS wartości ciągu zapytania są zabezpieczane przez połączenie TLS. Jednak wiele serwerów rejestruje wartości ciągu zapytania. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące SignalR zabezpieczeń w ASP.NET Core ](xref:signalr/security). SignalRużywa nagłówków do przesyłania tokenów w środowiskach, które je obsługują (takich jak klienci .NET i Java).

### <a name="no-loccookies-vs-bearer-tokens"></a>Cookietokeny s i Bearer 

Cookies są specyficzne dla przeglądarek. Wysyłanie ich z innych rodzajów klientów zwiększa złożoność w porównaniu do wysyłania tokenów okaziciela. W związku z tym cookie uwierzytelnianie nie jest zalecane, chyba że aplikacja wymaga tylko uwierzytelnienia użytkowników z poziomu klienta przeglądarki. Uwierzytelnianie tokenów okaziciela jest zalecanym rozwiązaniem w przypadku korzystania z klientów innych niż klient przeglądarki.

### <a name="windows-authentication"></a>Uwierzytelnianie Windows

Jeśli w aplikacji skonfigurowano [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) , program SignalR może używać tej tożsamości do zabezpieczania centrów. Aby jednak wysyłać komunikaty do poszczególnych użytkowników, należy dodać niestandardowego dostawcę identyfikatora użytkownika. System uwierzytelniania systemu Windows nie zapewnia żądania "name identifier". SignalRużywa tego żądania, aby określić nazwę użytkownika.

Dodaj nową klasę, która implementuje `IUserIdProvider` i pobiera jedno z oświadczeń od użytkownika do użycia jako identyfikator. Aby na przykład użyć żądania "name" (czyli nazwy użytkownika systemu Windows w formularzu `[Domain]\[Username]` ), Utwórz następującą klasę:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Zamiast programu `ClaimTypes.Name` można użyć dowolnej wartości z (na przykład `User` identyfikatora SID systemu Windows itd.).

> [!NOTE]
> Wybrana wartość musi być unikatowa wśród wszystkich użytkowników w systemie. W przeciwnym razie komunikat przeznaczony dla jednego użytkownika może zostać zakończony przez innego użytkownika.

Zarejestruj ten składnik w `Startup.ConfigureServices` metodzie.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

W kliencie .NET należy włączyć uwierzytelnianie systemu Windows, ustawiając właściwość [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Uwierzytelnianie systemu Windows jest obsługiwane w programach Internet Explorer i Microsoft Edge, ale nie we wszystkich przeglądarkach. Na przykład w przeglądarce Chrome i Safari próba użycia uwierzytelniania systemu Windows i gniazd sieciowych kończy się niepowodzeniem. W przypadku niepowodzenia uwierzytelniania systemu Windows klient próbuje wrócić do innych transportów, które mogą działać.

### <a name="use-claims-to-customize-identity-handling"></a>Używanie oświadczeń do dostosowywania obsługi tożsamości

Aplikacja, która uwierzytelnia użytkowników, może wyprowadzać SignalR identyfikatory użytkowników z oświadczeń użytkowników. Aby określić sposób SignalR tworzenia identyfikatorów użytkowników, implementowania `IUserIdProvider` i rejestrowania implementacji.

Przykładowy kod demonstruje, jak używać oświadczeń do wybierania adresu e-mail użytkownika jako właściwości identyfikującej. 

> [!NOTE]
> Wybrana wartość musi być unikatowa wśród wszystkich użytkowników w systemie. W przeciwnym razie komunikat przeznaczony dla jednego użytkownika może zostać zakończony przez innego użytkownika.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

Rejestracja konta dodaje `ClaimsTypes.Email` do bazy danych tożsamości ASP.NET zgłoszenie typu.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Zarejestruj ten składnik w `Startup.ConfigureServices` .

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autoryzuj użytkowników do uzyskiwania dostępu do centrów i metod centrów

Domyślnie wszystkie metody w koncentratorze mogą być wywoływane przez nieuwierzytelniony użytkownik. Aby wymagać uwierzytelniania, zastosuj atrybut [Autoryzuj](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) do centrum:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Można użyć argumentów konstruktora i właściwości `[Authorize]` atrybutu w celu ograniczenia dostępu tylko do użytkowników zgodnych z określonymi [zasadami autoryzacji](xref:security/authorization/policies). Na przykład jeśli masz niestandardowe zasady autoryzacji o nazwie, możesz `MyAuthorizationPolicy` upewnić się, że tylko użytkownicy pasujący do tych zasad mogą uzyskać dostęp do centrum przy użyciu następującego kodu:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Do poszczególnych metod centrów można `[Authorize]` również zastosować atrybut. Jeśli bieżący użytkownik nie jest zgodny z zasadami zastosowanymi do metody, zwracany jest błąd do obiektu wywołującego:

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Używanie programów obsługi autoryzacji do dostosowywania autoryzacji metody centrum

SignalRudostępnia zasób niestandardowy do obsługi autoryzacji, gdy metoda centrum wymaga autoryzacji. Zasób jest wystąpieniem `HubInvocationContext` . `HubInvocationContext`Obejmuje `HubCallerContext` , nazwę wywoływanej metody centrum oraz argumenty metody centrum.

Rozważmy przykład pokoju czatu umożliwiającego logowanie do wielu organizacji za pośrednictwem Azure Active Directory. Każda osoba mająca konto Microsoft może zalogować się do programu chat, ale tylko członkowie organizacji będącej właścicielem będą mogli uniemożliwić użytkownikom lub wyświetlać historie rozmów użytkowników. Ponadto możemy chcieć ograniczyć niektóre funkcje do określonych użytkowników. Korzystanie z zaktualizowanych funkcji w ASP.NET Core 3,0 jest to w całości możliwe. Zwróć uwagę na to, jak `DomainRestrictedRequirement` służy jako niestandardowy `IAuthorizationRequirement` . Teraz, gdy `HubInvocationContext` parametr zasobu jest przesyłany, wewnętrzna logika może sprawdzić kontekst, w którym jest wywoływana centrum, i podjąć decyzje dotyczące umożliwienia użytkownikowi wykonywania poszczególnych metod centrów.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

W programie `Startup.ConfigureServices` Dodaj nowe zasady, dostarczając niestandardowe `DomainRestrictedRequirement` wymagania jako parametr do tworzenia `DomainRestricted` zasad.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

W poprzednim przykładzie `DomainRestrictedRequirement` Klasa jest zarówno, `IAuthorizationRequirement` jak i `AuthorizationHandler` dla tego wymagania. Można podzielić te dwa składniki na osobne klasy w celu oddzielenia obaw. Zaletą podejścia przykładowego jest brak konieczności wstrzykiwania `AuthorizationHandler` podczas uruchamiania, ponieważ wymaganie i procedura obsługi są takie same.

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Uwierzytelnianie tokenu okaziciela w ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Autoryzacja na podstawie zasobów](xref:security/authorization/resourcebased)

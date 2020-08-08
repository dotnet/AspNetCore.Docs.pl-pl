---
title: Rozwiązywanie problemów i debugowanie ASP.NET Core projektów
author: Rick-Anderson
description: Zrozumienie i rozwiązywanie problemów z ostrzeżeniami i błędami w projektach ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: a05b5f85ee9e399daf35c32dabe0149be38ff6c2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021357"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Rozwiązywanie problemów i debugowanie ASP.NET Core projektów

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Poniższe linki udostępniają wskazówki dotyczące rozwiązywania problemów:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Konferencja NDC (Londyn, 2018): diagnozowanie problemów w aplikacjach ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [Blog ASP.NET: Rozwiązywanie problemów z wydajnością ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Ostrzeżenia zestaw .NET Core SDK

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Są zainstalowane zarówno 32-bitowe, jak i 64-bitowe wersje zestaw .NET Core SDK.

W oknie dialogowym **Nowy projekt** dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:

> Zainstalowane są zarówno 32-bitowe, jak i 64-bitowe wersje zestaw .NET Core SDK. Wyświetlane są tylko szablony z wersji 64-bitowych zainstalowanych w lokalizacji "C: \\ Program Files \\ dotnet \\ SDK \\ ".

To ostrzeżenie jest wyświetlane, gdy są zainstalowane zarówno wersje 32-bitowe (x86), jak i 64-bitowe (x64) [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) . Typowe przyczyny instalacji obu wersji obejmują:

* Instalator zestaw .NET Core SDK został pierwotnie pobrany przy użyciu maszyny 32-bitowej, ale następnie został skopiowany na komputer z systemem 64-bitowym.
* 32-bitowy zestaw .NET Core SDK został zainstalowany przez inną aplikację.
* Pobrano i zainstalowano nieprawidłową wersję.

Odinstaluj 32-bitowy zestaw .NET Core SDK, aby uniknąć tego ostrzeżenia. Odinstaluj z **Panelu sterowania**  >  **programy i funkcje**  >  **Odinstaluj lub zmień program**. Jeśli rozumiesz, dlaczego ostrzeżenie i jego konsekwencje, możesz zignorować to ostrzeżenie.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Zestaw .NET Core SDK jest zainstalowany w wielu lokalizacjach

W oknie dialogowym **Nowy projekt** dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:

> Zestaw .NET Core SDK jest zainstalowany w wielu lokalizacjach. Wyświetlane są tylko szablony z zestawów SDK zainstalowanych w lokalizacji "C: \\ Program Files \\ dotnet \\ SDK \\ ".

Ten komunikat jest wyświetlany w przypadku co najmniej jednej instalacji zestaw .NET Core SDK w katalogu poza językiem *C: \\ Program Files \\ \\ zestawu dotnet SDK \\ *. Zwykle zdarza się to, gdy zestaw .NET Core SDK został wdrożony na komputerze przy użyciu funkcji kopiowania/wklejania zamiast Instalatora MSI.

Odinstaluj wszystkie 32-bitowe zestawy SDK platformy .NET Core i środowiska uruchomieniowe, aby uniknąć tego ostrzeżenia. Odinstaluj z **Panelu sterowania**  >  **programy i funkcje**  >  **Odinstaluj lub zmień program**. Jeśli rozumiesz, dlaczego ostrzeżenie i jego konsekwencje, możesz zignorować to ostrzeżenie.

### <a name="no-net-core-sdks-were-detected"></a>Nie wykryto żadnych zestawów .NET Core SDK

* W oknie dialogowym **Nowy projekt** programu Visual Studio dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:

  > Nie wykryto żadnych zestawów .NET Core SDK, upewnij się, że są one uwzględnione w zmiennej środowiskowej `PATH` .

* Gdy wykonujesz `dotnet` polecenie, ostrzeżenie jest wyświetlane jako:

  > Nie można znaleźć żadnych zainstalowanych zestawów SDK dotnet.

Te ostrzeżenia są wyświetlane, gdy zmienna środowiskowa `PATH` nie wskazuje żadnych zestawów SDK platformy .NET Core na komputerze. Aby rozwiązać ten problem:

* Zainstaluj zestaw .NET Core SDK. Uzyskaj najnowszy Instalator z [programu .NET downloads](https://dotnet.microsoft.com/download).
* Sprawdź, czy `PATH` zmienna środowiskowa wskazuje lokalizację, w której zainstalowano zestaw SDK ( `C:\Program Files\dotnet\` 64-bitowy/x64 lub `C:\Program Files (x86)\dotnet\` 32-bitowy/x86). Instalator zestawu SDK zazwyczaj ustawia `PATH` . Zawsze Instaluj te same zestawy SDK i środowiska uruchomieniowe na tym samym komputerze.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Brak zestawu SDK po zainstalowaniu pakietu hostingu platformy .NET Core

Zainstalowanie [pakietu hostingu platformy .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modyfikuje, `PATH` kiedy instaluje środowisko uruchomieniowe programu .NET Core w celu wskazywania wersji 32-bitowej (x86) platformy .NET Core ( `C:\Program Files (x86)\dotnet\` ). Może to spowodować brak zestawów SDK, gdy zostanie użyte polecenie programu .NET Core 32-bitowe (x86) `dotnet` ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](#no-net-core-sdks-were-detected)). Aby rozwiązać ten problem, przejdź `C:\Program Files\dotnet\` do pozycji przed `C:\Program Files (x86)\dotnet\` `PATH` .

## <a name="obtain-data-from-an-app"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacja może odpowiadać na żądania, można uzyskać następujące dane z aplikacji za pomocą oprogramowania pośredniczącego:

* Żądanie: metoda, schemat, host, pathbase, ścieżka, ciąg zapytania, nagłówki
* Połączenie: zdalny adres IP, Port zdalny, lokalny adres IP, port lokalny, certyfikat klienta
* Identity: Nazwa, nazwa wyświetlana
* Ustawienia konfiguracji
* Zmienne środowiskowe

Umieść poniższy kod [oprogramowania pośredniczącego](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na początku `Startup.Configure` potoku przetwarzania żądania metody. Środowisko jest sprawdzane przed uruchomieniem oprogramowania pośredniczącego, aby upewnić się, że kod jest wykonywany tylko w środowisku deweloperskim.

Aby uzyskać środowisko, użyj jednej z następujących metod:

* Wstrzyknąć `IHostingEnvironment` do `Startup.Configure` metody i Sprawdź środowisko przy użyciu zmiennej lokalnej. Poniższy przykładowy kod demonstruje takie podejście.

* Przypisz środowisko do właściwości w `Startup` klasie. Sprawdź środowisko przy użyciu właściwości (na przykład `if (Environment.IsDevelopment())` ).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>Debuguj ASP.NET Core aplikacje

Poniższe linki zawierają informacje dotyczące debugowania ASP.NET Core aplikacji.

* [Debugowanie środowiska ASP Core w systemie Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Debugowanie programu .NET Core w systemie UNIX za pośrednictwem protokołu SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Szybki Start: debugowanie ASP.NET z debugerem programu Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Aby uzyskać więcej informacji o debugowaniu, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/2960) w usłudze GitHub.

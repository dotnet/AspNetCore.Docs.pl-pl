---
title: Rozwiązywanie problemów i debugowanie projektów ASP.NET podstawowych
author: Rick-Anderson
description: Zrozumienie i rozwiązywanie problemów z ostrzeżeniami i błędami w projektach ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511512"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Rozwiązywanie problemów i debugowanie projektów ASP.NET podstawowych

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Poniższe łącza zawierają wskazówki dotyczące rozwiązywania problemów:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Konferencja NDC (Londyn, 2018): Diagnozowanie problemów w ASP.NET podstawowych aplikacji](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET Blog: Rozwiązywanie problemów z wydajnością ASP.NET podstawowych](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Ostrzeżenia o sdku rdzenia .NET

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>32-bitowe i 64-bitowe wersje sdk .NET Core SDK są zainstalowane

W oknie dialogowym **Nowy projekt** dla ASP.NET Core może pojawić się następujące ostrzeżenie:

> Są zainstalowane zarówno 32-bitowe, jak i 64-bitowe wersje sdk .NET Core. Wyświetlane są tylko szablony z wersji 64-bitowych zainstalowanych w\\'C: Program\\Files dotnet\\sdk'.\\

To ostrzeżenie pojawia się po zainstalowaniu zarówno 32-bitowych (x86) i 64-bitowych (x64) wersji [.NET Core SDK.](https://dotnet.microsoft.com/download/dotnet-core) Typowe powody, dla których obie wersje mogą być zainstalowane, obejmują:

* Instalator .NET Core SDK został pierwotnie pobrany przy użyciu komputera 32-bitowego, ale następnie skopiowano go i zainstalowałeś na komputerze 64-bitowym.
* 32-bitowy pakiet SDK .NET Core został zainstalowany przez inną aplikację.
* Pobrano i zainstalowano niewłaściwą wersję.

Odinstaluj 32-bitowy pakiet SDK .NET Core, aby zapobiec temu ostrzeżeniu. Odinstaluj z **Panelu** > **sterowania Programy i funkcje** > **Odinstaluj lub zmień program**. Jeśli zrozumiesz, dlaczego ostrzeżenie występuje i jego konsekwencje, można zignorować ostrzeżenie.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Zestaw SDK .NET Core jest instalowany w wielu lokalizacjach

W oknie dialogowym **Nowy projekt** dla ASP.NET Core może pojawić się następujące ostrzeżenie:

> Zestaw SDK rdzenia .NET jest instalowany w wielu lokalizacjach. Wyświetlane są tylko szablony z pakietów\\SDK zainstalowanych w 'C: Program Files\\dotnet\\sdk'.\\

Ten komunikat jest wyświetlany, gdy masz co najmniej jedną instalację .NET Core SDK w katalogu poza *C:\\Program Files\\dotnet\\sdk\\*. Zwykle dzieje się tak, gdy pakiet .NET Core SDK został wdrożony na komputerze przy użyciu funkcji kopiowania/wklejania zamiast instalatora MSI.

Odinstaluj wszystkie 32-bitowe 32-bitowe core sdks .NET i środowiska wykonawcze, aby zapobiec temu ostrzeżeniu. Odinstaluj z **Panelu** > **sterowania Programy i funkcje** > **Odinstaluj lub zmień program**. Jeśli zrozumiesz, dlaczego ostrzeżenie występuje i jego konsekwencje, można zignorować ostrzeżenie.

### <a name="no-net-core-sdks-were-detected"></a>Nie wykryto żadnych podstawowych sdk .NET

* W oknie dialogowym Visual Studio **New Project** dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:

  > Nie wykryto żadnych podstawowych sdk .NET, upewnij się, że są one zawarte w zmiennej `PATH`środowiskowej .

* Podczas wykonywania `dotnet` polecenia ostrzeżenie jest wyświetlane w następujący sposób:

  > Nie można było znaleźć żadnych zainstalowanych dotnet SDK.

Te ostrzeżenia są wyświetlane, gdy zmienna `PATH` środowiskowa nie wskazuje żadnych .NET Core SDK na komputerze. Aby rozwiązać ten problem:

* Zainstaluj pakiet SDK rdzenia .NET. Pobierz najnowszy instalator z [pliku .NET Downloads](https://dotnet.microsoft.com/download).
* Sprawdź, `PATH` czy zmienna środowiskowa wskazuje lokalizację, w`C:\Program Files\dotnet\` której jest zainstalowany pakiet SDK (dla 64-bit/x64 lub `C:\Program Files (x86)\dotnet\` 32-bit/x86). Instalator SDK zwykle ustawia `PATH`plik . Zawsze instaluj te same bityści sdk i środowiska wykonawcze na tym samym komputerze.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Brak zestawu SDK po zainstalowaniu pakietu hostingowego .NET Core

Zainstalowanie [pakietu hostingowego .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modyfikuje `PATH` czas instalacji środowiska uruchomieniowego .NET Core w celu wskazania`C:\Program Files (x86)\dotnet\`32-bitowej (x86) wersji programu .NET Core ( ). Może to spowodować brakujących zestawów SDK, gdy zostanie użyte `dotnet` 32-bitowe (x86) polecenie .NET Core[(wykryto nie wykryto zestawów SDK .NET Core).](#no-net-core-sdks-were-detected) Aby rozwiązać ten `C:\Program Files\dotnet\` problem, przejdź `C:\Program Files (x86)\dotnet\` do `PATH`pozycji przed na .

## <a name="obtain-data-from-an-app"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacja jest w stanie odpowiadać na żądania, można uzyskać następujące dane z aplikacji za pomocą oprogramowania pośredniczącego:

* Metoda &ndash; żądania, schemat, host, baza ścieżek, ścieżka, ciąg zapytania, nagłówki
* Zdalny adres IP połączenia, &ndash; port zdalny, lokalny adres IP, port lokalny, certyfikat klienta
* Nazwa &ndash; tożsamości, nazwa wyświetlana
* Ustawienia konfiguracji
* Zmienne środowiskowe

Umieść następujący kod [oprogramowania pośredniczącego](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na początku potoku `Startup.Configure` przetwarzania żądania metody. Środowisko jest sprawdzane przed uruchomieniem oprogramowania pośredniczącego, aby upewnić się, że kod jest wykonywany tylko w środowisku deweloperskim.

Aby uzyskać środowisko, należy użyć jednej z następujących metod:

* Wstrzyknąć do `IHostingEnvironment` `Startup.Configure` metody i sprawdzić środowisko z zmienną lokalną. Poniższy przykładowy kod demonstruje to podejście.

* Przypisz środowisko do właściwości `Startup` w klasie. Sprawdź środowisko przy użyciu właściwości `if (Environment.IsDevelopment())`(na przykład).

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

## <a name="debug-aspnet-core-apps"></a>Debugowanie aplikacji ASP.NET Core

Poniższe łącza zawierają informacje na temat debugowania ASP.NET aplikacji Core.

* [Debugowanie rdzenia ASP w systemie Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Debugowanie .NET Core na unixie przez SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Szybki start: debugowanie ASP.NET za pomocą debugera programu Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Zobacz [ten problem GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) więcej informacji debugowania.

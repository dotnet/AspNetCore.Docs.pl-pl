---
title: Przykładowy plik cookie SameSite 2,1 ASP.NET Core MVC
author: rick-anderson
description: Przykładowy plik cookie SameSite 2,1 ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: ce301cd7e2cbfbfc724d78bd5734dff231d0ab93
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944740"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>Przykładowy plik cookie SameSite 2,1 ASP.NET Core MVC

ASP.NET Core 2,1 ma wbudowaną obsługę atrybutu [SameSite](https://www.owasp.org/index.php/SameSite) , ale został on zapisany w oryginalnym standardzie. [Zachowanie poprawek](https://github.com/dotnet/aspnetcore/issues/8212) zostało zmienione tak, `SameSite.None` Aby wyemitować atrybut sameSite o wartości `None` , a nie nie emitować wartości wcale. Jeśli nie chcesz emitować wartości, możesz ustawić `SameSite` Właściwość pliku cookie na-1.

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a>Pisanie atrybutu SameSite

Poniżej przedstawiono przykład sposobu pisania atrybutu SameSite w pliku cookie:

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>Ustawianie uwierzytelniania plików cookie i plików cookie stanu sesji

Uwierzytelnianie plików cookie, stan sesji i [różne inne składniki](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) ustawiają opcje sameSite za pomocą opcji plików cookie, na przykład

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

W poprzednim kodzie zarówno uwierzytelnianie plików cookie, jak i stan sesji ustawiają ich atrybut sameSite na `None` , emitujący atrybut o `None` wartości, a także ustawić atrybut Secure na true.

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

W przypadku uruchomienia [przykładowego projektu](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)Załaduj debuger przeglądarki na stronie początkowej i użyj go do wyświetlenia kolekcji plików cookie dla tej witryny. Aby to zrobić, naciśnij i przytrzymaj klawisz, `F12` a następnie wybierz `Application` kartę i kliknij adres URL witryny pod `Cookies` opcją w `Storage` sekcji.

![Lista plików cookie debugera przeglądarki](BrowserDebugger.png)

Na obrazie można zobaczyć, że plik cookie utworzony przez przykład po kliknięciu przycisku "Utwórz SameSite cookie" ma wartość atrybutu SameSite `Lax` , odpowiadającą wartości ustawionej w [przykładowym kodzie](#sampleCode).

## <a name="intercepting-cookies"></a><a name="interception"></a>Przechwytywanie plików cookie

W celu przechwycenia plików cookie w celu dostosowania wartości brak zgodnie z jej obsługą w agencie przeglądarki użytkownika należy użyć `CookiePolicy` oprogramowania pośredniczącego. Ta wartość musi być umieszczona w potoku żądania HTTP **przed** wszelkimi składnikami, które zapisują pliki cookie i są skonfigurowane w programie `ConfigureServices()` .

Aby wstawić go do użycia potoku `app.UseCookiePolicy()` w `Configure(IApplicationBuilder, IHostingEnvironment)` metodzie w [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs). Przykład:

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

Następnie w obszarze `ConfigureServices(IServiceCollection services)` Konfigurowanie zasad dotyczących plików cookie do wywołania klasy pomocnika, gdy pliki cookie są dołączane lub usuwane. Przykład:

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

Funkcja pomocnika `CheckSameSite(HttpContext, CookieOptions)` :

* Jest wywoływana, gdy pliki cookie są dołączane do żądania lub usuwane z żądania.
* Sprawdza, czy `SameSite` Właściwość jest ustawiona na `None` .
* Jeśli `SameSite` jest ustawiona na `None` , a bieżący agent użytkownika jest znany jako nieobsługujący wartości atrybutu none. Sprawdzanie jest wykonywane przy użyciu klasy [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :
  * Ustawia, `SameSite` aby nie emitować wartości przez ustawienie właściwości na`(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>.NET Framework określania wartości docelowej

ASP.NET Core i system. Web (ASP.NET Classic) mają niezależne implementacje SameSite. Poprawki SameSite KB dla .NET Framework nie są wymagane w przypadku używania ASP.NET Core, ani do programu System. Web SameSite minimalne wymagania wersji platformy (.NET 4.7.2) mają zastosowanie do ASP.NET Core.

ASP.NET Core na platformie .NET wymaga aktualizacji zależności pakietów NuGet w celu uzyskania odpowiednich poprawek.

Aby uzyskać ASP.NET Core zmiany .NET Framework upewnij się, że masz bezpośrednie odwołanie do pakietów i wersji z poprawkami (2.1.14 lub nowszymi wersjami 2,1).

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>Więcej informacji
 
[Aktualizacje](https://www.chromium.org/updates/same-site) 
 programu Chrome [ASP.NET Core dokumentacja SameSite](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) 
 [ASP.NET Core 2,1 SameSite zmiany anonsu](https://github.com/dotnet/aspnetcore/issues/8212)
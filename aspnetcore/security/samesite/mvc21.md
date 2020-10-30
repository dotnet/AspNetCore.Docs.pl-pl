---
title: 'Przykład ASP.NET Core SameSite 2,1 MVC :::no-loc(cookie):::'
author: rick-anderson
description: 'Przykład ASP.NET Core SameSite 2,1 MVC :::no-loc(cookie):::'
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051554"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="0f12d-103">Przykład ASP.NET Core SameSite 2,1 MVC :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="0f12d-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: sample</span></span>

<span data-ttu-id="0f12d-104">ASP.NET Core 2,1 ma wbudowaną obsługę atrybutu [SameSite](https://www.owasp.org/index.php/SameSite) , ale został on zapisany w oryginalnym standardzie.</span><span class="sxs-lookup"><span data-stu-id="0f12d-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="0f12d-105">[Zachowanie poprawek](https://github.com/dotnet/aspnetcore/issues/8212) zostało zmienione tak, `SameSite.None` Aby wyemitować atrybut sameSite o wartości `None` , a nie nie emitować wartości wcale.</span><span class="sxs-lookup"><span data-stu-id="0f12d-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="0f12d-106">Jeśli nie chcesz emitować wartości, możesz ustawić `SameSite` Właściwość na wartość :::no-loc(cookie)::: -1.</span><span class="sxs-lookup"><span data-stu-id="0f12d-106">If you want to not emit the value you can set the `SameSite` property on a :::no-loc(cookie)::: to -1.</span></span>

[!INCLUDE[](~/includes/SameSite:::no-loc(Identity):::.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="0f12d-107">Pisanie atrybutu SameSite</span><span class="sxs-lookup"><span data-stu-id="0f12d-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="0f12d-108">Poniżej przedstawiono przykład sposobu pisania atrybutu SameSite w :::no-loc(cookie)::: :</span><span class="sxs-lookup"><span data-stu-id="0f12d-108">Following is an example of how to write a SameSite attribute on a :::no-loc(cookie)::::</span></span>

```c#
var :::no-loc(cookie):::Options = new :::no-loc(Cookie):::Options
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the :::no-loc(cookie)::: to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the :::no-loc(cookie)::: to the response :::no-loc(cookie)::: collection
Response.:::no-loc(Cookie):::s.Append(:::no-loc(Cookie):::Name, ":::no-loc(cookie):::Value", :::no-loc(cookie):::Options);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="0f12d-109">Ustawianie :::no-loc(Cookie)::: stanu uwierzytelniania i sesji :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="0f12d-109">Setting :::no-loc(Cookie)::: Authentication and Session State :::no-loc(cookie):::s</span></span>

<span data-ttu-id="0f12d-110">:::no-loc(Cookie)::: uwierzytelnianie, stan sesji i [różne inne składniki](../samesite.md?view=aspnetcore-2.1) ustawiają opcje sameSiteymi za pomocą :::no-loc(Cookie)::: opcji, na przykład</span><span class="sxs-lookup"><span data-stu-id="0f12d-110">:::no-loc(Cookie)::: authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via :::no-loc(Cookie)::: options, for example</span></span>

```c#
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
        options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
        options.:::no-loc(Cookie):::.IsEssential = true;
    });

services.AddSession(options =>
{
    options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
    options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
    options.:::no-loc(Cookie):::.IsEssential = true;
});
```

<span data-ttu-id="0f12d-111">W powyższym kodzie :::no-loc(cookie)::: uwierzytelnianie i stan sesji ustawia ich atrybut sameSite na `None` , emitujący atrybut o `None` wartości, a także ustawić atrybut Secure na true.</span><span class="sxs-lookup"><span data-stu-id="0f12d-111">In the preceding code, both :::no-loc(cookie)::: authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="0f12d-112">Uruchamianie aplikacji przykładowej</span><span class="sxs-lookup"><span data-stu-id="0f12d-112">Run the sample</span></span>

<span data-ttu-id="0f12d-113">W przypadku uruchomienia [przykładowego projektu](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)Załaduj debuger przeglądarki na stronie początkowej i użyj go, aby wyświetlić :::no-loc(cookie)::: kolekcję dla witryny.</span><span class="sxs-lookup"><span data-stu-id="0f12d-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the :::no-loc(cookie)::: collection for the site.</span></span> <span data-ttu-id="0f12d-114">Aby to zrobić, naciśnij i przytrzymaj klawisz, `F12` a następnie wybierz `Application` kartę i kliknij adres URL witryny pod `:::no-loc(Cookie):::s` opcją w `Storage` sekcji.</span><span class="sxs-lookup"><span data-stu-id="0f12d-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `:::no-loc(Cookie):::s` option in the `Storage` section.</span></span>

![Debuger przeglądarki::: No-Loc (cookie)::: list](BrowserDebugger.png)

<span data-ttu-id="0f12d-116">Po kliknięciu przycisku "Utwórz SameSite" w powyższym obrazie można zobaczyć :::no-loc(cookie)::: , że został utworzony przez próbkę :::no-loc(Cookie)::: , która ma wartość atrybutu SameSite `Lax` , odpowiadającą wartości ustawionej w [przykładowym kodzie](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="0f12d-116">You can see from the image above that the :::no-loc(cookie)::: created by the sample when you click the "Create SameSite :::no-loc(Cookie):::" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="0f12d-117">Przechwytywanie :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="0f12d-117">Intercepting :::no-loc(cookie):::s</span></span>

<span data-ttu-id="0f12d-118">W celu przechwycenia elementu :::no-loc(cookie)::: s, aby dostosować wartość Brak zgodnie z obsługą agenta przeglądarki użytkownika, należy użyć `:::no-loc(Cookie):::Policy` oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="0f12d-118">In order to intercept :::no-loc(cookie):::s, to adjust the none value according to its support in the user's browser agent you must use the `:::no-loc(Cookie):::Policy` middleware.</span></span> <span data-ttu-id="0f12d-119">Ta wartość musi być umieszczona w potoku żądania HTTP **przed** wszelkimi składnikami, które zapisują :::no-loc(cookie)::: i konfigurują w ramach `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="0f12d-119">This must be placed into the http request pipeline **before** any components that write :::no-loc(cookie):::s and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="0f12d-120">Aby wstawić go do użycia potoku `app.Use:::no-loc(Cookie):::Policy()` w `Configure(IApplicationBuilder, IHostingEnvironment)` metodzie w [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="0f12d-120">To insert it into the pipeline use `app.Use:::no-loc(Cookie):::Policy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="0f12d-121">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0f12d-121">For example:</span></span>

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
    app.Use:::no-loc(Cookie):::Policy();
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

<span data-ttu-id="0f12d-122">Następnie w obszarze `ConfigureServices(IServiceCollection services)` Konfigurowanie :::no-loc(cookie)::: zasad do wywołania klasy pomocnika, gdy :::no-loc(cookie)::: s są dołączane lub usuwane.</span><span class="sxs-lookup"><span data-stu-id="0f12d-122">Then in the `ConfigureServices(IServiceCollection services)` configure the :::no-loc(cookie)::: policy to call out to a helper class when :::no-loc(cookie):::s are appended or deleted.</span></span> <span data-ttu-id="0f12d-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0f12d-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<:::no-loc(Cookie):::PolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppend:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
        options.OnDelete:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
    });
}

private void CheckSameSite(HttpContext httpContext, :::no-loc(Cookie):::Options options)
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

<span data-ttu-id="0f12d-124">Funkcja pomocnika `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)` :</span><span class="sxs-lookup"><span data-stu-id="0f12d-124">The helper function `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)`:</span></span>

* <span data-ttu-id="0f12d-125">Jest wywoływana, gdy :::no-loc(cookie)::: s jest dołączany do żądania lub usuwany z żądania.</span><span class="sxs-lookup"><span data-stu-id="0f12d-125">Is called when :::no-loc(cookie):::s are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="0f12d-126">Sprawdza, czy `SameSite` Właściwość jest ustawiona na `None` .</span><span class="sxs-lookup"><span data-stu-id="0f12d-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="0f12d-127">Jeśli `SameSite` jest ustawiona na `None` , a bieżący agent użytkownika jest znany jako nieobsługujący wartości atrybutu none.</span><span class="sxs-lookup"><span data-stu-id="0f12d-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="0f12d-128">Sprawdzanie jest wykonywane przy użyciu klasy [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="0f12d-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="0f12d-129">Ustawia, `SameSite` aby nie emitować wartości przez ustawienie właściwości na `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="0f12d-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="0f12d-130">.NET Framework określania wartości docelowej</span><span class="sxs-lookup"><span data-stu-id="0f12d-130">Targeting .NET Framework</span></span>

<span data-ttu-id="0f12d-131">ASP.NET Core i system. Web (ASP.NET Classic) mają niezależne implementacje SameSite.</span><span class="sxs-lookup"><span data-stu-id="0f12d-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="0f12d-132">Poprawki SameSite KB dla .NET Framework nie są wymagane w przypadku używania ASP.NET Core, ani do programu System. Web SameSite minimalne wymagania wersji platformy (.NET 4.7.2) mają zastosowanie do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0f12d-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="0f12d-133">ASP.NET Core na platformie .NET wymaga aktualizacji zależności pakietów NuGet w celu uzyskania odpowiednich poprawek.</span><span class="sxs-lookup"><span data-stu-id="0f12d-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="0f12d-134">Aby uzyskać ASP.NET Core zmiany .NET Framework upewnij się, że masz bezpośrednie odwołanie do pakietów i wersji z poprawkami (2.1.14 lub nowszymi wersjami 2,1).</span><span class="sxs-lookup"><span data-stu-id="0f12d-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.:::no-loc(Cookie):::Policy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="0f12d-135">Więcej informacji</span><span class="sxs-lookup"><span data-stu-id="0f12d-135">More Information</span></span>
 
<span data-ttu-id="0f12d-136">[Aktualizacje](https://www.chromium.org/updates/same-site) 
 programu Chrome [ASP.NET Core dokumentacja SameSite](../samesite.md?view=aspnetcore-2.1) 
 [ASP.NET Core 2,1 SameSite zmiany anonsu](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="0f12d-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>
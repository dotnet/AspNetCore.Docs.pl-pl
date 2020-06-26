---
title: Udostępnianie plików cookie uwierzytelniania między aplikacjami ASP.NET
author: rick-anderson
description: Dowiedz się, jak udostępniać pliki cookie uwierzytelniania między ASP.NET. x i aplikacjami ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: b8de5ea1da47c7813aac0137719989959d4c8ce4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407034"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="8731e-103">Udostępnianie plików cookie uwierzytelniania między aplikacjami ASP.NET</span><span class="sxs-lookup"><span data-stu-id="8731e-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="8731e-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8731e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8731e-105">Witryny sieci Web często składają się ze współpracujących aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8731e-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="8731e-106">Aby zapewnić obsługę logowania jednokrotnego, aplikacje sieci Web w ramach lokacji muszą udostępniać pliki cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="8731e-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="8731e-107">Aby obsłużyć ten scenariusz, stos ochrony danych umożliwia udostępnianie Katana plików cookie oraz ASP.NET Core biletów uwierzytelniania plików cookie.</span><span class="sxs-lookup"><span data-stu-id="8731e-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="8731e-108">W następujących przykładach:</span><span class="sxs-lookup"><span data-stu-id="8731e-108">In the examples that follow:</span></span>

* <span data-ttu-id="8731e-109">Nazwa pliku cookie uwierzytelniania jest ustawiona na wspólną wartość `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="8731e-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="8731e-110">`AuthenticationType`Jest ustawiona `Identity.Application` jawnie lub domyślnie.</span><span class="sxs-lookup"><span data-stu-id="8731e-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="8731e-111">Nazwa wspólnej aplikacji jest używana w celu umożliwienia systemowi ochrony danych udostępniania kluczy ochrony danych ( `SharedCookieApp` ).</span><span class="sxs-lookup"><span data-stu-id="8731e-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="8731e-112">`Identity.Application`jest używany jako schemat uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="8731e-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="8731e-113">Niezależnie od tego, jaki schemat jest używany, musi być używany spójnie *w ramach i między* udostępnionymi aplikacjami cookie jako schemat domyślny lub przez jawne ustawienie.</span><span class="sxs-lookup"><span data-stu-id="8731e-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="8731e-114">Schemat jest używany podczas szyfrowania i odszyfrowywania plików cookie, dlatego w aplikacjach musi być używany spójny schemat.</span><span class="sxs-lookup"><span data-stu-id="8731e-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="8731e-115">Używana jest wspólna lokalizacja magazynu [kluczy ochrony danych](xref:security/data-protection/implementation/key-management) .</span><span class="sxs-lookup"><span data-stu-id="8731e-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="8731e-116">W ASP.NET Core aplikacje <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> służy do ustawiania lokalizacji magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="8731e-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="8731e-117">W .NET Framework aplikacje oprogramowanie pośredniczące uwierzytelniania plików cookie korzysta z implementacji programu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="8731e-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="8731e-118">`DataProtectionProvider`zapewnia usługi ochrony danych do szyfrowania i odszyfrowywania danych ładunku plików cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="8731e-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="8731e-119">`DataProtectionProvider`Wystąpienie jest odizolowane od systemu ochrony danych używanego przez inne części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8731e-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="8731e-120">[DataProtectionProvider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) przyjmuje wartość <xref:System.IO.DirectoryInfo> określającą lokalizację magazynu kluczy ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="8731e-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="8731e-121">`DataProtectionProvider`wymaga pakietu NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="8731e-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="8731e-122">W ASP.NET Core 2. x aplikacje odwołują się do [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8731e-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="8731e-123">W .NET Framework aplikacje Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="8731e-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="8731e-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Ustawia wspólną nazwę aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8731e-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a><span data-ttu-id="8731e-125">Udostępnianie plików cookie uwierzytelniania przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="8731e-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="8731e-126">W przypadku korzystania z ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="8731e-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="8731e-127">Klucze ochrony danych i nazwa aplikacji muszą być udostępniane między aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="8731e-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="8731e-128">W poniższych przykładach udostępniono wspólną lokalizację magazynu kluczy <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> .</span><span class="sxs-lookup"><span data-stu-id="8731e-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="8731e-129">Użyj, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Aby skonfigurować wspólną nazwę udostępnionej aplikacji ( `SharedCookieApp` w poniższych przykładach).</span><span class="sxs-lookup"><span data-stu-id="8731e-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="8731e-130">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="8731e-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="8731e-131">Użyj <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metody rozszerzenia, aby skonfigurować usługę ochrony danych dla plików cookie.</span><span class="sxs-lookup"><span data-stu-id="8731e-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="8731e-132">Domyślny typ uwierzytelniania to `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="8731e-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="8731e-133">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8731e-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a><span data-ttu-id="8731e-134">Udostępnianie plików cookie uwierzytelniania bez ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="8731e-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="8731e-135">W przypadku używania plików cookie bezpośrednio bez ASP.NET Core Identity Skonfiguruj ochronę danych i uwierzytelnianie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8731e-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8731e-136">W poniższym przykładzie typ uwierzytelniania jest ustawiony na `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="8731e-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a><span data-ttu-id="8731e-137">Udostępnianie plików cookie między różnymi ścieżkami podstawowymi</span><span class="sxs-lookup"><span data-stu-id="8731e-137">Share cookies across different base paths</span></span>

<span data-ttu-id="8731e-138">Plik cookie uwierzytelniania używa jako domyślnego [pliku cookie](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path) [. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) . Path.</span><span class="sxs-lookup"><span data-stu-id="8731e-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="8731e-139">Jeśli plik cookie aplikacji musi być współużytkowany przez różne ścieżki podstawowe, `Path` należy zastąpić:</span><span class="sxs-lookup"><span data-stu-id="8731e-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a><span data-ttu-id="8731e-140">Udostępnianie plików cookie w poddomenach</span><span class="sxs-lookup"><span data-stu-id="8731e-140">Share cookies across subdomains</span></span>

<span data-ttu-id="8731e-141">W przypadku hostowania aplikacji, które współużytkują pliki cookie w poddomenach, należy określić wspólną domenę we właściwości [plik cookie. domena](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) .</span><span class="sxs-lookup"><span data-stu-id="8731e-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="8731e-142">Aby udostępnić pliki cookie w aplikacjach `contoso.com` , takich jak `first_subdomain.contoso.com` i `second_subdomain.contoso.com` , określ `Cookie.Domain` jako `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="8731e-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="8731e-143">Szyfruj klucze ochrony danych w spoczynku</span><span class="sxs-lookup"><span data-stu-id="8731e-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="8731e-144">W przypadku wdrożeń produkcyjnych Skonfiguruj `DataProtectionProvider` do szyfrowania klucze przechowywane przy użyciu funkcji DPAPI lub x509.</span><span class="sxs-lookup"><span data-stu-id="8731e-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="8731e-145">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="8731e-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="8731e-146">W poniższym przykładzie podano odcisk palca certyfikatu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="8731e-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="8731e-147">Udostępnianie plików cookie uwierzytelniania między ASP.NET. x a aplikacjami ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8731e-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="8731e-148">ASP.NET 4. x aplikacje używające oprogramowania do uwierzytelniania plików cookie Katana mogą być skonfigurowane do generowania plików cookie uwierzytelniania, które są zgodne z ASP.NET Corem oprogramowania pośredniczącego uwierzytelniania plików cookie.</span><span class="sxs-lookup"><span data-stu-id="8731e-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="8731e-149">Dzięki temu można uaktualnić poszczególne aplikacje dużej witryny w kilku krokach, jednocześnie zapewniając bezproblemowe środowisko logowania jednokrotnego w całej lokacji.</span><span class="sxs-lookup"><span data-stu-id="8731e-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="8731e-150">Gdy aplikacja używa oprogramowania pośredniczącego Katana uwierzytelniania plików cookie, wywołuje `UseCookieAuthentication` w pliku *Startup.auth.cs* projektu.</span><span class="sxs-lookup"><span data-stu-id="8731e-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="8731e-151">Projekty aplikacji sieci Web ASP.NET 4. x utworzone przy użyciu Visual Studio 2013 i później domyślnie korzystają z oprogramowania pośredniczącego uwierzytelniania Katana cookie.</span><span class="sxs-lookup"><span data-stu-id="8731e-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="8731e-152">Chociaż `UseCookieAuthentication` jest przestarzała i nieobsługiwana w przypadku aplikacji ASP.NET Core, wywołanie `UseCookieAuthentication` w aplikacji ASP.NET 4. x, która używa oprogramowania pośredniczącego Katana uwierzytelniania plików cookie.</span><span class="sxs-lookup"><span data-stu-id="8731e-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="8731e-153">Aplikacja ASP.NET 4. x musi być docelowa .NET Framework 4.5.1 lub nowsza.</span><span class="sxs-lookup"><span data-stu-id="8731e-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="8731e-154">W przeciwnym razie instalacja niezbędnych pakietów NuGet nie powiodła się.</span><span class="sxs-lookup"><span data-stu-id="8731e-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="8731e-155">Aby udostępnić pliki cookie uwierzytelniania między aplikacją ASP.NET 4. x i aplikacją ASP.NET Core, skonfiguruj aplikację ASP.NET Core zgodnie z opisem w sekcji [udostępnianie plików cookie uwierzytelniania w aplikacjach ASP.NET Core Apps](#share-authentication-cookies-with-aspnet-core-identity) , a następnie skonfiguruj aplikację ASP.NET 4. x w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="8731e-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="8731e-156">Upewnij się, że pakiety aplikacji zostały zaktualizowane do najnowszej wersji.</span><span class="sxs-lookup"><span data-stu-id="8731e-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="8731e-157">Zainstaluj pakiet [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) w każdej aplikacji ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="8731e-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="8731e-158">Znajdź i zmodyfikuj wywołanie `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="8731e-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="8731e-159">Zmień nazwę pliku cookie, aby odpowiadała nazwie używanej przez oprogramowanie pośredniczące uwierzytelniania plików cookie ASP.NET Core ( `.AspNet.SharedCookie` w tym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="8731e-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="8731e-160">W poniższym przykładzie typ uwierzytelniania jest ustawiony na `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="8731e-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="8731e-161">Podaj wystąpienie `DataProtectionProvider` zainicjowane do lokalizacji magazynu kluczy Common Data Protection.</span><span class="sxs-lookup"><span data-stu-id="8731e-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="8731e-162">Upewnij się, że nazwa aplikacji jest ustawiona na wspólną nazwę aplikacji używaną przez wszystkie aplikacje, które współużytkują pliki cookie uwierzytelniania ( `SharedCookieApp` w tym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="8731e-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="8731e-163">Jeśli to nie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` jest ustawienie i `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , Ustaw jako <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> zastrzeżenie odróżniające unikatowych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8731e-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="8731e-164">*/Startup.auth.cs App_Start*:</span><span class="sxs-lookup"><span data-stu-id="8731e-164">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="8731e-165">Podczas generowania tożsamości użytkownika typ uwierzytelniania ( `Identity.Application` ) musi być zgodny z typem zdefiniowanym w elemencie `AuthenticationType` `UseCookieAuthentication` w *App_Start/Startup.auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="8731e-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="8731e-166">*Modele/IdentityModels. cs*:</span><span class="sxs-lookup"><span data-stu-id="8731e-166">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="8731e-167">Używanie wspólnej bazy danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="8731e-167">Use a common user database</span></span>

<span data-ttu-id="8731e-168">Gdy aplikacje korzystają z tego samego Identity schematu (w tej samej wersji programu Identity ), upewnij się, że Identity system dla każdej aplikacji jest wskazany w tej samej bazie danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8731e-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="8731e-169">W przeciwnym razie system tożsamości generuje błędy w czasie wykonywania, gdy próbuje dopasować informacje w pliku cookie uwierzytelniania względem informacji w swojej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8731e-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="8731e-170">Gdy Identity schemat różni się między aplikacjami, zwykle ponieważ aplikacje korzystają z różnych Identity wersji, udostępnianie wspólnej bazy danych opartej na najnowszej wersji programu Identity nie jest możliwe bez ponownego mapowania i dodawania kolumn w Identity schematach innych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8731e-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="8731e-171">Jest to często bardziej wydajne, aby uaktualnić inne aplikacje w celu korzystania z najnowszej wersji, aby Identity Wspólna baza danych mogła być współużytkowana przez aplikacje.</span><span class="sxs-lookup"><span data-stu-id="8731e-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8731e-172">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8731e-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>

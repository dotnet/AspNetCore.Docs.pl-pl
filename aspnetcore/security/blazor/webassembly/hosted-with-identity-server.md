---
title: Zabezpieczanie hostowanego Blazor przez ASP.NET Core aplikacji hostowanego webassembly za pomocą serwera tożsamości
author: guardrex
description: Aby utworzyć Blazor nową aplikację hostowane z uwierzytelnianiem z poziomu programu Visual Studio, która używa zaplecza [serwera tożsamości](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501285"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="f228b-103">Zabezpieczanie hostowanego Blazor przez ASP.NET Core aplikacji hostowanego webassembly za pomocą serwera tożsamości</span><span class="sxs-lookup"><span data-stu-id="f228b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="f228b-104">Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f228b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="f228b-105">Aby utworzyć Blazor nową aplikację hostowane w programie Visual Studio, która używa [usługi IdentityServer](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="f228b-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="f228b-106">Użyj programu Visual Studio, aby utworzyć nową \*\* Blazor aplikację WebAssembly.\*\*</span><span class="sxs-lookup"><span data-stu-id="f228b-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="f228b-107">Aby uzyskać więcej informacji, zobacz <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="f228b-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="f228b-108">W oknie **dialogowym Tworzenie Blazor nowej aplikacji** wybierz pozycję **Zmień** w sekcji **Uwierzytelnianie.**</span><span class="sxs-lookup"><span data-stu-id="f228b-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="f228b-109">Wybierz **pozycję Indywidualne konta użytkowników,** po których następuje przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="f228b-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="f228b-110">Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane.**</span><span class="sxs-lookup"><span data-stu-id="f228b-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="f228b-111">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f228b-111">Select the **Create** button.</span></span>

<span data-ttu-id="f228b-112">Aby utworzyć aplikację w powłoce poleceń, wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f228b-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="f228b-113">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="f228b-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f228b-114">Nazwa folderu również staje się częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="f228b-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="f228b-115">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="f228b-115">Server app configuration</span></span>

<span data-ttu-id="f228b-116">W poniższych sekcjach opisano dodatki do projektu, gdy obsługa uwierzytelniania jest uwzględniona.</span><span class="sxs-lookup"><span data-stu-id="f228b-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="f228b-117">Klasa początkowa</span><span class="sxs-lookup"><span data-stu-id="f228b-117">Startup class</span></span>

<span data-ttu-id="f228b-118">Klasa `Startup` ma następujące dodatki:</span><span class="sxs-lookup"><span data-stu-id="f228b-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="f228b-119">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f228b-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="f228b-120">Tożsamość z domyślnym interfejsem użytkownika:</span><span class="sxs-lookup"><span data-stu-id="f228b-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="f228b-121">IdentityServer z <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> dodatkową metodą pomocniczą, która konfiguruje niektóre domyślne konwencje ASP.NET Core na górze IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="f228b-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="f228b-122">Uwierzytelnianie za <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocą dodatkowej metody pomocniczej, która konfiguruje aplikację do sprawdzania poprawności tokenów JWT wyprodukowanych przez IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="f228b-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="f228b-123">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f228b-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="f228b-124">Oprogramowanie pośredniczące uwierzytelniania, które jest odpowiedzialne za sprawdzanie poprawności poświadczeń żądania i ustawienie użytkownika w kontekście żądania:</span><span class="sxs-lookup"><span data-stu-id="f228b-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="f228b-125">Oprogramowanie pośredniczące usługi IdentityServer, które udostępnia punkty końcowe Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="f228b-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="f228b-126">AddApiAuthorization (Dodawanie)</span><span class="sxs-lookup"><span data-stu-id="f228b-126">AddApiAuthorization</span></span>

<span data-ttu-id="f228b-127">Metoda <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnika konfiguruje [IdentityServer](https://identityserver.io/) dla scenariuszy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f228b-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="f228b-128">IdentityServer to zaawansowana i rozszerzalna struktura obsługi problemów związanych z zabezpieczeniami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f228b-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="f228b-129">IdentityServer udostępnia niepotrzebne złożoności dla najbardziej typowych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="f228b-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="f228b-130">W związku z tym zestaw konwencji i opcji konfiguracji jest pod warunkiem, że uważamy za dobry punkt wyjścia.</span><span class="sxs-lookup"><span data-stu-id="f228b-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="f228b-131">Po zmianie potrzeb uwierzytelniania, pełna moc IdentityServer jest nadal dostępna, aby dostosować uwierzytelnianie do wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f228b-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="f228b-132">Dodaj IdentyfikatoryServerJwt</span><span class="sxs-lookup"><span data-stu-id="f228b-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="f228b-133">Metoda <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnika konfiguruje schemat zasad dla aplikacji jako domyślny program obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f228b-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="f228b-134">Zasada jest skonfigurowana tak, aby umożliwić tożsamości do obsługi wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w przestrzeni `/Identity`adresu URL tożsamości .</span><span class="sxs-lookup"><span data-stu-id="f228b-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="f228b-135">Obsługuje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> wszystkie inne żądania.</span><span class="sxs-lookup"><span data-stu-id="f228b-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="f228b-136">Ponadto ta metoda:</span><span class="sxs-lookup"><span data-stu-id="f228b-136">Additionally, this method:</span></span>

* <span data-ttu-id="f228b-137">Rejestruje zasób interfejsu API w `{APPLICATION NAME}API` usłudze `{APPLICATION NAME}API`IdentityServer o domyślnym zakresie .</span><span class="sxs-lookup"><span data-stu-id="f228b-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="f228b-138">Konfiguruje oprogramowanie pośredniczące tokenu nośnika JWT w celu sprawdzania poprawności tokenów wystawionych przez IdentityServer dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f228b-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="f228b-139">WeatherForecastController (Kontroler weatherforecast)</span><span class="sxs-lookup"><span data-stu-id="f228b-139">WeatherForecastController</span></span>

<span data-ttu-id="f228b-140">W `WeatherForecastController` *(Controllers/WeatherForecastController.cs)* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy.</span><span class="sxs-lookup"><span data-stu-id="f228b-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="f228b-141">Atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnej zasady dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="f228b-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="f228b-142">Domyślna zasada autoryzacji jest skonfigurowana do używania domyślnego schematu uwierzytelniania, który jest skonfigurowany przez <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> schemat zasad, który został wymieniony wcześniej.</span><span class="sxs-lookup"><span data-stu-id="f228b-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="f228b-143">Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślny program obsługi żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f228b-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="f228b-144">ApplicationDbContext (ApplicationDbContext)</span><span class="sxs-lookup"><span data-stu-id="f228b-144">ApplicationDbContext</span></span>

<span data-ttu-id="f228b-145">W `ApplicationDbContext` *(Data/ApplicationDbContext.cs),* to <xref:Microsoft.EntityFrameworkCore.DbContext> samo jest używane w tożsamości <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> z wyjątkiem, że rozszerza się na schemat IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="f228b-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="f228b-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="f228b-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="f228b-147">Aby uzyskać pełną kontrolę nad schematem bazy danych, <xref:Microsoft.EntityFrameworkCore.DbContext> dziedzicz po jednej z dostępnych klas `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` tożsamości `OnModelCreating` i skonfiguruj kontekst, aby uwzględnić schemat tożsamości, wywołując w metodzie.</span><span class="sxs-lookup"><span data-stu-id="f228b-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="f228b-148">OidcConfigurationController (Kontroler konfiguracji oidc)</span><span class="sxs-lookup"><span data-stu-id="f228b-148">OidcConfigurationController</span></span>

<span data-ttu-id="f228b-149">W `OidcConfigurationController` *(Controllers/OidcConfigurationController.cs)* punkt końcowy klienta jest aprowizowana do obsługi parametrów OIDC.</span><span class="sxs-lookup"><span data-stu-id="f228b-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="f228b-150">Pliki ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="f228b-150">App settings files</span></span>

<span data-ttu-id="f228b-151">W pliku ustawień aplikacji (*appsettings.json*) `IdentityServer` w katalogu głównym projektu w sekcji opisano listę skonfigurowanych klientów.</span><span class="sxs-lookup"><span data-stu-id="f228b-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="f228b-152">W poniższym przykładzie istnieje jeden klient.</span><span class="sxs-lookup"><span data-stu-id="f228b-152">In the following example, there's a single client.</span></span> <span data-ttu-id="f228b-153">Nazwa klienta odpowiada nazwie aplikacji i jest mapowana według konwencji `ClientId` na parametr OAuth.</span><span class="sxs-lookup"><span data-stu-id="f228b-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="f228b-154">Profil wskazuje typ aplikacji, który jest konfigurowany.</span><span class="sxs-lookup"><span data-stu-id="f228b-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="f228b-155">Profil jest używany wewnętrznie do kierowania konwencjami, które upraszczają proces konfiguracji serwera.</span><span class="sxs-lookup"><span data-stu-id="f228b-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="f228b-156">W pliku ustawień aplikacji środowiska deweloperskiego (*appsettings. Development.json*) w katalogu `IdentityServer` głównym projektu w sekcji opisano klucz używany do podpisywania tokenów.</span><span class="sxs-lookup"><span data-stu-id="f228b-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="f228b-157">Konfiguracja aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="f228b-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f228b-158">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="f228b-158">Authentication package</span></span>

<span data-ttu-id="f228b-159">Po utworzeniu aplikacji do korzystania`Individual`z kont indywidualnych użytkowników ( ), `Microsoft.AspNetCore.Components.WebAssembly.Authentication` aplikacja automatycznie odbiera odwołanie do pakietu w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f228b-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="f228b-160">Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="f228b-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f228b-161">Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f228b-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="f228b-162">Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.</span><span class="sxs-lookup"><span data-stu-id="f228b-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="f228b-163">Obsługa autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="f228b-163">API authorization support</span></span>

<span data-ttu-id="f228b-164">Obsługa uwierzytelniania użytkowników jest podłączony do kontenera usługi przez `Microsoft.AspNetCore.Components.WebAssembly.Authentication` metodę rozszerzenia pod warunkiem, wewnątrz pakietu.</span><span class="sxs-lookup"><span data-stu-id="f228b-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="f228b-165">Ta metoda konfiguruje wszystkie usługi potrzebne do interakcji aplikacji z istniejącym systemem autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="f228b-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="f228b-166">Domyślnie ładuje konfigurację aplikacji według konwencji `_configuration/{client-id}`z .</span><span class="sxs-lookup"><span data-stu-id="f228b-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="f228b-167">Zgodnie z konwencją identyfikator klienta jest ustawiony na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f228b-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="f228b-168">Ten adres URL można zmienić, aby wskazać oddzielny punkt końcowy, wywołując przeciążenie z opcjami.</span><span class="sxs-lookup"><span data-stu-id="f228b-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="f228b-169">Import pliku</span><span class="sxs-lookup"><span data-stu-id="f228b-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="f228b-170">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="f228b-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="f228b-171">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="f228b-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="f228b-172">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="f228b-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="f228b-173">Składnik LogowanieWydajnik</span><span class="sxs-lookup"><span data-stu-id="f228b-173">LoginDisplay component</span></span>

<span data-ttu-id="f228b-174">Składnik `LoginDisplay` *(Shared/LoginDisplay.brzytwa)* jest `MainLayout` renderowany w składniku (*Shared/MainLayout.brzytwa*) i zarządza następującymi zachowaniami:</span><span class="sxs-lookup"><span data-stu-id="f228b-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="f228b-175">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="f228b-175">For authenticated users:</span></span>
  * <span data-ttu-id="f228b-176">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f228b-176">Displays the current user name.</span></span>
  * <span data-ttu-id="f228b-177">Oferuje łącze do strony profilu użytkownika w ASP.NET Tożsamość podstawowa.</span><span class="sxs-lookup"><span data-stu-id="f228b-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="f228b-178">Oferuje przycisk, aby wylogować się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f228b-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="f228b-179">Dla użytkowników anonimowych:</span><span class="sxs-lookup"><span data-stu-id="f228b-179">For anonymous users:</span></span>
  * <span data-ttu-id="f228b-180">Oferuje możliwość rejestracji.</span><span class="sxs-lookup"><span data-stu-id="f228b-180">Offers the option to register.</span></span>
  * <span data-ttu-id="f228b-181">Oferuje możliwość logowania.</span><span class="sxs-lookup"><span data-stu-id="f228b-181">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="f228b-182">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="f228b-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="f228b-183">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="f228b-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="f228b-184">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="f228b-184">Run the app</span></span>

<span data-ttu-id="f228b-185">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="f228b-185">Run the app from the Server project.</span></span> <span data-ttu-id="f228b-186">Korzystając z programu Visual Studio, wybierz projekt serwera w **Eksploratorze rozwiązań** i wybierz przycisk **Uruchom** na pasku narzędzi lub uruchom aplikację z menu **debugowania.**</span><span class="sxs-lookup"><span data-stu-id="f228b-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f228b-187">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f228b-187">Additional resources</span></span>

* [<span data-ttu-id="f228b-188">Żądanie dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="f228b-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)

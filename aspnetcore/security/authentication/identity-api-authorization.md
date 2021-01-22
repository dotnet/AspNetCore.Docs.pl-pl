---
title: Wprowadzenie do uwierzytelniania aplikacji jednostronicowych na ASP.NET Core
author: javiercn
description: Używanie Identity z aplikacją jednostronicową hostowaną w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658667"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="c6019-103">Uwierzytelnianie i autoryzacja dla aplikacji jednostronicowych</span><span class="sxs-lookup"><span data-stu-id="c6019-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="c6019-104">Szablony ASP.NET Core 3,1 i nowsze oferują uwierzytelnianie w aplikacjach jednostronicowych (aplikacji jednostronicowych) przy użyciu obsługi autoryzacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="c6019-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="c6019-105">ASP.NET Core Identityuwierzytelnianie i przechowywanie użytkowników są łączone z [ Identity serwerem](https://identityserver.io/) w celu zaimplementowania połączenia OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="c6019-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="c6019-106">Parametr uwierzytelniania został dodany do szablonów projektów **kątowych** i **reagowania** , które są podobne do parametrów uwierzytelniania w szablonach **aplikacji sieci Web (Model-View-Controller)** (MVC) i **aplikacji sieci Web** ( Razor strony).</span><span class="sxs-lookup"><span data-stu-id="c6019-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="c6019-107">Dozwolone wartości parametrów to **none** i **indywidualny**.</span><span class="sxs-lookup"><span data-stu-id="c6019-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="c6019-108">Szablon projektu **React.js i Redux** nie obsługuje w tym momencie parametru Authentication.</span><span class="sxs-lookup"><span data-stu-id="c6019-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="c6019-109">Tworzenie aplikacji z obsługą autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="c6019-109">Create an app with API authorization support</span></span>

<span data-ttu-id="c6019-110">Uwierzytelnianie i autoryzacja użytkowników mogą być używane z aplikacji jednostronicowychą kątową i reagują.</span><span class="sxs-lookup"><span data-stu-id="c6019-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="c6019-111">Otwórz powłokę poleceń i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c6019-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="c6019-112">**Kątowy**:</span><span class="sxs-lookup"><span data-stu-id="c6019-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="c6019-113">**Reagowanie**:</span><span class="sxs-lookup"><span data-stu-id="c6019-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="c6019-114">Poprzednie polecenie tworzy aplikację ASP.NET Core z katalogiem *ClientApp* zawierającym spa.</span><span class="sxs-lookup"><span data-stu-id="c6019-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="c6019-115">Ogólny opis składników ASP.NET Core aplikacji</span><span class="sxs-lookup"><span data-stu-id="c6019-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="c6019-116">W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="c6019-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="c6019-117">Klasa początkowa</span><span class="sxs-lookup"><span data-stu-id="c6019-117">Startup class</span></span>

<span data-ttu-id="c6019-118">Poniższe przykłady kodu bazują na [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Pakiet NuGet serwera.</span><span class="sxs-lookup"><span data-stu-id="c6019-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="c6019-119">Przykłady konfigurowania uwierzytelniania i autoryzacji interfejsu API przy użyciu <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> metod rozszerzenia i.</span><span class="sxs-lookup"><span data-stu-id="c6019-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="c6019-120">Projekty używające szablonów projektów reaguje lub SPA z uwierzytelnianiem zawierają odwołanie do tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="c6019-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="c6019-121">`Startup`Klasa zawiera następujące dodatki:</span><span class="sxs-lookup"><span data-stu-id="c6019-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="c6019-122">Wewnątrz `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="c6019-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="c6019-123">Identity przy użyciu domyślnego interfejsu użytkownika:</span><span class="sxs-lookup"><span data-stu-id="c6019-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="c6019-124">IdentitySerwer z dodatkową `AddApiAuthorization` metodą pomocniczą, która konfiguruje niektóre domyślne konwencje ASP.NET Core na Identity serwerze:</span><span class="sxs-lookup"><span data-stu-id="c6019-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="c6019-125">Uwierzytelnianie za pomocą dodatkowej `AddIdentityServerJwt` metody pomocnika, która konfiguruje aplikację do weryfikacji tokenów JWT utworzonych przez Identity serwer:</span><span class="sxs-lookup"><span data-stu-id="c6019-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="c6019-126">Wewnątrz `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="c6019-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="c6019-127">Oprogramowanie pośredniczące uwierzytelniania odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:</span><span class="sxs-lookup"><span data-stu-id="c6019-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="c6019-128">IdentityOprogramowanie pośredniczące serwera, które uwidacznia punkty końcowe połączenia OpenID Connect:</span><span class="sxs-lookup"><span data-stu-id="c6019-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="c6019-129">Azure App Service w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="c6019-129">Azure App Service on Linux</span></span>

<span data-ttu-id="c6019-130">W przypadku wdrożeń Azure App Service w systemie Linux Określ wystawcy jawnie w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c6019-130">For Azure App Service deployments on Linux, specify the issuer explicitly in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

<span data-ttu-id="c6019-131">W poprzednim kodzie `{AUTHORITY}` symbol zastępczy jest <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> używany podczas wykonywania wywołań OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="c6019-131">In the preceding code, the `{AUTHORITY}` placeholder is the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> to use when making OpenID Connect calls.</span></span>

<span data-ttu-id="c6019-132">Przykład:</span><span class="sxs-lookup"><span data-stu-id="c6019-132">Example:</span></span>

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a><span data-ttu-id="c6019-133">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="c6019-133">AddApiAuthorization</span></span>

<span data-ttu-id="c6019-134">Ta metoda pomocnika konfiguruje Identity serwer tak, aby korzystał z naszej obsługiwanej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c6019-134">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="c6019-135">IdentitySerwer to zaawansowana i rozszerzalna platforma do obsługi zagadnień związanych z zabezpieczeniami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-135">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="c6019-136">W tym samym czasie, które ujawnia niezbędną złożoność dla najbardziej typowych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="c6019-136">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="c6019-137">W związku z tym zestaw Konwencji i opcji konfiguracji jest dostarczany do użytkownika, który jest uważany za dobry punkt wyjścia.</span><span class="sxs-lookup"><span data-stu-id="c6019-137">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="c6019-138">Po zmianie uwierzytelniania należy zapewnić pełną moc Identity serwera, aby dostosować uwierzytelnianie do własnych potrzeb.</span><span class="sxs-lookup"><span data-stu-id="c6019-138">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="c6019-139">Dodaj Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="c6019-139">AddIdentityServerJwt</span></span>

<span data-ttu-id="c6019-140">Ta metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="c6019-140">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="c6019-141">Zasady są skonfigurowane tak, aby Identity obsługiwać wszystkie żądania kierowane do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL "/ Identity ".</span><span class="sxs-lookup"><span data-stu-id="c6019-141">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="c6019-142">`JwtBearerHandler`Obsługuje wszystkie inne żądania.</span><span class="sxs-lookup"><span data-stu-id="c6019-142">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="c6019-143">Ponadto ta metoda rejestruje `<<ApplicationName>>API` zasób interfejsu API z Identity serwerem z domyślnym zakresem `<<ApplicationName>>API` i konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikacji tokenów wystawionych przez Identity serwer dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-143">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="c6019-144">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="c6019-144">WeatherForecastController</span></span>

<span data-ttu-id="c6019-145">W pliku *Controllers\WeatherForecastController.cs* Zwróć uwagę na `[Authorize]` atrybut zastosowany do klasy, który wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="c6019-145">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="c6019-146">Domyślne zasady autoryzacji są skonfigurowane tak, aby korzystały z domyślnego schematu uwierzytelniania, który jest skonfigurowany przez `AddIdentityServerJwt` program do schematu zasad, który został wymieniony powyżej, przez `JwtBearerHandler` skonfigurowanie przez tę metodę pomocnika domyślnej procedury obsługi żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-146">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="c6019-147">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="c6019-147">ApplicationDbContext</span></span>

<span data-ttu-id="c6019-148">W pliku *Data\ApplicationDbContext.cs* Zauważ, `DbContext` że jest on używany w Identity połączeniu z wyjątkiem, który rozszerza `ApiAuthorizationDbContext` (Klasa pochodna z), `IdentityDbContext` Aby uwzględnić schemat dla Identity serwera.</span><span class="sxs-lookup"><span data-stu-id="c6019-148">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="c6019-149">Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity `DbContext` klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="c6019-149">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="c6019-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="c6019-150">OidcConfigurationController</span></span>

<span data-ttu-id="c6019-151">W pliku *Controllers\OidcConfigurationController.cs* Zwróć uwagę na punkt końcowy, który jest wstępnie zainicjowany do obsługi parametrów OIDC wymaganych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="c6019-151">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="c6019-152">W *appsettings.json* pliku katalogu głównego projektu znajduje się nowa `IdentityServer` sekcja opisująca listę skonfigurowanych klientów.</span><span class="sxs-lookup"><span data-stu-id="c6019-152">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="c6019-153">W poniższym przykładzie istnieje pojedynczy klient.</span><span class="sxs-lookup"><span data-stu-id="c6019-153">In the following example, there's a single client.</span></span> <span data-ttu-id="c6019-154">Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth.</span><span class="sxs-lookup"><span data-stu-id="c6019-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="c6019-155">Profil wskazuje konfigurowany typ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="c6019-156">Jest on używany wewnętrznie w przypadku Konwencji, które upraszczają proces konfiguracji serwera.</span><span class="sxs-lookup"><span data-stu-id="c6019-156">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="c6019-157">Istnieje kilka dostępnych profilów, zgodnie z opisem w sekcji [Profile aplikacji](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="c6019-157">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="c6019-158">appsettings.Development.jsna</span><span class="sxs-lookup"><span data-stu-id="c6019-158">appsettings.Development.json</span></span>

<span data-ttu-id="c6019-159">W *appsettings.Development.jsna* pliku katalogu głównego projektu znajduje się sekcja opisująca `IdentityServer` klucz używany do podpisywania tokenów.</span><span class="sxs-lookup"><span data-stu-id="c6019-159">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="c6019-160">Podczas wdrażania w środowisku produkcyjnym należy zainicjować i wdrożyć klucz wraz z aplikacją, jak wyjaśniono w sekcji [wdrażanie w środowisku produkcyjnym](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="c6019-160">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="c6019-161">Ogólny opis aplikacji kątowej</span><span class="sxs-lookup"><span data-stu-id="c6019-161">General description of the Angular app</span></span>

<span data-ttu-id="c6019-162">Obsługa uwierzytelniania i autoryzacji interfejsu API w szablonie kątowym znajduje się w jego własnym module skośnym w katalogu *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="c6019-162">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="c6019-163">Moduł składa się z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="c6019-163">The module is composed of the following elements:</span></span>

* <span data-ttu-id="c6019-164">3 składniki:</span><span class="sxs-lookup"><span data-stu-id="c6019-164">3 components:</span></span>
  * <span data-ttu-id="c6019-165">*login. Component. TS*: obsługuje przepływ logowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-165">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="c6019-166">*Wyloguj. składnik. TS*: obsługuje przepływ wylogowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-166">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="c6019-167">*login-menu. składnik. TS*: element widget wyświetlający jeden z następujących zestawów linków:</span><span class="sxs-lookup"><span data-stu-id="c6019-167">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="c6019-168">Zarządzanie profilami użytkowników i wylogowywanie łączy podczas uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c6019-168">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="c6019-169">Rejestrowanie i logowanie w przypadku braku uwierzytelnienia użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c6019-169">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="c6019-170">Ochrona trasy `AuthorizeGuard` , którą można dodać do tras i wymaga uwierzytelnienia użytkownika przed odwiedzeniem trasy.</span><span class="sxs-lookup"><span data-stu-id="c6019-170">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="c6019-171">Interceptor protokołu HTTP `AuthorizeInterceptor` , który dołącza token dostępu do wychodzących żądań HTTP przeznaczonych dla interfejsu API podczas uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c6019-171">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="c6019-172">Usługa `AuthorizeService` , która obsługuje szczegóły niższego poziomu procesu uwierzytelniania i ujawnia informacje o uwierzytelnionym użytkowniku w pozostałej części aplikacji do użycia.</span><span class="sxs-lookup"><span data-stu-id="c6019-172">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="c6019-173">Moduł kątowy, który definiuje trasy skojarzone z częściami uwierzytelniania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-173">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="c6019-174">Przedstawia on składnik menu logowania, Interceptor, ochronę i usługę do użycia w pozostałej części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-174">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="c6019-175">Ogólny opis aplikacji do reagowania</span><span class="sxs-lookup"><span data-stu-id="c6019-175">General description of the React app</span></span>

<span data-ttu-id="c6019-176">Obsługa uwierzytelniania i autoryzacji interfejsu API w szablonie reagowania znajduje się w katalogu *ClientApp\src\components\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="c6019-176">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="c6019-177">Składa się z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="c6019-177">It's composed of the following elements:</span></span>

* <span data-ttu-id="c6019-178">4 składniki:</span><span class="sxs-lookup"><span data-stu-id="c6019-178">4 components:</span></span>
  * <span data-ttu-id="c6019-179">*Login.js*: obsługuje przepływ logowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-179">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="c6019-180">*Logout.js*: obsługuje przepływ wylogowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-180">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="c6019-181">*LoginMenu.js*: element widget wyświetlający jeden z następujących zestawów linków:</span><span class="sxs-lookup"><span data-stu-id="c6019-181">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="c6019-182">Zarządzanie profilami użytkowników i wylogowywanie łączy podczas uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c6019-182">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="c6019-183">Rejestrowanie i logowanie w przypadku braku uwierzytelnienia użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c6019-183">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="c6019-184">*AuthorizeRoute.js*: składnik trasy, który wymaga uwierzytelnienia użytkownika przed renderowaniem składnika wskazanego w `Component` parametrze.</span><span class="sxs-lookup"><span data-stu-id="c6019-184">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="c6019-185">Wyeksportowane `authService` wystąpienie klasy `AuthorizeService` , które obsługuje szczegóły niższego poziomu procesu uwierzytelniania i ujawnia informacje o uwierzytelnionym użytkowniku w pozostałej części aplikacji do użycia.</span><span class="sxs-lookup"><span data-stu-id="c6019-185">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="c6019-186">Teraz, gdy widzisz główne składniki rozwiązania, możesz zapoznać się ze szczegółowymi scenariuszami dotyczącymi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-186">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="c6019-187">Wymagaj autoryzacji na nowym interfejsie API</span><span class="sxs-lookup"><span data-stu-id="c6019-187">Require authorization on a new API</span></span>

<span data-ttu-id="c6019-188">Domyślnie system jest skonfigurowany tak, aby z łatwością wymagał autoryzacji dla nowych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="c6019-188">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="c6019-189">W tym celu Utwórz nowy kontroler i Dodaj `[Authorize]` atrybut do klasy Controller lub do dowolnej akcji w ramach kontrolera.</span><span class="sxs-lookup"><span data-stu-id="c6019-189">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="c6019-190">Dostosowywanie procedury obsługi uwierzytelniania interfejsu API</span><span class="sxs-lookup"><span data-stu-id="c6019-190">Customize the API authentication handler</span></span>

<span data-ttu-id="c6019-191">Aby dostosować konfigurację procedury obsługi JWT interfejsu API, należy skonfigurować jej <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="c6019-191">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="c6019-192">Procedura obsługi JWT interfejsu API wywołuje zdarzenia, które umożliwiają kontrolę nad procesem uwierzytelniania przy użyciu `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="c6019-192">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="c6019-193">Aby zapewnić obsługę autoryzacji interfejsu API, `AddIdentityServerJwt` rejestruje własne programy obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="c6019-193">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="c6019-194">Aby dostosować obsługę zdarzenia, zawiń istniejący program obsługi zdarzeń z dodatkową logiką zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="c6019-194">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="c6019-195">Przykład:</span><span class="sxs-lookup"><span data-stu-id="c6019-195">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="c6019-196">W poprzednim kodzie `OnTokenValidated` procedura obsługi zdarzeń jest zastępowana implementacją niestandardową.</span><span class="sxs-lookup"><span data-stu-id="c6019-196">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="c6019-197">Ta implementacja:</span><span class="sxs-lookup"><span data-stu-id="c6019-197">This implementation:</span></span>

1. <span data-ttu-id="c6019-198">Wywołuje oryginalną implementację dostarczoną przez obsługę autoryzacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="c6019-198">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="c6019-199">Uruchom własną logikę niestandardową.</span><span class="sxs-lookup"><span data-stu-id="c6019-199">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="c6019-200">Ochrona trasy po stronie klienta (wartość kątowa)</span><span class="sxs-lookup"><span data-stu-id="c6019-200">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="c6019-201">Ochrona trasy po stronie klienta odbywa się przez dodanie funkcji Autoryzuj ochronę do listy osłon do uruchomienia podczas konfigurowania trasy.</span><span class="sxs-lookup"><span data-stu-id="c6019-201">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="c6019-202">Na przykład można zobaczyć, jak `fetch-data` trasa jest skonfigurowana w module kątowym aplikacji głównej:</span><span class="sxs-lookup"><span data-stu-id="c6019-202">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="c6019-203">Należy pamiętać, że ochrona trasy nie chroni faktycznego punktu końcowego (co nadal wymaga `[Authorize]` zastosowania atrybutu), ale uniemożliwia użytkownikowi przechodzenie do podanej trasy po stronie klienta, gdy nie jest ona uwierzytelniana.</span><span class="sxs-lookup"><span data-stu-id="c6019-203">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="c6019-204">Uwierzytelnianie żądań interfejsu API (kątowy)</span><span class="sxs-lookup"><span data-stu-id="c6019-204">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="c6019-205">Żądania uwierzytelniające do interfejsów API hostowanych razem z aplikacją są wykonywane automatycznie przy użyciu interceptora klienta HTTP zdefiniowanego przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="c6019-205">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="c6019-206">Ochrona trasy po stronie klienta (reagowanie)</span><span class="sxs-lookup"><span data-stu-id="c6019-206">Protect a client-side route (React)</span></span>

<span data-ttu-id="c6019-207">Ochrona trasy po stronie klienta przy użyciu `AuthorizeRoute` składnika zamiast zwykłego `Route` składnika.</span><span class="sxs-lookup"><span data-stu-id="c6019-207">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="c6019-208">Na przykład Zwróć uwagę na to, jak `fetch-data` trasa jest skonfigurowana w `App` składniku:</span><span class="sxs-lookup"><span data-stu-id="c6019-208">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="c6019-209">Ochrona trasy:</span><span class="sxs-lookup"><span data-stu-id="c6019-209">Protecting a route:</span></span>

* <span data-ttu-id="c6019-210">Nie chroni rzeczywistego punktu końcowego (co nadal wymaga `[Authorize]` zastosowania atrybutu).</span><span class="sxs-lookup"><span data-stu-id="c6019-210">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="c6019-211">Uniemożliwia użytkownikowi przechodzenie do danej trasy po stronie klienta, gdy nie jest ona uwierzytelniana.</span><span class="sxs-lookup"><span data-stu-id="c6019-211">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="c6019-212">Uwierzytelnianie żądań interfejsu API (reagowanie)</span><span class="sxs-lookup"><span data-stu-id="c6019-212">Authenticate API requests (React)</span></span>

<span data-ttu-id="c6019-213">Żądania uwierzytelniania przy użyciu reakcji są wykonywane najpierw przez zaimportowanie `authService` wystąpienia z programu `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="c6019-213">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="c6019-214">Token dostępu jest pobierany z `authService` i jest dołączany do żądania, jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="c6019-214">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="c6019-215">W przypadku składników reagujących ta czynność jest zwykle wykonywana w `componentDidMount` metodzie cyklu życia lub w wyniku działania niektórych działań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c6019-215">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="c6019-216">Zaimportuj authService do składnika</span><span class="sxs-lookup"><span data-stu-id="c6019-216">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="c6019-217">Pobieranie i dołączanie tokenu dostępu do odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="c6019-217">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="c6019-218">Wdrażanie w środowisku produkcyjnym</span><span class="sxs-lookup"><span data-stu-id="c6019-218">Deploy to production</span></span>

<span data-ttu-id="c6019-219">Aby wdrożyć aplikację w środowisku produkcyjnym, należy zainicjować następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="c6019-219">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="c6019-220">Baza danych przechowująca Identity konta użytkowników i Identity dotacje serwera.</span><span class="sxs-lookup"><span data-stu-id="c6019-220">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="c6019-221">Certyfikat produkcyjny do użycia na potrzeby podpisywania tokenów.</span><span class="sxs-lookup"><span data-stu-id="c6019-221">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="c6019-222">Nie ma określonych wymagań dotyczących tego certyfikatu; może to być certyfikat z podpisem własnym lub certyfikat obsługiwany przez urząd certyfikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-222">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="c6019-223">Można go wygenerować za poorednictwem standardowych narzędzi, takich jak PowerShell lub OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="c6019-223">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="c6019-224">Można go zainstalować w magazynie certyfikatów na maszynach docelowych lub wdrożyć jako plik *PFX* przy użyciu silnego hasła.</span><span class="sxs-lookup"><span data-stu-id="c6019-224">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="c6019-225">Przykład: wdrażanie do dostawcy hostingu w sieci Web spoza platformy Azure</span><span class="sxs-lookup"><span data-stu-id="c6019-225">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="c6019-226">W panelu hostingu w sieci Web Utwórz lub Załaduj certyfikat.</span><span class="sxs-lookup"><span data-stu-id="c6019-226">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="c6019-227">Następnie w *appsettings.json* pliku aplikacji zmodyfikuj `IdentityServer` sekcję, aby zawierała szczegóły klucza.</span><span class="sxs-lookup"><span data-stu-id="c6019-227">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="c6019-228">Przykład:</span><span class="sxs-lookup"><span data-stu-id="c6019-228">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="c6019-229">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="c6019-229">In the preceding example:</span></span>

* <span data-ttu-id="c6019-230">`StoreName` reprezentuje nazwę magazynu certyfikatów, w którym przechowywany jest certyfikat.</span><span class="sxs-lookup"><span data-stu-id="c6019-230">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="c6019-231">W tym przypadku wskazuje magazyn hostingu w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c6019-231">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="c6019-232">`StoreLocation` reprezentuje miejsce załadowania certyfikatu ( `CurrentUser` w tym przypadku).</span><span class="sxs-lookup"><span data-stu-id="c6019-232">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="c6019-233">`Name` odpowiada podmiotowi wyróżnionemu dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="c6019-233">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="c6019-234">Przykład: Wdróż do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c6019-234">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="c6019-235">W tej sekcji opisano wdrażanie aplikacji do Azure App Service przy użyciu certyfikatu przechowywanego w magazynie certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="c6019-235">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="c6019-236">Aby zmodyfikować aplikację w celu załadowania certyfikatu z magazynu certyfikatów, podczas konfigurowania aplikacji w Azure Portal w późniejszym kroku wymagany jest plan usługi warstwy Standardowa lub lepszy.</span><span class="sxs-lookup"><span data-stu-id="c6019-236">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="c6019-237">W *appsettings.json* pliku aplikacji zmodyfikuj `IdentityServer` sekcję, aby zawierała szczegóły klucza:</span><span class="sxs-lookup"><span data-stu-id="c6019-237">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="c6019-238">Nazwa magazynu reprezentuje nazwę magazynu certyfikatów, w którym przechowywany jest certyfikat.</span><span class="sxs-lookup"><span data-stu-id="c6019-238">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="c6019-239">W tym przypadku wskazuje osobisty magazyn użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c6019-239">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="c6019-240">Lokalizacja magazynu reprezentuje miejsce załadowania certyfikatu ( `CurrentUser` lub `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="c6019-240">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="c6019-241">Właściwość Name certyfikatu odpowiada podmiotowi wyróżnionemu dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="c6019-241">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="c6019-242">Aby wdrożyć program do Azure App Service, wykonaj kroki opisane w artykule [wdrażanie aplikacji na platformie Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), w którym wyjaśniono, jak utworzyć niezbędne zasoby platformy Azure i wdrożyć aplikację w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="c6019-242">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="c6019-243">Po wykonaniu powyższych instrukcji aplikacja zostanie wdrożona na platformie Azure, ale nie jest jeszcze funkcjonalna.</span><span class="sxs-lookup"><span data-stu-id="c6019-243">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="c6019-244">Certyfikat używany w aplikacji musi być skonfigurowany w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c6019-244">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="c6019-245">Znajdź odcisk palca certyfikatu i postępuj zgodnie z instrukcjami opisanymi w artykule [ładowanie certyfikatów](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="c6019-245">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="c6019-246">Chociaż te kroki zawierają informacje o protokole SSL, w Azure Portal znajduje się sekcja **Certyfikaty prywatne** , w której można przekazać certyfikat z obsługą administracyjną do użycia z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="c6019-246">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="c6019-247">Po skonfigurowaniu aplikacji i ustawieniach aplikacji w Azure Portal ponownie uruchom aplikację w portalu.</span><span class="sxs-lookup"><span data-stu-id="c6019-247">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="c6019-248">Inne opcje konfiguracji</span><span class="sxs-lookup"><span data-stu-id="c6019-248">Other configuration options</span></span>

<span data-ttu-id="c6019-249">Obsługa autoryzacji interfejsu API jest oparta na Identity serwerze z zestawem Konwencji, wartościami domyślnymi i ulepszeniami, aby uprościć środowisko aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="c6019-249">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="c6019-250">Niezbędna jest pełna moc Identity serwera w tle, jeśli integracja ASP.NET Core nie obejmuje Twojego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="c6019-250">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="c6019-251">Pomoc techniczna ASP.NET Core jest skoncentrowana na aplikacjach "pierwszej strony", w których wszystkie aplikacje są tworzone i wdrażane przez naszą organizację.</span><span class="sxs-lookup"><span data-stu-id="c6019-251">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="c6019-252">W związku z tym pomoc techniczna nie jest oferowana dla elementów, takich jak zgody lub Federacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-252">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="c6019-253">W tych scenariuszach Użyj Identity serwera i postępuj zgodnie z dokumentacją.</span><span class="sxs-lookup"><span data-stu-id="c6019-253">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="c6019-254">Profile aplikacji</span><span class="sxs-lookup"><span data-stu-id="c6019-254">Application profiles</span></span>

<span data-ttu-id="c6019-255">Profile aplikacji są wstępnie zdefiniowanymi konfiguracjami dla aplikacji, które definiują ich parametry.</span><span class="sxs-lookup"><span data-stu-id="c6019-255">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="c6019-256">W tej chwili obsługiwane są następujące profile:</span><span class="sxs-lookup"><span data-stu-id="c6019-256">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="c6019-257">`IdentityServerSPA`: Reprezentuje funkcję SPA hostowaną razem z Identity serwerem jako pojedynczą jednostką.</span><span class="sxs-lookup"><span data-stu-id="c6019-257">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="c6019-258">`redirect_uri`Wartość domyślna to `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="c6019-258">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="c6019-259">`post_logout_redirect_uri`Wartość domyślna to `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="c6019-259">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="c6019-260">Zestaw zakresów obejmuje `openid` , `profile` i każdy zakres zdefiniowany dla interfejsów API w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="c6019-261">Zestaw dozwolonych typów odpowiedzi OIDC jest `id_token token` lub każdy z nich indywidualnie ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="c6019-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="c6019-262">Dozwolony tryb odpowiedzi to `fragment` .</span><span class="sxs-lookup"><span data-stu-id="c6019-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="c6019-263">`SPA`: Reprezentuje SPA, który nie jest obsługiwany z Identity serwerem.</span><span class="sxs-lookup"><span data-stu-id="c6019-263">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="c6019-264">Zestaw zakresów obejmuje `openid` , `profile` i każdy zakres zdefiniowany dla interfejsów API w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-264">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="c6019-265">Zestaw dozwolonych typów odpowiedzi OIDC jest `id_token token` lub każdy z nich indywidualnie ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="c6019-265">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="c6019-266">Dozwolony tryb odpowiedzi to `fragment` .</span><span class="sxs-lookup"><span data-stu-id="c6019-266">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="c6019-267">`IdentityServerJwt`: Reprezentuje interfejs API, który jest hostowany razem z Identity serwerem.</span><span class="sxs-lookup"><span data-stu-id="c6019-267">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="c6019-268">Aplikacja jest skonfigurowana tak, aby zawierała pojedynczy zakres, który jest wartością domyślną dla nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-268">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="c6019-269">`API`: Reprezentuje interfejs API, który nie jest obsługiwany z Identity serwerem.</span><span class="sxs-lookup"><span data-stu-id="c6019-269">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="c6019-270">Aplikacja jest skonfigurowana tak, aby zawierała pojedynczy zakres, który jest wartością domyślną dla nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c6019-270">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="c6019-271">Konfiguracja za poorednictwem AppSettings</span><span class="sxs-lookup"><span data-stu-id="c6019-271">Configuration through AppSettings</span></span>

<span data-ttu-id="c6019-272">Skonfiguruj aplikacje za pomocą systemu konfiguracji, dodając je do listy `Clients` lub `Resources` .</span><span class="sxs-lookup"><span data-stu-id="c6019-272">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="c6019-273">Skonfiguruj poszczególne klienta `redirect_uri` i `post_logout_redirect_uri` właściwości, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="c6019-273">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="c6019-274">Podczas konfigurowania zasobów można skonfigurować zakresy dla zasobu, jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="c6019-274">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="c6019-275">Konfiguracja przy użyciu kodu</span><span class="sxs-lookup"><span data-stu-id="c6019-275">Configuration through code</span></span>

<span data-ttu-id="c6019-276">Klientów i zasoby można również skonfigurować za pomocą kodu przy użyciu przeciążenia `AddApiAuthorization` , które wykonuje akcję konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="c6019-276">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="c6019-277">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c6019-277">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>

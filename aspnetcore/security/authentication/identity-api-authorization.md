---
title: Wprowadzenie do uwierzytelniania aplikacji jednostronicowych na ASP.NET Core
author: javiercn
description: Używanie Identity z aplikacją jednostronicową hostowaną w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: c06f1d4bf772d7726d19163fcdee8c92d4006cd2
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819116"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="edcec-103">Uwierzytelnianie i autoryzacja dla aplikacji jednostronicowych</span><span class="sxs-lookup"><span data-stu-id="edcec-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="edcec-104">ASP.NET Core 3,0 lub nowszy oferuje uwierzytelnianie w aplikacjach jednostronicowych (aplikacji jednostronicowych) przy użyciu obsługi autoryzacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="edcec-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="edcec-105">ASP.NET Core Identity uwierzytelniania i przechowywania użytkowników są łączone z [ Identity serwerem](https://identityserver.io/) w celu zaimplementowania połączenia OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="edcec-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="edcec-106">Parametr uwierzytelniania został dodany do szablonów projektów **kątowych** i **reagowania** , które są podobne do parametrów uwierzytelniania w szablonach **aplikacji sieci Web (Model-View-Controller)** (MVC) i **aplikacji sieci Web** ( Razor strony).</span><span class="sxs-lookup"><span data-stu-id="edcec-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="edcec-107">Dozwolone wartości parametrów to **none** i **indywidualny**.</span><span class="sxs-lookup"><span data-stu-id="edcec-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="edcec-108">Szablon projektu **React.js i Redux** nie obsługuje w tym momencie parametru Authentication.</span><span class="sxs-lookup"><span data-stu-id="edcec-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="edcec-109">Tworzenie aplikacji z obsługą autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="edcec-109">Create an app with API authorization support</span></span>

<span data-ttu-id="edcec-110">Uwierzytelnianie i autoryzacja użytkowników mogą być używane z aplikacji jednostronicowychą kątową i reagują.</span><span class="sxs-lookup"><span data-stu-id="edcec-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="edcec-111">Otwórz powłokę poleceń i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="edcec-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="edcec-112">**Kątowy**:</span><span class="sxs-lookup"><span data-stu-id="edcec-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="edcec-113">**Reagowanie**:</span><span class="sxs-lookup"><span data-stu-id="edcec-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="edcec-114">Poprzednie polecenie tworzy aplikację ASP.NET Core z katalogiem *ClientApp* zawierającym spa.</span><span class="sxs-lookup"><span data-stu-id="edcec-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="edcec-115">Ogólny opis składników ASP.NET Core aplikacji</span><span class="sxs-lookup"><span data-stu-id="edcec-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="edcec-116">W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="edcec-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="edcec-117">Klasa początkowa</span><span class="sxs-lookup"><span data-stu-id="edcec-117">Startup class</span></span>

<span data-ttu-id="edcec-118">Poniższe przykłady kodu bazują na [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer)Pakiet NuGet serwera.</span><span class="sxs-lookup"><span data-stu-id="edcec-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="edcec-119">Przykłady konfigurowania uwierzytelniania i autoryzacji interfejsu API przy użyciu <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> metod rozszerzenia i.</span><span class="sxs-lookup"><span data-stu-id="edcec-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="edcec-120">Projekty używające szablonów projektów reaguje lub SPA z uwierzytelnianiem zawierają odwołanie do tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="edcec-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="edcec-121">`Startup`Klasa zawiera następujące dodatki:</span><span class="sxs-lookup"><span data-stu-id="edcec-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="edcec-122">Wewnątrz `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="edcec-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="edcec-123">Identityprzy użyciu domyślnego interfejsu użytkownika:</span><span class="sxs-lookup"><span data-stu-id="edcec-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="edcec-124">IdentitySerwer z dodatkową `AddApiAuthorization` metodą pomocniczą, która konfiguruje niektóre domyślne konwencje ASP.NET Core na Identity serwerze:</span><span class="sxs-lookup"><span data-stu-id="edcec-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="edcec-125">Uwierzytelnianie za pomocą dodatkowej `AddIdentityServerJwt` metody pomocnika, która konfiguruje aplikację do weryfikacji tokenów JWT utworzonych przez Identity serwer:</span><span class="sxs-lookup"><span data-stu-id="edcec-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="edcec-126">Wewnątrz `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="edcec-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="edcec-127">Oprogramowanie pośredniczące uwierzytelniania odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:</span><span class="sxs-lookup"><span data-stu-id="edcec-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="edcec-128">IdentityOprogramowanie pośredniczące serwera, które uwidacznia punkty końcowe połączenia OpenID Connect:</span><span class="sxs-lookup"><span data-stu-id="edcec-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="edcec-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="edcec-129">AddApiAuthorization</span></span>

<span data-ttu-id="edcec-130">Ta metoda pomocnika konfiguruje Identity serwer tak, aby korzystał z naszej obsługiwanej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="edcec-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="edcec-131">IdentitySerwer to zaawansowana i rozszerzalna platforma do obsługi zagadnień związanych z zabezpieczeniami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="edcec-132">W tym samym czasie, które ujawnia niezbędną złożoność dla najbardziej typowych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="edcec-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="edcec-133">W związku z tym zestaw Konwencji i opcji konfiguracji jest dostarczany do użytkownika, który jest uważany za dobry punkt wyjścia.</span><span class="sxs-lookup"><span data-stu-id="edcec-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="edcec-134">Po zmianie uwierzytelniania należy zapewnić pełną moc Identity serwera, aby dostosować uwierzytelnianie do własnych potrzeb.</span><span class="sxs-lookup"><span data-stu-id="edcec-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="edcec-135">Dodaj Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="edcec-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="edcec-136">Ta metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="edcec-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="edcec-137">Zasady są skonfigurowane tak, aby Identity obsługiwać wszystkie żądania kierowane do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL "/ Identity ".</span><span class="sxs-lookup"><span data-stu-id="edcec-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="edcec-138">`JwtBearerHandler`Obsługuje wszystkie inne żądania.</span><span class="sxs-lookup"><span data-stu-id="edcec-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="edcec-139">Ponadto ta metoda rejestruje `<<ApplicationName>>API` zasób interfejsu API z Identity serwerem z domyślnym zakresem `<<ApplicationName>>API` i konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikacji tokenów wystawionych przez Identity serwer dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="edcec-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="edcec-140">WeatherForecastController</span></span>

<span data-ttu-id="edcec-141">W pliku *Controllers\WeatherForecastController.cs* Zwróć uwagę na `[Authorize]` atrybut zastosowany do klasy, który wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="edcec-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="edcec-142">Domyślne zasady autoryzacji są skonfigurowane tak, aby korzystały z domyślnego schematu uwierzytelniania, który jest skonfigurowany przez `AddIdentityServerJwt` program do schematu zasad, który został wymieniony powyżej, przez `JwtBearerHandler` skonfigurowanie przez tę metodę pomocnika domyślnej procedury obsługi żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="edcec-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="edcec-143">ApplicationDbContext</span></span>

<span data-ttu-id="edcec-144">W pliku *Data\ApplicationDbContext.cs* Zauważ, `DbContext` że jest on używany w Identity połączeniu z wyjątkiem, który rozszerza `ApiAuthorizationDbContext` (Klasa pochodna z), `IdentityDbContext` Aby uwzględnić schemat dla Identity serwera.</span><span class="sxs-lookup"><span data-stu-id="edcec-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="edcec-145">Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity `DbContext` klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="edcec-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="edcec-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="edcec-146">OidcConfigurationController</span></span>

<span data-ttu-id="edcec-147">W pliku *Controllers\OidcConfigurationController.cs* Zwróć uwagę na punkt końcowy, który jest wstępnie zainicjowany do obsługi parametrów OIDC wymaganych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="edcec-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="edcec-148">appsettings.jsna</span><span class="sxs-lookup"><span data-stu-id="edcec-148">appsettings.json</span></span>

<span data-ttu-id="edcec-149">W *appsettings.js* w pliku katalogu głównego projektu znajduje się nowa `IdentityServer` sekcja opisująca listę skonfigurowanych klientów.</span><span class="sxs-lookup"><span data-stu-id="edcec-149">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="edcec-150">W poniższym przykładzie istnieje pojedynczy klient.</span><span class="sxs-lookup"><span data-stu-id="edcec-150">In the following example, there's a single client.</span></span> <span data-ttu-id="edcec-151">Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth.</span><span class="sxs-lookup"><span data-stu-id="edcec-151">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="edcec-152">Profil wskazuje konfigurowany typ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-152">The profile indicates the app type being configured.</span></span> <span data-ttu-id="edcec-153">Jest on używany wewnętrznie w przypadku Konwencji, które upraszczają proces konfiguracji serwera.</span><span class="sxs-lookup"><span data-stu-id="edcec-153">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="edcec-154">Istnieje kilka dostępnych profilów, zgodnie z opisem w sekcji [Profile aplikacji](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="edcec-154">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="edcec-155">appsettings.Development.jsna</span><span class="sxs-lookup"><span data-stu-id="edcec-155">appsettings.Development.json</span></span>

<span data-ttu-id="edcec-156">W *appsettings.Development.jsna* pliku katalogu głównego projektu znajduje się sekcja opisująca `IdentityServer` klucz używany do podpisywania tokenów.</span><span class="sxs-lookup"><span data-stu-id="edcec-156">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="edcec-157">Podczas wdrażania w środowisku produkcyjnym należy zainicjować i wdrożyć klucz wraz z aplikacją, jak wyjaśniono w sekcji [wdrażanie w środowisku produkcyjnym](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="edcec-157">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="edcec-158">Ogólny opis aplikacji kątowej</span><span class="sxs-lookup"><span data-stu-id="edcec-158">General description of the Angular app</span></span>

<span data-ttu-id="edcec-159">Obsługa uwierzytelniania i autoryzacji interfejsu API w szablonie kątowym znajduje się w jego własnym module skośnym w katalogu *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="edcec-159">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="edcec-160">Moduł składa się z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="edcec-160">The module is composed of the following elements:</span></span>

* <span data-ttu-id="edcec-161">3 składniki:</span><span class="sxs-lookup"><span data-stu-id="edcec-161">3 components:</span></span>
  * <span data-ttu-id="edcec-162">*login. Component. TS*: obsługuje przepływ logowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-162">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="edcec-163">*Wyloguj. składnik. TS*: obsługuje przepływ wylogowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-163">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="edcec-164">*login-menu. składnik. TS*: element widget wyświetlający jeden z następujących zestawów linków:</span><span class="sxs-lookup"><span data-stu-id="edcec-164">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="edcec-165">Zarządzanie profilami użytkowników i wylogowywanie łączy podczas uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="edcec-165">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="edcec-166">Rejestrowanie i logowanie w przypadku braku uwierzytelnienia użytkownika.</span><span class="sxs-lookup"><span data-stu-id="edcec-166">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="edcec-167">Ochrona trasy `AuthorizeGuard` , którą można dodać do tras i wymaga uwierzytelnienia użytkownika przed odwiedzeniem trasy.</span><span class="sxs-lookup"><span data-stu-id="edcec-167">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="edcec-168">Interceptor protokołu HTTP `AuthorizeInterceptor` , który dołącza token dostępu do wychodzących żądań HTTP przeznaczonych dla interfejsu API podczas uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="edcec-168">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="edcec-169">Usługa `AuthorizeService` , która obsługuje szczegóły niższego poziomu procesu uwierzytelniania i ujawnia informacje o uwierzytelnionym użytkowniku w pozostałej części aplikacji do użycia.</span><span class="sxs-lookup"><span data-stu-id="edcec-169">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="edcec-170">Moduł kątowy, który definiuje trasy skojarzone z częściami uwierzytelniania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-170">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="edcec-171">Przedstawia on składnik menu logowania, Interceptor, ochronę i usługę do użycia w pozostałej części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-171">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="edcec-172">Ogólny opis aplikacji do reagowania</span><span class="sxs-lookup"><span data-stu-id="edcec-172">General description of the React app</span></span>

<span data-ttu-id="edcec-173">Obsługa uwierzytelniania i autoryzacji interfejsu API w szablonie reagowania znajduje się w katalogu *ClientApp\src\components\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="edcec-173">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="edcec-174">Składa się z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="edcec-174">It's composed of the following elements:</span></span>

* <span data-ttu-id="edcec-175">4 składniki:</span><span class="sxs-lookup"><span data-stu-id="edcec-175">4 components:</span></span>
  * <span data-ttu-id="edcec-176">*Login.js*: obsługuje przepływ logowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-176">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="edcec-177">*Logout.js*: obsługuje przepływ wylogowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-177">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="edcec-178">*LoginMenu.js*: element widget wyświetlający jeden z następujących zestawów linków:</span><span class="sxs-lookup"><span data-stu-id="edcec-178">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="edcec-179">Zarządzanie profilami użytkowników i wylogowywanie łączy podczas uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="edcec-179">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="edcec-180">Rejestrowanie i logowanie w przypadku braku uwierzytelnienia użytkownika.</span><span class="sxs-lookup"><span data-stu-id="edcec-180">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="edcec-181">*AuthorizeRoute.js*: składnik trasy, który wymaga uwierzytelnienia użytkownika przed renderowaniem składnika wskazanego w `Component` parametrze.</span><span class="sxs-lookup"><span data-stu-id="edcec-181">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="edcec-182">Wyeksportowane `authService` wystąpienie klasy `AuthorizeService` , które obsługuje szczegóły niższego poziomu procesu uwierzytelniania i ujawnia informacje o uwierzytelnionym użytkowniku w pozostałej części aplikacji do użycia.</span><span class="sxs-lookup"><span data-stu-id="edcec-182">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="edcec-183">Teraz, gdy widzisz główne składniki rozwiązania, możesz zapoznać się ze szczegółowymi scenariuszami dotyczącymi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-183">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="edcec-184">Wymagaj autoryzacji na nowym interfejsie API</span><span class="sxs-lookup"><span data-stu-id="edcec-184">Require authorization on a new API</span></span>

<span data-ttu-id="edcec-185">Domyślnie system jest skonfigurowany tak, aby z łatwością wymagał autoryzacji dla nowych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="edcec-185">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="edcec-186">W tym celu Utwórz nowy kontroler i Dodaj `[Authorize]` atrybut do klasy Controller lub do dowolnej akcji w ramach kontrolera.</span><span class="sxs-lookup"><span data-stu-id="edcec-186">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="edcec-187">Dostosowywanie procedury obsługi uwierzytelniania interfejsu API</span><span class="sxs-lookup"><span data-stu-id="edcec-187">Customize the API authentication handler</span></span>

<span data-ttu-id="edcec-188">Aby dostosować konfigurację procedury obsługi JWT interfejsu API, należy skonfigurować jej <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="edcec-188">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

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

<span data-ttu-id="edcec-189">Procedura obsługi JWT interfejsu API wywołuje zdarzenia, które umożliwiają kontrolę nad procesem uwierzytelniania przy użyciu `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="edcec-189">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="edcec-190">Aby zapewnić obsługę autoryzacji interfejsu API, `AddIdentityServerJwt` rejestruje własne programy obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="edcec-190">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="edcec-191">Aby dostosować obsługę zdarzenia, zawiń istniejący program obsługi zdarzeń z dodatkową logiką zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="edcec-191">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="edcec-192">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="edcec-192">For example:</span></span>

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

<span data-ttu-id="edcec-193">W poprzednim kodzie `OnTokenValidated` procedura obsługi zdarzeń jest zastępowana implementacją niestandardową.</span><span class="sxs-lookup"><span data-stu-id="edcec-193">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="edcec-194">Ta implementacja:</span><span class="sxs-lookup"><span data-stu-id="edcec-194">This implementation:</span></span>

1. <span data-ttu-id="edcec-195">Wywołuje oryginalną implementację dostarczoną przez obsługę autoryzacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="edcec-195">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="edcec-196">Uruchom własną logikę niestandardową.</span><span class="sxs-lookup"><span data-stu-id="edcec-196">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="edcec-197">Ochrona trasy po stronie klienta (wartość kątowa)</span><span class="sxs-lookup"><span data-stu-id="edcec-197">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="edcec-198">Ochrona trasy po stronie klienta odbywa się przez dodanie funkcji Autoryzuj ochronę do listy osłon do uruchomienia podczas konfigurowania trasy.</span><span class="sxs-lookup"><span data-stu-id="edcec-198">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="edcec-199">Na przykład można zobaczyć, jak `fetch-data` trasa jest skonfigurowana w module kątowym aplikacji głównej:</span><span class="sxs-lookup"><span data-stu-id="edcec-199">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="edcec-200">Należy pamiętać, że ochrona trasy nie chroni faktycznego punktu końcowego (co nadal wymaga `[Authorize]` zastosowania atrybutu), ale uniemożliwia użytkownikowi przechodzenie do podanej trasy po stronie klienta, gdy nie jest ona uwierzytelniana.</span><span class="sxs-lookup"><span data-stu-id="edcec-200">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="edcec-201">Uwierzytelnianie żądań interfejsu API (kątowy)</span><span class="sxs-lookup"><span data-stu-id="edcec-201">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="edcec-202">Żądania uwierzytelniające do interfejsów API hostowanych razem z aplikacją są wykonywane automatycznie przy użyciu interceptora klienta HTTP zdefiniowanego przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="edcec-202">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="edcec-203">Ochrona trasy po stronie klienta (reagowanie)</span><span class="sxs-lookup"><span data-stu-id="edcec-203">Protect a client-side route (React)</span></span>

<span data-ttu-id="edcec-204">Ochrona trasy po stronie klienta przy użyciu `AuthorizeRoute` składnika zamiast zwykłego `Route` składnika.</span><span class="sxs-lookup"><span data-stu-id="edcec-204">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="edcec-205">Na przykład Zwróć uwagę na to, jak `fetch-data` trasa jest skonfigurowana w `App` składniku:</span><span class="sxs-lookup"><span data-stu-id="edcec-205">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="edcec-206">Ochrona trasy:</span><span class="sxs-lookup"><span data-stu-id="edcec-206">Protecting a route:</span></span>

* <span data-ttu-id="edcec-207">Nie chroni rzeczywistego punktu końcowego (co nadal wymaga `[Authorize]` zastosowania atrybutu).</span><span class="sxs-lookup"><span data-stu-id="edcec-207">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="edcec-208">Uniemożliwia użytkownikowi przechodzenie do danej trasy po stronie klienta, gdy nie jest ona uwierzytelniana.</span><span class="sxs-lookup"><span data-stu-id="edcec-208">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="edcec-209">Uwierzytelnianie żądań interfejsu API (reagowanie)</span><span class="sxs-lookup"><span data-stu-id="edcec-209">Authenticate API requests (React)</span></span>

<span data-ttu-id="edcec-210">Żądania uwierzytelniania przy użyciu reakcji są wykonywane najpierw przez zaimportowanie `authService` wystąpienia z programu `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="edcec-210">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="edcec-211">Token dostępu jest pobierany z `authService` i jest dołączany do żądania, jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="edcec-211">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="edcec-212">W przypadku składników reagujących ta czynność jest zwykle wykonywana w `componentDidMount` metodzie cyklu życia lub w wyniku działania niektórych działań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="edcec-212">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="edcec-213">Zaimportuj authService do składnika</span><span class="sxs-lookup"><span data-stu-id="edcec-213">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="edcec-214">Pobieranie i dołączanie tokenu dostępu do odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="edcec-214">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="edcec-215">Wdrażanie w środowisku produkcyjnym</span><span class="sxs-lookup"><span data-stu-id="edcec-215">Deploy to production</span></span>

<span data-ttu-id="edcec-216">Aby wdrożyć aplikację w środowisku produkcyjnym, należy zainicjować następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="edcec-216">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="edcec-217">Baza danych przechowująca Identity konta użytkowników i Identity dotacje serwera.</span><span class="sxs-lookup"><span data-stu-id="edcec-217">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="edcec-218">Certyfikat produkcyjny do użycia na potrzeby podpisywania tokenów.</span><span class="sxs-lookup"><span data-stu-id="edcec-218">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="edcec-219">Nie ma określonych wymagań dotyczących tego certyfikatu; może to być certyfikat z podpisem własnym lub certyfikat obsługiwany przez urząd certyfikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-219">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="edcec-220">Można go wygenerować za poorednictwem standardowych narzędzi, takich jak PowerShell lub OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="edcec-220">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="edcec-221">Można go zainstalować w magazynie certyfikatów na maszynach docelowych lub wdrożyć jako plik *PFX* przy użyciu silnego hasła.</span><span class="sxs-lookup"><span data-stu-id="edcec-221">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="edcec-222">Przykład: Wdróż do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="edcec-222">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="edcec-223">W tej sekcji opisano wdrażanie aplikacji do Azure App Service przy użyciu certyfikatu przechowywanego w magazynie certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="edcec-223">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="edcec-224">Aby zmodyfikować aplikację w celu załadowania certyfikatu z magazynu certyfikatów, podczas konfigurowania aplikacji w Azure Portal w późniejszym kroku wymagany jest plan usługi warstwy Standardowa lub lepszy.</span><span class="sxs-lookup"><span data-stu-id="edcec-224">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="edcec-225">W *appsettings.js* aplikacji w pliku zmodyfikuj `IdentityServer` sekcję, aby uwzględnić szczegóły klucza:</span><span class="sxs-lookup"><span data-stu-id="edcec-225">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="edcec-226">Nazwa magazynu reprezentuje nazwę magazynu certyfikatów, w którym przechowywany jest certyfikat.</span><span class="sxs-lookup"><span data-stu-id="edcec-226">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="edcec-227">W tym przypadku wskazuje osobisty magazyn użytkownika.</span><span class="sxs-lookup"><span data-stu-id="edcec-227">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="edcec-228">Lokalizacja magazynu reprezentuje miejsce załadowania certyfikatu ( `CurrentUser` lub `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="edcec-228">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="edcec-229">Właściwość Name certyfikatu odpowiada podmiotowi wyróżnionemu dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="edcec-229">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="edcec-230">Aby wdrożyć program do Azure App Service, wykonaj kroki opisane w artykule [wdrażanie aplikacji na platformie Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), w którym wyjaśniono, jak utworzyć niezbędne zasoby platformy Azure i wdrożyć aplikację w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="edcec-230">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="edcec-231">Po wykonaniu powyższych instrukcji aplikacja zostanie wdrożona na platformie Azure, ale nie jest jeszcze funkcjonalna.</span><span class="sxs-lookup"><span data-stu-id="edcec-231">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="edcec-232">Certyfikat używany w aplikacji musi być skonfigurowany w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="edcec-232">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="edcec-233">Znajdź odcisk palca certyfikatu i postępuj zgodnie z instrukcjami opisanymi w artykule [ładowanie certyfikatów](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="edcec-233">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="edcec-234">Chociaż te kroki zawierają informacje o protokole SSL, w Azure Portal znajduje się sekcja **Certyfikaty prywatne** , w której można przekazać certyfikat z obsługą administracyjną do użycia z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="edcec-234">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="edcec-235">Po skonfigurowaniu aplikacji i ustawieniach aplikacji w Azure Portal ponownie uruchom aplikację w portalu.</span><span class="sxs-lookup"><span data-stu-id="edcec-235">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="edcec-236">Inne opcje konfiguracji</span><span class="sxs-lookup"><span data-stu-id="edcec-236">Other configuration options</span></span>

<span data-ttu-id="edcec-237">Obsługa autoryzacji interfejsu API jest oparta na Identity serwerze z zestawem Konwencji, wartościami domyślnymi i ulepszeniami, aby uprościć środowisko aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="edcec-237">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="edcec-238">Niezbędna jest pełna moc Identity serwera w tle, jeśli integracja ASP.NET Core nie obejmuje Twojego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="edcec-238">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="edcec-239">Pomoc techniczna ASP.NET Core jest skoncentrowana na aplikacjach "pierwszej strony", w których wszystkie aplikacje są tworzone i wdrażane przez naszą organizację.</span><span class="sxs-lookup"><span data-stu-id="edcec-239">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="edcec-240">W związku z tym pomoc techniczna nie jest oferowana dla elementów, takich jak zgody lub Federacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-240">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="edcec-241">W tych scenariuszach Użyj Identity serwera i postępuj zgodnie z dokumentacją.</span><span class="sxs-lookup"><span data-stu-id="edcec-241">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="edcec-242">Profile aplikacji</span><span class="sxs-lookup"><span data-stu-id="edcec-242">Application profiles</span></span>

<span data-ttu-id="edcec-243">Profile aplikacji są wstępnie zdefiniowanymi konfiguracjami dla aplikacji, które definiują ich parametry.</span><span class="sxs-lookup"><span data-stu-id="edcec-243">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="edcec-244">W tej chwili obsługiwane są następujące profile:</span><span class="sxs-lookup"><span data-stu-id="edcec-244">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="edcec-245">`IdentityServerSPA`: Reprezentuje funkcję SPA hostowaną razem z Identity serwerem jako pojedynczą jednostką.</span><span class="sxs-lookup"><span data-stu-id="edcec-245">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="edcec-246">`redirect_uri`Wartość domyślna to `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="edcec-246">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="edcec-247">`post_logout_redirect_uri`Wartość domyślna to `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="edcec-247">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="edcec-248">Zestaw zakresów obejmuje `openid` , `profile` i każdy zakres zdefiniowany dla interfejsów API w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-248">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="edcec-249">Zestaw dozwolonych typów odpowiedzi OIDC jest `id_token token` lub każdy z nich indywidualnie ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="edcec-249">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="edcec-250">Dozwolony tryb odpowiedzi to `fragment` .</span><span class="sxs-lookup"><span data-stu-id="edcec-250">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="edcec-251">`SPA`: Reprezentuje SPA, który nie jest obsługiwany z Identity serwerem.</span><span class="sxs-lookup"><span data-stu-id="edcec-251">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="edcec-252">Zestaw zakresów obejmuje `openid` , `profile` i każdy zakres zdefiniowany dla interfejsów API w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-252">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="edcec-253">Zestaw dozwolonych typów odpowiedzi OIDC jest `id_token token` lub każdy z nich indywidualnie ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="edcec-253">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="edcec-254">Dozwolony tryb odpowiedzi to `fragment` .</span><span class="sxs-lookup"><span data-stu-id="edcec-254">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="edcec-255">`IdentityServerJwt`: Reprezentuje interfejs API, który jest hostowany razem z Identity serwerem.</span><span class="sxs-lookup"><span data-stu-id="edcec-255">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="edcec-256">Aplikacja jest skonfigurowana tak, aby zawierała pojedynczy zakres, który jest wartością domyślną dla nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-256">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="edcec-257">`API`: Reprezentuje interfejs API, który nie jest obsługiwany z Identity serwerem.</span><span class="sxs-lookup"><span data-stu-id="edcec-257">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="edcec-258">Aplikacja jest skonfigurowana tak, aby zawierała pojedynczy zakres, który jest wartością domyślną dla nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="edcec-258">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="edcec-259">Konfiguracja za poorednictwem AppSettings</span><span class="sxs-lookup"><span data-stu-id="edcec-259">Configuration through AppSettings</span></span>

<span data-ttu-id="edcec-260">Skonfiguruj aplikacje za pomocą systemu konfiguracji, dodając je do listy `Clients` lub `Resources` .</span><span class="sxs-lookup"><span data-stu-id="edcec-260">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="edcec-261">Skonfiguruj poszczególne klienta `redirect_uri` i `post_logout_redirect_uri` właściwości, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="edcec-261">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="edcec-262">Podczas konfigurowania zasobów można skonfigurować zakresy dla zasobu, jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="edcec-262">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="edcec-263">Konfiguracja przy użyciu kodu</span><span class="sxs-lookup"><span data-stu-id="edcec-263">Configuration through code</span></span>

<span data-ttu-id="edcec-264">Klientów i zasoby można również skonfigurować za pomocą kodu przy użyciu przeciążenia `AddApiAuthorization` , które wykonuje akcję konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="edcec-264">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="edcec-265">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="edcec-265">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>

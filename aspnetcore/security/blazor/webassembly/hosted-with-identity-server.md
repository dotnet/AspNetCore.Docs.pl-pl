---
title: Zabezpiecz Blazor aplikację hostowaną ASP.NET Core webassembly z Identity serwerem
author: guardrex
description: Aby utworzyć nową Blazor hostowaną aplikację z uwierzytelnianiem z poziomu programu Visual Studio, który używa zaplecza [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 2ab43ac5f4de398c57707de23a06a1650f6140cb
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153634"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="c4b3b-103">Zabezpiecz Blazor aplikację hostowaną ASP.NET Core webassembly z Identity serwerem</span><span class="sxs-lookup"><span data-stu-id="c4b3b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="c4b3b-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4b3b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="c4b3b-105">Aby utworzyć nową Blazor hostowaną aplikację w programie Visual Studio, która używa [IdentityServer](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="c4b3b-106">Użyj programu Visual Studio, aby utworzyć nową aplikację \*\* Blazor webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="c4b3b-107">Aby uzyskać więcej informacji, zobacz <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="c4b3b-108">W oknie dialogowym **Tworzenie nowej Blazor aplikacji** wybierz pozycję **Zmień** w sekcji **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="c4b3b-109">Wybierz **pojedyncze konta użytkowników** , a następnie **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="c4b3b-110">Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane** .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="c4b3b-111">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-111">Select the **Create** button.</span></span>

<span data-ttu-id="c4b3b-112">Aby utworzyć aplikację w powłoce poleceń, wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="c4b3b-113">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="c4b3b-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="c4b3b-114">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="c4b3b-115">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="c4b3b-115">Server app configuration</span></span>

<span data-ttu-id="c4b3b-116">W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="c4b3b-117">Klasa początkowa</span><span class="sxs-lookup"><span data-stu-id="c4b3b-117">Startup class</span></span>

<span data-ttu-id="c4b3b-118">`Startup`Klasa zawiera następujące dodatki:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="c4b3b-119">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-119">In `Startup.ConfigureServices`:</span></span>

  * Identity<span data-ttu-id="c4b3b-120">:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-120">:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="c4b3b-121">IdentityServer z dodatkową <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodą pomocniczą, która konfiguruje niektóre domyślne konwencje ASP.NET Core w oparciu o IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="c4b3b-122">Uwierzytelnianie za pomocą dodatkowej <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metody pomocnika, która konfiguruje aplikację do weryfikowania tokenów JWT utworzonych przez IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="c4b3b-123">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="c4b3b-124">Oprogramowanie pośredniczące uwierzytelniania odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="c4b3b-125">Oprogramowanie pośredniczące IdentityServer, które uwidacznia punkty końcowe połączenia Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="c4b3b-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="c4b3b-126">Oprogramowanie pośredniczące uwierzytelniania i autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="c4b3b-127">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="c4b3b-127">AddApiAuthorization</span></span>

<span data-ttu-id="c4b3b-128"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Metoda pomocnika konfiguruje [IdentityServer](https://identityserver.io/) dla scenariuszy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="c4b3b-129">IdentityServer to zaawansowane i rozszerzalne środowisko do obsługi zagadnień związanych z zabezpieczeniami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="c4b3b-130">IdentityServer ujawnia niepotrzebną złożoność w najbardziej typowych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="c4b3b-131">W związku z tym zestaw Konwencji i opcji konfiguracji jest dostępny, ponieważ rozważamy dobry punkt wyjścia.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="c4b3b-132">Gdy uwierzytelnianie będzie wymagało zmiany, pełne możliwości IdentityServer są nadal dostępne, aby dostosować uwierzytelnianie zgodnie z wymaganiami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="c4b3b-133">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="c4b3b-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="c4b3b-134"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="c4b3b-135">Zasady są skonfigurowane tak, aby zezwalały Identity na obsługę wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="c4b3b-136"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Obsługuje wszystkie inne żądania.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="c4b3b-137">Ponadto ta metoda:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-137">Additionally, this method:</span></span>

* <span data-ttu-id="c4b3b-138">Rejestruje `{APPLICATION NAME}API` zasób interfejsu API z IdentityServer z domyślnym zakresem `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="c4b3b-139">Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez IdentityServer dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="c4b3b-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="c4b3b-140">WeatherForecastController</span></span>

<span data-ttu-id="c4b3b-141">W `WeatherForecastController` (*controllers/WeatherForecastController. cs*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="c4b3b-142">Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="c4b3b-143">Domyślne zasady autoryzacji są skonfigurowane tak, aby korzystały z domyślnego schematu uwierzytelniania, który jest skonfigurowany zgodnie ze <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> schematem zasad wymienionym wcześniej.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="c4b3b-144">Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="c4b3b-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="c4b3b-145">ApplicationDbContext</span></span>

<span data-ttu-id="c4b3b-146">W programie `ApplicationDbContext` (*Data/ApplicationDbContext. cs*) ta sama wartość <xref:Microsoft.EntityFrameworkCore.DbContext> jest używana w Identity połączeniu z wyjątkiem, który rozszerza w <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> celu uwzględnienia schematu IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="c4b3b-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi od <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="c4b3b-148">Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity <xref:Microsoft.EntityFrameworkCore.DbContext> klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="c4b3b-149">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="c4b3b-149">OidcConfigurationController</span></span>

<span data-ttu-id="c4b3b-150">W `OidcConfigurationController` (*controllers/OidcConfigurationController. cs*) punkt końcowy klienta jest inicjowany do obsługi parametrów OIDC.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="c4b3b-151">Pliki ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="c4b3b-151">App settings files</span></span>

<span data-ttu-id="c4b3b-152">W sekcji plik ustawień aplikacji (*appSettings. JSON*) w katalogu głównym projektu znajduje się `IdentityServer` Opis listy skonfigurowanych klientów.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="c4b3b-153">W poniższym przykładzie istnieje pojedynczy klient.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-153">In the following example, there's a single client.</span></span> <span data-ttu-id="c4b3b-154">Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="c4b3b-155">Profil wskazuje konfigurowany typ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="c4b3b-156">Profil jest używany wewnętrznie w celu napędu Konwencji upraszczających proces konfiguracji serwera.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="c4b3b-157">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="c4b3b-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="c4b3b-158">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="c4b3b-158">Authentication package</span></span>

<span data-ttu-id="c4b3b-159">Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników ( `Individual` ), aplikacja automatycznie otrzymuje odwołanie do pakietu `Microsoft.AspNetCore.Components.WebAssembly.Authentication` w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="c4b3b-160">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="c4b3b-161">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="c4b3b-162">Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="c4b3b-163">Obsługa autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="c4b3b-163">API authorization support</span></span>

<span data-ttu-id="c4b3b-164">Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiecie.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="c4b3b-165">Ta metoda konfiguruje wszystkie usługi, które są konieczne, aby aplikacja mogła współdziałać z istniejącym systemem autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="c4b3b-166">Domyślnie ładuje ona konfigurację aplikacji według Konwencji z programu `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="c4b3b-167">Zgodnie z Konwencją identyfikator klienta jest ustawiany na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="c4b3b-168">Ten adres URL można zmienić tak, aby wskazywał osobny punkt końcowy przez wywołanie przeciążenia z opcjami.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="c4b3b-169">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="c4b3b-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="c4b3b-170">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="c4b3b-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="c4b3b-171">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="c4b3b-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="c4b3b-172">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="c4b3b-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="c4b3b-173">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="c4b3b-173">LoginDisplay component</span></span>

<span data-ttu-id="c4b3b-174">`LoginDisplay`Składnik (*Shared/LoginDisplay. Razor*) jest renderowany w `MainLayout` składniku (*Shared/MainLayout. Razor*) i zarządza następującymi zachowaniami:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="c4b3b-175">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-175">For authenticated users:</span></span>
  * <span data-ttu-id="c4b3b-176">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-176">Displays the current user name.</span></span>
  * <span data-ttu-id="c4b3b-177">Oferuje link do strony profilu użytkownika w ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="c4b3b-178">Oferuje przycisk umożliwiający wylogowanie się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="c4b3b-179">Dla użytkowników anonimowych:</span><span class="sxs-lookup"><span data-stu-id="c4b3b-179">For anonymous users:</span></span>
  * <span data-ttu-id="c4b3b-180">Oferuje opcję rejestracji.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-180">Offers the option to register.</span></span>
  * <span data-ttu-id="c4b3b-181">Oferuje opcję logowania.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="c4b3b-182">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="c4b3b-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="c4b3b-183">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="c4b3b-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="c4b3b-184">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c4b3b-184">Run the app</span></span>

<span data-ttu-id="c4b3b-185">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="c4b3b-185">Run the app from the Server project.</span></span> <span data-ttu-id="c4b3b-186">W przypadku korzystania z programu Visual Studio wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="c4b3b-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="c4b3b-187">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c4b3b-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="c4b3b-188">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="c4b3b-188">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

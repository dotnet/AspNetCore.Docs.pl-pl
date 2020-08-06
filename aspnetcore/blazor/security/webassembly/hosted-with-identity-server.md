---
title: Zabezpieczanie hostowanej Blazor WebAssembly aplikacji ASP.NET Core z Identity serwerem
author: guardrex
description: Aby utworzyć nowe rozwiązanie hostowane Blazor z uwierzytelnianiem z poziomu programu Visual Studio, które korzysta z zaplecza [ Identity serwera](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 87424f413ab21ae51fc1b1b2033069f5a41da566
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818888"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="0ab36-103">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core Identity z serwerem</span><span class="sxs-lookup"><span data-stu-id="0ab36-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="0ab36-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0ab36-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0ab36-105">W tym artykule wyjaśniono, jak utworzyć nowe Blazor rozwiązanie hostowane, które używa [ Identity serwera](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="0ab36-105">This article explains how to create a new hosted Blazor solution that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="0ab36-106">Aby skonfigurować autonomiczną lub hostowaną Blazor WebAssembly aplikację do korzystania z istniejącego Identity wystąpienia serwera zewnętrznego, postępuj zgodnie ze wskazówkami w temacie <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="0ab36-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ab36-107">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ab36-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0ab36-108">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="0ab36-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="0ab36-109">Po wybraniu szablonu \*\* Blazor WebAssembly aplikacji\*\* w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="0ab36-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="0ab36-110">Wybierz opcję **konta poszczególnych użytkowników** z opcją **Zapisz konta użytkowników w aplikacji** , aby przechowywać użytkowników w ramach aplikacji przy użyciu [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ab36-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="0ab36-111">Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane** .</span><span class="sxs-lookup"><span data-stu-id="0ab36-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="0ab36-112">Visual Studio Code/interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0ab36-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="0ab36-113">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania w pustym folderze, określ `Individual` mechanizm uwierzytelniania z `-au|--auth` opcją zapisania użytkowników w aplikacji przy użyciu [Identity](xref:security/authentication/identity) systemu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0ab36-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="0ab36-114">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="0ab36-114">Placeholder</span></span>  | <span data-ttu-id="0ab36-115">Przykład</span><span class="sxs-lookup"><span data-stu-id="0ab36-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="0ab36-116">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="0ab36-117">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ab36-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ab36-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ab36-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0ab36-119">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="0ab36-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="0ab36-120">W kroku **Skonfiguruj nową Blazor WebAssembly aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="0ab36-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="0ab36-121">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="0ab36-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="0ab36-122">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="0ab36-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="0ab36-123">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="0ab36-123">Server app configuration</span></span>

<span data-ttu-id="0ab36-124">W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0ab36-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="0ab36-125">Klasa początkowa</span><span class="sxs-lookup"><span data-stu-id="0ab36-125">Startup class</span></span>

<span data-ttu-id="0ab36-126">`Startup`Klasa zawiera następujące dodatki.</span><span class="sxs-lookup"><span data-stu-id="0ab36-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="0ab36-127">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0ab36-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="0ab36-128">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="0ab36-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="0ab36-129">IdentitySerwer z dodatkową <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodą pomocnika, która konfiguruje domyślne konwencje ASP.NET Core na Identity serwerze:</span><span class="sxs-lookup"><span data-stu-id="0ab36-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="0ab36-130">Uwierzytelnianie za pomocą dodatkowej <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metody pomocnika, która konfiguruje aplikację do weryfikacji tokenów JWT utworzonych przez Identity serwer:</span><span class="sxs-lookup"><span data-stu-id="0ab36-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="0ab36-131">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0ab36-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="0ab36-132">IdentityOprogramowanie pośredniczące serwera uwidacznia punkty końcowe OpenID Connect Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="0ab36-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="0ab36-133">Oprogramowanie pośredniczące uwierzytelniania jest odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:</span><span class="sxs-lookup"><span data-stu-id="0ab36-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="0ab36-134">Oprogramowanie pośredniczące autoryzacji włącza funkcje autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="0ab36-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="0ab36-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="0ab36-135">AddApiAuthorization</span></span>

<span data-ttu-id="0ab36-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Metoda pomocnika konfiguruje [ Identity serwer](https://identityserver.io/) dla scenariuszy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ab36-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="0ab36-137">IdentitySerwer to zaawansowana i rozszerzalna platforma do obsługi zagadnień związanych z zabezpieczeniami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="0ab36-138">IdentitySerwer uwidacznia niepotrzebną złożoność dla najbardziej typowych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="0ab36-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="0ab36-139">W związku z tym zestaw Konwencji i opcji konfiguracji jest dostępny, ponieważ rozważamy dobry punkt wyjścia.</span><span class="sxs-lookup"><span data-stu-id="0ab36-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="0ab36-140">Gdy uwierzytelnianie wymaga zmiany, Identity dostępna jest pełna moc serwera, aby dostosować uwierzytelnianie do wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="0ab36-141">Dodaj Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="0ab36-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="0ab36-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0ab36-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="0ab36-143">Zasady są skonfigurowane tak, aby zezwalały Identity na obsługę wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="0ab36-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="0ab36-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Obsługuje wszystkie inne żądania.</span><span class="sxs-lookup"><span data-stu-id="0ab36-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="0ab36-145">Ponadto ta metoda:</span><span class="sxs-lookup"><span data-stu-id="0ab36-145">Additionally, this method:</span></span>

* <span data-ttu-id="0ab36-146">Rejestruje `{APPLICATION NAME}API` zasób interfejsu API z Identity serwerem z domyślnym zakresem `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="0ab36-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="0ab36-147">Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez Identity serwer dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="0ab36-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="0ab36-148">WeatherForecastController</span></span>

<span data-ttu-id="0ab36-149">W `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy.</span><span class="sxs-lookup"><span data-stu-id="0ab36-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="0ab36-150">Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="0ab36-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="0ab36-151">Domyślne zasady autoryzacji są skonfigurowane do używania domyślnego schematu uwierzytelniania, który jest konfigurowany przez program <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="0ab36-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="0ab36-152">Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="0ab36-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="0ab36-153">ApplicationDbContext</span></span>

<span data-ttu-id="0ab36-154">W programie `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) program <xref:Microsoft.EntityFrameworkCore.DbContext> rozszerza, <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Aby uwzględnić schemat dla Identity serwera.</span><span class="sxs-lookup"><span data-stu-id="0ab36-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="0ab36-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi od <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="0ab36-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="0ab36-156">Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity <xref:Microsoft.EntityFrameworkCore.DbContext> klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="0ab36-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="0ab36-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="0ab36-157">OidcConfigurationController</span></span>

<span data-ttu-id="0ab36-158">W `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) punkt końcowy klienta jest inicjowany do obsługi parametrów OIDC.</span><span class="sxs-lookup"><span data-stu-id="0ab36-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="0ab36-159">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="0ab36-159">App settings</span></span>

<span data-ttu-id="0ab36-160">W sekcji Ustawienia aplikacji ( `appsettings.json` ) w katalogu głównym projektu `IdentityServer` sekcja zawiera opis listy skonfigurowanych klientów.</span><span class="sxs-lookup"><span data-stu-id="0ab36-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="0ab36-161">W poniższym przykładzie istnieje pojedynczy klient.</span><span class="sxs-lookup"><span data-stu-id="0ab36-161">In the following example, there's a single client.</span></span> <span data-ttu-id="0ab36-162">Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth.</span><span class="sxs-lookup"><span data-stu-id="0ab36-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="0ab36-163">Profil wskazuje konfigurowany typ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="0ab36-164">Profil jest używany wewnętrznie w celu napędu Konwencji upraszczających proces konfiguracji serwera.</span><span class="sxs-lookup"><span data-stu-id="0ab36-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="0ab36-165">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="0ab36-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="0ab36-166">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="0ab36-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="0ab36-167">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0ab36-167">Authentication package</span></span>

<span data-ttu-id="0ab36-168">Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników ( `Individual` ), aplikacja automatycznie otrzymuje odwołanie do pakietu [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="0ab36-169">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="0ab36-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0ab36-170">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="0ab36-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a><span data-ttu-id="0ab36-171">`HttpClient`skonfigurować</span><span class="sxs-lookup"><span data-stu-id="0ab36-171">`HttpClient` configuration</span></span>

<span data-ttu-id="0ab36-172">W programie `Program.Main` ( `Program.cs` ) nazwa <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) jest skonfigurowana do dostarczania <xref:System.Net.Http.HttpClient> wystąpień, które zawierają tokeny dostępu podczas wykonywania żądań do interfejsu API serwera:</span><span class="sxs-lookup"><span data-stu-id="0ab36-172">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="0ab36-173">Jeśli konfigurujesz Blazor WebAssembly aplikację tak, aby korzystała z istniejącego Identity wystąpienia serwera, które nie jest częścią rozwiązania hostowanego Blazor , Zmień <xref:System.Net.Http.HttpClient> rejestrację adresu podstawowego z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) na adres URL punktu końcowego autoryzacji interfejsu API aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="0ab36-173">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="0ab36-174">Obsługa autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="0ab36-174">API authorization support</span></span>

<span data-ttu-id="0ab36-175">Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pakiecie.</span><span class="sxs-lookup"><span data-stu-id="0ab36-175">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="0ab36-176">Ta metoda konfiguruje usługi wymagane przez aplikację do współdziałania z istniejącym systemem autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-176">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="0ab36-177">Domyślnie konfiguracja aplikacji jest ładowana zgodnie z Konwencją z programu `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="0ab36-177">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="0ab36-178">Zgodnie z Konwencją identyfikator klienta jest ustawiany na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-178">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="0ab36-179">Ten adres URL można zmienić tak, aby wskazywał osobny punkt końcowy przez wywołanie przeciążenia z opcjami.</span><span class="sxs-lookup"><span data-stu-id="0ab36-179">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="0ab36-180">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="0ab36-180">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="0ab36-181">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="0ab36-181">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="0ab36-182">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="0ab36-182">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="0ab36-183">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="0ab36-183">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="0ab36-184">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="0ab36-184">LoginDisplay component</span></span>

<span data-ttu-id="0ab36-185">`LoginDisplay`Składnik ( `Shared/LoginDisplay.razor` ) jest renderowany w `MainLayout` składniku ( `Shared/MainLayout.razor` ) i zarządza następującymi zachowaniami:</span><span class="sxs-lookup"><span data-stu-id="0ab36-185">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="0ab36-186">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="0ab36-186">For authenticated users:</span></span>
  * <span data-ttu-id="0ab36-187">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0ab36-187">Displays the current user name.</span></span>
  * <span data-ttu-id="0ab36-188">Oferuje link do strony profilu użytkownika w ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="0ab36-188">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="0ab36-189">Oferuje przycisk umożliwiający wylogowanie się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-189">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="0ab36-190">Dla użytkowników anonimowych:</span><span class="sxs-lookup"><span data-stu-id="0ab36-190">For anonymous users:</span></span>
  * <span data-ttu-id="0ab36-191">Oferuje opcję rejestracji.</span><span class="sxs-lookup"><span data-stu-id="0ab36-191">Offers the option to register.</span></span>
  * <span data-ttu-id="0ab36-192">Oferuje opcję logowania.</span><span class="sxs-lookup"><span data-stu-id="0ab36-192">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="0ab36-193">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0ab36-193">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="0ab36-194">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="0ab36-194">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="0ab36-195">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="0ab36-195">Run the app</span></span>

<span data-ttu-id="0ab36-196">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="0ab36-196">Run the app from the Server project.</span></span> <span data-ttu-id="0ab36-197">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0ab36-197">When using Visual Studio, either:</span></span>

* <span data-ttu-id="0ab36-198">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="0ab36-198">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="0ab36-199">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="0ab36-199">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="0ab36-200">Nazwa i rola roli z autoryzacją interfejsu API</span><span class="sxs-lookup"><span data-stu-id="0ab36-200">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="0ab36-201">Niestandardowa fabryka użytkowników</span><span class="sxs-lookup"><span data-stu-id="0ab36-201">Custom user factory</span></span>

<span data-ttu-id="0ab36-202">W aplikacji klienckiej Utwórz niestandardową fabrykę użytkowników.</span><span class="sxs-lookup"><span data-stu-id="0ab36-202">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="0ab36-203">IdentitySerwer wysyła wiele ról jako tablicę JSON w ramach pojedynczego `role` żądania.</span><span class="sxs-lookup"><span data-stu-id="0ab36-203">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="0ab36-204">Pojedyncza rola jest wysyłana jako wartość ciągu w ramach żądania.</span><span class="sxs-lookup"><span data-stu-id="0ab36-204">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="0ab36-205">Fabryka tworzy pojedyncze zgłoszenie `role` dla każdej z ról użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0ab36-205">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="0ab36-206">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="0ab36-206">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="0ab36-207">W aplikacji klienckiej Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="0ab36-207">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="0ab36-208">W aplikacji serwera Wywołaj <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity konstruktora, który dodaje usługi związane z rolami:</span><span class="sxs-lookup"><span data-stu-id="0ab36-208">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="0ab36-209">Skonfiguruj Identity serwer</span><span class="sxs-lookup"><span data-stu-id="0ab36-209">Configure Identity Server</span></span>

<span data-ttu-id="0ab36-210">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0ab36-210">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="0ab36-211">Opcje autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="0ab36-211">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="0ab36-212">Usługa profilowania</span><span class="sxs-lookup"><span data-stu-id="0ab36-212">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="0ab36-213">Opcje autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="0ab36-213">API authorization options</span></span>

<span data-ttu-id="0ab36-214">W aplikacji serwerowej:</span><span class="sxs-lookup"><span data-stu-id="0ab36-214">In the Server app:</span></span>

* <span data-ttu-id="0ab36-215">Skonfiguruj Identity serwer, aby umieścić `name` oświadczenia i w tokenie `role` dostępu.</span><span class="sxs-lookup"><span data-stu-id="0ab36-215">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="0ab36-216">Zapobiegaj domyślnym mapowaniu ról w programie obsługi tokenów JWT.</span><span class="sxs-lookup"><span data-stu-id="0ab36-216">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="0ab36-217">Usługa profilowania</span><span class="sxs-lookup"><span data-stu-id="0ab36-217">Profile Service</span></span>

<span data-ttu-id="0ab36-218">W aplikacji serwerowej Utwórz `ProfileService` implementację.</span><span class="sxs-lookup"><span data-stu-id="0ab36-218">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="0ab36-219">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="0ab36-219">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="0ab36-220">W aplikacji serwera Zarejestruj usługę profilu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0ab36-220">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="0ab36-221">Korzystanie z mechanizmów autoryzacji</span><span class="sxs-lookup"><span data-stu-id="0ab36-221">Use authorization mechanisms</span></span>

<span data-ttu-id="0ab36-222">W aplikacji klienckiej podejścia do autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="0ab36-222">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="0ab36-223">Każdy mechanizm autoryzacji w składnikach może używać roli do autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0ab36-223">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="0ab36-224">[ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="0ab36-224">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="0ab36-225">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="0ab36-225">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="0ab36-226">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="0ab36-226">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="0ab36-227">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="0ab36-227">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="0ab36-228">`User.Identity.Name`Program jest wypełniany w aplikacji klienckiej przy użyciu nazwy użytkownika, która jest zazwyczaj ich adresem e-mail logowania.</span><span class="sxs-lookup"><span data-stu-id="0ab36-228">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0ab36-229">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0ab36-229">Additional resources</span></span>

* [<span data-ttu-id="0ab36-230">Wdrożenie do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0ab36-230">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="0ab36-231">Importowanie certyfikatu z Key Vault (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="0ab36-231">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="0ab36-232">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="0ab36-232">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

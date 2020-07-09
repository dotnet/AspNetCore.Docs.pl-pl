---
title: Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core Identity z serwerem
author: guardrex
description: Aby utworzyć nową Blazor hostowaną aplikację z uwierzytelnianiem z poziomu programu Visual Studio, który używa zaplecza [ Identity serwera](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 001fa0885c4ef4f365d9849278d3aa36e7657c54
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147722"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="71b9a-103">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core Identity z serwerem</span><span class="sxs-lookup"><span data-stu-id="71b9a-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="71b9a-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="71b9a-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="71b9a-105">W tym artykule wyjaśniono, jak utworzyć nową Blazor hostowaną aplikację, która używa [ Identity serwera](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="71b9a-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="71b9a-106">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71b9a-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="71b9a-107">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="71b9a-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="71b9a-108">Po wybraniu szablonu \*\* Blazor WebAssembly aplikacji\*\* w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="71b9a-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="71b9a-109">Wybierz opcję **konta poszczególnych użytkowników** z opcją **Zapisz konta użytkowników w aplikacji** , aby przechowywać użytkowników w ramach aplikacji przy użyciu [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="71b9a-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="71b9a-110">Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane** .</span><span class="sxs-lookup"><span data-stu-id="71b9a-110">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="71b9a-111">Visual Studio Code/interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="71b9a-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="71b9a-112">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania w pustym folderze, określ `Individual` mechanizm uwierzytelniania z `-au|--auth` opcją zapisania użytkowników w aplikacji przy użyciu [Identity](xref:security/authentication/identity) systemu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="71b9a-112">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="71b9a-113">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="71b9a-113">Placeholder</span></span>  | <span data-ttu-id="71b9a-114">Przykład</span><span class="sxs-lookup"><span data-stu-id="71b9a-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="71b9a-115">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="71b9a-116">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="71b9a-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="71b9a-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="71b9a-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="71b9a-118">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="71b9a-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="71b9a-119">W kroku **Skonfiguruj nową Blazor WebAssembly aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="71b9a-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="71b9a-120">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="71b9a-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="71b9a-121">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="71b9a-121">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="71b9a-122">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="71b9a-122">Server app configuration</span></span>

<span data-ttu-id="71b9a-123">W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="71b9a-123">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="71b9a-124">Klasa początkowa</span><span class="sxs-lookup"><span data-stu-id="71b9a-124">Startup class</span></span>

<span data-ttu-id="71b9a-125">`Startup`Klasa zawiera następujące dodatki.</span><span class="sxs-lookup"><span data-stu-id="71b9a-125">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="71b9a-126">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="71b9a-126">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="71b9a-127">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="71b9a-127">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity<span data-ttu-id="71b9a-128">Serwer z dodatkową <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodą pomocnika, która konfiguruje domyślne konwencje ASP.NET Core na Identity serwerze:</span><span class="sxs-lookup"><span data-stu-id="71b9a-128">Server with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="71b9a-129">Uwierzytelnianie za pomocą dodatkowej <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metody pomocnika, która konfiguruje aplikację do weryfikacji tokenów JWT utworzonych przez Identity serwer:</span><span class="sxs-lookup"><span data-stu-id="71b9a-129">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="71b9a-130">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="71b9a-130">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="71b9a-131">IdentityOprogramowanie pośredniczące serwera uwidacznia punkty końcowe połączenia Open ID (OIDC):</span><span class="sxs-lookup"><span data-stu-id="71b9a-131">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="71b9a-132">Oprogramowanie pośredniczące uwierzytelniania jest odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:</span><span class="sxs-lookup"><span data-stu-id="71b9a-132">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="71b9a-133">Oprogramowanie pośredniczące autoryzacji włącza funkcje autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="71b9a-133">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="71b9a-134">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="71b9a-134">AddApiAuthorization</span></span>

<span data-ttu-id="71b9a-135"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Metoda pomocnika konfiguruje [ Identity serwer](https://identityserver.io/) dla scenariuszy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="71b9a-135">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> Identity<span data-ttu-id="71b9a-136">Serwer to zaawansowana i rozszerzalna platforma do obsługi zagadnień związanych z zabezpieczeniami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-136">Server is a powerful and extensible framework for handling app security concerns.</span></span> Identity<span data-ttu-id="71b9a-137">Serwer uwidacznia niepotrzebną złożoność dla najbardziej typowych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="71b9a-137">Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="71b9a-138">W związku z tym zestaw Konwencji i opcji konfiguracji jest dostępny, ponieważ rozważamy dobry punkt wyjścia.</span><span class="sxs-lookup"><span data-stu-id="71b9a-138">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="71b9a-139">Gdy uwierzytelnianie wymaga zmiany, Identity dostępna jest pełna moc serwera, aby dostosować uwierzytelnianie do wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-139">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="71b9a-140">Dodaj Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="71b9a-140">AddIdentityServerJwt</span></span>

<span data-ttu-id="71b9a-141"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="71b9a-141">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="71b9a-142">Zasady są skonfigurowane tak, aby zezwalały Identity na obsługę wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="71b9a-142">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="71b9a-143"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Obsługuje wszystkie inne żądania.</span><span class="sxs-lookup"><span data-stu-id="71b9a-143">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="71b9a-144">Ponadto ta metoda:</span><span class="sxs-lookup"><span data-stu-id="71b9a-144">Additionally, this method:</span></span>

* <span data-ttu-id="71b9a-145">Rejestruje `{APPLICATION NAME}API` zasób interfejsu API z Identity serwerem z domyślnym zakresem `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="71b9a-145">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="71b9a-146">Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez Identity serwer dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-146">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="71b9a-147">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="71b9a-147">WeatherForecastController</span></span>

<span data-ttu-id="71b9a-148">W `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy.</span><span class="sxs-lookup"><span data-stu-id="71b9a-148">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="71b9a-149">Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="71b9a-149">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="71b9a-150">Domyślne zasady autoryzacji są skonfigurowane do używania domyślnego schematu uwierzytelniania, który jest konfigurowany przez program <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="71b9a-150">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="71b9a-151">Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-151">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="71b9a-152">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="71b9a-152">ApplicationDbContext</span></span>

<span data-ttu-id="71b9a-153">W programie `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) program <xref:Microsoft.EntityFrameworkCore.DbContext> rozszerza, <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Aby uwzględnić schemat dla Identity serwera.</span><span class="sxs-lookup"><span data-stu-id="71b9a-153">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="71b9a-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi od <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="71b9a-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="71b9a-155">Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity <xref:Microsoft.EntityFrameworkCore.DbContext> klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="71b9a-155">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="71b9a-156">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="71b9a-156">OidcConfigurationController</span></span>

<span data-ttu-id="71b9a-157">W `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) punkt końcowy klienta jest inicjowany do obsługi parametrów OIDC.</span><span class="sxs-lookup"><span data-stu-id="71b9a-157">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="71b9a-158">Pliki ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="71b9a-158">App settings files</span></span>

<span data-ttu-id="71b9a-159">W sekcji Ustawienia aplikacji ( `appsettings.json` ) w katalogu głównym projektu `IdentityServer` sekcja zawiera opis listy skonfigurowanych klientów.</span><span class="sxs-lookup"><span data-stu-id="71b9a-159">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="71b9a-160">W poniższym przykładzie istnieje pojedynczy klient.</span><span class="sxs-lookup"><span data-stu-id="71b9a-160">In the following example, there's a single client.</span></span> <span data-ttu-id="71b9a-161">Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth.</span><span class="sxs-lookup"><span data-stu-id="71b9a-161">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="71b9a-162">Profil wskazuje konfigurowany typ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-162">The profile indicates the app type being configured.</span></span> <span data-ttu-id="71b9a-163">Profil jest używany wewnętrznie w celu napędu Konwencji upraszczających proces konfiguracji serwera.</span><span class="sxs-lookup"><span data-stu-id="71b9a-163">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="71b9a-164">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="71b9a-164">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="71b9a-165">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="71b9a-165">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="71b9a-166">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="71b9a-166">Authentication package</span></span>

<span data-ttu-id="71b9a-167">Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników ( `Individual` ), aplikacja automatycznie otrzymuje odwołanie do pakietu [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-167">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="71b9a-168">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="71b9a-168">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="71b9a-169">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="71b9a-169">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="71b9a-170">Obsługa autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="71b9a-170">API authorization support</span></span>

<span data-ttu-id="71b9a-171">Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pakiecie.</span><span class="sxs-lookup"><span data-stu-id="71b9a-171">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="71b9a-172">Ta metoda konfiguruje usługi wymagane przez aplikację do współdziałania z istniejącym systemem autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-172">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="71b9a-173">Domyślnie konfiguracja aplikacji jest ładowana zgodnie z Konwencją z programu `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="71b9a-173">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="71b9a-174">Zgodnie z Konwencją identyfikator klienta jest ustawiany na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-174">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="71b9a-175">Ten adres URL można zmienić tak, aby wskazywał osobny punkt końcowy przez wywołanie przeciążenia z opcjami.</span><span class="sxs-lookup"><span data-stu-id="71b9a-175">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="71b9a-176">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="71b9a-176">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="71b9a-177">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="71b9a-177">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="71b9a-178">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="71b9a-178">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="71b9a-179">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="71b9a-179">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="71b9a-180">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="71b9a-180">LoginDisplay component</span></span>

<span data-ttu-id="71b9a-181">`LoginDisplay`Składnik ( `Shared/LoginDisplay.razor` ) jest renderowany w `MainLayout` składniku ( `Shared/MainLayout.razor` ) i zarządza następującymi zachowaniami:</span><span class="sxs-lookup"><span data-stu-id="71b9a-181">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="71b9a-182">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="71b9a-182">For authenticated users:</span></span>
  * <span data-ttu-id="71b9a-183">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="71b9a-183">Displays the current user name.</span></span>
  * <span data-ttu-id="71b9a-184">Oferuje link do strony profilu użytkownika w ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="71b9a-184">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="71b9a-185">Oferuje przycisk umożliwiający wylogowanie się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-185">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="71b9a-186">Dla użytkowników anonimowych:</span><span class="sxs-lookup"><span data-stu-id="71b9a-186">For anonymous users:</span></span>
  * <span data-ttu-id="71b9a-187">Oferuje opcję rejestracji.</span><span class="sxs-lookup"><span data-stu-id="71b9a-187">Offers the option to register.</span></span>
  * <span data-ttu-id="71b9a-188">Oferuje opcję logowania.</span><span class="sxs-lookup"><span data-stu-id="71b9a-188">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="71b9a-189">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="71b9a-189">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="71b9a-190">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="71b9a-190">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="71b9a-191">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="71b9a-191">Run the app</span></span>

<span data-ttu-id="71b9a-192">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="71b9a-192">Run the app from the Server project.</span></span> <span data-ttu-id="71b9a-193">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="71b9a-193">When using Visual Studio, either:</span></span>

* <span data-ttu-id="71b9a-194">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="71b9a-194">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="71b9a-195">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="71b9a-195">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="71b9a-196">Nazwa i rola roli z autoryzacją interfejsu API</span><span class="sxs-lookup"><span data-stu-id="71b9a-196">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="71b9a-197">Niestandardowa fabryka użytkowników</span><span class="sxs-lookup"><span data-stu-id="71b9a-197">Custom user factory</span></span>

<span data-ttu-id="71b9a-198">W aplikacji klienckiej Utwórz niestandardową fabrykę użytkowników.</span><span class="sxs-lookup"><span data-stu-id="71b9a-198">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="71b9a-199">Serwer wysyła wiele ról jako tablicę JSON w ramach pojedynczego `role` żądania.</span><span class="sxs-lookup"><span data-stu-id="71b9a-199"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="71b9a-200">Pojedyncza rola jest wysyłana jako wartość ciągu w ramach żądania.</span><span class="sxs-lookup"><span data-stu-id="71b9a-200">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="71b9a-201">Fabryka tworzy pojedyncze zgłoszenie `role` dla każdej z ról użytkownika.</span><span class="sxs-lookup"><span data-stu-id="71b9a-201">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="71b9a-202">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="71b9a-202">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="71b9a-203">W aplikacji klienckiej Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="71b9a-203">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="71b9a-204">W aplikacji serwera Wywołaj <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity konstruktora, który dodaje usługi związane z rolami:</span><span class="sxs-lookup"><span data-stu-id="71b9a-204">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="71b9a-205">Skonfiguruj Identity serwer</span><span class="sxs-lookup"><span data-stu-id="71b9a-205">Configure Identity Server</span></span>

<span data-ttu-id="71b9a-206">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="71b9a-206">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="71b9a-207">Opcje autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="71b9a-207">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="71b9a-208">Usługa profilowania</span><span class="sxs-lookup"><span data-stu-id="71b9a-208">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="71b9a-209">Opcje autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="71b9a-209">API authorization options</span></span>

<span data-ttu-id="71b9a-210">W aplikacji serwerowej:</span><span class="sxs-lookup"><span data-stu-id="71b9a-210">In the Server app:</span></span>

* <span data-ttu-id="71b9a-211">Skonfiguruj Identity serwer, aby umieścić `name` oświadczenia i w tokenie `role` dostępu.</span><span class="sxs-lookup"><span data-stu-id="71b9a-211">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="71b9a-212">Zapobiegaj domyślnym mapowaniu ról w programie obsługi tokenów JWT.</span><span class="sxs-lookup"><span data-stu-id="71b9a-212">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="71b9a-213">Usługa profilowania</span><span class="sxs-lookup"><span data-stu-id="71b9a-213">Profile Service</span></span>

<span data-ttu-id="71b9a-214">W aplikacji serwerowej Utwórz `ProfileService` implementację.</span><span class="sxs-lookup"><span data-stu-id="71b9a-214">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="71b9a-215">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="71b9a-215">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="71b9a-216">W aplikacji serwera Zarejestruj usługę profilu w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71b9a-216">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="71b9a-217">Korzystanie z mechanizmów autoryzacji</span><span class="sxs-lookup"><span data-stu-id="71b9a-217">Use authorization mechanisms</span></span>

<span data-ttu-id="71b9a-218">W aplikacji klienckiej podejścia do autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="71b9a-218">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="71b9a-219">Każdy mechanizm autoryzacji w składnikach może używać roli do autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="71b9a-219">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="71b9a-220">[ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="71b9a-220">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="71b9a-221">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="71b9a-221">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="71b9a-222">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="71b9a-222">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="71b9a-223">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="71b9a-223">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="71b9a-224">`User.Identity.Name`Program jest wypełniany w aplikacji klienckiej przy użyciu nazwy użytkownika, która jest zazwyczaj ich adresem e-mail logowania.</span><span class="sxs-lookup"><span data-stu-id="71b9a-224">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="71b9a-225">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="71b9a-225">Additional resources</span></span>

* [<span data-ttu-id="71b9a-226">Wdrożenie do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="71b9a-226">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="71b9a-227">Importowanie certyfikatu z Key Vault (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="71b9a-227">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="71b9a-228">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="71b9a-228">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

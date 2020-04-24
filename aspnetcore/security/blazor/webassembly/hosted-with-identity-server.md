---
title: Zabezpiecz aplikację hostowaną ASP.NET Core Blazor webassembly z serwerem tożsamości
author: guardrex
description: Aby utworzyć nową Blazor hostowaną aplikację z uwierzytelnianiem z poziomu programu Visual Studio, który używa zaplecza [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: f8de07e2e21ca19b5c4e95839e7b7e621c335ad0
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110953"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="90d94-103">Zabezpiecz aplikację hostowaną ASP.NET Core Blazor webassembly z serwerem tożsamości</span><span class="sxs-lookup"><span data-stu-id="90d94-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="90d94-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="90d94-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="90d94-105">Wskazówki zawarte w tym artykule dotyczą ASP.NET Core 3,2 w wersji zapoznawczej 4.</span><span class="sxs-lookup"><span data-stu-id="90d94-105">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="90d94-106">Ten temat zostanie zaktualizowany do wersji zapoznawczej 5 w piątek, 24 kwietnia.</span><span class="sxs-lookup"><span data-stu-id="90d94-106">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="90d94-107">Aby utworzyć nową Blazor hostowaną aplikację w programie Visual Studio, która używa [IdentityServer](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="90d94-107">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="90d94-108">Użyj programu Visual Studio, aby utworzyć nową \*\* Blazor aplikację webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="90d94-108">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="90d94-109">Aby uzyskać więcej informacji, zobacz <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="90d94-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="90d94-110">W oknie dialogowym **Tworzenie nowej Blazor aplikacji** wybierz pozycję **Zmień** w sekcji **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="90d94-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="90d94-111">Wybierz **pojedyncze konta użytkowników** , a następnie **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="90d94-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="90d94-112">Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane** .</span><span class="sxs-lookup"><span data-stu-id="90d94-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="90d94-113">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="90d94-113">Select the **Create** button.</span></span>

<span data-ttu-id="90d94-114">Aby utworzyć aplikację w powłoce poleceń, wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="90d94-114">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="90d94-115">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="90d94-115">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="90d94-116">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="90d94-116">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="90d94-117">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="90d94-117">Server app configuration</span></span>

<span data-ttu-id="90d94-118">W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="90d94-118">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="90d94-119">Klasa początkowa</span><span class="sxs-lookup"><span data-stu-id="90d94-119">Startup class</span></span>

<span data-ttu-id="90d94-120">`Startup` Klasa zawiera następujące dodatki:</span><span class="sxs-lookup"><span data-stu-id="90d94-120">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="90d94-121">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="90d94-121">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="90d94-122">Identity</span><span class="sxs-lookup"><span data-stu-id="90d94-122">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="90d94-123">IdentityServer z dodatkową <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodą pomocniczą, która konfiguruje niektóre domyślne konwencje ASP.NET Core w oparciu o IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="90d94-123">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="90d94-124">Uwierzytelnianie za pomocą dodatkowej <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metody pomocnika, która konfiguruje aplikację do weryfikowania tokenów JWT utworzonych przez IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="90d94-124">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="90d94-125">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="90d94-125">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="90d94-126">Oprogramowanie pośredniczące uwierzytelniania odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:</span><span class="sxs-lookup"><span data-stu-id="90d94-126">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="90d94-127">Oprogramowanie pośredniczące IdentityServer, które uwidacznia punkty końcowe połączenia Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="90d94-127">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="90d94-128">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="90d94-128">AddApiAuthorization</span></span>

<span data-ttu-id="90d94-129">Metoda <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnika konfiguruje [IdentityServer](https://identityserver.io/) dla scenariuszy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="90d94-129">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="90d94-130">IdentityServer to zaawansowane i rozszerzalne środowisko do obsługi zagadnień związanych z zabezpieczeniami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-130">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="90d94-131">IdentityServer ujawnia niepotrzebną złożoność w najbardziej typowych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="90d94-131">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="90d94-132">W związku z tym zestaw Konwencji i opcji konfiguracji jest dostępny, ponieważ rozważamy dobry punkt wyjścia.</span><span class="sxs-lookup"><span data-stu-id="90d94-132">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="90d94-133">Gdy uwierzytelnianie będzie wymagało zmiany, pełne możliwości IdentityServer są nadal dostępne, aby dostosować uwierzytelnianie zgodnie z wymaganiami aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-133">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="90d94-134">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="90d94-134">AddIdentityServerJwt</span></span>

<span data-ttu-id="90d94-135">Metoda <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="90d94-135">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="90d94-136">Zasady są skonfigurowane tak, aby zezwalać na tożsamość do obsługi wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej `/Identity`w obszarze adresu URL tożsamości.</span><span class="sxs-lookup"><span data-stu-id="90d94-136">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="90d94-137"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Obsługuje wszystkie inne żądania.</span><span class="sxs-lookup"><span data-stu-id="90d94-137">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="90d94-138">Ponadto ta metoda:</span><span class="sxs-lookup"><span data-stu-id="90d94-138">Additionally, this method:</span></span>

* <span data-ttu-id="90d94-139">Rejestruje zasób `{APPLICATION NAME}API` interfejsu API z IdentityServer z domyślnym zakresem `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="90d94-139">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="90d94-140">Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez IdentityServer dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-140">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="90d94-141">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="90d94-141">WeatherForecastController</span></span>

<span data-ttu-id="90d94-142">W `WeatherForecastController` (*controllers/WeatherForecastController. cs*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy.</span><span class="sxs-lookup"><span data-stu-id="90d94-142">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="90d94-143">Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="90d94-143">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="90d94-144">Domyślne zasady autoryzacji są skonfigurowane tak, aby korzystały z domyślnego schematu uwierzytelniania, który jest skonfigurowany <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> zgodnie ze schematem zasad wymienionym wcześniej.</span><span class="sxs-lookup"><span data-stu-id="90d94-144">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="90d94-145">Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-145">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="90d94-146">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="90d94-146">ApplicationDbContext</span></span>

<span data-ttu-id="90d94-147">W programie `ApplicationDbContext` (*Data/ApplicationDbContext. cs*) ta sama <xref:Microsoft.EntityFrameworkCore.DbContext> wartość jest używana w tożsamości z wyjątkiem, który rozszerza <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> , aby uwzględnić schemat dla IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="90d94-147">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="90d94-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi od <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="90d94-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="90d94-149">Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych klas <xref:Microsoft.EntityFrameworkCore.DbContext> tożsamości i skonfiguruj kontekst, aby uwzględnić schemat tożsamości przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="90d94-149">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="90d94-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="90d94-150">OidcConfigurationController</span></span>

<span data-ttu-id="90d94-151">W `OidcConfigurationController` (*controllers/OidcConfigurationController. cs*) punkt końcowy klienta jest inicjowany do obsługi parametrów OIDC.</span><span class="sxs-lookup"><span data-stu-id="90d94-151">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="90d94-152">Pliki ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="90d94-152">App settings files</span></span>

<span data-ttu-id="90d94-153">W sekcji plik ustawień aplikacji (*appSettings. JSON*) w katalogu głównym projektu znajduje `IdentityServer` się opis listy skonfigurowanych klientów.</span><span class="sxs-lookup"><span data-stu-id="90d94-153">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="90d94-154">W poniższym przykładzie istnieje pojedynczy klient.</span><span class="sxs-lookup"><span data-stu-id="90d94-154">In the following example, there's a single client.</span></span> <span data-ttu-id="90d94-155">Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do parametru OAuth `ClientId` .</span><span class="sxs-lookup"><span data-stu-id="90d94-155">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="90d94-156">Profil wskazuje konfigurowany typ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-156">The profile indicates the app type being configured.</span></span> <span data-ttu-id="90d94-157">Profil jest używany wewnętrznie w celu napędu Konwencji upraszczających proces konfiguracji serwera.</span><span class="sxs-lookup"><span data-stu-id="90d94-157">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="90d94-158">W pliku ustawień aplikacji środowiska programistycznego (*appSettings. Development. JSON*) w katalogu głównym projektu `IdentityServer` sekcja opisuje klucz używany do podpisywania tokenów.</span><span class="sxs-lookup"><span data-stu-id="90d94-158">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="90d94-159">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="90d94-159">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="90d94-160">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="90d94-160">Authentication package</span></span>

<span data-ttu-id="90d94-161">Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników (`Individual`), aplikacja automatycznie otrzymuje odwołanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do pakietu w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-161">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="90d94-162">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="90d94-162">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="90d94-163">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="90d94-163">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="90d94-164">Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="90d94-164">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="90d94-165">Obsługa autoryzacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="90d94-165">API authorization support</span></span>

<span data-ttu-id="90d94-166">Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiecie.</span><span class="sxs-lookup"><span data-stu-id="90d94-166">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="90d94-167">Ta metoda konfiguruje wszystkie usługi, które są konieczne, aby aplikacja mogła współdziałać z istniejącym systemem autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-167">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="90d94-168">Domyślnie ładuje ona konfigurację aplikacji według Konwencji z programu `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="90d94-168">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="90d94-169">Zgodnie z Konwencją identyfikator klienta jest ustawiany na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="90d94-170">Ten adres URL można zmienić tak, aby wskazywał osobny punkt końcowy przez wywołanie przeciążenia z opcjami.</span><span class="sxs-lookup"><span data-stu-id="90d94-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="90d94-171">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="90d94-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="90d94-172">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="90d94-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="90d94-173">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="90d94-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="90d94-174">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="90d94-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="90d94-175">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="90d94-175">LoginDisplay component</span></span>

<span data-ttu-id="90d94-176">Składnik (*Shared/LoginDisplay. Razor* `MainLayout` ) jest renderowany w składniku (*Shared/MainLayout. Razor*) i zarządza następującymi zachowaniami: `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="90d94-176">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="90d94-177">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="90d94-177">For authenticated users:</span></span>
  * <span data-ttu-id="90d94-178">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="90d94-178">Displays the current user name.</span></span>
  * <span data-ttu-id="90d94-179">Oferuje link do strony profilu użytkownika w ASP.NET Core tożsamość.</span><span class="sxs-lookup"><span data-stu-id="90d94-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="90d94-180">Oferuje przycisk umożliwiający wylogowanie się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90d94-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="90d94-181">Dla użytkowników anonimowych:</span><span class="sxs-lookup"><span data-stu-id="90d94-181">For anonymous users:</span></span>
  * <span data-ttu-id="90d94-182">Oferuje opcję rejestracji.</span><span class="sxs-lookup"><span data-stu-id="90d94-182">Offers the option to register.</span></span>
  * <span data-ttu-id="90d94-183">Oferuje opcję logowania.</span><span class="sxs-lookup"><span data-stu-id="90d94-183">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="90d94-184">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="90d94-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="90d94-185">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="90d94-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="90d94-186">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="90d94-186">Run the app</span></span>

<span data-ttu-id="90d94-187">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="90d94-187">Run the app from the Server project.</span></span> <span data-ttu-id="90d94-188">W przypadku korzystania z programu Visual Studio wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="90d94-188">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="90d94-189">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="90d94-189">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>

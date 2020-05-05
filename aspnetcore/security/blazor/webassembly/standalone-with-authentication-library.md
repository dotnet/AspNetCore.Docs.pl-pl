---
title: Zabezpieczanie aplikacji Blazor autonomicznej ASP.NET Core webassembly przy użyciu biblioteki uwierzytelniania
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 6907a1213a6a9089e2aed885093c2fd38f972ad0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768055"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="7e769-102">Zabezpieczanie aplikacji Blazor autonomicznej ASP.NET Core webassembly przy użyciu biblioteki uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="7e769-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="7e769-103">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7e769-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="7e769-104">*W przypadku Azure Active Directory (AAD) i Azure Active Directory B2C (AAD B2C) nie postępuj zgodnie ze wskazówkami w tym temacie. Zobacz tematy dotyczące usługi AAD i AAD B2C w tym węźle spisu treści.*</span><span class="sxs-lookup"><span data-stu-id="7e769-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="7e769-105">Aby utworzyć Blazor autonomiczną aplikację webassembly, która `Microsoft.AspNetCore.Components.WebAssembly.Authentication` używa biblioteki, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="7e769-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="7e769-106">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="7e769-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7e769-107">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="7e769-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="7e769-108">W programie Visual Studio [Utwórz Blazor aplikację webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="7e769-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="7e769-109">Skonfiguruj **uwierzytelnianie** na **poszczególnych kontach użytkowników** , korzystając z opcji **Zapisz konta użytkowników w aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="7e769-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="7e769-110">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="7e769-110">Authentication package</span></span>

<span data-ttu-id="7e769-111">Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników, aplikacja automatycznie otrzymuje odwołanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do pakietu w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7e769-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="7e769-112">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="7e769-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7e769-113">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7e769-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="7e769-114">Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="7e769-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="7e769-115">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="7e769-115">Authentication service support</span></span>

<span data-ttu-id="7e769-116">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu metody `AddOidcAuthentication` rozszerzenia dostarczonej przez `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiet.</span><span class="sxs-lookup"><span data-stu-id="7e769-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="7e769-117">Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="7e769-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7e769-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7e769-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="7e769-119">Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7e769-119">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="7e769-120">Obsługa uwierzytelniania dla aplikacji autonomicznych jest oferowana przy użyciu polecenia Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="7e769-120">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="7e769-121">`AddOidcAuthentication` Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelniania aplikacji przy użyciu OIDC.</span><span class="sxs-lookup"><span data-stu-id="7e769-121">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="7e769-122">Wartości wymagane do skonfigurowania aplikacji można uzyskać z adresu IP zgodnego z OIDC.</span><span class="sxs-lookup"><span data-stu-id="7e769-122">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="7e769-123">Uzyskaj wartości podczas rejestrowania aplikacji, która zwykle odbywa się w portalu online.</span><span class="sxs-lookup"><span data-stu-id="7e769-123">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="7e769-124">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="7e769-124">Access token scopes</span></span>

<span data-ttu-id="7e769-125">Blazor Szablon webassembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7e769-125">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="7e769-126">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów `OidcProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="7e769-126">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="7e769-127">Jeśli Azure Portal udostępnia identyfikator URI zakresu, a **aplikacja zgłasza nieobsłużony wyjątek** , gdy odbierze *401 nieautoryzowaną* odpowiedź z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="7e769-127">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="7e769-128">Na przykład Azure Portal może podać jeden z następujących formatów identyfikatorów URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="7e769-128">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="7e769-129">Podaj identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="7e769-129">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="7e769-130">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="7e769-130">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7e769-131">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="7e769-131">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7e769-132">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="7e769-132">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="7e769-133">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="7e769-133">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="7e769-134">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="7e769-134">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="7e769-135">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="7e769-135">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="7e769-136">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7e769-136">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="7e769-137">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7e769-137">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="7e769-138">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="7e769-138">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7e769-139">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7e769-139">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>

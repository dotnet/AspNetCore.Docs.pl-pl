---
title: Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą biblioteki uwierzytelniania
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977044"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="75e66-102">Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą biblioteki uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="75e66-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="75e66-103">Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="75e66-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="75e66-104">*W przypadku usługi Azure Active Directory (AAD) i usługi Azure Active Directory B2C (AAD B2C) nie postępuj zgodnie ze wskazówkami zawartymi w tym temacie. Zobacz tematy AAD i AAD B2C w tym węźle spisu treści.*</span><span class="sxs-lookup"><span data-stu-id="75e66-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="75e66-105">Aby utworzyć Blazor autonomiczną aplikację WebAssembly, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` która korzysta z biblioteki, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="75e66-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="75e66-106">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="75e66-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="75e66-107">Nazwa folderu również staje się częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="75e66-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="75e66-108">W programie Visual Studio [utwórz aplikację Blazor WebAssembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="75e66-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="75e66-109">Ustaw **uwierzytelnianie** na **indywidualne konta użytkowników** za pomocą opcji Konta użytkowników Sklepu w **aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="75e66-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="75e66-110">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="75e66-110">Authentication package</span></span>

<span data-ttu-id="75e66-111">Po utworzeniu aplikacji do korzystania z kont poszczególnych użytkowników aplikacja `Microsoft.AspNetCore.Components.WebAssembly.Authentication` automatycznie odbiera odwołanie do pakietu w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="75e66-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="75e66-112">Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="75e66-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="75e66-113">Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="75e66-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="75e66-114">Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.</span><span class="sxs-lookup"><span data-stu-id="75e66-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="75e66-115">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="75e66-115">Authentication service support</span></span>

<span data-ttu-id="75e66-116">Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddOidcAuthentication` kontenerze usługi `Microsoft.AspNetCore.Components.WebAssembly.Authentication` przy użyciu metody rozszerzenia dostarczonej przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="75e66-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="75e66-117">Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).</span><span class="sxs-lookup"><span data-stu-id="75e66-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="75e66-118">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="75e66-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="75e66-119">Obsługa uwierzytelniania dla autonomicznych aplikacji jest oferowana przy użyciu funkcji Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="75e66-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="75e66-120">Metoda `AddOidcAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji przy użyciu funkcji OIDC.</span><span class="sxs-lookup"><span data-stu-id="75e66-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="75e66-121">Wartości wymagane do skonfigurowania aplikacji można uzyskać z adresu IP zgodnego ze standardem OIDC.</span><span class="sxs-lookup"><span data-stu-id="75e66-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="75e66-122">Uzyskaj wartości podczas rejestracji aplikacji, która zazwyczaj występuje w ich portalu online.</span><span class="sxs-lookup"><span data-stu-id="75e66-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="75e66-123">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="75e66-123">Access token scopes</span></span>

<span data-ttu-id="75e66-124">Szablon Blazor WebAssembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="75e66-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="75e66-125">Aby aprowizować token jako część przepływu logowania, dodaj zakres do `OidcProviderOptions`domyślnych zakresów tokenu:</span><span class="sxs-lookup"><span data-stu-id="75e66-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="75e66-126">Jeśli portal Azure udostępnia identyfikator URI zakresu i **aplikacja zgłasza nieobsługiwanie wyjątek** po otrzymaniu *401 nieautoryzowanej* odpowiedzi z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="75e66-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="75e66-127">Na przykład witryny Azure portal może zapewnić jeden z następujących formatów identyfikatora URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="75e66-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="75e66-128">Podać identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="75e66-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="75e66-129">Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="75e66-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="75e66-130">Import pliku</span><span class="sxs-lookup"><span data-stu-id="75e66-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="75e66-131">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="75e66-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="75e66-132">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="75e66-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="75e66-133">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="75e66-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="75e66-134">Składnik LogowanieWydajnik</span><span class="sxs-lookup"><span data-stu-id="75e66-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="75e66-135">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="75e66-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="75e66-136">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="75e66-136">Additional resources</span></span>

* [<span data-ttu-id="75e66-137">Żądanie dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="75e66-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)

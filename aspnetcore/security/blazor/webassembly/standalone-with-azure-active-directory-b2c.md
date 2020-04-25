---
title: Zabezpiecz ASP.NET Core Blazor autonomiczną aplikację webassembly z Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 4ccf86550a520f1d001088859ef5909041178781
ms.sourcegitcommit: 6d271f4b4c3cd1e82267f51d9bfb6de221c394fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150001"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="a6081-102">Zabezpiecz ASP.NET Core Blazor autonomiczną aplikację webassembly z Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="a6081-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="a6081-103">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a6081-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="a6081-104">Aby utworzyć Blazor autonomiczną aplikację webassembly, która używa usługi [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="a6081-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="a6081-105">Postępuj zgodnie ze wskazówkami w poniższych tematach, aby utworzyć dzierżawę i zarejestrować aplikację sieci Web w witrynie Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a6081-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="a6081-106">[Utwórz AAD B2C dzierżawy](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="a6081-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="a6081-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="a6081-107">1\.</span></span> <span data-ttu-id="a6081-108">Wystąpienie AAD B2C (na przykład `https://contoso.b2clogin.com/`, które obejmuje końcowy ukośnik)</span><span class="sxs-lookup"><span data-stu-id="a6081-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="a6081-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="a6081-109">2\.</span></span> <span data-ttu-id="a6081-110">AAD B2C domeny dzierżawy (na przykład `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="a6081-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="a6081-111">[Zarejestruj aplikację](/azure/active-directory-b2c/tutorial-register-applications) &ndash; sieci Web, podczas rejestracji aplikacji należy wybrać następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="a6081-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="a6081-112">1 \.</span><span class="sxs-lookup"><span data-stu-id="a6081-112">1\.</span></span> <span data-ttu-id="a6081-113">Ustaw wartość **tak dla opcji** **Web App/Web API** .</span><span class="sxs-lookup"><span data-stu-id="a6081-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="a6081-114">2 \.</span><span class="sxs-lookup"><span data-stu-id="a6081-114">2\.</span></span> <span data-ttu-id="a6081-115">Ustaw opcję **Zezwalaj na niejawny przepływ** na **wartość tak**.</span><span class="sxs-lookup"><span data-stu-id="a6081-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="a6081-116">3 \.</span><span class="sxs-lookup"><span data-stu-id="a6081-116">3\.</span></span> <span data-ttu-id="a6081-117">Dodaj **adres URL odpowiedzi** `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="a6081-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="a6081-118">Zapisz identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="a6081-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="a6081-119">[Tworzenie przepływów](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; użytkowników Utwórz proces tworzenia konta i logowania.</span><span class="sxs-lookup"><span data-stu-id="a6081-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="a6081-120">Aby wypełnić `context.User.Identity.Name`  >  `LoginDisplay` składnik (*Shared/LoginDisplay. Razor*), wybierz co najmniej atrybut użytkownika**Nazwa wyświetlana** **oświadczenia aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="a6081-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="a6081-121">Zapisz nazwę nowego przepływu logowania i logowania utworzonego dla aplikacji (na przykład `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="a6081-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="a6081-122">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="a6081-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="a6081-123">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="a6081-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a6081-124">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="a6081-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a6081-125">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a6081-125">Authentication package</span></span>

<span data-ttu-id="a6081-126">Gdy aplikacja zostanie utworzona w celu korzystania z pojedynczego konta B2C (`IndividualB2C`), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="a6081-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a6081-127">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="a6081-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a6081-128">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a6081-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="a6081-129">Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="a6081-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="a6081-130">Pakiet `Microsoft.Authentication.WebAssembly.Msal` zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6081-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a6081-131">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a6081-131">Authentication service support</span></span>

<span data-ttu-id="a6081-132">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu metody `AddMsalAuthentication` rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet.</span><span class="sxs-lookup"><span data-stu-id="a6081-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a6081-133">Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z dostawcą tożsamości (IP).</span><span class="sxs-lookup"><span data-stu-id="a6081-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a6081-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6081-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="a6081-135">`AddMsalAuthentication` Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6081-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a6081-136">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji kont Microsoft podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6081-136">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="a6081-137">Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a6081-137">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="a6081-138">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a6081-138">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="a6081-139">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="a6081-139">Access token scopes</span></span>

<span data-ttu-id="a6081-140">Blazor Szablon webassembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="a6081-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a6081-141">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="a6081-141">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a6081-142">Jeśli Azure Portal udostępnia identyfikator URI zakresu, a **aplikacja zgłasza nieobsłużony wyjątek** , gdy odbierze *401 nieautoryzowaną* odpowiedź z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="a6081-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a6081-143">Na przykład Azure Portal może podać jeden z następujących formatów identyfikatorów URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="a6081-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a6081-144">Podaj identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="a6081-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="a6081-145">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="a6081-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a6081-146">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="a6081-146">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a6081-147">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="a6081-147">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="a6081-148">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="a6081-148">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a6081-149">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="a6081-149">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a6081-150">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="a6081-150">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a6081-151">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a6081-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a6081-152">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="a6081-152">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a6081-153">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a6081-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a6081-154">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a6081-154">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="a6081-155">Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="a6081-155">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="a6081-156">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="a6081-156">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

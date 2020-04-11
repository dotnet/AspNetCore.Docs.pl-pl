---
title: Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą usługi Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 96e39a4c975a65fd11776f774fb1799acab525b9
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123449"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="a953c-102">Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="a953c-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="a953c-103">Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a953c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="a953c-104">Aby utworzyć Blazor autonomiczną aplikację WebAssembly, która używa [usługi Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="a953c-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="a953c-105">Postępuj zgodnie ze wskazówkami w następujących tematach, aby utworzyć dzierżawę i zarejestrować aplikację sieci web w witrynie Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a953c-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="a953c-106">&ndash; [Utwórz dzierżawę usługi AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) Rejestr następujących informacji:</span><span class="sxs-lookup"><span data-stu-id="a953c-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="a953c-107">1\.</span><span class="sxs-lookup"><span data-stu-id="a953c-107">1\.</span></span> <span data-ttu-id="a953c-108">AAD B2C (na `https://contoso.b2clogin.com/`przykład , który zawiera ukośnik)</span><span class="sxs-lookup"><span data-stu-id="a953c-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="a953c-109">2\.</span><span class="sxs-lookup"><span data-stu-id="a953c-109">2\.</span></span> <span data-ttu-id="a953c-110">Domena dzierżawy AAD B2C (na `contoso.onmicrosoft.com`przykład)</span><span class="sxs-lookup"><span data-stu-id="a953c-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="a953c-111">[Zarejestruj aplikację](/azure/active-directory-b2c/tutorial-register-applications) &ndash; internetową Dokonaj następujących wyborów podczas rejestracji aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a953c-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="a953c-112">1\.</span><span class="sxs-lookup"><span data-stu-id="a953c-112">1\.</span></span> <span data-ttu-id="a953c-113">Ustaw **interfejs API aplikacji sieci Web / sieci Web** na **Tak**.</span><span class="sxs-lookup"><span data-stu-id="a953c-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="a953c-114">2\.</span><span class="sxs-lookup"><span data-stu-id="a953c-114">2\.</span></span> <span data-ttu-id="a953c-115">Ustaw **zezwalaj na przepływ niejawny** tak . **Yes**</span><span class="sxs-lookup"><span data-stu-id="a953c-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="a953c-116">3\.</span><span class="sxs-lookup"><span data-stu-id="a953c-116">3\.</span></span> <span data-ttu-id="a953c-117">Dodaj **adres** URL `https://localhost:5001/authentication/login-callback`odpowiedzi .</span><span class="sxs-lookup"><span data-stu-id="a953c-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="a953c-118">Zarejestruj identyfikator aplikacji (identyfikator klienta) (na `11111111-1111-1111-1111-111111111111`przykład ).</span><span class="sxs-lookup"><span data-stu-id="a953c-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="a953c-119">[Tworzenie przepływów](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; użytkowników Tworzenie przepływu użytkownika rejestracji i logowania.</span><span class="sxs-lookup"><span data-stu-id="a953c-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="a953c-120">Co najmniej wybierz atrybut Użytkownika Nazwa > **wyświetlana** **oświadczeń aplikacji,** `LoginDisplay` aby wypełnić `context.User.Identity.Name` składnik (*Shared/LoginDisplay.brzytwa*).</span><span class="sxs-lookup"><span data-stu-id="a953c-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="a953c-121">Nagraj nazwę przepływu użytkownika rejestracji i logowania utworzoną `B2C_1_signupsignin`dla aplikacji (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="a953c-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="a953c-122">Zastąp symbole zastępcze w następującym poleceniu informacjami zarejestrowanymi wcześniej i wykonaj polecenie w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="a953c-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="a953c-123">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="a953c-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a953c-124">Nazwa folderu również staje się częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="a953c-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a953c-125">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a953c-125">Authentication package</span></span>

<span data-ttu-id="a953c-126">Po utworzeniu aplikacji do korzystania z indywidualnego konta B2C (`IndividualB2C`aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania Firmy Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="a953c-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a953c-127">Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="a953c-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a953c-128">Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a953c-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="a953c-129">Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.</span><span class="sxs-lookup"><span data-stu-id="a953c-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="a953c-130">Pakiet `Microsoft.Authentication.WebAssembly.Msal` przechodnie dodaje `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiet do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a953c-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a953c-131">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a953c-131">Authentication service support</span></span>

<span data-ttu-id="a953c-132">Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddMsalAuthentication` kontenerze usługi `Microsoft.Authentication.WebAssembly.Msal` przy użyciu metody rozszerzenia dostarczonej przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="a953c-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a953c-133">Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).</span><span class="sxs-lookup"><span data-stu-id="a953c-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a953c-134">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="a953c-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="a953c-135">Metoda `AddMsalAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a953c-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a953c-136">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD usługi Azure Portal podczas rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a953c-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="a953c-137">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="a953c-137">Access token scopes</span></span>

<span data-ttu-id="a953c-138">Szablon Blazor WebAssembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="a953c-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a953c-139">Aby aprowizować token jako część przepływu logowania, dodaj zakres do domyślnych zakresów tokenu `MsalProviderOptions`dostępu:</span><span class="sxs-lookup"><span data-stu-id="a953c-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a953c-140">Jeśli portal Azure udostępnia identyfikator URI zakresu i **aplikacja zgłasza nieobsługiwanie wyjątek** po otrzymaniu *401 nieautoryzowanej* odpowiedzi z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="a953c-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a953c-141">Na przykład witryny Azure portal może zapewnić jeden z następujących formatów identyfikatora URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="a953c-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a953c-142">Podać identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="a953c-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="a953c-143">Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="a953c-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="a953c-144">Import pliku</span><span class="sxs-lookup"><span data-stu-id="a953c-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a953c-145">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="a953c-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a953c-146">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="a953c-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a953c-147">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a953c-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a953c-148">Składnik LogowanieWydajnik</span><span class="sxs-lookup"><span data-stu-id="a953c-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a953c-149">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a953c-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a953c-150">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a953c-150">Additional resources</span></span>

* [<span data-ttu-id="a953c-151">Żądanie dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="a953c-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="a953c-152">Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="a953c-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="a953c-153">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="a953c-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

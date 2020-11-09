---
title: 'Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej za pomocą Azure Active Directory'
author: guardrex
description: 'Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację autonomiczną ASP.NET Core przy użyciu Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 4e8c22c56b7023301499fd273a9194b8c7b58f3d
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343716"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="052f3-103">Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej za pomocą Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="052f3-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="052f3-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="052f3-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="052f3-105">W tym artykule opisano sposób tworzenia [autonomicznej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="052f3-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="052f3-106">W przypadku Blazor WebAssembly aplikacji utworzonych w programie Visual Studio, które są skonfigurowane do obsługi kont w katalogu organizacji usługi AAD, program Visual Studio nie skonfiguruje aplikacji poprawnie na potrzeby generowania projektu.</span><span class="sxs-lookup"><span data-stu-id="052f3-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="052f3-107">Zostanie to rozkierowane w przyszłej wersji programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="052f3-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="052f3-108">W tym artykule pokazano, jak utworzyć aplikację za pomocą polecenia interfejs wiersza polecenia platformy .NET Core `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="052f3-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="052f3-109">Jeśli wolisz utworzyć aplikację za pomocą programu Visual Studio przed zaktualizowaniem IDE dla najnowszych Blazor szablonów w ASP.NET Core 5,0, zapoznaj się z każdą sekcją tego artykułu i Potwierdź lub zaktualizuj konfigurację aplikacji po utworzeniu aplikacji przez program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="052f3-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest Blazor templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

<span data-ttu-id="052f3-110">Zarejestruj aplikację usługi AAD w obszarze **Azure Active Directory** > **rejestracje aplikacji** Azure Active Directory w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="052f3-110">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="052f3-111">Podaj **nazwę** aplikacji (na przykład **Blazor autonomicznej usługi AAD** ).</span><span class="sxs-lookup"><span data-stu-id="052f3-111">Provide a **Name** for the app (for example, **Blazor Standalone AAD** ).</span></span>
1. <span data-ttu-id="052f3-112">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="052f3-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="052f3-113">**W tym katalogu organizacji można wybrać konta tylko** dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="052f3-113">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="052f3-114">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="052f3-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="052f3-115">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="052f3-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="052f3-116">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052f3-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="052f3-117">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="052f3-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="052f3-118">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="052f3-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="052f3-119">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="052f3-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="052f3-120">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="052f3-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="052f3-121">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="052f3-121">Select **Register**.</span></span>

<span data-ttu-id="052f3-122">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="052f3-122">Record the following information:</span></span>

* <span data-ttu-id="052f3-123">Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="052f3-123">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="052f3-124">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="052f3-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="052f3-125">W **Authentication** obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="052f3-125">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="052f3-126">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="052f3-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="052f3-127">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="052f3-127">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="052f3-128">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="052f3-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="052f3-129">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="052f3-129">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="052f3-130">Zarejestruj aplikację usługi AAD w obszarze **Azure Active Directory** > **rejestracje aplikacji** Azure Active Directory w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="052f3-130">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="052f3-131">Podaj **nazwę** aplikacji (na przykład **Blazor autonomicznej usługi AAD** ).</span><span class="sxs-lookup"><span data-stu-id="052f3-131">Provide a **Name** for the app (for example, **Blazor Standalone AAD** ).</span></span>
1. <span data-ttu-id="052f3-132">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="052f3-132">Choose a **Supported account types**.</span></span> <span data-ttu-id="052f3-133">**W tym katalogu organizacji można wybrać konta tylko** dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="052f3-133">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="052f3-134">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="052f3-134">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="052f3-135">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="052f3-135">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="052f3-136">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052f3-136">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="052f3-137">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="052f3-137">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="052f3-138">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="052f3-138">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="052f3-139">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="052f3-139">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="052f3-140">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="052f3-140">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="052f3-141">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="052f3-141">Select **Register**.</span></span>

<span data-ttu-id="052f3-142">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="052f3-142">Record the following information:</span></span>

* <span data-ttu-id="052f3-143">Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="052f3-143">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="052f3-144">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="052f3-144">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="052f3-145">W **Authentication** obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="052f3-145">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="052f3-146">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="052f3-146">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="052f3-147">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="052f3-147">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="052f3-148">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="052f3-148">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="052f3-149">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="052f3-149">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="052f3-150">Utwórz aplikację w pustym folderze.</span><span class="sxs-lookup"><span data-stu-id="052f3-150">Create the app in an empty folder.</span></span> <span data-ttu-id="052f3-151">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="052f3-151">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="052f3-152">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="052f3-152">Placeholder</span></span>   | <span data-ttu-id="052f3-153">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="052f3-153">Azure portal name</span></span>       | <span data-ttu-id="052f3-154">Przykład</span><span class="sxs-lookup"><span data-stu-id="052f3-154">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="052f3-155">Identyfikator aplikacji (klienta)</span><span class="sxs-lookup"><span data-stu-id="052f3-155">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="052f3-156">Identyfikator katalogu (dzierżawcy)</span><span class="sxs-lookup"><span data-stu-id="052f3-156">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="052f3-157">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052f3-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="052f3-158">W Azure Portal **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="052f3-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="052f3-159">Jeśli aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="052f3-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="052f3-160">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu aplikacji, Wróć do rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="052f3-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="052f3-161">Po utworzeniu aplikacji powinno być możliwe:</span><span class="sxs-lookup"><span data-stu-id="052f3-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="052f3-162">Zaloguj się do aplikacji przy użyciu konta użytkownika usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="052f3-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="052f3-163">Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="052f3-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="052f3-164">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="052f3-164">For more information, see:</span></span>
  * [<span data-ttu-id="052f3-165">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="052f3-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="052f3-166">[Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="052f3-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="052f3-167">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="052f3-167">Authentication package</span></span>

<span data-ttu-id="052f3-168">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="052f3-168">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="052f3-169">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="052f3-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="052f3-170">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="052f3-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="052f3-171">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="052f3-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="052f3-172">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052f3-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="052f3-173">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="052f3-173">Authentication service support</span></span>

<span data-ttu-id="052f3-174">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="052f3-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="052f3-175">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="052f3-175">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="052f3-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="052f3-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="052f3-177"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052f3-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="052f3-178">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="052f3-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="052f3-179">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="052f3-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="052f3-180">Przykład:</span><span class="sxs-lookup"><span data-stu-id="052f3-180">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="052f3-181">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="052f3-181">Access token scopes</span></span>

<span data-ttu-id="052f3-182">Blazor WebAssemblySzablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="052f3-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="052f3-183">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="052f3-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="052f3-184">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="052f3-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="052f3-185">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="052f3-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="052f3-186">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="052f3-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="052f3-187">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="052f3-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="052f3-188">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="052f3-188">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="052f3-189">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="052f3-189">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="052f3-190">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="052f3-190">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="052f3-191">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="052f3-191">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="052f3-192">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="052f3-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="052f3-193">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="052f3-193">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="052f3-194">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="052f3-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="052f3-195">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="052f3-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="052f3-196">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="052f3-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="052f3-197">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="052f3-197">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

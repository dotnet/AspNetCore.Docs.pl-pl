---
title: Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej za pomocą Azure Active Directory
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację autonomiczną ASP.NET Core przy użyciu Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 21d6e6fd9859cf4daf28e0bc3cf70562192844dd
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280927"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="00e48-103">Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej za pomocą Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="00e48-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="00e48-104">W tym artykule opisano sposób tworzenia [autonomicznej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="00e48-104">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="00e48-105">W przypadku Blazor WebAssembly aplikacji utworzonych w programie Visual Studio, które są skonfigurowane do obsługi kont w katalogu organizacji usługi AAD, program Visual Studio nie konfiguruje obecnie Azure Portal rejestracji aplikacji podczas generowania projektu.</span><span class="sxs-lookup"><span data-stu-id="00e48-105">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="00e48-106">Zostanie to rozkierowane w przyszłej wersji programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="00e48-106">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="00e48-107">W tym artykule pokazano, jak utworzyć rozwiązanie i rejestrację portalu aplikacji Azure przy użyciu interfejsu wiersza polecenia platformy .NET `dotnet new` oraz ręcznie utworzyć rejestrację aplikacji w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="00e48-107">This article shows how to create the solution and Azure app portal registration with the .NET CLI `dotnet new` command and by manually creating the app registration in the Azure portal.</span></span>
>
> <span data-ttu-id="00e48-108">Jeśli wolisz utworzyć rozwiązanie i rejestrację aplikacji platformy Azure w programie Visual Studio przed zaktualizowaniem środowiska IDE, zapoznaj się z **_każdą sekcją tego artykułu_** i Potwierdź lub zaktualizuj konfigurację aplikacji oraz rejestrację aplikacji po utworzeniu rozwiązania przez program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="00e48-108">If you prefer to create the solution and Azure app registration with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the app's configuration and the app's registration after Visual Studio creates the solution.</span></span>

<span data-ttu-id="00e48-109">Zarejestruj aplikację usługi AAD w obszarze  > **rejestracje aplikacji** Azure Active Directory w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="00e48-109">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="00e48-110">Podaj **nazwę** aplikacji (na przykład **Blazor autonomicznej usługi AAD**).</span><span class="sxs-lookup"><span data-stu-id="00e48-110">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="00e48-111">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="00e48-111">Choose a **Supported account types**.</span></span> <span data-ttu-id="00e48-112">**W tym katalogu organizacji można wybrać konta tylko** dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="00e48-112">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="00e48-113">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="00e48-113">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="00e48-114">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="00e48-114">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="00e48-115">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e48-115">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="00e48-116">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="00e48-116">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="00e48-117">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="00e48-117">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="00e48-118">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="00e48-118">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="00e48-119">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="00e48-119">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="00e48-120">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="00e48-120">Select **Register**.</span></span>

<span data-ttu-id="00e48-121">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="00e48-121">Record the following information:</span></span>

* <span data-ttu-id="00e48-122">Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="00e48-122">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="00e48-123">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="00e48-123">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="00e48-124">W  obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="00e48-124">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="00e48-125">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="00e48-125">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="00e48-126">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="00e48-126">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="00e48-127">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="00e48-127">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="00e48-128">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="00e48-128">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="00e48-129">Zarejestruj aplikację usługi AAD w obszarze  > **rejestracje aplikacji** Azure Active Directory w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="00e48-129">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="00e48-130">Podaj **nazwę** aplikacji (na przykład **Blazor autonomicznej usługi AAD**).</span><span class="sxs-lookup"><span data-stu-id="00e48-130">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="00e48-131">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="00e48-131">Choose a **Supported account types**.</span></span> <span data-ttu-id="00e48-132">**W tym katalogu organizacji można wybrać konta tylko** dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="00e48-132">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="00e48-133">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="00e48-133">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="00e48-134">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="00e48-134">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="00e48-135">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e48-135">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="00e48-136">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="00e48-136">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="00e48-137">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="00e48-137">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="00e48-138">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="00e48-138">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="00e48-139">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="00e48-139">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="00e48-140">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="00e48-140">Select **Register**.</span></span>

<span data-ttu-id="00e48-141">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="00e48-141">Record the following information:</span></span>

* <span data-ttu-id="00e48-142">Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="00e48-142">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="00e48-143">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="00e48-143">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="00e48-144">W  obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="00e48-144">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="00e48-145">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="00e48-145">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="00e48-146">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="00e48-146">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="00e48-147">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="00e48-147">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="00e48-148">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="00e48-148">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="00e48-149">Utwórz aplikację w pustym folderze.</span><span class="sxs-lookup"><span data-stu-id="00e48-149">Create the app in an empty folder.</span></span> <span data-ttu-id="00e48-150">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="00e48-150">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="00e48-151">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="00e48-151">Placeholder</span></span>   | <span data-ttu-id="00e48-152">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="00e48-152">Azure portal name</span></span>       | <span data-ttu-id="00e48-153">Przykład</span><span class="sxs-lookup"><span data-stu-id="00e48-153">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="00e48-154">Identyfikator aplikacji (klienta)</span><span class="sxs-lookup"><span data-stu-id="00e48-154">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="00e48-155">Identyfikator katalogu (dzierżawcy)</span><span class="sxs-lookup"><span data-stu-id="00e48-155">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="00e48-156">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e48-156">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="00e48-157">W Azure Portal **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="00e48-157">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="00e48-158">Jeśli aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="00e48-158">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="00e48-159">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu aplikacji, Wróć do rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="00e48-159">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="00e48-160">Po utworzeniu aplikacji powinno być możliwe:</span><span class="sxs-lookup"><span data-stu-id="00e48-160">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="00e48-161">Zaloguj się do aplikacji przy użyciu konta użytkownika usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="00e48-161">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="00e48-162">Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="00e48-162">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="00e48-163">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="00e48-163">For more information, see:</span></span>
  * [<span data-ttu-id="00e48-164">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="00e48-164">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="00e48-165">[Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="00e48-165">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="00e48-166">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="00e48-166">Authentication package</span></span>

<span data-ttu-id="00e48-167">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="00e48-167">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="00e48-168">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="00e48-168">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="00e48-169">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="00e48-169">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="00e48-170">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="00e48-170">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="00e48-171">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e48-171">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="00e48-172">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="00e48-172">Authentication service support</span></span>

<span data-ttu-id="00e48-173">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="00e48-173">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="00e48-174">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="00e48-174">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="00e48-175">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="00e48-175">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="00e48-176"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e48-176">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="00e48-177">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e48-177">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="00e48-178">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="00e48-178">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="00e48-179">Przykład:</span><span class="sxs-lookup"><span data-stu-id="00e48-179">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="00e48-180">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="00e48-180">Access token scopes</span></span>

<span data-ttu-id="00e48-181">Blazor WebAssemblySzablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="00e48-181">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="00e48-182">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="00e48-182">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="00e48-183">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="00e48-183">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="00e48-184">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="00e48-184">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="00e48-185">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="00e48-185">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="00e48-186">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="00e48-186">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="00e48-187">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="00e48-187">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="00e48-188">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="00e48-188">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="00e48-189">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="00e48-189">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="00e48-190">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="00e48-190">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="00e48-191">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="00e48-191">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="00e48-192">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="00e48-192">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="00e48-193">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="00e48-193">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="00e48-194">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="00e48-194">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="00e48-195">Utwórz niestandardową wersję biblioteki MSAL JavaScript.</span><span class="sxs-lookup"><span data-stu-id="00e48-195">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="00e48-196">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="00e48-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="00e48-197">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="00e48-197">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

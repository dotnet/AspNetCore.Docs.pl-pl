---
title: Konfiguracja zewnętrznego logowania do usługi Google w ASP.NET Core
author: rick-anderson
description: W tym samouczku przedstawiono integrację uwierzytelniania użytkownika konta Google z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/18/2021
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
uid: security/authentication/google-logins
ms.openlocfilehash: 181ce87e8085839e0fcc0d77010c588ef7a290b1
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110134"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="0a2f0-103">Konfiguracja zewnętrznego logowania do usługi Google w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a2f0-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="0a2f0-104">Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0a2f0-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0a2f0-105">W tym samouczku pokazano, jak umożliwić użytkownikom logowanie się za pomocą konta Google przy użyciu projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="0a2f0-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="0a2f0-106">Tworzenie projektu konsoli interfejsu API firmy Google i identyfikatora klienta</span><span class="sxs-lookup"><span data-stu-id="0a2f0-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="0a2f0-107">Dodaj pakiet [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-107">Add the [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet package to the app.</span></span>
* <span data-ttu-id="0a2f0-108">Postępuj zgodnie ze wskazówkami zawartymi w temacie [Integrowanie usługi Google Sign-In z aplikacją sieci Web](https://developers.google.com/identity/sign-in/web/sign-in) (dokumentacją Google).</span><span class="sxs-lookup"><span data-stu-id="0a2f0-108">Follow the guidance in [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) (Google documentation).</span></span>
* <span data-ttu-id="0a2f0-109">Na stronie **poświadczenia** w [konsoli Google](https://console.developers.google.com/apis/credentials)wybierz pozycję **Utwórz poświadczenia**  >  **Identyfikator klienta OAuth**.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-109">In the **Credentials** page of the [Google console](https://console.developers.google.com/apis/credentials), select **CREATE CREDENTIALS** > **OAuth client ID**.</span></span>
* <span data-ttu-id="0a2f0-110">W oknie dialogowym **Typ aplikacji** wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-110">In the **Application type** dialog, select **Web application**.</span></span> <span data-ttu-id="0a2f0-111">Podaj **nazwę** aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-111">Provide a **Name** for the app.</span></span>
* <span data-ttu-id="0a2f0-112">W sekcji **autoryzowane adresy URI przekierowania** wybierz pozycję **Dodaj identyfikator URI** , aby ustawić identyfikator URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-112">In the **Authorized redirect URIs** section, select **ADD URI** to set the redirect URI.</span></span> <span data-ttu-id="0a2f0-113">Przykładowy identyfikator URI przekierowania: `https://localhost:{PORT}/signin-google` , gdzie `{PORT}` symbol zastępczy jest portem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-113">Example redirect URI: `https://localhost:{PORT}/signin-google`, where the `{PORT}` placeholder is the app's port.</span></span>
* <span data-ttu-id="0a2f0-114">Wybierz przycisk **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="0a2f0-114">Select the **CREATE** button.</span></span>
* <span data-ttu-id="0a2f0-115">Zapisz **Identyfikator klienta** i **klucz tajny klienta** do użycia w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-115">Save the **Client ID** and **Client Secret** for use in the app's configuration.</span></span>
* <span data-ttu-id="0a2f0-116">Podczas wdrażania lokacji:</span><span class="sxs-lookup"><span data-stu-id="0a2f0-116">When deploying the site, either:</span></span>
  * <span data-ttu-id="0a2f0-117">Zaktualizuj identyfikator URI przekierowania aplikacji w **konsoli Google** do WDROŻONEGO identyfikatora URI przekierowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-117">Update the app's redirect URI in the **Google Console** to the app's deployed redirect URI.</span></span>
  * <span data-ttu-id="0a2f0-118">Utwórz nową rejestrację usługi Google API w **konsoli Google** dla aplikacji produkcyjnej przy użyciu identyfikatora URI przekierowania produkcji.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-118">Create a new Google API registration in the **Google Console** for the production app with its production redirect URI.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="0a2f0-119">Zapisz identyfikator i klucz tajny klienta Google</span><span class="sxs-lookup"><span data-stu-id="0a2f0-119">Store the Google client ID and secret</span></span>

<span data-ttu-id="0a2f0-120">Przechowuj ustawienia poufne, takie jak identyfikator klienta Google i wartości tajne przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="0a2f0-120">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="0a2f0-121">W tym przykładzie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="0a2f0-121">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="0a2f0-122">Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="0a2f0-122">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="0a2f0-123">Zapisz poufne ustawienia w lokalnym magazynie wpisów tajnych przy użyciu kluczy tajnych `Authentication:Google:ClientId` i `Authentication:Google:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="0a2f0-123">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="0a2f0-124">Poświadczenia interfejsu API i użycie można zarządzać w [konsoli interfejsu API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="0a2f0-124">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="0a2f0-125">Skonfiguruj uwierzytelnianie Google</span><span class="sxs-lookup"><span data-stu-id="0a2f0-125">Configure Google authentication</span></span>

<span data-ttu-id="0a2f0-126">Dodaj usługę Google do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0a2f0-126">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="0a2f0-127">Zaloguj się przy użyciu usługi Google</span><span class="sxs-lookup"><span data-stu-id="0a2f0-127">Sign in with Google</span></span>

* <span data-ttu-id="0a2f0-128">Uruchom aplikację i kliknij pozycję **Zaloguj**.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-128">Run the app and click **Log in**.</span></span> <span data-ttu-id="0a2f0-129">Zostanie wyświetlona opcja zalogowania się za pomocą usługi Google.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-129">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="0a2f0-130">Kliknij przycisk **Google** , który przekierowuje do usługi Google w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-130">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="0a2f0-131">Po wprowadzeniu poświadczeń Google nastąpi przekierowanie z powrotem do witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-131">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="0a2f0-132"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Więcej informacji o opcjach konfiguracji obsługiwanych przez uwierzytelnianie Google można znaleźć w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-132">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="0a2f0-133">Może to służyć do żądania różnych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-133">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="0a2f0-134">Zmień domyślny identyfikator URI wywołania zwrotnego</span><span class="sxs-lookup"><span data-stu-id="0a2f0-134">Change the default callback URI</span></span>

<span data-ttu-id="0a2f0-135">Segment identyfikatora URI `/signin-google` jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania Google.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-135">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="0a2f0-136">Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego usługi Google Authentication za pośrednictwem odziedziczonej <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> właściwości <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-136">You can change the default callback URI while configuring the Google authentication middleware via the inherited <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType>) property of the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0a2f0-137">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="0a2f0-137">Troubleshooting</span></span>

* <span data-ttu-id="0a2f0-138">Jeśli logowanie nie działa i nie pojawiają się żadne błędy, przełącz się do trybu deweloperskiego, aby ułatwić debugowanie problemu.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-138">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="0a2f0-139">Jeśli Identity nie jest skonfigurowany przez wywołanie `services.AddIdentity` w `ConfigureServices` , próba uwierzytelnienia wyników w *argumencieexception: należy podać opcję "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-139">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="0a2f0-140">Szablon projektu używany w tym samouczku zapewnia, że jest to gotowe.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-140">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="0a2f0-141">Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, podczas *przetwarzania błędu żądania nie można wykonać operacji bazy danych* .</span><span class="sxs-lookup"><span data-stu-id="0a2f0-141">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="0a2f0-142">Wybierz pozycję **Zastosuj migracje** , aby utworzyć bazę danych, a następnie Odśwież stronę, aby kontynuować z powodu błędu.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-142">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0a2f0-143">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="0a2f0-143">Next steps</span></span>

* <span data-ttu-id="0a2f0-144">W tym artykule pokazano, jak można uwierzytelniać za pomocą usługi Google.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-144">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="0a2f0-145">Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="0a2f0-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="0a2f0-146">Po opublikowaniu aplikacji na platformie Azure zresetuj ją `ClientSecret` w konsoli interfejsu API firmy Google.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-146">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="0a2f0-147">Ustaw `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` Ustawienia aplikacji i na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-147">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="0a2f0-148">System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="0a2f0-148">The configuration system is set up to read keys from environment variables.</span></span>

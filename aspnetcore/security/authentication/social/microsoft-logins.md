---
title: Konfiguracja logowania zewnętrznego konta Microsoft z ASP.NET Core
author: rick-anderson
description: Ten przykład pokazuje integrację konto Microsoft uwierzytelniania użytkowników w istniejącej aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 731a17085a1fd01852bb3fe2f0fc9f3e7a9ac30f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775664"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="9cd31-103">Konfiguracja logowania zewnętrznego konta Microsoft z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cd31-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="9cd31-104">Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9cd31-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9cd31-105">W tym przykładzie pokazano, jak umożliwić użytkownikom zalogowanie się przy użyciu pracy, szkoły lub konto Microsoft osobistych za pomocą projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9cd31-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="9cd31-106">Tworzenie aplikacji w portalu dla deweloperów firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="9cd31-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="9cd31-107">Dodaj pakiet NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) do projektu.</span><span class="sxs-lookup"><span data-stu-id="9cd31-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="9cd31-108">Przejdź do strony [Azure Portal-rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) i Utwórz lub Zaloguj się do konto Microsoft:</span><span class="sxs-lookup"><span data-stu-id="9cd31-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="9cd31-109">Jeśli nie masz konto Microsoft, wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="9cd31-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="9cd31-110">Po zalogowaniu nastąpi przekierowanie do strony **rejestracje aplikacji** :</span><span class="sxs-lookup"><span data-stu-id="9cd31-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="9cd31-111">Wybierz **nową rejestrację**</span><span class="sxs-lookup"><span data-stu-id="9cd31-111">Select **New registration**</span></span>
* <span data-ttu-id="9cd31-112">Wprowadź **nazwę**.</span><span class="sxs-lookup"><span data-stu-id="9cd31-112">Enter a **Name**.</span></span>
* <span data-ttu-id="9cd31-113">Wybierz opcję dla **obsługiwanych typów kont**.</span><span class="sxs-lookup"><span data-stu-id="9cd31-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="9cd31-114">W obszarze **Identyfikator URI przekierowania**wprowadź adres URL `/signin-microsoft` programowania z dołączonym.</span><span class="sxs-lookup"><span data-stu-id="9cd31-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="9cd31-115">Na przykład `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="9cd31-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="9cd31-116">Schemat uwierzytelniania firmy Microsoft skonfigurowany w dalszej części tego przykładu będzie automatycznie `/signin-microsoft` obsługiwał żądania w marszrucie w celu zaimplementowania przepływu OAuth.</span><span class="sxs-lookup"><span data-stu-id="9cd31-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="9cd31-117">Wybierz pozycję **zarejestruj**</span><span class="sxs-lookup"><span data-stu-id="9cd31-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="9cd31-118">Utwórz klucz tajny klienta</span><span class="sxs-lookup"><span data-stu-id="9cd31-118">Create client secret</span></span>

* <span data-ttu-id="9cd31-119">W lewym okienku wybierz pozycję **certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="9cd31-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="9cd31-120">W obszarze wpisy **tajne klienta**wybierz pozycję **nowy klucz tajny klienta** .</span><span class="sxs-lookup"><span data-stu-id="9cd31-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="9cd31-121">Dodaj opis wpisu tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="9cd31-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="9cd31-122">Wybierz przycisk **Add** (Dodaj).</span><span class="sxs-lookup"><span data-stu-id="9cd31-122">Select the **Add** button.</span></span>

* <span data-ttu-id="9cd31-123">W obszarze wpisy **tajne klienta**skopiuj wartość klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="9cd31-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="9cd31-124">Segment `/signin-microsoft` identyfikatora URI jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9cd31-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="9cd31-125">Można zmienić domyślny identyfikator URI wywołania zwrotnego podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania firmy Microsoft za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="9cd31-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="9cd31-126">Zapisz identyfikator i klucz tajny klienta firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="9cd31-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="9cd31-127">Przechowuj ustawienia poufne, takie jak identyfikator klienta firmy Microsoft i wartości tajne przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9cd31-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="9cd31-128">W tym przykładzie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="9cd31-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="9cd31-129">Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="9cd31-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="9cd31-130">Zapisz poufne ustawienia w lokalnym magazynie wpisów tajnych przy użyciu kluczy `Authentication:Microsoft:ClientId` tajnych `Authentication:Microsoft:ClientSecret`i:</span><span class="sxs-lookup"><span data-stu-id="9cd31-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="9cd31-131">Konfigurowanie uwierzytelniania konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="9cd31-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="9cd31-132">Dodaj usługę konta Microsoft do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9cd31-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="9cd31-133">Aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie konta Microsoft, zobacz Dokumentacja interfejsu API [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="9cd31-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="9cd31-134">Może to służyć do żądania różnych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="9cd31-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="9cd31-135">Konto Zaloguj się przy użyciu konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="9cd31-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="9cd31-136">Uruchom aplikację i kliknij pozycję **Zaloguj**.</span><span class="sxs-lookup"><span data-stu-id="9cd31-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="9cd31-137">Zostanie wyświetlona opcja zalogowania się do firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9cd31-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="9cd31-138">Po kliknięciu firmy Microsoft nastąpi przekierowanie do firmy Microsoft w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="9cd31-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="9cd31-139">Po zalogowaniu się przy użyciu konta Microsoft zostanie wyświetlony monit o zezwolenie aplikacji na dostęp do informacji:</span><span class="sxs-lookup"><span data-stu-id="9cd31-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="9cd31-140">Naciśnij pozycję **tak** . nastąpi przekierowanie z powrotem do witryny sieci Web, w której można ustawić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="9cd31-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="9cd31-141">Jesteś teraz zalogowany przy użyciu poświadczeń firmy Microsoft:</span><span class="sxs-lookup"><span data-stu-id="9cd31-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="9cd31-142">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="9cd31-142">Troubleshooting</span></span>

* <span data-ttu-id="9cd31-143">Jeśli dostawca kont Microsoft przekieruje Cię do strony błędu logowania, należy zwrócić uwagę na tytuły i opis błędu parametrów zapytania bezpośrednio po elemencie `#` (hasztagów) w identyfikatorze URI.</span><span class="sxs-lookup"><span data-stu-id="9cd31-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="9cd31-144">Mimo że zostanie wyświetlony komunikat o błędzie z informacją o problemie z uwierzytelnianiem firmy Microsoft, najbardziej typową przyczyną jest to, że identyfikator URI aplikacji nie pasuje do żadnego z **identyfikatorów URI przekierowania** określonych dla danej platformy **sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="9cd31-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="9cd31-145">Jeśli Identity nie jest skonfigurowany przez `services.AddIdentity` wywołanie `ConfigureServices`w, próba uwierzytelnienia spowoduje powstanie *argumentu ArgumentException: należy podać opcję "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="9cd31-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="9cd31-146">Szablon projektu używany w tym przykładzie zapewnia, że jest to gotowe.</span><span class="sxs-lookup"><span data-stu-id="9cd31-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="9cd31-147">Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, *podczas przetwarzania błędu żądania nie powiodła się operacja bazy danych* .</span><span class="sxs-lookup"><span data-stu-id="9cd31-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="9cd31-148">Naciśnij pozycję **Zastosuj migracje** , aby utworzyć bazę danych i odświeżyć, aby kontynuować z powodu błędu.</span><span class="sxs-lookup"><span data-stu-id="9cd31-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9cd31-149">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="9cd31-149">Next steps</span></span>

* <span data-ttu-id="9cd31-150">W tym artykule pokazano, jak można uwierzytelnić się w firmie Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9cd31-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="9cd31-151">Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9cd31-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="9cd31-152">Po opublikowaniu witryny sieci Web w usłudze Azure Web App Utwórz nowe wpisy tajne klienta w portalu dla deweloperów firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9cd31-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="9cd31-153">Ustaw ustawienia `Authentication:Microsoft:ClientId` aplikacji `Authentication:Microsoft:ClientSecret` i na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="9cd31-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="9cd31-154">System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="9cd31-154">The configuration system is set up to read keys from environment variables.</span></span>

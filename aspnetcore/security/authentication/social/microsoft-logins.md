---
title: Konfiguracja logowania zewnętrznego konta Microsoft za pomocą ASP.NET Core
author: rick-anderson
description: W tym przykładzie pokazano integrację uwierzytelniania użytkowników konta Microsoft z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384048"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="ed3c6-103">Konfiguracja logowania zewnętrznego konta Microsoft za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ed3c6-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="ed3c6-104">Autorstwa [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ed3c6-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ed3c6-105">W tym przykładzie pokazano, jak umożliwić użytkownikom zalogowanie się za pomocą pracy, szkoły lub osobistego konta Microsoft przy użyciu projektu ASP.NET Core 3.0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ed3c6-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="ed3c6-106">Tworzenie aplikacji w portalu Microsoft Developer Portal</span><span class="sxs-lookup"><span data-stu-id="ed3c6-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="ed3c6-107">Dodaj pakiet [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="ed3c6-108">Przejdź do strony [Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) i utwórz lub zaloguj się do konta Microsoft:</span><span class="sxs-lookup"><span data-stu-id="ed3c6-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="ed3c6-109">Jeśli nie masz konta Microsoft, wybierz pozycję **Utwórz jedno**.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="ed3c6-110">Po zalogowaniu się użytkownik zostanie przekierowany na stronę **Rejestracje aplikacji:**</span><span class="sxs-lookup"><span data-stu-id="ed3c6-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="ed3c6-111">Wybierz **nową rejestrację**</span><span class="sxs-lookup"><span data-stu-id="ed3c6-111">Select **New registration**</span></span>
* <span data-ttu-id="ed3c6-112">Wprowadź **nazwę**.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-112">Enter a **Name**.</span></span>
* <span data-ttu-id="ed3c6-113">Wybierz opcję dla **obsługiwanych typów kont**.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="ed3c6-114">W obszarze **Przekierowanie identyfikatora URI**wprowadź adres URL rozwoju z `/signin-microsoft` dołączeniem.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="ed3c6-115">Na przykład `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="ed3c6-116">Schemat uwierzytelniania firmy Microsoft skonfigurowany w dalszej `/signin-microsoft` części tego przykładu będzie automatycznie obsługiwał żądania w drodze w celu zaimplementowania przepływu OAuth.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="ed3c6-117">Wybierz **zarejestruj się**</span><span class="sxs-lookup"><span data-stu-id="ed3c6-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="ed3c6-118">Tworzenie klucza tajnego klienta</span><span class="sxs-lookup"><span data-stu-id="ed3c6-118">Create client secret</span></span>

* <span data-ttu-id="ed3c6-119">W lewym okienku wybierz pozycję **Certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="ed3c6-120">W obszarze **Wpisy tajne klienta**wybierz pozycję **Nowy klucz tajny klienta**</span><span class="sxs-lookup"><span data-stu-id="ed3c6-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="ed3c6-121">Dodaj opis klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="ed3c6-122">Wybierz przycisk **Add** (Dodaj).</span><span class="sxs-lookup"><span data-stu-id="ed3c6-122">Select the **Add** button.</span></span>

* <span data-ttu-id="ed3c6-123">W obszarze **Wpisy tajne klienta**skopiuj wartość klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="ed3c6-124">Segment identyfikatora `/signin-microsoft` URI jest ustawiony jako domyślne wywołanie zwrotne dostawcy uwierzytelniania firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="ed3c6-125">Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania firmy Microsoft za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="ed3c6-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="ed3c6-126">Przechowywanie identyfikatora klienta firmy Microsoft i klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="ed3c6-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="ed3c6-127">Przechowuj poufne ustawienia, takie jak identyfikator klienta firmy Microsoft i wartości tajne, za pomocą [menedżera tajnego](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ed3c6-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="ed3c6-128">W tym przykładzie należy wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="ed3c6-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="ed3c6-129">Inicjowanie projektu do przechowywania w celu uzyskania instrukcji w [usłudze Włącz magazyn tajny](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="ed3c6-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="ed3c6-130">Przechowuj poufne ustawienia w lokalnym magazynie tajnym z tajnymi kluczami `Authentication:Microsoft:ClientId` i: `Authentication:Microsoft:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="ed3c6-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="ed3c6-131">Konfigurowanie uwierzytelniania konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="ed3c6-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="ed3c6-132">Dodaj usługę Konto Microsoft `Startup.ConfigureServices`do :</span><span class="sxs-lookup"><span data-stu-id="ed3c6-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="ed3c6-133">Aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie konta Microsoft, zobacz odwołanie do interfejsu API [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="ed3c6-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="ed3c6-134">Może to służyć do żądania różnych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="ed3c6-135">Logowanie się za pomocą konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="ed3c6-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="ed3c6-136">Uruchom aplikację i kliknij pozycję **Zaloguj się**.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="ed3c6-137">Pojawi się opcja logowania się za pomocą firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="ed3c6-138">Po kliknięciu firmy Microsoft użytkownik zostanie przekierowany do firmy Microsoft w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="ed3c6-139">Po zalogowaniu się za pomocą konta Microsoft zostanie wyświetlony monit o umożliwienie aplikacji dostępu do informacji:</span><span class="sxs-lookup"><span data-stu-id="ed3c6-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="ed3c6-140">Naciśnij **pozycję Tak,** a zostaniesz przekierowany z powrotem do witryny sieci Web, na której możesz ustawić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="ed3c6-141">Jesteś teraz zalogowany przy użyciu poświadczeń firmy Microsoft:</span><span class="sxs-lookup"><span data-stu-id="ed3c6-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="ed3c6-142">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="ed3c6-142">Troubleshooting</span></span>

* <span data-ttu-id="ed3c6-143">Jeśli dostawca konta Microsoft przekierowuje użytkownika do strony błędu logowania, zanotuj `#` parametry ciągu kwerendy o tytule błędu i opisie bezpośrednio po (hashtag) w identyfikatorze Uri.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="ed3c6-144">Chociaż komunikat o błędzie wydaje się wskazywać na problem z uwierzytelnianiem firmy Microsoft, najczęstszą przyczyną jest aplikacja Uri nie pasujące do żadnej z **identyfikatorów URI przekierowania** określonych dla platformy **sieci Web.**</span><span class="sxs-lookup"><span data-stu-id="ed3c6-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="ed3c6-145">Jeśli tożsamość nie jest `services.AddIdentity` skonfigurowana przez `ConfigureServices`wywołanie, próba uwierzytelnienia *spowoduje, że argumentexception: musi zostać podana opcja "SignInScheme".*</span><span class="sxs-lookup"><span data-stu-id="ed3c6-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="ed3c6-146">Szablon projektu użyty w tym przykładzie zapewnia, że jest to zrobione.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="ed3c6-147">Jeśli baza danych lokacji nie została utworzona przez zastosowanie migracji początkowej, podczas przetwarzania błędu żądania zostanie wyświetlony *komunikat A database.*</span><span class="sxs-lookup"><span data-stu-id="ed3c6-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="ed3c6-148">Naciśnij pozycję **Zastosuj migracje,** aby utworzyć bazę danych i odświeżyć, aby przejść obok błędu.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ed3c6-149">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="ed3c6-149">Next steps</span></span>

* <span data-ttu-id="ed3c6-150">W tym artykule pokazano, jak można uwierzytelniać się w firmie Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="ed3c6-151">Podobne podejście można stosować w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ed3c6-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="ed3c6-152">Po opublikowaniu witryny sieci Web w aplikacji sieci Web platformy Azure utwórz nowe wpisy tajne klienta w portalu microsoft developer portal.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="ed3c6-153">Ustaw `Authentication:Microsoft:ClientId` ustawienia `Authentication:Microsoft:ClientSecret` aplikacji i jako w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="ed3c6-154">System konfiguracji jest skonfigurowany do odczytu kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="ed3c6-154">The configuration system is set up to read keys from environment variables.</span></span>

---
title: Konfiguracja logowania zewnętrznego konta Microsoft z ASP.NET Core
author: rick-anderson
description: Ten przykład pokazuje integrację konto Microsoft uwierzytelniania użytkowników w istniejącej aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 36341a0e439be57d7da4f787aa6103b92c624e96
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847588"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="c71e4-103">Konfiguracja logowania zewnętrznego konta Microsoft z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c71e4-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="c71e4-104">Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c71e4-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c71e4-105">W tym przykładzie pokazano, jak umożliwić użytkownikom zalogowanie się przy użyciu pracy, szkoły lub konto Microsoft osobistych za pomocą projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c71e4-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="c71e4-106">Tworzenie aplikacji w portalu dla deweloperów firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="c71e4-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="c71e4-107">Dodaj pakiet NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) do projektu.</span><span class="sxs-lookup"><span data-stu-id="c71e4-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="c71e4-108">Przejdź do strony [Azure Portal-rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) i Utwórz lub Zaloguj się do konto Microsoft:</span><span class="sxs-lookup"><span data-stu-id="c71e4-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="c71e4-109">Jeśli nie masz konto Microsoft, wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c71e4-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="c71e4-110">Po zalogowaniu nastąpi przekierowanie do strony **rejestracje aplikacji** :</span><span class="sxs-lookup"><span data-stu-id="c71e4-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="c71e4-111">Wybierz **nową rejestrację**</span><span class="sxs-lookup"><span data-stu-id="c71e4-111">Select **New registration**</span></span>
* <span data-ttu-id="c71e4-112">Wprowadź **nazwę**.</span><span class="sxs-lookup"><span data-stu-id="c71e4-112">Enter a **Name**.</span></span>
* <span data-ttu-id="c71e4-113">Wybierz opcję dla **obsługiwanych typów kont**.</span><span class="sxs-lookup"><span data-stu-id="c71e4-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * <span data-ttu-id="c71e4-114">`MicrosoftAccount`Pakiet obsługuje rejestracje aplikacji utworzone przy użyciu opcji "konta w dowolnym katalogu organizacyjnym" lub "konta w dowolnym katalogu organizacyjnym i na kontach Microsoft".</span><span class="sxs-lookup"><span data-stu-id="c71e4-114">The `MicrosoftAccount` package supports App Registrations created using "Accounts in any organizational directory" or "Accounts in any organizational directory and Microsoft accounts" options by default.</span></span>
  * <span data-ttu-id="c71e4-115">Aby skorzystać z innych opcji, ustaw `AuthorizationEndpoint` i `TokenEndpoint` elementy członkowskie `MicrosoftAccountOptions` użyte do zainicjowania uwierzytelniania konta Microsoft do adresów URL wyświetlanych na stronie **punkty końcowe** rejestracji aplikacji po jej utworzeniu (dostępne przez kliknięcie punktów końcowych na stronie **Przegląd** ).</span><span class="sxs-lookup"><span data-stu-id="c71e4-115">To use other options, set `AuthorizationEndpoint` and `TokenEndpoint` members of `MicrosoftAccountOptions` used to initialize the Microsoft Account authentication to the URLs displayed on **Endpoints** page of the App Registration after it is created (available by clicking Endpoints on the **Overview** page).</span></span>
* <span data-ttu-id="c71e4-116">W obszarze **Identyfikator URI przekierowania**wprowadź adres URL programowania z `/signin-microsoft` dołączonym.</span><span class="sxs-lookup"><span data-stu-id="c71e4-116">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="c71e4-117">Na przykład `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="c71e4-117">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="c71e4-118">Schemat uwierzytelniania firmy Microsoft skonfigurowany w dalszej części tego przykładu będzie automatycznie obsługiwał żądania w `/signin-microsoft` marszrucie w celu zaimplementowania przepływu OAuth.</span><span class="sxs-lookup"><span data-stu-id="c71e4-118">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="c71e4-119">Wybierz pozycję **zarejestruj**</span><span class="sxs-lookup"><span data-stu-id="c71e4-119">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="c71e4-120">Utwórz klucz tajny klienta</span><span class="sxs-lookup"><span data-stu-id="c71e4-120">Create client secret</span></span>

* <span data-ttu-id="c71e4-121">W lewym okienku wybierz pozycję **certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="c71e4-121">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="c71e4-122">W obszarze wpisy **tajne klienta**wybierz pozycję **nowy klucz tajny klienta** .</span><span class="sxs-lookup"><span data-stu-id="c71e4-122">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="c71e4-123">Dodaj opis wpisu tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="c71e4-123">Add a description for the client secret.</span></span>
  * <span data-ttu-id="c71e4-124">Wybierz przycisk **Add** (Dodaj).</span><span class="sxs-lookup"><span data-stu-id="c71e4-124">Select the **Add** button.</span></span>

* <span data-ttu-id="c71e4-125">W obszarze wpisy **tajne klienta**skopiuj wartość klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="c71e4-125">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="c71e4-126">Segment identyfikatora URI `/signin-microsoft` jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c71e4-126">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="c71e4-127">Można zmienić domyślny identyfikator URI wywołania zwrotnego podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania firmy Microsoft za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="c71e4-127">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="c71e4-128">Zapisz identyfikator i klucz tajny klienta firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="c71e4-128">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="c71e4-129">Przechowuj ustawienia poufne, takie jak identyfikator klienta firmy Microsoft i wartości tajne przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c71e4-129">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c71e4-130">W tym przykładzie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="c71e4-130">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="c71e4-131">Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="c71e4-131">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="c71e4-132">Zapisz poufne ustawienia w lokalnym magazynie wpisów tajnych przy użyciu kluczy tajnych `Authentication:Microsoft:ClientId` i `Authentication:Microsoft:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="c71e4-132">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="c71e4-133">Konfigurowanie uwierzytelniania konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="c71e4-133">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="c71e4-134">Dodaj usługę konta Microsoft do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c71e4-134">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="c71e4-135">Aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie konta Microsoft, zobacz Dokumentacja interfejsu API [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="c71e4-135">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="c71e4-136">Może to służyć do żądania różnych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="c71e4-136">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="c71e4-137">Konto Zaloguj się przy użyciu konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="c71e4-137">Sign in with Microsoft Account</span></span>

<span data-ttu-id="c71e4-138">Uruchom aplikację i kliknij pozycję **Zaloguj**.</span><span class="sxs-lookup"><span data-stu-id="c71e4-138">Run the app and click **Log in**.</span></span> <span data-ttu-id="c71e4-139">Zostanie wyświetlona opcja zalogowania się do firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c71e4-139">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="c71e4-140">Po kliknięciu firmy Microsoft nastąpi przekierowanie do firmy Microsoft w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="c71e4-140">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="c71e4-141">Po zalogowaniu się przy użyciu konta Microsoft zostanie wyświetlony monit o zezwolenie aplikacji na dostęp do informacji:</span><span class="sxs-lookup"><span data-stu-id="c71e4-141">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="c71e4-142">Naciśnij pozycję **tak** . nastąpi przekierowanie z powrotem do witryny sieci Web, w której można ustawić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="c71e4-142">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="c71e4-143">Jesteś teraz zalogowany przy użyciu poświadczeń firmy Microsoft:</span><span class="sxs-lookup"><span data-stu-id="c71e4-143">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="c71e4-144">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="c71e4-144">Troubleshooting</span></span>

* <span data-ttu-id="c71e4-145">Jeśli dostawca kont Microsoft przekieruje Cię do strony błędu logowania, należy zwrócić uwagę na tytuły i opis błędu parametrów zapytania bezpośrednio po elemencie `#` (hasztagów) w identyfikatorze URI.</span><span class="sxs-lookup"><span data-stu-id="c71e4-145">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="c71e4-146">Mimo że zostanie wyświetlony komunikat o błędzie z informacją o problemie z uwierzytelnianiem firmy Microsoft, najbardziej typową przyczyną jest to, że identyfikator URI aplikacji nie pasuje do żadnego z **identyfikatorów URI przekierowania** określonych dla danej platformy **sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="c71e4-146">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="c71e4-147">Jeśli Identity nie jest skonfigurowany przez wywołanie `services.AddIdentity` w `ConfigureServices` , próba uwierzytelnienia spowoduje powstanie *argumentu ArgumentException: należy podać opcję "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="c71e4-147">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="c71e4-148">Szablon projektu używany w tym przykładzie zapewnia, że jest to gotowe.</span><span class="sxs-lookup"><span data-stu-id="c71e4-148">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="c71e4-149">Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, *podczas przetwarzania błędu żądania nie powiodła się operacja bazy danych* .</span><span class="sxs-lookup"><span data-stu-id="c71e4-149">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c71e4-150">Naciśnij pozycję **Zastosuj migracje** , aby utworzyć bazę danych i odświeżyć, aby kontynuować z powodu błędu.</span><span class="sxs-lookup"><span data-stu-id="c71e4-150">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c71e4-151">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="c71e4-151">Next steps</span></span>

* <span data-ttu-id="c71e4-152">W tym artykule pokazano, jak można uwierzytelnić się w firmie Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c71e4-152">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="c71e4-153">Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c71e4-153">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="c71e4-154">Po opublikowaniu witryny sieci Web w usłudze Azure Web App Utwórz nowe wpisy tajne klienta w portalu dla deweloperów firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c71e4-154">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="c71e4-155">Ustaw `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` Ustawienia aplikacji i na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c71e4-155">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="c71e4-156">System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="c71e4-156">The configuration system is set up to read keys from environment variables.</span></span>

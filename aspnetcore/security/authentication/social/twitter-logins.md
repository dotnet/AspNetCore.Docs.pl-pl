---
title: Konfiguracja logowania zewnętrznego usługi Twitter za pomocą ASP.NET Core
author: rick-anderson
description: W tym samouczku przedstawiono integrację uwierzytelniania użytkownika konta usługi Twitter z istniejącą aplikacją ASP.NET Core.
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: c6498704214de5e805c9bf57033529d4acc5fd3e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775794"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="8e117-103">Konfiguracja logowania zewnętrznego usługi Twitter za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e117-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="8e117-104">Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8e117-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8e117-105">Ten przykład pokazuje, jak umożliwić użytkownikom [zalogowanie](https://dev.twitter.com/web/sign-in/desktop-browser) się przy użyciu konta usługi Twitter za pomocą przykładowego projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8e117-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="8e117-106">Tworzenie aplikacji w usłudze Twitter</span><span class="sxs-lookup"><span data-stu-id="8e117-106">Create the app in Twitter</span></span>

* <span data-ttu-id="8e117-107">Dodaj pakiet NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) do projektu.</span><span class="sxs-lookup"><span data-stu-id="8e117-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="8e117-108">Przejdź do [https://apps.twitter.com/](https://apps.twitter.com/) i zaloguj się.</span><span class="sxs-lookup"><span data-stu-id="8e117-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="8e117-109">Jeśli nie masz jeszcze konta usługi Twitter, Użyj linku Utwórz **[teraz](https://twitter.com/signup)** , aby go utworzyć.</span><span class="sxs-lookup"><span data-stu-id="8e117-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="8e117-110">Wybierz pozycję **Utwórz aplikację**.</span><span class="sxs-lookup"><span data-stu-id="8e117-110">Select **Create an app**.</span></span> <span data-ttu-id="8e117-111">Wypełnij pola **Nazwa aplikacji**, **Opis aplikacji** i publiczny identyfikator URI **witryny sieci Web** (może to być czas tymczasowy do momentu zarejestrowania nazwy domeny):</span><span class="sxs-lookup"><span data-stu-id="8e117-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="8e117-112">Zaznacz pole wyboru obok pozycji **Włącz logowanie za pomocą usługi Twitter**</span><span class="sxs-lookup"><span data-stu-id="8e117-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="8e117-113">Microsoft. AspNetCore.Identity</span><span class="sxs-lookup"><span data-stu-id="8e117-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="8e117-114">Domyślnie program wymaga od użytkowników posiadania adresu e-mail.</span><span class="sxs-lookup"><span data-stu-id="8e117-114">requires users to have an email address by default.</span></span> <span data-ttu-id="8e117-115">Przejdź do karty **uprawnienia** , kliknij przycisk **Edytuj** , a następnie zaznacz pole wyboru obok **żądania adresu e-mail od użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="8e117-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="8e117-116">Wprowadź swój identyfikator URI rozwoju `/signin-twitter` z dołączonym do pola **adresy URL wywołania zwrotnego** ( `https://webapp128.azurewebsites.net/signin-twitter`na przykład:).</span><span class="sxs-lookup"><span data-stu-id="8e117-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="8e117-117">Schemat uwierzytelniania usługi Twitter skonfigurowany w dalszej części tego przykładu będzie automatycznie `/signin-twitter` obsługiwał żądania w marszrucie w celu zaimplementowania przepływu OAuth.</span><span class="sxs-lookup"><span data-stu-id="8e117-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8e117-118">Segment `/signin-twitter` identyfikatora URI jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania w usłudze Twitter.</span><span class="sxs-lookup"><span data-stu-id="8e117-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="8e117-119">Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania usługi Twitter za pomocą dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="8e117-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="8e117-120">Wypełnij resztę formularza i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="8e117-120">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="8e117-121">Wyświetlane są nowe szczegóły aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8e117-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="8e117-122">Przechowywanie klucza i wpisu tajnego interfejsu API klienta usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="8e117-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="8e117-123">Przechowuj ustawienia poufne, takie jak klucz interfejsu API użytkownika usługi Twitter i wpis tajny przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8e117-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8e117-124">W tym przykładzie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="8e117-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="8e117-125">Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="8e117-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="8e117-126">Zapisz ustawienia poufne w lokalnym magazynie wpisów tajnych przy użyciu kluczy `Authentication:Twitter:ConsumerKey` tajnych `Authentication:Twitter:ConsumerSecret`i:</span><span class="sxs-lookup"><span data-stu-id="8e117-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8e117-127">Te tokeny można znaleźć na karcie **klucze i tokeny dostępu** po utworzeniu nowej aplikacji usługi Twitter:</span><span class="sxs-lookup"><span data-stu-id="8e117-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="8e117-128">Konfigurowanie uwierzytelniania w usłudze Twitter</span><span class="sxs-lookup"><span data-stu-id="8e117-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="8e117-129">Dodaj usługę Twitter do `ConfigureServices` metody w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="8e117-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="8e117-130">Zobacz Dokumentacja interfejsu API [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) , aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie w usłudze Twitter.</span><span class="sxs-lookup"><span data-stu-id="8e117-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="8e117-131">Może to służyć do żądania różnych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="8e117-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="8e117-132">Zaloguj się przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="8e117-132">Sign in with Twitter</span></span>

<span data-ttu-id="8e117-133">Uruchom aplikację i wybierz pozycję **Zaloguj się**.</span><span class="sxs-lookup"><span data-stu-id="8e117-133">Run the app and select **Log in**.</span></span> <span data-ttu-id="8e117-134">Zostanie wyświetlona opcja zalogowania się przy użyciu usługi Twitter:</span><span class="sxs-lookup"><span data-stu-id="8e117-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="8e117-135">Klikanie przekierowania w usłudze **Twitter** do usługi Twitter w celu uwierzytelnienia:</span><span class="sxs-lookup"><span data-stu-id="8e117-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="8e117-136">Po wprowadzeniu poświadczeń usługi Twitter nastąpi przekierowanie do witryny sieci Web, w której można ustawić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="8e117-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="8e117-137">Użytkownik jest obecnie zalogowany przy użyciu poświadczeń usługi Twitter:</span><span class="sxs-lookup"><span data-stu-id="8e117-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="8e117-138">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="8e117-138">Troubleshooting</span></span>

* <span data-ttu-id="8e117-139">**Tylko ASP.NET Core 2. x:** Jeśli Identity nie jest skonfigurowany przez `services.AddIdentity` wywołanie `ConfigureServices`w, próba uwierzytelnienia spowoduje powstanie *argumentu ArgumentException: należy podać opcję "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="8e117-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="8e117-140">Szablon projektu używany w tym przykładzie zapewnia, że jest to gotowe.</span><span class="sxs-lookup"><span data-stu-id="8e117-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="8e117-141">Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, *podczas przetwarzania błędu żądania nie powiodła się operacja bazy danych* .</span><span class="sxs-lookup"><span data-stu-id="8e117-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="8e117-142">Naciśnij pozycję **Zastosuj migracje** , aby utworzyć bazę danych i odświeżyć, aby kontynuować z powodu błędu.</span><span class="sxs-lookup"><span data-stu-id="8e117-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8e117-143">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="8e117-143">Next steps</span></span>

* <span data-ttu-id="8e117-144">W tym artykule pokazano, jak można uwierzytelnić się w usłudze Twitter.</span><span class="sxs-lookup"><span data-stu-id="8e117-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="8e117-145">Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8e117-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="8e117-146">Po opublikowaniu witryny sieci Web w usłudze Azure Web App należy zresetować ją `ConsumerSecret` w portalu dla deweloperów w usłudze Twitter.</span><span class="sxs-lookup"><span data-stu-id="8e117-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="8e117-147">Ustaw ustawienia `Authentication:Twitter:ConsumerKey` aplikacji `Authentication:Twitter:ConsumerSecret` i na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="8e117-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="8e117-148">System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="8e117-148">The configuration system is set up to read keys from environment variables.</span></span>

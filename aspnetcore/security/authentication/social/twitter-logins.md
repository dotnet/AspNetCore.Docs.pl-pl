---
title: Zewnętrzna konfiguracja logowania na Twitterze z ASP.NET Core
author: rick-anderson
description: W tym samouczku pokazano integrację uwierzytelniania użytkowników konta Twitter z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277291"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="59e7a-103">Zewnętrzna konfiguracja logowania na Twitterze z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59e7a-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="59e7a-104">Autorstwa [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="59e7a-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="59e7a-105">W tym przykładzie pokazano, jak umożliwić użytkownikom zalogowanie się za pomocą [konta na Twitterze](https://dev.twitter.com/web/sign-in/desktop-browser) przy użyciu przykładowego projektu ASP.NET Core 3.0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="59e7a-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="59e7a-106">Tworzenie aplikacji w Aplikacji Twitter</span><span class="sxs-lookup"><span data-stu-id="59e7a-106">Create the app in Twitter</span></span>

* <span data-ttu-id="59e7a-107">Dodaj pakiet [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="59e7a-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="59e7a-108">Przejdź [https://apps.twitter.com/](https://apps.twitter.com/) do i zaloguj się.</span><span class="sxs-lookup"><span data-stu-id="59e7a-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="59e7a-109">Jeśli nie masz jeszcze konta na Twitterze, użyj linku **[Zarejestruj się teraz,](https://twitter.com/signup)** aby je utworzyć.</span><span class="sxs-lookup"><span data-stu-id="59e7a-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="59e7a-110">Wybierz **pozycję Utwórz aplikację**.</span><span class="sxs-lookup"><span data-stu-id="59e7a-110">Select **Create an app**.</span></span> <span data-ttu-id="59e7a-111">Wypełnij **nazwę aplikacji,** **opis aplikacji** i publiczny identyfikator URI **witryny sieci Web** (może to być tymczasowe, dopóki nie zarejestrujesz nazwy domeny):</span><span class="sxs-lookup"><span data-stu-id="59e7a-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="59e7a-112">Zaznacz pole wyboru obok pozycji **Włącz logowanie za pomocą twittera**</span><span class="sxs-lookup"><span data-stu-id="59e7a-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="59e7a-113">Microsoft.AspNetCore.Identity wymaga, aby użytkownicy mieli domyślnie adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="59e7a-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="59e7a-114">Przejdź do karty **Uprawnienia,** kliknij przycisk **Edytuj** i zaznacz pole wyboru Obok pozycji **Żądanie adresu e-mail od użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="59e7a-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="59e7a-115">Wprowadź identyfikator URI `/signin-twitter` rozwoju z dołączonym do pola `https://webapp128.azurewebsites.net/signin-twitter` **Adresy URL wywołania zwrotnego** (na przykład: ).</span><span class="sxs-lookup"><span data-stu-id="59e7a-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="59e7a-116">Schemat uwierzytelniania Twitter skonfigurowany w dalszej części `/signin-twitter` tego przykładu będzie automatycznie obsługiwać żądania na trasie w celu zaimplementowania przepływu OAuth.</span><span class="sxs-lookup"><span data-stu-id="59e7a-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="59e7a-117">Segment identyfikatora `/signin-twitter` URI jest ustawiony jako domyślne wywołanie zwrotne dostawcy uwierzytelniania Twittera.</span><span class="sxs-lookup"><span data-stu-id="59e7a-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="59e7a-118">Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania Twittera za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)</span><span class="sxs-lookup"><span data-stu-id="59e7a-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="59e7a-119">Wypełnij pozostałą część formularza i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="59e7a-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="59e7a-120">Wyświetlane są nowe szczegóły aplikacji:</span><span class="sxs-lookup"><span data-stu-id="59e7a-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="59e7a-121">Przechowywanie klucza API konsumenta Twittera i klucz tajny</span><span class="sxs-lookup"><span data-stu-id="59e7a-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="59e7a-122">Przechowuj poufne ustawienia, takie jak klucz interfejsu API konsumenta Twittera i klucz tajny za pomocą [Secret Managera](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="59e7a-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="59e7a-123">W tym przykładzie należy wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="59e7a-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="59e7a-124">Inicjowanie projektu do przechowywania w celu uzyskania instrukcji w [usłudze Włącz magazyn tajny](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="59e7a-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="59e7a-125">Przechowuj poufne ustawienia w lokalnym magazynie tajnym z kluczami wpisów `Authentication:Twitter:ConsumerKey` tajnych i: `Authentication:Twitter:ConsumerSecret`</span><span class="sxs-lookup"><span data-stu-id="59e7a-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="59e7a-126">Tokeny te można znaleźć na karcie **Klucze i tokeny dostępu** po utworzeniu nowej aplikacji Twitter:</span><span class="sxs-lookup"><span data-stu-id="59e7a-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="59e7a-127">Konfigurowanie uwierzytelniania twitterowego</span><span class="sxs-lookup"><span data-stu-id="59e7a-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="59e7a-128">Dodaj usługę Twitter `ConfigureServices` w metodzie *w pliku Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="59e7a-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="59e7a-129">Zobacz odwołanie interfejsu API [TwitterOptions,](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie na Twitterze.</span><span class="sxs-lookup"><span data-stu-id="59e7a-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="59e7a-130">Może to służyć do żądania różnych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="59e7a-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="59e7a-131">Zaloguj się za pomocą Twittera</span><span class="sxs-lookup"><span data-stu-id="59e7a-131">Sign in with Twitter</span></span>

<span data-ttu-id="59e7a-132">Uruchom aplikację i wybierz pozycję **Zaloguj się**.</span><span class="sxs-lookup"><span data-stu-id="59e7a-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="59e7a-133">Pojawi się opcja logowania się za pomocą Twittera:</span><span class="sxs-lookup"><span data-stu-id="59e7a-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="59e7a-134">Kliknięcie na **Twitter** przekierowuje do Twitter do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="59e7a-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="59e7a-135">Po wprowadzeniu poświadczeń twittera zostaniesz przekierowany z powrotem do witryny sieci Web, na której możesz ustawić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="59e7a-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="59e7a-136">Jesteś teraz zalogowany przy użyciu poświadczeń Twitter:</span><span class="sxs-lookup"><span data-stu-id="59e7a-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="59e7a-137">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="59e7a-137">Troubleshooting</span></span>

* <span data-ttu-id="59e7a-138">**ASP.NET tylko Core 2.x:** Jeśli tożsamość nie jest `services.AddIdentity` skonfigurowana przez `ConfigureServices`wywołanie, próba uwierzytelnienia *spowoduje, że argumentexception: musi zostać podana opcja "SignInScheme".*</span><span class="sxs-lookup"><span data-stu-id="59e7a-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="59e7a-139">Szablon projektu użyty w tym przykładzie zapewnia, że jest to zrobione.</span><span class="sxs-lookup"><span data-stu-id="59e7a-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="59e7a-140">Jeśli baza danych lokacji nie została utworzona przez zastosowanie migracji początkowej, podczas przetwarzania błędu żądania zostanie wyświetlony *komunikat A database.*</span><span class="sxs-lookup"><span data-stu-id="59e7a-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="59e7a-141">Naciśnij pozycję **Zastosuj migracje,** aby utworzyć bazę danych i odświeżyć, aby przejść obok błędu.</span><span class="sxs-lookup"><span data-stu-id="59e7a-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="59e7a-142">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="59e7a-142">Next steps</span></span>

* <span data-ttu-id="59e7a-143">W tym artykule pokazano, jak można uwierzytelnić się za pomocą Twittera.</span><span class="sxs-lookup"><span data-stu-id="59e7a-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="59e7a-144">Podobne podejście można stosować w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="59e7a-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="59e7a-145">Po opublikowaniu witryny sieci Web w aplikacji `ConsumerSecret` sieci Web platformy Azure należy zresetować witrynę w portalu dewelopera Twitter.</span><span class="sxs-lookup"><span data-stu-id="59e7a-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="59e7a-146">Ustaw `Authentication:Twitter:ConsumerKey` ustawienia `Authentication:Twitter:ConsumerSecret` aplikacji i jako w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="59e7a-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="59e7a-147">System konfiguracji jest skonfigurowany do odczytu kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="59e7a-147">The configuration system is set up to read keys from environment variables.</span></span>

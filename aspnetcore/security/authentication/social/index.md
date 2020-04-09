---
title: Uwierzytelnianie Facebooka, Google i zewnętrznego dostawcy w ASP.NET Core
author: rick-anderson
description: W tym samouczku pokazano, jak utworzyć aplikację core ASP.NET przy użyciu OAuth 2.0 z zewnętrznymi dostawcami uwierzytelniania.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: c698edbd85d665509366287b1dcad08e276e71cc
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78668043"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="a2b96-103">Uwierzytelnianie Facebooka, Google i zewnętrznego dostawcy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2b96-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="a2b96-104">Autorstwa [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a2b96-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a2b96-105">W tym samouczku pokazano, jak utworzyć ASP.NET core 3.0 aplikacji, która umożliwia użytkownikom zalogować się przy użyciu OAuth 2.0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a2b96-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="a2b96-106">[Dostawcy usług Facebook,](xref:security/authentication/facebook-logins) [Twitter,](xref:security/authentication/twitter-logins) [Google](xref:security/authentication/google-logins)i [Microsoft](xref:security/authentication/microsoft-logins) są omówione w poniższych sekcjach i korzystają z projektu startowego utworzonego w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="a2b96-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="a2b96-107">Inni dostawcy są dostępni w pakietach innych firm, takich jak [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) i [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="a2b96-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="a2b96-108">Umożliwienie użytkownikom logowania się przy użyciu istniejących poświadczeń:</span><span class="sxs-lookup"><span data-stu-id="a2b96-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="a2b96-109">Jest wygodny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a2b96-109">Is convenient for the users.</span></span>
* <span data-ttu-id="a2b96-110">Przenosi wiele zawiłości zarządzania procesem logowania na stronę trzecią.</span><span class="sxs-lookup"><span data-stu-id="a2b96-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="a2b96-111">Przykłady, w jaki sposób logowania społecznościowe mogą napędzać ruch i konwersje klientów, można znaleźć w studiach przypadku [na Facebooku](https://www.facebook.com/unsupportedbrowser) i [Twitterze.](https://dev.twitter.com/resources/case-studies)</span><span class="sxs-lookup"><span data-stu-id="a2b96-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="a2b96-112">Tworzenie nowego projektu ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="a2b96-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a2b96-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2b96-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a2b96-114">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="a2b96-114">Create a new project.</span></span>
* <span data-ttu-id="a2b96-115">Wybierz **ASP.NET Core Web Application** i **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="a2b96-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="a2b96-116">Podaj **nazwę projektu** i potwierdź lub zmień **lokalizację**.</span><span class="sxs-lookup"><span data-stu-id="a2b96-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="a2b96-117">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a2b96-117">Select **Create**.</span></span>
* <span data-ttu-id="a2b96-118">Wybierz najnowszą wersję ASP.NET Core w rozwijanej **(ASP.NET Core {X.Y}),** a następnie wybierz opcję **Aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="a2b96-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="a2b96-119">W obszarze **Uwierzytelnianie**wybierz **pozycję Zmień** i ustaw uwierzytelnianie na Indywidualne **konta użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="a2b96-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="a2b96-120">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="a2b96-120">Select **OK**.</span></span>
* <span data-ttu-id="a2b96-121">W oknie **Tworzenie nowej ASP.NET podstawowej aplikacji sieci Web** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a2b96-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a2b96-122">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a2b96-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a2b96-123">Otwórz terminal.</span><span class="sxs-lookup"><span data-stu-id="a2b96-123">Open the terminal.</span></span>  <span data-ttu-id="a2b96-124">W programie Visual Studio Code można otworzyć [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a2b96-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a2b96-125">Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.</span><span class="sxs-lookup"><span data-stu-id="a2b96-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="a2b96-126">W systemie Windows uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="a2b96-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="a2b96-127">W przypadku systemów macOS i Linux uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="a2b96-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="a2b96-128">Polecenie `dotnet new` tworzy nowy projekt Razor Pages w folderze *WebApp1.*</span><span class="sxs-lookup"><span data-stu-id="a2b96-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="a2b96-129">`-au Individual`tworzy kod do indywidualnego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a2b96-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="a2b96-130">`-uld`używa LocalDB, lekka wersja programu SQL Server Express dla systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="a2b96-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="a2b96-131">Pomiń `-uld` użycie funkcji SQLite.</span><span class="sxs-lookup"><span data-stu-id="a2b96-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="a2b96-132">Polecenie `code` otwiera folder *WebApp1* w nowym wystąpieniu programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a2b96-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="a2b96-133">Stosowanie migracji</span><span class="sxs-lookup"><span data-stu-id="a2b96-133">Apply migrations</span></span>

* <span data-ttu-id="a2b96-134">Uruchom aplikację i wybierz **łącze Zarejestruj.**</span><span class="sxs-lookup"><span data-stu-id="a2b96-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="a2b96-135">Wprowadź adres e-mail i hasło dla nowego konta, a następnie wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="a2b96-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="a2b96-136">Postępuj zgodnie z instrukcjami, aby zastosować migracje.</span><span class="sxs-lookup"><span data-stu-id="a2b96-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="a2b96-137">Używanie programu SecretManager do przechowywania tokenów przypisanych przez dostawców logowania</span><span class="sxs-lookup"><span data-stu-id="a2b96-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="a2b96-138">Dostawcy logowania społecznościowego **przypisują** identyfikator aplikacji i tokeny **klucza tajnego aplikacji** podczas procesu rejestracji.</span><span class="sxs-lookup"><span data-stu-id="a2b96-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="a2b96-139">Dokładne nazwy tokenów różnią się w zależności od dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a2b96-139">The exact token names vary by provider.</span></span> <span data-ttu-id="a2b96-140">Tokeny te reprezentują poświadczenia używane przez aplikację do uzyskiwania dostępu do interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="a2b96-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="a2b96-141">Tokeny stanowią "tajemnice", które mogą być połączone z konfiguracją aplikacji za pomocą [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="a2b96-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="a2b96-142">Secret Manager jest bezpieczniejszą alternatywą dla przechowywania tokenów w pliku konfiguracyjnym, takich jak *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a2b96-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a2b96-143">Secret Manager służy wyłącznie do celów programisty.</span><span class="sxs-lookup"><span data-stu-id="a2b96-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="a2b96-144">Wpisy tajne testów i produkcji platformy Azure można przechowywać i chronić za pomocą [dostawcy konfiguracji usługi Azure Key Vault.](xref:security/key-vault-configuration)</span><span class="sxs-lookup"><span data-stu-id="a2b96-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="a2b96-145">Wykonaj kroki opisane w [bezpieczne przechowywanie wpisów tajnych aplikacji w rozwoju w ASP.NET tematu Core](xref:security/app-secrets) do przechowywania tokenów przypisanych przez każdego dostawcę logowania poniżej.</span><span class="sxs-lookup"><span data-stu-id="a2b96-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="a2b96-146">Konfigurowanie dostawców logowania wymaganych przez aplikację</span><span class="sxs-lookup"><span data-stu-id="a2b96-146">Setup login providers required by your application</span></span>

<span data-ttu-id="a2b96-147">Użyj następujących tematów, aby skonfigurować aplikację do korzystania z odpowiednich dostawców:</span><span class="sxs-lookup"><span data-stu-id="a2b96-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="a2b96-148">Instrukcje [na Facebooku](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="a2b96-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="a2b96-149">Instrukcje [twitterowe](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="a2b96-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="a2b96-150">Instrukcje [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="a2b96-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="a2b96-151">Instrukcje [firmy Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="a2b96-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="a2b96-152">[Inne](xref:security/authentication/otherlogins) instrukcje dla dostawców</span><span class="sxs-lookup"><span data-stu-id="a2b96-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="a2b96-153">Opcjonalnie ustaw hasło</span><span class="sxs-lookup"><span data-stu-id="a2b96-153">Optionally set password</span></span>

<span data-ttu-id="a2b96-154">Po zarejestrowaniu się u zewnętrznego dostawcy logowania nie masz hasła zarejestrowanego w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a2b96-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="a2b96-155">To łagodzi cię od tworzenia i zapamiętywania hasła do witryny, ale także uzależnia cię od zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="a2b96-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="a2b96-156">Jeśli zewnętrzny dostawca logowania jest niedostępny, nie będzie można zalogować się do witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a2b96-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="a2b96-157">Aby utworzyć hasło i zalogować się przy użyciu poczty e-mail ustawionej podczas procesu logowania u zewnętrznych dostawców:</span><span class="sxs-lookup"><span data-stu-id="a2b96-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="a2b96-158">Wybierz **łącze &lt;&gt; Hello email alias** w prawym górnym rogu, aby przejść do widoku **Zarządzaj.**</span><span class="sxs-lookup"><span data-stu-id="a2b96-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Aplikacja sieci Web Zarządzaj widokiem](index/_static/pass1a.png)

* <span data-ttu-id="a2b96-160">Wybierz **pozycję Utwórz**</span><span class="sxs-lookup"><span data-stu-id="a2b96-160">Select **Create**</span></span>

![Ustawianie strony hasła](index/_static/pass2a.png)

* <span data-ttu-id="a2b96-162">Ustaw prawidłowe hasło i możesz użyć go do zalogowania się za pomocą poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="a2b96-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a2b96-163">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="a2b96-163">Next steps</span></span>

* <span data-ttu-id="a2b96-164">Zobacz [ten problem GitHub,](https://github.com/dotnet/AspNetCore.Docs/issues/10563) aby uzyskać informacje na temat dostosowywania przycisków logowania.</span><span class="sxs-lookup"><span data-stu-id="a2b96-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="a2b96-165">W tym artykule wprowadzono uwierzytelnianie zewnętrzne i wyjaśniono wymagania wstępne wymagane do dodawania zewnętrznych loginów do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2b96-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="a2b96-166">Strony specyficzne dla dostawcy odwołań do konfigurowania loginów dla dostawców wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="a2b96-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="a2b96-167">Można utrwalić dodatkowe dane dotyczące użytkownika i ich tokenów dostępu i odświeżania.</span><span class="sxs-lookup"><span data-stu-id="a2b96-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="a2b96-168">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="a2b96-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

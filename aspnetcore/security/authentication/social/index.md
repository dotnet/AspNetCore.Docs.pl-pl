---
title: Uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym w ASP.NET Core
author: rick-anderson
description: W tym samouczku przedstawiono sposób tworzenia aplikacji ASP.NET Core przy użyciu protokołu OAuth 2,0 z zewnętrznymi dostawcami uwierzytelniania.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: da025cb8dced8ac8a6ecf559d4de7795c9797f71
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634283"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="f9772-103">Uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9772-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="f9772-104">Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f9772-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f9772-105">W tym samouczku przedstawiono sposób tworzenia aplikacji ASP.NET Core 3,0, która umożliwia użytkownikom logowanie się przy użyciu uwierzytelniania OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f9772-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="f9772-106">Dostawcy [serwisu Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)i [Microsoft](xref:security/authentication/microsoft-logins) są objęci następującymi sekcjami i używają początkowego projektu utworzonego w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="f9772-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="f9772-107">Inni dostawcy są dostępni w pakietach innych firm, takich jak [ASPNET. Security. OAuth. Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) i [ASPNET. Security. OpenID Connect. Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="f9772-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="f9772-108">Umożliwienie użytkownikom logowania się przy użyciu istniejących poświadczeń:</span><span class="sxs-lookup"><span data-stu-id="f9772-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="f9772-109">Jest wygodna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="f9772-109">Is convenient for the users.</span></span>
* <span data-ttu-id="f9772-110">Przenosi wiele kompleksów związanych z zarządzaniem procesem logowania na stronie trzeciej.</span><span class="sxs-lookup"><span data-stu-id="f9772-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="f9772-111">Aby zapoznać się z przykładami sposobu, w jaki nazwy logowania społecznościowe mogą na przykład przeanalizować [ruch i konwersje](https://dev.twitter.com/resources/case-studies)klientów, zobacz temat analizy przypadków według [serwisu Facebook](https://www.facebook.com/unsupportedbrowser)</span><span class="sxs-lookup"><span data-stu-id="f9772-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="f9772-112">Utwórz nowy projekt ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9772-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f9772-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9772-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f9772-114">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="f9772-114">Create a new project.</span></span>
* <span data-ttu-id="f9772-115">Wybierz pozycję **ASP.NET Core aplikacja sieci Web** i przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f9772-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="f9772-116">Podaj **nazwę projektu** i Potwierdź lub Zmień **lokalizację**.</span><span class="sxs-lookup"><span data-stu-id="f9772-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="f9772-117">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f9772-117">Select **Create**.</span></span>
* <span data-ttu-id="f9772-118">Wybierz najnowszą wersję ASP.NET Core z listy rozwijanej (**ASP.NET Core {X. Y}**), a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="f9772-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="f9772-119">W obszarze **uwierzytelnianie**wybierz opcję **Zmień** i ustaw uwierzytelnianie na **konta poszczególnych użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="f9772-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="f9772-120">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="f9772-120">Select **OK**.</span></span>
* <span data-ttu-id="f9772-121">W oknie **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f9772-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f9772-122">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f9772-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f9772-123">Otwórz Terminal.</span><span class="sxs-lookup"><span data-stu-id="f9772-123">Open the terminal.</span></span>  <span data-ttu-id="f9772-124">Aby uzyskać Visual Studio Code można otworzyć [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f9772-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="f9772-125">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="f9772-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="f9772-126">W systemie Windows uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f9772-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="f9772-127">W przypadku systemów macOS i Linux Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f9772-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="f9772-128">`dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="f9772-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="f9772-129">`-au Individual` Tworzy kod dla indywidualnego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f9772-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="f9772-130">`-uld` używa LocalDB, uproszczonej wersji SQL Server Express dla systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="f9772-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="f9772-131">Pomiń `-uld` Korzystanie z oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="f9772-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="f9772-132">`code`Polecenie otwiera folder *WebApp1* w nowym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f9772-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="f9772-133">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="f9772-133">Apply migrations</span></span>

* <span data-ttu-id="f9772-134">Uruchom aplikację i wybierz łącze **zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="f9772-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="f9772-135">Wprowadź adres e-mail i hasło dla nowego konta, a następnie wybierz pozycję **zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="f9772-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="f9772-136">Postępuj zgodnie z instrukcjami, aby zastosować migracje.</span><span class="sxs-lookup"><span data-stu-id="f9772-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="f9772-137">Używanie klucza tajnego do przechowywania tokenów przypisanych przez dostawców logowania</span><span class="sxs-lookup"><span data-stu-id="f9772-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="f9772-138">Dostawcy logowania społecznościowego przypisują **tokeny** i **identyfikatory aplikacji** podczas procesu rejestracji.</span><span class="sxs-lookup"><span data-stu-id="f9772-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="f9772-139">Dokładne nazwy tokenów różnią się w zależności od dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f9772-139">The exact token names vary by provider.</span></span> <span data-ttu-id="f9772-140">Te tokeny reprezentują poświadczenia używane przez aplikację w celu uzyskania dostępu do interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="f9772-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="f9772-141">Tokeny stanowią "wpisy tajne", które mogą być połączone z konfiguracją aplikacji za pomocą [Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="f9772-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="f9772-142">Program Secret Manager jest bezpieczniejszym rozwiązaniem do przechowywania tokenów w pliku konfiguracyjnym, takim jak *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f9772-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f9772-143">Menedżer wpisów tajnych służy tylko do celów deweloperskich.</span><span class="sxs-lookup"><span data-stu-id="f9772-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="f9772-144">Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="f9772-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="f9772-145">Postępuj zgodnie z instrukcjami [dotyczącymi bezpiecznego przechowywania wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core](xref:security/app-secrets) tematu, aby przechowywać tokeny przypisane przez każdego dostawcę logowania poniżej.</span><span class="sxs-lookup"><span data-stu-id="f9772-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="f9772-146">Konfigurowanie dostawców logowania wymaganych przez aplikację</span><span class="sxs-lookup"><span data-stu-id="f9772-146">Setup login providers required by your application</span></span>

<span data-ttu-id="f9772-147">Poniższe tematy zawierają informacje dotyczące konfigurowania aplikacji do korzystania z odpowiednich dostawców:</span><span class="sxs-lookup"><span data-stu-id="f9772-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="f9772-148">Instrukcje [serwisu Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="f9772-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="f9772-149">Instrukcje dla usługi [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="f9772-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="f9772-150">Instrukcje [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="f9772-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="f9772-151">Instrukcje [firmy Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="f9772-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="f9772-152">[Inne](xref:security/authentication/otherlogins) instrukcje dotyczące dostawcy</span><span class="sxs-lookup"><span data-stu-id="f9772-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="f9772-153">Opcjonalnie Ustaw hasło</span><span class="sxs-lookup"><span data-stu-id="f9772-153">Optionally set password</span></span>

<span data-ttu-id="f9772-154">Gdy zarejestrujesz się przy użyciu zewnętrznego dostawcy logowania, nie masz hasła zarejestrowanego w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f9772-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="f9772-155">Pozwala to uniknąć tworzenia i zapamiętywania hasła dla lokacji, ale również od zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="f9772-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="f9772-156">Jeśli dostawca logowania zewnętrznego jest niedostępny, nie będzie można zalogować się do witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f9772-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="f9772-157">Aby utworzyć hasło i zalogować się przy użyciu poczty e-mail, która została ustawiona w procesie logowania z zewnętrznymi dostawcami:</span><span class="sxs-lookup"><span data-stu-id="f9772-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="f9772-158">Wybierz link \*\*Hello &lt; email alias &gt; \*\* w prawym górnym rogu, aby przejść do widoku **zarządzania** .</span><span class="sxs-lookup"><span data-stu-id="f9772-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Widok zarządzania aplikacjami sieci Web](index/_static/pass1a.png)

* <span data-ttu-id="f9772-160">Wybierz pozycję **Utwórz**</span><span class="sxs-lookup"><span data-stu-id="f9772-160">Select **Create**</span></span>

![Ustawianie strony hasła](index/_static/pass2a.png)

* <span data-ttu-id="f9772-162">Ustaw prawidłowe hasło i możesz go użyć do zalogowania się za pomocą poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="f9772-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f9772-163">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="f9772-163">Next steps</span></span>

* <span data-ttu-id="f9772-164">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/10563) w usłudze GitHub, aby uzyskać informacje na temat dostosowywania przycisków logowania.</span><span class="sxs-lookup"><span data-stu-id="f9772-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="f9772-165">W tym artykule wprowadzono uwierzytelnianie zewnętrzne i wyjaśniono wymagania wstępne wymagane do dodania zewnętrznych logowań do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9772-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="f9772-166">Odwołuje się do stron specyficznych dla dostawcy, aby skonfigurować logowania dla dostawców wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="f9772-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="f9772-167">Możesz chcieć utrzymać dodatkowe dane dotyczące użytkownika oraz tokeny dostępu i odświeżania.</span><span class="sxs-lookup"><span data-stu-id="f9772-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="f9772-168">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="f9772-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

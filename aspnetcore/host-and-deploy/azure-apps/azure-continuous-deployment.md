---
title: Ciągłe wdrażanie na platformie Azure za pomocą programu Visual Studio i rozwiązania Git na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację internetową ASP.NET Core przy użyciu programu Visual Studio i wdrożyć ją do Azure App Service przy użyciu narzędzia Git do ciągłego wdrażania.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 0b9f5f15403c7d18d68a41baa2a59582cf039f44
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850568"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="5ea9d-103">Ciągłe wdrażanie na platformie Azure za pomocą programu Visual Studio i rozwiązania Git na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ea9d-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="5ea9d-104">Autor [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="5ea9d-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="5ea9d-105">W tym samouczku pokazano, jak utworzyć aplikację internetową ASP.NET Core przy użyciu programu Visual Studio i wdrożyć ją z poziomu programu Visual Studio w celu Azure App Service korzystania z ciągłego wdrażania.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="5ea9d-106">Zobacz również [Tworzenie pierwszego potoku za pomocą Azure Pipelines](/azure/devops/pipelines/get-started-yaml), który pokazuje, jak skonfigurować przepływ pracy ciągłego dostarczania (CD) dla [Azure App Service](/azure/app-service/app-service-web-overview) przy użyciu Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="5ea9d-107">Azure Pipelines (usługa Azure DevOps Services) upraszcza Konfigurowanie niezawodnego potoku wdrażania w celu publikowania aktualizacji dla aplikacji hostowanych w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="5ea9d-108">Potok można skonfigurować z poziomu Azure Portal, aby kompilować, uruchamiać testy, wdrażać w miejscu przejściowym, a następnie wdrażać je w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="5ea9d-109">Do ukończenia tego samouczka wymagane jest konto Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="5ea9d-110">Aby uzyskać konto, [Aktywuj korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) lub [zarejestruj się w celu uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="5ea9d-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5ea9d-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5ea9d-111">Prerequisites</span></span>

<span data-ttu-id="5ea9d-112">W tym samouczku założono, że zainstalowano następujące oprogramowanie:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="5ea9d-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ea9d-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="5ea9d-114">[Git](https://git-scm.com/downloads) dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="5ea9d-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="5ea9d-115">Tworzenie aplikacji internetowej ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ea9d-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="5ea9d-116">Uruchom program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="5ea9d-117">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="5ea9d-118">Wybierz szablon projektu **aplikacji sieci Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="5ea9d-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="5ea9d-119">Jest on wyświetlany w obszarze **zainstalowane** > **Szablony** > **Visual C#** > **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="5ea9d-120">Nadaj nazwę projektowi `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="5ea9d-121">Wybierz opcję **Utwórz nowe repozytorium git** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Okno dialogowe Nowy projekt](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="5ea9d-123">W oknie dialogowym **Nowy projekt ASP.NET Core** wybierz ASP.NET Core **pusty** szablon, a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Nowe okno dialogowe projektu ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="5ea9d-125">Najnowsza wersja platformy .NET Core to 2,0.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="5ea9d-126">Lokalne uruchamianie aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="5ea9d-126">Running the web app locally</span></span>

1. <span data-ttu-id="5ea9d-127">Po zakończeniu tworzenia aplikacji przez program Visual Studio Uruchom aplikację, wybierając pozycję **Debuguj** > **Rozpocznij debugowanie**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="5ea9d-128">Alternatywnie naciśnij klawisz **F5**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="5ea9d-129">Zainicjowanie programu Visual Studio i nowej aplikacji może zająć trochę czasu.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="5ea9d-130">Po zakończeniu przeglądarka wyświetli działającą aplikację.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-130">Once it's complete, the browser shows the running app.</span></span>

   ![Okno przeglądarki z uruchomioną aplikacją, która wyświetla "Hello world!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="5ea9d-132">Po przejrzeniu działającej aplikacji sieci Web zamknij przeglądarkę i wybierz ikonę "Zatrzymaj debugowanie" na pasku narzędzi programu Visual Studio, aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="5ea9d-133">Tworzenie aplikacji sieci Web w witrynie Azure Portal</span><span class="sxs-lookup"><span data-stu-id="5ea9d-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="5ea9d-134">Poniższe kroki tworzą aplikację sieci Web w witrynie Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="5ea9d-135">Zaloguj się do witryny [Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="5ea9d-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="5ea9d-136">Wybierz pozycję **Nowy** w lewym górnym rogu interfejsu portalu.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="5ea9d-137">Wybierz pozycję **Sieć Web + aplikacje mobilne** > **aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Portal Microsoft Azure: nowy przycisk: Sieć Web + aplikacje mobilne na rynku Marketplace: przycisk aplikacji sieci Web w obszarze Polecane aplikacje](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="5ea9d-139">W bloku **aplikacja sieci Web** wprowadź unikatową wartość dla **nazwy App Service**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Blok aplikacji sieci Web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="5ea9d-141">Nazwa **App Service nazwa** musi być unikatowa.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="5ea9d-142">Portal wymusza tę regułę w przypadku podanej nazwy.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="5ea9d-143">W przypadku podania innej wartości należy zastąpić tę wartość dla każdego wystąpienia **SampleWebAppDemo** w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="5ea9d-144">W bloku **aplikacja sieci Web** wybierz istniejący **Plan/lokalizację App Service** lub Utwórz nowy.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="5ea9d-145">W przypadku tworzenia nowego planu wybierz warstwę cenową, lokalizację i inne opcje.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="5ea9d-146">Więcej informacji o planach App Service można znaleźć [w temacie Azure App Service planach szczegółowych](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="5ea9d-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="5ea9d-147">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-147">Select **Create**.</span></span> <span data-ttu-id="5ea9d-148">Na platformie Azure zostanie zainicjowana i uruchomiona aplikacja internetowa.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-148">Azure will provision and start the web app.</span></span>

   ![Azure Portal: przykładowy Przykładowa aplikacja internetowa — Demonstracja 01 — blok](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="5ea9d-150">Włącz publikowanie git dla nowej aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="5ea9d-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="5ea9d-151">Git to rozproszony system kontroli wersji, którego można użyć do wdrożenia aplikacji sieci Web Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="5ea9d-152">Kod aplikacji sieci Web jest przechowywany w lokalnym repozytorium git, a kod jest wdrażany na platformie Azure przez wypychanie do repozytorium zdalnego.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="5ea9d-153">Zaloguj się do witryny [Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="5ea9d-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="5ea9d-154">Wybierz pozycję **App Services** , aby wyświetlić listę usług aplikacji skojarzonych z subskrypcją platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="5ea9d-155">Wybierz aplikację sieci Web utworzoną w poprzedniej sekcji tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="5ea9d-156">W bloku **wdrożenie** wybierz pozycję **Opcje** > wdrażania**Wybierz pozycję źródłowe** > **lokalne repozytorium git**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Blok ustawień: blok źródła wdrożenia: Wybierz blok źródłowy](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="5ea9d-158">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-158">Select **OK**.</span></span>

1. <span data-ttu-id="5ea9d-159">Jeśli nie skonfigurowano wcześniej poświadczeń wdrażania do publikowania aplikacji internetowej lub innej aplikacji App Service, skonfiguruj je teraz:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="5ea9d-160">Wybierz pozycję **Ustawienia** > **poświadczenia wdrożenia**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="5ea9d-161">Zostanie wyświetlony blok **Ustawianie poświadczeń wdrożenia** .</span><span class="sxs-lookup"><span data-stu-id="5ea9d-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="5ea9d-162">Utwórz nazwę użytkownika i hasło.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-162">Create a user name and password.</span></span> <span data-ttu-id="5ea9d-163">Zapisz hasło do późniejszego użycia podczas konfigurowania usługi git.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="5ea9d-164">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-164">Select **Save**.</span></span>

1. <span data-ttu-id="5ea9d-165">W bloku **aplikacja sieci Web** wybierz pozycję **Ustawienia** > **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="5ea9d-166">Adres URL zdalnego repozytorium git do wdrożenia zostanie wyświetlony w obszarze **adres URL usługi git**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="5ea9d-167">Skopiuj wartość **adresu URL usługi git** do późniejszego użycia w samouczku.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure Portal: blok właściwości aplikacji](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="5ea9d-169">Publikowanie aplikacji internetowej w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5ea9d-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="5ea9d-170">W tej sekcji Utwórz lokalne repozytorium git przy użyciu programu Visual Studio i wypchnij z tego repozytorium do platformy Azure, aby wdrożyć aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="5ea9d-171">Obejmują one następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-171">The steps involved include the following:</span></span>

* <span data-ttu-id="5ea9d-172">Dodaj ustawienie repozytorium zdalne przy użyciu wartości adresu URL usługi GIT, aby można było wdrożyć repozytorium lokalne na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="5ea9d-173">Zatwierdź zmiany projektu.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-173">Commit project changes.</span></span>
* <span data-ttu-id="5ea9d-174">Wypychanie zmian projektu z repozytorium lokalnego do zdalnego repozytorium na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="5ea9d-175">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy **rozwiązanie "SampleWebAppDemo"** i wybierz pozycję **Zatwierdź**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="5ea9d-176">Zostanie wyświetlone okno **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-176">The **Team Explorer** is displayed.</span></span>

   ![Karta Team Explorer Connect](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="5ea9d-178">W **Team Explorer**wybierz pozycję **Strona główna** (ikona główna) > **Settings** > **Ustawienia repozytorium**ustawień.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="5ea9d-179">W sekcji **Elementy zdalne** obszaru **Ustawienia repozytorium** wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="5ea9d-180">Zostanie wyświetlone okno dialogowe **Dodawanie elementu zdalnego**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="5ea9d-181">Ustaw **nazwę** zdalnego na **Azure-SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="5ea9d-182">Ustaw wartość w polu **Pobierz** na **adres URL usługi git** , który został skopiowany z platformy Azure wcześniej w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="5ea9d-183">Należy zauważyć, że jest to adres URL kończący się na **. git**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Edytowanie okna dialogowego zdalnego](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="5ea9d-185">Alternatywnie należy określić repozytorium zdalne z **okna polecenia** , otwierając **okno wiersza polecenia**, zmieniając katalog projektu i wprowadzając polecenie.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="5ea9d-186">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="5ea9d-187">Wybierz pozycję **Strona główna** (ikona główna) ustawienia > **Ustawienia** > **globalne**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="5ea9d-188">Upewnij się, że nazwa i adres e-mail zostały ustawione.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="5ea9d-189">W razie potrzeby wybierz pozycję **Aktualizuj** .</span><span class="sxs-lookup"><span data-stu-id="5ea9d-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="5ea9d-190">Wybierz pozycję**zmiany** **Główne** > , aby powrócić do widoku **zmian** .</span><span class="sxs-lookup"><span data-stu-id="5ea9d-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="5ea9d-191">Wprowadź wiadomość dotyczącą zatwierdzenia, taką jak **początkowa #1 wypychania** i wybierz pozycję **Zatwierdź**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="5ea9d-192">Ta akcja tworzy *zatwierdzenie* lokalnie.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-192">This action creates a *commit* locally.</span></span>

   ![Karta Team Explorer Connect](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="5ea9d-194">Alternatywnie Zatwierdź zmiany z **okna polecenia** , otwierając **okno poleceń**, zmieniając katalog projektu i wprowadzając polecenia git.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="5ea9d-195">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="5ea9d-196">Wybierz **Home** > pozycję > **Akcje\*\*\*\*synchronizacji** > głównej**Otwórz wiersz polecenia**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="5ea9d-197">Zostanie otwarty wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="5ea9d-198">Wprowadź następujące polecenie w oknie polecenia:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="5ea9d-199">Wprowadź hasło **poświadczeń wdrożenia** platformy Azure utworzone wcześniej na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="5ea9d-200">To polecenie uruchamia proces wypychania plików projektu lokalnego do platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="5ea9d-201">Dane wyjściowe powyższego polecenia kończą się komunikatem, że wdrożenie zakończyło się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="5ea9d-202">Jeśli wymagana jest współpraca w projekcie, przed rozpoczęciem wypychania do platformy Azure Rozważ przeprowadzenie wypychania do serwisu [GitHub](https://github.com) .</span><span class="sxs-lookup"><span data-stu-id="5ea9d-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="5ea9d-203">Weryfikowanie aktywnego wdrożenia</span><span class="sxs-lookup"><span data-stu-id="5ea9d-203">Verify the Active Deployment</span></span>

<span data-ttu-id="5ea9d-204">Sprawdź, czy transfer aplikacji sieci Web ze środowiska lokalnego na platformę Azure zakończył się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="5ea9d-205">W [witrynie Azure Portal](https://portal.azure.com)wybierz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="5ea9d-206">Wybierz pozycję**Opcje wdrażania** **wdrożenia** > .</span><span class="sxs-lookup"><span data-stu-id="5ea9d-206">Select **Deployment** > **Deployment options**.</span></span>

![Witryna Azure Portal: blok ustawień: blok wdrożenia przedstawiający pomyślne wdrożenie](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="5ea9d-208">Uruchamianie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="5ea9d-208">Run the app in Azure</span></span>

<span data-ttu-id="5ea9d-209">Teraz, gdy aplikacja sieci Web jest wdrożona na platformie Azure, uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="5ea9d-210">Można to zrobić na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="5ea9d-211">W witrynie Azure Portal Znajdź blok aplikacji sieci Web dla aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="5ea9d-212">Wybierz pozycję **Przeglądaj** , aby wyświetlić aplikację w domyślnej przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="5ea9d-213">Otwórz przeglądarkę i wprowadź adres URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="5ea9d-214">Przykład: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="5ea9d-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="5ea9d-215">Aktualizowanie aplikacji sieci Web i ponowne publikowanie</span><span class="sxs-lookup"><span data-stu-id="5ea9d-215">Update the web app and republish</span></span>

<span data-ttu-id="5ea9d-216">Po wprowadzeniu zmian w kodzie lokalnym należy ponownie opublikować:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="5ea9d-217">W **Eksplorator rozwiązań** programu Visual Studio otwórz plik *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="5ea9d-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="5ea9d-218">W `Configure` metodzie zmodyfikuj `Response.WriteAsync` metodę, tak aby była wyświetlana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="5ea9d-219">Zapisz zmiany w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="5ea9d-220">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy **rozwiązanie "SampleWebAppDemo"** i wybierz pozycję **Zatwierdź**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="5ea9d-221">Zostanie wyświetlone okno **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="5ea9d-222">Wprowadź komunikat dotyczący zatwierdzenia, taki `Update #2`jak.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="5ea9d-223">Naciśnij przycisk **Zatwierdź** , aby zatwierdzić zmiany projektu.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="5ea9d-224">Wybierz **Home** > pozycję > **Akcje\*\*\*\*synchronizacji** > narzędzia**wypychanie**.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="5ea9d-225">Alternatywnie wypchnij zmiany z **okna polecenia** , otwierając **okno wiersza polecenia**, zmieniając katalog projektu i wprowadzając polecenie git.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="5ea9d-226">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ea9d-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="5ea9d-227">Wyświetlanie zaktualizowanej aplikacji sieci Web na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="5ea9d-227">View the updated web app in Azure</span></span>

<span data-ttu-id="5ea9d-228">Aby wyświetlić zaktualizowaną aplikację sieci Web, wybierz pozycję **Przeglądaj** w bloku aplikacji sieci Web w witrynie Azure Portal lub przez otwarcie przeglądarki i wprowadzenie adresu URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5ea9d-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="5ea9d-229">Przykład: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="5ea9d-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5ea9d-230">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5ea9d-230">Additional resources</span></span>

* [<span data-ttu-id="5ea9d-231">Tworzenie pierwszego potoku za pomocą Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="5ea9d-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="5ea9d-232">Projekt Kudu</span><span class="sxs-lookup"><span data-stu-id="5ea9d-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>

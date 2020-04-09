---
title: Ciągłe wdrażanie na platformie Azure za pomocą programu Visual Studio i rozwiązania Git na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację sieci web ASP.NET Core za pomocą programu Visual Studio i wdrożyć ją w usłudze Azure App Service przy użyciu git do ciągłego wdrażania.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3b344505739bb4292ed1683c73ff314b6e4e01e9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660854"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="71a6b-103">Ciągłe wdrażanie na platformie Azure za pomocą programu Visual Studio i rozwiązania Git na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71a6b-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="71a6b-104">Przez [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="71a6b-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="71a6b-105">W tym samouczku pokazano, jak utworzyć aplikację sieci web ASP.NET Core przy użyciu programu Visual Studio i wdrożyć ją z programu Visual Studio do usługi Azure App Service przy użyciu ciągłego wdrażania.</span><span class="sxs-lookup"><span data-stu-id="71a6b-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="71a6b-106">Zobacz też [Tworzenie pierwszego potoku za pomocą usługi Azure Pipelines](/azure/devops/pipelines/get-started-yaml), który pokazuje, jak skonfigurować przepływ pracy dostarczania ciągłego (CD) dla [usługi Azure App Service](/azure/app-service/app-service-web-overview) przy użyciu usług Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="71a6b-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="71a6b-107">Potoki platformy Azure (usługa Azure DevOps Services) upraszczają konfigurowanie niezawodnego potoku wdrażania w celu publikowania aktualizacji dla aplikacji hostowanych w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="71a6b-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="71a6b-108">Potok można skonfigurować z witryny Azure Portal do tworzenia, uruchamiania testów, wdrażania w miejscu przejściowym, a następnie wdrażania w środowiskach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="71a6b-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="71a6b-109">Aby ukończyć ten samouczek, wymagane jest konto platformy Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="71a6b-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="71a6b-110">Aby uzyskać konto, [aktywuj korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) lub [zarejestruj się, aby skorzystać z bezpłatnej wersji próbnej.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)</span><span class="sxs-lookup"><span data-stu-id="71a6b-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="71a6b-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="71a6b-111">Prerequisites</span></span>

<span data-ttu-id="71a6b-112">W tym samouczku przyjęto założenie, że jest zainstalowane następujące oprogramowanie:</span><span class="sxs-lookup"><span data-stu-id="71a6b-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="71a6b-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71a6b-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="71a6b-114">[Git](https://git-scm.com/downloads) dla Systemu Windows</span><span class="sxs-lookup"><span data-stu-id="71a6b-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="71a6b-115">Tworzenie aplikacji internetowej ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71a6b-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="71a6b-116">Uruchom program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="71a6b-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="71a6b-117">Z menu **Plik** wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="71a6b-118">Wybierz szablon projektu **ASP.NET Core Web Application.**</span><span class="sxs-lookup"><span data-stu-id="71a6b-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="71a6b-119">Pojawia się w obszarze **Zainstalowane** > **szablony** > **Visual C#** > **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="71a6b-120">Nazwij `SampleWebAppDemo`projekt .</span><span class="sxs-lookup"><span data-stu-id="71a6b-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="71a6b-121">Wybierz opcję **Utwórz nowe repozytorium Git** i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Okno dialogowe Nowy projekt](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="71a6b-123">W oknie dialogowym **Nowy ASP.NET Podstawowy projekt** wybierz ASP.NET **szablonu Puste rdzenie,** a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Nowe okno dialogowe projektu ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="71a6b-125">Najnowsza wersja .NET Core to 2.0.</span><span class="sxs-lookup"><span data-stu-id="71a6b-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="71a6b-126">Lokalne uruchamianie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="71a6b-126">Running the web app locally</span></span>

1. <span data-ttu-id="71a6b-127">Po zakończeniu tworzenia aplikacji przez program Visual Studio uruchom aplikację, wybierając pozycję **Debugowanie** > **startowe debugowania**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="71a6b-128">Alternatywnie naciśnij klawisz **F5**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="71a6b-129">Zainicjowanie programu Visual Studio i nowej aplikacji może zająć trochę czasu.</span><span class="sxs-lookup"><span data-stu-id="71a6b-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="71a6b-130">Po jego zakończeniu przeglądarka pokazuje uruchomiającą aplikację.</span><span class="sxs-lookup"><span data-stu-id="71a6b-130">Once it's complete, the browser shows the running app.</span></span>

   ![Okno przeglądarki z uruchomiona aplikacją wyświetlającego "Hello World!".](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="71a6b-132">Po przejrzeniu uruchomionej aplikacji sieci Web zamknij przeglądarkę i wybierz ikonę "Zatrzymaj debugowanie" na pasku narzędzi programu Visual Studio, aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="71a6b-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="71a6b-133">Tworzenie aplikacji sieci Web w witrynie Azure Portal</span><span class="sxs-lookup"><span data-stu-id="71a6b-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="71a6b-134">Następujące kroki tworzenia aplikacji sieci web w witrynie Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="71a6b-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="71a6b-135">Zaloguj się do [portalu Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="71a6b-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="71a6b-136">Wybierz **POZYCJĘ NOWY** w lewym górnym rogu interfejsu portalu.</span><span class="sxs-lookup"><span data-stu-id="71a6b-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="71a6b-137">Wybierz **pozycję Web + Mobile** > **Web App**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure Portal: Nowy przycisk: Sieć Web + Mobile w obszarze Marketplace: Przycisk aplikacji sieci Web w obszarze Polecane aplikacje](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="71a6b-139">W bloku **aplikacji sieci Web** wprowadź unikatową wartość nazwy usługi **app**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Blok aplikacji sieci Web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="71a6b-141">Nazwa **usługi aplikacji** musi być unikatowa.</span><span class="sxs-lookup"><span data-stu-id="71a6b-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="71a6b-142">Portal wymusza tę regułę, gdy nazwa jest podana.</span><span class="sxs-lookup"><span data-stu-id="71a6b-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="71a6b-143">Jeśli podasz inną wartość, zastąp tę wartość dla każdego wystąpienia **SampleWebAppDemo** w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="71a6b-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="71a6b-144">Również w bloku **aplikacji sieci Web** wybierz istniejący plan usługi **aplikacji/lokalizację** lub utwórz nowy.</span><span class="sxs-lookup"><span data-stu-id="71a6b-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="71a6b-145">Jeśli tworzysz nowy plan, wybierz warstwę cenową, lokalizację i inne opcje.</span><span class="sxs-lookup"><span data-stu-id="71a6b-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="71a6b-146">Aby uzyskać więcej informacji na temat planów usługi App Service, zobacz [szczegółowe omówienie planów usługi Azure App Service.](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)</span><span class="sxs-lookup"><span data-stu-id="71a6b-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="71a6b-147">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-147">Select **Create**.</span></span> <span data-ttu-id="71a6b-148">Platforma Azure będzie aprowizować i uruchomić aplikację sieci web.</span><span class="sxs-lookup"><span data-stu-id="71a6b-148">Azure will provision and start the web app.</span></span>

   ![Usługa Azure Portal: przykładowy blok Podstawowych wersji aplikacji sieci Web 01](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="71a6b-150">Włącz publikowanie git dla nowej aplikacji sieci web</span><span class="sxs-lookup"><span data-stu-id="71a6b-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="71a6b-151">Git to rozproszony system kontroli wersji, który może służyć do wdrażania aplikacji sieci web usługi Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="71a6b-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="71a6b-152">Kod aplikacji sieci Web jest przechowywany w lokalnym repozytorium Git, a kod jest wdrażany na platformie Azure przez wypychanie do zdalnego repozytorium.</span><span class="sxs-lookup"><span data-stu-id="71a6b-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="71a6b-153">Zaloguj się do [portalu Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="71a6b-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="71a6b-154">Wybierz **usługi aplikacji,** aby wyświetlić listę usług aplikacji skojarzonych z subskrypcją platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="71a6b-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="71a6b-155">Wybierz aplikację sieci web utworzoną w poprzedniej sekcji tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="71a6b-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="71a6b-156">W bloku **Wdrażanie** wybierz **pozycję Opcje** > wdrażania**Wybierz źródło** > **lokalne repozytorium Git**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Blok ustawień: Blok źródła wdrożenia: wybierz lemiesz źródłowy](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="71a6b-158">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-158">Select **OK**.</span></span>

1. <span data-ttu-id="71a6b-159">Jeśli poświadczenia wdrażania do publikowania aplikacji sieci web lub innej aplikacji usługi App Service nie zostały wcześniej skonfigurowane, skonfiguruj je teraz:</span><span class="sxs-lookup"><span data-stu-id="71a6b-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="71a6b-160">Wybierz **pozycję Poświadczenia** > **wdrażania**ustawień .</span><span class="sxs-lookup"><span data-stu-id="71a6b-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="71a6b-161">Zostanie wyświetlony **blok Ustaw poświadczenia wdrożenia.**</span><span class="sxs-lookup"><span data-stu-id="71a6b-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="71a6b-162">Utwórz nazwę użytkownika i hasło.</span><span class="sxs-lookup"><span data-stu-id="71a6b-162">Create a user name and password.</span></span> <span data-ttu-id="71a6b-163">Zapisz hasło do późniejszego użycia podczas konfigurowania Git.</span><span class="sxs-lookup"><span data-stu-id="71a6b-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="71a6b-164">Wybierz **pozycję Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-164">Select **Save**.</span></span>

1. <span data-ttu-id="71a6b-165">W bloku **Aplikacji sieci Web** wybierz pozycję**Właściwości** **ustawień** > .</span><span class="sxs-lookup"><span data-stu-id="71a6b-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="71a6b-166">Adres URL zdalnego repozytorium Git do wdrożenia jest wyświetlany pod **adresem URL GIT**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="71a6b-167">Skopiuj wartość **adresu URL GIT** do późniejszego użycia w samouczku.</span><span class="sxs-lookup"><span data-stu-id="71a6b-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure Portal: blok właściwości aplikacji](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="71a6b-169">Publikowanie aplikacji internetowej w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="71a6b-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="71a6b-170">W tej sekcji utwórz lokalne repozytorium Git przy użyciu programu Visual Studio i wypchnij z tego repozytorium na platformę Azure, aby wdrożyć aplikację sieci web.</span><span class="sxs-lookup"><span data-stu-id="71a6b-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="71a6b-171">Kroki, o które chodzi, są następujące:</span><span class="sxs-lookup"><span data-stu-id="71a6b-171">The steps involved include the following:</span></span>

* <span data-ttu-id="71a6b-172">Dodaj ustawienie zdalnego repozytorium przy użyciu wartości adresu URL GIT, aby lokalne repozytorium można było wdrożyć na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="71a6b-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="71a6b-173">Zatwierdzanie zmian w projekcie.</span><span class="sxs-lookup"><span data-stu-id="71a6b-173">Commit project changes.</span></span>
* <span data-ttu-id="71a6b-174">Wypychanie zmian projektu z repozytorium lokalnego do zdalnego repozytorium na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="71a6b-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="71a6b-175">W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy **pozycję Rozwiązanie "SampleWebAppDemo"** i wybierz pozycję **Zatwierdź**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="71a6b-176">Zostanie wyświetlone okno **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-176">The **Team Explorer** is displayed.</span></span>

   ![Karta Łączenie eksploratora zespołu](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="71a6b-178">W **Eksploratorze zespołu**wybierz pozycję **Strona główna** (ikona główna) >**ustawieniami repozytorium** **ustawień** > .</span><span class="sxs-lookup"><span data-stu-id="71a6b-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="71a6b-179">W sekcji **Elementy zdalne** obszaru **Ustawienia repozytorium** wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="71a6b-180">Zostanie wyświetlone okno dialogowe **Dodawanie elementu zdalnego**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="71a6b-181">Ustaw **nazwę** pilota na **Azure-SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="71a6b-182">Ustaw wartość **pobierania** na **adres URL Git,** który został skopiowany z platformy Azure wcześniej w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="71a6b-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="71a6b-183">Należy zauważyć, że jest to adres URL, który kończy się **na .git**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Okno dialogowe Edytowanie zdalnego](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="71a6b-185">Alternatywnie należy określić repozytorium zdalne z **okna polecenia,** otwierając **okno polecenia**, zmieniając katalog projektu i wprowadzając polecenie.</span><span class="sxs-lookup"><span data-stu-id="71a6b-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="71a6b-186">Przykład:</span><span class="sxs-lookup"><span data-stu-id="71a6b-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="71a6b-187">Wybierz **ikonę Strona główna** (ikona głównej) >**Ustawienia globalne** **ustawień** > .</span><span class="sxs-lookup"><span data-stu-id="71a6b-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="71a6b-188">Upewnij się, że nazwa i adres e-mail zostały ustawione.</span><span class="sxs-lookup"><span data-stu-id="71a6b-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="71a6b-189">W razie potrzeby wybierz **pozycję Aktualizuj.**</span><span class="sxs-lookup"><span data-stu-id="71a6b-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="71a6b-190">Wybierz **pozycję Zmiany w domu,** > **Changes** aby powrócić do widoku **Zmiany.**</span><span class="sxs-lookup"><span data-stu-id="71a6b-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="71a6b-191">Wprowadź komunikat zatwierdzenia, na przykład **początkowe #1 wypychania** i wybierz pozycję **Zatwierdź**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="71a6b-192">Ta akcja tworzy *zatwierdzenie* lokalnie.</span><span class="sxs-lookup"><span data-stu-id="71a6b-192">This action creates a *commit* locally.</span></span>

   ![Karta Łączenie eksploratora zespołu](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="71a6b-194">Alternatywnie zatwierdź zmiany z **okna polecenia,** otwierając **okno polecenia**, zmieniając katalog projektu i wprowadzając polecenia git.</span><span class="sxs-lookup"><span data-stu-id="71a6b-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="71a6b-195">Przykład:</span><span class="sxs-lookup"><span data-stu-id="71a6b-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="71a6b-196">Wybierz **pozycję Otwieranie** > **wiersza polecenia\*\*\*\*Akcji** > **synchronizacji** > domowej .</span><span class="sxs-lookup"><span data-stu-id="71a6b-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="71a6b-197">Wiersz polecenia zostanie otwarty w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="71a6b-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="71a6b-198">W oknie polecenia wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="71a6b-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="71a6b-199">Wprowadź hasło **poświadczeń wdrożenia** platformy Azure utworzone wcześniej na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="71a6b-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="71a6b-200">To polecenie rozpoczyna proces wypychania lokalnych plików projektu na platformę Azure.</span><span class="sxs-lookup"><span data-stu-id="71a6b-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="71a6b-201">Dane wyjściowe z powyższego polecenia kończy się komunikatem, że wdrożenie zakończyło się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="71a6b-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="71a6b-202">Jeśli wymagana jest współpraca nad projektem, należy rozważyć wypychanie do [usługi GitHub](https://github.com) przed wypychaniem na platformę Azure.</span><span class="sxs-lookup"><span data-stu-id="71a6b-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="71a6b-203">Sprawdź aktywne wdrażanie</span><span class="sxs-lookup"><span data-stu-id="71a6b-203">Verify the Active Deployment</span></span>

<span data-ttu-id="71a6b-204">Sprawdź, czy transfer aplikacji sieci web ze środowiska lokalnego na platformę Azure zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="71a6b-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="71a6b-205">W witrynie [Azure Portal](https://portal.azure.com)wybierz aplikację sieci web.</span><span class="sxs-lookup"><span data-stu-id="71a6b-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="71a6b-206">Wybierz**opcje wdrażania** **wdrożenia** > .</span><span class="sxs-lookup"><span data-stu-id="71a6b-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure Portal: Blok ustawień: blok wdrożeń z pomyślnym wdrożeniem](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="71a6b-208">Uruchamianie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="71a6b-208">Run the app in Azure</span></span>

<span data-ttu-id="71a6b-209">Teraz, gdy aplikacja sieci web zostanie wdrożona na platformie Azure, uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="71a6b-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="71a6b-210">Można to osiągnąć na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="71a6b-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="71a6b-211">W witrynie Azure Portal znajdź blok aplikacji sieci web dla aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="71a6b-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="71a6b-212">Wybierz **pozycję Przeglądaj,** aby wyświetlić aplikację w domyślnej przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="71a6b-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="71a6b-213">Otwórz przeglądarkę i wprowadź adres URL aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="71a6b-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="71a6b-214">Przykład: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="71a6b-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="71a6b-215">Aktualizowanie aplikacji sieci Web i ponowne publikowanie</span><span class="sxs-lookup"><span data-stu-id="71a6b-215">Update the web app and republish</span></span>

<span data-ttu-id="71a6b-216">Po wszczęciem zmian w kodzie lokalnym ponownie opublikuj:</span><span class="sxs-lookup"><span data-stu-id="71a6b-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="71a6b-217">W **Eksploratorze rozwiązań** programu Visual Studio otwórz plik *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="71a6b-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="71a6b-218">W `Configure` metodzie zmodyfikuj `Response.WriteAsync` metodę tak, aby była wyświetlana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="71a6b-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="71a6b-219">Zapisz zmiany w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="71a6b-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="71a6b-220">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję Rozwiązanie "SampleWebAppDemo"** i wybierz polecenie **Zatwierdź**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="71a6b-221">Zostanie wyświetlone okno **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="71a6b-222">Wprowadź komunikat zatwierdzania, `Update #2`na przykład .</span><span class="sxs-lookup"><span data-stu-id="71a6b-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="71a6b-223">Naciśnij przycisk **Zatwierdzj,** aby zatwierdzić zmiany projektu.</span><span class="sxs-lookup"><span data-stu-id="71a6b-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="71a6b-224">Wybierz **pozycję Push** > **Akcje** > **synchronizacji** > **domowej**.</span><span class="sxs-lookup"><span data-stu-id="71a6b-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="71a6b-225">Alternatywnie należy wypchnąć zmiany z **okna polecenia,** otwierając **okno polecenia**, zmieniając katalog projektu i wprowadzając polecenie git.</span><span class="sxs-lookup"><span data-stu-id="71a6b-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="71a6b-226">Przykład:</span><span class="sxs-lookup"><span data-stu-id="71a6b-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="71a6b-227">Wyświetlanie zaktualizowanej aplikacji sieci Web na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="71a6b-227">View the updated web app in Azure</span></span>

<span data-ttu-id="71a6b-228">Wyświetl zaktualizowaną aplikację sieci web, wybierając **pozycję Przeglądaj** z bloku aplikacji sieci Web w witrynie Azure Portal lub otwierając przeglądarkę i wprowadzając adres URL aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="71a6b-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="71a6b-229">Przykład: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="71a6b-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71a6b-230">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="71a6b-230">Additional resources</span></span>

* [<span data-ttu-id="71a6b-231">Tworzenie pierwszego potoku za pomocą usługi Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="71a6b-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="71a6b-232">Projekt Kudu</span><span class="sxs-lookup"><span data-stu-id="71a6b-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>

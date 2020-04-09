---
title: Ciągła integracja i wdrażanie — devops z ASP.NET Core i azure
author: CamSoper
description: Ciągła integracja i wdrażanie w programach DevOps dzięki ASP.NET Core i platformie Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655835"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="11d70-103">Ciągła integracja i ciągłe wdrażanie</span><span class="sxs-lookup"><span data-stu-id="11d70-103">Continuous integration and deployment</span></span>

<span data-ttu-id="11d70-104">W poprzednim rozdziale utworzono lokalne repozytorium Git dla aplikacji Simple Feed Reader.</span><span class="sxs-lookup"><span data-stu-id="11d70-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="11d70-105">W tym rozdziale opublikujesz ten kod w repozytorium Usługi GitHub i skonstruujesz potok usług Azure DevOps przy użyciu potoków platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="11d70-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="11d70-106">Potok umożliwia ciągłe kompilacje i wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="11d70-107">Każde zatwierdzenie do repozytorium GitHub wyzwala kompilację i wdrożenie w miejscu przejściowym aplikacji Azure Web App.</span><span class="sxs-lookup"><span data-stu-id="11d70-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="11d70-108">W tej sekcji wykonasz następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="11d70-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="11d70-109">Publikowanie kodu aplikacji w usłudze GitHub</span><span class="sxs-lookup"><span data-stu-id="11d70-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="11d70-110">Odłączanie lokalnego wdrożenia git</span><span class="sxs-lookup"><span data-stu-id="11d70-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="11d70-111">Tworzenie organizacji usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="11d70-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="11d70-112">Tworzenie projektu zespołowego w usługach Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="11d70-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="11d70-113">Tworzenie definicji kompilacji</span><span class="sxs-lookup"><span data-stu-id="11d70-113">Create a build definition</span></span>
* <span data-ttu-id="11d70-114">Tworzenie potoku wydania</span><span class="sxs-lookup"><span data-stu-id="11d70-114">Create a release pipeline</span></span>
* <span data-ttu-id="11d70-115">Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="11d70-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="11d70-116">Sprawdzanie potoku potoku azure</span><span class="sxs-lookup"><span data-stu-id="11d70-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="11d70-117">Publikowanie kodu aplikacji w usłudze GitHub</span><span class="sxs-lookup"><span data-stu-id="11d70-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="11d70-118">Otwórz okno przeglądarki i `https://github.com`przejdź do pliku .</span><span class="sxs-lookup"><span data-stu-id="11d70-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="11d70-119">Kliknij **+** pozycję rozwijaną w nagłówku, a następnie wybierz pozycję **Nowe repozytorium:**</span><span class="sxs-lookup"><span data-stu-id="11d70-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub Nowa opcja repozytorium](media/cicd/github-new-repo.png)

1. <span data-ttu-id="11d70-121">Wybierz swoje konto z listy rozwijanej **Właściciel** i wprowadź *czytnik prostego kanału informacyjnego* w polach tekstowych **nazwa repozytorium.**</span><span class="sxs-lookup"><span data-stu-id="11d70-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="11d70-122">Kliknij przycisk **Utwórz repozytorium.**</span><span class="sxs-lookup"><span data-stu-id="11d70-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="11d70-123">Otwórz powłokę poleceń komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="11d70-123">Open your local machine's command shell.</span></span> <span data-ttu-id="11d70-124">Przejdź do katalogu, w którym przechowywane jest repozytorium Git *z prostym czytnikiem kanałów.*</span><span class="sxs-lookup"><span data-stu-id="11d70-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="11d70-125">Zmień nazwę istniejącego *pilota pochodzenia* na *nadrzędny*.</span><span class="sxs-lookup"><span data-stu-id="11d70-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="11d70-126">Wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="11d70-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="11d70-127">Dodaj nowy pilot *pochodzenia* wskazujący kopię repozytorium w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="11d70-128">Wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="11d70-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="11d70-129">Opublikuj lokalne repozytorium Git w nowo utworzonym repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="11d70-130">Wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="11d70-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="11d70-131">Otwórz okno przeglądarki i `https://github.com/<GitHub_username>/simple-feed-reader/`przejdź do pliku .</span><span class="sxs-lookup"><span data-stu-id="11d70-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="11d70-132">Sprawdź, czy kod jest wyświetlany w repozytorium Usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="11d70-133">Odłączanie lokalnego wdrożenia git</span><span class="sxs-lookup"><span data-stu-id="11d70-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="11d70-134">Usuń lokalne wdrożenie Git z następującymi krokami.</span><span class="sxs-lookup"><span data-stu-id="11d70-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="11d70-135">Usługi Azure Pipelines (usługa Azure DevOps) zastępuje i rozszerza tę funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="11d70-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="11d70-136">Otwórz [witrynę Azure portal](https://portal.azure.com/)i przejdź do *aplikacji przejściowej (mywebapp\<unique_number\>/staging).*</span><span class="sxs-lookup"><span data-stu-id="11d70-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="11d70-137">Aplikację sieci Web można szybko zlokalizować, wprowadzając *przemieszczania* w polu wyszukiwania portalu:</span><span class="sxs-lookup"><span data-stu-id="11d70-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![przemieszczania terminu wyszukiwania aplikacji sieci Web](media/cicd/portal-search-box.png)

1. <span data-ttu-id="11d70-139">Kliknij pozycję **Centrum wdrażania**.</span><span class="sxs-lookup"><span data-stu-id="11d70-139">Click **Deployment Center**.</span></span> <span data-ttu-id="11d70-140">Pojawi się nowy panel.</span><span class="sxs-lookup"><span data-stu-id="11d70-140">A new panel appears.</span></span> <span data-ttu-id="11d70-141">Kliknij **przycisk Rozłącz,** aby usunąć lokalną konfigurację kontroli źródła Git, która została dodana w poprzednim rozdziale.</span><span class="sxs-lookup"><span data-stu-id="11d70-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="11d70-142">Potwierdź operację usuwania, klikając przycisk **Tak.**</span><span class="sxs-lookup"><span data-stu-id="11d70-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="11d70-143">Przejdź do *usługi mywebapp<unique_number>* App Service.</span><span class="sxs-lookup"><span data-stu-id="11d70-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="11d70-144">Przypominamy, że pole wyszukiwania portalu może służyć do szybkiego zlokalizowania usługi app service.</span><span class="sxs-lookup"><span data-stu-id="11d70-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="11d70-145">Kliknij pozycję **Centrum wdrażania**.</span><span class="sxs-lookup"><span data-stu-id="11d70-145">Click **Deployment Center**.</span></span> <span data-ttu-id="11d70-146">Pojawi się nowy panel.</span><span class="sxs-lookup"><span data-stu-id="11d70-146">A new panel appears.</span></span> <span data-ttu-id="11d70-147">Kliknij **przycisk Rozłącz,** aby usunąć lokalną konfigurację kontroli źródła Git, która została dodana w poprzednim rozdziale.</span><span class="sxs-lookup"><span data-stu-id="11d70-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="11d70-148">Potwierdź operację usuwania, klikając przycisk **Tak.**</span><span class="sxs-lookup"><span data-stu-id="11d70-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="11d70-149">Tworzenie organizacji usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="11d70-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="11d70-150">Otwórz przeglądarkę i przejdź do [strony tworzenia organizacji Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="11d70-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="11d70-151">Wpisz unikatową nazwę w pola **tekstowym Wybierz nazwę z zapamiętywaniem,** aby utworzyć adres URL dostępu do organizacji Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="11d70-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="11d70-152">Wybierz przycisk opcji **Git,** ponieważ kod jest hostowany w repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="11d70-153">Kliknij przycisk **Kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="11d70-153">Click the **Continue** button.</span></span> <span data-ttu-id="11d70-154">Po krótkim oczekiwaniu tworzone jest konto i projekt zespołowy o nazwie *MyFirstProject.*</span><span class="sxs-lookup"><span data-stu-id="11d70-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Strona tworzenia organizacji Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="11d70-156">Otwórz wiadomość e-mail z potwierdzeniem wskazującą, że organizacja i projekt Azure DevOps są gotowe do użycia.</span><span class="sxs-lookup"><span data-stu-id="11d70-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="11d70-157">Kliknij przycisk **Rozpocznij projekt:**</span><span class="sxs-lookup"><span data-stu-id="11d70-157">Click the **Start your project** button:</span></span>

    ![Przycisk Rozpocznij projekt](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="11d70-159">Przeglądarka otwiera się \* \<account_name\>.visualstudio.com\*.</span><span class="sxs-lookup"><span data-stu-id="11d70-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="11d70-160">Kliknij łącze *MyFirstProject,* aby rozpocząć konfigurowanie potoku DevOps projektu.</span><span class="sxs-lookup"><span data-stu-id="11d70-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="11d70-161">Konfigurowanie potoku potoku azure</span><span class="sxs-lookup"><span data-stu-id="11d70-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="11d70-162">Istnieją trzy różne kroki do wykonania.</span><span class="sxs-lookup"><span data-stu-id="11d70-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="11d70-163">Wykonanie kroków w kolejnych trzech sekcjach powoduje operacyjne potok DevOps.</span><span class="sxs-lookup"><span data-stu-id="11d70-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="11d70-164">Udzielanie dostępu do usługi Azure DevOps do repozytorium Usługi GitHub</span><span class="sxs-lookup"><span data-stu-id="11d70-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="11d70-165">Rozwiń **kod lub zbuduj z zewnętrznego akordeonu repozytorium.**</span><span class="sxs-lookup"><span data-stu-id="11d70-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="11d70-166">Kliknij przycisk **Kompilacja instalacji:**</span><span class="sxs-lookup"><span data-stu-id="11d70-166">Click the **Setup Build** button:</span></span>

    ![Przycisk Kompilacja instalacji](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="11d70-168">Wybierz opcję **GitHub** z sekcji **Wybierz źródło:**</span><span class="sxs-lookup"><span data-stu-id="11d70-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Wybieranie źródła - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="11d70-170">Autoryzacja jest wymagana, zanim usługa Azure DevOps będzie mogła uzyskać dostęp do repozytorium Usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="11d70-171">Wprowadź *<GitHub_username> połączeniu GitHub* w polach tekstowych Nazwa **połączenia.**</span><span class="sxs-lookup"><span data-stu-id="11d70-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="11d70-172">Przykład:</span><span class="sxs-lookup"><span data-stu-id="11d70-172">For example:</span></span>

    ![Nazwa połączenia github](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="11d70-174">Jeśli uwierzytelnianie dwuskładnikowe jest włączone na koncie Usługi GitHub, wymagany jest token dostępu osobistego.</span><span class="sxs-lookup"><span data-stu-id="11d70-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="11d70-175">W takim przypadku kliknij link **Autoryzuj za pomocą osobistego tokenu dostępu GitHub.**</span><span class="sxs-lookup"><span data-stu-id="11d70-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="11d70-176">Aby uzyskać pomoc, zapoznaj się z [oficjalnymi instrukcjami tworzenia tokenów dostępu osobistego GitHub.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)</span><span class="sxs-lookup"><span data-stu-id="11d70-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="11d70-177">Potrzebne jest tylko zakres *uprawnień repozytorium.*</span><span class="sxs-lookup"><span data-stu-id="11d70-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="11d70-178">W przeciwnym razie kliknij przycisk **Autoryzuj przy użyciu OAuth.**</span><span class="sxs-lookup"><span data-stu-id="11d70-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="11d70-179">Po wyświetleniu monitu zaloguj się na swoje konto Usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="11d70-180">Następnie wybierz pozycję Autoryzuj, aby udzielić dostępu do organizacji Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="11d70-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="11d70-181">W przypadku powodzenia tworzony jest nowy punkt końcowy usługi.</span><span class="sxs-lookup"><span data-stu-id="11d70-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="11d70-182">Kliknij przycisk wielokropka obok przycisku **Repozytorium.**</span><span class="sxs-lookup"><span data-stu-id="11d70-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="11d70-183">Wybierz z listy repozytorium *<GitHub_username>/simple-feed-reader.*</span><span class="sxs-lookup"><span data-stu-id="11d70-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="11d70-184">Kliknij przycisk **Wybierz.**</span><span class="sxs-lookup"><span data-stu-id="11d70-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="11d70-185">Wybierz gałąź *główną* z **gałęzi Domyślna dla ręcznego i zaplanowanego tworzenia listy** rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="11d70-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="11d70-186">Kliknij przycisk **Kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="11d70-186">Click the **Continue** button.</span></span> <span data-ttu-id="11d70-187">Zostanie wyświetlona strona wyboru szablonu.</span><span class="sxs-lookup"><span data-stu-id="11d70-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="11d70-188">Tworzenie definicji kompilacji</span><span class="sxs-lookup"><span data-stu-id="11d70-188">Create the build definition</span></span>

1. <span data-ttu-id="11d70-189">Na stronie wyboru szablonu wprowadź *ASP.NET Core* w polu wyszukiwania:</span><span class="sxs-lookup"><span data-stu-id="11d70-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![wyszukiwanie ASP.NET Core na stronie szablonu](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="11d70-191">Zostaną wyświetlone wyniki wyszukiwania szablonu.</span><span class="sxs-lookup"><span data-stu-id="11d70-191">The template search results appear.</span></span> <span data-ttu-id="11d70-192">Umieść wskaźnik myszy na szablonie **ASP.NET Core** i kliknij przycisk **Zastosuj.**</span><span class="sxs-lookup"><span data-stu-id="11d70-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="11d70-193">Pojawi się karta **Zadania** definicji kompilacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="11d70-194">Kliknij kartę **Wyzwalacze.**</span><span class="sxs-lookup"><span data-stu-id="11d70-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="11d70-195">Zaznacz pole **Włącz ciągłą integrację.**</span><span class="sxs-lookup"><span data-stu-id="11d70-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="11d70-196">W sekcji **Filtry gałęzi** upewnij się, że z listy rozwijanej **Typ** jest ustawiona na *Uwzględnij*.</span><span class="sxs-lookup"><span data-stu-id="11d70-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="11d70-197">Ustaw z listy rozwijanej **Specyfikacja gałęzi** do *wzorca*.</span><span class="sxs-lookup"><span data-stu-id="11d70-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Włączanie ustawień ciągłej integracji](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="11d70-199">Te ustawienia powodują, że kompilacja wyzwalać, gdy wszelkie zmiany są wypychane do gałęzi *głównej* repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="11d70-200">Ciągła integracja jest testowana w sekcji [Zatwierdzanie zmian w usłudze GitHub i automatycznie wdrażana w](#commit-changes-to-github-and-automatically-deploy-to-azure) usłudze Azure.</span><span class="sxs-lookup"><span data-stu-id="11d70-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="11d70-201">Kliknij przycisk **Zapisz & kolejki,** a następnie wybierz opcję **Zapisz:**</span><span class="sxs-lookup"><span data-stu-id="11d70-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Przycisk Save (Zapisz)](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="11d70-203">Zostanie wyświetlone następujące modalne okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="11d70-203">The following modal dialog appears:</span></span>

    ![Zapisz definicję kompilacji — modalne okno dialogowe](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="11d70-205">Użyj folderu *\\*domyślnego programu i kliknij przycisk **Zapisz.**</span><span class="sxs-lookup"><span data-stu-id="11d70-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="11d70-206">Tworzenie potoku wydania</span><span class="sxs-lookup"><span data-stu-id="11d70-206">Create the release pipeline</span></span>

1. <span data-ttu-id="11d70-207">Kliknij kartę **Wersje** projektu zespołowego.</span><span class="sxs-lookup"><span data-stu-id="11d70-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="11d70-208">Kliknij przycisk **Nowy potok.**</span><span class="sxs-lookup"><span data-stu-id="11d70-208">Click the **New pipeline** button.</span></span>

    ![Karta Zwalnia — przycisk Nowa definicja](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="11d70-210">Zostanie wyświetlene okienko wyboru szablonu.</span><span class="sxs-lookup"><span data-stu-id="11d70-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="11d70-211">Na stronie wyboru szablonu wprowadź usługę *App Service* w polu wyszukiwania:</span><span class="sxs-lookup"><span data-stu-id="11d70-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Zwolnij pole wyszukiwania szablonu potoku](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="11d70-213">Zostaną wyświetlone wyniki wyszukiwania szablonu.</span><span class="sxs-lookup"><span data-stu-id="11d70-213">The template search results appear.</span></span> <span data-ttu-id="11d70-214">Umieść wskaźnik myszy na **wdrożeniu usługi Azure App Service za pomocą szablonu Slot** i kliknij przycisk **Zastosuj.**</span><span class="sxs-lookup"><span data-stu-id="11d70-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="11d70-215">Pojawi się karta **Potok** potoku wydania.</span><span class="sxs-lookup"><span data-stu-id="11d70-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Karta Potok potoku wydania](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="11d70-217">Kliknij przycisk **Dodaj** w polu **Artefakty.**</span><span class="sxs-lookup"><span data-stu-id="11d70-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="11d70-218">Pojawi się panel **Dodaj artefakt:**</span><span class="sxs-lookup"><span data-stu-id="11d70-218">The **Add artifact** panel appears:</span></span>

    ![Potok wydania — dodawanie panelu artefaktów](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="11d70-220">Wybierz kafelek **Kompilacja** z sekcji **Typ źródła.**</span><span class="sxs-lookup"><span data-stu-id="11d70-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="11d70-221">Ten typ umożliwia łączenie potoku wydania z definicją kompilacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="11d70-222">Wybierz *pozycję MyFirstProject* z listy rozwijanej **Projekt.**</span><span class="sxs-lookup"><span data-stu-id="11d70-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="11d70-223">Wybierz nazwę definicji kompilacji, *MyFirstProject-ASP.NET Core-CI*, z listy rozwijanej **Źródło (definicja kompilacji).**</span><span class="sxs-lookup"><span data-stu-id="11d70-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="11d70-224">Z listy rozwijanej **Wersja domyślna** wybierz pozycję *Najnowsze.*</span><span class="sxs-lookup"><span data-stu-id="11d70-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="11d70-225">Ta opcja tworzy artefakty produkowane przez najnowsze uruchomienie definicji kompilacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="11d70-226">Zastąp tekst w polach **tekstowych Alias źródłowy** *przycisku Upuść*.</span><span class="sxs-lookup"><span data-stu-id="11d70-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="11d70-227">Kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="11d70-227">Click the **Add** button.</span></span> <span data-ttu-id="11d70-228">Sekcja **Artefakty** zostanie zaktualizowana w celu wyświetlenia zmian.</span><span class="sxs-lookup"><span data-stu-id="11d70-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="11d70-229">Kliknij ikonę błyskawicy, aby włączyć ciągłe wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="11d70-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Zwolnij potok Artefakty - ikona błyskawicy](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="11d70-231">Po włączeniu tej opcji wdrożenie odbywa się za każdym razem, gdy dostępna jest nowa kompilacja.</span><span class="sxs-lookup"><span data-stu-id="11d70-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="11d70-232">Po prawej stronie pojawi się panel **wyzwalacza ciągłego wdrażania.**</span><span class="sxs-lookup"><span data-stu-id="11d70-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="11d70-233">Kliknij przycisk przełączania, aby włączyć operację.</span><span class="sxs-lookup"><span data-stu-id="11d70-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="11d70-234">Nie jest konieczne, aby włączyć **wyzwalacz żądania ściągania**.</span><span class="sxs-lookup"><span data-stu-id="11d70-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="11d70-235">Kliknij pozycję listy rozwijanej **Dodaj** w sekcji **Filtry gałęzi kompilacji.**</span><span class="sxs-lookup"><span data-stu-id="11d70-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="11d70-236">Wybierz **domyślną opcję gałęzi definicji kompilacji.**</span><span class="sxs-lookup"><span data-stu-id="11d70-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="11d70-237">Ten filtr powoduje, że wyzwalanie wersji tylko dla kompilacji z gałęzi *głównej* repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="11d70-238">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="11d70-238">Click the **Save** button.</span></span> <span data-ttu-id="11d70-239">Kliknij przycisk **OK** w wynikowym oknie dialogowym **Zapisz** modalne.</span><span class="sxs-lookup"><span data-stu-id="11d70-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="11d70-240">Kliknij pole **Środowisko 1.**</span><span class="sxs-lookup"><span data-stu-id="11d70-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="11d70-241">Po prawej stronie pojawi się panel **Środowisko.**</span><span class="sxs-lookup"><span data-stu-id="11d70-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="11d70-242">Zmień tekst *Środowiska 1* w pola tekstowym **Nazwa środowiska** na *Produkcja*.</span><span class="sxs-lookup"><span data-stu-id="11d70-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Potok wydania — pole tekstowe nazwa środowiska](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="11d70-244">Kliknij **łącze 1 faza, 2 zadania** w polu **Produkcja:**</span><span class="sxs-lookup"><span data-stu-id="11d70-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Potok wydania — środowisko produkcji link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="11d70-246">Pojawi się karta **Zadania** w środowisku.</span><span class="sxs-lookup"><span data-stu-id="11d70-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="11d70-247">Kliknij zadanie **Wdrażanie usługi Azure App Service w miejscu.**</span><span class="sxs-lookup"><span data-stu-id="11d70-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="11d70-248">Jego ustawienia są wyświetlane w panelu po prawej stronie.</span><span class="sxs-lookup"><span data-stu-id="11d70-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="11d70-249">Wybierz subskrypcję platformy Azure skojarzoną z usługą app service z listy rozwijanej **subskrypcji platformy Azure.**</span><span class="sxs-lookup"><span data-stu-id="11d70-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="11d70-250">Po wybraniu tej opcji kliknij przycisk **Autoryzuj.**</span><span class="sxs-lookup"><span data-stu-id="11d70-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="11d70-251">Wybierz *aplikację sieci Web* z listy rozwijanej Typ **aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="11d70-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="11d70-252">Z listy rozwijanej Nazwa **usługi aplikacji** *wybierz mywebapp/<unique_number/>.*</span><span class="sxs-lookup"><span data-stu-id="11d70-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="11d70-253">Wybierz *AzureTutorial* z listy rozwijanej **Grupa zasobów.**</span><span class="sxs-lookup"><span data-stu-id="11d70-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="11d70-254">Wybierz *tymczasową* z listy rozwijanej **Slot.**</span><span class="sxs-lookup"><span data-stu-id="11d70-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="11d70-255">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="11d70-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="11d70-256">Umieść wskaźnik myszy na domyślnej nazwie potoku wydania.</span><span class="sxs-lookup"><span data-stu-id="11d70-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="11d70-257">Kliknij ikonę ołówka, aby ją edytować.</span><span class="sxs-lookup"><span data-stu-id="11d70-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="11d70-258">Użyj *MyFirstProject-ASP.NET Core-CD* jako nazwy.</span><span class="sxs-lookup"><span data-stu-id="11d70-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nazwa potoku wydania](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="11d70-260">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="11d70-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="11d70-261">Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="11d70-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="11d70-262">Otwórz *simplefeedreader.sln* w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="11d70-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="11d70-263">W Eksploratorze rozwiązań otwórz *stronę Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="11d70-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="11d70-264">Zmień `<h2>Simple Feed Reader - V3</h2>` `<h2>Simple Feed Reader - V4</h2>`na .</span><span class="sxs-lookup"><span data-stu-id="11d70-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="11d70-265">Naciśnij **klawisze Ctrl**+**Shift**+**B,** aby utworzyć aplikację.</span><span class="sxs-lookup"><span data-stu-id="11d70-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="11d70-266">Zaagnaj plik do repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="11d70-267">Użyj strony **Zmiany** na karcie *Eksplorator zespołu* programu Visual Studio lub wykonaj następujące czynności przy użyciu powłoki poleceń komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="11d70-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="11d70-268">Wypchnij zmianę w gałęzi *głównej* do *źródła* zdalnego repozytorium GitHub:</span><span class="sxs-lookup"><span data-stu-id="11d70-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="11d70-269">Zatwierdzenie pojawia się w gałęzi *głównej* repozytorium GitHub:</span><span class="sxs-lookup"><span data-stu-id="11d70-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Zatwierdzanie gitHub w gałęzi głównej](media/cicd/github-commit.png)

    <span data-ttu-id="11d70-271">Kompilacja jest wyzwalana, ponieważ ciągła integracja jest włączona na karcie Wyzwalacze definicji **kompilacji:**</span><span class="sxs-lookup"><span data-stu-id="11d70-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![umożliwić ciągłą integrację](media/cicd/enable-ci.png)

1. <span data-ttu-id="11d70-273">Przejdź do karty **w kolejce** na stronie**Kompilacje** **potoków** > platformy Azure w usługach Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="11d70-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="11d70-274">Kompilacja w kolejce pokazuje gałąź i zatwierdzenie, które wyzwoliło kompilację:</span><span class="sxs-lookup"><span data-stu-id="11d70-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![kompilacja w kolejce](media/cicd/build-queued.png)

1. <span data-ttu-id="11d70-276">Po pomyślnym uruchomieniu kompilacji na platformie Azure występuje wdrożenie.</span><span class="sxs-lookup"><span data-stu-id="11d70-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="11d70-277">Przejdź do aplikacji w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="11d70-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="11d70-278">Zwróć uwagę, że tekst "V4" pojawia się w nagłówku:</span><span class="sxs-lookup"><span data-stu-id="11d70-278">Notice that the "V4" text appears in the heading:</span></span>

    ![zaktualizowana aplikacja](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="11d70-280">Sprawdzanie potoku potoku azure</span><span class="sxs-lookup"><span data-stu-id="11d70-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="11d70-281">Definicja kompilacji</span><span class="sxs-lookup"><span data-stu-id="11d70-281">Build definition</span></span>

<span data-ttu-id="11d70-282">Definicja kompilacji została utworzona pod nazwą *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="11d70-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="11d70-283">Po zakończeniu kompilacji tworzy plik *.zip,* w tym zasoby, które mają zostać opublikowane.</span><span class="sxs-lookup"><span data-stu-id="11d70-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="11d70-284">Potok wydania wdraża te zasoby na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="11d70-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="11d70-285">Na karcie Zadania definicji **kompilacji** wymieniono poszczególne kroki używane.</span><span class="sxs-lookup"><span data-stu-id="11d70-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="11d70-286">Istnieje pięć zadań kompilacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-286">There are five build tasks.</span></span>

![tworzenie zadań definicji](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="11d70-288">**Przywróć** &mdash; `dotnet restore` wykonuje polecenie, aby przywrócić pakiety NuGet aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="11d70-289">Domyślny kanał pakietu używany jest nuget.org.</span><span class="sxs-lookup"><span data-stu-id="11d70-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="11d70-290">**Kompilacja** &mdash; `dotnet build --configuration release` wykonuje polecenie do skompilowania kodu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="11d70-291">Ta `--configuration` opcja jest używana do tworzenia zoptymalizowanej wersji kodu, który jest odpowiedni do wdrożenia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="11d70-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="11d70-292">Zmodyfikuj zmienną *BuildConfiguration* na karcie Zmienne definicji **kompilacji,** jeśli na przykład potrzebna jest konfiguracja debugowania.</span><span class="sxs-lookup"><span data-stu-id="11d70-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="11d70-293">**Test** &mdash; wykonuje `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` polecenie uruchamiania testów jednostkowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="11d70-294">Testy jednostkowe są wykonywane w `**/*Tests/*.csproj` ramach dowolnego projektu Języka C# pasującego do wzorca glob.</span><span class="sxs-lookup"><span data-stu-id="11d70-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="11d70-295">Wyniki testów są zapisywane w pliku *.trx* w lokalizacji określonej `--results-directory` przez tę opcję.</span><span class="sxs-lookup"><span data-stu-id="11d70-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="11d70-296">Jeśli wszystkie testy nie powiodą się, kompilacja zakończy się niepowodzeniem i nie jest wdrażana.</span><span class="sxs-lookup"><span data-stu-id="11d70-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="11d70-297">Aby sprawdzić, czy testy jednostkowe działają, zmodyfikuj *SimpleFeedReader.Tests\Services\NewsServiceTests.cs,* aby celowo przerwać jeden z testów.</span><span class="sxs-lookup"><span data-stu-id="11d70-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="11d70-298">Na przykład `Assert.True(result.Count > 0);` zmień `Assert.False(result.Count > 0);` na `Returns_News_Stories_Given_Valid_Uri` metodę.</span><span class="sxs-lookup"><span data-stu-id="11d70-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="11d70-299">Zaagnaj i wypychaj zmianę do GitHub.</span><span class="sxs-lookup"><span data-stu-id="11d70-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="11d70-300">Kompilacja jest wyzwalana i kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="11d70-300">The build is triggered and fails.</span></span> <span data-ttu-id="11d70-301">Stan potoku kompilacji zmienia się na **nie powiódł**się.</span><span class="sxs-lookup"><span data-stu-id="11d70-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="11d70-302">Przywróć zmianę, zaobróć i wypchnij ponownie.</span><span class="sxs-lookup"><span data-stu-id="11d70-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="11d70-303">Kompilacja powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="11d70-303">The build succeeds.</span></span>

1. <span data-ttu-id="11d70-304">**Publish** &mdash; Wykonuje `dotnet publish --configuration release --output <local_path_on_build_agent>` polecenie do produkcji pliku *zip* z artefaktami do wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="11d70-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="11d70-305">Opcja `--output` określa lokalizację publikowania pliku *zip.*</span><span class="sxs-lookup"><span data-stu-id="11d70-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="11d70-306">Ta lokalizacja jest określona przez przekazanie `$(build.artifactstagingdirectory)` [wstępnie zdefiniowanej zmiennej](/azure/devops/pipelines/build/variables) o nazwie .</span><span class="sxs-lookup"><span data-stu-id="11d70-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="11d70-307">Ta zmienna rozwija się do ścieżki lokalnej, takiej jak *c:\agent\_work\1\a*, w agencie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="11d70-308">**Publikuj artefakt** &mdash; Publikuje plik *zip* wyprodukowany przez zadanie **Publikowania.**</span><span class="sxs-lookup"><span data-stu-id="11d70-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="11d70-309">Zadanie akceptuje lokalizację pliku *zip* jako parametr, który jest wstępnie `$(build.artifactstagingdirectory)`zdefiniowaną zmienną .</span><span class="sxs-lookup"><span data-stu-id="11d70-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="11d70-310">Plik *zip* jest publikowany jako folder o nazwie *drop*.</span><span class="sxs-lookup"><span data-stu-id="11d70-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="11d70-311">Kliknij łącze Podsumowanie definicji **kompilacji,** aby wyświetlić historię kompilacji z definicją:</span><span class="sxs-lookup"><span data-stu-id="11d70-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Zrzut ekranu przedstawiający historię definicji kompilacji](media/cicd/build-definition-summary.png)

<span data-ttu-id="11d70-313">Na wynikowej stronie kliknij łącze odpowiadające unikatowemu numerowi kompilacji:</span><span class="sxs-lookup"><span data-stu-id="11d70-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Zrzut ekranu przedstawiający stronę podsumowania definicji kompilacji](media/cicd/build-definition-completed.png)

<span data-ttu-id="11d70-315">Zostanie wyświetlone podsumowanie tej konkretnej kompilacji.</span><span class="sxs-lookup"><span data-stu-id="11d70-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="11d70-316">Kliknij kartę **Artefakty** i zwróć uwagę, że folder *upuszczania* wyprodukowany przez kompilację jest wymieniony:</span><span class="sxs-lookup"><span data-stu-id="11d70-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Zrzut ekranu przedstawiający artefakty definicji kompilacji — folder upuszczania](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="11d70-318">Użyj **łącza Pobierz** i **Eksploruj,** aby sprawdzić opublikowane artefakty.</span><span class="sxs-lookup"><span data-stu-id="11d70-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="11d70-319">Potok wydania</span><span class="sxs-lookup"><span data-stu-id="11d70-319">Release pipeline</span></span>

<span data-ttu-id="11d70-320">Potok wydania został utworzony pod nazwą *MyFirstProject-ASP.NET Core-CD:*</span><span class="sxs-lookup"><span data-stu-id="11d70-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Zrzut ekranu przedstawiający omówienie potoku wydania](media/cicd/release-definition-overview.png)

<span data-ttu-id="11d70-322">Dwa główne składniki potoku wydania są **artefakty** i **środowiska**.</span><span class="sxs-lookup"><span data-stu-id="11d70-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="11d70-323">Kliknięcie pola w sekcji **Artefakty** jest następujące:</span><span class="sxs-lookup"><span data-stu-id="11d70-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Zrzut ekranu przedstawiający artefakty potoku wydania](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="11d70-325">**Wartość Source (Build definition)** reprezentuje definicję kompilacji, z którą połączony jest ten potok wydania.</span><span class="sxs-lookup"><span data-stu-id="11d70-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="11d70-326">Plik *zip* wyprodukowany przez pomyślne uruchomienie definicji kompilacji jest dostarczany do środowiska *produkcyjnego* do wdrożenia na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="11d70-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="11d70-327">Kliknij *łącze 1 faza, 2 zadania* w polu Środowisko *produkcyjne,* aby wyświetlić zadania potoku wydania:</span><span class="sxs-lookup"><span data-stu-id="11d70-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Zrzut ekranu przedstawiający zadania potoku wydania](media/cicd/release-definition-tasks.png)

<span data-ttu-id="11d70-329">Potok wydania składa się z dwóch zadań: *Wdrażanie usługi Azure App Service na gnieździe* i *zarządzanie usługą Azure App Service — zamiana gniazd*.</span><span class="sxs-lookup"><span data-stu-id="11d70-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="11d70-330">Kliknięcie pierwszego zadania jest następujące:</span><span class="sxs-lookup"><span data-stu-id="11d70-330">Clicking the first task reveals the following task configuration:</span></span>

![Zrzut ekranu przedstawiający zadanie wdrażania potoku wydania](media/cicd/release-definition-task1.png)

<span data-ttu-id="11d70-332">Subskrypcja platformy Azure, typ usługi, nazwa aplikacji sieci web, grupa zasobów i gniazdo wdrażania są zdefiniowane w zadaniu wdrażania.</span><span class="sxs-lookup"><span data-stu-id="11d70-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="11d70-333">Pole tekstowe **Pakiet lub folder** zawiera ścieżkę pliku *zip,* która ma zostać wyodrębniona i wdrożona w miejscu *przejściowym* aplikacji sieci web \*\<mywebapp unique_number.\> \*</span><span class="sxs-lookup"><span data-stu-id="11d70-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="11d70-334">Kliknięcie zadania zamiany gniazda ujawnia następującą konfigurację zadania:</span><span class="sxs-lookup"><span data-stu-id="11d70-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Zrzut ekranu przedstawiający zadanie wymiany gniazda potoku wydania](media/cicd/release-definition-task2.png)

<span data-ttu-id="11d70-336">Podano subskrypcję, grupę zasobów, typ usługi, nazwę aplikacji sieci web i szczegóły gniazda wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="11d70-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="11d70-337">Pole wyboru **Zamiana z produkcją** jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="11d70-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="11d70-338">W związku z tym bity wdrożone w miejscu *przejściowym* są zamieniane w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="11d70-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="11d70-339">Dodatkowa lektura</span><span class="sxs-lookup"><span data-stu-id="11d70-339">Additional reading</span></span>

* [<span data-ttu-id="11d70-340">Tworzenie pierwszego potoku za pomocą usługi Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="11d70-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="11d70-341">Tworzenie i projekt .NET Core</span><span class="sxs-lookup"><span data-stu-id="11d70-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="11d70-342">Wdrażanie aplikacji sieci Web za pomocą usługi Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="11d70-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)

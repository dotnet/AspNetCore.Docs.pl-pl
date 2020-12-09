---
title: Ciągła integracja i wdrażanie — DevOps z użyciem ASP.NET Core i platformy Azure
author: CamSoper
description: Ciągła integracja i wdrażanie w programie DevOps z usługami ASP.NET Core i Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
no-loc:
- appsettings.json
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
uid: azure/devops/cicd
ms.openlocfilehash: 2ac7a130d223b21330d0a797c1d460fc0cf467d7
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901213"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="ade53-103">Ciągła integracja i ciągłe wdrażanie</span><span class="sxs-lookup"><span data-stu-id="ade53-103">Continuous integration and deployment</span></span>

<span data-ttu-id="ade53-104">W poprzednim rozdziale utworzono lokalne repozytorium git dla aplikacji czytnika prostego źródła danych.</span><span class="sxs-lookup"><span data-stu-id="ade53-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="ade53-105">W tym rozdziale opublikujesz ten kod w repozytorium GitHub i utworzysz potok Azure DevOps Services przy użyciu Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="ade53-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="ade53-106">Potok umożliwia ciągłe kompilacje i wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="ade53-107">Każde zatwierdzenie repozytorium GitHub wyzwala kompilację i wdrożenie w miejscu przejściowym aplikacji sieci Web platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="ade53-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="ade53-108">W tej sekcji wykonasz następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="ade53-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="ade53-109">Opublikuj kod aplikacji w usłudze GitHub</span><span class="sxs-lookup"><span data-stu-id="ade53-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="ade53-110">Rozłącz lokalne wdrożenie narzędzia Git</span><span class="sxs-lookup"><span data-stu-id="ade53-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="ade53-111">Tworzenie organizacji usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="ade53-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="ade53-112">Utwórz projekt zespołowy w Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="ade53-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="ade53-113">Tworzenie definicji kompilacji</span><span class="sxs-lookup"><span data-stu-id="ade53-113">Create a build definition</span></span>
* <span data-ttu-id="ade53-114">Tworzenie potoku wydania</span><span class="sxs-lookup"><span data-stu-id="ade53-114">Create a release pipeline</span></span>
* <span data-ttu-id="ade53-115">Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="ade53-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="ade53-116">Sprawdzanie potoku Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ade53-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="ade53-117">Opublikuj kod aplikacji w usłudze GitHub</span><span class="sxs-lookup"><span data-stu-id="ade53-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="ade53-118">Otwórz okno przeglądarki i przejdź do `https://github.com` .</span><span class="sxs-lookup"><span data-stu-id="ade53-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="ade53-119">Kliknij **+** listę rozwijaną w nagłówku i wybierz pozycję **nowe repozytorium**:</span><span class="sxs-lookup"><span data-stu-id="ade53-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Opcja nowego repozytorium usługi GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="ade53-121">Wybierz swoje konto z listy rozwijanej **właściciel** , a *następnie wprowadź tekst* w polu tekstowym **Nazwa repozytorium** .</span><span class="sxs-lookup"><span data-stu-id="ade53-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="ade53-122">Kliknij przycisk **Utwórz repozytorium** .</span><span class="sxs-lookup"><span data-stu-id="ade53-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="ade53-123">Otwórz powłokę poleceń komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="ade53-123">Open your local machine's command shell.</span></span> <span data-ttu-id="ade53-124">Przejdź do katalogu, w którym jest przechowywane repozytorium git programu *Simple-Reader* .</span><span class="sxs-lookup"><span data-stu-id="ade53-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="ade53-125">Zmień nazwę istniejącego *pochodzenia* zdalnego na *nadrzędny*.</span><span class="sxs-lookup"><span data-stu-id="ade53-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="ade53-126">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ade53-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="ade53-127">Dodaj nowe *Źródło* zdalne wskazujące swoją kopię repozytorium w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="ade53-128">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ade53-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="ade53-129">Opublikuj lokalne repozytorium Git w nowo utworzonym repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="ade53-130">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ade53-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="ade53-131">Otwórz okno przeglądarki i przejdź do `https://github.com/<GitHub_username>/simple-feed-reader/` .</span><span class="sxs-lookup"><span data-stu-id="ade53-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="ade53-132">Sprawdź, czy kod jest wyświetlany w repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="ade53-133">Rozłącz lokalne wdrożenie narzędzia Git</span><span class="sxs-lookup"><span data-stu-id="ade53-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="ade53-134">Usuń lokalne wdrożenie narzędzia Git, wykonując poniższe kroki.</span><span class="sxs-lookup"><span data-stu-id="ade53-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="ade53-135">Azure Pipelines (usługa Azure DevOps) zamienia i rozszerza te funkcje.</span><span class="sxs-lookup"><span data-stu-id="ade53-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="ade53-136">Otwórz [Azure Portal](https://portal.azure.com/)i przejdź do aplikacji sieci Web *przemieszczania (mywebapp \<unique_number\> /Staging)* .</span><span class="sxs-lookup"><span data-stu-id="ade53-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="ade53-137">Aplikację sieci Web można szybko zlokalizować *, wprowadzając w* polu wyszukiwania portalu:</span><span class="sxs-lookup"><span data-stu-id="ade53-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![termin wyszukiwania aplikacji sieci Web](media/cicd/portal-search-box.png)

1. <span data-ttu-id="ade53-139">Kliknij pozycję **centrum wdrażania**.</span><span class="sxs-lookup"><span data-stu-id="ade53-139">Click **Deployment Center**.</span></span> <span data-ttu-id="ade53-140">Zostanie wyświetlony nowy panel.</span><span class="sxs-lookup"><span data-stu-id="ade53-140">A new panel appears.</span></span> <span data-ttu-id="ade53-141">Kliknij przycisk **Rozłącz** , aby usunąć konfigurację lokalnej kontroli źródła git, która została dodana w poprzednim rozdziale.</span><span class="sxs-lookup"><span data-stu-id="ade53-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="ade53-142">Potwierdź operację usuwania, klikając przycisk **tak** .</span><span class="sxs-lookup"><span data-stu-id="ade53-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="ade53-143">Przejdź do *mywebapp<unique_number>* App Service.</span><span class="sxs-lookup"><span data-stu-id="ade53-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="ade53-144">Za pomocą pola wyszukiwania portalu można szybko zlokalizować App Service.</span><span class="sxs-lookup"><span data-stu-id="ade53-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="ade53-145">Kliknij pozycję **centrum wdrażania**.</span><span class="sxs-lookup"><span data-stu-id="ade53-145">Click **Deployment Center**.</span></span> <span data-ttu-id="ade53-146">Zostanie wyświetlony nowy panel.</span><span class="sxs-lookup"><span data-stu-id="ade53-146">A new panel appears.</span></span> <span data-ttu-id="ade53-147">Kliknij przycisk **Rozłącz** , aby usunąć konfigurację lokalnej kontroli źródła git, która została dodana w poprzednim rozdziale.</span><span class="sxs-lookup"><span data-stu-id="ade53-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="ade53-148">Potwierdź operację usuwania, klikając przycisk **tak** .</span><span class="sxs-lookup"><span data-stu-id="ade53-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="ade53-149">Tworzenie organizacji usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="ade53-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="ade53-150">Otwórz przeglądarkę i przejdź do [strony tworzenie organizacji usługi Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="ade53-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="ade53-151">Wpisz unikatową nazwę w polu tekstowym **Wybierz zapamiętaną nazwę** , aby utworzyć adres URL do uzyskiwania dostępu do organizacji usługi Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="ade53-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="ade53-152">Wybierz przycisk radiowy **git** , ponieważ kod jest hostowany w repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="ade53-153">Kliknij przycisk **Kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="ade53-153">Click the **Continue** button.</span></span> <span data-ttu-id="ade53-154">Po krótkim czasie oczekiwania tworzone jest konto i projekt zespołowy o nazwie *MyFirstProject*.</span><span class="sxs-lookup"><span data-stu-id="ade53-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Strona tworzenia organizacji usługi Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="ade53-156">Otwórz wiadomość e-mail z potwierdzeniem wskazującą, że organizacja i projekt usługi Azure DevOps są gotowe do użycia.</span><span class="sxs-lookup"><span data-stu-id="ade53-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="ade53-157">Kliknij przycisk **Rozpocznij projekt** :</span><span class="sxs-lookup"><span data-stu-id="ade53-157">Click the **Start your project** button:</span></span>

    ![Przycisk Rozpocznij projekt](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="ade53-159">Zostanie otwarta przeglądarka *\<account_name\> . VisualStudio.com*.</span><span class="sxs-lookup"><span data-stu-id="ade53-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="ade53-160">Kliknij link *MyFirstProject* , aby rozpocząć konfigurowanie potoku DevOps projektu.</span><span class="sxs-lookup"><span data-stu-id="ade53-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="ade53-161">Konfigurowanie potoku Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ade53-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="ade53-162">Istnieją trzy różne czynności do wykonania.</span><span class="sxs-lookup"><span data-stu-id="ade53-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="ade53-163">Wykonanie kroków opisanych w poniższych trzech sekcjach powoduje wystąpienie działającego potoku DevOps.</span><span class="sxs-lookup"><span data-stu-id="ade53-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="ade53-164">Przyznaj usłudze Azure DevOps dostęp do repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="ade53-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="ade53-165">Rozwiń **lub Skompiluj kod z repozytorium zewnętrznego** .</span><span class="sxs-lookup"><span data-stu-id="ade53-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="ade53-166">Kliknij przycisk **kompilacja instalacji** :</span><span class="sxs-lookup"><span data-stu-id="ade53-166">Click the **Setup Build** button:</span></span>

    ![Przycisk Kompilacja instalacji](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="ade53-168">Wybierz opcję **GitHub** z sekcji **Wybierz źródło** :</span><span class="sxs-lookup"><span data-stu-id="ade53-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Wybierz źródło — GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="ade53-170">Autoryzacja jest wymagana, aby usługa Azure DevOps mogła uzyskać dostęp do Twojego repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="ade53-171">Wprowadź *<GitHub_username> połączenia GitHub* w polu tekstowym **Nazwa połączenia** .</span><span class="sxs-lookup"><span data-stu-id="ade53-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="ade53-172">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="ade53-172">For example:</span></span>

    ![Nazwa połączenia GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="ade53-174">Jeśli na koncie usługi GitHub jest włączone uwierzytelnianie dwuskładnikowe, wymagany jest osobisty token dostępu.</span><span class="sxs-lookup"><span data-stu-id="ade53-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="ade53-175">W takim przypadku kliknij link **Autoryzuj przy użyciu osobistego tokenu dostępu usługi GitHub** .</span><span class="sxs-lookup"><span data-stu-id="ade53-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="ade53-176">Zobacz [oficjalne instrukcje tworzenia tokenu dostępu osobistego usługi GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) , aby uzyskać pomoc.</span><span class="sxs-lookup"><span data-stu-id="ade53-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="ade53-177">Wymagany jest tylko zakres uprawnień do *repozytorium* .</span><span class="sxs-lookup"><span data-stu-id="ade53-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="ade53-178">W przeciwnym razie kliknij przycisk **Autoryzuj przy użyciu protokołu OAuth** .</span><span class="sxs-lookup"><span data-stu-id="ade53-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="ade53-179">Po wyświetleniu monitu zaloguj się do konta usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="ade53-180">Następnie wybierz pozycję Autoryzuj, aby udzielić dostępu do Twojej organizacji usługi Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="ade53-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="ade53-181">Jeśli to się powiedzie, zostanie utworzony nowy punkt końcowy usługi.</span><span class="sxs-lookup"><span data-stu-id="ade53-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="ade53-182">Kliknij przycisk wielokropka obok przycisku **repozytorium** .</span><span class="sxs-lookup"><span data-stu-id="ade53-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="ade53-183">Wybierz z listy *<GitHub_username>repozytorium/Simple-Feed-Reader* .</span><span class="sxs-lookup"><span data-stu-id="ade53-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="ade53-184">Kliknij przycisk **Wybierz** .</span><span class="sxs-lookup"><span data-stu-id="ade53-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="ade53-185">Wybierz gałąź domyślną (*Master*) z **gałęzi domyślnej dla listy rozwijanej ręczne i zaplanowane kompilacje** .</span><span class="sxs-lookup"><span data-stu-id="ade53-185">Select the default branch (*master*) from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="ade53-186">Kliknij przycisk **Kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="ade53-186">Click the **Continue** button.</span></span> <span data-ttu-id="ade53-187">Zostanie wyświetlona strona Wybieranie szablonu.</span><span class="sxs-lookup"><span data-stu-id="ade53-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="ade53-188">Tworzenie definicji kompilacji</span><span class="sxs-lookup"><span data-stu-id="ade53-188">Create the build definition</span></span>

1. <span data-ttu-id="ade53-189">Na stronie Wybór szablonu wprowadź *ASP.NET Core* w polu wyszukiwania:</span><span class="sxs-lookup"><span data-stu-id="ade53-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core wyszukiwanie na stronie szablonu](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="ade53-191">Zostaną wyświetlone wyniki wyszukiwania szablonu.</span><span class="sxs-lookup"><span data-stu-id="ade53-191">The template search results appear.</span></span> <span data-ttu-id="ade53-192">Umieść kursor nad szablonem **ASP.NET Core** i kliknij przycisk **Zastosuj** .</span><span class="sxs-lookup"><span data-stu-id="ade53-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="ade53-193">Zostanie wyświetlona karta **zadania** w definicji kompilacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="ade53-194">Kliknij kartę **wyzwalacze** .</span><span class="sxs-lookup"><span data-stu-id="ade53-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="ade53-195">Zaznacz pole wyboru **Włącz ciągłą integrację** .</span><span class="sxs-lookup"><span data-stu-id="ade53-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="ade53-196">W sekcji **filtry gałęzi** upewnij się, że na liście rozwijanej **typ** jest ustawiona wartość *include*.</span><span class="sxs-lookup"><span data-stu-id="ade53-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="ade53-197">Ustaw listę rozwijaną **Specyfikacja gałęzi** z *główną*.</span><span class="sxs-lookup"><span data-stu-id="ade53-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Włącz ustawienia ciągłej integracji](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="ade53-199">Te ustawienia powodują, że kompilacja jest wyzwalana, gdy jakakolwiek zmiana jest wypychana do domyślnej gałęzi (*Master*) repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-199">These settings cause a build to trigger when any change is pushed to the default branch (*master*) of the GitHub repository.</span></span> <span data-ttu-id="ade53-200">Ciągła integracja jest testowana w usłudze [GitHub i automatycznie wdrażana](#commit-changes-to-github-and-automatically-deploy-to-azure) w usłudze Azure.</span><span class="sxs-lookup"><span data-stu-id="ade53-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="ade53-201">Kliknij przycisk **zapisz & kolejkę** i wybierz opcję **Zapisz** :</span><span class="sxs-lookup"><span data-stu-id="ade53-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Przycisk Zapisz](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="ade53-203">Wyświetlane jest następujące modalne okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="ade53-203">The following modal dialog appears:</span></span>

    ![Zapisz definicję kompilacji — modalne okno dialogowe](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="ade53-205">Użyj folderu domyślnego *\\* , a następnie kliknij przycisk **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="ade53-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="ade53-206">Tworzenie potoku wydania</span><span class="sxs-lookup"><span data-stu-id="ade53-206">Create the release pipeline</span></span>

1. <span data-ttu-id="ade53-207">Kliknij kartę **wydania** w projekcie zespołowym.</span><span class="sxs-lookup"><span data-stu-id="ade53-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="ade53-208">Kliknij przycisk **nowe potoku** .</span><span class="sxs-lookup"><span data-stu-id="ade53-208">Click the **New pipeline** button.</span></span>

    ![Karta wydania — przycisk Nowa definicja](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="ade53-210">Zostanie wyświetlone okienko wybór szablonu.</span><span class="sxs-lookup"><span data-stu-id="ade53-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="ade53-211">Na stronie Wybór szablonu wprowadź *App Service* w polu wyszukiwania:</span><span class="sxs-lookup"><span data-stu-id="ade53-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Pole wyszukiwania szablonu potoku wydania](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="ade53-213">Zostaną wyświetlone wyniki wyszukiwania szablonu.</span><span class="sxs-lookup"><span data-stu-id="ade53-213">The template search results appear.</span></span> <span data-ttu-id="ade53-214">Umieść kursor nad **wdrożeniem Azure App Service z** szablonem miejsca, a następnie kliknij przycisk **Zastosuj** .</span><span class="sxs-lookup"><span data-stu-id="ade53-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="ade53-215">Zostanie wyświetlona karta **potok** w potoku wydania.</span><span class="sxs-lookup"><span data-stu-id="ade53-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Karta potoku wydania potoku](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="ade53-217">Kliknij przycisk **Dodaj** w polu **artefakty** .</span><span class="sxs-lookup"><span data-stu-id="ade53-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="ade53-218">Zostanie wyświetlony panel **Dodaj artefakt** :</span><span class="sxs-lookup"><span data-stu-id="ade53-218">The **Add artifact** panel appears:</span></span>

    ![Potok wydania — Dodawanie panelu artefaktów](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="ade53-220">Wybierz kafelek **kompilacja** z sekcji **Typ źródła** .</span><span class="sxs-lookup"><span data-stu-id="ade53-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="ade53-221">Ten typ umożliwia łączenie potoku wydania z definicją kompilacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="ade53-222">Wybierz pozycję *MyFirstProject* z listy rozwijanej **projekt** .</span><span class="sxs-lookup"><span data-stu-id="ade53-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="ade53-223">Wybierz nazwę definicji kompilacji, *MyFirstProject-ASP.NET Core-Ci*, z listy rozwijanej **Źródło (definicja kompilacji)** .</span><span class="sxs-lookup"><span data-stu-id="ade53-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="ade53-224">Wybierz pozycję *Najnowsza* z listy rozwijanej **wersja domyślna** .</span><span class="sxs-lookup"><span data-stu-id="ade53-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="ade53-225">Ta opcja kompiluje artefakty wytwarzane przez ostatnie uruchomienie definicji kompilacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="ade53-226">Zastąp tekst w polu tekstowym **aliasu źródła** obiektem *Drop*.</span><span class="sxs-lookup"><span data-stu-id="ade53-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="ade53-227">Kliknij przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="ade53-227">Click the **Add** button.</span></span> <span data-ttu-id="ade53-228">Sekcja **artefakty** jest aktualizowana w celu wyświetlenia zmian.</span><span class="sxs-lookup"><span data-stu-id="ade53-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="ade53-229">Kliknij ikonę błyskawicy, aby włączyć ciągłe wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="ade53-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Artefakty potoku wydania — ikona błyskawicy](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="ade53-231">Po włączeniu tej opcji wdrożenie odbywa się za każdym razem, gdy dostępna jest nowa kompilacja.</span><span class="sxs-lookup"><span data-stu-id="ade53-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="ade53-232">Po prawej stronie zostanie wyświetlony panel **wyzwalacz ciągłego wdrażania** .</span><span class="sxs-lookup"><span data-stu-id="ade53-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="ade53-233">Kliknij przycisk Przełącz, aby włączyć tę funkcję.</span><span class="sxs-lookup"><span data-stu-id="ade53-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="ade53-234">Nie jest konieczne włączenie **wyzwalacza żądania ściągnięcia**.</span><span class="sxs-lookup"><span data-stu-id="ade53-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="ade53-235">Kliknij przycisk **Dodaj** listę rozwijaną w sekcji **filtry gałęzi kompilacji** .</span><span class="sxs-lookup"><span data-stu-id="ade53-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="ade53-236">Wybierz opcję **domyślne rozgałęzienie definicji kompilacji** .</span><span class="sxs-lookup"><span data-stu-id="ade53-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="ade53-237">Ten filtr powoduje, że wersja jest wyzwalana tylko dla kompilacji z domyślnej gałęzi repozytorium GitHub (*Master*).</span><span class="sxs-lookup"><span data-stu-id="ade53-237">This filter causes the release to trigger only for a build from the GitHub repository's default branch (*master*).</span></span>
1. <span data-ttu-id="ade53-238">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="ade53-238">Click the **Save** button.</span></span> <span data-ttu-id="ade53-239">Kliknij przycisk **OK** w oknie dialogowym **zapisuje** modalne okno dialogowe.</span><span class="sxs-lookup"><span data-stu-id="ade53-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="ade53-240">Kliknij pole **środowisko 1** .</span><span class="sxs-lookup"><span data-stu-id="ade53-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="ade53-241">Panel **środowiska** pojawia się po prawej stronie.</span><span class="sxs-lookup"><span data-stu-id="ade53-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="ade53-242">Zmień tekst *środowiska 1* w polu tekstowym **Nazwa środowiska** na środowisko *produkcyjne*.</span><span class="sxs-lookup"><span data-stu-id="ade53-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Potok wydania — pole tekstowe nazwy środowiska](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="ade53-244">Kliknij link **1 fazy, 2 zadania** w polu **produkcja** :</span><span class="sxs-lookup"><span data-stu-id="ade53-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Potok wydania — środowisko produkcyjne link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="ade53-246">Zostanie wyświetlona karta **zadania** środowiska.</span><span class="sxs-lookup"><span data-stu-id="ade53-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="ade53-247">Kliknij zadanie **wdróż Azure App Service w miejscu** .</span><span class="sxs-lookup"><span data-stu-id="ade53-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="ade53-248">Ustawienia są wyświetlane w panelu po prawej stronie.</span><span class="sxs-lookup"><span data-stu-id="ade53-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="ade53-249">Wybierz subskrypcję platformy Azure skojarzoną z App Serviceą z listy rozwijanej **subskrypcja platformy Azure** .</span><span class="sxs-lookup"><span data-stu-id="ade53-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="ade53-250">Po wybraniu kliknij przycisk **Autoryzuj** .</span><span class="sxs-lookup"><span data-stu-id="ade53-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="ade53-251">Wybierz pozycję *aplikacja sieci Web* z listy rozwijanej **Typ aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ade53-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="ade53-252">Wybierz pozycję *mywebapp/<unique_number/>* z listy rozwijanej **nazwa usługi App Service** .</span><span class="sxs-lookup"><span data-stu-id="ade53-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="ade53-253">Wybierz pozycję *AzureTutorial* z listy rozwijanej **Grupa zasobów** .</span><span class="sxs-lookup"><span data-stu-id="ade53-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="ade53-254">Wybierz pozycję *przemieszczanie* z listy rozwijanej **miejsce** .</span><span class="sxs-lookup"><span data-stu-id="ade53-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="ade53-255">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="ade53-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="ade53-256">Umieść kursor nad domyślną nazwą potoku wydania.</span><span class="sxs-lookup"><span data-stu-id="ade53-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="ade53-257">Kliknij ikonę ołówka, aby ją edytować.</span><span class="sxs-lookup"><span data-stu-id="ade53-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="ade53-258">Użyj *MyFirstProject-ASP.NET Core-CD* jako nazwy.</span><span class="sxs-lookup"><span data-stu-id="ade53-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nazwa potoku wydania](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="ade53-260">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="ade53-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="ade53-261">Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="ade53-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="ade53-262">Otwórz *SimpleFeedReader. sln* w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ade53-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="ade53-263">W Eksplorator rozwiązań Otwórz *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ade53-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="ade53-264">Zmień `<h2>Simple Feed Reader - V3</h2>` na `<h2>Simple Feed Reader - V4</h2>` .</span><span class="sxs-lookup"><span data-stu-id="ade53-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="ade53-265">Naciśnij **klawisze CTRL** + **SHIFT** + **B** , aby skompilować aplikację.</span><span class="sxs-lookup"><span data-stu-id="ade53-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="ade53-266">Zatwierdź plik do repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="ade53-267">Użyj strony **zmiany** w karcie *Team Explorer* programu Visual Studio lub wykonaj następujące czynności przy użyciu powłoki poleceń komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="ade53-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="ade53-268">Wypchnij zmiany w gałęzi domyślnej (*głównej*) do *źródła* zdalnego w repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-268">Push the change in the default branch (*master*) to the *origin* remote of your GitHub repository.</span></span> <span data-ttu-id="ade53-269">W poniższym poleceniu Zastąp symbol zastępczy `{BRANCH}` domyślną gałęzią (Użyj `master` ):</span><span class="sxs-lookup"><span data-stu-id="ade53-269">In the following command, replace the placeholder `{BRANCH}` with the default branch (use `master`):</span></span>

    ```console
    git push origin {BRANCH}
    ```

    <span data-ttu-id="ade53-270">Zatwierdzenie pojawia się w domyślnej gałęzi repozytorium GitHub (*Master*):</span><span class="sxs-lookup"><span data-stu-id="ade53-270">The commit appears in the GitHub repository's default branch (*master*):</span></span>

    ![Zatwierdzenie GitHub w gałęzi domyślnej (główny)](media/cicd/github-commit.png)

    <span data-ttu-id="ade53-272">Kompilacja jest wyzwalana, ponieważ na karcie **wyzwalacze** definicji kompilacji jest włączona Integracja ciągła:</span><span class="sxs-lookup"><span data-stu-id="ade53-272">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Włącz integrację ciągłą](media/cicd/enable-ci.png)

1. <span data-ttu-id="ade53-274">Przejdź do karty z **kolejką** na **Azure Pipelines**  >  stronie **kompilacje** Azure Pipelines w Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="ade53-274">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="ade53-275">Kompilacja w kolejce pokazuje gałąź i zatwierdzenie, które wyzwoliły kompilację:</span><span class="sxs-lookup"><span data-stu-id="ade53-275">The queued build shows the branch and commit that triggered the build:</span></span>

    ![Kompilacja umieszczona w kolejce](media/cicd/build-queued.png)

1. <span data-ttu-id="ade53-277">Gdy kompilacja zakończy się powodzeniem, wystąpi wdrożenie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="ade53-277">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="ade53-278">Przejdź do aplikacji w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="ade53-278">Navigate to the app in the browser.</span></span> <span data-ttu-id="ade53-279">Zwróć uwagę, że tekst "v4" pojawia się w nagłówku:</span><span class="sxs-lookup"><span data-stu-id="ade53-279">Notice that the "V4" text appears in the heading:</span></span>

    ![zaktualizowana aplikacja](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="ade53-281">Sprawdzanie potoku Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ade53-281">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="ade53-282">Definicja kompilacji</span><span class="sxs-lookup"><span data-stu-id="ade53-282">Build definition</span></span>

<span data-ttu-id="ade53-283">Definicja kompilacji została utworzona przy użyciu nazwy *MyFirstProject-ASP.NET Core-Ci*.</span><span class="sxs-lookup"><span data-stu-id="ade53-283">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="ade53-284">Po zakończeniu kompilacja tworzy plik *zip* , w tym zasoby do opublikowania.</span><span class="sxs-lookup"><span data-stu-id="ade53-284">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="ade53-285">Potok wydania wdraża te zasoby na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="ade53-285">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="ade53-286">Na karcie **zadania** definicji kompilacji są wyświetlane poszczególne etapy, które są używane.</span><span class="sxs-lookup"><span data-stu-id="ade53-286">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="ade53-287">Istnieje pięć zadań kompilacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-287">There are five build tasks.</span></span>

![zadania definicji kompilacji](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="ade53-289">**Przywróć** &mdash; Wykonuje `dotnet restore` polecenie, aby przywrócić pakiety NuGet aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-289">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="ade53-290">Domyślnym źródłem pakietów jest nuget.org.</span><span class="sxs-lookup"><span data-stu-id="ade53-290">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="ade53-291">**Kompilacja** &mdash; Wykonuje `dotnet build --configuration release` polecenie, aby skompilować kod aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-291">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="ade53-292">Ta `--configuration` opcja służy do tworzenia zoptymalizowanej wersji kodu, która jest odpowiednia do wdrożenia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="ade53-292">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="ade53-293">Zmodyfikuj zmienną *BuildConfiguration* na karcie **zmienne** definicji kompilacji, jeśli na przykład wymagana jest Konfiguracja debugowania.</span><span class="sxs-lookup"><span data-stu-id="ade53-293">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="ade53-294">**Test** &mdash; Wykonuje `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` polecenie, aby uruchomić testy jednostkowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-294">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="ade53-295">Testy jednostkowe są wykonywane w dowolnym projekcie języka C# pasującym do `**/*Tests/*.csproj` wzorca globalizowania.</span><span class="sxs-lookup"><span data-stu-id="ade53-295">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="ade53-296">Wyniki testu są zapisywane w pliku *. TRX* w lokalizacji określonej przez `--results-directory` opcję.</span><span class="sxs-lookup"><span data-stu-id="ade53-296">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="ade53-297">Jeśli jakiekolwiek testy zakończą się niepowodzeniem, kompilacja nie powiedzie się i nie zostanie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="ade53-297">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="ade53-298">Aby sprawdzić, czy testy jednostkowe działają, zmodyfikuj *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* na celowo całkowicie Przerwij jeden z testów.</span><span class="sxs-lookup"><span data-stu-id="ade53-298">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="ade53-299">Na przykład zmień `Assert.True(result.Count > 0);` na `Assert.False(result.Count > 0);` w `Returns_News_Stories_Given_Valid_Uri` metodzie.</span><span class="sxs-lookup"><span data-stu-id="ade53-299">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="ade53-300">Zatwierdź i wypchnij zmianę do usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="ade53-300">Commit and push the change to GitHub.</span></span> <span data-ttu-id="ade53-301">Kompilacja jest wyzwalana i kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="ade53-301">The build is triggered and fails.</span></span> <span data-ttu-id="ade53-302">Stan potoku kompilacji zmieni się na **Niepowodzenie**.</span><span class="sxs-lookup"><span data-stu-id="ade53-302">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="ade53-303">Przywróć zmiany, Zatwierdź i wypchnij ponownie.</span><span class="sxs-lookup"><span data-stu-id="ade53-303">Revert the change, commit, and push again.</span></span> <span data-ttu-id="ade53-304">Kompilacja powiodła się.</span><span class="sxs-lookup"><span data-stu-id="ade53-304">The build succeeds.</span></span>

1. <span data-ttu-id="ade53-305">**Publikuj** &mdash; Wykonuje `dotnet publish --configuration release --output <local_path_on_build_agent>` polecenie, aby utworzyć plik *zip* z artefaktami, które mają zostać wdrożone.</span><span class="sxs-lookup"><span data-stu-id="ade53-305">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="ade53-306">`--output`Opcja określa lokalizację publikacji pliku *zip* .</span><span class="sxs-lookup"><span data-stu-id="ade53-306">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="ade53-307">Ta lokalizacja jest określona przez przekazanie [wstępnie zdefiniowanej zmiennej](/azure/devops/pipelines/build/variables) o nazwie `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="ade53-307">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="ade53-308">Ta zmienna powiększa się do ścieżki lokalnej, takiej jak *c:\agent \_ work\1\a*, na agencie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-308">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="ade53-309">**Publikuj artefakt** &mdash; Publikuje plik *zip* utworzony przez zadanie **publikowania** .</span><span class="sxs-lookup"><span data-stu-id="ade53-309">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="ade53-310">Zadanie przyjmuje lokalizację pliku *. zip* jako parametr, który jest wstępnie zdefiniowaną zmienną `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="ade53-310">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="ade53-311">Plik *zip* jest publikowany jako folder o nazwie *Drop*.</span><span class="sxs-lookup"><span data-stu-id="ade53-311">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="ade53-312">Kliknij link **podsumowania** definicji kompilacji, aby wyświetlić historię kompilacji z definicją:</span><span class="sxs-lookup"><span data-stu-id="ade53-312">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Zrzut ekranu przedstawiający historię definicji kompilacji](media/cicd/build-definition-summary.png)

<span data-ttu-id="ade53-314">Na stronie wyników kliknij link odpowiadający unikatowemu numerowi kompilacji:</span><span class="sxs-lookup"><span data-stu-id="ade53-314">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Zrzut ekranu przedstawiający stronę podsumowania definicji kompilacji](media/cicd/build-definition-completed.png)

<span data-ttu-id="ade53-316">Zostanie wyświetlona podsumowanie tej konkretnej kompilacji.</span><span class="sxs-lookup"><span data-stu-id="ade53-316">A summary of this specific build is displayed.</span></span> <span data-ttu-id="ade53-317">Kliknij kartę **artefakty** i zwróć uwagę na to, że folder *upuszczania* utworzony przez kompilację zostanie wyświetlony na liście:</span><span class="sxs-lookup"><span data-stu-id="ade53-317">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Zrzut ekranu przedstawiający artefakty definicji kompilacji — folder docelowy](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="ade53-319">Użyj linków **pobierania** i **eksplorowania** , aby sprawdzić opublikowane artefakty.</span><span class="sxs-lookup"><span data-stu-id="ade53-319">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="ade53-320">Potok wydania</span><span class="sxs-lookup"><span data-stu-id="ade53-320">Release pipeline</span></span>

<span data-ttu-id="ade53-321">Potok wydania został utworzony przy użyciu nazwy *MyFirstProject-ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="ade53-321">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Zrzut ekranu przedstawiający przegląd potoku wydania](media/cicd/release-definition-overview.png)

<span data-ttu-id="ade53-323">Dwa główne składniki potoku wydania to **artefakty** i **środowiska**.</span><span class="sxs-lookup"><span data-stu-id="ade53-323">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="ade53-324">Kliknięcie pola w sekcji **artefakty** ujawnia następujący panel:</span><span class="sxs-lookup"><span data-stu-id="ade53-324">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Zrzut ekranu przedstawiający artefakty potoku wydania](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="ade53-326">Wartość **źródłowa (definicji kompilacji)** reprezentuje definicję kompilacji, z którą połączony jest potok wersji.</span><span class="sxs-lookup"><span data-stu-id="ade53-326">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="ade53-327">Plik *zip* utworzony przez pomyślne uruchomienie definicji kompilacji jest dostarczany do środowiska *produkcyjnego* w celu wdrożenia na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="ade53-327">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="ade53-328">Kliknij link *1 fazy, 2 zadania* w polu środowisko *produkcyjne* , aby wyświetlić zadania potoku wydania:</span><span class="sxs-lookup"><span data-stu-id="ade53-328">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Zrzut ekranu przedstawiający zadania potoku wydania](media/cicd/release-definition-tasks.png)

<span data-ttu-id="ade53-330">Potok wersji składa się z dwóch zadań: *wdróż Azure App Service w gnieździe* i *Zarządzaj wymianą między gniazdami Azure App Service*.</span><span class="sxs-lookup"><span data-stu-id="ade53-330">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="ade53-331">Kliknięcie pierwszego zadania wykazuje następującą konfigurację zadania:</span><span class="sxs-lookup"><span data-stu-id="ade53-331">Clicking the first task reveals the following task configuration:</span></span>

![Zrzut ekranu przedstawiający zadanie wdrażania potoku wydania](media/cicd/release-definition-task1.png)

<span data-ttu-id="ade53-333">Subskrypcja platformy Azure, typ usługi, nazwa aplikacji sieci Web, Grupa zasobów i miejsce wdrożenia są zdefiniowane w zadaniu wdrażania.</span><span class="sxs-lookup"><span data-stu-id="ade53-333">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="ade53-334">Pole tekstowe **pakiet lub folder** zawiera ścieżkę pliku *. zip* , która ma zostać wyodrębniona i wdrożona w miejscu *przejściowym* aplikacji sieci Web *mywebapp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="ade53-334">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="ade53-335">Kliknięcie zadania wymiany gniazd powoduje wyświetlenie następującej konfiguracji zadania:</span><span class="sxs-lookup"><span data-stu-id="ade53-335">Clicking the slot swap task reveals the following task configuration:</span></span>

![Zrzut ekranu przedstawiający zadanie wymiany miejsca potoku wydania](media/cicd/release-definition-task2.png)

<span data-ttu-id="ade53-337">Podane są szczegóły subskrypcji, grupy zasobów, typu usługi, nazwy aplikacji sieci Web i miejsca wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="ade53-337">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="ade53-338">Pole wyboru **Zamień z produkcją** jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="ade53-338">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="ade53-339">W związku z tym bity wdrożone w miejscu *przejściowym* są wymieniane w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="ade53-339">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="ade53-340">Materiały uzupełniające</span><span class="sxs-lookup"><span data-stu-id="ade53-340">Additional reading</span></span>

* [<span data-ttu-id="ade53-341">Tworzenie pierwszego potoku za pomocą Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ade53-341">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="ade53-342">Kompilacja i projekt .NET Core</span><span class="sxs-lookup"><span data-stu-id="ade53-342">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="ade53-343">Wdrażanie aplikacji sieci Web za pomocą Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="ade53-343">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)

---
title: Wdrażanie aplikacji w usłudze app service — DevOps z ASP.NET Core i platformą Azure
author: CamSoper
description: Wdrażanie aplikacji ASP.NET Core w usłudze Azure App Service, pierwszym kroku dla usługi DevOps z ASP.NET Core i platformą Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657746"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="0c924-103">Wdrażanie aplikacji w usłudze app service</span><span class="sxs-lookup"><span data-stu-id="0c924-103">Deploy an app to App Service</span></span>

<span data-ttu-id="0c924-104">[Usługa Azure App Service](/azure/app-service/) to platforma hostingowa platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0c924-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="0c924-105">Wdrażanie aplikacji sieci web w usłudze Azure App Service można wykonać ręcznie lub za pomocą zautomatyzowanego procesu.</span><span class="sxs-lookup"><span data-stu-id="0c924-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="0c924-106">W tej sekcji przewodnika omówiono metody wdrażania, które mogą być wyzwalane ręcznie lub za pomocą skryptu przy użyciu wiersza polecenia lub wyzwalane ręcznie przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c924-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="0c924-107">W tej sekcji wykonasz następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="0c924-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="0c924-108">Pobierz i skompiluj przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="0c924-108">Download and build the sample app.</span></span>
* <span data-ttu-id="0c924-109">Utwórz aplikację sieci Web usługi Azure App Service przy użyciu usługi Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="0c924-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="0c924-110">Wdrażanie przykładowej aplikacji na platformie Azure przy użyciu git.</span><span class="sxs-lookup"><span data-stu-id="0c924-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="0c924-111">Wdrażanie zmiany w aplikacji za pomocą programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c924-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="0c924-112">Dodaj miejsce przejściowe do aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="0c924-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="0c924-113">Wdrażanie aktualizacji do miejsca przejściowego.</span><span class="sxs-lookup"><span data-stu-id="0c924-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="0c924-114">Zamień miejsce przejściowe i miejsce produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="0c924-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="0c924-115">Pobieranie i testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="0c924-115">Download and test the app</span></span>

<span data-ttu-id="0c924-116">Aplikacja używana w tym przewodniku jest wstępnie zbudowany ASP.NET Core aplikacji, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="0c924-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="0c924-117">Jest to aplikacja Razor Pages, `Microsoft.SyndicationFeed.ReaderWriter` która używa interfejsu API do pobierania kanału RSS/Atom i wyświetlania elementów wiadomości na liście.</span><span class="sxs-lookup"><span data-stu-id="0c924-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="0c924-118">Zapraszam do przeglądania kodu, ale ważne jest, aby zrozumieć, że nie ma nic specjalnego w tej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0c924-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="0c924-119">To po prostu prosta aplikacja ASP.NET Core w celach ilustracyjnych.</span><span class="sxs-lookup"><span data-stu-id="0c924-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="0c924-120">Z powłoki poleceń pobierz kod, skompiluj projekt i uruchom go w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="0c924-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="0c924-121">*Uwaga: Użytkownicy systemów Linux/macOS powinni wprowadzić odpowiednie zmiany w ścieżkach, np.`/``\`*</span><span class="sxs-lookup"><span data-stu-id="0c924-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="0c924-122">Sklonuj kod do folderu na komputerze lokalnym.</span><span class="sxs-lookup"><span data-stu-id="0c924-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="0c924-123">Zmień folder roboczy na utworzony folder *prostego czytnika kanałów informacyjnych.*</span><span class="sxs-lookup"><span data-stu-id="0c924-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="0c924-124">Przywróć pakiety i skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="0c924-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="0c924-125">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="0c924-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Polecenie dotnet run zakończyło się pomyślnie](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="0c924-127">Otwórz przeglądarkę i przejdź pod adres `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="0c924-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="0c924-128">Aplikacja pozwala wpisać lub wkleić adres URL źródła danych zesłania i wyświetlić listę elementów wiadomości.</span><span class="sxs-lookup"><span data-stu-id="0c924-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Aplikacja wyświetlająca zawartość kanału RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="0c924-130">Po upewnieniu się, że aplikacja działa poprawnie, zamknij ją, naciskając **klawisz Ctrl**+**C** w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="0c924-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="0c924-131">Tworzenie aplikacji sieci Web usługi Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0c924-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="0c924-132">Aby wdrożyć aplikację, musisz utworzyć aplikację [sieci Web](/azure/app-service/app-service-web-overview)usługi app.</span><span class="sxs-lookup"><span data-stu-id="0c924-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="0c924-133">Po utworzeniu aplikacji sieci Web, można wdrożyć do niego z komputera lokalnego przy użyciu git.</span><span class="sxs-lookup"><span data-stu-id="0c924-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="0c924-134">Zaloguj się do usługi [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="0c924-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="0c924-135">Uwaga: Gdy zalogujesz się po raz pierwszy, usługa Cloud Shell wyświetli monit o utworzenie konta magazynu dla plików konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="0c924-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="0c924-136">Zaakceptuj ustawienia domyślne lub podaj unikatową nazwę.</span><span class="sxs-lookup"><span data-stu-id="0c924-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="0c924-137">Użyj powłoki chmury dla następujących kroków.</span><span class="sxs-lookup"><span data-stu-id="0c924-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="0c924-138">a.</span><span class="sxs-lookup"><span data-stu-id="0c924-138">a.</span></span> <span data-ttu-id="0c924-139">Zadeklaruj zmienną do przechowywania nazwy aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="0c924-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="0c924-140">Nazwa musi być unikatowa, aby była używana w domyślnym adresie URL.</span><span class="sxs-lookup"><span data-stu-id="0c924-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="0c924-141">`$RANDOM` Za pomocą bash funkcji do konstruowania nazwy `webappname99999`gwarantuje unikatowość i wyniki w formacie .</span><span class="sxs-lookup"><span data-stu-id="0c924-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="0c924-142">b.</span><span class="sxs-lookup"><span data-stu-id="0c924-142">b.</span></span> <span data-ttu-id="0c924-143">Utwórz grupę zasobów.</span><span class="sxs-lookup"><span data-stu-id="0c924-143">Create a resource group.</span></span> <span data-ttu-id="0c924-144">Grupy zasobów zapewniają środki do agregowania zasobów platformy Azure, które mają być zarządzane jako grupa.</span><span class="sxs-lookup"><span data-stu-id="0c924-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="0c924-145">Polecenie `az` wywołuje interfejsu [wiersza polecenia platformy Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="0c924-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="0c924-146">Cli można uruchomić lokalnie, ale przy użyciu go w cloud shell oszczędza czas i konfigurację.</span><span class="sxs-lookup"><span data-stu-id="0c924-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="0c924-147">d.</span><span class="sxs-lookup"><span data-stu-id="0c924-147">c.</span></span> <span data-ttu-id="0c924-148">Utwórz plan usługi app service w warstwie S1.</span><span class="sxs-lookup"><span data-stu-id="0c924-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="0c924-149">Plan usługi aplikacji to grupowanie aplikacji sieci web, które mają tę samą warstwę cenową.</span><span class="sxs-lookup"><span data-stu-id="0c924-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="0c924-150">Warstwa S1 nie jest bezpłatna, ale jest wymagana dla funkcji miejsca przejściowe.</span><span class="sxs-lookup"><span data-stu-id="0c924-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="0c924-151">d.</span><span class="sxs-lookup"><span data-stu-id="0c924-151">d.</span></span> <span data-ttu-id="0c924-152">Utwórz zasób aplikacji sieci Web przy użyciu planu usługi app service w tej samej grupie zasobów.</span><span class="sxs-lookup"><span data-stu-id="0c924-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="0c924-153">e.</span><span class="sxs-lookup"><span data-stu-id="0c924-153">e.</span></span> <span data-ttu-id="0c924-154">Ustaw poświadczenia wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="0c924-154">Set the deployment credentials.</span></span> <span data-ttu-id="0c924-155">Te poświadczenia wdrożenia dotyczą wszystkich aplikacji sieci web w ramach subskrypcji.</span><span class="sxs-lookup"><span data-stu-id="0c924-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="0c924-156">Nie używaj znaków specjalnych w nazwie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0c924-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="0c924-157">f.</span><span class="sxs-lookup"><span data-stu-id="0c924-157">f.</span></span> <span data-ttu-id="0c924-158">Skonfiguruj aplikację internetową tak, aby akceptowała wdrożenia z lokalnego gita i wyświetlała *adres URL wdrożenia Gita*.</span><span class="sxs-lookup"><span data-stu-id="0c924-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="0c924-159">**Zanotuj ten adres URL w celu późniejszego odniesienia**.</span><span class="sxs-lookup"><span data-stu-id="0c924-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="0c924-160">g.</span><span class="sxs-lookup"><span data-stu-id="0c924-160">g.</span></span> <span data-ttu-id="0c924-161">Wyświetl *adres URL aplikacji internetowej*.</span><span class="sxs-lookup"><span data-stu-id="0c924-161">Display the *web app URL*.</span></span> <span data-ttu-id="0c924-162">Przejdź do tego adresu URL, aby wyświetlić pustą aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="0c924-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="0c924-163">**Zanotuj ten adres URL w celu późniejszego odniesienia**.</span><span class="sxs-lookup"><span data-stu-id="0c924-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="0c924-164">Korzystając z powłoki poleceń na komputerze lokalnym, przejdź do folderu projektu aplikacji sieci web (na przykład `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="0c924-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="0c924-165">Wykonaj następujące polecenia, aby skonfigurować git do wypychania do adresu URL wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="0c924-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="0c924-166">a.</span><span class="sxs-lookup"><span data-stu-id="0c924-166">a.</span></span> <span data-ttu-id="0c924-167">Dodaj zdalny adres URL do lokalnego repozytorium.</span><span class="sxs-lookup"><span data-stu-id="0c924-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="0c924-168">b.</span><span class="sxs-lookup"><span data-stu-id="0c924-168">b.</span></span> <span data-ttu-id="0c924-169">Wypchnij lokalną gałąź *wzorcową* do gałęzi *głównej* pilota *azure-prod.*</span><span class="sxs-lookup"><span data-stu-id="0c924-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="0c924-170">Zostanie wyświetlony monit o poświadczenia wdrożenia utworzone wcześniej.</span><span class="sxs-lookup"><span data-stu-id="0c924-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="0c924-171">Obserwować dane wyjściowe w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="0c924-171">Observe the output in the command shell.</span></span> <span data-ttu-id="0c924-172">Platforma Azure zdalnie tworzy aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c924-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="0c924-173">W przeglądarce przejdź do *adresu URL aplikacji sieci Web* i zanotuj, że aplikacja została sbudona i wdrożona.</span><span class="sxs-lookup"><span data-stu-id="0c924-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="0c924-174">Dodatkowe zmiany mogą zostać wprowadzone do lokalnego repozytorium Git z . `git commit`</span><span class="sxs-lookup"><span data-stu-id="0c924-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="0c924-175">Te zmiany są wypychane na `git push` platformę Azure za pomocą poprzedniego polecenia.</span><span class="sxs-lookup"><span data-stu-id="0c924-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="0c924-176">Wdrażanie za pomocą programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c924-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="0c924-177">*Uwaga: Ta sekcja dotyczy tylko systemu Windows. Użytkownicy systemów Linux i macOS powinni wprowadzić zmiany opisane w kroku 2 poniżej. Zapisz plik i zatwierdź zmianę w lokalnym `git commit`repozytorium za pomocą pliku . Na koniec naciśnij zmianę `git push`za pomocą , jak w pierwszej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="0c924-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="0c924-178">Aplikacja została już wdrożona z powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="0c924-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="0c924-179">Użyjmy zintegrowanych narzędzi programu Visual Studio do wdrożenia aktualizacji w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0c924-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="0c924-180">W tle visual studio wykonuje to samo, co narzędzia wiersza polecenia, ale w programie Visual Studio znanego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0c924-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="0c924-181">Otwórz *simplefeedreader.sln* w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c924-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="0c924-182">W Eksploratorze rozwiązań otwórz *stronę Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0c924-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="0c924-183">Zmień `<h2>Simple Feed Reader</h2>` `<h2>Simple Feed Reader - V2</h2>`na .</span><span class="sxs-lookup"><span data-stu-id="0c924-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="0c924-184">Naciśnij **klawisze Ctrl**+**Shift**+**B,** aby utworzyć aplikację.</span><span class="sxs-lookup"><span data-stu-id="0c924-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="0c924-185">W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i kliknij polecenie **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="0c924-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Zrzut ekranu przedstawiający kliknięcie prawym przyciskiem myszy, Opublikuj](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="0c924-187">Visual Studio można utworzyć nowy zasób usługi App Service, ale ta aktualizacja zostanie opublikowana w istniejącym wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="0c924-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="0c924-188">W oknie **dialogowym Wybieranie celu publikowania** wybierz pozycję **App Service** z listy po lewej stronie, a następnie wybierz pozycję **Wybierz istniejący**.</span><span class="sxs-lookup"><span data-stu-id="0c924-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="0c924-189">Kliknij przycisk **Opublikuj**.</span><span class="sxs-lookup"><span data-stu-id="0c924-189">Click **Publish**.</span></span>
6. <span data-ttu-id="0c924-190">W oknie dialogowym **Usługi aplikacji** upewnij się, że konto microsoft lub konto organizacyjne używane do tworzenia subskrypcji platformy Azure jest wyświetlane w prawym górnym rogu.</span><span class="sxs-lookup"><span data-stu-id="0c924-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="0c924-191">Jeśli tak nie jest, kliknij pozycję rozwijaną i dodaj ją.</span><span class="sxs-lookup"><span data-stu-id="0c924-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="0c924-192">Upewnij się, że wybrano poprawną **subskrypcję** platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0c924-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="0c924-193">W **obszarze Widok**wybierz pozycję Grupa **zasobów**.</span><span class="sxs-lookup"><span data-stu-id="0c924-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="0c924-194">Rozwiń grupę zasobów **AzureTutorial,** a następnie wybierz istniejącą aplikację sieci web.</span><span class="sxs-lookup"><span data-stu-id="0c924-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="0c924-195">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="0c924-195">Click **OK**.</span></span>

    ![Zrzut ekranu przedstawiający okno dialogowe Publikowania usługi aplikacji](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="0c924-197">Visual Studio tworzy i wdraża aplikację na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="0c924-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="0c924-198">Przejdź do adresu URL aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="0c924-198">Browse to the web app URL.</span></span> <span data-ttu-id="0c924-199">Sprawdź, czy `<h2>` modyfikacja elementu jest na żywo.</span><span class="sxs-lookup"><span data-stu-id="0c924-199">Validate that the `<h2>` element modification is live.</span></span>

![Aplikacja ze zmienionym tytułem](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="0c924-201">Miejsca wdrożenia</span><span class="sxs-lookup"><span data-stu-id="0c924-201">Deployment slots</span></span>

<span data-ttu-id="0c924-202">Gniazda wdrażania obsługują przemieszczania zmian bez wpływu na aplikację działającą w produkcji.</span><span class="sxs-lookup"><span data-stu-id="0c924-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="0c924-203">Po etapowej wersji aplikacji jest sprawdzany przez zespół zapewnienia jakości, produkcji i przemieszczania slotów mogą być zamienione.</span><span class="sxs-lookup"><span data-stu-id="0c924-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="0c924-204">Aplikacja w przemieszczania jest promowany do produkcji w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="0c924-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="0c924-205">Poniższe kroki tworzą miejsce przejściowe, wdrażają do niego pewne zmiany i zamieniaj miejsce przejściowe z produkcją po weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="0c924-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="0c924-206">Zaloguj się do [usługi Azure Cloud Shell](https://shell.azure.com/bash), jeśli nie jest jeszcze zalogowany.</span><span class="sxs-lookup"><span data-stu-id="0c924-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="0c924-207">Utwórz miejsce przejściowe.</span><span class="sxs-lookup"><span data-stu-id="0c924-207">Create the staging slot.</span></span>

    <span data-ttu-id="0c924-208">a.</span><span class="sxs-lookup"><span data-stu-id="0c924-208">a.</span></span> <span data-ttu-id="0c924-209">Utwórz miejsce wdrożenia o nazwie *przemieszczania*.</span><span class="sxs-lookup"><span data-stu-id="0c924-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="0c924-210">b.</span><span class="sxs-lookup"><span data-stu-id="0c924-210">b.</span></span> <span data-ttu-id="0c924-211">Skonfiguruj miejsce przejściowe do używania wdrożenia z lokalnego gita i uzyskaj adres URL wdrożenia **przejściowego.**</span><span class="sxs-lookup"><span data-stu-id="0c924-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="0c924-212">**Zanotuj ten adres URL w celu późniejszego odniesienia**.</span><span class="sxs-lookup"><span data-stu-id="0c924-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="0c924-213">d.</span><span class="sxs-lookup"><span data-stu-id="0c924-213">c.</span></span> <span data-ttu-id="0c924-214">Wyświetl adres URL miejsca przemieszczania.</span><span class="sxs-lookup"><span data-stu-id="0c924-214">Display the staging slot's URL.</span></span> <span data-ttu-id="0c924-215">Przejdź do adresu URL, aby wyświetlić puste miejsce przemieszczania.</span><span class="sxs-lookup"><span data-stu-id="0c924-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="0c924-216">**Zanotuj ten adres URL w celu późniejszego odniesienia**.</span><span class="sxs-lookup"><span data-stu-id="0c924-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="0c924-217">W edytorze tekstu lub programie Visual Studio ponownie zmodyfikuj *pages/index.cshtml,* aby `<h2>` element odczytywana `<h2>Simple Feed Reader - V3</h2>` i zapisać plik.</span><span class="sxs-lookup"><span data-stu-id="0c924-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="0c924-218">Zaagnalizuj plik do lokalnego repozytorium Git, używając strony **Zmiany** na karcie *Eksplorator zespołu* programu Visual Studio lub wprowadzając następujące elementy przy użyciu powłoki poleceń komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="0c924-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="0c924-219">Korzystając z powłoki poleceń komputera lokalnego, dodaj adres URL wdrożenia przemieszczania jako pilot Git i wypchnij zatwierdzone zmiany:</span><span class="sxs-lookup"><span data-stu-id="0c924-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="0c924-220">a.</span><span class="sxs-lookup"><span data-stu-id="0c924-220">a.</span></span> <span data-ttu-id="0c924-221">Dodaj zdalny adres URL do przemieszczania do lokalnego repozytorium Git.</span><span class="sxs-lookup"><span data-stu-id="0c924-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="0c924-222">b.</span><span class="sxs-lookup"><span data-stu-id="0c924-222">b.</span></span> <span data-ttu-id="0c924-223">Wypchnij lokalną gałąź *wzorcową* do gałęzi *głównej* pilota zdalnego *przemieszczania azure.*</span><span class="sxs-lookup"><span data-stu-id="0c924-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="0c924-224">Poczekaj, aż platforma Azure skompiluje i wdroży aplikację.</span><span class="sxs-lookup"><span data-stu-id="0c924-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="0c924-225">Aby sprawdzić, czy wersja 3 została wdrożona w miejscu przejściowym, otwórz dwa okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0c924-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="0c924-226">W jednym oknie przejdź do oryginalnego adresu URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="0c924-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="0c924-227">W drugim oknie przejdź do adresu URL aplikacji sieci web przemieszczania.</span><span class="sxs-lookup"><span data-stu-id="0c924-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="0c924-228">Produkcyjny adres URL obsługuje w wersji 2 aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0c924-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="0c924-229">Przemieszczania adres URL obsługuje V3 aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0c924-229">The staging URL serves V3 of the app.</span></span>

    ![Zrzut ekranu porównujący okna przeglądarki](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="0c924-231">W usłudze Cloud Shell zamienić zweryfikowane/rozgrzane miejsce postoju na produkcję.</span><span class="sxs-lookup"><span data-stu-id="0c924-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="0c924-232">Sprawdź, czy doszło do wymiany, odświeżając dwa okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0c924-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Porównywanie okien przeglądarki po wymianie](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="0c924-234">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="0c924-234">Summary</span></span>

<span data-ttu-id="0c924-235">W tej sekcji wykonano następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="0c924-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="0c924-236">Pobrano i sbudowa przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="0c924-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="0c924-237">Utworzono aplikację sieci Web usługi Azure App Service przy użyciu usługi Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="0c924-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="0c924-238">Wdrożono przykładową aplikację na platformie Azure przy użyciu git.</span><span class="sxs-lookup"><span data-stu-id="0c924-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="0c924-239">Wdrożono zmianę w aplikacji przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c924-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="0c924-240">Dodano miejsce przejściowe do aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="0c924-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="0c924-241">Wdrożono aktualizację miejsca przejściowego.</span><span class="sxs-lookup"><span data-stu-id="0c924-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="0c924-242">Zamieniliśmy miejsca przemieszczania i produkcji.</span><span class="sxs-lookup"><span data-stu-id="0c924-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="0c924-243">W następnej sekcji dowiesz się, jak utworzyć potok DevOps za pomocą usługi Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="0c924-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="0c924-244">Dodatkowa lektura</span><span class="sxs-lookup"><span data-stu-id="0c924-244">Additional reading</span></span>

* [<span data-ttu-id="0c924-245">Omówienie aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="0c924-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="0c924-246">Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0c924-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="0c924-247">Konfigurowanie poświadczeń wdrażania usługi Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0c924-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="0c924-248">Konfigurowanie środowisk przejściowych w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0c924-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)

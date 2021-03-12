---
title: Wdrażanie aplikacji w usłudze App Service DevOps przy użyciu ASP.NET Core i platformy Azure
author: CamSoper
description: Wdróż aplikację ASP.NET Core do Azure App Service, pierwszy krok dla DevOps z ASP.NET Core i platformą Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: 6d82611eb2176126652d0d6c8db3779cde70dab5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586244"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="25aec-103">Wdróż aplikację w App Service</span><span class="sxs-lookup"><span data-stu-id="25aec-103">Deploy an app to App Service</span></span>

<span data-ttu-id="25aec-104">[Azure App Service](/azure/app-service/) to platforma hostingu w sieci Web platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="25aec-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="25aec-105">Wdrażanie aplikacji sieci Web do Azure App Service można wykonać ręcznie lub przez proces zautomatyzowany.</span><span class="sxs-lookup"><span data-stu-id="25aec-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="25aec-106">W tej części przewodnika omówiono metody wdrażania, które mogą być wyzwalane ręcznie lub przez skrypt przy użyciu wiersza polecenia lub wyzwalane ręcznie przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25aec-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="25aec-107">W tej sekcji zostaną wykonane następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="25aec-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="25aec-108">Pobierz aplikację przykładową i skompiluj ją.</span><span class="sxs-lookup"><span data-stu-id="25aec-108">Download and build the sample app.</span></span>
* <span data-ttu-id="25aec-109">Utwórz Azure App Service aplikację sieci Web przy użyciu Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="25aec-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="25aec-110">Wdróż przykładową aplikację na platformie Azure przy użyciu narzędzia Git.</span><span class="sxs-lookup"><span data-stu-id="25aec-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="25aec-111">Wdróż zmianę w aplikacji przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25aec-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="25aec-112">Dodaj miejsce przejściowe do aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="25aec-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="25aec-113">Wdróż aktualizację do miejsca przejściowego.</span><span class="sxs-lookup"><span data-stu-id="25aec-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="25aec-114">Zamień miejsce przejściowe i miejsce produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="25aec-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="25aec-115">Pobieranie i testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="25aec-115">Download and test the app</span></span>

<span data-ttu-id="25aec-116">Aplikacja używana w tym przewodniku jest wstępnie zbudowaną aplikacją ASP.NET Core, [prostym czytnikiem strumieniowego źródła danych](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="25aec-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="25aec-117">Jest to Razor aplikacja ze stronami, która używa `Microsoft.SyndicationFeed.ReaderWriter` interfejsu API do pobierania źródła danych RSS/Atom i wyświetlania elementów wiadomości na liście.</span><span class="sxs-lookup"><span data-stu-id="25aec-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="25aec-118">Możesz przejrzeć kod, ale ważne jest, aby zrozumieć, że nie ma żadnych specjalnych informacji o tej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="25aec-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="25aec-119">Jest to tylko prosta aplikacja ASP.NET Core do celów informacyjnych.</span><span class="sxs-lookup"><span data-stu-id="25aec-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="25aec-120">Z poziomu powłoki poleceń Pobierz kod, Skompiluj projekt i uruchom go w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="25aec-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="25aec-121">*Uwaga: Użytkownicy systemu Linux/macOS powinni wprowadzać odpowiednie zmiany w ścieżkach, np. przy użyciu ukośnika ( `/` ), a nie ukośnika odwrotnego ( `\` ).*</span><span class="sxs-lookup"><span data-stu-id="25aec-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="25aec-122">Sklonuj kod do folderu na komputerze lokalnym.</span><span class="sxs-lookup"><span data-stu-id="25aec-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="25aec-123">Zmień folder roboczy na utworzony folder *czytnika źródła danych* .</span><span class="sxs-lookup"><span data-stu-id="25aec-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="25aec-124">Przywróć pakiety i skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="25aec-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="25aec-125">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="25aec-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Pomyślnie uruchomiono polecenie dotnet](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="25aec-127">Otwórz przeglądarkę i przejdź pod adres `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="25aec-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="25aec-128">Aplikacja umożliwia wpisanie lub wklejenie adresu URL źródła zespolonego oraz wyświetlenie listy elementów wiadomości.</span><span class="sxs-lookup"><span data-stu-id="25aec-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Aplikacja wyświetlająca zawartość kanału informacyjnego RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="25aec-130">Po upewnieniu się, że aplikacja działa prawidłowo, zamknij ją, naciskając klawisz **Ctrl** + **C** w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="25aec-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="25aec-131">Tworzenie aplikacji sieci Web Azure App Service</span><span class="sxs-lookup"><span data-stu-id="25aec-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="25aec-132">Aby wdrożyć aplikację, musisz utworzyć App Service [aplikację sieci Web](/azure/app-service/app-service-web-overview).</span><span class="sxs-lookup"><span data-stu-id="25aec-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="25aec-133">Po utworzeniu aplikacji sieci Web zostanie ona wdrożona na komputerze lokalnym za pomocą usługi git.</span><span class="sxs-lookup"><span data-stu-id="25aec-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="25aec-134">Zaloguj się do usługi [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="25aec-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="25aec-135">Uwaga: po pierwszym zalogowaniu Cloud Shell zostanie wyświetlony komunikat z prośbą o utworzenie konta magazynu dla plików konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="25aec-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="25aec-136">Zaakceptuj wartości domyślne lub podaj unikatową nazwę.</span><span class="sxs-lookup"><span data-stu-id="25aec-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="25aec-137">Użyj Cloud Shell, aby wykonać następujące czynności.</span><span class="sxs-lookup"><span data-stu-id="25aec-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="25aec-138">a.</span><span class="sxs-lookup"><span data-stu-id="25aec-138">a.</span></span> <span data-ttu-id="25aec-139">Zadeklaruj zmienną do przechowywania nazwy aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="25aec-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="25aec-140">Nazwa musi być unikatowa, aby można jej było używać w domyślnym adresie URL.</span><span class="sxs-lookup"><span data-stu-id="25aec-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="25aec-141">Użycie `$RANDOM` funkcji bash do konstruowania nazwy gwarantuje unikatowość i wyniki w formacie `webappname99999` .</span><span class="sxs-lookup"><span data-stu-id="25aec-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="25aec-142">b.</span><span class="sxs-lookup"><span data-stu-id="25aec-142">b.</span></span> <span data-ttu-id="25aec-143">Utwórz grupę zasobów.</span><span class="sxs-lookup"><span data-stu-id="25aec-143">Create a resource group.</span></span> <span data-ttu-id="25aec-144">Grupy zasobów zapewniają metodę agregowania zasobów platformy Azure, która ma być zarządzana jako Grupa.</span><span class="sxs-lookup"><span data-stu-id="25aec-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="25aec-145">`az`Polecenie wywołuje [interfejs wiersza polecenia platformy Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="25aec-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="25aec-146">Interfejs wiersza polecenia może być uruchamiany lokalnie, ale jego użycie w Cloud Shell pozwala zaoszczędzić czas i konfigurację.</span><span class="sxs-lookup"><span data-stu-id="25aec-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="25aec-147">c.</span><span class="sxs-lookup"><span data-stu-id="25aec-147">c.</span></span> <span data-ttu-id="25aec-148">Utwórz plan App Service w warstwie S1.</span><span class="sxs-lookup"><span data-stu-id="25aec-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="25aec-149">Plan App Service jest grupą aplikacji sieci Web, które współużytkują tę samą warstwę cenową.</span><span class="sxs-lookup"><span data-stu-id="25aec-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="25aec-150">Warstwa S1 nie jest bezpłatna, ale jest wymagana w przypadku funkcji miejsc przejściowych.</span><span class="sxs-lookup"><span data-stu-id="25aec-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="25aec-151">d.</span><span class="sxs-lookup"><span data-stu-id="25aec-151">d.</span></span> <span data-ttu-id="25aec-152">Utwórz zasób aplikacji sieci Web przy użyciu planu App Service w tej samej grupie zasobów.</span><span class="sxs-lookup"><span data-stu-id="25aec-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="25aec-153">e.</span><span class="sxs-lookup"><span data-stu-id="25aec-153">e.</span></span> <span data-ttu-id="25aec-154">Ustaw poświadczenia wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="25aec-154">Set the deployment credentials.</span></span> <span data-ttu-id="25aec-155">Te poświadczenia wdrażania dotyczą wszystkich aplikacji sieci Web w Twojej subskrypcji.</span><span class="sxs-lookup"><span data-stu-id="25aec-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="25aec-156">Nie używaj znaków specjalnych w nazwie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="25aec-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="25aec-157">f.</span><span class="sxs-lookup"><span data-stu-id="25aec-157">f.</span></span> <span data-ttu-id="25aec-158">Skonfiguruj aplikację sieci Web tak, aby akceptowała wdrożenia z lokalnego narzędzia Git i wyświetlała *adres URL wdrożenia narzędzia Git*.</span><span class="sxs-lookup"><span data-stu-id="25aec-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="25aec-159">**Zanotuj ten adres URL później**.</span><span class="sxs-lookup"><span data-stu-id="25aec-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="25aec-160">przykład</span><span class="sxs-lookup"><span data-stu-id="25aec-160">g.</span></span> <span data-ttu-id="25aec-161">Wyświetl *adres URL aplikacji sieci Web*.</span><span class="sxs-lookup"><span data-stu-id="25aec-161">Display the *web app URL*.</span></span> <span data-ttu-id="25aec-162">Przejdź do tego adresu URL, aby wyświetlić pustą aplikację sieci Web.</span><span class="sxs-lookup"><span data-stu-id="25aec-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="25aec-163">**Zanotuj ten adres URL później**.</span><span class="sxs-lookup"><span data-stu-id="25aec-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="25aec-164">Przy użyciu powłoki poleceń na komputerze lokalnym przejdź do folderu projektu aplikacji sieci Web (na przykład `.\simple-feed-reader\SimpleFeedReader` ).</span><span class="sxs-lookup"><span data-stu-id="25aec-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="25aec-165">Wykonaj następujące polecenia, aby skonfigurować usługę git do wypychania do adresu URL wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="25aec-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="25aec-166">a.</span><span class="sxs-lookup"><span data-stu-id="25aec-166">a.</span></span> <span data-ttu-id="25aec-167">Dodaj zdalny adres URL do repozytorium lokalnego.</span><span class="sxs-lookup"><span data-stu-id="25aec-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="25aec-168">b.</span><span class="sxs-lookup"><span data-stu-id="25aec-168">b.</span></span> <span data-ttu-id="25aec-169">Wypchnij lokalną gałąź domyślną (*główną*) do domyślnej gałęzi *platformy Azure-prod* (*głównej*).</span><span class="sxs-lookup"><span data-stu-id="25aec-169">Push the local default branch (*main*) to the *azure-prod* remote's default branch (*main*).</span></span>

    ```console
    git push azure-prod main
    ```

    <span data-ttu-id="25aec-170">Zostanie wyświetlony monit o utworzenie wcześniej utworzonych poświadczeń wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="25aec-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="25aec-171">Obserwuj dane wyjściowe w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="25aec-171">Observe the output in the command shell.</span></span> <span data-ttu-id="25aec-172">Platforma Azure kompiluje aplikację ASP.NET Core zdalnie.</span><span class="sxs-lookup"><span data-stu-id="25aec-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="25aec-173">W przeglądarce przejdź do *adresu URL aplikacji sieci Web* i zanotuj, że aplikacja została skompilowana i wdrożona.</span><span class="sxs-lookup"><span data-stu-id="25aec-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="25aec-174">Dodatkowe zmiany można zatwierdzić w lokalnym repozytorium git przy użyciu programu `git commit` .</span><span class="sxs-lookup"><span data-stu-id="25aec-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="25aec-175">Te zmiany są przekazywane do platformy Azure przy użyciu poprzedniego `git push` polecenia.</span><span class="sxs-lookup"><span data-stu-id="25aec-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="25aec-176">Wdrażanie za pomocą programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25aec-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="25aec-177">*Uwaga: Ta sekcja dotyczy tylko systemu Windows. Użytkownicy systemów Linux i macOS powinni wprowadzić zmiany opisane w kroku 2 poniżej. Zapisz plik i zatwierdź zmianę w repozytorium lokalnym za pomocą programu `git commit` . Na koniec wypchnij zmiany z `git push` , jak w pierwszej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="25aec-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="25aec-178">Aplikacja została już wdrożona z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="25aec-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="25aec-179">Użyjmy zintegrowanych narzędzi programu Visual Studio, aby wdrożyć aktualizację do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="25aec-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="25aec-180">W tle program Visual Studio wykonuje te same czynności co w przypadku narzędzi wiersza polecenia, ale w znanym interfejsie użytkownika programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25aec-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="25aec-181">Otwórz *SimpleFeedReader. sln* w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25aec-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="25aec-182">W Eksplorator rozwiązań Otwórz *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="25aec-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="25aec-183">Zmień `<h2>Simple Feed Reader</h2>` na `<h2>Simple Feed Reader - V2</h2>` .</span><span class="sxs-lookup"><span data-stu-id="25aec-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="25aec-184">Naciśnij **klawisze CTRL** + **SHIFT** + **B** , aby skompilować aplikację.</span><span class="sxs-lookup"><span data-stu-id="25aec-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="25aec-185">W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij pozycję **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="25aec-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Zrzut ekranu przedstawiający kliknięcie prawym przyciskiem myszy, Publikuj](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="25aec-187">Program Visual Studio może utworzyć nowy zasób App Service, ale ta aktualizacja zostanie opublikowana w ramach istniejącego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="25aec-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="25aec-188">W oknie dialogowym **Wybieranie elementu docelowego publikowania** wybierz pozycję **App Service** z listy po lewej stronie, a następnie wybierz pozycję **Wybierz istniejące**.</span><span class="sxs-lookup"><span data-stu-id="25aec-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="25aec-189">Kliknij przycisk **Opublikuj**.</span><span class="sxs-lookup"><span data-stu-id="25aec-189">Click **Publish**.</span></span>
6. <span data-ttu-id="25aec-190">W oknie dialogowym **App Service** upewnij się, że konto Microsoft lub organizacyjne używane do tworzenia subskrypcji platformy Azure jest wyświetlane w prawym górnym rogu.</span><span class="sxs-lookup"><span data-stu-id="25aec-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="25aec-191">Jeśli nie, kliknij listę rozwijaną i Dodaj ją.</span><span class="sxs-lookup"><span data-stu-id="25aec-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="25aec-192">Upewnij się, że wybrano poprawną **subskrypcję** platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="25aec-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="25aec-193">W obszarze **Widok** wybierz pozycję **Grupa zasobów**.</span><span class="sxs-lookup"><span data-stu-id="25aec-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="25aec-194">Rozwiń grupę zasobów **AzureTutorial** , a następnie wybierz istniejącą aplikację sieci Web.</span><span class="sxs-lookup"><span data-stu-id="25aec-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="25aec-195">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="25aec-195">Click **OK**.</span></span>

    ![Zrzut ekranu przedstawiający okno dialogowe publikowania App Service](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="25aec-197">Program Visual Studio kompiluje i wdraża aplikację na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="25aec-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="25aec-198">Przejdź do adresu URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="25aec-198">Browse to the web app URL.</span></span> <span data-ttu-id="25aec-199">Sprawdź, czy `<h2>` Modyfikacja elementu jest na żywo.</span><span class="sxs-lookup"><span data-stu-id="25aec-199">Validate that the `<h2>` element modification is live.</span></span>

![Aplikacja ze zmienionym tytułem](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="25aec-201">Miejsca wdrożenia</span><span class="sxs-lookup"><span data-stu-id="25aec-201">Deployment slots</span></span>

<span data-ttu-id="25aec-202">Miejsca wdrożenia obsługują przemieszczanie zmian bez wpływu na działanie aplikacji w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="25aec-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="25aec-203">Po sprawdzeniu przez zespół zapewnienia jakości przygotowanej wersji aplikacji można wymienić miejsca produkcyjne i przejściowe.</span><span class="sxs-lookup"><span data-stu-id="25aec-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="25aec-204">W ten sposób aplikacja do przemieszczania jest podwyższana do produkcji.</span><span class="sxs-lookup"><span data-stu-id="25aec-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="25aec-205">Poniższe kroki tworzą miejsce przejściowe, wdrażają w nim pewne zmiany i wymieniają miejsce przejściowe w środowisku produkcyjnym po weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="25aec-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="25aec-206">Zaloguj się do [Azure Cloud Shell](https://shell.azure.com/bash), jeśli jeszcze nie jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="25aec-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="25aec-207">Utwórz miejsce przejściowe.</span><span class="sxs-lookup"><span data-stu-id="25aec-207">Create the staging slot.</span></span>

    <span data-ttu-id="25aec-208">a.</span><span class="sxs-lookup"><span data-stu-id="25aec-208">a.</span></span> <span data-ttu-id="25aec-209">Utwórz miejsce wdrożenia o nazwie *tymczasowej*.</span><span class="sxs-lookup"><span data-stu-id="25aec-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="25aec-210">b.</span><span class="sxs-lookup"><span data-stu-id="25aec-210">b.</span></span> <span data-ttu-id="25aec-211">Skonfiguruj miejsce przejściowe tak, aby korzystało z wdrożenia z lokalnego narzędzia Git i uzyskać adres URL wdrożenia **przemieszczania** .</span><span class="sxs-lookup"><span data-stu-id="25aec-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="25aec-212">**Zanotuj ten adres URL później**.</span><span class="sxs-lookup"><span data-stu-id="25aec-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="25aec-213">c.</span><span class="sxs-lookup"><span data-stu-id="25aec-213">c.</span></span> <span data-ttu-id="25aec-214">Wyświetl adres URL miejsca przemieszczania.</span><span class="sxs-lookup"><span data-stu-id="25aec-214">Display the staging slot's URL.</span></span> <span data-ttu-id="25aec-215">Przejdź do adresu URL, aby wyświetlić puste miejsce przejściowe.</span><span class="sxs-lookup"><span data-stu-id="25aec-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="25aec-216">**Zanotuj ten adres URL później**.</span><span class="sxs-lookup"><span data-stu-id="25aec-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="25aec-217">W edytorze tekstu lub programie Visual Studio zmodyfikuj ponownie *strony/index. cshtml* , aby element był `<h2>` odczytywany `<h2>Simple Feed Reader - V3</h2>` i zapisywał plik.</span><span class="sxs-lookup"><span data-stu-id="25aec-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="25aec-218">Zatwierdź plik w lokalnym repozytorium git, używając strony **zmiany** w karcie *Team Explorer* w programie Visual Studio lub wprowadzając następujące polecenie przy użyciu powłoki poleceń komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="25aec-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="25aec-219">Przy użyciu powłoki poleceń komputera lokalnego Dodaj adres URL wdrożenia przemieszczania jako zdalny element git i wypchnij zatwierdzone zmiany:</span><span class="sxs-lookup"><span data-stu-id="25aec-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="25aec-220">a.</span><span class="sxs-lookup"><span data-stu-id="25aec-220">a.</span></span> <span data-ttu-id="25aec-221">Dodaj zdalny adres URL na potrzeby przemieszczania do lokalnego repozytorium git.</span><span class="sxs-lookup"><span data-stu-id="25aec-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="25aec-222">b.</span><span class="sxs-lookup"><span data-stu-id="25aec-222">b.</span></span> <span data-ttu-id="25aec-223">Wypchnij lokalną gałąź domyślną (*główną*) do gałęzi domyślnej dla zdalnego *rozmieszczenia platformy Azure* (*głównej*).</span><span class="sxs-lookup"><span data-stu-id="25aec-223">Push the local default branch (*main*) to the *azure-staging* remote's default branch (*main*).</span></span>

    ```console
    git push azure-staging main
    ```

    <span data-ttu-id="25aec-224">Zaczekaj, aż platforma Azure utworzy i wdroży aplikację.</span><span class="sxs-lookup"><span data-stu-id="25aec-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="25aec-225">Aby sprawdzić, czy wersja 3 została wdrożona w miejscu przejściowym, Otwórz dwa okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="25aec-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="25aec-226">W jednym oknie przejdź do oryginalnego adresu URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="25aec-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="25aec-227">W drugim oknie przejdź do adresu URL aplikacji sieci Web przemieszczania.</span><span class="sxs-lookup"><span data-stu-id="25aec-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="25aec-228">Produkcyjny adres URL służy do wdrożenia wersji 2 aplikacji.</span><span class="sxs-lookup"><span data-stu-id="25aec-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="25aec-229">Przejściowy adres URL służy do działania wersji 3 aplikacji.</span><span class="sxs-lookup"><span data-stu-id="25aec-229">The staging URL serves V3 of the app.</span></span>

    ![Zrzut ekranu porównujący okna przeglądarki](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="25aec-231">W Cloud Shell zamienić zweryfikowane i rozgrzane miejsce przejściowe na środowisko produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="25aec-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="25aec-232">Sprawdź, czy nastąpiło zastępowanie, odświeżając dwa okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="25aec-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Porównywanie okien przeglądarki po wymianie](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="25aec-234">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="25aec-234">Summary</span></span>

<span data-ttu-id="25aec-235">W tej sekcji zostały wykonane następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="25aec-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="25aec-236">Pobrano i skompilowano przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="25aec-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="25aec-237">Utworzono aplikację internetową Azure App Service przy użyciu Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="25aec-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="25aec-238">Wdrożono przykładową aplikację na platformie Azure przy użyciu narzędzia Git.</span><span class="sxs-lookup"><span data-stu-id="25aec-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="25aec-239">Wdrożono zmianę aplikacji przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25aec-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="25aec-240">Dodano miejsce przejściowe do aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="25aec-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="25aec-241">Wdrożono aktualizację do miejsca przejściowego.</span><span class="sxs-lookup"><span data-stu-id="25aec-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="25aec-242">Zamienione miejsca przejściowe i produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="25aec-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="25aec-243">W następnej sekcji dowiesz się, jak utworzyć potok DevOps za pomocą Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="25aec-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="25aec-244">Materiały uzupełniające</span><span class="sxs-lookup"><span data-stu-id="25aec-244">Additional reading</span></span>

* [<span data-ttu-id="25aec-245">Przegląd Web Apps</span><span class="sxs-lookup"><span data-stu-id="25aec-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="25aec-246">Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="25aec-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="25aec-247">Skonfiguruj poświadczenia wdrażania dla Azure App Service</span><span class="sxs-lookup"><span data-stu-id="25aec-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="25aec-248">Konfigurowanie środowisk przejściowych w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="25aec-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)

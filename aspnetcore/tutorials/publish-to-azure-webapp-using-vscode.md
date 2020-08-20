---
title: Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu Visual Studio Code
author: rick-anderson
description: Dowiedz się, jak opublikować aplikację ASP.NET Core do Azure App Service przy użyciu Visual Studio Code
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 113ebbefe338f49738095633c95ee3327b615078
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627900"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="2a77e-103">Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a77e-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="2a77e-104">Autor [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="2a77e-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="2a77e-105">Aby rozwiązać problem z wdrożeniem App Service, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="2a77e-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="2a77e-106">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="2a77e-106">Intro</span></span>

<span data-ttu-id="2a77e-107">Korzystając z tego samouczka, dowiesz się, jak utworzyć aplikację ASP.Net Core MVC i wdrożyć ją w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2a77e-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="2a77e-108">Konfiguruj</span><span class="sxs-lookup"><span data-stu-id="2a77e-108">Set up</span></span>

- <span data-ttu-id="2a77e-109">Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz.</span><span class="sxs-lookup"><span data-stu-id="2a77e-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="2a77e-110">Zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="2a77e-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="2a77e-111">Zainstaluj [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="2a77e-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="2a77e-112">Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) , aby Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a77e-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="2a77e-113">Zainstaluj [rozszerzenie Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) , aby Visual Studio Code i skonfigurować je przed kontynuowaniem</span><span class="sxs-lookup"><span data-stu-id="2a77e-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="2a77e-114">Tworzenie projektu ASP.Net Core MVC</span><span class="sxs-lookup"><span data-stu-id="2a77e-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="2a77e-115">Za pomocą terminalu przejdź do folderu, w którym ma zostać utworzony projekt, i użyj następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="2a77e-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="2a77e-116">Będziesz mieć strukturę folderów podobną do następującej:</span><span class="sxs-lookup"><span data-stu-id="2a77e-116">You'll have a folder structure similar to the following:</span></span>

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="2a77e-117">Otwórz za pomocą Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a77e-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="2a77e-118">Po utworzeniu projektu można go otworzyć z Visual Studio Code przy użyciu jednej z poniższych opcji:</span><span class="sxs-lookup"><span data-stu-id="2a77e-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="2a77e-119">Za pomocą wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="2a77e-119">Through the command line</span></span>

<span data-ttu-id="2a77e-120">Użyj następującego polecenia w folderze, w którym został utworzony projekt:</span><span class="sxs-lookup"><span data-stu-id="2a77e-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="2a77e-121">Jeśli poniższe polecenie nie działa, sprawdź, czy instalacja została prawidłowo skonfigurowana przez odwołanie do [tego linku](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="2a77e-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="2a77e-122">Za za poorednictwem Visual Studio Code interfejsu</span><span class="sxs-lookup"><span data-stu-id="2a77e-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="2a77e-123">Otwórz program Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a77e-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="2a77e-124">Z menu wybierz pozycję `File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="2a77e-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="2a77e-125">Wybierz katalog główny folderu, w którym został utworzony projekt MVC</span><span class="sxs-lookup"><span data-stu-id="2a77e-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="2a77e-126">Po otwarciu folderu projektu zostanie wyświetlony komunikat informujący o braku wymaganych zasobów do skompilowania i debugowania.</span><span class="sxs-lookup"><span data-stu-id="2a77e-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="2a77e-127">Zaakceptuj pomoc, aby je dodać.</span><span class="sxs-lookup"><span data-stu-id="2a77e-127">Accept the help to add them.</span></span>

![Interfejs Visual Studio Code z załadowanym projektem](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="2a77e-129">`.vscode`Folder zostanie utworzony w ramach struktury projektu.</span><span class="sxs-lookup"><span data-stu-id="2a77e-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="2a77e-130">Będzie zawierać następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2a77e-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="2a77e-131">Są to pliki narzędzi, które ułatwiają tworzenie i debugowanie aplikacji sieci Web platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a77e-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="2a77e-132">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2a77e-132">Run the app</span></span>

<span data-ttu-id="2a77e-133">Przed wdrożeniem aplikacji na platformie Azure upewnij się, że działa ona prawidłowo na komputerze lokalnym.</span><span class="sxs-lookup"><span data-stu-id="2a77e-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="2a77e-134">Naciśnij klawisz F5, aby uruchomić projekt</span><span class="sxs-lookup"><span data-stu-id="2a77e-134">Press F5 to run the project</span></span>

<span data-ttu-id="2a77e-135">Aplikacja sieci Web zacznie działać na nowej karcie domyślnej przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2a77e-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="2a77e-136">Ostrzeżenie o prywatności można zauważyć zaraz po rozpoczęciu.</span><span class="sxs-lookup"><span data-stu-id="2a77e-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="2a77e-137">Jest to spowodowane tym, że aplikacja rozpocznie korzystanie z protokołów HTTP i HTTPS i domyślnie przechodzi do punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2a77e-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Ostrzeżenie dotyczące prywatności podczas lokalnego debugowania aplikacji](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="2a77e-139">Aby zachować sesję debugowania, kliknij przycisk `Advanced` , a następnie `Continue to localhost (unsafe)` .</span><span class="sxs-lookup"><span data-stu-id="2a77e-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="2a77e-140">Wygeneruj lokalnie pakiet wdrożeniowy</span><span class="sxs-lookup"><span data-stu-id="2a77e-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="2a77e-141">Otwórz Visual Studio Code Terminal</span><span class="sxs-lookup"><span data-stu-id="2a77e-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="2a77e-142">Użyj następującego polecenia, aby wygenerować `Release` pakiet do podfolderu o nazwie `publish` :</span><span class="sxs-lookup"><span data-stu-id="2a77e-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="2a77e-143">Nowy `publish` folder zostanie utworzony pod strukturą projektu</span><span class="sxs-lookup"><span data-stu-id="2a77e-143">A new `publish` folder will be created under the project structure</span></span>

![Struktura folderu publikowania](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="2a77e-145">Publikowanie w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2a77e-145">Publish to Azure App Service</span></span>

<span data-ttu-id="2a77e-146">Korzystając z rozszerzenia Azure App Service Visual Studio Code, wykonaj poniższe kroki, aby opublikować witrynę internetową bezpośrednio w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2a77e-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="2a77e-147">Jeśli tworzysz nową aplikację sieci Web</span><span class="sxs-lookup"><span data-stu-id="2a77e-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="2a77e-148">Kliknij prawym przyciskiem myszy `publish` folder, a następnie wybierz pozycję `Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="2a77e-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="2a77e-149">Wybierz subskrypcję, dla której chcesz utworzyć aplikację sieci Web</span><span class="sxs-lookup"><span data-stu-id="2a77e-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="2a77e-150">Wybierz pozycję `Create New Web App`</span><span class="sxs-lookup"><span data-stu-id="2a77e-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="2a77e-151">Wprowadź nazwę aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="2a77e-151">Enter a name for the Web App</span></span>

<span data-ttu-id="2a77e-152">Rozszerzenie utworzy nową aplikację sieci Web i rozpocznie automatyczne wdrożenie pakietu.</span><span class="sxs-lookup"><span data-stu-id="2a77e-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="2a77e-153">Po zakończeniu wdrożenia kliknij, `Browse Website` Aby sprawdzić poprawność wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="2a77e-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Komunikat wdrożenia zakończony pomyślnie](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="2a77e-155">Po kliknięciu `Browse Website` Przejdź do niego przy użyciu domyślnej przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="2a77e-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Pomyślnie wdrożono nową aplikację sieci Web](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="2a77e-157">Jeśli wdrażasz w istniejącej aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="2a77e-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="2a77e-158">Kliknij prawym przyciskiem myszy `publish` folder, a następnie wybierz pozycję `Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="2a77e-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="2a77e-159">Wybierz subskrypcję istniejącej aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="2a77e-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="2a77e-160">Wybierz aplikację internetową z listy</span><span class="sxs-lookup"><span data-stu-id="2a77e-160">Select the Web App from the list</span></span>
- <span data-ttu-id="2a77e-161">Visual Studio Code będzie pytał, czy chcesz zastąpić istniejącą zawartość.</span><span class="sxs-lookup"><span data-stu-id="2a77e-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="2a77e-162">Kliknij `Deploy` , aby potwierdzić</span><span class="sxs-lookup"><span data-stu-id="2a77e-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="2a77e-163">Rozszerzenie spowoduje wdrożenie zaktualizowanej zawartości w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2a77e-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="2a77e-164">Po zakończeniu kliknij przycisk, `Browse Website` Aby sprawdzić poprawność wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="2a77e-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Istniejąca aplikacja sieci Web została pomyślnie wdrożona](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="2a77e-166">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="2a77e-166">Next steps</span></span>

- [<span data-ttu-id="2a77e-167">Tworzenie pierwszego potoku usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="2a77e-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="2a77e-168">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2a77e-168">Additional resources</span></span>

- [<span data-ttu-id="2a77e-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2a77e-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="2a77e-170">Grupa zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="2a77e-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)

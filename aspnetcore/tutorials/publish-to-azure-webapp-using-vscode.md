---
title: Publikowanie aplikacji ASP.NET Core na platformie Azure za pomocą kodu programu Visual Studio
author: rick-anderson
description: Dowiedz się, jak opublikować aplikację ASP.NET Core w usłudze Azure App Service przy użyciu kodu programu Visual Studio
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 5f117cb2867a6e7b54269ef39abe819256b429ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242681"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="3e4a8-103">Publikowanie aplikacji ASP.NET Core na platformie Azure za pomocą kodu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4a8-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="3e4a8-104">Przez [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="3e4a8-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="3e4a8-105">Aby rozwiązać problem z wdrażaniem usługi App Service, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="3e4a8-106">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="3e4a8-106">Intro</span></span>

<span data-ttu-id="3e4a8-107">W tym samouczku dowiesz się, jak utworzyć ASP.Net podstawową aplikację MVC i wdrożyć ją w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="3e4a8-108">Konfiguruj</span><span class="sxs-lookup"><span data-stu-id="3e4a8-108">Set up</span></span>

- <span data-ttu-id="3e4a8-109">Otwórz [bezpłatne konto platformy Azure,](https://azure.microsoft.com/free/dotnet/) jeśli go nie masz.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="3e4a8-110">Instalowanie [pakietu SDK core platformy .NET](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="3e4a8-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="3e4a8-111">Instalowanie [kodu programu Visual Studio](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="3e4a8-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="3e4a8-112">Instalowanie [rozszerzenia C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) do kodu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4a8-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="3e4a8-113">Zainstaluj [rozszerzenie usługi Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) do kodu programu Visual Studio i skonfiguruj je przed kontynuowaniem</span><span class="sxs-lookup"><span data-stu-id="3e4a8-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="3e4a8-114">Tworzenie projektu MVC ASP.Net Core</span><span class="sxs-lookup"><span data-stu-id="3e4a8-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="3e4a8-115">Za pomocą terminala przejdź do folderu, w którego ma być tworzony projekt, i użyj następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="3e4a8-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="3e4a8-116">Struktura folderów będzie podobna do następującej:</span><span class="sxs-lookup"><span data-stu-id="3e4a8-116">You'll have a folder structure similar to the following:</span></span>

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

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="3e4a8-117">Otwórz go za pomocą programu Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e4a8-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="3e4a8-118">Po utworzeniu projektu można go otworzyć za pomocą programu Visual Studio Code przy użyciu jednej z poniższych opcji:</span><span class="sxs-lookup"><span data-stu-id="3e4a8-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="3e4a8-119">Za pośrednictwem wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="3e4a8-119">Through the command line</span></span>

<span data-ttu-id="3e4a8-120">Użyj następującego polecenia w folderze utworzonym w projekcie:</span><span class="sxs-lookup"><span data-stu-id="3e4a8-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="3e4a8-121">Jeśli poniższe polecenie nie działa, sprawdź, czy instalacja jest poprawnie skonfigurowana, odwołując się do [tego łącza](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="3e4a8-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="3e4a8-122">Za pośrednictwem interfejsu kodu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4a8-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="3e4a8-123">Otwórz kod programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4a8-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="3e4a8-124">W menu wybierz pozycję`File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="3e4a8-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="3e4a8-125">Wybierz katalog główny folderu utworzonego projektu MVC</span><span class="sxs-lookup"><span data-stu-id="3e4a8-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="3e4a8-126">Po otwarciu folderu projektu zostanie wyświetlony komunikat informujący, że brakuje wymaganych zasobów do tworzenia i debugowania.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="3e4a8-127">Zaakceptuj pomoc, aby je dodać.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-127">Accept the help to add them.</span></span>

![Interfejs kodu programu Visual Studio z załadowanym projektem](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="3e4a8-129">Folder `.vscode` zostanie utworzony w ramach struktury projektu.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="3e4a8-130">Będzie zawierać następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="3e4a8-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="3e4a8-131">Są to pliki narzędziowe ułatwiające tworzenie i debugowanie aplikacji sieci Web .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3e4a8-132">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="3e4a8-132">Run the app</span></span>

<span data-ttu-id="3e4a8-133">Zanim wdrożymy aplikację na platformie Azure, upewnij się, że działa poprawnie na komputerze lokalnym.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="3e4a8-134">Naciśnij klawisz F5, aby uruchomić projekt</span><span class="sxs-lookup"><span data-stu-id="3e4a8-134">Press F5 to run the project</span></span>

<span data-ttu-id="3e4a8-135">Aplikacja internetowa zacznie działać na nowej karcie domyślnej przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="3e4a8-136">Po jego uruchomieniu można zauważyć ostrzeżenie o ochronie prywatności.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="3e4a8-137">Dzieje się tak, ponieważ aplikacja rozpocznie się przy użyciu protokołu HTTP i HTTPS i domyślnie przechodzi do punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Ostrzeżenie o prywatności podczas debugowania aplikacji lokalnie](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="3e4a8-139">Aby zachować sesję debugowania, kliknij, a następnie kliknij `Advanced` przycisk `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="3e4a8-140">Generowanie pakietu wdrożeniowego lokalnie</span><span class="sxs-lookup"><span data-stu-id="3e4a8-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="3e4a8-141">Otwórz terminal Kodu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4a8-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="3e4a8-142">Użyj następującego polecenia, `Release` aby wygenerować pakiet do podfolderu o nazwie: `publish`</span><span class="sxs-lookup"><span data-stu-id="3e4a8-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="3e4a8-143">Nowy `publish` folder zostanie utworzony w ramach struktury projektu</span><span class="sxs-lookup"><span data-stu-id="3e4a8-143">A new `publish` folder will be created under the project structure</span></span>

![Publikowanie struktury folderów](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="3e4a8-145">Publikowanie w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="3e4a8-145">Publish to Azure App Service</span></span>

<span data-ttu-id="3e4a8-146">Korzystając z rozszerzenia usługi Azure App Service dla programu Visual Studio Code, wykonaj poniższe czynności, aby opublikować witrynę sieci Web bezpośrednio w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="3e4a8-147">Jeśli tworzysz nową aplikację sieci Web</span><span class="sxs-lookup"><span data-stu-id="3e4a8-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="3e4a8-148">Kliknij prawym `publish` przyciskiem myszy folder i wybierz polecenie`Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="3e4a8-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="3e4a8-149">Wybierz subskrypcję, którą chcesz utworzyć aplikację sieci Web</span><span class="sxs-lookup"><span data-stu-id="3e4a8-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="3e4a8-150">Wybierz pozycję `Create New Web App`</span><span class="sxs-lookup"><span data-stu-id="3e4a8-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="3e4a8-151">Wprowadzanie nazwy aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="3e4a8-151">Enter a name for the Web App</span></span>

<span data-ttu-id="3e4a8-152">Rozszerzenie utworzy nową aplikację sieci Web i automatycznie rozpocznie wdrażanie pakietu do niego.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="3e4a8-153">Po zakończeniu wdrażania kliknij, `Browse Website` aby sprawdzić poprawność wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Komunikat o pomyślnym wdrożeniu](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="3e4a8-155">Po kliknięciu przycisku `Browse Website`przejdź do niego za pomocą domyślnej przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="3e4a8-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Nowa aplikacja Sieci Web została pomyślnie wdrożona](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="3e4a8-157">Jeśli wdrażasz w istniejącej aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="3e4a8-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="3e4a8-158">Kliknij prawym `publish` przyciskiem myszy folder i wybierz polecenie`Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="3e4a8-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="3e4a8-159">Wybieranie subskrypcji istniejącej aplikacji Sieci Web</span><span class="sxs-lookup"><span data-stu-id="3e4a8-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="3e4a8-160">Wybieranie aplikacji sieci Web z listy</span><span class="sxs-lookup"><span data-stu-id="3e4a8-160">Select the Web App from the list</span></span>
- <span data-ttu-id="3e4a8-161">Visual Studio Code zapyta, czy chcesz zastąpić istniejącą zawartość.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="3e4a8-162">Kliknij, `Deploy` aby potwierdzić</span><span class="sxs-lookup"><span data-stu-id="3e4a8-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="3e4a8-163">Rozszerzenie wdroży zaktualizowaną zawartość w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="3e4a8-164">Po zakończeniu kliknij, `Browse Website` aby sprawdzić poprawność wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="3e4a8-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Istniejące aplikacje sieci Web zostały pomyślnie wdrożone](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="3e4a8-166">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="3e4a8-166">Next steps</span></span>

- [<span data-ttu-id="3e4a8-167">Tworzenie pierwszego potoku devops platformy Azure</span><span class="sxs-lookup"><span data-stu-id="3e4a8-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="3e4a8-168">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3e4a8-168">Additional resources</span></span>

- [<span data-ttu-id="3e4a8-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="3e4a8-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="3e4a8-170">Grupy zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="3e4a8-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)

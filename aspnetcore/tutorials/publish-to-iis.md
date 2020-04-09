---
title: Publikowanie aplikacji ASP.NET Core w ys
author: rick-anderson
description: Dowiedz się, jak hostować aplikację core ASP.NET na serwerze usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 47f78ba78741a8e0175ce801c0c0e51f091273a8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511395"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="00da7-103">Publikowanie aplikacji ASP.NET Core w ys</span><span class="sxs-lookup"><span data-stu-id="00da7-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="00da7-104">W tym samouczku pokazano, jak hostować aplikację core ASP.NET na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="00da7-105">Ten samouczek obejmuje następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="00da7-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="00da7-106">Zainstaluj pakiet hostingowy .NET Core w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="00da7-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="00da7-107">Utwórz witrynę usług IIS w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="00da7-108">Wdrażanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="00da7-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="00da7-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="00da7-109">Prerequisites</span></span>

* <span data-ttu-id="00da7-110">[.NET Core SDK](/dotnet/core/sdk) zainstalowany na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="00da7-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="00da7-111">System Windows Server skonfigurowany z rolą serwera **sieci Web (IIS).**</span><span class="sxs-lookup"><span data-stu-id="00da7-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="00da7-112">Jeśli serwer nie jest skonfigurowany do hosta witryn sieci Web z usługami IIS, postępuj zgodnie ze wskazówkami w sekcji *konfiguracji usług IIS* w <xref:host-and-deploy/iis/index#iis-configuration> tym artykule, a następnie wróć do tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="00da7-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="00da7-113">**Konfiguracja usług IIS i zabezpieczenia witryny sieci Web obejmują pojęcia, które nie są objęte tym samouczku.**</span><span class="sxs-lookup"><span data-stu-id="00da7-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="00da7-114">Zapoznaj się ze wskazówkami usług IIS w [dokumentacji usług Microsoft IIS](https://www.iis.net/) i [artykułem ASP.NET Core na temat hostingu z usługami IIS](xref:host-and-deploy/iis/index) przed hostowanie aplikacji produkcyjnych na usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="00da7-115">Ważne scenariusze dla hostingu IIS nieobjęte tym samouczkiem obejmują:</span><span class="sxs-lookup"><span data-stu-id="00da7-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="00da7-116">Tworzenie gałęzi rejestru dla ASP.NET Podstawowej Ochrony Danych</span><span class="sxs-lookup"><span data-stu-id="00da7-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="00da7-117">Konfiguracja listy kontroli dostępu puli aplikacji (ACL)</span><span class="sxs-lookup"><span data-stu-id="00da7-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="00da7-118">Aby skupić się na pojęciach wdrażania usług IIS, w tym samouczku wdraża się aplikacja bez zabezpieczeń HTTPS skonfigurowanych w serwisach IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="00da7-119">Aby uzyskać więcej informacji na temat hostowania aplikacji włączonej dla protokołu HTTPS, zobacz tematy zabezpieczeń w sekcji [Dodatkowe zasoby](#additional-resources) tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="00da7-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="00da7-120">Dalsze wskazówki dotyczące hostingu aplikacji ASP.NET <xref:host-and-deploy/iis/index> Core znajdują się w artykule.</span><span class="sxs-lookup"><span data-stu-id="00da7-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="00da7-121">Zainstaluj pakiet hostingowy .NET Core</span><span class="sxs-lookup"><span data-stu-id="00da7-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="00da7-122">Zainstaluj *pakiet hostingu .NET Core* na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="00da7-123">Pakiet instaluje środowisko uruchomieniowe .NET Core, bibliotekę .NET Core library i [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="00da7-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="00da7-124">Moduł umożliwia ASP.NET aplikacje Core działać za usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="00da7-125">Pobierz instalatora za pomocą następującego linku:</span><span class="sxs-lookup"><span data-stu-id="00da7-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="00da7-126">Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)</span><span class="sxs-lookup"><span data-stu-id="00da7-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="00da7-127">Uruchom instalator na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="00da7-128">Uruchom ponownie serwer lub wykonaj **net stop był /y,** a następnie **net start w3svc** w powiększe polecenia.</span><span class="sxs-lookup"><span data-stu-id="00da7-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="00da7-129">Tworzenie witryny usługi IIS</span><span class="sxs-lookup"><span data-stu-id="00da7-129">Create the IIS site</span></span>

1. <span data-ttu-id="00da7-130">Na serwerze usług IIS utwórz folder zawierający opublikowane foldery i pliki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00da7-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="00da7-131">W następnym kroku ścieżka folderu jest dostarczana do iIS jako ścieżka fizyczna do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00da7-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="00da7-132">W Menedżerze usług IIS otwórz węzeł serwera w panelu **Połączenia.**</span><span class="sxs-lookup"><span data-stu-id="00da7-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="00da7-133">Kliknij prawym przyciskiem myszy folder **Witryny.**</span><span class="sxs-lookup"><span data-stu-id="00da7-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="00da7-134">Z menu kontekstowego **wybierz polecenie Dodaj witrynę sieci Web.**</span><span class="sxs-lookup"><span data-stu-id="00da7-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="00da7-135">Podaj **nazwę witryny** i ustaw **ścieżkę fizyczną** do utworzonego folderu wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00da7-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="00da7-136">Podaj konfigurację **powiązania** i utwórz witrynę sieci Web, wybierając **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="00da7-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="00da7-137">Tworzenie aplikacji ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="00da7-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="00da7-138">Postępuj <xref:getting-started> zgodnie z samouczkiem, aby utworzyć aplikację Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="00da7-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="00da7-139">Publikowanie i wdrażanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="00da7-139">Publish and deploy the app</span></span>

<span data-ttu-id="00da7-140">*Publikowanie aplikacji* oznacza, aby utworzyć skompilowaną aplikację, która może być obsługiwana przez serwer.</span><span class="sxs-lookup"><span data-stu-id="00da7-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="00da7-141">*Wdrażanie aplikacji* oznacza przeniesienie opublikowanej aplikacji do systemu hostingowego.</span><span class="sxs-lookup"><span data-stu-id="00da7-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="00da7-142">Krok publikowania jest obsługiwany przez [.NET Core SDK](/dotnet/core/sdk), podczas gdy krok wdrażania może być obsługiwany przez różne podejścia.</span><span class="sxs-lookup"><span data-stu-id="00da7-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="00da7-143">W tym samouczku przyjęto podejście do wdrażania *folderów,* gdzie:</span><span class="sxs-lookup"><span data-stu-id="00da7-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="00da7-144">Aplikacja zostanie opublikowana w folderze.</span><span class="sxs-lookup"><span data-stu-id="00da7-144">The app is published to a folder.</span></span>
* <span data-ttu-id="00da7-145">Zawartość folderu zostanie przeniesiona do folderu witryny usług IIS **(ścieżka fizyczna** do witryny w Menedżerze usług IIS).</span><span class="sxs-lookup"><span data-stu-id="00da7-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="00da7-146">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00da7-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="00da7-147">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="00da7-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="00da7-148">W oknie **dialogowym Wybieranie celu publikacji** wybierz opcję **Publikowanie folderów.**</span><span class="sxs-lookup"><span data-stu-id="00da7-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="00da7-149">Ustaw ścieżkę **Folder lub File Share.**</span><span class="sxs-lookup"><span data-stu-id="00da7-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="00da7-150">Jeśli utworzono folder dla witryny IIS, który jest dostępny na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału.</span><span class="sxs-lookup"><span data-stu-id="00da7-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="00da7-151">Bieżący użytkownik musi mieć dostęp do zapisu, aby opublikować w udziale.</span><span class="sxs-lookup"><span data-stu-id="00da7-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="00da7-152">Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze usług IIS, opublikuj w folderze na nośniku usuwalnym i fizycznie przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** witryny w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="00da7-153">Przenieś zawartość folderu *bin/release/{TARGET FRAMEWORK}/publish* do folderu lokacji usług IIS na serwerze, który jest **ścieżką fizyczną witryny** w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="00da7-154">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="00da7-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="00da7-155">W powłoce poleceń opublikuj aplikację w konfiguracji wydania za pomocą polecenia [dotnet publish:](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="00da7-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="00da7-156">Przenieś zawartość folderu *bin/release/{TARGET FRAMEWORK}/publish* do folderu lokacji usług IIS na serwerze, który jest **ścieżką fizyczną witryny** w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="00da7-157">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="00da7-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="00da7-158">Kliknij prawym przyciskiem myszy projekt w **rozwiązaniu** i wybierz pozycję **Publikuj** > **w folderze**.</span><span class="sxs-lookup"><span data-stu-id="00da7-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="00da7-159">Ustaw **ścieżkę Wybierania folderu.**</span><span class="sxs-lookup"><span data-stu-id="00da7-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="00da7-160">Jeśli utworzono folder dla witryny IIS, który jest dostępny na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału.</span><span class="sxs-lookup"><span data-stu-id="00da7-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="00da7-161">Bieżący użytkownik musi mieć dostęp do zapisu, aby opublikować w udziale.</span><span class="sxs-lookup"><span data-stu-id="00da7-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="00da7-162">Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze usług IIS, opublikuj w folderze na nośniku usuwalnym i fizycznie przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** witryny w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="00da7-163">Przenieś zawartość folderu *bin/release/{TARGET FRAMEWORK}/publish* do folderu lokacji usług IIS na serwerze, który jest **ścieżką fizyczną witryny** w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="00da7-164">Przeglądanie witryny sieci Web</span><span class="sxs-lookup"><span data-stu-id="00da7-164">Browse the website</span></span>

<span data-ttu-id="00da7-165">Aplikacja jest dostępna w przeglądarce po otrzymaniu pierwszego żądania.</span><span class="sxs-lookup"><span data-stu-id="00da7-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="00da7-166">Złożyć żądanie do aplikacji w powiązaniu punktu końcowego, które zostało ustanowione w Menedżerze usług IIS dla witryny.</span><span class="sxs-lookup"><span data-stu-id="00da7-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="00da7-167">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="00da7-167">Next steps</span></span>

<span data-ttu-id="00da7-168">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="00da7-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="00da7-169">Zainstaluj pakiet hostingowy .NET Core w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="00da7-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="00da7-170">Utwórz witrynę usług IIS w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="00da7-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="00da7-171">Wdrażanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="00da7-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="00da7-172">Aby dowiedzieć się więcej o hostingu aplikacji ASP.NET Core w serwisach IIS, zobacz artykuł Omówienie usług IIS:</span><span class="sxs-lookup"><span data-stu-id="00da7-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="00da7-173">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="00da7-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="00da7-174">Artykuły w zestawie dokumentacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00da7-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="00da7-175">Artykuły dotyczące wdrażania aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00da7-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="00da7-176">Publikowanie aplikacji sieci Web w folderze przy użyciu programu Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="00da7-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="00da7-177">Artykuły dotyczące konfiguracji protokołu HTTPS usług IIS</span><span class="sxs-lookup"><span data-stu-id="00da7-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="00da7-178">Konfigurowanie ssl w menedżerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="00da7-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="00da7-179">Jak skonfigurować ssl w serwisach IIS</span><span class="sxs-lookup"><span data-stu-id="00da7-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="00da7-180">Artykuły na usługach IIS i windows server</span><span class="sxs-lookup"><span data-stu-id="00da7-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="00da7-181">Oficjalna witryna microsoft iis</span><span class="sxs-lookup"><span data-stu-id="00da7-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="00da7-182">Biblioteka zawartości technicznej systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="00da7-182">Windows Server technical content library</span></span>](/windows-server/windows-server)

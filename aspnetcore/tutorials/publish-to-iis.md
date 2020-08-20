---
title: Publikowanie aplikacji ASP.NET Core w usługach IIS
author: rick-anderson
description: Dowiedz się, jak hostować aplikację ASP.NET Core na serwerze usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 34707def9728211b9c2aa36d255f2467d1e3d661
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627796"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="fd755-103">Publikowanie aplikacji ASP.NET Core w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="fd755-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="fd755-104">W tym samouczku pokazano, jak hostować aplikację ASP.NET Core na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="fd755-105">Ten samouczek obejmuje następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="fd755-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fd755-106">Zainstaluj pakiet hostingu platformy .NET Core w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="fd755-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="fd755-107">Utwórz witrynę usług IIS w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="fd755-108">Wdróż aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd755-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fd755-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="fd755-109">Prerequisites</span></span>

* <span data-ttu-id="fd755-110">[Zestaw .NET Core SDK](/dotnet/core/sdk) zainstalowane na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="fd755-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="fd755-111">System Windows Server skonfigurowany z rolą serwera **serwer sieci Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="fd755-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="fd755-112">Jeśli serwer nie jest skonfigurowany do hostowania witryn sieci Web za pomocą usług IIS, postępuj zgodnie ze wskazówkami w sekcji *Konfiguracja usług IIS* w <xref:host-and-deploy/iis/index#iis-configuration> artykule, a następnie wróć do tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="fd755-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="fd755-113">**Konfiguracja usług IIS i zabezpieczenia witryny sieci Web obejmują pojęcia, które nie są objęte tym samouczkiem.**</span><span class="sxs-lookup"><span data-stu-id="fd755-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="fd755-114">Zapoznaj się ze wskazówkami dotyczącymi usług IIS w [dokumentacji usług Microsoft IIS](https://www.iis.net/) i [artykułem ASP.NET Core na hostowanie usług IIS](xref:host-and-deploy/iis/index) przed hostowania aplikacji produkcyjnych w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="fd755-115">Ważne scenariusze dotyczące hostingu usług IIS, które nie są objęte tym samouczkiem, obejmują:</span><span class="sxs-lookup"><span data-stu-id="fd755-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="fd755-116">Tworzenie gałęzi rejestru na potrzeby ochrony danych ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd755-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="fd755-117">Konfiguracja listy Access Control puli aplikacji (ACL)</span><span class="sxs-lookup"><span data-stu-id="fd755-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="fd755-118">Aby skoncentrować się na pojęciach dotyczących wdrażania usług IIS, w tym samouczku wdrożono aplikację bez zabezpieczeń protokołu HTTPS skonfigurowanej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="fd755-119">Aby uzyskać więcej informacji na temat hostowania aplikacji z włączoną obsługą protokołu HTTPS, zobacz tematy dotyczące zabezpieczeń w sekcji [dodatkowe zasoby](#additional-resources) w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="fd755-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="fd755-120">Dalsze wskazówki dotyczące hostingu ASP.NET Core aplikacji znajdują się w <xref:host-and-deploy/iis/index> artykule.</span><span class="sxs-lookup"><span data-stu-id="fd755-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="fd755-121">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="fd755-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="fd755-122">Zainstaluj *pakiet hostingu platformy .NET Core* na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="fd755-123">Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fd755-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="fd755-124">Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="fd755-125">Pobierz instalatora przy użyciu następującego linku:</span><span class="sxs-lookup"><span data-stu-id="fd755-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="fd755-126">Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)</span><span class="sxs-lookup"><span data-stu-id="fd755-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="fd755-127">Uruchom Instalatora na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="fd755-128">Uruchom ponownie serwer, a **następnie wykonaj polecenie** **net start W3SVC** w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="fd755-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="fd755-129">Tworzenie witryny usług IIS</span><span class="sxs-lookup"><span data-stu-id="fd755-129">Create the IIS site</span></span>

1. <span data-ttu-id="fd755-130">Na serwerze usług IIS Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fd755-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="fd755-131">W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fd755-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="fd755-132">W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="fd755-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="fd755-133">Kliknij prawym przyciskiem myszy folder **sitess** .</span><span class="sxs-lookup"><span data-stu-id="fd755-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="fd755-134">Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="fd755-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="fd755-135">Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** do utworzonego folderu wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fd755-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="fd755-136">Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="fd755-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="fd755-137">Tworzenie aplikacji ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fd755-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="fd755-138">Postępuj zgodnie z <xref:getting-started> samouczkiem, aby utworzyć Razor aplikację Pages.</span><span class="sxs-lookup"><span data-stu-id="fd755-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="fd755-139">Publikowanie i wdrażanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="fd755-139">Publish and deploy the app</span></span>

<span data-ttu-id="fd755-140">*Opublikowanie aplikacji* oznacza utworzenie skompilowanej aplikacji, która może być hostowana przez serwer programu.</span><span class="sxs-lookup"><span data-stu-id="fd755-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="fd755-141">*Wdrożenie aplikacji* oznacza przeniesienie opublikowanej aplikacji do systemu hostingu.</span><span class="sxs-lookup"><span data-stu-id="fd755-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="fd755-142">Krok Publikuj jest obsługiwany przez [zestaw .NET Core SDK](/dotnet/core/sdk), podczas gdy krok wdrożenia może być obsługiwany przez różne podejścia.</span><span class="sxs-lookup"><span data-stu-id="fd755-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="fd755-143">Ten samouczek przyjmuje podejście do wdrożenia *folderu* , gdzie:</span><span class="sxs-lookup"><span data-stu-id="fd755-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="fd755-144">Aplikacja zostanie opublikowana w folderze.</span><span class="sxs-lookup"><span data-stu-id="fd755-144">The app is published to a folder.</span></span>
* <span data-ttu-id="fd755-145">Zawartość folderu zostanie przeniesiona do folderu witryny usług IIS ( **Ścieżka fizyczna** do lokacji w Menedżerze usług IIS).</span><span class="sxs-lookup"><span data-stu-id="fd755-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fd755-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd755-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fd755-147">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="fd755-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="fd755-148">W oknie dialogowym **Wybieranie elementu docelowego publikowania** wybierz opcję Publikuj **folder** .</span><span class="sxs-lookup"><span data-stu-id="fd755-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="fd755-149">Ustaw ścieżkę **folderu lub udziału plików** .</span><span class="sxs-lookup"><span data-stu-id="fd755-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="fd755-150">Jeśli utworzono folder dla witryny usług IIS, która jest dostępna na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału.</span><span class="sxs-lookup"><span data-stu-id="fd755-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="fd755-151">Bieżący użytkownik musi mieć dostęp do zapisu w celu opublikowania w udziale.</span><span class="sxs-lookup"><span data-stu-id="fd755-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="fd755-152">Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze IIS, Opublikuj je w folderze na nośniku umożliwiającym usunięcie i fizycznie Przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="fd755-153">Przenieś zawartość folderu *bin/Release/{Target Framework}/Publish* do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="fd755-154">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fd755-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="fd755-155">W powłoce poleceń Opublikuj aplikację w konfiguracji wydania przy użyciu polecenia [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="fd755-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="fd755-156">Przenieś zawartość folderu *bin/Release/{Target Framework}/Publish* do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fd755-157">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="fd755-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="fd755-158">Kliknij prawym przyciskiem myszy projekt w **rozwiązaniu** , a następnie wybierz pozycję **Publikuj**  >  **Publikowanie w folderze**.</span><span class="sxs-lookup"><span data-stu-id="fd755-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="fd755-159">Ustaw ścieżkę do **folderu wybierz** .</span><span class="sxs-lookup"><span data-stu-id="fd755-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="fd755-160">Jeśli utworzono folder dla witryny usług IIS, która jest dostępna na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału.</span><span class="sxs-lookup"><span data-stu-id="fd755-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="fd755-161">Bieżący użytkownik musi mieć dostęp do zapisu w celu opublikowania w udziale.</span><span class="sxs-lookup"><span data-stu-id="fd755-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="fd755-162">Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze IIS, Opublikuj je w folderze na nośniku umożliwiającym usunięcie i fizycznie Przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="fd755-163">Przenieś zawartość folderu *bin/Release/{Target Framework}/Publish* do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="fd755-164">Przeglądanie witryny sieci Web</span><span class="sxs-lookup"><span data-stu-id="fd755-164">Browse the website</span></span>

<span data-ttu-id="fd755-165">Aplikacja jest dostępna w przeglądarce po odebraniu pierwszego żądania.</span><span class="sxs-lookup"><span data-stu-id="fd755-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="fd755-166">Prześlij żądanie do aplikacji przy użyciu powiązania punktu końcowego, który został ustanowiony w Menedżerze usług IIS dla tej lokacji.</span><span class="sxs-lookup"><span data-stu-id="fd755-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fd755-167">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="fd755-167">Next steps</span></span>

<span data-ttu-id="fd755-168">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="fd755-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fd755-169">Zainstaluj pakiet hostingu platformy .NET Core w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="fd755-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="fd755-170">Utwórz witrynę usług IIS w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="fd755-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="fd755-171">Wdróż aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd755-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="fd755-172">Aby dowiedzieć się więcej na temat hostowania aplikacji ASP.NET Core w usługach IIS, zobacz artykuł Omówienie usług IIS:</span><span class="sxs-lookup"><span data-stu-id="fd755-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="fd755-173">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="fd755-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="fd755-174">Artykuły w zestawie dokumentacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd755-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="fd755-175">Artykuły dotyczące wdrażania aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd755-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="fd755-176">Publikowanie aplikacji sieci Web w folderze przy użyciu Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="fd755-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="fd755-177">Artykuły na temat konfiguracji protokołu HTTPS usług IIS</span><span class="sxs-lookup"><span data-stu-id="fd755-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="fd755-178">Konfigurowanie protokołu SSL w Menedżerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="fd755-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="fd755-179">Jak skonfigurować protokół SSL w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="fd755-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="fd755-180">Artykuły dotyczące usług IIS i systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="fd755-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="fd755-181">Oficjalna witryna programu Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="fd755-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="fd755-182">Biblioteka zawartości technicznej systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="fd755-182">Windows Server technical content library</span></span>](/windows-server/windows-server)

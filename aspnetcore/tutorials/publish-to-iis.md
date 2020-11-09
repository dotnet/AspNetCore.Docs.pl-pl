---
title: Publikowanie aplikacji ASP.NET Core w usługach IIS
author: rick-anderson
description: Dowiedz się, jak hostować aplikację ASP.NET Core na serwerze usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/publish-to-iis
ms.openlocfilehash: b3c714ea8e741430df1f70b2df258f1e8f1c7ad5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060511"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="a0778-103">Publikowanie aplikacji ASP.NET Core w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="a0778-104">W tym samouczku pokazano, jak hostować aplikację ASP.NET Core na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="a0778-105">Ten samouczek obejmuje następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="a0778-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a0778-106">Zainstaluj pakiet hostingu platformy .NET Core w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="a0778-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="a0778-107">Utwórz witrynę usług IIS w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="a0778-108">Wdróż aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0778-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a0778-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a0778-109">Prerequisites</span></span>

* <span data-ttu-id="a0778-110">[Zestaw .NET Core SDK](/dotnet/core/sdk) zainstalowane na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="a0778-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="a0778-111">System Windows Server skonfigurowany z rolą serwera **serwer sieci Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="a0778-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="a0778-112">Jeśli serwer nie jest skonfigurowany do hostowania witryn sieci Web za pomocą usług IIS, postępuj zgodnie ze wskazówkami w sekcji *Konfiguracja usług IIS* w <xref:host-and-deploy/iis/index#iis-configuration> artykule, a następnie wróć do tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="a0778-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="a0778-113">**Konfiguracja usług IIS i zabezpieczenia witryny sieci Web obejmują pojęcia, które nie są objęte tym samouczkiem.**</span><span class="sxs-lookup"><span data-stu-id="a0778-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="a0778-114">Zapoznaj się ze wskazówkami dotyczącymi usług IIS w [dokumentacji usług Microsoft IIS](https://www.iis.net/) i [artykułem ASP.NET Core na hostowanie usług IIS](xref:host-and-deploy/iis/index) przed hostowania aplikacji produkcyjnych w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="a0778-115">Ważne scenariusze dotyczące hostingu usług IIS, które nie są objęte tym samouczkiem, obejmują:</span><span class="sxs-lookup"><span data-stu-id="a0778-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="a0778-116">Tworzenie gałęzi rejestru na potrzeby ochrony danych ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0778-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="a0778-117">Konfiguracja listy Access Control puli aplikacji (ACL)</span><span class="sxs-lookup"><span data-stu-id="a0778-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="a0778-118">Aby skoncentrować się na pojęciach dotyczących wdrażania usług IIS, w tym samouczku wdrożono aplikację bez zabezpieczeń protokołu HTTPS skonfigurowanej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="a0778-119">Aby uzyskać więcej informacji na temat hostowania aplikacji z włączoną obsługą protokołu HTTPS, zobacz tematy dotyczące zabezpieczeń w sekcji [dodatkowe zasoby](#additional-resources) w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="a0778-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="a0778-120">Dalsze wskazówki dotyczące hostingu ASP.NET Core aplikacji znajdują się w <xref:host-and-deploy/iis/index> artykule.</span><span class="sxs-lookup"><span data-stu-id="a0778-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="a0778-121">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="a0778-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="a0778-122">Zainstaluj *pakiet hostingu platformy .NET Core* na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="a0778-123">Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a0778-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a0778-124">Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="a0778-125">Pobierz instalatora przy użyciu następującego linku:</span><span class="sxs-lookup"><span data-stu-id="a0778-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="a0778-126">Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)</span><span class="sxs-lookup"><span data-stu-id="a0778-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="a0778-127">Uruchom Instalatora na serwerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="a0778-128">Uruchom ponownie serwer lub wykonaj `net stop was /y` `net start w3svc` polecenie w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="a0778-128">Restart the server or execute `net stop was /y` followed by `net start w3svc` in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="a0778-129">Tworzenie witryny usług IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-129">Create the IIS site</span></span>

1. <span data-ttu-id="a0778-130">Na serwerze usług IIS Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a0778-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="a0778-131">W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a0778-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="a0778-132">Aby uzyskać więcej informacji na temat folderu wdrożenia aplikacji i układu pliku, zobacz <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="a0778-132">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="a0778-133">W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="a0778-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="a0778-134">Kliknij prawym przyciskiem myszy folder **sitess** .</span><span class="sxs-lookup"><span data-stu-id="a0778-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="a0778-135">Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="a0778-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="a0778-136">Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** do utworzonego folderu wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a0778-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="a0778-137">Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK** .</span><span class="sxs-lookup"><span data-stu-id="a0778-137">Provide the **Binding** configuration and create the website by selecting **OK** .</span></span>

   > [!WARNING]
   > <span data-ttu-id="a0778-138">`http://*:80/` `http://+:80` **Nie** należy używać powiązań z symbolami wieloznacznymi (i) najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="a0778-138">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a0778-139">Powiązania z symbolami wieloznacznymi najwyższego poziomu mogą otwierać aplikację pod kątem luk w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="a0778-139">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="a0778-140">Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="a0778-140">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a0778-141">Używaj jawnych nazw hostów zamiast symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="a0778-141">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="a0778-142">Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.mysub.com` ) nie ma tego ryzyka bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , która jest narażona).</span><span class="sxs-lookup"><span data-stu-id="a0778-142">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a0778-143">Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="a0778-143">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="a0778-144">Potwierdź, że tożsamość modelu procesu ma odpowiednie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="a0778-144">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="a0778-145">Jeśli domyślna tożsamość puli aplikacji ( **model procesów**  >  **Identity** ) została zmieniona z `ApplicationPoolIdentity` na inną tożsamość, sprawdź, czy Nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a0778-145">If the default identity of the app pool ( **Process Model** > **Identity** ) is changed from `ApplicationPoolIdentity` to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="a0778-146">Na przykład Pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.</span><span class="sxs-lookup"><span data-stu-id="a0778-146">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="a0778-147">Tworzenie aplikacji ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a0778-147">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="a0778-148">Postępuj zgodnie z <xref:getting-started> samouczkiem, aby utworzyć Razor aplikację Pages.</span><span class="sxs-lookup"><span data-stu-id="a0778-148">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="a0778-149">Publikowanie i wdrażanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="a0778-149">Publish and deploy the app</span></span>

<span data-ttu-id="a0778-150">*Opublikowanie aplikacji* oznacza utworzenie skompilowanej aplikacji, która może być hostowana przez serwer programu.</span><span class="sxs-lookup"><span data-stu-id="a0778-150">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="a0778-151">*Wdrożenie aplikacji* oznacza przeniesienie opublikowanej aplikacji do systemu hostingu.</span><span class="sxs-lookup"><span data-stu-id="a0778-151">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="a0778-152">Krok Publikuj jest obsługiwany przez [zestaw .NET Core SDK](/dotnet/core/sdk), podczas gdy krok wdrożenia może być obsługiwany przez różne podejścia.</span><span class="sxs-lookup"><span data-stu-id="a0778-152">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="a0778-153">Ten samouczek przyjmuje podejście do wdrożenia *folderu* , gdzie:</span><span class="sxs-lookup"><span data-stu-id="a0778-153">This tutorial adopts the *folder* deployment approach, where:</span></span>
 
* <span data-ttu-id="a0778-154">Aplikacja zostanie opublikowana w folderze.</span><span class="sxs-lookup"><span data-stu-id="a0778-154">The app is published to a folder.</span></span>
* <span data-ttu-id="a0778-155">Zawartość folderu zostanie przeniesiona do folderu witryny usług IIS ( **Ścieżka fizyczna** do lokacji w Menedżerze usług IIS).</span><span class="sxs-lookup"><span data-stu-id="a0778-155">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0778-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0778-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a0778-157">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj** .</span><span class="sxs-lookup"><span data-stu-id="a0778-157">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>
1. <span data-ttu-id="a0778-158">W oknie dialogowym **Wybieranie elementu docelowego publikowania** wybierz opcję Publikuj **folder** .</span><span class="sxs-lookup"><span data-stu-id="a0778-158">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="a0778-159">Ustaw ścieżkę **folderu lub udziału plików** .</span><span class="sxs-lookup"><span data-stu-id="a0778-159">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="a0778-160">Jeśli utworzono folder dla witryny usług IIS, która jest dostępna na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału.</span><span class="sxs-lookup"><span data-stu-id="a0778-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="a0778-161">Bieżący użytkownik musi mieć dostęp do zapisu w celu opublikowania w udziale.</span><span class="sxs-lookup"><span data-stu-id="a0778-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="a0778-162">Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze IIS, Opublikuj je w folderze na nośniku wymiennym i fizycznie Przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="a0778-163">Przenieś zawartość `bin/Release/{TARGET FRAMEWORK}/publish` folderu do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** tej witryny w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-163">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a0778-164">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="a0778-164">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="a0778-165">W powłoce poleceń Opublikuj aplikację w konfiguracji wydania przy użyciu polecenia [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="a0778-165">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="a0778-166">Przenieś zawartość `bin/Release/{TARGET FRAMEWORK}/publish` folderu do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** tej witryny w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-166">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0778-167">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a0778-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a0778-168">Kliknij prawym przyciskiem myszy projekt w **rozwiązaniu** , a następnie wybierz pozycję **Publikuj**  >  **Publikowanie w folderze** .</span><span class="sxs-lookup"><span data-stu-id="a0778-168">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder** .</span></span>
1. <span data-ttu-id="a0778-169">Ustaw ścieżkę do **folderu wybierz** .</span><span class="sxs-lookup"><span data-stu-id="a0778-169">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="a0778-170">Jeśli utworzono folder dla witryny usług IIS, która jest dostępna na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału.</span><span class="sxs-lookup"><span data-stu-id="a0778-170">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="a0778-171">Bieżący użytkownik musi mieć dostęp do zapisu w celu opublikowania w udziale.</span><span class="sxs-lookup"><span data-stu-id="a0778-171">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="a0778-172">Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze IIS, Opublikuj je w folderze na nośniku umożliwiającym usunięcie i fizycznie Przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-172">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="a0778-173">Przenieś zawartość `bin/Release/{TARGET FRAMEWORK}/publish` folderu do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** tej witryny w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-173">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="a0778-174">Przeglądanie witryny sieci Web</span><span class="sxs-lookup"><span data-stu-id="a0778-174">Browse the website</span></span>

<span data-ttu-id="a0778-175">Aplikacja jest dostępna w przeglądarce po odebraniu pierwszego żądania.</span><span class="sxs-lookup"><span data-stu-id="a0778-175">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="a0778-176">Prześlij żądanie do aplikacji przy użyciu powiązania punktu końcowego, który został ustanowiony w Menedżerze usług IIS dla tej lokacji.</span><span class="sxs-lookup"><span data-stu-id="a0778-176">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a0778-177">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="a0778-177">Next steps</span></span>

<span data-ttu-id="a0778-178">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="a0778-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a0778-179">Zainstaluj pakiet hostingu platformy .NET Core w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="a0778-179">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="a0778-180">Utwórz witrynę usług IIS w Menedżerze usług IIS.</span><span class="sxs-lookup"><span data-stu-id="a0778-180">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="a0778-181">Wdróż aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0778-181">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="a0778-182">Aby dowiedzieć się więcej na temat hostowania aplikacji ASP.NET Core w usługach IIS, zobacz artykuł Omówienie usług IIS:</span><span class="sxs-lookup"><span data-stu-id="a0778-182">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="a0778-183">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a0778-183">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="a0778-184">Artykuły w zestawie dokumentacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0778-184">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="a0778-185">Artykuły dotyczące wdrażania aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0778-185">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="a0778-186">Publikowanie aplikacji sieci Web w folderze przy użyciu Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a0778-186">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="a0778-187">Artykuły na temat konfiguracji protokołu HTTPS usług IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-187">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="a0778-188">Konfigurowanie protokołu SSL w Menedżerze usług IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-188">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="a0778-189">Jak skonfigurować protokół SSL w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-189">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="a0778-190">Artykuły dotyczące usług IIS i systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="a0778-190">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="a0778-191">Oficjalna witryna programu Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-191">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="a0778-192">Biblioteka zawartości technicznej systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="a0778-192">Windows Server technical content library</span></span>](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="a0778-193">Zasoby wdrażania dla administratorów usług IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-193">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="a0778-194">Dokumentacja usług IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-194">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="a0778-195">Wprowadzenie z menedżerem usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="a0778-195">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="a0778-196">Wdrażanie aplikacji .NET Core</span><span class="sxs-lookup"><span data-stu-id="a0778-196">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>


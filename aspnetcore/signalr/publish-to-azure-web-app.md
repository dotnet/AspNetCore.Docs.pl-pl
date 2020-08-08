---
title: Publikowanie aplikacji ASP.NET Core SignalR w Azure App Service
author: bradygaster
description: Dowiedz się, jak opublikować SignalR aplikację ASP.NET Core w Azure App Service.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 3163a2743c544d0a07d92eb7547e77c3b200ac83
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022540"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="cf93b-103">Publikowanie aplikacji ASP.NET Core SignalR w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cf93b-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="cf93b-104">Autor [Brady gastera](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="cf93b-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="cf93b-105">[Azure App Service](/azure/app-service/app-service-web-overview) to usługa platformy [obliczeniowej w chmurze firmy Microsoft](https://azure.microsoft.com/) do hostowania aplikacji sieci web, w tym ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cf93b-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="cf93b-106">Ten artykuł odnosi się do publikowania SignalR aplikacji ASP.NET Core z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cf93b-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="cf93b-107">Aby uzyskać więcej informacji, zobacz [ SignalR usługa Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="cf93b-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="cf93b-108">Publikowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="cf93b-108">Publish the app</span></span>

<span data-ttu-id="cf93b-109">W tym artykule opisano Publikowanie przy użyciu narzędzi w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cf93b-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="cf93b-110">Visual Studio Code użytkownicy mogą używać poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure) do publikowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="cf93b-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="cf93b-111">Aby uzyskać więcej informacji, zobacz temat [publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu narzędzi wiersza polecenia](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="cf93b-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="cf93b-112">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="cf93b-113">Upewnij się, że w oknie dialogowym **Wybieranie elementu docelowego publikowania** są wybrane **App Service** i **Utwórz nowe** .</span><span class="sxs-lookup"><span data-stu-id="cf93b-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="cf93b-114">Wybierz pozycję **Utwórz profil** na liście rozwijanej przycisk **Publikuj** .</span><span class="sxs-lookup"><span data-stu-id="cf93b-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="cf93b-115">Wprowadź informacje opisane w poniższej tabeli w oknie dialogowym **tworzenie App Service** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="cf93b-116">Element</span><span class="sxs-lookup"><span data-stu-id="cf93b-116">Item</span></span>               | <span data-ttu-id="cf93b-117">Opis</span><span class="sxs-lookup"><span data-stu-id="cf93b-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="cf93b-118">**Nazwa**</span><span class="sxs-lookup"><span data-stu-id="cf93b-118">**Name**</span></span>           | <span data-ttu-id="cf93b-119">Unikatowa nazwa aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cf93b-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="cf93b-120">**Subskrypcja**</span><span class="sxs-lookup"><span data-stu-id="cf93b-120">**Subscription**</span></span>   | <span data-ttu-id="cf93b-121">Subskrypcja platformy Azure, której używa aplikacja.</span><span class="sxs-lookup"><span data-stu-id="cf93b-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="cf93b-122">**Grupa zasobów**</span><span class="sxs-lookup"><span data-stu-id="cf93b-122">**Resource Group**</span></span> | <span data-ttu-id="cf93b-123">Grupa powiązanych zasobów, do której należy aplikacja.</span><span class="sxs-lookup"><span data-stu-id="cf93b-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="cf93b-124">**Plan hostingu**</span><span class="sxs-lookup"><span data-stu-id="cf93b-124">**Hosting Plan**</span></span>   | <span data-ttu-id="cf93b-125">Plan cenowy dla aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="cf93b-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="cf93b-126">Wybierz pozycję \*\* SignalR usługa platformy Azure\*\* na **Dependencies**  >  liście rozwijanej**Dodaj** zależności:</span><span class="sxs-lookup"><span data-stu-id="cf93b-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Obszar zależności pokazujący wybór platformy Azure::: No-Loc (sygnalizujący)::: Service na liście rozwijanej dodawania](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="cf93b-128">W oknie **dialogowym SignalR usługi platformy Azure** wybierz pozycję **Utwórz nowe SignalR wystąpienie usługi platformy Azure**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="cf93b-129">Podaj **nazwę**, **grupę zasobów**i **lokalizację**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="cf93b-130">Wróć do okna **dialogowego SignalR usługi platformy Azure** i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="cf93b-131">Program Visual Studio wykonuje następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="cf93b-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="cf93b-132">Tworzy profil publikacji zawierający ustawienia publikowania.</span><span class="sxs-lookup"><span data-stu-id="cf93b-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="cf93b-133">Tworzy *aplikację internetową platformy Azure* z podanymi informacjami.</span><span class="sxs-lookup"><span data-stu-id="cf93b-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="cf93b-134">Publikuje aplikację.</span><span class="sxs-lookup"><span data-stu-id="cf93b-134">Publishes the app.</span></span>
* <span data-ttu-id="cf93b-135">Uruchamia przeglądarkę, która ładuje aplikację sieci Web.</span><span class="sxs-lookup"><span data-stu-id="cf93b-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="cf93b-136">Format adresu URL aplikacji to `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="cf93b-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="cf93b-137">Na przykład aplikacja o nazwie `SignalRChatApp` ma adres URL `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="cf93b-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="cf93b-138">502,2 Jeśli podczas wdrażania aplikacji przeznaczonej dla wersji zapoznawczej programu .NET Core wystąpi błąd *nieprawidłowej bramy* , zapoznaj się z artykułem [Wdróż ASP.NET Core wersja zapoznawcza, aby Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) rozwiązać ten problem.</span><span class="sxs-lookup"><span data-stu-id="cf93b-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="cf93b-139">Skonfiguruj aplikację w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cf93b-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="cf93b-140">*Ta sekcja dotyczy tylko aplikacji, które nie korzystają z SignalR usługi platformy Azure.*</span><span class="sxs-lookup"><span data-stu-id="cf93b-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="cf93b-141">Jeśli aplikacja używa usługi platformy Azure SignalR , App Service nie wymaga konfiguracji koligacji i sieci Web usługi Routing żądań aplikacji opisanych w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="cf93b-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="cf93b-142">Klienci łączą swoje gniazda sieci Web z SignalR usługą platformy Azure, nie bezpośrednio z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="cf93b-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="cf93b-143">W przypadku aplikacji hostowanych bez SignalR usługi Azure enable:</span><span class="sxs-lookup"><span data-stu-id="cf93b-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="cf93b-144">[Koligacja ARR] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(ARR- cookie ) -for-Azure-web-apps.html) do trasy żądań od użytkownika z powrotem do tego samego wystąpienia App Service.</span><span class="sxs-lookup"><span data-stu-id="cf93b-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="cf93b-145">Ustawieniem domyślnym jest **włączone**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-145">The default setting is **On**.</span></span>
* <span data-ttu-id="cf93b-146">[Gniazda sieci Web](xref:fundamentals/websockets) , aby umożliwić transport gniazd sieci Web.</span><span class="sxs-lookup"><span data-stu-id="cf93b-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="cf93b-147">Ustawienie domyślne jest **wyłączone**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="cf93b-148">W Azure Portal przejdź do aplikacji sieci Web w **App Services**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="cf93b-149">Otwórz **Configuration**  >  **Ustawienia ogólne**konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="cf93b-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="cf93b-150">Ustaw dla opcji **gniazda sieci Web** wartość **włączone**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="cf93b-151">Sprawdź, czy **koligacja ARR** jest ustawiona na wartość **włączone**.</span><span class="sxs-lookup"><span data-stu-id="cf93b-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="cf93b-152">Limity planu App Service</span><span class="sxs-lookup"><span data-stu-id="cf93b-152">App Service Plan limits</span></span>

<span data-ttu-id="cf93b-153">Gniazda sieci Web i inne transporty są ograniczone w zależności od wybranego planu App Service.</span><span class="sxs-lookup"><span data-stu-id="cf93b-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="cf93b-154">Aby uzyskać więcej informacji, zapoznaj się z sekcją *limity Cloud Services platformy Azure* i *App Service limity* dotyczące [subskrypcji, limitów, przydziałów i ograniczeń usługi](/azure/azure-subscription-service-limits#app-service-limits) Azure.</span><span class="sxs-lookup"><span data-stu-id="cf93b-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf93b-155">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="cf93b-155">Additional resources</span></span>

* [<span data-ttu-id="cf93b-156">Co to jest SignalR usługa Azure?</span><span class="sxs-lookup"><span data-stu-id="cf93b-156">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="cf93b-157">Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu narzędzi wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="cf93b-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="cf93b-158">Hostowanie i wdrażanie aplikacji ASP.NET Core w wersji zapoznawczej na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="cf93b-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)

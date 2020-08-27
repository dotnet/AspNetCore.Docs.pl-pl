---
title: Publikowanie ASP.NET Core internetowego interfejsu API w usłudze Azure API Management przy użyciu programu Visual Studio
author: codemillmatt
description: Dowiedz się, jak opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management przy użyciu programu Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945761"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="fb64f-103">Publikowanie ASP.NET Core internetowego interfejsu API w usłudze Azure API Management przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb64f-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="fb64f-104">Przez [Soucoup matowy](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="fb64f-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="fb64f-105">Konfiguruj</span><span class="sxs-lookup"><span data-stu-id="fb64f-105">Set up</span></span>

- <span data-ttu-id="fb64f-106">Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz.</span><span class="sxs-lookup"><span data-stu-id="fb64f-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="fb64f-107">[Utwórz nowe wystąpienie usługi Azure API Management](/azure/api-management/get-started-create-service-instance) , jeśli jeszcze tego nie zrobiono.</span><span class="sxs-lookup"><span data-stu-id="fb64f-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="fb64f-108">[Utwórz projekt aplikacji interfejsu API sieci Web](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="fb64f-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="fb64f-109">Konfigurowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="fb64f-109">Configure the app</span></span>

<span data-ttu-id="fb64f-110">Dodanie definicji struktury Swagger do ASP.NET Core internetowego interfejsu API umożliwia usłudze Azure API Management odczytywanie definicji interfejsu API aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fb64f-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="fb64f-111">Wykonaj poniższe czynności.</span><span class="sxs-lookup"><span data-stu-id="fb64f-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="fb64f-112">Dodaj strukturę Swagger</span><span class="sxs-lookup"><span data-stu-id="fb64f-112">Add Swagger</span></span>

1. <span data-ttu-id="fb64f-113">Dodaj pakiet NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) do projektu interfejsu API sieci Web ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fb64f-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Zrzut ekranu, aby skonfigurować okno dialogowe NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="fb64f-115">Dodaj następujący wiersz do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fb64f-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="fb64f-116">Dodaj następujący wiersz do metody `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="fb64f-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="fb64f-117">Zmiana routingu interfejsu API</span><span class="sxs-lookup"><span data-stu-id="fb64f-117">Change the API routing</span></span>

<span data-ttu-id="fb64f-118">Następnie Zmień strukturę adresów URL potrzebną do uzyskania dostępu do `Get` akcji `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="fb64f-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="fb64f-119">Wykonaj poniższe czynności:</span><span class="sxs-lookup"><span data-stu-id="fb64f-119">Complete the following steps:</span></span>

1. <span data-ttu-id="fb64f-120">Otwórz plik *WeatherForecastController.cs* .</span><span class="sxs-lookup"><span data-stu-id="fb64f-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="fb64f-121">Usuń `[Route("[controller]")]` atrybut poziomu klasy.</span><span class="sxs-lookup"><span data-stu-id="fb64f-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="fb64f-122">Definicja klasy będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="fb64f-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="fb64f-123">Dodaj `[Route("/")]` atrybut do `Get()` akcji.</span><span class="sxs-lookup"><span data-stu-id="fb64f-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="fb64f-124">Definicja funkcji będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="fb64f-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="fb64f-125">Publikowanie internetowego interfejsu API do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="fb64f-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="fb64f-126">Wykonaj następujące kroki, aby opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management:</span><span class="sxs-lookup"><span data-stu-id="fb64f-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="fb64f-127">Opublikuj aplikację interfejsu API w celu Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="fb64f-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="fb64f-128">Dodaj pusty interfejs API do wystąpienia usługi Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="fb64f-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="fb64f-129">Opublikuj ASP.NET Core aplikacji interfejsu API sieci Web w wystąpieniu usługi Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="fb64f-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="fb64f-130">Publikowanie aplikacji interfejsu API w celu Azure App Service</span><span class="sxs-lookup"><span data-stu-id="fb64f-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="fb64f-131">Wykonaj następujące kroki, aby opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management:</span><span class="sxs-lookup"><span data-stu-id="fb64f-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="fb64f-132">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Publikuj**:</span><span class="sxs-lookup"><span data-stu-id="fb64f-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="fb64f-134">W oknie dialogowym **Publikowanie** wybierz pozycję **Azure** , a następnie wybierz przycisk **dalej** :</span><span class="sxs-lookup"><span data-stu-id="fb64f-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Okno dialogowe publikowania](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="fb64f-136">Wybierz pozycję **Azure App Service (Windows)** i wybierz przycisk **dalej** :</span><span class="sxs-lookup"><span data-stu-id="fb64f-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Okno dialogowe publikowania: Wybierz App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="fb64f-138">Wybierz pozycję **Utwórz nowy Azure App Service**.</span><span class="sxs-lookup"><span data-stu-id="fb64f-138">Select **Create a new Azure App Service**.</span></span>

    ![Okno dialogowe publikowania: wybierz wystąpienie usługi platformy Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="fb64f-140">Zostanie wyświetlone okno dialogowe **tworzenie App Service** .</span><span class="sxs-lookup"><span data-stu-id="fb64f-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="fb64f-141">Pola **Nazwa aplikacji**, **Grupa zasobów**i zapis **planu App Service** są wypełniane.</span><span class="sxs-lookup"><span data-stu-id="fb64f-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="fb64f-142">Te nazwy można zachować lub zmienić.</span><span class="sxs-lookup"><span data-stu-id="fb64f-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="fb64f-143">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="fb64f-143">Select the **Create** button.</span></span>

    ![Okno dialogowe Tworzenie usługi App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="fb64f-145">Po zakończeniu tworzenia okno dialogowe zostanie automatycznie zamknięte, a okno dialogowe **Publikowanie** ponownie przyniesie fokus.</span><span class="sxs-lookup"><span data-stu-id="fb64f-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="fb64f-146">Utworzone wystąpienie jest automatycznie wybierane.</span><span class="sxs-lookup"><span data-stu-id="fb64f-146">The instance that was created is automatically selected.</span></span>

![Okno dialogowe publikowania: wybierz wystąpienie App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="fb64f-148">Na tym etapie należy dodać interfejs API do usługi Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="fb64f-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="fb64f-149">Pozostaw program Visual Studio otwarty podczas wykonywania poniższych zadań.</span><span class="sxs-lookup"><span data-stu-id="fb64f-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="fb64f-150">Dodawanie interfejsu API do usługi Azure API Management</span><span class="sxs-lookup"><span data-stu-id="fb64f-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="fb64f-151">Otwórz API Management wystąpienie usługi utworzone wcześniej w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="fb64f-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="fb64f-152">Wybierz blok **interfejsy API** :</span><span class="sxs-lookup"><span data-stu-id="fb64f-152">Select the **APIs** blade:</span></span>

    ![Blok interfejsów API wybrany z wystąpienia usługi API Management](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="fb64f-154">W panelu **Dodaj nowy interfejs API** wybierz kafelek **pusty interfejs API** :</span><span class="sxs-lookup"><span data-stu-id="fb64f-154">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

    ![Ekran z wyróżnionym pustym kafelkiem interfejsu API](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="fb64f-156">Wprowadź następujące wartości w wyświetlonym oknie dialogowym **Tworzenie pustego interfejsu API** :</span><span class="sxs-lookup"><span data-stu-id="fb64f-156">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="fb64f-157">**Nazwa wyświetlana**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="fb64f-157">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="fb64f-158">**Nazwa**: *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="fb64f-158">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="fb64f-159">**Sufiks adresu URL interfejsu API**: *V1*</span><span class="sxs-lookup"><span data-stu-id="fb64f-159">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="fb64f-160">Pozostaw puste pole **adres URL usługi sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="fb64f-160">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="fb64f-161">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="fb64f-161">Select the **Create** button.</span></span>

    ![Zrzut ekranu przedstawiający okno dialogowe ukończono tworzenie pustego interfejsu API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="fb64f-163">Zostanie utworzony pusty interfejs API.</span><span class="sxs-lookup"><span data-stu-id="fb64f-163">The blank API is created.</span></span>

![Zrzut ekranu przedstawiający pusty interfejs API w portalu API Management](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="fb64f-165">Publikowanie ASP.NET Core internetowego interfejsu API na platformie Azure API Management</span><span class="sxs-lookup"><span data-stu-id="fb64f-165">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="fb64f-166">Wróć do programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fb64f-166">Switch back to Visual Studio.</span></span> <span data-ttu-id="fb64f-167">Okno dialogowe **publikowania** nadal powinno być otwarte w miejscu, w którym zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="fb64f-167">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="fb64f-168">Wybierz nowo opublikowane Azure App Service tak, aby były zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="fb64f-168">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="fb64f-169">Kliknij przycisk **Next** (Dalej).</span><span class="sxs-lookup"><span data-stu-id="fb64f-169">Select the **Next** button.</span></span>

    ![Zrzut ekranu okna dialogowego publikowanie z wybraną usługą App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="fb64f-171">W oknie dialogowym zostanie wyświetlona usługa Azure API Management utworzona wcześniej.</span><span class="sxs-lookup"><span data-stu-id="fb64f-171">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="fb64f-172">Rozwiń go i folder *interfejsów API* , aby wyświetlić utworzony przez siebie pusty interfejs API.</span><span class="sxs-lookup"><span data-stu-id="fb64f-172">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="fb64f-173">Wybierz nazwę pustego interfejsu API i wybierz przycisk **Zakończ** .</span><span class="sxs-lookup"><span data-stu-id="fb64f-173">Select the blank API's name and select the **Finish** button.</span></span>

    ![Zrzut ekranu przedstawiający nowo utworzony interfejs API platformy Azure API Management wybranego w oknie dialogowym publikowania](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="fb64f-175">Okno dialogowe zostanie zamknięte i zostanie wyświetlony ekran podsumowanie z informacjami o publikacji.</span><span class="sxs-lookup"><span data-stu-id="fb64f-175">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="fb64f-176">Wybierz przycisk **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="fb64f-176">Select the **Publish** button.</span></span>

    ![Zrzut ekranu programu Visual Studio z wyświetlonym profilem publikowania](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="fb64f-178">Internetowy interfejs API będzie publikować zarówno w Azure App Service, jak i na platformie Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="fb64f-178">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="fb64f-179">Zostanie wyświetlone nowe okno przeglądarki z interfejsem API działającym w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="fb64f-179">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="fb64f-180">Możesz zamknąć to okno.</span><span class="sxs-lookup"><span data-stu-id="fb64f-180">You can close that window.</span></span>

1. <span data-ttu-id="fb64f-181">Przełącz się z powrotem do wystąpienia usługi Azure API Management w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="fb64f-181">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="fb64f-182">Odśwież okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="fb64f-182">Refresh the browser window.</span></span>
1. <span data-ttu-id="fb64f-183">Wybierz pusty interfejs API utworzony w poprzednich krokach.</span><span class="sxs-lookup"><span data-stu-id="fb64f-183">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="fb64f-184">Jest ona teraz wypełniana i można ją poznać.</span><span class="sxs-lookup"><span data-stu-id="fb64f-184">It's now populated and you can explore around.</span></span>

    ![Zrzut ekranu przedstawiający wypełniony interfejs API na platformie Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="fb64f-186">Skonfiguruj nazwę opublikowanego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="fb64f-186">Configure the published API name</span></span>

<span data-ttu-id="fb64f-187">Zwróć uwagę, że nazwa interfejsu API różni się od nazwy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fb64f-187">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="fb64f-188">Opublikowany interfejs API ma nazwę *WeatherAPI*; jednak nazywamy ją *WeatherForecasts* podczas jej tworzenia.</span><span class="sxs-lookup"><span data-stu-id="fb64f-188">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="fb64f-189">Wykonaj następujące kroki, aby naprawić nazwę:</span><span class="sxs-lookup"><span data-stu-id="fb64f-189">Complete the following steps to fix the name:</span></span>

![Zrzut ekranu przedstawiający wypełniony interfejs API z różnymi nazwami wyróżnionymi](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="fb64f-191">Usuń następujący wiersz z `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="fb64f-191">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="fb64f-192">Dodaj następujący kod do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fb64f-192">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="fb64f-193">Otwórz nowo utworzony profil publikowania.</span><span class="sxs-lookup"><span data-stu-id="fb64f-193">Open the newly created publish profile.</span></span> <span data-ttu-id="fb64f-194">Można go znaleźć z **Eksplorator rozwiązań** w folderze *Properties/PublishProfiles* .</span><span class="sxs-lookup"><span data-stu-id="fb64f-194">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Zrzut ekranu pokazujący lokalizację pliku profilu publikowania wyróżnione](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="fb64f-196">Zmień `<OpenAPIDocumentName>` wartość elementu z `v1` na `WeatherForecasts` .</span><span class="sxs-lookup"><span data-stu-id="fb64f-196">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![zrzut ekranu przedstawiający zmiany wymagane dla profilu publikacji](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="fb64f-198">Ponownie Opublikuj ASP.NET Core internetowy interfejs API i Otwórz wystąpienie usługi Azure API Management w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="fb64f-198">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="fb64f-199">Odśwież stronę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="fb64f-199">Refresh the page in your browser.</span></span> <span data-ttu-id="fb64f-200">Zobaczysz, że nazwa interfejsu API jest teraz poprawna.</span><span class="sxs-lookup"><span data-stu-id="fb64f-200">You'll see the name of the API is now correct.</span></span>

    ![Zrzut ekranu ukończonego interfejsu API w portalu](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="fb64f-202">Sprawdź, czy interfejs API sieci Web działa</span><span class="sxs-lookup"><span data-stu-id="fb64f-202">Verify the web API is working</span></span>

<span data-ttu-id="fb64f-203">Wdrożony ASP.NET Core Web API można przetestować na platformie Azure API Management z poziomu Azure Portal, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="fb64f-203">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="fb64f-204">Otwórz kartę **Test**.</span><span class="sxs-lookup"><span data-stu-id="fb64f-204">Open the **Test** tab.</span></span>
1. <span data-ttu-id="fb64f-205">Wybierz **/** lub **Pobierz** operację.</span><span class="sxs-lookup"><span data-stu-id="fb64f-205">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="fb64f-206">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="fb64f-206">Select **Send**.</span></span>

    ![Zrzut ekranu portalu przed testem](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="fb64f-208">Pomyślna odpowiedź będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="fb64f-208">A successful response will look like the following:</span></span>

![Zrzut ekranu przedstawiający pomyślną odpowiedź z API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="fb64f-210">Czyszczenie</span><span class="sxs-lookup"><span data-stu-id="fb64f-210">Clean up</span></span>

<span data-ttu-id="fb64f-211">Po zakończeniu testowania aplikacji przejdź do [Azure Portal](https://portal.azure.com/) i Usuń aplikację.</span><span class="sxs-lookup"><span data-stu-id="fb64f-211">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="fb64f-212">Wybierz pozycję **grupy zasobów**, a następnie wybierz utworzoną grupę zasobów.</span><span class="sxs-lookup"><span data-stu-id="fb64f-212">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Witryna Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="fb64f-214">Na stronie **grupy zasobów** wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="fb64f-214">In the **Resource groups** page, select **Delete**.</span></span>

    ![Witryna Azure Portal: Strona grupy zasobów](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="fb64f-216">Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="fb64f-216">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="fb64f-217">Twoja aplikacja i wszystkie inne zasoby utworzone w tym samouczku zostaną usunięte z platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="fb64f-217">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fb64f-218">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="fb64f-218">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="fb64f-219">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="fb64f-219">Additional resources</span></span>

- [<span data-ttu-id="fb64f-220">Azure API Management</span><span class="sxs-lookup"><span data-stu-id="fb64f-220">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="fb64f-221">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="fb64f-221">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)

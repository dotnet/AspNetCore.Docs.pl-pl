---
title: Publikowanie ASP.NET Core internetowego interfejsu API w usłudze Azure API Management przy użyciu programu Visual Studio
author: codemillmatt
description: Dowiedz się, jak opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management przy użyciu programu Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: ddff54bbd146c98cf83a865910401df26e7ac4ec
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217586"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="77cae-103">Publikowanie ASP.NET Core internetowego interfejsu API w usłudze Azure API Management przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77cae-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="77cae-104">Przez [Soucoup matowy](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="77cae-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="77cae-105">Konfiguruj</span><span class="sxs-lookup"><span data-stu-id="77cae-105">Set up</span></span>

- <span data-ttu-id="77cae-106">Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz.</span><span class="sxs-lookup"><span data-stu-id="77cae-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="77cae-107">[Utwórz nowe wystąpienie usługi Azure API Management](/azure/api-management/get-started-create-service-instance) , jeśli jeszcze tego nie zrobiono.</span><span class="sxs-lookup"><span data-stu-id="77cae-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="77cae-108">[Utwórz projekt aplikacji interfejsu API sieci Web](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="77cae-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="77cae-109">Konfigurowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="77cae-109">Configure the app</span></span>

<span data-ttu-id="77cae-110">Dodanie definicji struktury Swagger do ASP.NET Core internetowego interfejsu API umożliwia usłudze Azure API Management odczytywanie definicji interfejsu API aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77cae-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="77cae-111">Wykonaj poniższe czynności.</span><span class="sxs-lookup"><span data-stu-id="77cae-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="77cae-112">Dodaj strukturę Swagger</span><span class="sxs-lookup"><span data-stu-id="77cae-112">Add Swagger</span></span>

1. <span data-ttu-id="77cae-113">Dodaj pakiet NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) do projektu interfejsu API sieci Web ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="77cae-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Zrzut ekranu, aby skonfigurować okno dialogowe NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="77cae-115">Dodaj następujący wiersz do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="77cae-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="77cae-116">Dodaj następujący wiersz do metody `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="77cae-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="77cae-117">Zmiana routingu interfejsu API</span><span class="sxs-lookup"><span data-stu-id="77cae-117">Change the API routing</span></span>

<span data-ttu-id="77cae-118">Następnie Zmień strukturę adresów URL potrzebną do uzyskania dostępu do `Get` akcji `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="77cae-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="77cae-119">Wykonaj poniższe czynności:</span><span class="sxs-lookup"><span data-stu-id="77cae-119">Complete the following steps:</span></span>

1. <span data-ttu-id="77cae-120">Otwórz plik *WeatherForecastController.cs* .</span><span class="sxs-lookup"><span data-stu-id="77cae-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="77cae-121">Usuń `[Route("[controller]")]` atrybut poziomu klasy.</span><span class="sxs-lookup"><span data-stu-id="77cae-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="77cae-122">Definicja klasy będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="77cae-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="77cae-123">Dodaj `[Route("/")]` atrybut do `Get()` akcji.</span><span class="sxs-lookup"><span data-stu-id="77cae-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="77cae-124">Definicja funkcji będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="77cae-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="77cae-125">Publikowanie internetowego interfejsu API do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="77cae-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="77cae-126">Wykonaj następujące kroki, aby opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management:</span><span class="sxs-lookup"><span data-stu-id="77cae-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="77cae-127">Opublikuj aplikację interfejsu API w celu Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="77cae-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="77cae-128">Dodaj pusty interfejs API do wystąpienia usługi Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="77cae-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="77cae-129">Opublikuj ASP.NET Core aplikacji interfejsu API sieci Web w wystąpieniu usługi Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="77cae-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="77cae-130">Publikowanie aplikacji interfejsu API w celu Azure App Service</span><span class="sxs-lookup"><span data-stu-id="77cae-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="77cae-131">Wykonaj następujące kroki, aby opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management:</span><span class="sxs-lookup"><span data-stu-id="77cae-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="77cae-132">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Publikuj**:</span><span class="sxs-lookup"><span data-stu-id="77cae-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="77cae-134">W oknie dialogowym **Publikowanie** wybierz pozycję **Azure** , a następnie wybierz przycisk **dalej** :</span><span class="sxs-lookup"><span data-stu-id="77cae-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Okno dialogowe publikowania](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="77cae-136">Wybierz pozycję **Azure App Service (Windows)** i wybierz przycisk **dalej** :</span><span class="sxs-lookup"><span data-stu-id="77cae-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Okno dialogowe publikowania: Wybierz App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="77cae-138">Wybierz pozycję **Utwórz nowy Azure App Service**.</span><span class="sxs-lookup"><span data-stu-id="77cae-138">Select **Create a new Azure App Service**.</span></span>

    ![Okno dialogowe publikowania: wybierz wystąpienie usługi platformy Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="77cae-140">Zostanie wyświetlone okno dialogowe **tworzenie App Service** .</span><span class="sxs-lookup"><span data-stu-id="77cae-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="77cae-141">Pola **Nazwa aplikacji**, **Grupa zasobów** i zapis **planu App Service** są wypełniane.</span><span class="sxs-lookup"><span data-stu-id="77cae-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="77cae-142">Te nazwy można zachować lub zmienić.</span><span class="sxs-lookup"><span data-stu-id="77cae-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="77cae-143">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="77cae-143">Select the **Create** button.</span></span>

    ![Okno dialogowe Tworzenie usługi App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="77cae-145">Po zakończeniu tworzenia okno dialogowe zostanie automatycznie zamknięte, a okno dialogowe **Publikowanie** ponownie przyniesie fokus.</span><span class="sxs-lookup"><span data-stu-id="77cae-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="77cae-146">Utworzone wystąpienie jest automatycznie wybierane.</span><span class="sxs-lookup"><span data-stu-id="77cae-146">The instance that was created is automatically selected.</span></span>

![Okno dialogowe publikowania: wybierz wystąpienie App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="77cae-148">Na tym etapie należy dodać interfejs API do usługi Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="77cae-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="77cae-149">Pozostaw program Visual Studio otwarty podczas wykonywania poniższych zadań.</span><span class="sxs-lookup"><span data-stu-id="77cae-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="77cae-150">Dodawanie interfejsu API do usługi Azure API Management</span><span class="sxs-lookup"><span data-stu-id="77cae-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="77cae-151">Otwórz API Management wystąpienie usługi utworzone wcześniej w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="77cae-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="77cae-152">Wybierz blok **interfejsy API** :</span><span class="sxs-lookup"><span data-stu-id="77cae-152">Select the **APIs** blade:</span></span>

  ![Blok interfejsów API wybrany z wystąpienia usługi API Management](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="77cae-154">Wybierz trzy kropki obok pozycji **echo API** , a następnie wybierz polecenie **Usuń** z menu podręcznego, aby je usunąć.</span><span class="sxs-lookup"><span data-stu-id="77cae-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![Usuwanie interfejsu API ECHA z wystąpienia usługi API Management](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="77cae-156">W panelu **Dodaj nowy interfejs API** wybierz kafelek **pusty interfejs API** :</span><span class="sxs-lookup"><span data-stu-id="77cae-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![Ekran z wyróżnionym pustym kafelkiem interfejsu API](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="77cae-158">Wprowadź następujące wartości w wyświetlonym oknie dialogowym **Tworzenie pustego interfejsu API** :</span><span class="sxs-lookup"><span data-stu-id="77cae-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="77cae-159">**Nazwa wyświetlana**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="77cae-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="77cae-160">**Nazwa**: *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="77cae-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="77cae-161">**Sufiks adresu URL interfejsu API**: *V1*</span><span class="sxs-lookup"><span data-stu-id="77cae-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="77cae-162">Pozostaw puste pole **adres URL usługi sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="77cae-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="77cae-163">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="77cae-163">Select the **Create** button.</span></span>

    ![Zrzut ekranu przedstawiający okno dialogowe ukończono tworzenie pustego interfejsu API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="77cae-165">Zostanie utworzony pusty interfejs API.</span><span class="sxs-lookup"><span data-stu-id="77cae-165">The blank API is created.</span></span>

![Zrzut ekranu przedstawiający pusty interfejs API w portalu API Management](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="77cae-167">Publikowanie ASP.NET Core internetowego interfejsu API na platformie Azure API Management</span><span class="sxs-lookup"><span data-stu-id="77cae-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="77cae-168">Wróć do programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="77cae-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="77cae-169">Okno dialogowe **publikowania** nadal powinno być otwarte w miejscu, w którym zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="77cae-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="77cae-170">Wybierz nowo opublikowane Azure App Service tak, aby były zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="77cae-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="77cae-171">Kliknij przycisk **Next** (Dalej).</span><span class="sxs-lookup"><span data-stu-id="77cae-171">Select the **Next** button.</span></span>

    ![Zrzut ekranu okna dialogowego publikowanie z wybraną usługą App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="77cae-173">W oknie dialogowym zostanie wyświetlona usługa Azure API Management utworzona wcześniej.</span><span class="sxs-lookup"><span data-stu-id="77cae-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="77cae-174">Rozwiń go i folder *interfejsów API* , aby wyświetlić utworzony przez siebie pusty interfejs API.</span><span class="sxs-lookup"><span data-stu-id="77cae-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="77cae-175">Wybierz nazwę pustego interfejsu API i wybierz przycisk **Zakończ** .</span><span class="sxs-lookup"><span data-stu-id="77cae-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![Zrzut ekranu przedstawiający nowo utworzony interfejs API platformy Azure API Management wybranego w oknie dialogowym publikowania](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="77cae-177">Okno dialogowe zostanie zamknięte i zostanie wyświetlony ekran podsumowanie z informacjami o publikacji.</span><span class="sxs-lookup"><span data-stu-id="77cae-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="77cae-178">Wybierz przycisk **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="77cae-178">Select the **Publish** button.</span></span>

    ![Zrzut ekranu programu Visual Studio z wyświetlonym profilem publikowania](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="77cae-180">Internetowy interfejs API będzie publikować zarówno w Azure App Service, jak i na platformie Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="77cae-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="77cae-181">Zostanie wyświetlone nowe okno przeglądarki z interfejsem API działającym w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="77cae-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="77cae-182">Możesz zamknąć to okno.</span><span class="sxs-lookup"><span data-stu-id="77cae-182">You can close that window.</span></span>

1. <span data-ttu-id="77cae-183">Przełącz się z powrotem do wystąpienia usługi Azure API Management w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="77cae-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="77cae-184">Odśwież okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="77cae-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="77cae-185">Wybierz pusty interfejs API utworzony w poprzednich krokach.</span><span class="sxs-lookup"><span data-stu-id="77cae-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="77cae-186">Jest ona teraz wypełniana i można ją poznać.</span><span class="sxs-lookup"><span data-stu-id="77cae-186">It's now populated and you can explore around.</span></span>

    ![Zrzut ekranu przedstawiający wypełniony interfejs API na platformie Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="77cae-188">Skonfiguruj nazwę opublikowanego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="77cae-188">Configure the published API name</span></span>

<span data-ttu-id="77cae-189">Zwróć uwagę, że nazwa interfejsu API różni się od nazwy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="77cae-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="77cae-190">Opublikowany interfejs API ma nazwę *WeatherAPI*; jednak nazywamy ją *WeatherForecasts* podczas jej tworzenia.</span><span class="sxs-lookup"><span data-stu-id="77cae-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="77cae-191">Wykonaj następujące kroki, aby naprawić nazwę:</span><span class="sxs-lookup"><span data-stu-id="77cae-191">Complete the following steps to fix the name:</span></span>

![Zrzut ekranu przedstawiający wypełniony interfejs API z różnymi nazwami wyróżnionymi](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="77cae-193">Usuń następujący wiersz z `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="77cae-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="77cae-194">Dodaj następujący kod do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="77cae-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "WeatherForecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="77cae-195">Otwórz nowo utworzony profil publikowania.</span><span class="sxs-lookup"><span data-stu-id="77cae-195">Open the newly created publish profile.</span></span> <span data-ttu-id="77cae-196">Można go znaleźć z **Eksplorator rozwiązań** w folderze *Properties/PublishProfiles* .</span><span class="sxs-lookup"><span data-stu-id="77cae-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Zrzut ekranu pokazujący lokalizację pliku profilu publikowania wyróżnione](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="77cae-198">Zmień `<OpenAPIDocumentName>` wartość elementu z `v1` na `WeatherForecasts` .</span><span class="sxs-lookup"><span data-stu-id="77cae-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![zrzut ekranu przedstawiający zmiany wymagane dla profilu publikacji](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="77cae-200">Ponownie Opublikuj ASP.NET Core internetowy interfejs API i Otwórz wystąpienie usługi Azure API Management w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="77cae-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="77cae-201">Odśwież stronę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="77cae-201">Refresh the page in your browser.</span></span> <span data-ttu-id="77cae-202">Zobaczysz, że nazwa interfejsu API jest teraz poprawna.</span><span class="sxs-lookup"><span data-stu-id="77cae-202">You'll see the name of the API is now correct.</span></span>

    ![Zrzut ekranu ukończonego interfejsu API w portalu](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="77cae-204">Sprawdź, czy interfejs API sieci Web działa</span><span class="sxs-lookup"><span data-stu-id="77cae-204">Verify the web API is working</span></span>

<span data-ttu-id="77cae-205">Wdrożony ASP.NET Core Web API można przetestować na platformie Azure API Management z poziomu Azure Portal, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="77cae-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="77cae-206">Otwórz kartę **Test**.</span><span class="sxs-lookup"><span data-stu-id="77cae-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="77cae-207">Wybierz **/** lub **Pobierz** operację.</span><span class="sxs-lookup"><span data-stu-id="77cae-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="77cae-208">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="77cae-208">Select **Send**.</span></span>

    ![Zrzut ekranu portalu przed testem](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="77cae-210">Pomyślna odpowiedź będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="77cae-210">A successful response will look like the following:</span></span>

![Zrzut ekranu przedstawiający pomyślną odpowiedź z API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="77cae-212">Czyszczenie</span><span class="sxs-lookup"><span data-stu-id="77cae-212">Clean up</span></span>

<span data-ttu-id="77cae-213">Po zakończeniu testowania aplikacji przejdź do [Azure Portal](https://portal.azure.com/) i Usuń aplikację.</span><span class="sxs-lookup"><span data-stu-id="77cae-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="77cae-214">Wybierz pozycję **grupy zasobów**, a następnie wybierz utworzoną grupę zasobów.</span><span class="sxs-lookup"><span data-stu-id="77cae-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Witryna Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="77cae-216">Na stronie **grupy zasobów** wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="77cae-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Witryna Azure Portal: Strona grupy zasobów](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="77cae-218">Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="77cae-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="77cae-219">Twoja aplikacja i wszystkie inne zasoby utworzone w tym samouczku zostaną usunięte z platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="77cae-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="77cae-220">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="77cae-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="77cae-221">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="77cae-221">Additional resources</span></span>

- [<span data-ttu-id="77cae-222">Usługa Azure API Management</span><span class="sxs-lookup"><span data-stu-id="77cae-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="77cae-223">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="77cae-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)

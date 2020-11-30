---
title: Publikowanie ASP.NET Core internetowego interfejsu API w usłudze Azure API Management przy użyciu programu Visual Studio
author: codemillmatt
description: Dowiedz się, jak opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management przy użyciu programu Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 395b5981a3018486235c38f032893f985ab71383
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332234"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Publikowanie ASP.NET Core internetowego interfejsu API w usłudze Azure API Management przy użyciu programu Visual Studio

Przez [Soucoup matowy](https://twitter.com/codemillmatt)

## <a name="set-up"></a>Konfiguruj

- Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz.
- [Utwórz nowe wystąpienie usługi Azure API Management](/azure/api-management/get-started-create-service-instance) , jeśli jeszcze tego nie zrobiono.
- [Utwórz projekt aplikacji interfejsu API sieci Web](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>Konfigurowanie aplikacji

Dodanie definicji struktury Swagger do ASP.NET Core internetowego interfejsu API umożliwia usłudze Azure API Management odczytywanie definicji interfejsu API aplikacji. Wykonaj poniższe czynności.

### <a name="add-swagger"></a>Dodaj strukturę Swagger

1. Dodaj pakiet NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) do projektu interfejsu API sieci Web ASP.NET Core:

    ![Zrzut ekranu, aby skonfigurować okno dialogowe NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. Dodaj następujący wiersz do metody `Startup.ConfigureServices`:
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. Dodaj następujący wiersz do metody `Startup.Configure`:

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>Zmiana routingu interfejsu API

Następnie Zmień strukturę adresów URL potrzebną do uzyskania dostępu do `Get` akcji `WeatherForecastController` . Wykonaj poniższe czynności:

1. Otwórz plik *WeatherForecastController.cs* .
1. Usuń `[Route("[controller]")]` atrybut poziomu klasy. Definicja klasy będzie wyglądać następująco:

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. Dodaj `[Route("/")]` atrybut do `Get()` akcji. Definicja funkcji będzie wyglądać następująco:

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Publikowanie internetowego interfejsu API do Azure App Service

Wykonaj następujące kroki, aby opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management:

1. Opublikuj aplikację interfejsu API w celu Azure App Service.
1. Dodaj pusty interfejs API do wystąpienia usługi Azure API Management.
1. Opublikuj ASP.NET Core aplikacji interfejsu API sieci Web w wystąpieniu usługi Azure API Management.

### <a name="publish-the-api-app-to-azure-app-service"></a>Publikowanie aplikacji interfejsu API w celu Azure App Service

Wykonaj następujące kroki, aby opublikować internetowy interfejs API ASP.NET Core na platformie Azure API Management:

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Publikuj**:

    ![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. W oknie dialogowym **Publikowanie** wybierz pozycję **Azure** , a następnie wybierz przycisk **dalej** :

    ![Okno dialogowe publikowania](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. Wybierz pozycję **Azure App Service (Windows)** i wybierz przycisk **dalej** :

    ![Okno dialogowe publikowania: Wybierz App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. Wybierz pozycję **Utwórz nowy Azure App Service**.

    ![Okno dialogowe publikowania: wybierz wystąpienie usługi platformy Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    Zostanie wyświetlone okno dialogowe **tworzenie App Service** . Pola **Nazwa aplikacji**, **Grupa zasobów** i zapis **planu App Service** są wypełniane. Te nazwy można zachować lub zmienić.
1. Wybierz przycisk **Utwórz**.

    ![Okno dialogowe Tworzenie usługi App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

Po zakończeniu tworzenia okno dialogowe zostanie automatycznie zamknięte, a okno dialogowe **Publikowanie** ponownie przyniesie fokus. Utworzone wystąpienie jest automatycznie wybierane.

![Okno dialogowe publikowania: wybierz wystąpienie App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

Na tym etapie należy dodać interfejs API do usługi Azure API Management. Pozostaw program Visual Studio otwarty podczas wykonywania poniższych zadań.

### <a name="add-an-api-to-azure-api-management"></a>Dodawanie interfejsu API do usługi Azure API Management

1. Otwórz API Management wystąpienie usługi utworzone wcześniej w Azure Portal. Wybierz blok **interfejsy API** :

  ![Blok interfejsów API wybrany z wystąpienia usługi API Management](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. Wybierz trzy kropki obok pozycji **echo API** , a następnie wybierz polecenie **Usuń** z menu podręcznego, aby je usunąć.

  ![Usuwanie interfejsu API ECHA z wystąpienia usługi API Management](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. W panelu **Dodaj nowy interfejs API** wybierz kafelek **pusty interfejs API** :

  ![Ekran z wyróżnionym pustym kafelkiem interfejsu API](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. Wprowadź następujące wartości w wyświetlonym oknie dialogowym **Tworzenie pustego interfejsu API** :    

    - **Nazwa wyświetlana**: *WeatherForecasts*
    - **Nazwa**: *weatherforecasts*
    - **Sufiks adresu URL interfejsu API**: *V1*
    - Pozostaw puste pole **adres URL usługi sieci Web** .

1. Wybierz przycisk **Utwórz**.

    ![Zrzut ekranu przedstawiający okno dialogowe ukończono tworzenie pustego interfejsu API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

Zostanie utworzony pusty interfejs API.

![Zrzut ekranu przedstawiający pusty interfejs API w portalu API Management](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>Publikowanie ASP.NET Core internetowego interfejsu API na platformie Azure API Management

1. Wróć do programu Visual Studio. Okno dialogowe **publikowania** nadal powinno być otwarte w miejscu, w którym zostało przerwane.
1. Wybierz nowo opublikowane Azure App Service tak, aby były zaznaczone.
1. Kliknij przycisk **Next** (Dalej).

    ![Zrzut ekranu okna dialogowego publikowanie z wybraną usługą App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. W oknie dialogowym zostanie wyświetlona usługa Azure API Management utworzona wcześniej. Rozwiń go i folder *interfejsów API* , aby wyświetlić utworzony przez siebie pusty interfejs API.
1. Wybierz nazwę pustego interfejsu API i wybierz przycisk **Zakończ** .

    ![Zrzut ekranu przedstawiający nowo utworzony interfejs API platformy Azure API Management wybranego w oknie dialogowym publikowania](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. Okno dialogowe zostanie zamknięte i zostanie wyświetlony ekran podsumowanie z informacjami o publikacji. Wybierz przycisk **Publikuj**.

    ![Zrzut ekranu programu Visual Studio z wyświetlonym profilem publikowania](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    Internetowy interfejs API będzie publikować zarówno w Azure App Service, jak i na platformie Azure API Management. Zostanie wyświetlone nowe okno przeglądarki z interfejsem API działającym w Azure App Service. Możesz zamknąć to okno.

1. Przełącz się z powrotem do wystąpienia usługi Azure API Management w Azure Portal.
1. Odśwież okno przeglądarki.
1. Wybierz pusty interfejs API utworzony w poprzednich krokach. Jest ona teraz wypełniana i można ją poznać.

    ![Zrzut ekranu przedstawiający wypełniony interfejs API na platformie Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>Skonfiguruj nazwę opublikowanego interfejsu API

Zwróć uwagę, że nazwa interfejsu API różni się od nazwy użytkownika. Opublikowany interfejs API ma nazwę *WeatherAPI*; jednak nazywamy ją *WeatherForecasts* podczas jej tworzenia. Wykonaj następujące kroki, aby naprawić nazwę:

![Zrzut ekranu przedstawiający wypełniony interfejs API z różnymi nazwami wyróżnionymi](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. Usuń następujący wiersz z `Startup.ConfigureServices` metody:
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. Dodaj następujący kod do metody `Startup.ConfigureServices`:
    
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

1. Otwórz nowo utworzony profil publikowania. Można go znaleźć z **Eksplorator rozwiązań** w folderze *Properties/PublishProfiles* .

    ![Zrzut ekranu pokazujący lokalizację pliku profilu publikowania wyróżnione](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. Zmień `<OpenAPIDocumentName>` wartość elementu z `v1` na `WeatherForecasts` .

    ![zrzut ekranu przedstawiający zmiany wymagane dla profilu publikacji](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. Ponownie Opublikuj ASP.NET Core internetowy interfejs API i Otwórz wystąpienie usługi Azure API Management w Azure Portal.
1. Odśwież stronę w przeglądarce. Zobaczysz, że nazwa interfejsu API jest teraz poprawna.

    ![Zrzut ekranu ukończonego interfejsu API w portalu](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>Sprawdź, czy interfejs API sieci Web działa

Wdrożony ASP.NET Core Web API można przetestować na platformie Azure API Management z poziomu Azure Portal, wykonując następujące czynności:

1. Otwórz kartę **Test**.
1. Wybierz **/** lub **Pobierz** operację.
1. Wybierz pozycję **Wyślij**.

    ![Zrzut ekranu portalu przed testem](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

Pomyślna odpowiedź będzie wyglądać następująco:

![Zrzut ekranu przedstawiający pomyślną odpowiedź z API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>Czyszczenie

Po zakończeniu testowania aplikacji przejdź do [Azure Portal](https://portal.azure.com/) i Usuń aplikację.

1. Wybierz pozycję **grupy zasobów**, a następnie wybierz utworzoną grupę zasobów.

    ![Witryna Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. Na stronie **grupy zasobów** wybierz pozycję **Usuń**.

    ![Witryna Azure Portal: Strona grupy zasobów](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**. Twoja aplikacja i wszystkie inne zasoby utworzone w tym samouczku zostaną usunięte z platformy Azure.

## <a name="next-steps"></a>Następne kroki

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Zasoby dodatkowe

- [API Management platformy Azure](/azure/api-management/api-management-key-concepts)
- [Azure App Service](/azure/app-service/app-service-web-overview)

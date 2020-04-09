---
title: Monitoruj i debuguj — devops z ASP.NET Core i azure
author: CamSoper
description: Monitorowanie i debugowanie kodu w ramach rozwiązania DevOps z ASP.NET Core i Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659503"
---
# <a name="monitor-and-debug"></a>Monitorowanie i debugowanie

Po wdrożeniu aplikacji i syteniu potoku DevOps ważne jest, aby zrozumieć, jak monitorować i rozwiązywać problemy z aplikacją.

W tej sekcji wykonasz następujące zadania:

* Znajdowanie podstawowych danych dotyczących monitorowania i rozwiązywania problemów w witrynie Azure portal
* Dowiedz się, jak usługa Azure Monitor zapewnia głębsze spojrzenie na metryki we wszystkich usługach platformy Azure
* Łączenie aplikacji sieci Web z aplikacją Application Insights do profilowania aplikacji
* Włącz rejestrowanie i dowiedz się, gdzie pobierać dzienniki
* Przesyłanie strumieniowe dzienników w czasie rzeczywistym
* Dowiedz się, gdzie skonfigurować alerty
* Dowiedz się więcej o zdalnym debugowaniu aplikacji sieci web usługi Azure App Service.

## <a name="basic-monitoring-and-troubleshooting"></a>Podstawowe monitorowanie i rozwiązywanie problemów

Aplikacje internetowe usługi App Service są łatwo monitorowane w czasie rzeczywistym. Portal Azure renderuje metryki na łatwych do zrozumienia wykresach i wykresach.

1. Otwórz [witrynę Azure portal](https://portal.azure.com), a następnie przejdź do *usługi\<mywebapp unique_number\> * App Service.

1. Na karcie **Przegląd** są wyświetlane przydatne informacje "w skrócie", w tym wykresy przedstawiające najnowsze dane.

    ![Zrzut ekranu przedstawiający panel przeglądu](./media/monitoring/overview.png)

    * **Http 5xx**: Liczba błędów po stronie serwera, zwykle wyjątków w kodzie ASP.NET Core.
    * **Dane w**: Dane przychodzące do aplikacji internetowej.
    * **Wyjście danych:** Wyjście danych z aplikacji sieci web do klientów.
    * **Żądania:** Liczba żądań HTTP.
    * **Średni czas odpowiedzi:** średni czas odpowiadania aplikacji sieci web na żądania HTTP.

    Na tej stronie znajduje się również kilka samoobsługowych narzędzi do rozwiązywania problemów i optymalizacji.

    ![Zrzut ekranu przedstawiający narzędzia samoobsługowe](./media/monitoring/wizards.png)

    * **Diagnozowanie i rozwiązywanie problemów** jest samoobsługowym narzędziem do rozwiązywania problemów.
    * **Usługa Application Insights** służy do profilowania wydajności i zachowania aplikacji i została omówiona w dalszej części tej sekcji.
    * **Doradca usługi aplikacji** wydaje zalecenia, aby dostosować środowisko aplikacji.

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

[Usługa Azure Monitor](/azure/monitoring-and-diagnostics/) to scentralizowana usługa do monitorowania wszystkich metryk i ustawiania alertów w usługach platformy Azure. W usłudze Azure Monitor administratorzy mogą szczegółowe śledzenie wydajności i identyfikować trendy. Każda usługa platformy Azure oferuje własny [zestaw metryk](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) do usługi Azure Monitor.

## <a name="profile-with-application-insights"></a>Profil ze statystykami aplikacji

[Usługa Application Insights](/azure/application-insights/app-insights-overview) to usługa platformy Azure do analizowania wydajności i stabilności aplikacji sieci web oraz sposobu, w jaki użytkownicy z nich korzystają. Dane z usługi Application Insights są szersze i głębsze niż dane usługi Azure Monitor. Dane mogą dostarczyć deweloperom i administratorom kluczowych informacji do ulepszania aplikacji. Usługa Application Insights można dodać do zasobu usługi Azure App Service bez zmian kodu.

1. Otwórz [witrynę Azure portal](https://portal.azure.com), a następnie przejdź do *usługi\<mywebapp unique_number\> * App Service.
1. Na karcie **Przegląd** kliknij kafelek **Usługi Application Insights.**

    ![Kafelek usługi Application Insights](./media/monitoring/app-insights.png)

1. Wybierz przycisk opcji **Utwórz nowy zasób.** Użyj domyślnej nazwy zasobu i wybierz lokalizację zasobu Usługi Application Insights. Lokalizacja nie musi być zgodna z lokalizacją aplikacji internetowej.

    ![Konfiguracja usługi Application Insights](./media/monitoring/new-app-insights.png)

1. W obszarze **Runtime/Framework**wybierz **opcję ASP.NET Core**. Zaakceptuj ustawienia domyślne.
1. Kliknij przycisk **OK**. Jeśli zostanie wyświetlony monit o potwierdzenie, wybierz pozycję **Kontynuuj**.
1. Po utworzeniu zasobu kliknij nazwę zasobu usługi Application Insights, aby przejść bezpośrednio do strony Usługi Application Insights.

    ![Nowy zasób usługi Application Insights jest gotowy](./media/monitoring/new-app-insights-done.png)

Gdy aplikacja jest używana, gromadzą się dane. Wybierz **odśwież,** aby ponownie załadować blok z nowymi danymi.

![Karta Przegląd usługi Application Insights](./media/monitoring/app-insights-overview.png)

Usługa Application Insights udostępnia przydatne informacje po stronie serwera bez dodatkowej konfiguracji. Aby uzyskać jak największą wartość z usługi Application Insights, [zaarząduj aplikację za pomocą sdk usługi Application Insights](/azure/application-insights/app-insights-asp-net-core). Po prawidłowym skonfigurowaniu usługa zapewnia kompleksowe monitorowanie na serwerze sieci Web i w przeglądarce, w tym wydajność po stronie klienta. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Rejestrowanie

Dzienniki serwera sieci Web i aplikacji są domyślnie wyłączone w usłudze Azure App Service. Włącz dzienniki, wykonując następujące czynności:

1. Otwórz [witrynę Azure portal](https://portal.azure.com)i przejdź do *usługi\<mywebapp unique_number\> * App Service.
1. W menu po lewej stronie przewiń w dół do sekcji **Monitorowanie.** Wybierz **dzienniki diagnostyczne**.

    ![Łącze dzienników diagnostycznych](./media/monitoring/logging.png)

1. Włącz **rejestrowanie aplikacji (System plików)**. Jeśli zostanie wyświetlony monit, kliknij to pole, aby zainstalować rozszerzenia, aby włączyć rejestrowanie aplikacji w aplikacji sieci web.
1. Ustaw **rejestrowanie serwera sieci Web** na System **plików**.
1. Wprowadź **okres przechowywania** w dniach. Na przykład 30.
1. Kliknij przycisk **Zapisz**.

dzienniki ASP.NET Core i serwera sieci web (App Service) są generowane dla aplikacji sieci web. Można je pobrać za pomocą wyświetlonych informacji FTP/FTPS. Hasło jest takie samo jak poświadczenia wdrożenia utworzone wcześniej w tym przewodniku. Dzienniki mogą być [przesyłane strumieniowo bezpośrednio do komputera lokalnego za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure.](/azure/app-service/web-sites-enable-diagnostic-log#download) Dzienniki można również [przeglądać w usłudze Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Przesyłanie strumieniowe w dzienniku

Dzienniki aplikacji i serwera sieci web mogą być przesyłane strumieniowo w czasie rzeczywistym za pośrednictwem portalu.

1. Otwórz [witrynę Azure portal](https://portal.azure.com)i przejdź do *usługi\<mywebapp unique_number\> * App Service.
1. W menu po lewej stronie przewiń w dół do sekcji **Monitorowanie** i wybierz pozycję **Strumień dziennika**.

    ![Zrzut ekranu przedstawiający łącze strumienia dziennika](./media/monitoring/log-stream.png)

Dzienniki mogą być również [przesyłane strumieniowo za pośrednictwem interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell,](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)w tym za pośrednictwem powłoki chmury.

## <a name="alerts"></a>Alerty

Usługa Azure Monitor udostępnia również [alerty w czasie rzeczywistym](/azure/monitoring-and-diagnostics/insights-alerts-portal) na podstawie metryk, zdarzeń administracyjnych i innych kryteriów.

> *Uwaga: Obecnie alerty dotyczące metryk aplikacji sieci web są dostępne tylko w usłudze Alerty (klasyczne).*

[Usługi alerty (klasyczne)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) można znaleźć w usłudze Azure Monitor lub w sekcji **Monitorowanie** ustawień usługi app service.

![Łącze alerty (klasyczne)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Debugowanie na żywo

Usługa Azure App Service można [debugować zdalnie za pomocą programu Visual Studio,](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) gdy dzienniki nie dostarczają wystarczających informacji. Jednak zdalne debugowanie wymaga aplikacji do skompilowania z symbolami debugowania. Debugowanie nie powinny być wykonywane w produkcji, z wyjątkiem w ostateczności.

## <a name="conclusion"></a>Podsumowanie

W tej sekcji wykonane są następujące zadania:

* Znajdowanie podstawowych danych dotyczących monitorowania i rozwiązywania problemów w witrynie Azure portal
* Dowiedz się, jak usługa Azure Monitor zapewnia głębsze spojrzenie na metryki we wszystkich usługach platformy Azure
* Łączenie aplikacji sieci Web z aplikacją Application Insights do profilowania aplikacji
* Włącz rejestrowanie i dowiedz się, gdzie pobierać dzienniki
* Przesyłanie strumieniowe dzienników w czasie rzeczywistym
* Dowiedz się, gdzie skonfigurować alerty
* Dowiedz się więcej o zdalnym debugowaniu aplikacji sieci web usługi Azure App Service.

## <a name="additional-reading"></a>Dodatkowa lektura

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitorowanie wydajności aplikacji sieci Web platformy Azure za pomocą usługi Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Rozwiązywanie problemów z aplikacją internetową w usłudze Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Tworzenie klasycznych alertów metryk w usłudze Azure Monitor dla usług platformy Azure — witryna Azure portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)

---
title: Monitorowanie i debugowanie — DevOps z użyciem ASP.NET Core i platformy Azure
author: CamSoper
description: Monitorowanie i debugowanie kodu w ramach rozwiązania DevOps z użyciem ASP.NET Core i platformy Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
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
uid: azure/devops/monitor
ms.openlocfilehash: 9e8b48df5a3ecf78e315c7f33b5ab75d6f329cc2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012777"
---
# <a name="monitor-and-debug"></a>Monitorowanie i debugowanie

Po wdrożeniu aplikacji i zbudowaniu potoku DevOps należy zrozumieć, jak monitorować i rozwiązywać problemy z aplikacją.

W tej sekcji wykonasz następujące zadania:

* Znajdź podstawowe dane monitorowania i rozwiązywania problemów w Azure Portal
* Dowiedz się, w jaki sposób Azure Monitor zapewnia bardziej szczegółowy wpływ na metryki dla wszystkich usług platformy Azure
* Łączenie aplikacji sieci Web za pomocą Application Insights na potrzeby profilowania aplikacji
* Włącz rejestrowanie i Dowiedz się, gdzie pobrać dzienniki
* Rejestrowanie strumieni w czasie rzeczywistym
* Dowiedz się, gdzie skonfigurować alerty
* Dowiedz się więcej na temat debugowania zdalnego Azure App Service Web Apps.

## <a name="basic-monitoring-and-troubleshooting"></a>Podstawowe monitorowanie i rozwiązywanie problemów

App Service aplikacje sieci Web są łatwo monitorowane w czasie rzeczywistym. Azure Portal renderuje metryki w łatwych do zrozumienia grafach i wykresach.

1. Otwórz [Azure Portal](https://portal.azure.com), a następnie przejdź do App Service *mywebapp \<unique_number\> * .

1. Karta **Przegląd** przedstawia przydatne informacje, w tym Wykresy zawierające ostatnie metryki.

    ![Zrzut ekranu przedstawiający panel przegląd](./media/monitoring/overview.png)

    * **Http 5xx**: liczba błędów po stronie serwera, zazwyczaj wyjątków w kodzie ASP.NET Core.
    * **Dane w**: dane przychodzące z aplikacji sieci Web.
    * **Dane wyjściowe: dane wychodzące**z aplikacji sieci Web do klientów.
    * **Żądania**: liczba żądań HTTP.
    * **Średni czas odpowiedzi**: Średni czas odpowiedzi aplikacji sieci Web na żądania HTTP.

    Na tej stronie znajdują się również różne narzędzia samoobsługowe umożliwiające rozwiązywanie problemów i optymalizację.

    ![Zrzut ekranu przedstawiający Narzędzia samoobsługowe](./media/monitoring/wizards.png)

    * **Diagnozowanie i rozwiązywanie problemów** to samoobsługowe Rozwiązywanie problemów.
    * **Application Insights** służy do profilowania wydajności i zachowania aplikacji, a następnie omówiono w dalszej części tej sekcji.
    * **App Service Advisor** udostępnia zalecenia dotyczące dostrajania środowiska aplikacji.

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

[Azure monitor](/azure/monitoring-and-diagnostics/) to scentralizowana usługa do monitorowania wszystkich metryk i ustawiania alertów w ramach usług platformy Azure. W ramach Azure Monitor Administratorzy mogą szczegółowo śledzić wydajność i identyfikować trendy. Każda usługa platformy Azure oferuje swój własny [zestaw metryk](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) do Azure monitor.

## <a name="profile-with-application-insights"></a>Profil z Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) to usługa platformy Azure do analizowania wydajności i stabilności aplikacji sieci Web oraz sposobu ich używania przez użytkowników. Dane z Application Insights są szersze i bardziej precyzyjne niż Azure Monitor. Dane mogą udostępniać deweloperom i administratorom najważniejsze informacje dotyczące ulepszania aplikacji. Application Insights można dodać do zasobu Azure App Service bez wprowadzania zmian w kodzie.

1. Otwórz [Azure Portal](https://portal.azure.com), a następnie przejdź do App Service *mywebapp \<unique_number\> * .
1. Na karcie **Omówienie** kliknij kafelek **Application Insights** .

    ![Kafelek Application Insights](./media/monitoring/app-insights.png)

1. Wybierz przycisk radiowy **Utwórz nowy zasób** . Użyj domyślnej nazwy zasobu i wybierz lokalizację dla Application Insights zasobu. Lokalizacja nie musi być zgodna z aplikacją sieci Web.

    ![Konfiguracja Application Insights](./media/monitoring/new-app-insights.png)

1. Dla **środowiska uruchomieniowego/platformy**wybierz pozycję **ASP.NET Core**. Zaakceptuj ustawienia domyślne.
1. Wybierz przycisk **OK**. Jeśli zostanie wyświetlony monit o potwierdzenie, wybierz pozycję **Kontynuuj**.
1. Po utworzeniu zasobu kliknij nazwę Application Insights zasobu, aby przejść bezpośrednio do strony Application Insights.

    ![Nowy zasób Application Insights jest gotowy](./media/monitoring/new-app-insights-done.png)

Gdy aplikacja jest używana, gromadzone są dane. Wybierz pozycję **Odśwież** , aby ponownie załadować blok przy użyciu nowych danych.

![Karta przegląd Application Insights](./media/monitoring/app-insights-overview.png)

Application Insights zapewnia przydatne informacje po stronie serwera bez dodatkowej konfiguracji. Aby uzyskać największą wartość z Application Insights, [Instrumentacja aplikacji za pomocą zestawu Application Insights SDK](/azure/application-insights/app-insights-asp-net-core). Po poprawnym skonfigurowaniu usługa zapewnia kompleksowe monitorowanie między serwerem sieci Web i przeglądarką, w tym wydajnością po stronie klienta. Aby uzyskać więcej informacji, zobacz [dokumentację Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Rejestrowanie

Dzienniki serwera sieci Web i aplikacji są domyślnie wyłączone w Azure App Service. Włącz dzienniki, wykonując następujące czynności:

1. Otwórz [Azure Portal](https://portal.azure.com)i przejdź do App Service *mywebapp \<unique_number\> * .
1. W menu po lewej stronie przewiń w dół do sekcji **monitorowanie** . Wybierz pozycję **dzienniki diagnostyczne**.

    ![Łącze do dzienników diagnostycznych](./media/monitoring/logging.png)

1. Włącz **Rejestrowanie aplikacji (system plików)**. Jeśli zostanie wyświetlony monit, kliknij pole, aby zainstalować rozszerzenia w celu włączenia rejestrowania aplikacji w aplikacji sieci Web.
1. Ustaw **rejestrowanie serwera sieci Web** w **systemie plików**.
1. Wprowadź **okres przechowywania** w dniach. Na przykład 30.
1. Kliknij pozycję **Zapisz**.

Dla aplikacji sieci Web generowane są dzienniki ASP.NET Core i serwera sieci Web (App Service). Można je pobrać przy użyciu informacji dotyczących protokołu FTP/FTPS. Hasło jest takie samo jak w przypadku poświadczeń wdrożenia utworzonych wcześniej w tym przewodniku. Dzienniki mogą być [przesyłane strumieniowo bezpośrednio do komputera lokalnego przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure](/azure/app-service/web-sites-enable-diagnostic-log#download). Dzienniki mogą być również [wyświetlane w Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Przesyłanie strumieniowe dzienników

Dzienniki aplikacji i serwera sieci Web mogą być przesyłane strumieniowo w czasie rzeczywistym za pomocą portalu.

1. Otwórz [Azure Portal](https://portal.azure.com)i przejdź do App Service *mywebapp \<unique_number\> * .
1. W menu po lewej stronie przewiń w dół do sekcji **monitorowanie** i wybierz pozycję **strumień dzienników**.

    ![Zrzut ekranu przedstawiający link strumienia dziennika](./media/monitoring/log-stream.png)

Dzienniki mogą być [przesyłane strumieniowo za pośrednictwem interfejsu wiersza polecenia platformy Azure lub Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), w tym Cloud Shell.

## <a name="alerts"></a>Alerty

Azure Monitor również zawiera [alerty w czasie rzeczywistym](/azure/monitoring-and-diagnostics/insights-alerts-portal) na podstawie metryk, zdarzeń administracyjnych i innych kryteriów.

> *Uwaga: obecnie alerty dotyczące metryk aplikacji sieci Web są dostępne tylko w usłudze alertów (klasycznych).*

[Usługę alertów (klasyczną)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) można znaleźć w sekcji Azure monitor lub w obszarze **monitorowanie** ustawień App Service.

![Link do alertów (klasyczny)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Debugowanie na żywo

Azure App Service może być [debugowany zdalnie z programem Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) , gdy dzienniki nie zapewniają wystarczającej ilości informacji. Debugowanie zdalne wymaga jednak, aby aplikacja była skompilowana przy użyciu symboli debugowania. Debugowanie nie powinno odbywać się w środowisku produkcyjnym, z wyjątkiem ostatniej.

## <a name="conclusion"></a>Podsumowanie

W tej sekcji zostały wykonane następujące zadania:

* Znajdź podstawowe dane monitorowania i rozwiązywania problemów w Azure Portal
* Dowiedz się, w jaki sposób Azure Monitor zapewnia bardziej szczegółowy wpływ na metryki dla wszystkich usług platformy Azure
* Łączenie aplikacji sieci Web za pomocą Application Insights na potrzeby profilowania aplikacji
* Włącz rejestrowanie i Dowiedz się, gdzie pobrać dzienniki
* Rejestrowanie strumieni w czasie rzeczywistym
* Dowiedz się, gdzie skonfigurować alerty
* Dowiedz się więcej na temat debugowania zdalnego Azure App Service Web Apps.

## <a name="additional-reading"></a>Materiały uzupełniające

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitorowanie wydajności aplikacji sieci Web platformy Azure za pomocą Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w programie Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Tworzenie klasycznych alertów metryk w Azure Monitor dla usług platformy Azure — Azure Portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)

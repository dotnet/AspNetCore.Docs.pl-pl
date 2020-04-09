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
# <a name="monitor-and-debug"></a><span data-ttu-id="9cfe9-103">Monitorowanie i debugowanie</span><span class="sxs-lookup"><span data-stu-id="9cfe9-103">Monitor and debug</span></span>

<span data-ttu-id="9cfe9-104">Po wdrożeniu aplikacji i syteniu potoku DevOps ważne jest, aby zrozumieć, jak monitorować i rozwiązywać problemy z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="9cfe9-105">W tej sekcji wykonasz następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="9cfe9-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="9cfe9-106">Znajdowanie podstawowych danych dotyczących monitorowania i rozwiązywania problemów w witrynie Azure portal</span><span class="sxs-lookup"><span data-stu-id="9cfe9-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="9cfe9-107">Dowiedz się, jak usługa Azure Monitor zapewnia głębsze spojrzenie na metryki we wszystkich usługach platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9cfe9-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="9cfe9-108">Łączenie aplikacji sieci Web z aplikacją Application Insights do profilowania aplikacji</span><span class="sxs-lookup"><span data-stu-id="9cfe9-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="9cfe9-109">Włącz rejestrowanie i dowiedz się, gdzie pobierać dzienniki</span><span class="sxs-lookup"><span data-stu-id="9cfe9-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="9cfe9-110">Przesyłanie strumieniowe dzienników w czasie rzeczywistym</span><span class="sxs-lookup"><span data-stu-id="9cfe9-110">Stream logs in real time</span></span>
* <span data-ttu-id="9cfe9-111">Dowiedz się, gdzie skonfigurować alerty</span><span class="sxs-lookup"><span data-stu-id="9cfe9-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="9cfe9-112">Dowiedz się więcej o zdalnym debugowaniu aplikacji sieci web usługi Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="9cfe9-113">Podstawowe monitorowanie i rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="9cfe9-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="9cfe9-114">Aplikacje internetowe usługi App Service są łatwo monitorowane w czasie rzeczywistym.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="9cfe9-115">Portal Azure renderuje metryki na łatwych do zrozumienia wykresach i wykresach.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="9cfe9-116">Otwórz [witrynę Azure portal](https://portal.azure.com), a następnie przejdź do \*usługi\<mywebapp unique_number\> \* App Service.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="9cfe9-117">Na karcie **Przegląd** są wyświetlane przydatne informacje "w skrócie", w tym wykresy przedstawiające najnowsze dane.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Zrzut ekranu przedstawiający panel przeglądu](./media/monitoring/overview.png)

    * <span data-ttu-id="9cfe9-119">**Http 5xx**: Liczba błędów po stronie serwera, zwykle wyjątków w kodzie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="9cfe9-120">**Dane w**: Dane przychodzące do aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="9cfe9-121">**Wyjście danych:** Wyjście danych z aplikacji sieci web do klientów.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="9cfe9-122">**Żądania:** Liczba żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="9cfe9-123">**Średni czas odpowiedzi:** średni czas odpowiadania aplikacji sieci web na żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="9cfe9-124">Na tej stronie znajduje się również kilka samoobsługowych narzędzi do rozwiązywania problemów i optymalizacji.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Zrzut ekranu przedstawiający narzędzia samoobsługowe](./media/monitoring/wizards.png)

    * <span data-ttu-id="9cfe9-126">**Diagnozowanie i rozwiązywanie problemów** jest samoobsługowym narzędziem do rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="9cfe9-127">**Usługa Application Insights** służy do profilowania wydajności i zachowania aplikacji i została omówiona w dalszej części tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="9cfe9-128">**Doradca usługi aplikacji** wydaje zalecenia, aby dostosować środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="9cfe9-129">Zaawansowane monitorowanie</span><span class="sxs-lookup"><span data-stu-id="9cfe9-129">Advanced monitoring</span></span>

<span data-ttu-id="9cfe9-130">[Usługa Azure Monitor](/azure/monitoring-and-diagnostics/) to scentralizowana usługa do monitorowania wszystkich metryk i ustawiania alertów w usługach platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="9cfe9-131">W usłudze Azure Monitor administratorzy mogą szczegółowe śledzenie wydajności i identyfikować trendy.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="9cfe9-132">Każda usługa platformy Azure oferuje własny [zestaw metryk](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) do usługi Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="9cfe9-133">Profil ze statystykami aplikacji</span><span class="sxs-lookup"><span data-stu-id="9cfe9-133">Profile with Application Insights</span></span>

<span data-ttu-id="9cfe9-134">[Usługa Application Insights](/azure/application-insights/app-insights-overview) to usługa platformy Azure do analizowania wydajności i stabilności aplikacji sieci web oraz sposobu, w jaki użytkownicy z nich korzystają.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="9cfe9-135">Dane z usługi Application Insights są szersze i głębsze niż dane usługi Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="9cfe9-136">Dane mogą dostarczyć deweloperom i administratorom kluczowych informacji do ulepszania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="9cfe9-137">Usługa Application Insights można dodać do zasobu usługi Azure App Service bez zmian kodu.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="9cfe9-138">Otwórz [witrynę Azure portal](https://portal.azure.com), a następnie przejdź do \*usługi\<mywebapp unique_number\> \* App Service.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9cfe9-139">Na karcie **Przegląd** kliknij kafelek **Usługi Application Insights.**</span><span class="sxs-lookup"><span data-stu-id="9cfe9-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Kafelek usługi Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="9cfe9-141">Wybierz przycisk opcji **Utwórz nowy zasób.**</span><span class="sxs-lookup"><span data-stu-id="9cfe9-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="9cfe9-142">Użyj domyślnej nazwy zasobu i wybierz lokalizację zasobu Usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="9cfe9-143">Lokalizacja nie musi być zgodna z lokalizacją aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-143">The location doesn't need to match that of your web app.</span></span>

    ![Konfiguracja usługi Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="9cfe9-145">W obszarze **Runtime/Framework**wybierz **opcję ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="9cfe9-146">Zaakceptuj ustawienia domyślne.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-146">Accept the default settings.</span></span>
1. <span data-ttu-id="9cfe9-147">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-147">Select **OK**.</span></span> <span data-ttu-id="9cfe9-148">Jeśli zostanie wyświetlony monit o potwierdzenie, wybierz pozycję **Kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="9cfe9-149">Po utworzeniu zasobu kliknij nazwę zasobu usługi Application Insights, aby przejść bezpośrednio do strony Usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Nowy zasób usługi Application Insights jest gotowy](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="9cfe9-151">Gdy aplikacja jest używana, gromadzą się dane.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="9cfe9-152">Wybierz **odśwież,** aby ponownie załadować blok z nowymi danymi.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-152">Select **Refresh** to reload the blade with new data.</span></span>

![Karta Przegląd usługi Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="9cfe9-154">Usługa Application Insights udostępnia przydatne informacje po stronie serwera bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="9cfe9-155">Aby uzyskać jak największą wartość z usługi Application Insights, [zaarząduj aplikację za pomocą sdk usługi Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="9cfe9-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="9cfe9-156">Po prawidłowym skonfigurowaniu usługa zapewnia kompleksowe monitorowanie na serwerze sieci Web i w przeglądarce, w tym wydajność po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="9cfe9-157">Aby uzyskać więcej informacji, zobacz [dokumentację usługi Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="9cfe9-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="9cfe9-158">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="9cfe9-158">Logging</span></span>

<span data-ttu-id="9cfe9-159">Dzienniki serwera sieci Web i aplikacji są domyślnie wyłączone w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="9cfe9-160">Włącz dzienniki, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="9cfe9-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="9cfe9-161">Otwórz [witrynę Azure portal](https://portal.azure.com)i przejdź do \*usługi\<mywebapp unique_number\> \* App Service.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9cfe9-162">W menu po lewej stronie przewiń w dół do sekcji **Monitorowanie.**</span><span class="sxs-lookup"><span data-stu-id="9cfe9-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="9cfe9-163">Wybierz **dzienniki diagnostyczne**.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-163">Select **Diagnostics logs**.</span></span>

    ![Łącze dzienników diagnostycznych](./media/monitoring/logging.png)

1. <span data-ttu-id="9cfe9-165">Włącz **rejestrowanie aplikacji (System plików)**.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="9cfe9-166">Jeśli zostanie wyświetlony monit, kliknij to pole, aby zainstalować rozszerzenia, aby włączyć rejestrowanie aplikacji w aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="9cfe9-167">Ustaw **rejestrowanie serwera sieci Web** na System **plików**.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="9cfe9-168">Wprowadź **okres przechowywania** w dniach.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="9cfe9-169">Na przykład 30.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-169">For example, 30.</span></span>
1. <span data-ttu-id="9cfe9-170">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-170">Click **Save**.</span></span>

<span data-ttu-id="9cfe9-171">dzienniki ASP.NET Core i serwera sieci web (App Service) są generowane dla aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="9cfe9-172">Można je pobrać za pomocą wyświetlonych informacji FTP/FTPS.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="9cfe9-173">Hasło jest takie samo jak poświadczenia wdrożenia utworzone wcześniej w tym przewodniku.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="9cfe9-174">Dzienniki mogą być [przesyłane strumieniowo bezpośrednio do komputera lokalnego za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure.](/azure/app-service/web-sites-enable-diagnostic-log#download)</span><span class="sxs-lookup"><span data-stu-id="9cfe9-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="9cfe9-175">Dzienniki można również [przeglądać w usłudze Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="9cfe9-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="9cfe9-176">Przesyłanie strumieniowe w dzienniku</span><span class="sxs-lookup"><span data-stu-id="9cfe9-176">Log streaming</span></span>

<span data-ttu-id="9cfe9-177">Dzienniki aplikacji i serwera sieci web mogą być przesyłane strumieniowo w czasie rzeczywistym za pośrednictwem portalu.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="9cfe9-178">Otwórz [witrynę Azure portal](https://portal.azure.com)i przejdź do \*usługi\<mywebapp unique_number\> \* App Service.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9cfe9-179">W menu po lewej stronie przewiń w dół do sekcji **Monitorowanie** i wybierz pozycję **Strumień dziennika**.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Zrzut ekranu przedstawiający łącze strumienia dziennika](./media/monitoring/log-stream.png)

<span data-ttu-id="9cfe9-181">Dzienniki mogą być również [przesyłane strumieniowo za pośrednictwem interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell,](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)w tym za pośrednictwem powłoki chmury.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="9cfe9-182">Alerty</span><span class="sxs-lookup"><span data-stu-id="9cfe9-182">Alerts</span></span>

<span data-ttu-id="9cfe9-183">Usługa Azure Monitor udostępnia również [alerty w czasie rzeczywistym](/azure/monitoring-and-diagnostics/insights-alerts-portal) na podstawie metryk, zdarzeń administracyjnych i innych kryteriów.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="9cfe9-184">*Uwaga: Obecnie alerty dotyczące metryk aplikacji sieci web są dostępne tylko w usłudze Alerty (klasyczne).*</span><span class="sxs-lookup"><span data-stu-id="9cfe9-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="9cfe9-185">[Usługi alerty (klasyczne)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) można znaleźć w usłudze Azure Monitor lub w sekcji **Monitorowanie** ustawień usługi app service.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Łącze alerty (klasyczne)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="9cfe9-187">Debugowanie na żywo</span><span class="sxs-lookup"><span data-stu-id="9cfe9-187">Live debugging</span></span>

<span data-ttu-id="9cfe9-188">Usługa Azure App Service można [debugować zdalnie za pomocą programu Visual Studio,](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) gdy dzienniki nie dostarczają wystarczających informacji.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="9cfe9-189">Jednak zdalne debugowanie wymaga aplikacji do skompilowania z symbolami debugowania.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="9cfe9-190">Debugowanie nie powinny być wykonywane w produkcji, z wyjątkiem w ostateczności.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="9cfe9-191">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="9cfe9-191">Conclusion</span></span>

<span data-ttu-id="9cfe9-192">W tej sekcji wykonane są następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="9cfe9-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="9cfe9-193">Znajdowanie podstawowych danych dotyczących monitorowania i rozwiązywania problemów w witrynie Azure portal</span><span class="sxs-lookup"><span data-stu-id="9cfe9-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="9cfe9-194">Dowiedz się, jak usługa Azure Monitor zapewnia głębsze spojrzenie na metryki we wszystkich usługach platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9cfe9-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="9cfe9-195">Łączenie aplikacji sieci Web z aplikacją Application Insights do profilowania aplikacji</span><span class="sxs-lookup"><span data-stu-id="9cfe9-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="9cfe9-196">Włącz rejestrowanie i dowiedz się, gdzie pobierać dzienniki</span><span class="sxs-lookup"><span data-stu-id="9cfe9-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="9cfe9-197">Przesyłanie strumieniowe dzienników w czasie rzeczywistym</span><span class="sxs-lookup"><span data-stu-id="9cfe9-197">Stream logs in real time</span></span>
* <span data-ttu-id="9cfe9-198">Dowiedz się, gdzie skonfigurować alerty</span><span class="sxs-lookup"><span data-stu-id="9cfe9-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="9cfe9-199">Dowiedz się więcej o zdalnym debugowaniu aplikacji sieci web usługi Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="9cfe9-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="9cfe9-200">Dodatkowa lektura</span><span class="sxs-lookup"><span data-stu-id="9cfe9-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="9cfe9-201">Monitorowanie wydajności aplikacji sieci Web platformy Azure za pomocą usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="9cfe9-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="9cfe9-202">Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9cfe9-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="9cfe9-203">Rozwiązywanie problemów z aplikacją internetową w usłudze Azure App Service przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9cfe9-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="9cfe9-204">Tworzenie klasycznych alertów metryk w usłudze Azure Monitor dla usług platformy Azure — witryna Azure portal</span><span class="sxs-lookup"><span data-stu-id="9cfe9-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)

---
title: Monitorowanie i debugowanie — DevOps z użyciem ASP.NET Core i platformy Azure
author: CamSoper
description: Monitorowanie i debugowanie kodu w ramach rozwiązania DevOps z użyciem ASP.NET Core i platformy Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056754"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="9fa0f-103">Monitorowanie i debugowanie</span><span class="sxs-lookup"><span data-stu-id="9fa0f-103">Monitor and debug</span></span>

<span data-ttu-id="9fa0f-104">Po wdrożeniu aplikacji i zbudowaniu potoku DevOps należy zrozumieć, jak monitorować i rozwiązywać problemy z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="9fa0f-105">W tej sekcji wykonasz następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="9fa0f-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="9fa0f-106">Znajdź podstawowe dane monitorowania i rozwiązywania problemów w Azure Portal</span><span class="sxs-lookup"><span data-stu-id="9fa0f-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="9fa0f-107">Dowiedz się, w jaki sposób Azure Monitor zapewnia bardziej szczegółowy wpływ na metryki dla wszystkich usług platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9fa0f-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="9fa0f-108">Łączenie aplikacji sieci Web za pomocą Application Insights na potrzeby profilowania aplikacji</span><span class="sxs-lookup"><span data-stu-id="9fa0f-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="9fa0f-109">Włącz rejestrowanie i Dowiedz się, gdzie pobrać dzienniki</span><span class="sxs-lookup"><span data-stu-id="9fa0f-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="9fa0f-110">Rejestrowanie strumieni w czasie rzeczywistym</span><span class="sxs-lookup"><span data-stu-id="9fa0f-110">Stream logs in real time</span></span>
* <span data-ttu-id="9fa0f-111">Dowiedz się, gdzie skonfigurować alerty</span><span class="sxs-lookup"><span data-stu-id="9fa0f-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="9fa0f-112">Dowiedz się więcej na temat debugowania zdalnego Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="9fa0f-113">Podstawowe monitorowanie i rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="9fa0f-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="9fa0f-114">App Service aplikacje sieci Web są łatwo monitorowane w czasie rzeczywistym.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="9fa0f-115">Azure Portal renderuje metryki w łatwych do zrozumienia grafach i wykresach.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="9fa0f-116">Otwórz [Azure Portal](https://portal.azure.com), a następnie przejdź do App Service *mywebapp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="9fa0f-117">Karta **Przegląd** przedstawia przydatne informacje, w tym Wykresy zawierające ostatnie metryki.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Zrzut ekranu przedstawiający panel przegląd](./media/monitoring/overview.png)

    * <span data-ttu-id="9fa0f-119">**Http 5xx** : liczba błędów po stronie serwera, zazwyczaj wyjątków w kodzie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-119">**Http 5xx** : Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="9fa0f-120">**Dane w** : dane przychodzące z aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-120">**Data In** : Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="9fa0f-121">**Dane wyjściowe: dane wychodzące** z aplikacji sieci Web do klientów.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-121">**Data Out** : Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="9fa0f-122">**Żądania** : liczba żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-122">**Requests** : Count of HTTP requests.</span></span>
    * <span data-ttu-id="9fa0f-123">**Średni czas odpowiedzi** : Średni czas odpowiedzi aplikacji sieci Web na żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-123">**Average Response Time** : Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="9fa0f-124">Na tej stronie znajdują się również różne narzędzia samoobsługowe umożliwiające rozwiązywanie problemów i optymalizację.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Zrzut ekranu przedstawiający Narzędzia samoobsługowe](./media/monitoring/wizards.png)

    * <span data-ttu-id="9fa0f-126">**Diagnozowanie i rozwiązywanie problemów** to samoobsługowe Rozwiązywanie problemów.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="9fa0f-127">**Application Insights** służy do profilowania wydajności i zachowania aplikacji, a następnie omówiono w dalszej części tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="9fa0f-128">**App Service Advisor** udostępnia zalecenia dotyczące dostrajania środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="9fa0f-129">Zaawansowane monitorowanie</span><span class="sxs-lookup"><span data-stu-id="9fa0f-129">Advanced monitoring</span></span>

<span data-ttu-id="9fa0f-130">[Azure monitor](/azure/monitoring-and-diagnostics/) to scentralizowana usługa do monitorowania wszystkich metryk i ustawiania alertów w ramach usług platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="9fa0f-131">W ramach Azure Monitor Administratorzy mogą szczegółowo śledzić wydajność i identyfikować trendy.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="9fa0f-132">Każda usługa platformy Azure oferuje swój własny [zestaw metryk](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) do Azure monitor.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="9fa0f-133">Profil z Application Insights</span><span class="sxs-lookup"><span data-stu-id="9fa0f-133">Profile with Application Insights</span></span>

<span data-ttu-id="9fa0f-134">[Application Insights](/azure/application-insights/app-insights-overview) to usługa platformy Azure do analizowania wydajności i stabilności aplikacji sieci Web oraz sposobu ich używania przez użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="9fa0f-135">Dane z Application Insights są szersze i bardziej precyzyjne niż Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="9fa0f-136">Dane mogą udostępniać deweloperom i administratorom najważniejsze informacje dotyczące ulepszania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="9fa0f-137">Application Insights można dodać do zasobu Azure App Service bez wprowadzania zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="9fa0f-138">Otwórz [Azure Portal](https://portal.azure.com), a następnie przejdź do App Service *mywebapp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9fa0f-139">Na karcie **Omówienie** kliknij kafelek **Application Insights** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Kafelek Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="9fa0f-141">Wybierz przycisk radiowy **Utwórz nowy zasób** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="9fa0f-142">Użyj domyślnej nazwy zasobu i wybierz lokalizację dla Application Insights zasobu.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="9fa0f-143">Lokalizacja nie musi być zgodna z aplikacją sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-143">The location doesn't need to match that of your web app.</span></span>

    ![Konfiguracja Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="9fa0f-145">Dla **środowiska uruchomieniowego/platformy** wybierz pozycję **ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-145">For **Runtime/Framework** , select **ASP.NET Core** .</span></span> <span data-ttu-id="9fa0f-146">Zaakceptuj ustawienia domyślne.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-146">Accept the default settings.</span></span>
1. <span data-ttu-id="9fa0f-147">Wybierz pozycję **OK** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-147">Select **OK** .</span></span> <span data-ttu-id="9fa0f-148">Jeśli zostanie wyświetlony monit o potwierdzenie, wybierz pozycję **Kontynuuj** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-148">If prompted to confirm, select **Continue** .</span></span>
1. <span data-ttu-id="9fa0f-149">Po utworzeniu zasobu kliknij nazwę Application Insights zasobu, aby przejść bezpośrednio do strony Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Nowy zasób Application Insights jest gotowy](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="9fa0f-151">Gdy aplikacja jest używana, gromadzone są dane.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="9fa0f-152">Wybierz pozycję **Odśwież** , aby ponownie załadować blok przy użyciu nowych danych.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-152">Select **Refresh** to reload the blade with new data.</span></span>

![Karta przegląd Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="9fa0f-154">Application Insights zapewnia przydatne informacje po stronie serwera bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="9fa0f-155">Aby uzyskać największą wartość z Application Insights, [Instrumentacja aplikacji za pomocą zestawu Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="9fa0f-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="9fa0f-156">Po poprawnym skonfigurowaniu usługa zapewnia kompleksowe monitorowanie między serwerem sieci Web i przeglądarką, w tym wydajnością po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="9fa0f-157">Aby uzyskać więcej informacji, zobacz [dokumentację Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="9fa0f-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="9fa0f-158">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="9fa0f-158">Logging</span></span>

<span data-ttu-id="9fa0f-159">Dzienniki serwera sieci Web i aplikacji są domyślnie wyłączone w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="9fa0f-160">Włącz dzienniki, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="9fa0f-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="9fa0f-161">Otwórz [Azure Portal](https://portal.azure.com)i przejdź do App Service *mywebapp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9fa0f-162">W menu po lewej stronie przewiń w dół do sekcji **monitorowanie** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="9fa0f-163">Wybierz pozycję **dzienniki diagnostyczne** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-163">Select **Diagnostics logs** .</span></span>

    ![Łącze do dzienników diagnostycznych](./media/monitoring/logging.png)

1. <span data-ttu-id="9fa0f-165">Włącz **Rejestrowanie aplikacji (system plików)** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-165">Turn on **Application Logging (Filesystem)** .</span></span> <span data-ttu-id="9fa0f-166">Jeśli zostanie wyświetlony monit, kliknij pole, aby zainstalować rozszerzenia w celu włączenia rejestrowania aplikacji w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="9fa0f-167">Ustaw **rejestrowanie serwera sieci Web** w **systemie plików** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-167">Set **Web server logging** to **File System** .</span></span>
1. <span data-ttu-id="9fa0f-168">Wprowadź **okres przechowywania** w dniach.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="9fa0f-169">Na przykład 30.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-169">For example, 30.</span></span>
1. <span data-ttu-id="9fa0f-170">Kliknij pozycję **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-170">Click **Save** .</span></span>

<span data-ttu-id="9fa0f-171">Dla aplikacji sieci Web generowane są dzienniki ASP.NET Core i serwera sieci Web (App Service).</span><span class="sxs-lookup"><span data-stu-id="9fa0f-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="9fa0f-172">Można je pobrać przy użyciu informacji dotyczących protokołu FTP/FTPS.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="9fa0f-173">Hasło jest takie samo jak w przypadku poświadczeń wdrożenia utworzonych wcześniej w tym przewodniku.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="9fa0f-174">Dzienniki mogą być [przesyłane strumieniowo bezpośrednio do komputera lokalnego przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="9fa0f-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="9fa0f-175">Dzienniki mogą być również [wyświetlane w Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="9fa0f-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="9fa0f-176">Przesyłanie strumieniowe dzienników</span><span class="sxs-lookup"><span data-stu-id="9fa0f-176">Log streaming</span></span>

<span data-ttu-id="9fa0f-177">Dzienniki aplikacji i serwera sieci Web mogą być przesyłane strumieniowo w czasie rzeczywistym za pomocą portalu.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="9fa0f-178">Otwórz [Azure Portal](https://portal.azure.com)i przejdź do App Service *mywebapp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="9fa0f-179">W menu po lewej stronie przewiń w dół do sekcji **monitorowanie** i wybierz pozycję **strumień dzienników** .</span><span class="sxs-lookup"><span data-stu-id="9fa0f-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream** .</span></span>

    ![Zrzut ekranu przedstawiający link strumienia dziennika](./media/monitoring/log-stream.png)

<span data-ttu-id="9fa0f-181">Dzienniki mogą być [przesyłane strumieniowo za pośrednictwem interfejsu wiersza polecenia platformy Azure lub Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), w tym Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="9fa0f-182">Alerty</span><span class="sxs-lookup"><span data-stu-id="9fa0f-182">Alerts</span></span>

<span data-ttu-id="9fa0f-183">Azure Monitor również zawiera [alerty w czasie rzeczywistym](/azure/monitoring-and-diagnostics/insights-alerts-portal) na podstawie metryk, zdarzeń administracyjnych i innych kryteriów.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="9fa0f-184">*Uwaga: obecnie alerty dotyczące metryk aplikacji sieci Web są dostępne tylko w usłudze alertów (klasycznych).*</span><span class="sxs-lookup"><span data-stu-id="9fa0f-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="9fa0f-185">[Usługę alertów (klasyczną)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) można znaleźć w sekcji Azure monitor lub w obszarze **monitorowanie** ustawień App Service.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Link do alertów (klasyczny)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="9fa0f-187">Debugowanie na żywo</span><span class="sxs-lookup"><span data-stu-id="9fa0f-187">Live debugging</span></span>

<span data-ttu-id="9fa0f-188">Azure App Service może być [debugowany zdalnie z programem Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) , gdy dzienniki nie zapewniają wystarczającej ilości informacji.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="9fa0f-189">Debugowanie zdalne wymaga jednak, aby aplikacja była skompilowana przy użyciu symboli debugowania.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="9fa0f-190">Debugowanie nie powinno odbywać się w środowisku produkcyjnym, z wyjątkiem ostatniej.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="9fa0f-191">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="9fa0f-191">Conclusion</span></span>

<span data-ttu-id="9fa0f-192">W tej sekcji zostały wykonane następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="9fa0f-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="9fa0f-193">Znajdź podstawowe dane monitorowania i rozwiązywania problemów w Azure Portal</span><span class="sxs-lookup"><span data-stu-id="9fa0f-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="9fa0f-194">Dowiedz się, w jaki sposób Azure Monitor zapewnia bardziej szczegółowy wpływ na metryki dla wszystkich usług platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9fa0f-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="9fa0f-195">Łączenie aplikacji sieci Web za pomocą Application Insights na potrzeby profilowania aplikacji</span><span class="sxs-lookup"><span data-stu-id="9fa0f-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="9fa0f-196">Włącz rejestrowanie i Dowiedz się, gdzie pobrać dzienniki</span><span class="sxs-lookup"><span data-stu-id="9fa0f-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="9fa0f-197">Rejestrowanie strumieni w czasie rzeczywistym</span><span class="sxs-lookup"><span data-stu-id="9fa0f-197">Stream logs in real time</span></span>
* <span data-ttu-id="9fa0f-198">Dowiedz się, gdzie skonfigurować alerty</span><span class="sxs-lookup"><span data-stu-id="9fa0f-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="9fa0f-199">Dowiedz się więcej na temat debugowania zdalnego Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="9fa0f-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="9fa0f-200">Materiały uzupełniające</span><span class="sxs-lookup"><span data-stu-id="9fa0f-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="9fa0f-201">Monitorowanie wydajności aplikacji sieci Web platformy Azure za pomocą Application Insights</span><span class="sxs-lookup"><span data-stu-id="9fa0f-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="9fa0f-202">Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w programie Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9fa0f-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="9fa0f-203">Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fa0f-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="9fa0f-204">Tworzenie klasycznych alertów metryk w Azure Monitor dla usług platformy Azure — Azure Portal</span><span class="sxs-lookup"><span data-stu-id="9fa0f-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)

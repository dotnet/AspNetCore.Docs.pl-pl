---
title: Wdrażanie aplikacji ASP.NET Core w usłudze Azure App Service
author: bradygaster
description: Ten artykuł zawiera łącza do hosta platformy Azure i wdrażania zasobów.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665145"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Wdrażanie aplikacji ASP.NET Core w usłudze Azure App Service

[Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) to [usługa platformy przetwarzania w chmurze firmy Microsoft](https://azure.microsoft.com/) do obsługi aplikacji sieci Web, w tym ASP.NET Core.

## <a name="useful-resources"></a>Przydatne zasoby

[Dokumentacja usługi app service](/azure/app-service/) jest domem dla usługi Azure Apps dokumentacji, samouczki, przykłady, poradniki i inne zasoby. Dwa godne uwagi samouczki dotyczące hostingu ASP.NET aplikacji Core to:

[Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Program Visual Studio służy do tworzenia i wdrażania aplikacji sieci web ASP.NET Core w usłudze Azure App Service w systemie Windows.

[Tworzenie aplikacji platformy ASP.NET Core w usłudze App Service dla systemu Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Użyj wiersza polecenia, aby utworzyć i wdrożyć ASP.NET podstawową aplikację sieci web w usłudze Azure App Service w systemie Linux.

Zobacz [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) dla wersji ASP.NET Core dostępnej w usłudze Azure App.

Subskrybuj repozytorium [anonse usługi app service](https://github.com/Azure/app-service-announcements/) i monitoruj problemy. Zespół usługi app service regularnie publikuje anonse i scenariusze przychodzące do usługi App Service.

Następujące artykuły są dostępne w ASP.NET dokumentacji Core:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Dowiedz się, jak opublikować aplikację ASP.NET Core w usłudze Azure App Service za pomocą programu Visual Studio.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Dowiedz się, jak utworzyć aplikację sieci web ASP.NET Core za pomocą programu Visual Studio i wdrożyć ją w usłudze Azure App Service przy użyciu git do ciągłego wdrażania.

[Tworzenie pierwszego potoku](/azure/devops/pipelines/get-started-yaml)  
Skonfiguruj kompilację ciągłej integracji dla aplikacji ASP.NET Core, a następnie utwórz wydanie ciągłego wdrażania usługi Azure App Service.

[Piaskownica aplikacji Azure Web App](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Odkryj ograniczenia wykonywania usługi Azure App Service wymuszane przez platformę Azure Apps.

<xref:test/troubleshoot>  
Zrozumienie i rozwiązywanie problemów z ostrzeżeniami i błędami w projektach ASP.NET Core.

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="platform"></a>Platforma

Architektura platformy (x86/x64) aplikacji usługi App Services jest ustawiona w ustawieniach aplikacji w witrynie Azure Portal dla aplikacji, które są hostowane w warstwie obliczeniowej serii A (Basic) lub wyższej. Upewnij się, że ustawienia publikowania aplikacji (na przykład w profilu publikowania w programie Visual Studio [(pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) są zgodne z ustawieniem w konfiguracji usługi aplikacji w witrynie Azure Portal.

::: moniker range=">= aspnetcore-2.2"

Środowiska wykonawcze dla aplikacji 64-bitowych (x64) i 32-bitowych (x86) są obecne w usłudze Azure App Service. Pakiet [.NET Core SDK](/dotnet/core/sdk) dostępny w usłudze App Service jest 32-bitowy, ale można wdrażać aplikacje 64-bitowe utworzone lokalnie przy użyciu konsoli [Kudu](https://github.com/projectkudu/kudu/wiki) lub procesu publikowania w programie Visual Studio. Aby uzyskać więcej informacji, zobacz [publikowanie i wdrażanie](#publish-and-deploy-the-app) sekcji aplikacji.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

W przypadku aplikacji z natywnymi zależnościami środowiska wykonawcze dla aplikacji 32-bitowych (x86) są obecne w usłudze Azure App Service. Plik [SDK .NET Core](/dotnet/core/sdk) dostępny w usłudze App Service jest 32-bitowy.

::: moniker-end

Aby uzyskać więcej informacji na temat składników frameworka .NET Core i metod dystrybucji, takich jak informacje o czasie wykonywania .NET Core i .NET Core SDK, zobacz [Informacje o rdzeniu net: skład](/dotnet/core/about#composition).

### <a name="packages"></a>Pakiety

Dołącz następujące pakiety NuGet, aby zapewnić funkcje automatycznego rejestrowania dla aplikacji wdrożonych w usłudze Azure App Service:

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) używa [IHostingStartup,](xref:fundamentals/configuration/platform-specific-configuration) aby zapewnić ASP.NET core integracji light-up z usługą Azure App Service. Dodane funkcje rejestrowania są `Microsoft.AspNetCore.AzureAppServicesIntegration` dostarczane przez pakiet.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) wykonuje [AddAzureWebAppDiagnostics,](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) aby dodać dostawców rejestrowania `Microsoft.Extensions.Logging.AzureAppServices` diagnostyki usługi Azure App Service w pakiecie.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) udostępnia implementacje rejestratorów do obsługi dzienników diagnostycznych usługi Azure App Service i funkcji przesyłania strumieniowego dziennika.

Powyższe pakiety nie są dostępne w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Aplikacje, które są przeznaczone `Microsoft.AspNetCore.App` dla platformy .NET Framework lub odwołują się do metapakiety, muszą jawnie odwoływać się do poszczególnych pakietów w pliku projektu aplikacji.

## <a name="override-app-configuration-using-the-azure-portal"></a>Zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal

Ustawienia aplikacji w witrynie Azure Portal umożliwiają ustawianie zmiennych środowiskowych dla aplikacji. Zmienne środowiskowe mogą być używane przez [dostawcę konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Po utworzeniu lub zmodyfikowaniu ustawienia aplikacji w witrynie Azure Portal i wybraniu przycisku **Zapisz** aplikacja Azure zostanie ponownie uruchomiona. Zmienna środowiskowa jest dostępna dla aplikacji po ponownym uruchomieniu usługi.

::: moniker range=">= aspnetcore-3.0"

Gdy aplikacja używa [ogólnego hosta,](xref:fundamentals/host/generic-host)zmienne środowiskowe są ładowane do konfiguracji aplikacji, gdy <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> jest wywoływana do tworzenia hosta. Aby uzyskać więcej <xref:fundamentals/host/generic-host> informacji, zobacz i [dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gdy aplikacja korzysta z [hosta sieci Web,](xref:fundamentals/host/web-host)zmienne środowiskowe <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> są ładowane do konfiguracji aplikacji, gdy jest wywoływana do tworzenia hosta. Aby uzyskać więcej <xref:fundamentals/host/web-host> informacji, zobacz i [dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

[Oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), które konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej podczas hostowania [poza procesem,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)oraz ASP.NET Core Module są skonfigurowane do przesyłania dalej schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

::: moniker range=">= aspnetcore-3.0"

ASP.NET aplikacje Core wdrożone w usłudze App Service automatycznie odbierają rozszerzenie usługi App **Service, ASP.NET integrację rejestrowania rdzeni .** Rozszerzenie umożliwia rejestrowanie integracji dla ASP.NET aplikacji Core w usłudze Azure App Service.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET aplikacje Core wdrożone w usłudze App Service automatycznie otrzymują rozszerzenie usługi App **Service, ASP.NET rozszerzenia rejestrowania rdzenia**. Rozszerzenie umożliwia rejestrowanie integracji dla ASP.NET aplikacji Core w usłudze Azure App Service.

::: moniker-end

Aby uzyskać informacje dotyczące monitorowania, rejestrowania i rozwiązywania problemów, zobacz następujące artykuły:

[Monitorowanie aplikacji w usłudze Azure App Service](/azure/app-service/web-sites-monitor)  
Dowiedz się, jak przeglądać przydziały i metryki dla aplikacji i planów usługi App Service.

[Włączanie rejestrowania diagnostyki dla aplikacji w usłudze Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)  
Dowiedz się, jak włączyć i uzyskać dostęp do rejestrowania diagnostycznego dla kodów stanu HTTP, żądań nie powiodło się i aktywności serwera sieci Web.

<xref:fundamentals/error-handling>  
Zapoznaj się z typowymi metodami obsługi błędów w aplikacjach ASP.NET Core.

<xref:test/troubleshoot-azure-iis>  
Dowiedz się, jak diagnozować problemy z wdrożeniami usługi Azure App Service za pomocą aplikacji ASP.NET Core.

<xref:host-and-deploy/azure-iis-errors-reference>  
Zapoznaj się z typowymi błędami konfiguracji wdrażania aplikacji obsługiwanych przez usługę Azure App Service/usługi IIS, aby uzyskać porady dotyczące rozwiązywania problemów.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Pierścień kluczy ochrony danych i miejsca wdrożenia

[Klucze ochrony danych](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) są zachowywane w folderze *%HOME%\ASP.NET\DataProtection-Keys.* Ten folder jest wspierany przez magazyn sieciowy i jest synchronizowany na wszystkich komputerach hostujących aplikację. Klucze nie są chronione w spoczynku. Ten folder dostarcza pierścień klucza do wszystkich wystąpień aplikacji w jednym miejscu wdrożenia. Oddzielne gniazda wdrażania, takie jak Staging i Production, nie współużytkuje pierścień klucza.

Podczas wymiany między gniazdami wdrażania każdy system korzystający z ochrony danych nie będzie mógł odszyfrować przechowywanych danych przy użyciu pierścienia kluczy wewnątrz poprzedniego gniazda. ASP.NET Cookie Middleware wykorzystuje ochronę danych w celu ochrony swoich plików cookie. Prowadzi to do wylogowania użytkowników z aplikacji korzystającej ze standardowego oprogramowania pośredniczącego ASP.NET plików cookie. W przypadku rozwiązania z pierścieniem kluczy niezależnym od gniazda należy użyć zewnętrznego dostawcy pierścieni kluczy, takiego jak:

* Azure Blob Storage
* W usłudze Azure Key Vault
* Sklep SQL
* Pamięć podręczna Redis

Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a>Wdrażanie aplikacji ASP.NET Core korzystającej z wersji zapoznawczej .NET Core

Aby wdrożyć aplikację, która używa wersji zapoznawczej programu .NET Core, zobacz następujące zasoby. Te podejścia są również używane, gdy środowisko wykonawcze jest dostępne, ale zestaw SDK nie został zainstalowany w usłudze Azure App Service.

* [Określ wersję SDK rdzenia platformy .NET przy użyciu potoków platformy Azure](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Wdrażanie samodzielnej aplikacji w wersji zapoznawczej](#deploy-a-self-contained-preview-app)
* [Używanie platformy Docker z aplikacjami sieci Web dla kontenerów](#use-docker-with-web-apps-for-containers)
* [Instalowanie rozszerzenia witryny w wersji zapoznawczej](#install-the-preview-site-extension)

Zobacz [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) dla wersji ASP.NET Core dostępnej w usłudze Azure App.

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Określ wersję SDK rdzenia platformy .NET przy użyciu potoków platformy Azure

Użyj [scenariuszy ciągłej integracji/dysku CD usługi Azure App Service,](/azure/app-service/deploy-continuous-deployment) aby skonfigurować kompilację ciągłej integracji z platformą Azure DevOps. Po utworzeniu kompilacji azure devops opcjonalnie skonfiguruj kompilację, aby używała określonej wersji SDK. 

#### <a name="specify-the-net-core-sdk-version"></a>Określ wersję SDK rdzenia .NET

Podczas tworzenia kompilacji usługi Azure DevOps przy użyciu centrum wdrażania `Restore`usługi `Build` `Test`App `Publish`Service domyślny potok kompilacji zawiera kroki dla , , i . Aby określić wersję SDK, wybierz przycisk **Dodaj (+)** na liście zadań agenta, aby dodać nowy krok. Wyszukaj **.NET Core SDK** na pasku wyszukiwania. 

![Dodawanie kroku net core sdk](index/add-sdk-step.png)

Przenieś krok do pierwszej pozycji w kompilacji, tak aby kroki po nim używać określonej wersji .NET Core SDK. Określ wersję sdk .NET Core. W tym przykładzie zestaw SDK jest ustawiony na `3.0.100`.

![Ukończony krok SDK](index/sdk-step-first-place.png)

Aby opublikować [samodzielne wdrożenie (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd)skonfiguruj scd w `Publish` kroku i podaj identyfikator środowiska wykonawczego [(RID)](/dotnet/core/rid-catalog).

![Samodzielne publikowanie](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Wdrażanie samodzielnej aplikacji w wersji zapoznawczej

[Samodzielne wdrożenie (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd) które jest przeznaczone dla środowiska uruchomieniowego w wersji zapoznawczej, przenosi środowisko uruchomieniowe w wersji zapoznawczej we wdrożeniu.

Podczas wdrażania aplikacji niezależnej:

* Witryna w usłudze Azure App Service nie wymaga [rozszerzenia witryny w wersji zapoznawczej.](#install-the-preview-site-extension)
* Aplikacja musi być opublikowana zgodnie z innym podejściem niż podczas publikowania dla [wdrożenia zależnego od struktury (FDD).](/dotnet/core/deploying#framework-dependent-deployments-fdd)

Postępuj zgodnie ze wskazówkami w [sekcji Wdrażanie aplikacji samodzielnej.](#deploy-the-app-self-contained)

### <a name="use-docker-with-web-apps-for-containers"></a>Używanie platformy Docker z aplikacjami sieci Web dla kontenerów

[Centrum platformy Docker](https://hub.docker.com/r/microsoft/aspnetcore/) zawiera najnowsze obrazy platformy Docker w wersji zapoznawczej. Obrazy mogą być używane jako obraz podstawowy. Użyj obrazu i wdrożyć w aplikacjach sieci Web dla kontenerów normalnie.

### <a name="install-the-preview-site-extension"></a>Instalowanie rozszerzenia witryny w wersji zapoznawczej

Jeśli wystąpi problem przy użyciu rozszerzenia witryny w wersji zapoznawczej, otwórz [problem dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).

1. W witrynie Azure Portal przejdź do usługi App Service.
1. Wybierz aplikację internetową.
1. Wpisz "ex" w polu wyszukiwania, aby filtrować "Rozszerzenia" lub przewiń w dół listę narzędzi do zarządzania.
1. Wybierz pozycję **Rozszerzenia**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz z listy rozszerzenie **ASP.NET Core {X.Y} ({x64|x86}),** gdzie `{X.Y}` znajduje się wersja ASP.NET `{x64|x86}` Core preview i określa platformę.
1. Wybierz **przycisk OK,** aby zaakceptować warunki prawne.
1. Wybierz **przycisk OK,** aby zainstalować rozszerzenie.

Po zakończeniu operacji zostanie zainstalowany najnowszy podgląd rdzenia .NET. Sprawdź instalację:

1. Wybierz opcję **Narzędzia zaawansowane**.
1. Wybierz **pozycję Przejdź** w obszarze Narzędzia **zaawansowane**.
1. Wybierz element menu >  **konsoli debugowania**programu**PowerShell.**
1. W wierszu programu PowerShell wykonaj następujące polecenie. Zastąp ASP.NET core wersji `{X.Y}` środowiska uruchomieniowego i platformy w `{PLATFORM}` poleceniu:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   Polecenie zwraca `True` po zainstalowaniu środowiska wykonawczego podglądu x64.

> [!NOTE]
> Architektura platformy (x86/x64) aplikacji usługi App Services jest ustawiona w ustawieniach aplikacji w witrynie Azure Portal dla aplikacji, które są hostowane w warstwie obliczeniowej serii A (Basic) lub wyższej. Upewnij się, że ustawienia publikowania aplikacji (na przykład w profilu publikowania w programie Visual Studio [(pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) są zgodne z ustawieniem w konfiguracji usługi aplikacji w witrynie Azure portal.
>
> Jeśli aplikacja jest uruchamiana w trybie w toku, a architektura platformy jest skonfigurowana dla 64-bitowych (x64), ASP.NET Core Module używa 64-bitowego środowiska wykonawczego podglądu, jeśli jest obecny. Zainstaluj rozszerzenie **środowiska wykonawczego ASP.NET Core {X.Y} (x64)** przy użyciu witryny Azure Portal.
>
> Po zainstalowaniu środowiska wykonawczego w wersji zapoznawczej x64 uruchom następujące polecenie w oknie polecenia programu Azure Kudu PowerShell, aby zweryfikować instalację. Zastąp ASP.NET core wersji `{X.Y}` środowiska uruchomieniowego w następującym poleceniu:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> Polecenie zwraca `True` po zainstalowaniu środowiska wykonawczego podglądu x64.

> [!NOTE]
> **ASP.NET rozszerzenia core** umożliwia dodatkowe funkcje dla ASP.NET Core w usłudze Azure App Services, takie jak włączanie rejestrowania platformy Azure. Rozszerzenie jest instalowane automatycznie podczas wdrażania z programu Visual Studio. Jeśli rozszerzenie nie jest zainstalowane, zainstaluj je dla aplikacji.

**Używanie rozszerzenia witryny podglądu z szablonem ARM**

Jeśli szablon ARM jest używany do tworzenia `siteextensions` i wdrażania aplikacji, typ zasobu może służyć do dodawania rozszerzenia witryny do aplikacji sieci web. Przykład:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Publikowanie i wdrażanie aplikacji

::: moniker range=">= aspnetcore-2.2"

W przypadku wdrożenia 64-bitowego:

* Użyj 64-bitowego sdk .NET Core do tworzenia aplikacji 64-bitowej.
* Ustaw **platformę** na **64 bit** w**ustawieniach ogólnych** **konfiguracji** > usługi app service . Aplikacja musi używać planu usługi Basic lub wyższej, aby umożliwić wybór bitowości platformy.

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a>Wdrażanie aplikacji zależne od struktury aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Wybierz **polecenie Buduj** > **publikuj {Nazwa aplikacji}** na pasku narzędzi programu Visual Studio lub kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Publikuj**.
1. W oknie **dialogowym Wybierz miejsce docelowe publikowania** upewnij się, że usługa **App Service** jest zaznaczona.
1. Wybierz **pozycję Zaawansowane**. Zostanie otwarte okno dialogowe **Publikowanie.**
1. W oknie dialogowym **Publikowanie:**
   * Upewnij się, że wybrano **konfigurację wydania.**
   * Otwórz listę rozwijaną **Tryb wdrażania** i wybierz opcję **Zależne od struktury**.
   * Wybierz **pozycję Przenośny** jako **docelowy czas pracy**.
   * Jeśli chcesz usunąć dodatkowe pliki po wdrożeniu, otwórz **opcje publikowania plików** i zaznacz pole wyboru, aby usunąć dodatkowe pliki w miejscu docelowym.
   * Wybierz **pozycję Zapisz**.
1. Utwórz nową witrynę lub zaktualizuj istniejącą witrynę, wykonując pozostałe monity kreatora publikowania.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

1. W pliku projektu nie określaj [identyfikatora środowiska uruchomieniowego (RID)](/dotnet/core/rid-catalog).

1. W powłoce poleceń opublikuj aplikację w konfiguracji wydania za pomocą polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish) W poniższym przykładzie aplikacja jest publikowana jako aplikacja zależna od struktury:

   ```console
   dotnet publish --configuration Release
   ```

1. Przenieś zawartość *katalogu bin/release/{TARGET FRAMEWORK}/publish* do witryny w usłudze App Service. Jeśli przeciągniesz zawartość folderu *publikowania* z lokalnego dysku twardego lub udziału sieciowego bezpośrednio `D:\home\site\wwwroot` do usługi App Service w konsoli [Kudu,](https://github.com/projectkudu/kudu/wiki) przeciągnij pliki do folderu w konsoli Kudu.

---

### <a name="deploy-the-app-self-contained"></a>Wdrażanie aplikacji niezależnej

Użyj programu Visual Studio lub interfejsu wiersza polecenia .NET Core dla [samodzielnego wdrożenia (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Wybierz **polecenie Buduj** > **publikuj {Nazwa aplikacji}** na pasku narzędzi programu Visual Studio lub kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Publikuj**.
1. W oknie **dialogowym Wybierz miejsce docelowe publikowania** upewnij się, że usługa **App Service** jest zaznaczona.
1. Wybierz **pozycję Zaawansowane**. Zostanie otwarte okno dialogowe **Publikowanie.**
1. W oknie dialogowym **Publikowanie:**
   * Upewnij się, że wybrano **konfigurację wydania.**
   * Otwórz listę rozwijaną **Tryb wdrażania** i wybierz opcję **Samodzielny**.
   * Wybierz docelowy czas wykonywania z listy rozwijanej **Docelowy czas wykonywania.** Wartość domyślna to `win-x86`.
   * Jeśli chcesz usunąć dodatkowe pliki po wdrożeniu, otwórz **opcje publikowania plików** i zaznacz pole wyboru, aby usunąć dodatkowe pliki w miejscu docelowym.
   * Wybierz **pozycję Zapisz**.
1. Utwórz nową witrynę lub zaktualizuj istniejącą witrynę, wykonując pozostałe monity kreatora publikowania.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

1. W pliku projektu określ jeden lub więcej [identyfikatorów środowiska uruchomieniowego (RID)](/dotnet/core/rid-catalog). Użyj `<RuntimeIdentifier>` (liczby pojedynczej) dla `<RuntimeIdentifiers>` pojedynczego rid lub użyj (liczba mnoga), aby zapewnić listę identyfikatorów RID rozdzielanych średnikami. W poniższym przykładzie `win-x86` określono identyfikator RID:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. W powłoce poleceń opublikuj aplikację w konfiguracji wydania dla środowiska wykonawczego hosta za pomocą polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish) W poniższym przykładzie aplikacja jest `win-x86` publikowana dla rid. Identyfikator RID podany `--runtime` do opcji musi `<RuntimeIdentifier>` być `<RuntimeIdentifiers>`podany we właściwości (lub) w pliku projektu.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. Przenieś zawartość *katalogu bin/release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory do witryny w usłudze App Service. Jeśli przeciągniesz zawartość folderu *publikowania* z lokalnego dysku twardego lub udziału sieciowego bezpośrednio `D:\home\site\wwwroot` do usługi App Service w konsoli Kudu, przeciągnij pliki do folderu w konsoli Kudu.

---

## <a name="protocol-settings-https"></a>Ustawienia protokołu (HTTPS)

Powiązania bezpiecznego protokołu umożliwiają określenie certyfikatu, który ma być używany podczas odpowiadania na żądania za pośrednictwem protokołu HTTPS. Powiązanie wymaga prawidłowego certyfikatu prywatnego (*.pfx*) wystawionego dla określonej nazwy hosta. Aby uzyskać więcej informacji, zobacz [Samouczek: Powiąż istniejący niestandardowy certyfikat SSL z usługą Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Jeśli chcesz przekształcić *web.config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na <xref:host-and-deploy/iis/transform-webconfig>podstawie konfiguracji, profilu lub środowiska), zobacz .

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Omówienie usługi App Service](/azure/app-service/app-service-web-overview)
* [Usługa Azure App Service: najlepsze miejsce do hostowania aplikacji .NET (55-minutowy klip wideo z omówieniem)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Usługa Azure Friday: Środowisko diagnostyki i rozwiązywania problemów usługi Azure App Service (12-minutowy film wideo)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Omówienie diagnostyki usługi Azure App Service](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Usługa Azure App Service w systemie Windows Server korzysta z [internetowych usług informacyjnych (IIS).](https://www.iis.net/) Następujące tematy odnoszą się do podstawowej technologii IIS:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server — zawartość administratora IT dla bieżących i poprzednich wersji](/windows-server/windows-server-versions)

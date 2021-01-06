---
title: ASP.NET Core hosta w kolektywie serwerów sieci Web
author: rick-anderson
description: Dowiedz się, jak hostować wiele wystąpień aplikacji ASP.NET Core z zasobami udostępnionymi w środowisku kolektywu serwerów sieci Web.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/web-farm
ms.openlocfilehash: ee78e80a4eda3089943765700aa6bb62c6c1e07d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057521"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>ASP.NET Core hosta w kolektywie serwerów sieci Web

[Krzysztof Ross](https://github.com/Tratcher)

*Farma sieci Web* jest grupą zawierającą co najmniej dwa serwery sieci Web (lub *węzły*), które obsługują wiele wystąpień aplikacji. Gdy żądania użytkowników docierają do kolektywu serwerów sieci Web, *moduł równoważenia obciążenia* dystrybuuje żądania do węzłów kolektywu serwerów sieci Web. Usprawnienia farmy sieci Web:

* **Niezawodność/dostępność**: Jeśli co najmniej jeden węzeł ulegnie awarii, moduł równoważenia obciążenia może kierować żądania do innych działających węzłów, aby kontynuować przetwarzanie żądań.
* **Pojemność/wydajność**: wiele węzłów może przetwarzać więcej żądań niż pojedynczy serwer. Moduł równoważenia obciążenia równoważy obciążenie przez dystrybuowanie żądań do węzłów.
* **Skalowalność**: gdy wymagana jest większa lub mniejsza pojemność, można zwiększyć lub zmniejszyć liczbę aktywnych węzłów w celu dopasowania do obciążenia. Technologie platformy farmy sieci Web, takie jak [Azure App Service](https://azure.microsoft.com/services/app-service/), mogą automatycznie dodawać lub usuwać węzły na żądanie administratora systemu lub automatycznie bez udziału człowieka.
* **Łatwość konserwacji**: węzły kolektywu serwerów sieci Web mogą polegać na zestawie usług udostępnionych, co ułatwia zarządzanie systemem. Na przykład węzły kolektywu serwerów sieci Web mogą polegać na jednym serwerze bazy danych i wspólnej lokalizacji sieciowej dla zasobów statycznych, takich jak obrazy i pliki do pobrania.

W tym temacie opisano konfigurację i zależności dla aplikacji ASP.NET Core hostowanych w kolektywie serwerów sieci Web, które są zależne od udostępnionych zasobów.

## <a name="general-configuration"></a>Konfiguracja ogólna

<xref:host-and-deploy/index>  
Dowiedz się, jak konfigurować środowiska hostingu i wdrażać ASP.NET Core aplikacje. Skonfiguruj Menedżera procesów na każdym węźle kolektywu serwerów sieci Web, aby zautomatyzować uruchamianie i ponowne uruchamianie aplikacji. Każdy węzeł wymaga środowiska uruchomieniowego ASP.NET Core. Aby uzyskać więcej informacji, zobacz tematy w obszarze [host i wdrażanie](xref:host-and-deploy/index) w dokumentacji.

<xref:host-and-deploy/proxy-load-balancer>  
Dowiedz się więcej o konfigurowaniu aplikacji hostowanych za serwerami proxy i usługami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniu.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) to [usługa platformy obliczeniowej w chmurze firmy Microsoft](https://azure.microsoft.com/) do hostowania aplikacji sieci web, w tym ASP.NET Core. App Service to w pełni zarządzana platforma, która zapewnia automatyczne skalowanie, równoważenie obciążenia, stosowanie poprawek i ciągłe wdrażanie.

## <a name="app-data"></a>Dane aplikacji

Gdy aplikacja jest skalowana w wielu wystąpieniach, może być stan aplikacji, który wymaga udostępniania między węzłami. Jeśli stan jest przejściowy, rozważ udostępnienie [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Jeśli współużytkowany stan wymaga trwałości, rozważ zapisanie stanu udostępnionego w bazie danych.

## <a name="required-configuration"></a>Wymagana konfiguracja

Ochrona danych i buforowanie wymagają konfiguracji aplikacji wdrożonych w kolektywie serwerów sieci Web.

### <a name="data-protection"></a>Ochrona danych

[System ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez aplikacje do ochrony danych. Ochrona danych opiera się na zestawie kluczy kryptograficznych przechowywanych w *pęku kluczy*. Gdy system ochrony danych jest zainicjowany, stosuje [domyślne ustawienia](xref:security/data-protection/configuration/default-settings) , które przechowują pierścień kluczy lokalnie. W ramach konfiguracji domyślnej unikatowy pierścień kluczy jest przechowywany w każdym węźle kolektywu serwerów sieci Web. W związku z tym każdy węzeł kolektywu serwerów sieci Web nie może odszyfrować danych szyfrowanych przez aplikację w żadnym innym węźle. Konfiguracja domyślna nie jest zazwyczaj odpowiednia do hostowania aplikacji w kolektywie serwerów sieci Web. Alternatywą dla implementacji pierścienia klucza współdzielonego jest zawsze kierowanie żądań użytkowników do tego samego węzła. Aby uzyskać więcej informacji na temat konfiguracji systemu ochrony danych dla wdrożeń farmy sieci Web, zobacz <xref:security/data-protection/configuration/overview> .

### <a name="caching"></a>Buforowanie

W środowisku kolektywu serwerów sieci Web mechanizm buforowania musi współdzielić elementy w pamięci podręcznej w węzłach kolektywu serwerów sieci Web. Buforowanie musi być zależne od typowej pamięci podręcznej Redis, udostępnionej bazy danych SQL Server lub niestandardowej implementacji buforowania, która udostępnia elementy w pamięci podręcznej w ramach kolektywu serwerów sieci Web. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Składniki zależne

Poniższe scenariusze nie wymagają dodatkowej konfiguracji, ale zależą od technologii, które wymagają konfiguracji farmy serwerów sieci Web.

| Scenariusz | Zależy od &hellip; |
| -------- | ------------------- |
| Authentication | Ochrona danych (zobacz <xref:security/data-protection/configuration/overview> ).<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/authentication/cookie> i <xref:security/cookie-sharing>. |
| Identity | Konfiguracja uwierzytelniania i bazy danych.<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>. |
| Sesja | Ochrona danych (zaszyfrowane cookie s) (zobacz <xref:security/data-protection/configuration/overview> ) i buforowanie (zobacz <xref:performance/caching/distributed> ).<br><br>Aby uzyskać więcej informacji, zobacz [Zarządzanie sesjami i Stanami: stan sesji](xref:fundamentals/app-state#session-state). |
| TempData | Ochrona danych (zaszyfrowana cookie s) (zobacz <xref:security/data-protection/configuration/overview> ) lub sesja (zobacz temat [Zarządzanie sesjami i Stanami: stan sesji](xref:fundamentals/app-state#session-state)).<br><br>Aby uzyskać więcej informacji, zobacz temat [Zarządzanie sesjami i Stanami: TempData](xref:fundamentals/app-state#tempdata). |
| Ochrona przed fałszowaniem | Ochrona danych (zobacz <xref:security/data-protection/configuration/overview> ).<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="data-protection-and-caching"></a>Ochrona i buforowanie danych

Gdy ochrona danych lub buforowanie nie jest skonfigurowane dla środowiska kolektywu serwerów sieci Web, podczas przetwarzania żądań występują sporadyczne błędy. Dzieje się tak, ponieważ węzły nie współdzielą tych samych zasobów i żądania użytkownika nie zawsze są kierowane do tego samego węzła.

Rozważ użytkownikowi, który zaloguje się do aplikacji przy użyciu cookie uwierzytelniania. Użytkownik loguje się do aplikacji w jednym węźle kolektywu serwerów sieci Web. Jeśli następne żądanie zostanie odebrane w tym samym węźle, na którym się zalogowano, aplikacja będzie mogła odszyfrować uwierzytelnianie cookie i zezwala na dostęp do zasobu aplikacji. Jeśli następne żądanie zostanie odebrane w innym węźle, aplikacja nie może odszyfrować uwierzytelniania cookie z węzła, w którym zalogowany jest użytkownik, a autoryzacja dla żądanego zasobu kończy się niepowodzeniem.

Gdy którykolwiek z następujących objawów występuje **sporadycznie**, problem zwykle jest śledzony do nieprawidłowej ochrony danych lub konfiguracji buforowania dla środowiska farmy sieci Web:

* Przerwy uwierzytelniania: uwierzytelnianie jest błędnie cookie skonfigurowane lub nie można go odszyfrować. Logowanie OAuth (Facebook, Microsoft, Twitter) lub OpenIdConnect kończy się niepowodzeniem z błędem "korelacja nie powiodła się".
* Przerwy autoryzacji: Identity zostały utracone.
* Stan sesji utraci dane.
* Wyznikane elementy w pamięci podręcznej.
* TempData kończy się niepowodzeniem.
* Wpisy nie powiodły się: sprawdzenie ochrony przed fałszerstwem nie powiedzie się.

Aby uzyskać więcej informacji na temat konfiguracji ochrony danych dla wdrożeń farmy sieci Web, zobacz <xref:security/data-protection/configuration/overview> . Aby uzyskać więcej informacji na temat buforowania konfiguracji dla wdrożeń farmy sieci Web, zobacz <xref:performance/caching/distributed> .

## <a name="obtain-data-from-apps"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacje kolektywu serwerów sieci Web mogą odpowiadać na żądania, uzyskiwać żądania, połączenia i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania terminala. Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:test/troubleshoot#obtain-data-from-an-app> .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Rozszerzenie niestandardowego skryptu dla systemu Windows](/azure/virtual-machines/extensions/custom-script-windows): Pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure, co jest przydatne w przypadku konfiguracji po wdrożeniu i instalacji oprogramowania.
* <xref:host-and-deploy/proxy-load-balancer>
 
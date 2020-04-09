---
title: Host ASP.NET Core w farmie internetowej
author: rick-anderson
description: Dowiedz się, jak hostować wiele wystąpień aplikacji ASP.NET Core z zasobami udostępnionymi w środowisku farmy sieci web.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/web-farm
ms.openlocfilehash: 316c87e5f49593c05991a94cbe5e55d175a49bb3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659370"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Host ASP.NET Core w farmie internetowej

Przez [Chris Ross](https://github.com/Tratcher)

*Farma internetowa* to grupa dwóch lub więcej serwerów sieci web (lub *węzłów),* które obsługują wiele wystąpień aplikacji. Gdy żądania od użytkowników docierają do farmy sieci web, *moduł równoważenia obciążenia* dystrybuuje żądania do węzłów farmy sieci web. Farmy internetowe poprawiają:

* **Niezawodność/dostępność** &ndash; Gdy jeden lub więcej węzłów zakończy się niepowodzeniem, moduł równoważenia obciążenia może kierować żądania do innych działających węzłów, aby kontynuować przetwarzanie żądań.
* **Pojemność/wydajność** &ndash; Wiele węzłów może przetwarzać więcej żądań niż jeden serwer. Moduł równoważenia obciążenia równoważy obciążenie, rozdzielając żądania do węzłów.
* **Skalowalność** &ndash; Gdy wymagana jest większa lub mniejsza pojemność, liczbę aktywnych węzłów można zwiększyć lub zmniejszyć, aby dopasować obciążenie. Technologie platformy farmy sieci Web, takie jak [usługa Azure App Service,](https://azure.microsoft.com/services/app-service/)mogą automatycznie dodawać lub usuwać węzły na żądanie administratora systemu lub automatycznie bez interwencji człowieka.
* **Węzły łatwości** &ndash; konserwacji farmy sieci web mogą polegać na zestawie usług udostępnionych, co ułatwia zarządzanie systemem. Na przykład węzły farmy sieci web mogą polegać na jednym serwerze bazy danych i wspólnej lokalizacji sieciowej dla zasobów statycznych, takich jak obrazy i pliki do pobrania.

W tym temacie opisano konfigurację i zależności dla ASP.NET podstawowych aplikacji hostowanych w farmie sieci web, które opierają się na zasobach udostępnionych.

## <a name="general-configuration"></a>Konfiguracja ogólna

<xref:host-and-deploy/index>  
Dowiedz się, jak skonfigurować środowiska hostingowe i wdrożyć ASP.NET aplikacje Core. Skonfiguruj menedżera procesów w każdym węźle farmy sieci web, aby zautomatyzować uruchamianie i ponowne uruchamianie aplikacji. Każdy węzeł wymaga ASP.NET core środowiska uruchomieniowego. Aby uzyskać więcej informacji, zobacz tematy w [obszarze Host i wdrażanie](xref:host-and-deploy/index) dokumentacji.

<xref:host-and-deploy/proxy-load-balancer>  
Dowiedz się więcej o konfiguracji aplikacji hostowanych za serwerami proxy i modułami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniach.

<xref:host-and-deploy/azure-apps/index>  
[Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) to [usługa platformy przetwarzania w chmurze firmy Microsoft](https://azure.microsoft.com/) do obsługi aplikacji sieci Web, w tym ASP.NET Core. Usługa App Service to w pełni zarządzana platforma, która zapewnia automatyczne skalowanie, równoważenie obciążenia, wprowadzanie poprawek i ciągłe wdrażanie.

## <a name="app-data"></a>Dane aplikacji

Gdy aplikacja jest skalowana do wielu wystąpień, może istnieć stan aplikacji, który wymaga udostępniania w węzłach. Jeśli stan jest przejściowy, należy rozważyć udostępnienie [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Jeśli stan udostępniony wymaga trwałości, należy rozważyć przechowywanie stanu udostępnionego w bazie danych.

## <a name="required-configuration"></a>Wymagana konfiguracja

Ochrona danych i buforowanie wymagają konfiguracji dla aplikacji wdrożonych w farmie sieci web.

### <a name="data-protection"></a>Ochrona danych

[System ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez aplikacje do ochrony danych. Ochrona danych opiera się na zestawie kluczy kryptograficznych przechowywanych w *pierścieniu kluczy.* Po zainicjowaniu systemu ochrony danych stosuje [domyślne ustawienia,](xref:security/data-protection/configuration/default-settings) które przechowują pierścień klucza lokalnie. W ramach konfiguracji domyślnej unikatowy pierścień klucza jest przechowywany w każdym węźle farmy sieci web. W związku z tym każdy węzeł farmy sieci web nie można odszyfrować danych, które są szyfrowane przez aplikację w innym węźle. Domyślna konfiguracja nie jest ogólnie odpowiednia do hostowania aplikacji w farmie sieci web. Alternatywą dla implementowania pierścienia klucza udostępnionego jest zawsze kierowanie żądań użytkowników do tego samego węzła. Aby uzyskać więcej informacji na temat konfiguracji systemu <xref:security/data-protection/configuration/overview>ochrony danych dla wdrożeń farmy sieci Web, zobacz .

### <a name="caching"></a>Buforowanie

W środowisku farmy sieci web mechanizm buforowania musi współużytkować elementy buforowane w węzłach farmy sieci web. Buforowanie musi polegać na wspólnej pamięci podręcznej Redis, udostępnionej bazie danych programu SQL Server lub niestandardowej implementacji buforowania, która udostępnia buforowane elementy w farmie sieci web. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Komponenty zależne

Poniższe scenariusze nie wymagają dodatkowej konfiguracji, ale zależą od technologii, które wymagają konfiguracji dla farm sieci web.

| Scenariusz | Zależy&hellip; |
| -------- | ------------------- |
| Authentication | Ochrony Danych <xref:security/data-protection/configuration/overview>(patrz ).<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/authentication/cookie> i <xref:security/cookie-sharing>. |
| Tożsamość | Uwierzytelnianie i konfiguracja bazy danych.<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>. |
| Sesja | Ochrona danych (zaszyfrowane pliki <xref:security/data-protection/configuration/overview>cookie) (patrz) <xref:performance/caching/distributed>i buforowanie (patrz ).<br><br>Aby uzyskać więcej informacji, zobacz [Zarządzanie sesjami i stanami: Stan sesji](xref:fundamentals/app-state#session-state). |
| TempData ( TempData ) | Ochrona danych (zaszyfrowane pliki <xref:security/data-protection/configuration/overview>cookie) (patrz ) lub Sesja (zobacz [zarządzanie sesją i stanem: Stan sesji](xref:fundamentals/app-state#session-state)).<br><br>Aby uzyskać więcej informacji, zobacz [Zarządzanie sesjami i stanami: TempData](xref:fundamentals/app-state#tempdata). |
| Przeciwdziałanie fałszerskości | Ochrony Danych <xref:security/data-protection/configuration/overview>(patrz ).<br><br>Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="data-protection-and-caching"></a>Ochrona danych i buforowanie

Gdy ochrona danych lub buforowanie nie jest skonfigurowany dla środowiska farmy sieci web, sporadyczne błędy występują podczas przetwarzania żądań. Dzieje się tak, ponieważ węzły nie współużytkować te same zasoby i żądania użytkowników nie zawsze są kierowane z powrotem do tego samego węzła.

Należy wziąć pod uwagę użytkownika, który loguje się do aplikacji przy użyciu uwierzytelniania plików cookie. Użytkownik loguje się do aplikacji w jednym węźle farmy sieci Web. Jeśli ich następne żądanie dociera do tego samego węzła, w którym się zalogowali, aplikacja może odszyfrować plik cookie uwierzytelniania i umożliwia dostęp do zasobu aplikacji. Jeśli ich następne żądanie dociera do innego węzła, aplikacja nie może odszyfrować pliku cookie uwierzytelniania z węzła, w którym użytkownik się zalogował, a autoryzacja żądanego zasobu nie powiedzie się.

Gdy którykolwiek z następujących objawów występuje **sporadycznie,** problem jest zwykle śledzone do niewłaściwej ochrony danych lub konfiguracji buforowania dla środowiska farmy sieci web:

* &ndash; Podziały uwierzytelniania Plik cookie uwierzytelniania jest nieprawidłowo skonfigurowany lub nie można go odszyfrować. Logowania OAuth (Facebook, Microsoft, Twitter) lub OpenIdConnect nie powiodą się z błędem "Korelacja nie powiodła się".
* Autoryzacja &ndash; przerwy Tożsamość jest tracona.
* Stan sesji traci dane.
* Elementy w pamięci podręcznej znikają.
* TempData kończy się niepowodzeniem.
* Posts &ndash; nie Sprawdzanie anty-fałszerstwo nie powiedzie się.

Aby uzyskać więcej informacji na temat konfiguracji <xref:security/data-protection/configuration/overview>ochrony danych dla wdrożeń farmy sieci Web, zobacz . Aby uzyskać więcej informacji na temat konfiguracji buforowania dla wdrożeń farmy sieci Web, zobacz <xref:performance/caching/distributed>.

## <a name="obtain-data-from-apps"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacje farmy sieci web są w stanie odpowiadać na żądania, uzyskać żądanie, połączenie i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania pośredniczącego terminalu. Aby uzyskać więcej informacji <xref:test/troubleshoot#obtain-data-from-an-app>i przykładowy kod, zobacz .

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Niestandardowe rozszerzenie skryptów dla systemu Windows](/azure/virtual-machines/extensions/custom-script-windows) &ndash; Pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure, co jest przydatne w przypadku konfiguracji po wdrożeniu i instalacji oprogramowania.
* <xref:host-and-deploy/proxy-load-balancer>
 
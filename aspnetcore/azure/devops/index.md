---
title: Metodyka DevOps z platformami ASP.NET Core i Azure
author: CamSoper
description: Przewodnik, który oferuje kompleksowe wskazówki dotyczące tworzenia potoku DevOps dla aplikacji ASP.NET Core hostowanej na platformie Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: efabfd7fdcd695186207654887292c6b2104342d
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130187"
---
# <a name="devops-with-aspnet-core-and-azure"></a>Metodyka DevOps z platformami ASP.NET Core i Azure

[![Obraz okładki](./media/cover-large.png)](https://aka.ms/devopsbook)

Według [Soper](https://twitter.com/camsoper) i [Scott Addie](https://twitter.com/scottaddie)

Ten przewodnik jest dostępny jako [książka elektroniczna do pobrania](https://aka.ms/devopsbook).

## <a name="welcome"></a>Powitanie 

Witamy w przewodniku po cyklu życia projektowania platformy Azure dla platformy .NET. W tym przewodniku przedstawiono podstawowe pojęcia związane z tworzeniem cyklu życia programowania na platformie Azure przy użyciu narzędzi i procesów platformy .NET. Po zakończeniu tego przewodnika skorzystać zalety DevOps łańcucha narzędzi.

## <a name="who-this-guide-is-for"></a>Dla kogo jest ten przewodnik

Powinien być doświadczonym ASP.NET Core deweloperem (poziom 200-300). Nie musisz niczego wiedzieć o platformie Azure, ponieważ zajmiemy się tym wprowadzeniem. Ten przewodnik może być również przydatny w przypadku inżynierów DevOps, którzy są bardziej ukierunkowani na operacje niż programowanie.

Ten przewodnik jest przeznaczony dla deweloperów systemu Windows. Jednak system Linux i macOS są w pełni obsługiwane przez platformę .NET Core. Aby dostosować ten przewodnik dla systemu Linux/macOS, Obejrzyj objaśnienia dotyczące różnic systemu Linux/macOS.

## <a name="what-this-guide-doesnt-cover"></a>Czym nie obejmuje ten przewodnik

Ten przewodnik koncentruje się na kompleksowym doświadczeniu wdrażania dla deweloperów platformy .NET. Nie jest to wyczerpujący Przewodnik dotyczący wszystkich rzeczy na platformie Azure i nie koncentruje się na interfejsach API platformy .NET dla usług platformy Azure. Ten nacisk dotyczy całej integracji, wdrażania, monitorowania i debugowania. W górnej części przewodnika są oferowane zalecenia dotyczące następnych kroków. Uwzględniono w sugestiach usługi platformy Azure, które są przydatne do ASP.NET Core deweloperów.

## <a name="whats-in-this-guide"></a>Co znajduje się w tym przewodniku

### <a name="tools-and-downloads"></a>[Narzędzia i pliki do pobrania](xref:azure/devops/tools-and-downloads)

Dowiedz się, gdzie uzyskać narzędzia używane w tym przewodniku.

### <a name="deploy-to-app-service"></a>[Wdrażanie w usłudze App Service](xref:azure/devops/deploy-to-app-service)

Poznaj różne metody wdrażania aplikacji ASP.NET Core w Azure App Service.

### <a name="continuous-integration-and-deployment"></a>[Ciągła integracja i ciągłe wdrażanie](xref:azure/devops/cicd)

Twórz kompleksowe rozwiązanie do ciągłej integracji i wdrażania aplikacji ASP.NET Core z usługami GitHub, Azure DevOps Services i Azure.

### <a name="monitor-and-debug"></a>[Monitorowanie i debugowanie](xref:azure/devops/monitor)

Korzystaj z narzędzi platformy Azure do monitorowania, rozwiązywania problemów i dostrajania aplikacji.

### <a name="next-steps"></a>[Następne kroki](xref:azure/devops/next-steps)

Inne ścieżki szkoleniowe dla ASP.NET Core deweloperów uczenie się na platformie Azure.

## <a name="additional-introductory-reading"></a>Dodatkowy odczyt wprowadzający

Jeśli jest to pierwsze narażenie na chmurę obliczeniową, te artykuły wyjaśniają podstawowe informacje.

* [Co to jest chmura obliczeniowa?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Przykłady chmury obliczeniowej](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [Co to jest IaaS?](https://azure.microsoft.com/overview/what-is-iaas/)
* [Co to jest PaaS?](https://azure.microsoft.com/overview/what-is-paas/)

---
title: Metodyka DevOps z platformami ASP.NET Core i Azure
author: CamSoper
description: Przewodnik, który zawiera kompleksowe wskazówki dotyczące tworzenia potoku DevOps dla aplikacji core ASP.NET hostowane na platformie Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
uid: azure/devops/index
ms.openlocfilehash: f45bb2a5dd4b3d1a820085ede7ce3219045ed80b
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658082"
---
# <a name="devops-with-aspnet-core-and-azure"></a>Metodyka DevOps z platformami ASP.NET Core i Azure

[![Obraz okładki](./media/cover-large.png)](https://aka.ms/devopsbook)

Przez [Cam Soper](https://twitter.com/camsoper) i [Scott Addie](https://twitter.com/scottaddie)

Ten przewodnik jest dostępny w formacie [PDF do pobrania.](https://aka.ms/devopsbook)

## <a name="welcome"></a>Powitanie 

Witamy w przewodniku po cyklu życia rozwoju platformy Azure dla platformy .NET! W tym przewodniku przedstawiono podstawowe pojęcia tworzenia cyklu życia rozwoju wokół platformy Azure przy użyciu narzędzi i procesów platformy .NET. Po zakończeniu tego przewodnika będziesz czerpać korzyści z dojrzałego paska narzędzi DevOps.

## <a name="who-this-guide-is-for"></a>Dla kogo przeznaczony jest ten przewodnik

Powinieneś być doświadczonym deweloperem ASP.NET Core (poziom 200-300). Nie musisz wiedzieć nic o platformie Azure, ponieważ omówimy to w tym wstępie. Ten przewodnik może być również przydatny dla inżynierów DevOps, którzy są bardziej skoncentrowani na operacjach niż na rozwoju.

Ten przewodnik jest przeznaczony dla deweloperów systemu Windows. Jednak Linux i macOS są w pełni obsługiwane przez .NET Core. Aby dostosować ten przewodnik dla linuksa / macOS, uważaj na objaśnienia dla różnic w systemie Linux/macOS.

## <a name="what-this-guide-doesnt-cover"></a>Czego nie obejmuje ten przewodnik

Ten przewodnik koncentruje się na kompleksowym środowisko ciągłego wdrażania dla deweloperów platformy .NET. Nie jest wyczerpującym przewodnikiem po wszystkich rzeczach platformy Azure i nie koncentruje się na interfejsach API platformy .NET dla usług platformy Azure. Nacisk kładzie się na ciągłą integrację, wdrażanie, monitorowanie i debugowanie. Pod koniec przewodnika oferowane są zalecenia dotyczące kolejnych kroków. Zawarte w sugestie są usługi platformy Azure platformy, które są przydatne do ASP.NET core deweloperów.

## <a name="whats-in-this-guide"></a>Co jest w tym przewodniku

### <a name="tools-and-downloads"></a>[Narzędzia i pliki do pobrania](xref:azure/devops/tools-and-downloads)

Dowiedz się, gdzie można uzyskać narzędzia użyte w tym przewodniku.

### <a name="deploy-to-app-service"></a>[Wdrażanie do usługi App Service](xref:azure/devops/deploy-to-app-service)

Poznaj różne metody wdrażania aplikacji ASP.NET Core w usłudze Azure App Service.

### <a name="continuous-integration-and-deployment"></a>[Ciągła integracja i ciągłe wdrażanie](xref:azure/devops/cicd)

Stwórz kompleksowe rozwiązanie do ciągłej integracji i wdrażania aplikacji ASP.NET Core za pomocą usługi GitHub, usługi Azure DevOps i platformy Azure.

### <a name="monitor-and-debug"></a>[Monitorowanie i debugowanie](xref:azure/devops/monitor)

Użyj narzędzi platformy Azure do monitorowania, rozwiązywania problemów i dostrajania aplikacji.

### <a name="next-steps"></a>[Następne kroki](xref:azure/devops/next-steps)

Inne ścieżki szkoleniowe dla ASP.NET Core developer learning Azure.

## <a name="additional-introductory-reading"></a>Dodatkowa lektura wprowadzająca

Jeśli jest to twoja pierwsza ekspozycja na chmurę obliczeniową, te artykuły wyjaśniają podstawy.

* [Co to jest przetwarzanie w chmurze?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Przykłady przetwarzania w chmurze](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [Co to jest IaaS?](https://azure.microsoft.com/overview/what-is-iaas/)
* [Co to jest PaaS?](https://azure.microsoft.com/overview/what-is-paas/)

---
title: Publikowanie aplikacji ASP.NET Core SignalR w Azure App Service
author: bradygaster
description: Dowiedz się, jak opublikować SignalR aplikację ASP.NET Core w Azure App Service.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: a5d19c1519c69351605e8da1d8fa70bff784efd4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777192"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>Opublikuj aplikację sygnalizującą ASP.NET Core, aby Azure App Service

Autor [Brady gastera](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) to usługa platformy [obliczeniowej w chmurze firmy Microsoft](https://azure.microsoft.com/) do hostowania aplikacji sieci web, w tym ASP.NET Core.

> [!NOTE]
> Ten artykuł odnosi się do publikowania aplikacji sygnalizującej ASP.NET Core z programu Visual Studio. Aby uzyskać więcej informacji, zobacz [Usługa signaler dla platformy Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Publikowanie aplikacji

W tym artykule opisano Publikowanie przy użyciu narzędzi w programie Visual Studio. Visual Studio Code użytkownicy mogą używać poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure) do publikowania aplikacji na platformie Azure. Aby uzyskać więcej informacji, zobacz temat [publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu narzędzi wiersza polecenia](/azure/app-service/app-service-web-get-started-dotnet).

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.

1. Upewnij się, że w oknie dialogowym **Wybieranie elementu docelowego publikowania** są wybrane **App Service** i **Utwórz nowe** .

1. Wybierz pozycję **Utwórz profil** na liście rozwijanej przycisk **Publikuj** .

   Wprowadź informacje opisane w poniższej tabeli w oknie dialogowym **tworzenie App Service** i wybierz pozycję **Utwórz**.

   | Element               | Opis |
   | ------------------ | ----------- |
   | **Nazwa**           | Unikatowa nazwa aplikacji. |
   | **Subskrypcja**   | Subskrypcja platformy Azure, której używa aplikacja. |
   | **Grupa zasobów** | Grupa powiązanych zasobów, do której należy aplikacja. |
   | **Plan hostingu**   | Plan cenowy dla aplikacji sieci Web. |

1. Wybierz pozycję **Usługa SignalR platformy Azure** na liście rozwijanej**Dodaj** **zależności** > :

   ![Obszar zależności przedstawiający wybór usługi platformy SignalR Azure na liście rozwijanej Dodaj](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. W oknie **dialogowym SignalR usługi platformy Azure** wybierz pozycję **Utwórz nowe wystąpienie SignalR usługi platformy Azure**.

1. Podaj **nazwę**, **grupę zasobów**i **lokalizację**. Wróć do okna **dialogowego SignalR usługi platformy Azure** i wybierz pozycję **Dodaj**.

Program Visual Studio wykonuje następujące zadania:

* Tworzy profil publikacji zawierający ustawienia publikowania.
* Tworzy *aplikację internetową platformy Azure* z podanymi informacjami.
* Publikuje aplikację.
* Uruchamia przeglądarkę, która ładuje aplikację sieci Web.

Format adresu URL aplikacji to `{APP SERVICE NAME}.azurewebsites.net`. Na przykład aplikacja o nazwie `SignalRChatApp` ma adres URL. `https://signalrchatapp.azurewebsites.net`

502,2 Jeśli podczas wdrażania aplikacji przeznaczonej dla wersji zapoznawczej programu .NET Core wystąpi błąd *nieprawidłowej bramy* , zapoznaj się z artykułem [Wdróż ASP.NET Core wersja zapoznawcza, aby Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) rozwiązać ten problem.

## <a name="configure-the-app-in-azure-app-service"></a>Skonfiguruj aplikację w Azure App Service

> [!NOTE]
> *Ta sekcja dotyczy tylko aplikacji, które nie korzystają z SignalR usługi platformy Azure.*
>
> Jeśli aplikacja używa usługi platformy Azure SignalR , App Service nie wymaga konfiguracji koligacji i sieci Web usługi Routing żądań aplikacji opisanych w tej sekcji. Klienci łączą swoje gniazda sieci Web z SignalR usługą platformy Azure, nie bezpośrednio z aplikacją.

W przypadku aplikacji hostowanych bez SignalR usługi Azure enable:

* [Koligacja ARR](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) do kierowania żądań od użytkownika z powrotem do tego samego wystąpienia App Service. Ustawieniem domyślnym jest **włączone**.
* [Gniazda sieci Web](xref:fundamentals/websockets) , aby umożliwić transport gniazd sieci Web. Ustawienie domyślne jest **wyłączone**.

1. W Azure Portal przejdź do aplikacji sieci Web w **App Services**.
1. Otwórz **Configuration** > **Ustawienia ogólne**konfiguracji.
1. Ustaw dla opcji **gniazda sieci Web** wartość **włączone**.
1. Sprawdź, czy **koligacja ARR** jest ustawiona na wartość **włączone**.

## <a name="app-service-plan-limits"></a>Limity planu App Service

Gniazda sieci Web i inne transporty są ograniczone w zależności od wybranego planu App Service. Aby uzyskać więcej informacji, zapoznaj się z sekcją *limity Cloud Services platformy Azure* i *App Service limity* dotyczące [subskrypcji, limitów, przydziałów i ograniczeń usługi](/azure/azure-subscription-service-limits#app-service-limits) Azure.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Co to jest SignalR usługa Azure?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu narzędzi wiersza polecenia](/azure/app-service/app-service-web-get-started-dotnet)
* [Hostowanie i wdrażanie aplikacji ASP.NET Core w wersji zapoznawczej na platformie Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)

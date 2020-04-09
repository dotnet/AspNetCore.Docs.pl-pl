---
title: Publikowanie aplikacji ASP.NET Core na platformie Azure za pomocą kodu programu Visual Studio
author: rick-anderson
description: Dowiedz się, jak opublikować aplikację ASP.NET Core w usłudze Azure App Service przy użyciu kodu programu Visual Studio
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 5f117cb2867a6e7b54269ef39abe819256b429ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242681"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Publikowanie aplikacji ASP.NET Core na platformie Azure za pomocą kodu programu Visual Studio

Przez [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Aby rozwiązać problem z wdrażaniem usługi App Service, zobacz <xref:test/troubleshoot-azure-iis>.

## <a name="intro"></a>Wprowadzenie

W tym samouczku dowiesz się, jak utworzyć ASP.Net podstawową aplikację MVC i wdrożyć ją w programie Visual Studio Code.

## <a name="set-up"></a>Konfiguruj

- Otwórz [bezpłatne konto platformy Azure,](https://azure.microsoft.com/free/dotnet/) jeśli go nie masz.
- Instalowanie [pakietu SDK core platformy .NET](https://dotnet.microsoft.com/download)
- Instalowanie [kodu programu Visual Studio](https://code.visualstudio.com/Download)
  - Instalowanie [rozszerzenia C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) do kodu programu Visual Studio
  - Zainstaluj [rozszerzenie usługi Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) do kodu programu Visual Studio i skonfiguruj je przed kontynuowaniem

## <a name="create-an-aspnet-core-mvc-project"></a>Tworzenie projektu MVC ASP.Net Core

Za pomocą terminala przejdź do folderu, w którego ma być tworzony projekt, i użyj następującego polecenia:

```dotnetcli
dotnet new mvc
```

Struktura folderów będzie podobna do następującej:

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Otwórz go za pomocą programu Visual Studio Code

Po utworzeniu projektu można go otworzyć za pomocą programu Visual Studio Code przy użyciu jednej z poniższych opcji:

### <a name="through-the-command-line"></a>Za pośrednictwem wiersza polecenia

Użyj następującego polecenia w folderze utworzonym w projekcie:

```cmd
> code .
```

Jeśli poniższe polecenie nie działa, sprawdź, czy instalacja jest poprawnie skonfigurowana, odwołując się do [tego łącza](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Za pośrednictwem interfejsu kodu programu Visual Studio

- Otwórz kod programu Visual Studio
- W menu wybierz pozycję`File > Open Folder`
- Wybierz katalog główny folderu utworzonego projektu MVC

Po otwarciu folderu projektu zostanie wyświetlony komunikat informujący, że brakuje wymaganych zasobów do tworzenia i debugowania. Zaakceptuj pomoc, aby je dodać.

![Interfejs kodu programu Visual Studio z załadowanym projektem](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Folder `.vscode` zostanie utworzony w ramach struktury projektu. Będzie zawierać następujące pliki:

```cmd
launch.json
tasks.json
```

Są to pliki narzędziowe ułatwiające tworzenie i debugowanie aplikacji sieci Web .NET Core.

## <a name="run-the-app"></a>Uruchomienie aplikacji

Zanim wdrożymy aplikację na platformie Azure, upewnij się, że działa poprawnie na komputerze lokalnym.

- Naciśnij klawisz F5, aby uruchomić projekt

Aplikacja internetowa zacznie działać na nowej karcie domyślnej przeglądarki. Po jego uruchomieniu można zauważyć ostrzeżenie o ochronie prywatności. Dzieje się tak, ponieważ aplikacja rozpocznie się przy użyciu protokołu HTTP i HTTPS i domyślnie przechodzi do punktu końcowego HTTPS.

![Ostrzeżenie o prywatności podczas debugowania aplikacji lokalnie](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Aby zachować sesję debugowania, kliknij, a następnie kliknij `Advanced` przycisk `Continue to localhost (unsafe)`.

## <a name="generate-the-deployment-package-locally"></a>Generowanie pakietu wdrożeniowego lokalnie

- Otwórz terminal Kodu programu Visual Studio
- Użyj następującego polecenia, `Release` aby wygenerować pakiet do podfolderu o nazwie: `publish`
  - `dotnet publish -c Release -o ./publish`
- Nowy `publish` folder zostanie utworzony w ramach struktury projektu

![Publikowanie struktury folderów](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Publikowanie w usłudze Azure App Service

Korzystając z rozszerzenia usługi Azure App Service dla programu Visual Studio Code, wykonaj poniższe czynności, aby opublikować witrynę sieci Web bezpośrednio w usłudze Azure App Service.

### <a name="if-youre-creating-a-new-web-app"></a>Jeśli tworzysz nową aplikację sieci Web

- Kliknij prawym `publish` przyciskiem myszy folder i wybierz polecenie`Deploy to Web App...`
- Wybierz subskrypcję, którą chcesz utworzyć aplikację sieci Web
- Wybierz pozycję `Create New Web App`
- Wprowadzanie nazwy aplikacji sieci Web

Rozszerzenie utworzy nową aplikację sieci Web i automatycznie rozpocznie wdrażanie pakietu do niego. Po zakończeniu wdrażania kliknij, `Browse Website` aby sprawdzić poprawność wdrożenia.

![Komunikat o pomyślnym wdrożeniu](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Po kliknięciu przycisku `Browse Website`przejdź do niego za pomocą domyślnej przeglądarki:

![Nowa aplikacja Sieci Web została pomyślnie wdrożona](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Jeśli wdrażasz w istniejącej aplikacji sieci Web

- Kliknij prawym `publish` przyciskiem myszy folder i wybierz polecenie`Deploy to Web App...`
- Wybieranie subskrypcji istniejącej aplikacji Sieci Web
- Wybieranie aplikacji sieci Web z listy
- Visual Studio Code zapyta, czy chcesz zastąpić istniejącą zawartość. Kliknij, `Deploy` aby potwierdzić

Rozszerzenie wdroży zaktualizowaną zawartość w aplikacji sieci Web. Po zakończeniu kliknij, `Browse Website` aby sprawdzić poprawność wdrożenia.

![Istniejące aplikacje sieci Web zostały pomyślnie wdrożone](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie pierwszego potoku devops platformy Azure](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Grupy zasobów platformy Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)

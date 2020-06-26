---
title: Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu Visual Studio Code
author: rick-anderson
description: Dowiedz się, jak opublikować aplikację ASP.NET Core do Azure App Service przy użyciu Visual Studio Code
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 43a2c4fed3a8873f64259167d3949d720f2c5dab
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406748"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu Visual Studio Code

Autor [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Aby rozwiązać problem z wdrożeniem App Service, zobacz <xref:test/troubleshoot-azure-iis> .

## <a name="intro"></a>Wprowadzenie

Korzystając z tego samouczka, dowiesz się, jak utworzyć aplikację ASP.Net Core MVC i wdrożyć ją w Visual Studio Code.

## <a name="set-up"></a>Konfiguruj

- Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz.
- Zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download)
- Zainstaluj [Visual Studio Code](https://code.visualstudio.com/Download)
  - Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) , aby Visual Studio Code
  - Zainstaluj [rozszerzenie Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) , aby Visual Studio Code i skonfigurować je przed kontynuowaniem

## <a name="create-an-aspnet-core-mvc-project"></a>Tworzenie projektu ASP.Net Core MVC

Za pomocą terminalu przejdź do folderu, w którym ma zostać utworzony projekt, i użyj następującego polecenia:

```dotnetcli
dotnet new mvc
```

Będziesz mieć strukturę folderów podobną do następującej:

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

## <a name="open-it-with-visual-studio-code"></a>Otwórz za pomocą Visual Studio Code

Po utworzeniu projektu można go otworzyć z Visual Studio Code przy użyciu jednej z poniższych opcji:

### <a name="through-the-command-line"></a>Za pomocą wiersza polecenia

Użyj następującego polecenia w folderze, w którym został utworzony projekt:

```cmd
> code .
```

Jeśli poniższe polecenie nie działa, sprawdź, czy instalacja została prawidłowo skonfigurowana przez odwołanie do [tego linku](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Za za poorednictwem Visual Studio Code interfejsu

- Otwórz Visual Studio Code
- Z menu wybierz pozycję`File > Open Folder`
- Wybierz katalog główny folderu, w którym został utworzony projekt MVC

Po otwarciu folderu projektu zostanie wyświetlony komunikat informujący o braku wymaganych zasobów do skompilowania i debugowania. Zaakceptuj pomoc, aby je dodać.

![Interfejs Visual Studio Code z załadowanym projektem](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

`.vscode`Folder zostanie utworzony w ramach struktury projektu. Będzie zawierać następujące pliki:

```cmd
launch.json
tasks.json
```

Są to pliki narzędzi, które ułatwiają tworzenie i debugowanie aplikacji sieci Web platformy .NET Core.

## <a name="run-the-app"></a>Uruchomienie aplikacji

Przed wdrożeniem aplikacji na platformie Azure upewnij się, że działa ona prawidłowo na komputerze lokalnym.

- Naciśnij klawisz F5, aby uruchomić projekt

Aplikacja sieci Web zacznie działać na nowej karcie domyślnej przeglądarki. Ostrzeżenie o prywatności można zauważyć zaraz po rozpoczęciu. Jest to spowodowane tym, że aplikacja rozpocznie korzystanie z protokołów HTTP i HTTPS i domyślnie przechodzi do punktu końcowego HTTPS.

![Ostrzeżenie dotyczące prywatności podczas lokalnego debugowania aplikacji](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Aby zachować sesję debugowania, kliknij przycisk `Advanced` , a następnie `Continue to localhost (unsafe)` .

## <a name="generate-the-deployment-package-locally"></a>Wygeneruj lokalnie pakiet wdrożeniowy

- Otwórz Visual Studio Code Terminal
- Użyj następującego polecenia, aby wygenerować `Release` pakiet do podfolderu o nazwie `publish` :
  - `dotnet publish -c Release -o ./publish`
- Nowy `publish` folder zostanie utworzony pod strukturą projektu

![Struktura folderu publikowania](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Publikowanie w usłudze Azure App Service

Korzystając z rozszerzenia Azure App Service Visual Studio Code, wykonaj poniższe kroki, aby opublikować witrynę internetową bezpośrednio w Azure App Service.

### <a name="if-youre-creating-a-new-web-app"></a>Jeśli tworzysz nową aplikację sieci Web

- Kliknij prawym przyciskiem myszy `publish` folder, a następnie wybierz pozycję`Deploy to Web App...`
- Wybierz subskrypcję, dla której chcesz utworzyć aplikację sieci Web
- Wybierz pozycję `Create New Web App`
- Wprowadź nazwę aplikacji sieci Web

Rozszerzenie utworzy nową aplikację sieci Web i rozpocznie automatyczne wdrożenie pakietu. Po zakończeniu wdrożenia kliknij, `Browse Website` Aby sprawdzić poprawność wdrożenia.

![Komunikat wdrożenia zakończony pomyślnie](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Po kliknięciu `Browse Website` Przejdź do niego przy użyciu domyślnej przeglądarki:

![Pomyślnie wdrożono nową aplikację sieci Web](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Jeśli wdrażasz w istniejącej aplikacji sieci Web

- Kliknij prawym przyciskiem myszy `publish` folder, a następnie wybierz pozycję`Deploy to Web App...`
- Wybierz subskrypcję istniejącej aplikacji sieci Web
- Wybierz aplikację internetową z listy
- Visual Studio Code będzie pytał, czy chcesz zastąpić istniejącą zawartość. Kliknij `Deploy` , aby potwierdzić

Rozszerzenie spowoduje wdrożenie zaktualizowanej zawartości w aplikacji sieci Web. Po zakończeniu kliknij przycisk, `Browse Website` Aby sprawdzić poprawność wdrożenia.

![Istniejąca aplikacja sieci Web została pomyślnie wdrożona](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie pierwszego potoku usługi Azure DevOps](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Grupa zasobów platformy Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)

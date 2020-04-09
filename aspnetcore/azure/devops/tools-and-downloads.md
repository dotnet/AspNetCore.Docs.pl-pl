---
title: Narzędzia i pliki do pobrania — DevOps z ASP.NET Core i platformą Azure
author: CamSoper
description: Narzędzia i pliki do pobrania wymagane dla devops z ASP.NET Core i platformy Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511148"
---
# <a name="tools-and-downloads"></a>Narzędzia i pliki do pobrania

Platforma Azure ma kilka interfejsów do inicjowania obsługi administracyjnej zasobów i zarządzania nimi, takich jak [witryna Azure portal](https://portal.azure.com), [interfejs wiersza polecenia platformy Azure,](/cli/azure/) [usługa Azure PowerShell,](/powershell/azure/overview) [usługa Azure Cloud Shell](https://shell.azure.com/bash)i program Visual Studio. Ten przewodnik przyjmuje minimalistyczne podejście i używa usługi Azure Cloud Shell, gdy tylko jest to możliwe, aby zmniejszyć wymagane kroki. Jednak witryny Azure portal musi być używany dla niektórych części.

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane są następujące subskrypcje:

* Platforma &mdash; Azure Jeśli nie masz konta, [uzyskaj bezpłatną wersję próbną.](https://azure.microsoft.com/free/)
* Usługi Azure DevOps &mdash; subskrypcji i organizacji Usługi Azure DevOps jest tworzony w rozdziale 4.
* GitHub &mdash; Jeśli nie masz konta, [zarejestruj się za darmo](https://github.com/join).

Wymagane są następujące narzędzia:

* [Git](https://git-scm.com/downloads) &mdash; Podstawowe zrozumienie Git jest zalecane dla tego przewodnika. Przejrzyj [dokumentację Git](https://git-scm.com/doc), w szczególności [git remote](https://git-scm.com/docs/git-remote) i [git push](https://git-scm.com/docs/git-push).
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; w wersji 2.1.300 lub nowszej jest wymagane do tworzenia i uruchamiania przykładowej aplikacji. Jeśli program Visual Studio jest zainstalowany z **wieloplatformowym obciążeniem deweloperskim .NET Core,** pakiet .NET Core SDK jest już zainstalowany.

    Sprawdź instalację sdk .NET Core. Otwórz powłokę polecenia i uruchom następujące polecenie:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Zalecane narzędzia (tylko windows)

* Niezawodne narzędzia platformy Azure programu [Visual Studio](https://visualstudio.microsoft.com)zapewniają interfejs graficzny dla większości funkcji opisanych w tym przewodniku. Każda wersja programu Visual Studio będzie działać, w tym bezpłatna wersja społeczności programu Visual Studio. Samouczki są zapisywane w celu zademonstrowania rozwoju, wdrożenia i devops zarówno z i bez programu Visual Studio.

  Upewnij się, że program Visual Studio ma zainstalowane następujące [obciążenia:](/visualstudio/install/modify-visual-studio)

  * Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
  * Tworzenie aplikacji na platformie Azure
  * Tworzenie aplikacji dla wielu platform w środowisku .NET Core

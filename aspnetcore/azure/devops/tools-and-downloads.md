---
title: Narzędzia i pliki do pobrania — DevOps z ASP.NET Core i Azure
author: CamSoper
description: Narzędzia i materiały do pobrania wymagane do DevOps z usługami ASP.NET Core i Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 3242031f07f3fa344422db4591ea1a244b6723dd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625248"
---
# <a name="tools-and-downloads"></a>Narzędzia i pliki do pobrania

Platforma Azure ma kilka interfejsów do aprowizacji zasobów i zarządzania nimi, takich jak [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)i programu Visual Studio. Ten przewodnik przyjmuje podejście minimalistyczny i używa Azure Cloud Shell, gdy jest to możliwe, aby zmniejszyć wymaganą procedurę. Jednak Azure Portal muszą być używane w niektórych częściach.

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane są następujące subskrypcje:

* &mdash;Jeśli nie masz konta, możesz [skorzystać z bezpłatnej wersji próbnej](https://azure.microsoft.com/free/dotnet/).
* Azure DevOps Services &mdash; subskrypcję platformy Azure DevOps i organizacja jest tworzona w rozdziale 4.
* GitHub &mdash; , jeśli nie masz konta, [zarejestruj się bezpłatnie](https://github.com/join).

Wymagane są następujące narzędzia:

* Narzędzie [git](https://git-scm.com/downloads) &mdash; W tym przewodniku zalecamy podstawową wiedzę na temat usługi git. Przejrzyj [dokumentację usługi git](https://git-scm.com/doc), w tym [narzędzia Git Remote](https://git-scm.com/docs/git-remote) i [git push](https://git-scm.com/docs/git-push).
* [Zestaw .NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Do kompilowania i uruchamiania przykładowej aplikacji wymagana jest wersja 2.1.300 lub nowsza. Jeśli program Visual Studio jest zainstalowany z obciążeniem **programistycznym dla wielu platform .NET Core** , zestaw .NET Core SDK jest już zainstalowany.

    Sprawdź poprawność instalacji zestaw .NET Core SDK. Otwórz powłokę poleceń i uruchom następujące polecenie:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Zalecane narzędzia (tylko system Windows)

* Zaawansowane narzędzia platformy Azure dla [programu Visual Studio](https://visualstudio.microsoft.com)zapewniają graficzny interfejs użytkownika dla większości funkcji opisanych w tym przewodniku. Będzie można korzystać z dowolnej wersji programu Visual Studio, w tym bezpłatnej wersji programu Visual Studio Community Edition. Samouczki są zapisywane w taki sposób, aby demonstrować programowanie, wdrażanie i DevOps zarówno z programem Visual Studio, jak i bez niego.

  Upewnij się, że program Visual Studio ma zainstalowane następujące [obciążenia](/visualstudio/install/modify-visual-studio) :

  * Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
  * Tworzenie aplikacji na platformie Azure
  * Programowanie dla wielu platform w środowisku .NET Core

---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Krótki samouczek, który tworzy i uruchamia podstawową aplikację Hello World przy użyciu ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 86a0c8d017138a949fddc0356f3de548d368a4c0
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417611"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Samouczek: Wprowadzenie do ASP.NET Core

W tym samouczku pokazano, jak utworzyć i uruchomić aplikację sieci web ASP.NET Core przy użyciu interfejsu wiersza polecenia .NET Core.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie projektu aplikacji sieci Web.
> * Zaufaj certyfikatowi dewelopera.
> * Uruchom aplikację.
> * Edytuj stronę Razor.

Na końcu będziesz mieć działającą aplikację sieci web uruchomiającą na komputerze lokalnym.

![Strona główna aplikacji internetowej](_static/home-page.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Tworzenie projektu aplikacji sieci Web

Otwórz powłokę polecenia i wprowadź następujące polecenie:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Poprzednie polecenie:

* Tworzy nową aplikację internetową.  
* Parametr `-o aspnetcoreapp` tworzy katalog o nazwie *aspnetcoreapp* z plikami źródłowymi aplikacji.

### <a name="trust-the-development-certificate"></a>Ufaj certyfikatowi dewelopera

Ufaj certyfikatowi programistycznego HTTPS:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Poprzednie polecenie wyświetla następujące okno dialogowe:

![Okno dialogowe z ostrzeżeniem o zabezpieczeniach](~/getting-started/_static/cert.png)

Wybierz **opcję Tak,** jeśli zgadzasz się ufać certyfikatowi dewelopera.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Poprzednie polecenie wyświetla następujący komunikat:

*Zażądano ufania certyfikatowi dewelopera HTTPS. Jeśli certyfikat nie jest jeszcze zaufany, uruchomimy następujące polecenie:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

To polecenie może monitować o zainstalowanie certyfikatu na pęku kluczy systemowych. Wprowadź hasło, jeśli zgadzasz się zaufać certyfikatowi dewelopera.

# <a name="linux"></a>[Linux](#tab/linux)

Zobacz dokumentację dystrybucji systemu Linux, jak ufać certyfikatowi programistycznemu HTTPS.

---

Aby uzyskać więcej informacji, zobacz [Ufanie ASP.NET podstawowym certyfikatem https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Uruchomienie aplikacji

Uruchom następujące polecenia:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Po tym, jak powłoka poleceń wskaże, że aplikacja została uruchomiona, przejdź do . `https://localhost:5001`

## <a name="edit-a-razor-page"></a>Edytowanie strony Razor

Otwórz *strony/Index.cshtml* i zmodyfikuj i zapisz stronę z następującymi wyróżnionymi znacznikami:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Przejdź do `https://localhost:5001`, odśwież stronę i sprawdź, czy zmiany są wyświetlane.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu aplikacji sieci Web.
> * Zaufaj certyfikatowi dewelopera.
> * Uruchom projekt.
> * Dokonaj zmiany.

Aby dowiedzieć się więcej o ASP.NET Core, zobacz zalecaną ścieżkę uczenia się we wstępie:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>

---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Krótki samouczek, który tworzy i uruchamia podstawową aplikację Hello world przy użyciu ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
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
uid: getting-started
ms.openlocfilehash: fef5ae525a7c01d0ea7733e990233f413aac61a7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057794"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Samouczek: wprowadzenie do ASP.NET Core

W tym samouczku pokazano, jak utworzyć i uruchomić aplikację sieci Web ASP.NET Core przy użyciu interfejs wiersza polecenia platformy .NET Core.

Omawiane tematy:

> [!div class="checklist"]
> * Utwórz projekt aplikacji sieci Web.
> * Zaufaj certyfikatowi Deweloperskiemu.
> * Uruchom aplikację.
> * Edytuj Razor stronę.

Na końcu będziesz mieć działającą aplikację sieci Web na komputerze lokalnym.

![Strona główna aplikacji internetowej](_static/home-page.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Tworzenie projektu aplikacji internetowej

Otwórz powłokę poleceń i wprowadź następujące polecenie:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Powyższe polecenie ma następujące działanie:

* Tworzy nową aplikację sieci Web.  
* `-o aspnetcoreapp`Parametr tworzy katalog o nazwie *aspnetcoreapp* z plikami źródłowymi aplikacji.

### <a name="trust-the-development-certificate"></a>Ufanie certyfikatowi Deweloperskiemu

Ufaj certyfikatowi deweloperskim HTTPS:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Poprzednie polecenie wyświetla następujące okno dialogowe:

![Okno dialogowe ostrzeżenia o zabezpieczeniach](~/getting-started/_static/cert.png)

Wybierz opcję **tak** , jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Poprzednie polecenie wyświetla następujący komunikat:

*Zażądano zaufania certyfikatu deweloperskiego https. Jeśli certyfikat nie jest już zaufany, zostanie uruchomione następujące polecenie:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

To polecenie może wyświetlać monit o podanie hasła w celu zainstalowania certyfikatu w łańcuchu kluczy systemu. Wprowadź hasło, jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.

# <a name="linux"></a>[Linux](#tab/linux)

Zapoznaj się z dokumentacją dystrybucji systemu Linux dotyczącą sposobu zaufania certyfikatu deweloperskiego HTTPS.

---

Aby uzyskać więcej informacji, zobacz artykuł [ufanie certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Uruchamianie aplikacji

Uruchom następujące polecenia:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Po powłoce poleceń wskazuje, że aplikacja została uruchomiona, przejdź do `https://localhost:5001` .

## <a name="edit-a-no-locrazor-page"></a>Edytowanie Razor strony

Otwórz stronę *Pages/index. cshtml* i zmodyfikuj ją i Zapisz przy użyciu następującego wyróżnionego znacznika:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Przejdź do `https://localhost:5001` strony, Odśwież stronę i sprawdź, czy są wyświetlane zmiany.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz projekt aplikacji sieci Web.
> * Zaufaj certyfikatowi Deweloperskiemu.
> * Uruchom projekt.
> * Wprowadź zmianę.

Aby dowiedzieć się więcej na temat ASP.NET Core, zapoznaj się ze zalecaną ścieżką szkoleniową we wprowadzeniu:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>

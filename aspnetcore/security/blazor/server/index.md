---
title: Bezpieczne aplikacje Blazor serwera ASP.NET Core
author: guardrex
description: Dowiedz się, Blazor jak zabezpieczyć aplikacje serwera jako aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206369"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Bezpieczne ASP.NET Core aplikacje serwera Blazor

Autor [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>Szablon projektu serwera Blazor

Szablon projektu serwera Blazor można skonfigurować do uwierzytelniania podczas tworzenia projektu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Postępuj zgodnie ze wskazówkami programu <xref:blazor/get-started> Visual Studio w artykule, aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania.

Po wybraniu szablonu **aplikacji Blazor Server** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.

Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:

* **Bez uwierzytelniania**
* **Individual User Accounts** &ndash; Konta użytkowników poszczególnych kont użytkowników mogą być przechowywane:
  * W aplikacji korzystającej z systemu [tożsamości](xref:security/authentication/identity) ASP.NET Core.
  * Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Konta służbowe**
* **Uwierzytelnianie systemu Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> Visual Studio Code w artykule, aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

W poniższej tabeli przedstawiono`{AUTHENTICATION}`dozwolone wartości uwierzytelniania ().

| Mechanizm uwierzytelniania | Opis |
| ------------------------ | ----------- |
| `None`wartooć         | Brak uwierzytelniania |
| `Individual`             | Użytkownicy przechowywani w aplikacji z tożsamością ASP.NET Core |
| `IndividualB2C`          | Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy |
| `MultiOrg`               | Uwierzytelnianie organizacyjne dla wielu dzierżawców |
| `Windows`                | Uwierzytelnianie systemu Windows |

Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:

* Utwórz folder dla projektu.
* Nadaj nazwę projektowi.

Aby uzyskać więcej informacji, zobacz polecenie [dotnet New](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> Visual Studio dla komputerów Mac w artykule.

1. W kroku **Konfigurowanie nowej aplikacji serwera Blazor** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .

1. Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z tożsamością ASP.NET Core.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> interfejs wiersza polecenia platformy .NET Core w artykule, aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

W poniższej tabeli przedstawiono`{AUTHENTICATION}`dozwolone wartości uwierzytelniania ().

| Mechanizm uwierzytelniania | Opis |
| ------------------------ | ----------- |
| `None`wartooć         | Brak uwierzytelniania |
| `Individual`             | Użytkownicy przechowywani w aplikacji z tożsamością ASP.NET Core |
| `IndividualB2C`          | Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy |
| `MultiOrg`               | Uwierzytelnianie organizacyjne dla wielu dzierżawców |
| `Windows`                | Uwierzytelnianie systemu Windows |

Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:

* Utwórz folder dla projektu.
* Nadaj nazwę projektowi.

Aby uzyskać więcej informacji, zobacz polecenie [dotnet New](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.

---

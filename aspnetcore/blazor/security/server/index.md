---
title: Bezpieczne Blazor aplikacje serwera ASP.NET Core
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor aplikacje serwera jako aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: 2bdd2f256f456cbf474181021fafc6830bfd68f4
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242930"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Bezpieczne Blazor aplikacje serwera ASP.NET Core

Autor [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>BlazorSzablon projektu serwera

BlazorSzablon projektu serwera można skonfigurować do uwierzytelniania podczas tworzenia projektu.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Postępuj zgodnie ze wskazówkami programu Visual Studio w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania.

Po wybraniu szablonu ** Blazor aplikacji serwera** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.

Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:

* **Bez uwierzytelniania**
* **Konta poszczególnych użytkowników**: konta użytkowników mogą być przechowywane:
  * W aplikacji korzystającej z [Identity](xref:security/authentication/identity) systemu ASP.NET Core.
  * Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Konta służbowe**
* **Uwierzytelnianie systemu Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postępuj zgodnie ze wskazówkami Visual Studio Code w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().

| Mechanizm uwierzytelniania | Opis |
| ------------------------ | ----------- |
| `None`wartooć         | Brak uwierzytelniania |
| `Individual`             | Użytkownicy przechowywani w aplikacji przy użyciu ASP.NET CoreIdentity |
| `IndividualB2C`          | Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy |
| `MultiOrg`               | Uwierzytelnianie organizacyjne dla wielu dzierżawców |
| `Windows`                | Uwierzytelnianie systemu Windows |

Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:

* Utwórz folder dla projektu.
* Nadaj nazwę projektowi.

Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Postępuj zgodnie ze wskazówkami Visual Studio dla komputerów Mac w <xref:blazor/get-started> artykule.

1. W kroku **Konfigurowanie nowej Blazor aplikacji serwera** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .

1. Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core Identity .

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

Postępuj zgodnie ze wskazówkami interfejs wiersza polecenia platformy .NET Core w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().

| Mechanizm uwierzytelniania | Opis |
| ------------------------ | ----------- |
| `None`wartooć         | Brak uwierzytelniania |
| `Individual`             | Użytkownicy przechowywani w aplikacji przy użyciu ASP.NET CoreIdentity |
| `IndividualB2C`          | Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy |
| `MultiOrg`               | Uwierzytelnianie organizacyjne dla wielu dzierżawców |
| `Windows`                | Uwierzytelnianie systemu Windows |

Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:

* Utwórz folder dla projektu.
* Nadaj nazwę projektowi.

Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.

---

## <a name="secure-an-existing-app"></a>Zabezpieczanie istniejącej aplikacji

BlazorAplikacje serwera są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core. Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index> .

## <a name="scaffold-identity"></a>FunkcjaIdentity

Tworzenie szkieletu Identity w Blazor projekcie serwera:

* [Bez istniejącej autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).

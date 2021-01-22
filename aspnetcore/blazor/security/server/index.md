---
title: Bezpieczne Blazor Server aplikacje ASP.NET Core
author: guardrex
description: Dowiedz się, jak zabezpieczać Blazor Server aplikacje jako aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5031273c3395be4365b3a6d239ebce7aaf9b66ac
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658641"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a>Bezpieczne Blazor Server aplikacje ASP.NET Core

Autor [Luke Latham](https://github.com/guardrex)

Blazor Server aplikacje są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core. Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index> . Tematy w ramach tego omówienia stosują się w odniesieniu do programu Blazor Server .

## <a name="no-locblazor-server-project-template"></a>Blazor Server szablon projektu

Blazor ServerSzablon projektu można skonfigurować do uwierzytelniania podczas tworzenia projektu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Postępuj zgodnie ze wskazówkami programu Visual Studio w programie, <xref:blazor/tooling> Aby utworzyć nowy Blazor Server projekt z mechanizmem uwierzytelniania.

Po wybraniu szablonu **Blazor Server aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.

Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:

* **Bez uwierzytelniania**
* **Konta poszczególnych użytkowników**: konta użytkowników mogą być przechowywane:
  * W aplikacji korzystającej z [Identity](xref:security/authentication/identity) systemu ASP.NET Core.
  * Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Konta służbowe**
* **Uwierzytelnianie systemu Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postępuj zgodnie ze wskazówkami Visual Studio Code w programie, <xref:blazor/tooling> Aby utworzyć nowy Blazor Server projekt z mechanizmem uwierzytelniania:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().

| Mechanizm uwierzytelniania | Opis |
| ------------------------ | ----------- |
| `None` wartooć         | Brak uwierzytelniania |
| `Individual`             | Użytkownicy przechowywani w aplikacji za pomocą ASP.NET Core Identity |
| `IndividualB2C`          | Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy |
| `MultiOrg`               | Uwierzytelnianie organizacyjne dla wielu dzierżawców |
| `Windows`                | Uwierzytelnianie systemu Windows |

Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:

* Utwórz folder dla projektu.
* Nadaj nazwę projektowi.

Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Postępuj zgodnie ze wskazówkami Visual Studio dla komputerów Mac w temacie <xref:blazor/tooling> .

1. W kroku **Skonfiguruj nową Blazor Server aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .

1. Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji za pomocą usługi ASP.NET Core Identity .

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

Utwórz nowy Blazor Server projekt z mechanizmem uwierzytelniania przy użyciu następującego polecenia w powłoce poleceń:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().

| Mechanizm uwierzytelniania | Opis |
| ------------------------ | ----------- |
| `None` wartooć         | Brak uwierzytelniania |
| `Individual`             | Użytkownicy przechowywani w aplikacji za pomocą ASP.NET Core Identity |
| `IndividualB2C`          | Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy |
| `MultiOrg`               | Uwierzytelnianie organizacyjne dla wielu dzierżawców |
| `Windows`                | Uwierzytelnianie systemu Windows |

Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:

* Utwórz folder dla projektu.
* Nadaj nazwę projektowi.

Więcej informacji:

* Zapoznaj się z [`dotnet new`](/dotnet/core/tools/dotnet-new) poleceniem w przewodniku .NET Core.
* Wykonaj polecenie pomocy dla Blazor Server szablonu ( `blazorserver` ) w powłoce poleceń:

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a>Funkcja Identity

Tworzenie szkieletu Identity w Blazor Server projekcie:

* [Bez istniejącej autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a>Azure App Service w systemie Linux z Identity serwerem

Określ wystawcę jawnie podczas wdrażania programu w celu Azure App Service w systemie Linux z Identity serwerem. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [Szybki Start: Ochrona ASP.NET Core internetowego interfejsu API za pomocą platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <xref:host-and-deploy/proxy-load-balancer>: Zawiera wskazówki dotyczące:
  * Używanie przekierowanych nagłówków oprogramowania do zachowywania informacji schematu HTTPS między serwerami proxy i sieciami wewnętrznymi.
  * Dodatkowe scenariusze i przypadki użycia, w tym konfiguracja schematu ręcznego, zmiany ścieżki żądań dla poprawnego routingu żądań i przekazywanie schematu żądań dla systemu Linux i innych niż usługi IIS zwrotnych serwerów proxy.

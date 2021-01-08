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
ms.openlocfilehash: aa24def1a003a2c2608691e6168066c740f47205
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024629"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="2117b-103">Bezpieczne Blazor Server aplikacje ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2117b-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="2117b-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2117b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2117b-105">Blazor Server aplikacje są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2117b-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="2117b-106">Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="2117b-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="2117b-107">Tematy w ramach tego omówienia stosują się w odniesieniu do programu Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="2117b-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="2117b-108">Blazor Server szablon projektu</span><span class="sxs-lookup"><span data-stu-id="2117b-108">Blazor Server project template</span></span>

<span data-ttu-id="2117b-109">Blazor ServerSzablon projektu można skonfigurować do uwierzytelniania podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="2117b-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2117b-110">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2117b-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2117b-111">Postępuj zgodnie ze wskazówkami programu Visual Studio w programie, <xref:blazor/tooling> Aby utworzyć nowy Blazor Server projekt z mechanizmem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2117b-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="2117b-112">Po wybraniu szablonu **Blazor Server aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="2117b-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="2117b-113">Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2117b-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="2117b-114">**Bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="2117b-114">**No Authentication**</span></span>
* <span data-ttu-id="2117b-115">**Konta poszczególnych użytkowników**: konta użytkowników mogą być przechowywane:</span><span class="sxs-lookup"><span data-stu-id="2117b-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="2117b-116">W aplikacji korzystającej z [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2117b-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="2117b-117">Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="2117b-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="2117b-118">**Konta służbowe**</span><span class="sxs-lookup"><span data-stu-id="2117b-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="2117b-119">**Uwierzytelnianie systemu Windows**</span><span class="sxs-lookup"><span data-stu-id="2117b-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2117b-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2117b-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2117b-121">Postępuj zgodnie ze wskazówkami Visual Studio Code w programie, <xref:blazor/tooling> Aby utworzyć nowy Blazor Server projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="2117b-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="2117b-122">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="2117b-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="2117b-123">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2117b-123">Authentication mechanism</span></span> | <span data-ttu-id="2117b-124">Opis</span><span class="sxs-lookup"><span data-stu-id="2117b-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="2117b-125">`None` wartooć</span><span class="sxs-lookup"><span data-stu-id="2117b-125">`None` (default)</span></span>         | <span data-ttu-id="2117b-126">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2117b-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="2117b-127">Użytkownicy przechowywani w aplikacji za pomocą ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2117b-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="2117b-128">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="2117b-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="2117b-129">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="2117b-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="2117b-130">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="2117b-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="2117b-131">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="2117b-131">Windows Authentication</span></span> |

<span data-ttu-id="2117b-132">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="2117b-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="2117b-133">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="2117b-133">Create a folder for the project.</span></span>
* <span data-ttu-id="2117b-134">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="2117b-134">Name the project.</span></span>

<span data-ttu-id="2117b-135">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2117b-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2117b-136">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2117b-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2117b-137">Postępuj zgodnie ze wskazówkami Visual Studio dla komputerów Mac w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="2117b-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="2117b-138">W kroku **Skonfiguruj nową Blazor Server aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="2117b-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="2117b-139">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji za pomocą usługi ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="2117b-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2117b-140">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="2117b-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2117b-141">Utwórz nowy Blazor Server projekt z mechanizmem uwierzytelniania przy użyciu następującego polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="2117b-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="2117b-142">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="2117b-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="2117b-143">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2117b-143">Authentication mechanism</span></span> | <span data-ttu-id="2117b-144">Opis</span><span class="sxs-lookup"><span data-stu-id="2117b-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="2117b-145">`None` wartooć</span><span class="sxs-lookup"><span data-stu-id="2117b-145">`None` (default)</span></span>         | <span data-ttu-id="2117b-146">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2117b-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="2117b-147">Użytkownicy przechowywani w aplikacji za pomocą ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2117b-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="2117b-148">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="2117b-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="2117b-149">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="2117b-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="2117b-150">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="2117b-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="2117b-151">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="2117b-151">Windows Authentication</span></span> |

<span data-ttu-id="2117b-152">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="2117b-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="2117b-153">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="2117b-153">Create a folder for the project.</span></span>
* <span data-ttu-id="2117b-154">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="2117b-154">Name the project.</span></span>

<span data-ttu-id="2117b-155">Więcej informacji:</span><span class="sxs-lookup"><span data-stu-id="2117b-155">For more information:</span></span>

* <span data-ttu-id="2117b-156">Zapoznaj się z [`dotnet new`](/dotnet/core/tools/dotnet-new) poleceniem w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2117b-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="2117b-157">Wykonaj polecenie pomocy dla Blazor Server szablonu ( `blazorserver` ) w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="2117b-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="2117b-158">Funkcja Identity</span><span class="sxs-lookup"><span data-stu-id="2117b-158">Scaffold Identity</span></span>

<span data-ttu-id="2117b-159">Tworzenie szkieletu Identity w Blazor Server projekcie:</span><span class="sxs-lookup"><span data-stu-id="2117b-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="2117b-160">[Bez istniejącej autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="2117b-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="2117b-161">[Z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="2117b-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2117b-162">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2117b-162">Additional resources</span></span>

* [<span data-ttu-id="2117b-163">Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2117b-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="2117b-164">Szybki Start: Ochrona ASP.NET Core internetowego interfejsu API za pomocą platformy tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="2117b-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="2117b-165"><xref:host-and-deploy/proxy-load-balancer>: Zawiera wskazówki dotyczące:</span><span class="sxs-lookup"><span data-stu-id="2117b-165"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="2117b-166">Używanie przekierowanych nagłówków oprogramowania do zachowywania informacji schematu HTTPS między serwerami proxy i sieciami wewnętrznymi.</span><span class="sxs-lookup"><span data-stu-id="2117b-166">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="2117b-167">Dodatkowe scenariusze i przypadki użycia, w tym konfiguracja schematu ręcznego, zmiany ścieżki żądań dla poprawnego routingu żądań i przekazywanie schematu żądań dla systemu Linux i innych niż usługi IIS zwrotnych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="2117b-167">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>

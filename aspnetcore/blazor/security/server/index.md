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
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="d6673-103">Bezpieczne Blazor Server aplikacje ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6673-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="d6673-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d6673-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d6673-105">Blazor Server aplikacje są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6673-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="d6673-106">Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="d6673-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="d6673-107">Tematy w ramach tego omówienia stosują się w odniesieniu do programu Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="d6673-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="d6673-108">Blazor Server szablon projektu</span><span class="sxs-lookup"><span data-stu-id="d6673-108">Blazor Server project template</span></span>

<span data-ttu-id="d6673-109">Blazor ServerSzablon projektu można skonfigurować do uwierzytelniania podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="d6673-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d6673-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6673-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d6673-111">Postępuj zgodnie ze wskazówkami programu Visual Studio w programie, <xref:blazor/tooling> Aby utworzyć nowy Blazor Server projekt z mechanizmem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="d6673-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="d6673-112">Po wybraniu szablonu **Blazor Server aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="d6673-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="d6673-113">Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d6673-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="d6673-114">**Bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="d6673-114">**No Authentication**</span></span>
* <span data-ttu-id="d6673-115">**Konta poszczególnych użytkowników**: konta użytkowników mogą być przechowywane:</span><span class="sxs-lookup"><span data-stu-id="d6673-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="d6673-116">W aplikacji korzystającej z [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6673-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="d6673-117">Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="d6673-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="d6673-118">**Konta służbowe**</span><span class="sxs-lookup"><span data-stu-id="d6673-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="d6673-119">**Uwierzytelnianie systemu Windows**</span><span class="sxs-lookup"><span data-stu-id="d6673-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d6673-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d6673-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d6673-121">Postępuj zgodnie ze wskazówkami Visual Studio Code w programie, <xref:blazor/tooling> Aby utworzyć nowy Blazor Server projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="d6673-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="d6673-122">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="d6673-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="d6673-123">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6673-123">Authentication mechanism</span></span> | <span data-ttu-id="d6673-124">Opis</span><span class="sxs-lookup"><span data-stu-id="d6673-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="d6673-125">`None` wartooć</span><span class="sxs-lookup"><span data-stu-id="d6673-125">`None` (default)</span></span>         | <span data-ttu-id="d6673-126">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6673-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="d6673-127">Użytkownicy przechowywani w aplikacji za pomocą ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="d6673-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="d6673-128">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="d6673-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="d6673-129">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="d6673-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="d6673-130">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="d6673-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="d6673-131">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="d6673-131">Windows Authentication</span></span> |

<span data-ttu-id="d6673-132">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="d6673-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="d6673-133">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="d6673-133">Create a folder for the project.</span></span>
* <span data-ttu-id="d6673-134">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="d6673-134">Name the project.</span></span>

<span data-ttu-id="d6673-135">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6673-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d6673-136">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d6673-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d6673-137">Postępuj zgodnie ze wskazówkami Visual Studio dla komputerów Mac w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="d6673-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="d6673-138">W kroku **Skonfiguruj nową Blazor Server aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="d6673-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="d6673-139">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji za pomocą usługi ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="d6673-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d6673-140">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d6673-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d6673-141">Utwórz nowy Blazor Server projekt z mechanizmem uwierzytelniania przy użyciu następującego polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="d6673-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="d6673-142">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="d6673-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="d6673-143">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6673-143">Authentication mechanism</span></span> | <span data-ttu-id="d6673-144">Opis</span><span class="sxs-lookup"><span data-stu-id="d6673-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="d6673-145">`None` wartooć</span><span class="sxs-lookup"><span data-stu-id="d6673-145">`None` (default)</span></span>         | <span data-ttu-id="d6673-146">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6673-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="d6673-147">Użytkownicy przechowywani w aplikacji za pomocą ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="d6673-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="d6673-148">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="d6673-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="d6673-149">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="d6673-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="d6673-150">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="d6673-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="d6673-151">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="d6673-151">Windows Authentication</span></span> |

<span data-ttu-id="d6673-152">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="d6673-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="d6673-153">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="d6673-153">Create a folder for the project.</span></span>
* <span data-ttu-id="d6673-154">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="d6673-154">Name the project.</span></span>

<span data-ttu-id="d6673-155">Więcej informacji:</span><span class="sxs-lookup"><span data-stu-id="d6673-155">For more information:</span></span>

* <span data-ttu-id="d6673-156">Zapoznaj się z [`dotnet new`](/dotnet/core/tools/dotnet-new) poleceniem w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6673-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="d6673-157">Wykonaj polecenie pomocy dla Blazor Server szablonu ( `blazorserver` ) w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="d6673-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="d6673-158">Funkcja Identity</span><span class="sxs-lookup"><span data-stu-id="d6673-158">Scaffold Identity</span></span>

<span data-ttu-id="d6673-159">Tworzenie szkieletu Identity w Blazor Server projekcie:</span><span class="sxs-lookup"><span data-stu-id="d6673-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="d6673-160">[Bez istniejącej autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="d6673-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="d6673-161">[Z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="d6673-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a><span data-ttu-id="d6673-162">Azure App Service w systemie Linux z Identity serwerem</span><span class="sxs-lookup"><span data-stu-id="d6673-162">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="d6673-163">Określ wystawcę jawnie podczas wdrażania programu w celu Azure App Service w systemie Linux z Identity serwerem.</span><span class="sxs-lookup"><span data-stu-id="d6673-163">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="d6673-164">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="d6673-164">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6673-165">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="d6673-165">Additional resources</span></span>

* [<span data-ttu-id="d6673-166">Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6673-166">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="d6673-167">Szybki Start: Ochrona ASP.NET Core internetowego interfejsu API za pomocą platformy tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="d6673-167">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="d6673-168"><xref:host-and-deploy/proxy-load-balancer>: Zawiera wskazówki dotyczące:</span><span class="sxs-lookup"><span data-stu-id="d6673-168"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="d6673-169">Używanie przekierowanych nagłówków oprogramowania do zachowywania informacji schematu HTTPS między serwerami proxy i sieciami wewnętrznymi.</span><span class="sxs-lookup"><span data-stu-id="d6673-169">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="d6673-170">Dodatkowe scenariusze i przypadki użycia, w tym konfiguracja schematu ręcznego, zmiany ścieżki żądań dla poprawnego routingu żądań i przekazywanie schematu żądań dla systemu Linux i innych niż usługi IIS zwrotnych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="d6673-170">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>

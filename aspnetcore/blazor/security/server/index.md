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
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="9b9f0-103">Bezpieczne Blazor aplikacje serwera ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b9f0-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="9b9f0-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9b9f0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="9b9f0-105">Szablon projektu serwera</span><span class="sxs-lookup"><span data-stu-id="9b9f0-105"> Server project template</span></span>

<span data-ttu-id="9b9f0-106">BlazorSzablon projektu serwera można skonfigurować do uwierzytelniania podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9b9f0-107">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b9f0-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9b9f0-108">Postępuj zgodnie ze wskazówkami programu Visual Studio w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="9b9f0-109">Po wybraniu szablonu \*\* Blazor aplikacji serwera\*\* w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="9b9f0-110">Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9b9f0-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="9b9f0-111">**Bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="9b9f0-111">**No Authentication**</span></span>
* <span data-ttu-id="9b9f0-112">**Konta poszczególnych użytkowników**: konta użytkowników mogą być przechowywane:</span><span class="sxs-lookup"><span data-stu-id="9b9f0-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="9b9f0-113">W aplikacji korzystającej z [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="9b9f0-114">Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="9b9f0-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="9b9f0-115">**Konta służbowe**</span><span class="sxs-lookup"><span data-stu-id="9b9f0-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="9b9f0-116">**Uwierzytelnianie systemu Windows**</span><span class="sxs-lookup"><span data-stu-id="9b9f0-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9b9f0-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b9f0-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9b9f0-118">Postępuj zgodnie ze wskazówkami Visual Studio Code w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="9b9f0-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9b9f0-119">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="9b9f0-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9b9f0-120">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b9f0-120">Authentication mechanism</span></span> | <span data-ttu-id="9b9f0-121">Opis</span><span class="sxs-lookup"><span data-stu-id="9b9f0-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9b9f0-122">`None`wartooć</span><span class="sxs-lookup"><span data-stu-id="9b9f0-122">`None` (default)</span></span>         | <span data-ttu-id="9b9f0-123">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b9f0-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9b9f0-124">Użytkownicy przechowywani w aplikacji przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="9b9f0-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9b9f0-125">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9b9f0-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9b9f0-126">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="9b9f0-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9b9f0-127">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="9b9f0-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9b9f0-128">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9b9f0-128">Windows Authentication</span></span> |

<span data-ttu-id="9b9f0-129">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="9b9f0-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9b9f0-130">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-130">Create a folder for the project.</span></span>
* <span data-ttu-id="9b9f0-131">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-131">Name the project.</span></span>

<span data-ttu-id="9b9f0-132">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-132">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9b9f0-133">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9b9f0-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9b9f0-134">Postępuj zgodnie ze wskazówkami Visual Studio dla komputerów Mac w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="9b9f0-135">W kroku **Konfigurowanie nowej Blazor aplikacji serwera** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="9b9f0-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="9b9f0-136">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="9b9f0-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9b9f0-137">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="9b9f0-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9b9f0-138">Postępuj zgodnie ze wskazówkami interfejs wiersza polecenia platformy .NET Core w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="9b9f0-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9b9f0-139">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="9b9f0-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9b9f0-140">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b9f0-140">Authentication mechanism</span></span> | <span data-ttu-id="9b9f0-141">Opis</span><span class="sxs-lookup"><span data-stu-id="9b9f0-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9b9f0-142">`None`wartooć</span><span class="sxs-lookup"><span data-stu-id="9b9f0-142">`None` (default)</span></span>         | <span data-ttu-id="9b9f0-143">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b9f0-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9b9f0-144">Użytkownicy przechowywani w aplikacji przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="9b9f0-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9b9f0-145">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9b9f0-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9b9f0-146">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="9b9f0-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9b9f0-147">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="9b9f0-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9b9f0-148">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9b9f0-148">Windows Authentication</span></span> |

<span data-ttu-id="9b9f0-149">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="9b9f0-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9b9f0-150">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-150">Create a folder for the project.</span></span>
* <span data-ttu-id="9b9f0-151">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-151">Name the project.</span></span>

<span data-ttu-id="9b9f0-152">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-152">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="9b9f0-153">Zabezpieczanie istniejącej aplikacji</span><span class="sxs-lookup"><span data-stu-id="9b9f0-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="9b9f0-154">Aplikacje serwera są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b9f0-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="9b9f0-155">Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="9b9f0-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="9b9f0-156">FunkcjaIdentity</span><span class="sxs-lookup"><span data-stu-id="9b9f0-156">Scaffold Identity</span></span>

<span data-ttu-id="9b9f0-157">Tworzenie szkieletu Identity w Blazor projekcie serwera:</span><span class="sxs-lookup"><span data-stu-id="9b9f0-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="9b9f0-158">[Bez istniejącej autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="9b9f0-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="9b9f0-159">[Z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="9b9f0-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

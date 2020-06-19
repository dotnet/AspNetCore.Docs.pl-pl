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
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103791"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="395ca-103">Bezpieczne Blazor aplikacje serwera ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="395ca-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="395ca-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="395ca-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="395ca-105">Szablon projektu serwera</span><span class="sxs-lookup"><span data-stu-id="395ca-105"> Server project template</span></span>

<span data-ttu-id="395ca-106">BlazorSzablon projektu serwera można skonfigurować do uwierzytelniania podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="395ca-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="395ca-107">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="395ca-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="395ca-108">Postępuj zgodnie ze wskazówkami programu Visual Studio w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="395ca-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="395ca-109">Po wybraniu szablonu \*\* Blazor aplikacji serwera\*\* w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="395ca-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="395ca-110">Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="395ca-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="395ca-111">**Bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="395ca-111">**No Authentication**</span></span>
* <span data-ttu-id="395ca-112">**Konta poszczególnych użytkowników**: konta użytkowników mogą być przechowywane:</span><span class="sxs-lookup"><span data-stu-id="395ca-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="395ca-113">W aplikacji korzystającej z [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="395ca-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="395ca-114">Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="395ca-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="395ca-115">**Konta służbowe**</span><span class="sxs-lookup"><span data-stu-id="395ca-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="395ca-116">**Uwierzytelnianie systemu Windows**</span><span class="sxs-lookup"><span data-stu-id="395ca-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="395ca-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="395ca-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="395ca-118">Postępuj zgodnie ze wskazówkami Visual Studio Code w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="395ca-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="395ca-119">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="395ca-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="395ca-120">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="395ca-120">Authentication mechanism</span></span> | <span data-ttu-id="395ca-121">Opis</span><span class="sxs-lookup"><span data-stu-id="395ca-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="395ca-122">`None`wartooć</span><span class="sxs-lookup"><span data-stu-id="395ca-122">`None` (default)</span></span>         | <span data-ttu-id="395ca-123">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="395ca-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="395ca-124">Użytkownicy przechowywani w aplikacji przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="395ca-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="395ca-125">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="395ca-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="395ca-126">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="395ca-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="395ca-127">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="395ca-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="395ca-128">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="395ca-128">Windows Authentication</span></span> |

<span data-ttu-id="395ca-129">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="395ca-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="395ca-130">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="395ca-130">Create a folder for the project.</span></span>
* <span data-ttu-id="395ca-131">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="395ca-131">Name the project.</span></span>

<span data-ttu-id="395ca-132">Aby uzyskać więcej informacji, zobacz polecenie [dotnet New](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="395ca-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="395ca-133">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="395ca-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="395ca-134">Postępuj zgodnie ze wskazówkami Visual Studio dla komputerów Mac w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="395ca-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="395ca-135">W kroku **Konfigurowanie nowej Blazor aplikacji serwera** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="395ca-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="395ca-136">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="395ca-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="395ca-137">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="395ca-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="395ca-138">Postępuj zgodnie ze wskazówkami interfejs wiersza polecenia platformy .NET Core w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="395ca-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="395ca-139">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="395ca-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="395ca-140">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="395ca-140">Authentication mechanism</span></span> | <span data-ttu-id="395ca-141">Opis</span><span class="sxs-lookup"><span data-stu-id="395ca-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="395ca-142">`None`wartooć</span><span class="sxs-lookup"><span data-stu-id="395ca-142">`None` (default)</span></span>         | <span data-ttu-id="395ca-143">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="395ca-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="395ca-144">Użytkownicy przechowywani w aplikacji przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="395ca-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="395ca-145">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="395ca-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="395ca-146">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="395ca-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="395ca-147">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="395ca-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="395ca-148">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="395ca-148">Windows Authentication</span></span> |

<span data-ttu-id="395ca-149">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="395ca-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="395ca-150">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="395ca-150">Create a folder for the project.</span></span>
* <span data-ttu-id="395ca-151">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="395ca-151">Name the project.</span></span>

<span data-ttu-id="395ca-152">Aby uzyskać więcej informacji, zobacz polecenie [dotnet New](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="395ca-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="395ca-153">Zabezpieczanie istniejącej aplikacji</span><span class="sxs-lookup"><span data-stu-id="395ca-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="395ca-154">Aplikacje serwera są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="395ca-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="395ca-155">Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="395ca-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="395ca-156">FunkcjaIdentity</span><span class="sxs-lookup"><span data-stu-id="395ca-156">Scaffold Identity</span></span>

<span data-ttu-id="395ca-157">Tworzenie szkieletu Identity w Blazor projekcie serwera:</span><span class="sxs-lookup"><span data-stu-id="395ca-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="395ca-158">[Bez istniejącej autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="395ca-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="395ca-159">[Z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="395ca-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

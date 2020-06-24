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
ms.openlocfilehash: 2811e08fd2f6c66112ffa0bb40f474158f4c7a59
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292688"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="6222c-103">Bezpieczne Blazor aplikacje serwera ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6222c-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="6222c-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6222c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="6222c-105">Aplikacje serwera są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6222c-105"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="6222c-106">Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="6222c-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="6222c-107">Tematy w ramach tego omówienia mają zastosowanie w odniesieniu do Blazor serwera programu.</span><span class="sxs-lookup"><span data-stu-id="6222c-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="6222c-108">Szablon projektu serwera</span><span class="sxs-lookup"><span data-stu-id="6222c-108"> Server project template</span></span>

<span data-ttu-id="6222c-109">BlazorSzablon projektu serwera można skonfigurować do uwierzytelniania podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="6222c-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6222c-110">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6222c-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6222c-111">Postępuj zgodnie ze wskazówkami programu Visual Studio w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6222c-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="6222c-112">Po wybraniu szablonu \*\* Blazor aplikacji serwera\*\* w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="6222c-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="6222c-113">Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6222c-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="6222c-114">**Bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="6222c-114">**No Authentication**</span></span>
* <span data-ttu-id="6222c-115">**Konta poszczególnych użytkowników**: konta użytkowników mogą być przechowywane:</span><span class="sxs-lookup"><span data-stu-id="6222c-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="6222c-116">W aplikacji korzystającej z [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6222c-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="6222c-117">Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="6222c-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="6222c-118">**Konta służbowe**</span><span class="sxs-lookup"><span data-stu-id="6222c-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="6222c-119">**Uwierzytelnianie systemu Windows**</span><span class="sxs-lookup"><span data-stu-id="6222c-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6222c-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6222c-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6222c-121">Postępuj zgodnie ze wskazówkami Visual Studio Code w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="6222c-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="6222c-122">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="6222c-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="6222c-123">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6222c-123">Authentication mechanism</span></span> | <span data-ttu-id="6222c-124">Opis</span><span class="sxs-lookup"><span data-stu-id="6222c-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="6222c-125">`None`wartooć</span><span class="sxs-lookup"><span data-stu-id="6222c-125">`None` (default)</span></span>         | <span data-ttu-id="6222c-126">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6222c-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="6222c-127">Użytkownicy przechowywani w aplikacji przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="6222c-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="6222c-128">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="6222c-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="6222c-129">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="6222c-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="6222c-130">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="6222c-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="6222c-131">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="6222c-131">Windows Authentication</span></span> |

<span data-ttu-id="6222c-132">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="6222c-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="6222c-133">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="6222c-133">Create a folder for the project.</span></span>
* <span data-ttu-id="6222c-134">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="6222c-134">Name the project.</span></span>

<span data-ttu-id="6222c-135">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6222c-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6222c-136">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6222c-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6222c-137">Postępuj zgodnie ze wskazówkami Visual Studio dla komputerów Mac w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="6222c-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="6222c-138">W kroku **Konfigurowanie nowej Blazor aplikacji serwera** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="6222c-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="6222c-139">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6222c-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6222c-140">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="6222c-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6222c-141">Postępuj zgodnie ze wskazówkami interfejs wiersza polecenia platformy .NET Core w <xref:blazor/get-started> artykule, aby utworzyć nowy Blazor projekt serwera z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="6222c-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="6222c-142">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="6222c-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="6222c-143">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6222c-143">Authentication mechanism</span></span> | <span data-ttu-id="6222c-144">Opis</span><span class="sxs-lookup"><span data-stu-id="6222c-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="6222c-145">`None`wartooć</span><span class="sxs-lookup"><span data-stu-id="6222c-145">`None` (default)</span></span>         | <span data-ttu-id="6222c-146">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6222c-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="6222c-147">Użytkownicy przechowywani w aplikacji przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="6222c-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="6222c-148">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="6222c-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="6222c-149">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="6222c-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="6222c-150">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="6222c-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="6222c-151">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="6222c-151">Windows Authentication</span></span> |

<span data-ttu-id="6222c-152">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="6222c-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="6222c-153">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="6222c-153">Create a folder for the project.</span></span>
* <span data-ttu-id="6222c-154">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="6222c-154">Name the project.</span></span>

<span data-ttu-id="6222c-155">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6222c-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="6222c-156">FunkcjaIdentity</span><span class="sxs-lookup"><span data-stu-id="6222c-156">Scaffold Identity</span></span>

<span data-ttu-id="6222c-157">Tworzenie szkieletu Identity w Blazor projekcie serwera:</span><span class="sxs-lookup"><span data-stu-id="6222c-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="6222c-158">[Bez istniejącej autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="6222c-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="6222c-159">[Z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="6222c-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

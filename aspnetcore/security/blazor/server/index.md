---
title: Bezpieczne aplikacje Blazor serwera ASP.NET Core
author: guardrex
description: Dowiedz się, Blazor jak zabezpieczyć aplikacje serwera jako aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 324b3e8ed2d931c81bb41381caa469b0ec2f2fda
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727812"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="ec150-103">Bezpieczne ASP.NET Core aplikacje serwera Blazor</span><span class="sxs-lookup"><span data-stu-id="ec150-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="ec150-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ec150-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="ec150-105">Szablon projektu serwera Blazor</span><span class="sxs-lookup"><span data-stu-id="ec150-105">Blazor Server project template</span></span>

<span data-ttu-id="ec150-106">Szablon projektu serwera Blazor można skonfigurować do uwierzytelniania podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="ec150-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec150-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec150-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec150-108">Postępuj zgodnie ze wskazówkami programu <xref:blazor/get-started> Visual Studio w artykule, aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ec150-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="ec150-109">Po wybraniu szablonu **aplikacji Blazor Server** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="ec150-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="ec150-110">Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ec150-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="ec150-111">**Bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="ec150-111">**No Authentication**</span></span>
* <span data-ttu-id="ec150-112">**Individual User Accounts** &ndash; Konta użytkowników poszczególnych kont użytkowników mogą być przechowywane:</span><span class="sxs-lookup"><span data-stu-id="ec150-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="ec150-113">W aplikacji korzystającej z systemu [tożsamości](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ec150-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="ec150-114">Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="ec150-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="ec150-115">**Konta służbowe**</span><span class="sxs-lookup"><span data-stu-id="ec150-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="ec150-116">**Uwierzytelnianie systemu Windows**</span><span class="sxs-lookup"><span data-stu-id="ec150-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec150-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec150-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec150-118">Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> Visual Studio Code w artykule, aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="ec150-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ec150-119">W poniższej tabeli przedstawiono`{AUTHENTICATION}`dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="ec150-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ec150-120">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="ec150-120">Authentication mechanism</span></span> | <span data-ttu-id="ec150-121">Opis</span><span class="sxs-lookup"><span data-stu-id="ec150-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="ec150-122">`None`wartooć</span><span class="sxs-lookup"><span data-stu-id="ec150-122">`None` (default)</span></span>         | <span data-ttu-id="ec150-123">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="ec150-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="ec150-124">Użytkownicy przechowywani w aplikacji z tożsamością ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec150-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="ec150-125">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="ec150-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="ec150-126">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="ec150-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="ec150-127">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="ec150-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="ec150-128">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="ec150-128">Windows Authentication</span></span> |

<span data-ttu-id="ec150-129">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="ec150-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="ec150-130">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="ec150-130">Create a folder for the project.</span></span>
* <span data-ttu-id="ec150-131">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="ec150-131">Name the project.</span></span>

<span data-ttu-id="ec150-132">Aby uzyskać więcej informacji, zobacz polecenie [dotnet New](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ec150-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ec150-133">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ec150-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ec150-134">Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> Visual Studio dla komputerów Mac w artykule.</span><span class="sxs-lookup"><span data-stu-id="ec150-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="ec150-135">W kroku **Konfigurowanie nowej aplikacji serwera Blazor** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="ec150-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="ec150-136">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z tożsamością ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ec150-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ec150-137">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ec150-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ec150-138">Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> interfejs wiersza polecenia platformy .NET Core w artykule, aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="ec150-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ec150-139">W poniższej tabeli przedstawiono`{AUTHENTICATION}`dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="ec150-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ec150-140">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="ec150-140">Authentication mechanism</span></span> | <span data-ttu-id="ec150-141">Opis</span><span class="sxs-lookup"><span data-stu-id="ec150-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="ec150-142">`None`wartooć</span><span class="sxs-lookup"><span data-stu-id="ec150-142">`None` (default)</span></span>         | <span data-ttu-id="ec150-143">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="ec150-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="ec150-144">Użytkownicy przechowywani w aplikacji z tożsamością ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec150-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="ec150-145">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="ec150-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="ec150-146">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="ec150-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="ec150-147">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="ec150-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="ec150-148">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="ec150-148">Windows Authentication</span></span> |

<span data-ttu-id="ec150-149">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="ec150-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="ec150-150">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="ec150-150">Create a folder for the project.</span></span>
* <span data-ttu-id="ec150-151">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="ec150-151">Name the project.</span></span>

<span data-ttu-id="ec150-152">Aby uzyskać więcej informacji, zobacz polecenie [dotnet New](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ec150-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="ec150-153">Zabezpieczanie istniejącej aplikacji</span><span class="sxs-lookup"><span data-stu-id="ec150-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="ec150-154">Aplikacje serwera są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ec150-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="ec150-155">Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="ec150-155">For more information, see the articles under <xref:security/index>.</span></span>

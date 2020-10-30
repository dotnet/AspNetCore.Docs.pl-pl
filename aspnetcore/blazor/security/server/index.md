---
title: 'Bezpieczne :::no-loc(Blazor Server)::: aplikacje ASP.NET Core'
author: guardrex
description: 'Dowiedz się, jak zabezpieczać :::no-loc(Blazor Server)::: aplikacje jako aplikacje ASP.NET Core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055480"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="1b3c4-103">Bezpieczne :::no-loc(Blazor Server)::: aplikacje ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b3c4-103">Secure ASP.NET Core :::no-loc(Blazor Server)::: apps</span></span>

<span data-ttu-id="1b3c4-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1b3c4-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1b3c4-105">:::no-loc(Blazor Server)::: aplikacje są skonfigurowane pod kątem zabezpieczeń w taki sam sposób, jak aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-105">:::no-loc(Blazor Server)::: apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="1b3c4-106">Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="1b3c4-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="1b3c4-107">Tematy w ramach tego omówienia stosują się w odniesieniu do programu :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="1b3c4-107">Topics under this overview apply specifically to :::no-loc(Blazor Server):::.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="1b3c4-108">:::no-loc(Blazor Server)::: szablon projektu</span><span class="sxs-lookup"><span data-stu-id="1b3c4-108">:::no-loc(Blazor Server)::: project template</span></span>

<span data-ttu-id="1b3c4-109">:::no-loc(Blazor Server):::Szablon projektu można skonfigurować do uwierzytelniania podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-109">The :::no-loc(Blazor Server)::: project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b3c4-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b3c4-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1b3c4-111">Postępuj zgodnie ze wskazówkami programu Visual Studio w programie, <xref:blazor/tooling> Aby utworzyć nowy :::no-loc(Blazor Server)::: projekt z mechanizmem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism.</span></span>

<span data-ttu-id="1b3c4-112">Po wybraniu szablonu **:::no-loc(Blazor Server)::: aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="1b3c4-112">After choosing the **:::no-loc(Blazor Server)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

<span data-ttu-id="1b3c4-113">Zostanie otwarte okno dialogowe z zaoferowaniem tego samego zestawu mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="1b3c4-114">**Bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="1b3c4-114">**No Authentication**</span></span>
* <span data-ttu-id="1b3c4-115">**Konta poszczególnych użytkowników** : konta użytkowników mogą być przechowywane:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-115">**Individual User Accounts** : User accounts can be stored:</span></span>
  * <span data-ttu-id="1b3c4-116">W aplikacji korzystającej z [:::no-loc(Identity):::](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-116">Within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="1b3c4-117">Z [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="1b3c4-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="1b3c4-118">**Konta służbowe**</span><span class="sxs-lookup"><span data-stu-id="1b3c4-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="1b3c4-119">**Uwierzytelnianie systemu Windows**</span><span class="sxs-lookup"><span data-stu-id="1b3c4-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b3c4-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b3c4-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1b3c4-121">Postępuj zgodnie ze wskazówkami Visual Studio Code w programie, <xref:blazor/tooling> Aby utworzyć nowy :::no-loc(Blazor Server)::: projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="1b3c4-122">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="1b3c4-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="1b3c4-123">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b3c4-123">Authentication mechanism</span></span> | <span data-ttu-id="1b3c4-124">Opis</span><span class="sxs-lookup"><span data-stu-id="1b3c4-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="1b3c4-125">`None` wartooć</span><span class="sxs-lookup"><span data-stu-id="1b3c4-125">`None` (default)</span></span>         | <span data-ttu-id="1b3c4-126">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b3c4-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="1b3c4-127">Użytkownicy przechowywani w aplikacji za pomocą :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="1b3c4-127">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="1b3c4-128">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="1b3c4-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="1b3c4-129">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="1b3c4-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="1b3c4-130">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="1b3c4-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="1b3c4-131">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="1b3c4-131">Windows Authentication</span></span> |

<span data-ttu-id="1b3c4-132">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="1b3c4-133">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-133">Create a folder for the project.</span></span>
* <span data-ttu-id="1b3c4-134">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-134">Name the project.</span></span>

<span data-ttu-id="1b3c4-135">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b3c4-136">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b3c4-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b3c4-137">Postępuj zgodnie ze wskazówkami Visual Studio dla komputerów Mac w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="1b3c4-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="1b3c4-138">W kroku **Skonfiguruj nową :::no-loc(Blazor Server)::: aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="1b3c4-138">On the **Configure your new :::no-loc(Blazor Server)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="1b3c4-139">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji za pomocą usługi :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="1b3c4-139">The app is created for individual users stored in the app with :::no-loc(ASP.NET Core Identity):::.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b3c4-140">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b3c4-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1b3c4-141">Utwórz nowy :::no-loc(Blazor Server)::: projekt z mechanizmem uwierzytelniania przy użyciu następującego polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-141">Create a new :::no-loc(Blazor Server)::: project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="1b3c4-142">`{AUTHENTICATION}`W poniższej tabeli przedstawiono dozwolone wartości uwierzytelniania ().</span><span class="sxs-lookup"><span data-stu-id="1b3c4-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="1b3c4-143">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b3c4-143">Authentication mechanism</span></span> | <span data-ttu-id="1b3c4-144">Opis</span><span class="sxs-lookup"><span data-stu-id="1b3c4-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="1b3c4-145">`None` wartooć</span><span class="sxs-lookup"><span data-stu-id="1b3c4-145">`None` (default)</span></span>         | <span data-ttu-id="1b3c4-146">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b3c4-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="1b3c4-147">Użytkownicy przechowywani w aplikacji za pomocą :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="1b3c4-147">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="1b3c4-148">Użytkownicy przechowywani w [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="1b3c4-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="1b3c4-149">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="1b3c4-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="1b3c4-150">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="1b3c4-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="1b3c4-151">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="1b3c4-151">Windows Authentication</span></span> |

<span data-ttu-id="1b3c4-152">Za pomocą `-o|--output` opcji, polecenie używa wartości podanej dla `{APP NAME}` symbolu zastępczego, aby:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="1b3c4-153">Utwórz folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-153">Create a folder for the project.</span></span>
* <span data-ttu-id="1b3c4-154">Nadaj nazwę projektowi.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-154">Name the project.</span></span>

<span data-ttu-id="1b3c4-155">Więcej informacji:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-155">For more information:</span></span>

* <span data-ttu-id="1b3c4-156">Zapoznaj się z [`dotnet new`](/dotnet/core/tools/dotnet-new) poleceniem w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1b3c4-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="1b3c4-157">Wykonaj polecenie pomocy dla :::no-loc(Blazor Server)::: szablonu ( `blazorserver` ) w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-157">Execute the help command for the :::no-loc(Blazor Server)::: template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="1b3c4-158">Funkcja :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1b3c4-158">Scaffold :::no-loc(Identity):::</span></span>

<span data-ttu-id="1b3c4-159">Tworzenie szkieletu :::no-loc(Identity)::: w :::no-loc(Blazor Server)::: projekcie:</span><span class="sxs-lookup"><span data-stu-id="1b3c4-159">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project:</span></span>

* <span data-ttu-id="1b3c4-160">[Bez istniejącej autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="1b3c4-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="1b3c4-161">[Z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="1b3c4-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b3c4-162">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1b3c4-162">Additional resources</span></span>

* [<span data-ttu-id="1b3c4-163">Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b3c4-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="1b3c4-164">Szybki Start: Ochrona ASP.NET Core internetowego interfejsu API za pomocą platformy tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="1b3c4-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)

---
title: Artykuły oparte na ASP.NET Core projektach utworzonych przy użyciu poszczególnych kont użytkowników
author: rick-anderson
description: Odkryj artykuły na podstawie ASP.NET Core projektów utworzonych przy użyciu poszczególnych kont użytkowników.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6d3743fc31c29bed5075ac29381aea51a64a908c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406254"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="3d9a6-103">Artykuły oparte na ASP.NET Core projektach utworzonych przy użyciu poszczególnych kont użytkowników</span><span class="sxs-lookup"><span data-stu-id="3d9a6-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="3d9a6-104">ASP.NET Core Identity jest zawarty w szablonach projektu w programie Visual Studio z opcją "indywidualne konta użytkowników".</span><span class="sxs-lookup"><span data-stu-id="3d9a6-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="3d9a6-105">Szablony uwierzytelniania są dostępne w interfejs wiersza polecenia platformy .NET Core z `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="3d9a6-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="3d9a6-106">Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5833) w usłudze GitHub, aby uzyskać uwierzytelnianie interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3d9a6-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="3d9a6-107">Bez uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="3d9a6-107">No Authentication</span></span>

<span data-ttu-id="3d9a6-108">Uwierzytelnianie jest określone w interfejs wiersza polecenia platformy .NET Core z `-au` opcją.</span><span class="sxs-lookup"><span data-stu-id="3d9a6-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="3d9a6-109">W programie Visual Studio okno dialogowe **Zmienianie uwierzytelniania** jest dostępne dla nowych aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3d9a6-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="3d9a6-110">Wartość domyślna dla nowych aplikacji sieci Web w programie Visual Studio **nie jest uwierzytelnianiem**.</span><span class="sxs-lookup"><span data-stu-id="3d9a6-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="3d9a6-111">Projekty utworzone bez uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="3d9a6-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="3d9a6-112">Nie zawierają stron sieci Web i interfejsu użytkownika w celu zalogowania się i wylogowania.</span><span class="sxs-lookup"><span data-stu-id="3d9a6-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="3d9a6-113">Nie zawierają kodu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="3d9a6-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="3d9a6-114">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3d9a6-114">Windows Authentication</span></span>

<span data-ttu-id="3d9a6-115">Uwierzytelnianie systemu Windows jest określone dla nowych aplikacji sieci Web w interfejs wiersza polecenia platformy .NET Core z `-au Windows` opcją.</span><span class="sxs-lookup"><span data-stu-id="3d9a6-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="3d9a6-116">W programie Visual Studio w oknie dialogowym **Zmienianie uwierzytelniania** dostępne są opcje **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="3d9a6-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="3d9a6-117">Jeśli wybrano opcję uwierzytelnianie systemu Windows, aplikacja jest skonfigurowana do korzystania z [modułu usług IIS uwierzytelniania systemu Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="3d9a6-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="3d9a6-118">Uwierzytelnianie systemu Windows jest przeznaczone dla intranetowych witryn sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3d9a6-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="3d9a6-119">Opcje uwierzytelniania w programie dotnet New webapp</span><span class="sxs-lookup"><span data-stu-id="3d9a6-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="3d9a6-120">W poniższej tabeli przedstawiono opcje uwierzytelniania dostępne dla nowych aplikacji sieci Web:</span><span class="sxs-lookup"><span data-stu-id="3d9a6-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="3d9a6-121">Opcja</span><span class="sxs-lookup"><span data-stu-id="3d9a6-121">Option</span></span> | <span data-ttu-id="3d9a6-122">Typ uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="3d9a6-122">Type of authentication</span></span> | <span data-ttu-id="3d9a6-123">Link umożliwiający uzyskanie dodatkowych informacji</span><span class="sxs-lookup"><span data-stu-id="3d9a6-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="3d9a6-124">Brak</span><span class="sxs-lookup"><span data-stu-id="3d9a6-124">None</span></span>            |  <span data-ttu-id="3d9a6-125">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="3d9a6-125">No authentication</span></span> | | 
| <span data-ttu-id="3d9a6-126">Szczegółowe</span><span class="sxs-lookup"><span data-stu-id="3d9a6-126">Individual</span></span>      |  <span data-ttu-id="3d9a6-127">Uwierzytelnianie indywidualne</span><span class="sxs-lookup"><span data-stu-id="3d9a6-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="3d9a6-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="3d9a6-128">IndividualB2C</span></span>   |  <span data-ttu-id="3d9a6-129">Uwierzytelnianie indywidualne hostowane w chmurze za pomocą Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3d9a6-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="3d9a6-130">Funkcje B2C platformy Azure</span><span class="sxs-lookup"><span data-stu-id="3d9a6-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="3d9a6-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="3d9a6-131">SingleOrg</span></span>       |  <span data-ttu-id="3d9a6-132">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="3d9a6-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="3d9a6-133">Usługa Azure AD</span><span class="sxs-lookup"><span data-stu-id="3d9a6-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="3d9a6-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="3d9a6-134">MultiOrg</span></span>        |  <span data-ttu-id="3d9a6-135">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="3d9a6-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="3d9a6-136">Usługa Azure AD</span><span class="sxs-lookup"><span data-stu-id="3d9a6-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="3d9a6-137">Windows</span><span class="sxs-lookup"><span data-stu-id="3d9a6-137">Windows</span></span>         |  <span data-ttu-id="3d9a6-138">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3d9a6-138">Windows authentication</span></span> | [<span data-ttu-id="3d9a6-139">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3d9a6-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="3d9a6-140">Opcje uwierzytelniania programu Visual Studio New webapp</span><span class="sxs-lookup"><span data-stu-id="3d9a6-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="3d9a6-141">W poniższej tabeli przedstawiono opcje uwierzytelniania dostępne podczas tworzenia nowej aplikacji sieci Web przy użyciu programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3d9a6-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="3d9a6-142">Opcja</span><span class="sxs-lookup"><span data-stu-id="3d9a6-142">Option</span></span> | <span data-ttu-id="3d9a6-143">Typ uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="3d9a6-143">Type of authentication</span></span> | <span data-ttu-id="3d9a6-144">Link umożliwiający uzyskanie dodatkowych informacji</span><span class="sxs-lookup"><span data-stu-id="3d9a6-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="3d9a6-145">Brak</span><span class="sxs-lookup"><span data-stu-id="3d9a6-145">None</span></span>            |  <span data-ttu-id="3d9a6-146">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="3d9a6-146">No authentication</span></span> | | 
| <span data-ttu-id="3d9a6-147">Konta użytkowników indywidualnych/konta użytkowników sklepu w aplikacji</span><span class="sxs-lookup"><span data-stu-id="3d9a6-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="3d9a6-148">Uwierzytelnianie indywidualne</span><span class="sxs-lookup"><span data-stu-id="3d9a6-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="3d9a6-149">Indywidualne konta użytkowników/łączenie z istniejącym magazynem użytkowników w chmurze</span><span class="sxs-lookup"><span data-stu-id="3d9a6-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="3d9a6-150">Uwierzytelnianie indywidualne hostowane w chmurze za pomocą Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3d9a6-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="3d9a6-151">Funkcje B2C platformy Azure</span><span class="sxs-lookup"><span data-stu-id="3d9a6-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="3d9a6-152">Chmura służbowa/jedna organizacja</span><span class="sxs-lookup"><span data-stu-id="3d9a6-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="3d9a6-153">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="3d9a6-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="3d9a6-154">Usługa Azure AD</span><span class="sxs-lookup"><span data-stu-id="3d9a6-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="3d9a6-155">Chmura służbowa/usługa szkoły i wiele organizacji</span><span class="sxs-lookup"><span data-stu-id="3d9a6-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="3d9a6-156">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="3d9a6-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="3d9a6-157">Usługa Azure AD</span><span class="sxs-lookup"><span data-stu-id="3d9a6-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="3d9a6-158">Windows</span><span class="sxs-lookup"><span data-stu-id="3d9a6-158">Windows</span></span>         |  <span data-ttu-id="3d9a6-159">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3d9a6-159">Windows authentication</span></span> | [<span data-ttu-id="3d9a6-160">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3d9a6-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="3d9a6-161">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3d9a6-161">Additional resources</span></span>

<span data-ttu-id="3d9a6-162">W poniższych artykułach pokazano, jak używać kodu wygenerowanego w szablonach ASP.NET Core, które używają poszczególnych kont użytkowników:</span><span class="sxs-lookup"><span data-stu-id="3d9a6-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="3d9a6-163">Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d9a6-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="3d9a6-164">Tworzenie aplikacji ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację</span><span class="sxs-lookup"><span data-stu-id="3d9a6-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)

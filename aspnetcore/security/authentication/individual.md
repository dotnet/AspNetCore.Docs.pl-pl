---
title: Artykuły oparte na ASP.NET Core projektach utworzonych przy użyciu poszczególnych kont użytkowników
author: rick-anderson
description: Odkryj artykuły na podstawie ASP.NET Core projektów utworzonych przy użyciu poszczególnych kont użytkowników.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/individual
ms.openlocfilehash: 656006396de120b7feae6f2e08b5dad3b5a170b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053348"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="5c20e-103">Artykuły oparte na ASP.NET Core projektach utworzonych przy użyciu poszczególnych kont użytkowników</span><span class="sxs-lookup"><span data-stu-id="5c20e-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="5c20e-104">ASP.NET Core Identity jest uwzględniony w szablonach projektu w programie Visual Studio z opcją "indywidualne konta użytkowników".</span><span class="sxs-lookup"><span data-stu-id="5c20e-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="5c20e-105">Szablony uwierzytelniania są dostępne w interfejs wiersza polecenia platformy .NET Core z `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="5c20e-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

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

<span data-ttu-id="5c20e-106">Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5833) w usłudze GitHub, aby uzyskać uwierzytelnianie interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5c20e-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="5c20e-107">Bez uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="5c20e-107">No Authentication</span></span>

<span data-ttu-id="5c20e-108">Uwierzytelnianie jest określone w interfejs wiersza polecenia platformy .NET Core z `-au` opcją.</span><span class="sxs-lookup"><span data-stu-id="5c20e-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="5c20e-109">W programie Visual Studio okno dialogowe **Zmienianie uwierzytelniania** jest dostępne dla nowych aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5c20e-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="5c20e-110">Wartość domyślna dla nowych aplikacji sieci Web w programie Visual Studio **nie jest uwierzytelnianiem** .</span><span class="sxs-lookup"><span data-stu-id="5c20e-110">The default for new web apps in Visual Studio is **No Authentication** .</span></span>

<span data-ttu-id="5c20e-111">Projekty utworzone bez uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="5c20e-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="5c20e-112">Nie zawierają stron sieci Web i interfejsu użytkownika w celu zalogowania się i wylogowania.</span><span class="sxs-lookup"><span data-stu-id="5c20e-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="5c20e-113">Nie zawierają kodu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="5c20e-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="5c20e-114">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="5c20e-114">Windows Authentication</span></span>

<span data-ttu-id="5c20e-115">Uwierzytelnianie systemu Windows jest określone dla nowych aplikacji sieci Web w interfejs wiersza polecenia platformy .NET Core z `-au Windows` opcją.</span><span class="sxs-lookup"><span data-stu-id="5c20e-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="5c20e-116">W programie Visual Studio w oknie dialogowym **Zmienianie uwierzytelniania** dostępne są opcje **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="5c20e-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="5c20e-117">Jeśli wybrano opcję uwierzytelnianie systemu Windows, aplikacja jest skonfigurowana do korzystania z [modułu usług IIS uwierzytelniania systemu Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="5c20e-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="5c20e-118">Uwierzytelnianie systemu Windows jest przeznaczone dla intranetowych witryn sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5c20e-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="5c20e-119">Opcje uwierzytelniania w programie dotnet New webapp</span><span class="sxs-lookup"><span data-stu-id="5c20e-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="5c20e-120">W poniższej tabeli przedstawiono opcje uwierzytelniania dostępne dla nowych aplikacji sieci Web:</span><span class="sxs-lookup"><span data-stu-id="5c20e-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="5c20e-121">Opcja</span><span class="sxs-lookup"><span data-stu-id="5c20e-121">Option</span></span> | <span data-ttu-id="5c20e-122">Typ uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="5c20e-122">Type of authentication</span></span> | <span data-ttu-id="5c20e-123">Link umożliwiający uzyskanie dodatkowych informacji</span><span class="sxs-lookup"><span data-stu-id="5c20e-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="5c20e-124">Brak</span><span class="sxs-lookup"><span data-stu-id="5c20e-124">None</span></span>            |  <span data-ttu-id="5c20e-125">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="5c20e-125">No authentication</span></span> | | 
| <span data-ttu-id="5c20e-126">Szczegółowe</span><span class="sxs-lookup"><span data-stu-id="5c20e-126">Individual</span></span>      |  <span data-ttu-id="5c20e-127">Uwierzytelnianie indywidualne</span><span class="sxs-lookup"><span data-stu-id="5c20e-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="5c20e-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="5c20e-128">IndividualB2C</span></span>   |  <span data-ttu-id="5c20e-129">Uwierzytelnianie indywidualne hostowane w chmurze za pomocą Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5c20e-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="5c20e-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5c20e-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="5c20e-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="5c20e-131">SingleOrg</span></span>       |  <span data-ttu-id="5c20e-132">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="5c20e-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="5c20e-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="5c20e-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="5c20e-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="5c20e-134">MultiOrg</span></span>        |  <span data-ttu-id="5c20e-135">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="5c20e-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="5c20e-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="5c20e-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="5c20e-137">Windows</span><span class="sxs-lookup"><span data-stu-id="5c20e-137">Windows</span></span>         |  <span data-ttu-id="5c20e-138">Uwierzytelnianie Windows</span><span class="sxs-lookup"><span data-stu-id="5c20e-138">Windows authentication</span></span> | [<span data-ttu-id="5c20e-139">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="5c20e-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="5c20e-140">Opcje uwierzytelniania programu Visual Studio New webapp</span><span class="sxs-lookup"><span data-stu-id="5c20e-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="5c20e-141">W poniższej tabeli przedstawiono opcje uwierzytelniania dostępne podczas tworzenia nowej aplikacji sieci Web przy użyciu programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5c20e-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="5c20e-142">Opcja</span><span class="sxs-lookup"><span data-stu-id="5c20e-142">Option</span></span> | <span data-ttu-id="5c20e-143">Typ uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="5c20e-143">Type of authentication</span></span> | <span data-ttu-id="5c20e-144">Link umożliwiający uzyskanie dodatkowych informacji</span><span class="sxs-lookup"><span data-stu-id="5c20e-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="5c20e-145">Brak</span><span class="sxs-lookup"><span data-stu-id="5c20e-145">None</span></span>            |  <span data-ttu-id="5c20e-146">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="5c20e-146">No authentication</span></span> | | 
| <span data-ttu-id="5c20e-147">Konta użytkowników indywidualnych/konta użytkowników sklepu w aplikacji</span><span class="sxs-lookup"><span data-stu-id="5c20e-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="5c20e-148">Uwierzytelnianie indywidualne</span><span class="sxs-lookup"><span data-stu-id="5c20e-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="5c20e-149">Indywidualne konta użytkowników/łączenie z istniejącym magazynem użytkowników w chmurze</span><span class="sxs-lookup"><span data-stu-id="5c20e-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="5c20e-150">Uwierzytelnianie indywidualne hostowane w chmurze za pomocą Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5c20e-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="5c20e-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5c20e-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="5c20e-152">Chmura służbowa/jedna organizacja</span><span class="sxs-lookup"><span data-stu-id="5c20e-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="5c20e-153">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="5c20e-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="5c20e-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="5c20e-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="5c20e-155">Chmura służbowa/usługa szkoły i wiele organizacji</span><span class="sxs-lookup"><span data-stu-id="5c20e-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="5c20e-156">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="5c20e-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="5c20e-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="5c20e-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="5c20e-158">Windows</span><span class="sxs-lookup"><span data-stu-id="5c20e-158">Windows</span></span>         |  <span data-ttu-id="5c20e-159">Uwierzytelnianie Windows</span><span class="sxs-lookup"><span data-stu-id="5c20e-159">Windows authentication</span></span> | [<span data-ttu-id="5c20e-160">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="5c20e-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="5c20e-161">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5c20e-161">Additional resources</span></span>

<span data-ttu-id="5c20e-162">W poniższych artykułach pokazano, jak używać kodu wygenerowanego w szablonach ASP.NET Core, które używają poszczególnych kont użytkowników:</span><span class="sxs-lookup"><span data-stu-id="5c20e-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="5c20e-163">Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c20e-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="5c20e-164">Tworzenie aplikacji ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację</span><span class="sxs-lookup"><span data-stu-id="5c20e-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)

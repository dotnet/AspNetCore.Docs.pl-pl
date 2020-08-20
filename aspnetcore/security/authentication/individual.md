---
title: Artykuły oparte na ASP.NET Core projektach utworzonych przy użyciu poszczególnych kont użytkowników
author: rick-anderson
description: Odkryj artykuły na podstawie ASP.NET Core projektów utworzonych przy użyciu poszczególnych kont użytkowników.
ms.author: riande
ms.date: 12/11/2019
no-loc:
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
uid: security/authentication/individual
ms.openlocfilehash: 0cbde7cf0be830dab9dd094df6d102d81f9f0949
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632619"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="8462a-103">Artykuły oparte na ASP.NET Core projektach utworzonych przy użyciu poszczególnych kont użytkowników</span><span class="sxs-lookup"><span data-stu-id="8462a-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="8462a-104">ASP.NET Core Identity jest uwzględniony w szablonach projektu w programie Visual Studio z opcją "indywidualne konta użytkowników".</span><span class="sxs-lookup"><span data-stu-id="8462a-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="8462a-105">Szablony uwierzytelniania są dostępne w interfejs wiersza polecenia platformy .NET Core z `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="8462a-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

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

<span data-ttu-id="8462a-106">Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5833) w usłudze GitHub, aby uzyskać uwierzytelnianie interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8462a-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="8462a-107">Bez uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="8462a-107">No Authentication</span></span>

<span data-ttu-id="8462a-108">Uwierzytelnianie jest określone w interfejs wiersza polecenia platformy .NET Core z `-au` opcją.</span><span class="sxs-lookup"><span data-stu-id="8462a-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="8462a-109">W programie Visual Studio okno dialogowe **Zmienianie uwierzytelniania** jest dostępne dla nowych aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8462a-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="8462a-110">Wartość domyślna dla nowych aplikacji sieci Web w programie Visual Studio **nie jest uwierzytelnianiem**.</span><span class="sxs-lookup"><span data-stu-id="8462a-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="8462a-111">Projekty utworzone bez uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="8462a-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="8462a-112">Nie zawierają stron sieci Web i interfejsu użytkownika w celu zalogowania się i wylogowania.</span><span class="sxs-lookup"><span data-stu-id="8462a-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="8462a-113">Nie zawierają kodu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="8462a-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="8462a-114">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="8462a-114">Windows Authentication</span></span>

<span data-ttu-id="8462a-115">Uwierzytelnianie systemu Windows jest określone dla nowych aplikacji sieci Web w interfejs wiersza polecenia platformy .NET Core z `-au Windows` opcją.</span><span class="sxs-lookup"><span data-stu-id="8462a-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="8462a-116">W programie Visual Studio w oknie dialogowym **Zmienianie uwierzytelniania** dostępne są opcje **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="8462a-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="8462a-117">Jeśli wybrano opcję uwierzytelnianie systemu Windows, aplikacja jest skonfigurowana do korzystania z [modułu usług IIS uwierzytelniania systemu Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="8462a-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="8462a-118">Uwierzytelnianie systemu Windows jest przeznaczone dla intranetowych witryn sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8462a-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="8462a-119">Opcje uwierzytelniania w programie dotnet New webapp</span><span class="sxs-lookup"><span data-stu-id="8462a-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="8462a-120">W poniższej tabeli przedstawiono opcje uwierzytelniania dostępne dla nowych aplikacji sieci Web:</span><span class="sxs-lookup"><span data-stu-id="8462a-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="8462a-121">Opcja</span><span class="sxs-lookup"><span data-stu-id="8462a-121">Option</span></span> | <span data-ttu-id="8462a-122">Typ uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="8462a-122">Type of authentication</span></span> | <span data-ttu-id="8462a-123">Link umożliwiający uzyskanie dodatkowych informacji</span><span class="sxs-lookup"><span data-stu-id="8462a-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="8462a-124">Brak</span><span class="sxs-lookup"><span data-stu-id="8462a-124">None</span></span>            |  <span data-ttu-id="8462a-125">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="8462a-125">No authentication</span></span> | | 
| <span data-ttu-id="8462a-126">Szczegółowe</span><span class="sxs-lookup"><span data-stu-id="8462a-126">Individual</span></span>      |  <span data-ttu-id="8462a-127">Uwierzytelnianie indywidualne</span><span class="sxs-lookup"><span data-stu-id="8462a-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="8462a-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="8462a-128">IndividualB2C</span></span>   |  <span data-ttu-id="8462a-129">Uwierzytelnianie indywidualne hostowane w chmurze za pomocą Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="8462a-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="8462a-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="8462a-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="8462a-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="8462a-131">SingleOrg</span></span>       |  <span data-ttu-id="8462a-132">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="8462a-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="8462a-133">Usługa Azure AD</span><span class="sxs-lookup"><span data-stu-id="8462a-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="8462a-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="8462a-134">MultiOrg</span></span>        |  <span data-ttu-id="8462a-135">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="8462a-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="8462a-136">Usługa Azure AD</span><span class="sxs-lookup"><span data-stu-id="8462a-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="8462a-137">Windows</span><span class="sxs-lookup"><span data-stu-id="8462a-137">Windows</span></span>         |  <span data-ttu-id="8462a-138">Uwierzytelnianie Windows</span><span class="sxs-lookup"><span data-stu-id="8462a-138">Windows authentication</span></span> | [<span data-ttu-id="8462a-139">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="8462a-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="8462a-140">Opcje uwierzytelniania programu Visual Studio New webapp</span><span class="sxs-lookup"><span data-stu-id="8462a-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="8462a-141">W poniższej tabeli przedstawiono opcje uwierzytelniania dostępne podczas tworzenia nowej aplikacji sieci Web przy użyciu programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8462a-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="8462a-142">Opcja</span><span class="sxs-lookup"><span data-stu-id="8462a-142">Option</span></span> | <span data-ttu-id="8462a-143">Typ uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="8462a-143">Type of authentication</span></span> | <span data-ttu-id="8462a-144">Link umożliwiający uzyskanie dodatkowych informacji</span><span class="sxs-lookup"><span data-stu-id="8462a-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="8462a-145">Brak</span><span class="sxs-lookup"><span data-stu-id="8462a-145">None</span></span>            |  <span data-ttu-id="8462a-146">Brak uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="8462a-146">No authentication</span></span> | | 
| <span data-ttu-id="8462a-147">Konta użytkowników indywidualnych/konta użytkowników sklepu w aplikacji</span><span class="sxs-lookup"><span data-stu-id="8462a-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="8462a-148">Uwierzytelnianie indywidualne</span><span class="sxs-lookup"><span data-stu-id="8462a-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="8462a-149">Indywidualne konta użytkowników/łączenie z istniejącym magazynem użytkowników w chmurze</span><span class="sxs-lookup"><span data-stu-id="8462a-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="8462a-150">Uwierzytelnianie indywidualne hostowane w chmurze za pomocą Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="8462a-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="8462a-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="8462a-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="8462a-152">Chmura służbowa/jedna organizacja</span><span class="sxs-lookup"><span data-stu-id="8462a-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="8462a-153">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy</span><span class="sxs-lookup"><span data-stu-id="8462a-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="8462a-154">Usługa Azure AD</span><span class="sxs-lookup"><span data-stu-id="8462a-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="8462a-155">Chmura służbowa/usługa szkoły i wiele organizacji</span><span class="sxs-lookup"><span data-stu-id="8462a-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="8462a-156">Uwierzytelnianie organizacyjne dla wielu dzierżawców</span><span class="sxs-lookup"><span data-stu-id="8462a-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="8462a-157">Usługa Azure AD</span><span class="sxs-lookup"><span data-stu-id="8462a-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="8462a-158">Windows</span><span class="sxs-lookup"><span data-stu-id="8462a-158">Windows</span></span>         |  <span data-ttu-id="8462a-159">Uwierzytelnianie Windows</span><span class="sxs-lookup"><span data-stu-id="8462a-159">Windows authentication</span></span> | [<span data-ttu-id="8462a-160">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="8462a-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="8462a-161">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="8462a-161">Additional resources</span></span>

<span data-ttu-id="8462a-162">W poniższych artykułach pokazano, jak używać kodu wygenerowanego w szablonach ASP.NET Core, które używają poszczególnych kont użytkowników:</span><span class="sxs-lookup"><span data-stu-id="8462a-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="8462a-163">Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8462a-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="8462a-164">Tworzenie aplikacji ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację</span><span class="sxs-lookup"><span data-stu-id="8462a-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)

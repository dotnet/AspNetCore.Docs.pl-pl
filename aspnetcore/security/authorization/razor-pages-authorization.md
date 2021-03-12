---
title: Razor Konwencje autoryzacji stron w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak kontrolować dostęp do stron z konwencjami, które autoryzują użytkowników i umożliwiają anonimowym użytkownikom dostęp do stron lub folderów stron.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ffcb16b626773da69c45b8ab5dbd7c3cdc84bb5f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589117"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="b64e7-103">Razor Konwencje autoryzacji stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b64e7-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b64e7-104">Jednym ze sposobów kontroli dostępu w Razor aplikacji stron jest korzystanie z Konwencji autoryzacji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="b64e7-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="b64e7-105">Konwencje te umożliwiają Autoryzowanie użytkowników i Zezwalanie użytkownikom anonimowym na dostęp do poszczególnych stron lub folderów stron.</span><span class="sxs-lookup"><span data-stu-id="b64e7-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="b64e7-106">Konwencje opisane w tym temacie automatycznie stosują [filtry autoryzacji](xref:mvc/controllers/filters#authorization-filters) do kontroli dostępu.</span><span class="sxs-lookup"><span data-stu-id="b64e7-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="b64e7-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b64e7-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b64e7-108">Przykładowa aplikacja używa [ cookie uwierzytelniania bez ASP.NET Core Identity ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="b64e7-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="b64e7-109">Koncepcje i Przykłady przedstawione w tym temacie dotyczą również aplikacji, które używają programu ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b64e7-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="b64e7-110">Aby użyć programu ASP.NET Core Identity , postępuj zgodnie ze wskazówkami w temacie <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="b64e7-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="b64e7-111">Wymagaj autoryzacji dostępu do strony</span><span class="sxs-lookup"><span data-stu-id="b64e7-111">Require authorization to access a page</span></span>

<span data-ttu-id="b64e7-112">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do strony w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="b64e7-113">Określona ścieżka jest ścieżką aparatu widoku, która jest Razor ścieżką względną głównej ścieżki bez rozszerzenia i zawierającą tylko ukośniki.</span><span class="sxs-lookup"><span data-stu-id="b64e7-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="b64e7-114">Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="b64e7-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="b64e7-115"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Można zastosować do klasy modelu strony z `[Authorize]` atrybutem Filter.</span><span class="sxs-lookup"><span data-stu-id="b64e7-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="b64e7-116">Aby uzyskać więcej informacji, zobacz temat [Autoryzuj atrybut Filter](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="b64e7-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="b64e7-117">Wymagaj autoryzacji dostępu do folderu stron</span><span class="sxs-lookup"><span data-stu-id="b64e7-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="b64e7-118">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do wszystkich stron w folderze w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="b64e7-119">Określona ścieżka jest ścieżką aparatu widoku, czyli Razor ścieżką względną głównej strony.</span><span class="sxs-lookup"><span data-stu-id="b64e7-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="b64e7-120">Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="b64e7-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="b64e7-121">Wymagaj autoryzacji dostępu do strony obszaru</span><span class="sxs-lookup"><span data-stu-id="b64e7-121">Require authorization to access an area page</span></span>

<span data-ttu-id="b64e7-122">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do strony obszaru w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="b64e7-123">Nazwa strony jest ścieżką pliku bez rozszerzenia względem katalogu głównego stron określonego obszaru.</span><span class="sxs-lookup"><span data-stu-id="b64e7-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="b64e7-124">Na przykład nazwa strony dla *obszaru plik/ Identity /Pages/Manage/accounts.cshtml* to */Manage/accounts*.</span><span class="sxs-lookup"><span data-stu-id="b64e7-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="b64e7-125">Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="b64e7-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="b64e7-126">Wymagaj autoryzacji dostępu do folderu obszarów</span><span class="sxs-lookup"><span data-stu-id="b64e7-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="b64e7-127">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do wszystkich obszarów w folderze w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="b64e7-128">Ścieżka folderu to ścieżka folderu względem katalogu głównego stron określonego obszaru.</span><span class="sxs-lookup"><span data-stu-id="b64e7-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="b64e7-129">Na przykład ścieżka folderu dla plików w obszarze *obszary/ Identity /Pages/Manage/* to */Manage*.</span><span class="sxs-lookup"><span data-stu-id="b64e7-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="b64e7-130">Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="b64e7-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="b64e7-131">Zezwalaj na dostęp anonimowy do strony</span><span class="sxs-lookup"><span data-stu-id="b64e7-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="b64e7-132">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do strony w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="b64e7-133">Określona ścieżka jest ścieżką aparatu widoku, która jest Razor ścieżką względną głównej ścieżki bez rozszerzenia i zawierającą tylko ukośniki.</span><span class="sxs-lookup"><span data-stu-id="b64e7-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="b64e7-134">Zezwalaj na dostęp anonimowy do folderu stron</span><span class="sxs-lookup"><span data-stu-id="b64e7-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="b64e7-135">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do wszystkich stron w folderze w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="b64e7-136">Określona ścieżka jest ścieżką aparatu widoku, czyli Razor ścieżką względną głównej strony.</span><span class="sxs-lookup"><span data-stu-id="b64e7-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="b64e7-137">Uwaga na temat łączenia autoryzowanych i anonimowych dostępu</span><span class="sxs-lookup"><span data-stu-id="b64e7-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="b64e7-138">Należy określić, że folder stron wymaga autoryzacji, a następnie określić, że strona w tym folderze zezwala na dostęp anonimowy:</span><span class="sxs-lookup"><span data-stu-id="b64e7-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="b64e7-139">Odwrócenie jest jednak nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="b64e7-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="b64e7-140">Nie można zadeklarować folderu stron dla dostępu anonimowego, a następnie określić stronę w tym folderze, która wymaga autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="b64e7-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="b64e7-141">Wymaganie autoryzacji na stronie prywatnej kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b64e7-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="b64e7-142">Gdy obie <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> i <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> są stosowane do strony, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> ma pierwszeństwo i kontroluje dostęp.</span><span class="sxs-lookup"><span data-stu-id="b64e7-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b64e7-143">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b64e7-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b64e7-144">Jednym ze sposobów kontroli dostępu w Razor aplikacji stron jest korzystanie z Konwencji autoryzacji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="b64e7-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="b64e7-145">Konwencje te umożliwiają Autoryzowanie użytkowników i Zezwalanie użytkownikom anonimowym na dostęp do poszczególnych stron lub folderów stron.</span><span class="sxs-lookup"><span data-stu-id="b64e7-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="b64e7-146">Konwencje opisane w tym temacie automatycznie stosują [filtry autoryzacji](xref:mvc/controllers/filters#authorization-filters) do kontroli dostępu.</span><span class="sxs-lookup"><span data-stu-id="b64e7-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="b64e7-147">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b64e7-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b64e7-148">Przykładowa aplikacja używa [ cookie uwierzytelniania bez ASP.NET Core Identity ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="b64e7-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="b64e7-149">Koncepcje i Przykłady przedstawione w tym temacie dotyczą również aplikacji, które używają programu ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b64e7-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="b64e7-150">Aby użyć programu ASP.NET Core Identity , postępuj zgodnie ze wskazówkami w temacie <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="b64e7-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="b64e7-151">Wymagaj autoryzacji dostępu do strony</span><span class="sxs-lookup"><span data-stu-id="b64e7-151">Require authorization to access a page</span></span>

<span data-ttu-id="b64e7-152">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do strony w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="b64e7-153">Określona ścieżka jest ścieżką aparatu widoku, która jest Razor ścieżką względną głównej ścieżki bez rozszerzenia i zawierającą tylko ukośniki.</span><span class="sxs-lookup"><span data-stu-id="b64e7-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="b64e7-154">Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="b64e7-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="b64e7-155"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Można zastosować do klasy modelu strony z `[Authorize]` atrybutem Filter.</span><span class="sxs-lookup"><span data-stu-id="b64e7-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="b64e7-156">Aby uzyskać więcej informacji, zobacz temat [Autoryzuj atrybut Filter](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="b64e7-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="b64e7-157">Wymagaj autoryzacji dostępu do folderu stron</span><span class="sxs-lookup"><span data-stu-id="b64e7-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="b64e7-158">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do wszystkich stron w folderze w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="b64e7-159">Określona ścieżka jest ścieżką aparatu widoku, czyli Razor ścieżką względną głównej strony.</span><span class="sxs-lookup"><span data-stu-id="b64e7-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="b64e7-160">Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="b64e7-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="b64e7-161">Wymagaj autoryzacji dostępu do strony obszaru</span><span class="sxs-lookup"><span data-stu-id="b64e7-161">Require authorization to access an area page</span></span>

<span data-ttu-id="b64e7-162">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do strony obszaru w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="b64e7-163">Nazwa strony jest ścieżką pliku bez rozszerzenia względem katalogu głównego stron określonego obszaru.</span><span class="sxs-lookup"><span data-stu-id="b64e7-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="b64e7-164">Na przykład nazwa strony dla *obszaru plik/ Identity /Pages/Manage/accounts.cshtml* to */Manage/accounts*.</span><span class="sxs-lookup"><span data-stu-id="b64e7-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="b64e7-165">Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="b64e7-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="b64e7-166">Wymagaj autoryzacji dostępu do folderu obszarów</span><span class="sxs-lookup"><span data-stu-id="b64e7-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="b64e7-167">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do wszystkich obszarów w folderze w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="b64e7-168">Ścieżka folderu to ścieżka folderu względem katalogu głównego stron określonego obszaru.</span><span class="sxs-lookup"><span data-stu-id="b64e7-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="b64e7-169">Na przykład ścieżka folderu dla plików w obszarze *obszary/ Identity /Pages/Manage/* to */Manage*.</span><span class="sxs-lookup"><span data-stu-id="b64e7-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="b64e7-170">Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="b64e7-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="b64e7-171">Zezwalaj na dostęp anonimowy do strony</span><span class="sxs-lookup"><span data-stu-id="b64e7-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="b64e7-172">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do strony w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="b64e7-173">Określona ścieżka jest ścieżką aparatu widoku, która jest Razor ścieżką względną głównej ścieżki bez rozszerzenia i zawierającą tylko ukośniki.</span><span class="sxs-lookup"><span data-stu-id="b64e7-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="b64e7-174">Zezwalaj na dostęp anonimowy do folderu stron</span><span class="sxs-lookup"><span data-stu-id="b64e7-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="b64e7-175">Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do wszystkich stron w folderze w określonej ścieżce:</span><span class="sxs-lookup"><span data-stu-id="b64e7-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="b64e7-176">Określona ścieżka jest ścieżką aparatu widoku, czyli Razor ścieżką względną głównej strony.</span><span class="sxs-lookup"><span data-stu-id="b64e7-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="b64e7-177">Uwaga na temat łączenia autoryzowanych i anonimowych dostępu</span><span class="sxs-lookup"><span data-stu-id="b64e7-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="b64e7-178">Prawidłowe jest określenie, że folder stron, które wymagają autoryzacji, a nie określa, że strona w tym folderze zezwala na dostęp anonimowy:</span><span class="sxs-lookup"><span data-stu-id="b64e7-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="b64e7-179">Odwrócenie jest jednak nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="b64e7-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="b64e7-180">Nie można zadeklarować folderu stron dla dostępu anonimowego, a następnie określić stronę w tym folderze, która wymaga autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="b64e7-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="b64e7-181">Wymaganie autoryzacji na stronie prywatnej kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b64e7-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="b64e7-182">Gdy obie <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> i <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> są stosowane do strony, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> ma pierwszeństwo i kontroluje dostęp.</span><span class="sxs-lookup"><span data-stu-id="b64e7-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b64e7-183">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b64e7-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

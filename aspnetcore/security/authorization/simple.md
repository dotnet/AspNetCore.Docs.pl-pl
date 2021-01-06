---
title: Prosta autoryzacja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, w jaki sposób używać atrybutu Autoryzuj, aby ograniczyć dostęp do kontrolerów ASP.NET Core i akcji.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/simple
ms.openlocfilehash: 1678f1b4af2c65e3b10c66f7ccdbecf19156a834
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97865567"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="ffc94-103">Prosta autoryzacja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffc94-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="ffc94-104">Autoryzacja w ASP.NET Core jest kontrolowana przy użyciu <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> i jego różnych parametrów.</span><span class="sxs-lookup"><span data-stu-id="ffc94-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="ffc94-105">W najprostszej postaci stosowanie `[Authorize]` atrybutu do kontrolera, akcji lub Razor strony ogranicza dostęp do tego składnika do dowolnego uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ffc94-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="ffc94-106">Na przykład poniższy kod ogranicza dostęp do `AccountController` dowolnego uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ffc94-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="ffc94-107">Jeśli chcesz zastosować autoryzację do akcji, a nie kontrolera, Zastosuj `AuthorizeAttribute` atrybut do samej akcji:</span><span class="sxs-lookup"><span data-stu-id="ffc94-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="ffc94-108">Teraz tylko uwierzytelnieni użytkownicy mogą uzyskiwać dostęp do `Logout` funkcji.</span><span class="sxs-lookup"><span data-stu-id="ffc94-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="ffc94-109">Można również użyć atrybutu, `AllowAnonymous` Aby zezwolić na dostęp nieuwierzytelnionym użytkownikom do poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="ffc94-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="ffc94-110">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ffc94-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="ffc94-111">Może to umożliwić tylko uwierzytelnionym użytkownikom `AccountController` , z wyjątkiem `Login` akcji, która jest dostępna dla wszystkich, niezależnie od ich uwierzytelnionego lub nieuwierzytelnionego/anonimowego stanu.</span><span class="sxs-lookup"><span data-stu-id="ffc94-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="ffc94-112">`[AllowAnonymous]` pomija wszystkie instrukcje autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ffc94-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="ffc94-113">W przypadku łączenia `[AllowAnonymous]` i dowolnych atrybutów `[Authorize]` `[Authorize]` atrybuty zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="ffc94-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="ffc94-114">Na przykład w przypadku zastosowania `[AllowAnonymous]` na poziomie kontrolera wszystkie `[Authorize]` atrybuty na tym samym kontrolerze (lub na dowolnej akcji w ramach tego elementu) zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="ffc94-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a><span data-ttu-id="ffc94-115">Autoryzuj atrybut i Razor strony</span><span class="sxs-lookup"><span data-stu-id="ffc94-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="ffc94-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>**Nie** można zastosować do Razor programów obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="ffc94-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can \***not** _ be applied to Razor Page handlers.</span></span> <span data-ttu-id="ffc94-117">Na przykład `[Authorize]` nie można zastosować do `OnGet` `OnPost` programu obsługi stron ani żadnej z nich.</span><span class="sxs-lookup"><span data-stu-id="ffc94-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="ffc94-118">Rozważ użycie kontrolera ASP.NET Core MVC dla stron z różnymi wymaganiami dotyczącymi autoryzacji dla różnych programów obsługi.</span><span class="sxs-lookup"><span data-stu-id="ffc94-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="ffc94-119">Poniższe dwa podejścia mogą służyć do stosowania autoryzacji do Razor metod obsługi stron:</span><span class="sxs-lookup"><span data-stu-id="ffc94-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

<span data-ttu-id="ffc94-120">_ Użyj oddzielnych stron dla programów obsługi stron wymagających innej autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ffc94-120">_ Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="ffc94-121">Przenieś zawartość udostępnioną do co najmniej jednego [widoku częściowego](xref:mvc/views/partial).</span><span class="sxs-lookup"><span data-stu-id="ffc94-121">Move shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="ffc94-122">Jeśli to możliwe, jest to zalecane podejście.</span><span class="sxs-lookup"><span data-stu-id="ffc94-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="ffc94-123">W przypadku zawartości, która musi udostępniać wspólną stronę, należy napisać filtr wykonujący autoryzację jako część [IAsyncPageFilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="ffc94-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="ffc94-124">Projekt [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub ilustruje następujące podejście:</span><span class="sxs-lookup"><span data-stu-id="ffc94-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="ffc94-125">[AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implementuje filtr autoryzacji:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="ffc94-125">The [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="ffc94-126">Atrybut [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) jest stosowany do `OnGet` obsługi stron: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="ffc94-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="ffc94-127">Podejście przykładowe [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) **nie może** składać się z atrybutów autoryzacji zastosowanych do strony, modelu strony lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="ffc94-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does \***not** _: _ Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="ffc94-128">Tworzenie atrybutów autoryzacji powoduje, że uwierzytelnianie i autoryzacja są wykonywane wiele razy, gdy tylko jeden z nich jest `AuthorizeAttribute` `AuthorizeFilter` również stosowany do strony.</span><span class="sxs-lookup"><span data-stu-id="ffc94-128">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="ffc94-129">Pracuj w połączeniu z pozostałą częścią systemu uwierzytelniania i autoryzacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ffc94-129">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="ffc94-130">Należy sprawdzić, czy użycie tego podejścia działa prawidłowo dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ffc94-130">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="ffc94-131">Brak planów do obsługi `AuthorizeAttribute` Razor stron obsługi na stronie.</span><span class="sxs-lookup"><span data-stu-id="ffc94-131">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 

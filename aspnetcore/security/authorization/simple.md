---
<span data-ttu-id="c5031-101">title: prosta autoryzacja w ASP.NET Core Author: Rick-Anderson Description: Dowiedz się, jak używać atrybutu Autoryzuj, aby ograniczyć dostęp do kontrolerów ASP.NET Core i akcji.</span><span class="sxs-lookup"><span data-stu-id="c5031-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="c5031-102">MS. Author: Riande MS. Date: 10/14/2016 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c5031-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="c5031-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c5031-103">'Blazor'</span></span>
- <span data-ttu-id="c5031-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c5031-104">'Identity'</span></span>
- <span data-ttu-id="c5031-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c5031-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c5031-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c5031-106">'Razor'</span></span>
- <span data-ttu-id="c5031-107">" SignalR UID: zabezpieczenia/autoryzacja/proste</span><span class="sxs-lookup"><span data-stu-id="c5031-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="c5031-108">Prosta autoryzacja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5031-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="c5031-109">Autoryzacja w ASP.NET Core jest kontrolowana przy użyciu <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> i jego różnych parametrów.</span><span class="sxs-lookup"><span data-stu-id="c5031-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="c5031-110">W najprostszej postaci stosowanie `[Authorize]` atrybutu do kontrolera, akcji lub Razor strony ogranicza dostęp do tego składnika do dowolnego uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c5031-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="c5031-111">Na przykład poniższy kod ogranicza dostęp do `AccountController` dowolnego uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c5031-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="c5031-112">Jeśli chcesz zastosować autoryzację do akcji, a nie kontrolera, Zastosuj `AuthorizeAttribute` atrybut do samej akcji:</span><span class="sxs-lookup"><span data-stu-id="c5031-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="c5031-113">Teraz tylko uwierzytelnieni użytkownicy mogą uzyskiwać dostęp do `Logout` funkcji.</span><span class="sxs-lookup"><span data-stu-id="c5031-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="c5031-114">Można również użyć atrybutu, `AllowAnonymous` Aby zezwolić na dostęp nieuwierzytelnionym użytkownikom do poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="c5031-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="c5031-115">Przykład:</span><span class="sxs-lookup"><span data-stu-id="c5031-115">For example:</span></span>

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

<span data-ttu-id="c5031-116">Może to umożliwić tylko uwierzytelnionym użytkownikom `AccountController` , z wyjątkiem `Login` akcji, która jest dostępna dla wszystkich, niezależnie od ich uwierzytelnionego lub nieuwierzytelnionego/anonimowego stanu.</span><span class="sxs-lookup"><span data-stu-id="c5031-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="c5031-117">`[AllowAnonymous]`pomija wszystkie instrukcje autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="c5031-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="c5031-118">W przypadku łączenia `[AllowAnonymous]` i dowolnych atrybutów `[Authorize]` `[Authorize]` atrybuty zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="c5031-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="c5031-119">Na przykład w przypadku zastosowania `[AllowAnonymous]` na poziomie kontrolera wszystkie `[Authorize]` atrybuty na tym samym kontrolerze (lub na dowolnej akcji w ramach tego elementu) zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="c5031-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

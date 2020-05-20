---
<span data-ttu-id="811ec-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="811ec-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="811ec-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="811ec-102">'Blazor'</span></span>
- <span data-ttu-id="811ec-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="811ec-103">'Identity'</span></span>
- <span data-ttu-id="811ec-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="811ec-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="811ec-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="811ec-105">'Razor'</span></span>
- <span data-ttu-id="811ec-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="811ec-106">'SignalR' uid:</span></span> 

---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="811ec-107">Napisz niestandardowe oprogramowanie pośredniczące ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="811ec-107">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="811ec-108">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="811ec-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="811ec-109">Oprogramowanie pośredniczące jest oprogramowaniem, które jest połączone z potokiem aplikacji w celu obsługi żądań i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="811ec-109">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="811ec-110">ASP.NET Core udostępnia bogaty zestaw wbudowanych komponentów oprogramowania pośredniczącego, ale w niektórych scenariuszach warto napisać niestandardowe oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="811ec-110">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="811ec-111">W tym temacie opisano sposób pisania oprogramowania pośredniczącego *opartego na konwencjach* .</span><span class="sxs-lookup"><span data-stu-id="811ec-111">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="811ec-112">W przypadku podejścia korzystającego z silnego wpisywania i aktywacji na żądanie, zobacz <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="811ec-112">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="811ec-113">Pośrednicząca Klasa oprogramowania</span><span class="sxs-lookup"><span data-stu-id="811ec-113">Middleware class</span></span>

<span data-ttu-id="811ec-114">Oprogramowanie pośredniczące jest zazwyczaj hermetyzowane w klasie i udostępniane przy użyciu metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="811ec-114">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="811ec-115">Rozważ następujące oprogramowanie pośredniczące, które ustawia kulturę dla bieżącego żądania z ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="811ec-115">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="811ec-116">Poprzedni przykładowy kod służy do zademonstrowania tworzenia składnika pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="811ec-116">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="811ec-117">Aby uzyskać wbudowaną obsługę lokalizacji ASP.NET Core, zobacz <xref:fundamentals/localization> .</span><span class="sxs-lookup"><span data-stu-id="811ec-117">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="811ec-118">Przetestuj oprogramowanie pośredniczące, przekazując kulturę.</span><span class="sxs-lookup"><span data-stu-id="811ec-118">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="811ec-119">Na przykład żądanie `https://localhost:5001/?culture=no` .</span><span class="sxs-lookup"><span data-stu-id="811ec-119">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="811ec-120">Poniższy kod przenosi delegata oprogramowania pośredniczącego do klasy:</span><span class="sxs-lookup"><span data-stu-id="811ec-120">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="811ec-121">Klasa pośrednicząca musi zawierać następujące:</span><span class="sxs-lookup"><span data-stu-id="811ec-121">The middleware class must include:</span></span>

* <span data-ttu-id="811ec-122">Konstruktor publiczny z parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate> .</span><span class="sxs-lookup"><span data-stu-id="811ec-122">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="811ec-123">Metoda publiczna o nazwie `Invoke` lub `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="811ec-123">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="811ec-124">Ta metoda musi:</span><span class="sxs-lookup"><span data-stu-id="811ec-124">This method must:</span></span>
  * <span data-ttu-id="811ec-125">Zwróć `Task` .</span><span class="sxs-lookup"><span data-stu-id="811ec-125">Return a `Task`.</span></span>
  * <span data-ttu-id="811ec-126">Zaakceptuj pierwszy parametr typu <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="811ec-126">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="811ec-127">Dodatkowe parametry dla konstruktora i `Invoke` / `InvokeAsync` są wypełniane przez [iniekcję zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="811ec-127">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="811ec-128">Zależności oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="811ec-128">Middleware dependencies</span></span>

<span data-ttu-id="811ec-129">Oprogramowanie pośredniczące powinno postępować zgodnie z [zasadą jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) przez ujawnienie jej zależności w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="811ec-129">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="811ec-130">Oprogramowanie pośredniczące jest zbudowane raz dla *okresu istnienia aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="811ec-130">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="811ec-131">Zapoznaj się z sekcją [zależności oprogramowania pośredniczącego na żądanie](#per-request-middleware-dependencies) , jeśli chcesz udostępnić usługi za pomocą oprogramowania pośredniczącego w ramach żądania.</span><span class="sxs-lookup"><span data-stu-id="811ec-131">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="811ec-132">Składniki pośredniczące mogą rozpoznać zależności od [iniekcji zależności (di)](xref:fundamentals/dependency-injection) przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="811ec-132">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="811ec-133">[UseMiddleware &lt; T &gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) mogą również akceptować dodatkowe parametry bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="811ec-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="811ec-134">Zależności oprogramowania pośredniczącego na żądanie</span><span class="sxs-lookup"><span data-stu-id="811ec-134">Per-request middleware dependencies</span></span>

<span data-ttu-id="811ec-135">Ponieważ oprogramowanie pośredniczące jest zbudowane przy uruchamianiu aplikacji, a nie na żądanie, usługi okresu istnienia w *zasięgu* , które są używane przez konstruktory oprogramowania pośredniczącego, nie są udostępniane innym typem z wstrzykiwanymi zależnościami podczas każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="811ec-135">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="811ec-136">Jeśli musisz udostępnić usługę z *zakresem* w ramach oprogramowania pośredniczącego i innych typów, Dodaj te usługi do `Invoke` sygnatury metody.</span><span class="sxs-lookup"><span data-stu-id="811ec-136">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="811ec-137">`Invoke`Metoda może akceptować dodatkowe parametry, które są wypełniane przez di:</span><span class="sxs-lookup"><span data-stu-id="811ec-137">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="811ec-138">Metoda rozszerzenia oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="811ec-138">Middleware extension method</span></span>

<span data-ttu-id="811ec-139">Następujące metody rozszerzające udostępniają oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> :</span><span class="sxs-lookup"><span data-stu-id="811ec-139">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="811ec-140">Poniższy kod wywołuje oprogramowanie pośredniczące z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="811ec-140">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="811ec-141">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="811ec-141">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

---
<span data-ttu-id="46444-101">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-102">'Blazor'</span></span>
- <span data-ttu-id="46444-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-103">'Identity'</span></span>
- <span data-ttu-id="46444-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-105">'Razor'</span></span>
- <span data-ttu-id="46444-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="46444-107">Buforowanie odpowiedzi w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46444-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="46444-108">[John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="46444-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="46444-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="46444-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="46444-110">Buforowanie odpowiedzi zmniejsza liczbę żądań wysyłanych przez klienta lub serwer proxy do serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="46444-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="46444-111">Buforowanie odpowiedzi zmniejsza również ilość pracy wykonywanej przez serwer sieci Web w celu wygenerowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="46444-112">Buforowanie odpowiedzi jest kontrolowane przez nagłówki, które określają sposób, w jaki klient, serwer proxy i oprogramowanie pośredniczące buforują odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="46444-113">[Atrybut ResponseCache](#responsecache-attribute) uczestniczy w ustawianiu nagłówków buforowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="46444-114">Klienci i pośredniczące proxy powinny przestrzegać nagłówków do buforowania odpowiedzi w [specyfikacji buforowania HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="46444-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="46444-115">W przypadku buforowania po stronie serwera, które następuje zgodnie ze specyfikacją buforowania HTTP 1,1, należy użyć [oprogramowania pośredniczącego buforowania odpowiedzi](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="46444-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="46444-116">Oprogramowanie pośredniczące może używać <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> właściwości, aby mieć wpływ na zachowanie buforowania po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="46444-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="46444-117">Buforowanie odpowiedzi oparte na protokole HTTP</span><span class="sxs-lookup"><span data-stu-id="46444-117">HTTP-based response caching</span></span>

<span data-ttu-id="46444-118">[Specyfikacja buforowania HTTP 1,1](https://tools.ietf.org/html/rfc7234) opisuje, jak należy zachować pamięć podręczną Internetu.</span><span class="sxs-lookup"><span data-stu-id="46444-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="46444-119">Podstawowy nagłówek HTTP używany do buforowania to [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), który jest używany do określania *dyrektyw*pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="46444-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="46444-120">Dyrektywy kontrolują zachowanie pamięci podręcznej w miarę jak żądania skierowane do klientów na serwery i jako odpowiedzi sprawiają, że serwery są z powrotem do klientów.</span><span class="sxs-lookup"><span data-stu-id="46444-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="46444-121">Żądania i odpowiedzi przechodzą przez serwery proxy, a serwery proxy muszą również być zgodne ze specyfikacją buforowania HTTP 1,1.</span><span class="sxs-lookup"><span data-stu-id="46444-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="46444-122">Wspólne `Cache-Control` dyrektywy przedstawiono w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="46444-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="46444-123">Dyrektywę</span><span class="sxs-lookup"><span data-stu-id="46444-123">Directive</span></span>                                                       | <span data-ttu-id="46444-124">Akcja</span><span class="sxs-lookup"><span data-stu-id="46444-124">Action</span></span> |
| ---
<span data-ttu-id="46444-125">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-126">'Blazor'</span></span>
- <span data-ttu-id="46444-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-127">'Identity'</span></span>
- <span data-ttu-id="46444-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-129">'Razor'</span></span>
- <span data-ttu-id="46444-130">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-131">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-132">'Blazor'</span></span>
- <span data-ttu-id="46444-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-133">'Identity'</span></span>
- <span data-ttu-id="46444-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-135">'Razor'</span></span>
- <span data-ttu-id="46444-136">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-137">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-138">'Blazor'</span></span>
- <span data-ttu-id="46444-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-139">'Identity'</span></span>
- <span data-ttu-id="46444-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-141">'Razor'</span></span>
- <span data-ttu-id="46444-142">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-143">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-144">'Blazor'</span></span>
- <span data-ttu-id="46444-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-145">'Identity'</span></span>
- <span data-ttu-id="46444-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-147">'Razor'</span></span>
- <span data-ttu-id="46444-148">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-149">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-150">'Blazor'</span></span>
- <span data-ttu-id="46444-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-151">'Identity'</span></span>
- <span data-ttu-id="46444-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-153">'Razor'</span></span>
- <span data-ttu-id="46444-154">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-155">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-156">'Blazor'</span></span>
- <span data-ttu-id="46444-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-157">'Identity'</span></span>
- <span data-ttu-id="46444-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-159">'Razor'</span></span>
- <span data-ttu-id="46444-160">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-161">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-162">'Blazor'</span></span>
- <span data-ttu-id="46444-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-163">'Identity'</span></span>
- <span data-ttu-id="46444-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-165">'Razor'</span></span>
- <span data-ttu-id="46444-166">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-167">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-168">'Blazor'</span></span>
- <span data-ttu-id="46444-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-169">'Identity'</span></span>
- <span data-ttu-id="46444-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-171">'Razor'</span></span>
- <span data-ttu-id="46444-172">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-173">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-174">'Blazor'</span></span>
- <span data-ttu-id="46444-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-175">'Identity'</span></span>
- <span data-ttu-id="46444-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-177">'Razor'</span></span>
- <span data-ttu-id="46444-178">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-179">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-180">'Blazor'</span></span>
- <span data-ttu-id="46444-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-181">'Identity'</span></span>
- <span data-ttu-id="46444-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-183">'Razor'</span></span>
- <span data-ttu-id="46444-184">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-185">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-186">'Blazor'</span></span>
- <span data-ttu-id="46444-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-187">'Identity'</span></span>
- <span data-ttu-id="46444-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-189">'Razor'</span></span>
- <span data-ttu-id="46444-190">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-191">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-192">'Blazor'</span></span>
- <span data-ttu-id="46444-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-193">'Identity'</span></span>
- <span data-ttu-id="46444-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-195">'Razor'</span></span>
- <span data-ttu-id="46444-196">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-197">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-198">'Blazor'</span></span>
- <span data-ttu-id="46444-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-199">'Identity'</span></span>
- <span data-ttu-id="46444-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-201">'Razor'</span></span>
- <span data-ttu-id="46444-202">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-203">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-204">'Blazor'</span></span>
- <span data-ttu-id="46444-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-205">'Identity'</span></span>
- <span data-ttu-id="46444-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-207">'Razor'</span></span>
- <span data-ttu-id="46444-208">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-209">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-210">'Blazor'</span></span>
- <span data-ttu-id="46444-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-211">'Identity'</span></span>
- <span data-ttu-id="46444-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-213">'Razor'</span></span>
- <span data-ttu-id="46444-214">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-215">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-216">'Blazor'</span></span>
- <span data-ttu-id="46444-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-217">'Identity'</span></span>
- <span data-ttu-id="46444-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-219">'Razor'</span></span>
- <span data-ttu-id="46444-220">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-221">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-222">'Blazor'</span></span>
- <span data-ttu-id="46444-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-223">'Identity'</span></span>
- <span data-ttu-id="46444-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-225">'Razor'</span></span>
- <span data-ttu-id="46444-226">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-227">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-228">'Blazor'</span></span>
- <span data-ttu-id="46444-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-229">'Identity'</span></span>
- <span data-ttu-id="46444-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-231">'Razor'</span></span>
- <span data-ttu-id="46444-232">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-233">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-234">'Blazor'</span></span>
- <span data-ttu-id="46444-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-235">'Identity'</span></span>
- <span data-ttu-id="46444-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-237">'Razor'</span></span>
- <span data-ttu-id="46444-238">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-239">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-240">'Blazor'</span></span>
- <span data-ttu-id="46444-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-241">'Identity'</span></span>
- <span data-ttu-id="46444-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-243">'Razor'</span></span>
- <span data-ttu-id="46444-244">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-245">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-246">'Blazor'</span></span>
- <span data-ttu-id="46444-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-247">'Identity'</span></span>
- <span data-ttu-id="46444-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-249">'Razor'</span></span>
- <span data-ttu-id="46444-250">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-251">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-252">'Blazor'</span></span>
- <span data-ttu-id="46444-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-253">'Identity'</span></span>
- <span data-ttu-id="46444-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-255">'Razor'</span></span>
- <span data-ttu-id="46444-256">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-257">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-258">'Blazor'</span></span>
- <span data-ttu-id="46444-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-259">'Identity'</span></span>
- <span data-ttu-id="46444-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-261">'Razor'</span></span>
- <span data-ttu-id="46444-262">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-263">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-264">'Blazor'</span></span>
- <span data-ttu-id="46444-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-265">'Identity'</span></span>
- <span data-ttu-id="46444-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-267">'Razor'</span></span>
- <span data-ttu-id="46444-268">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-269">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-270">'Blazor'</span></span>
- <span data-ttu-id="46444-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-271">'Identity'</span></span>
- <span data-ttu-id="46444-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-273">'Razor'</span></span>
- <span data-ttu-id="46444-274">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-275">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-276">'Blazor'</span></span>
- <span data-ttu-id="46444-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-277">'Identity'</span></span>
- <span data-ttu-id="46444-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-279">'Razor'</span></span>
- <span data-ttu-id="46444-280">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-281">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-282">'Blazor'</span></span>
- <span data-ttu-id="46444-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-283">'Identity'</span></span>
- <span data-ttu-id="46444-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-285">'Razor'</span></span>
- <span data-ttu-id="46444-286">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-287">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-288">'Blazor'</span></span>
- <span data-ttu-id="46444-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-289">'Identity'</span></span>
- <span data-ttu-id="46444-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-291">'Razor'</span></span>
- <span data-ttu-id="46444-292">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-293">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-294">'Blazor'</span></span>
- <span data-ttu-id="46444-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-295">'Identity'</span></span>
- <span data-ttu-id="46444-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-297">'Razor'</span></span>
- <span data-ttu-id="46444-298">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-298">'SignalR' uid:</span></span> 

<span data-ttu-id="46444-299">-------------------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-300">'Blazor'</span></span>
- <span data-ttu-id="46444-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-301">'Identity'</span></span>
- <span data-ttu-id="46444-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-303">'Razor'</span></span>
- <span data-ttu-id="46444-304">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-304">'SignalR' uid:</span></span> 

<span data-ttu-id="46444-305">--- | | [publiczny](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Pamięć podręczna może przechowywać odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="46444-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="46444-306">| | [prywatny](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | Odpowiedź nie może być przechowywana w udostępnionej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="46444-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="46444-307">Prywatna pamięć podręczna może przechowywać i ponownie używać odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="46444-308">| | [maks. wiek](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | Klient nie akceptuje odpowiedzi, której wiek jest większy niż określona liczba sekund.</span><span class="sxs-lookup"><span data-stu-id="46444-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="46444-309">Przykłady: `max-age=60` (60 sekund), `max-age=2592000` (1 miesiąc) | | [nie-pamięć podręczna](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **Na**żądanie: pamięć podręczna nie może używać zapisanej odpowiedzi w celu spełnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="46444-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="46444-310">Serwer pierwotny ponownie generuje odpowiedź dla klienta, a oprogramowanie pośredniczące aktualizuje zapisaną odpowiedź w jej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="46444-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="46444-311">**W odpowiedzi**: nie można używać odpowiedzi dla kolejnych żądań bez sprawdzania poprawności na serwerze źródłowym.</span><span class="sxs-lookup"><span data-stu-id="46444-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="46444-312">| | [bez sklepu](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **Żądania: pamięć**podręczna nie może przechowywać żądania.</span><span class="sxs-lookup"><span data-stu-id="46444-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="46444-313">**Odpowiedzi**: pamięć podręczna nie może przechowywać żadnej części odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="46444-314">W poniższej tabeli przedstawiono inne nagłówki pamięci podręcznej, które odgrywają rolę w buforowanie.</span><span class="sxs-lookup"><span data-stu-id="46444-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="46444-315">Header</span><span class="sxs-lookup"><span data-stu-id="46444-315">Header</span></span>                                                     | <span data-ttu-id="46444-316">Funkcja</span><span class="sxs-lookup"><span data-stu-id="46444-316">Function</span></span> |
| ---
<span data-ttu-id="46444-317">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-318">'Blazor'</span></span>
- <span data-ttu-id="46444-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-319">'Identity'</span></span>
- <span data-ttu-id="46444-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-321">'Razor'</span></span>
- <span data-ttu-id="46444-322">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-323">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-324">'Blazor'</span></span>
- <span data-ttu-id="46444-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-325">'Identity'</span></span>
- <span data-ttu-id="46444-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-327">'Razor'</span></span>
- <span data-ttu-id="46444-328">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-329">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-330">'Blazor'</span></span>
- <span data-ttu-id="46444-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-331">'Identity'</span></span>
- <span data-ttu-id="46444-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-333">'Razor'</span></span>
- <span data-ttu-id="46444-334">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-335">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-336">'Blazor'</span></span>
- <span data-ttu-id="46444-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-337">'Identity'</span></span>
- <span data-ttu-id="46444-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-339">'Razor'</span></span>
- <span data-ttu-id="46444-340">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-341">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-342">'Blazor'</span></span>
- <span data-ttu-id="46444-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-343">'Identity'</span></span>
- <span data-ttu-id="46444-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-345">'Razor'</span></span>
- <span data-ttu-id="46444-346">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-347">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-348">'Blazor'</span></span>
- <span data-ttu-id="46444-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-349">'Identity'</span></span>
- <span data-ttu-id="46444-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-351">'Razor'</span></span>
- <span data-ttu-id="46444-352">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-353">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-354">'Blazor'</span></span>
- <span data-ttu-id="46444-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-355">'Identity'</span></span>
- <span data-ttu-id="46444-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-357">'Razor'</span></span>
- <span data-ttu-id="46444-358">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-359">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-360">'Blazor'</span></span>
- <span data-ttu-id="46444-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-361">'Identity'</span></span>
- <span data-ttu-id="46444-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-363">'Razor'</span></span>
- <span data-ttu-id="46444-364">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-365">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-366">'Blazor'</span></span>
- <span data-ttu-id="46444-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-367">'Identity'</span></span>
- <span data-ttu-id="46444-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-369">'Razor'</span></span>
- <span data-ttu-id="46444-370">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-371">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-372">'Blazor'</span></span>
- <span data-ttu-id="46444-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-373">'Identity'</span></span>
- <span data-ttu-id="46444-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-375">'Razor'</span></span>
- <span data-ttu-id="46444-376">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-377">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-378">'Blazor'</span></span>
- <span data-ttu-id="46444-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-379">'Identity'</span></span>
- <span data-ttu-id="46444-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-381">'Razor'</span></span>
- <span data-ttu-id="46444-382">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-383">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-384">'Blazor'</span></span>
- <span data-ttu-id="46444-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-385">'Identity'</span></span>
- <span data-ttu-id="46444-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-387">'Razor'</span></span>
- <span data-ttu-id="46444-388">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-389">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-390">'Blazor'</span></span>
- <span data-ttu-id="46444-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-391">'Identity'</span></span>
- <span data-ttu-id="46444-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-393">'Razor'</span></span>
- <span data-ttu-id="46444-394">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-395">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-396">'Blazor'</span></span>
- <span data-ttu-id="46444-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-397">'Identity'</span></span>
- <span data-ttu-id="46444-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-399">'Razor'</span></span>
- <span data-ttu-id="46444-400">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-401">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-402">'Blazor'</span></span>
- <span data-ttu-id="46444-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-403">'Identity'</span></span>
- <span data-ttu-id="46444-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-405">'Razor'</span></span>
- <span data-ttu-id="46444-406">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-407">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-408">'Blazor'</span></span>
- <span data-ttu-id="46444-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-409">'Identity'</span></span>
- <span data-ttu-id="46444-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-411">'Razor'</span></span>
- <span data-ttu-id="46444-412">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-413">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-414">'Blazor'</span></span>
- <span data-ttu-id="46444-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-415">'Identity'</span></span>
- <span data-ttu-id="46444-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-417">'Razor'</span></span>
- <span data-ttu-id="46444-418">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-419">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-420">'Blazor'</span></span>
- <span data-ttu-id="46444-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-421">'Identity'</span></span>
- <span data-ttu-id="46444-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-423">'Razor'</span></span>
- <span data-ttu-id="46444-424">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-425">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-426">'Blazor'</span></span>
- <span data-ttu-id="46444-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-427">'Identity'</span></span>
- <span data-ttu-id="46444-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-429">'Razor'</span></span>
- <span data-ttu-id="46444-430">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-431">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-432">'Blazor'</span></span>
- <span data-ttu-id="46444-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-433">'Identity'</span></span>
- <span data-ttu-id="46444-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-435">'Razor'</span></span>
- <span data-ttu-id="46444-436">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-437">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-438">'Blazor'</span></span>
- <span data-ttu-id="46444-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-439">'Identity'</span></span>
- <span data-ttu-id="46444-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-441">'Razor'</span></span>
- <span data-ttu-id="46444-442">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-443">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-444">'Blazor'</span></span>
- <span data-ttu-id="46444-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-445">'Identity'</span></span>
- <span data-ttu-id="46444-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-447">'Razor'</span></span>
- <span data-ttu-id="46444-448">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-449">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-450">'Blazor'</span></span>
- <span data-ttu-id="46444-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-451">'Identity'</span></span>
- <span data-ttu-id="46444-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-453">'Razor'</span></span>
- <span data-ttu-id="46444-454">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-455">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-456">'Blazor'</span></span>
- <span data-ttu-id="46444-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-457">'Identity'</span></span>
- <span data-ttu-id="46444-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-459">'Razor'</span></span>
- <span data-ttu-id="46444-460">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-461">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-462">'Blazor'</span></span>
- <span data-ttu-id="46444-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-463">'Identity'</span></span>
- <span data-ttu-id="46444-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-465">'Razor'</span></span>
- <span data-ttu-id="46444-466">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-467">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-468">'Blazor'</span></span>
- <span data-ttu-id="46444-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-469">'Identity'</span></span>
- <span data-ttu-id="46444-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-471">'Razor'</span></span>
- <span data-ttu-id="46444-472">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-473">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-474">'Blazor'</span></span>
- <span data-ttu-id="46444-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-475">'Identity'</span></span>
- <span data-ttu-id="46444-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-477">'Razor'</span></span>
- <span data-ttu-id="46444-478">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-478">'SignalR' uid:</span></span> 

<span data-ttu-id="46444-479">----------------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-480">'Blazor'</span></span>
- <span data-ttu-id="46444-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-481">'Identity'</span></span>
- <span data-ttu-id="46444-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-483">'Razor'</span></span>
- <span data-ttu-id="46444-484">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-485">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-486">'Blazor'</span></span>
- <span data-ttu-id="46444-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-487">'Identity'</span></span>
- <span data-ttu-id="46444-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-489">'Razor'</span></span>
- <span data-ttu-id="46444-490">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-490">'SignalR' uid:</span></span> 

<span data-ttu-id="46444-491">---- | | [Wiek](https://tools.ietf.org/html/rfc7234#section-5.1) | Oszacowanie czasu (w sekundach), po którym odpowiedź została wygenerowana lub pomyślnie zweryfikowana na serwerze pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="46444-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="46444-492">| | [Wygasa](https://tools.ietf.org/html/rfc7234#section-5.3) | Czas, po którym odpowiedź jest uważana za przestarzałą.</span><span class="sxs-lookup"><span data-stu-id="46444-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="46444-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Istnieje w celu zachowania zgodności z poprzednimi wersjami z pamięcią podręczną protokołu HTTP/1.0 na potrzeby ustawienia `no-cache` zachowania.</span><span class="sxs-lookup"><span data-stu-id="46444-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="46444-494">Jeśli `Cache-Control` nagłówek jest obecny, `Pragma` nagłówek jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="46444-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="46444-495">| | [Różne](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Określa, że nie można wysyłać buforowanej odpowiedzi, chyba że wszystkie `Vary` pola nagłówka są zgodne z oryginalnym żądaniem odpowiedzi w pamięci podręcznej i nowym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="46444-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="46444-496">Buforowanie oparte na protokole HTTP uwzględnia dyrektywy kontroli pamięci podręcznej żądań</span><span class="sxs-lookup"><span data-stu-id="46444-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="46444-497">[Specyfikacja buforowania HTTP 1,1 dla nagłówka Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) wymaga, aby pamięć podręczna zatraktował prawidłowy `Cache-Control` nagłówek Wysłany przez klienta.</span><span class="sxs-lookup"><span data-stu-id="46444-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="46444-498">Klient może wykonywać żądania z `no-cache` wartością nagłówka i wymusić, aby serwer generował nową odpowiedź dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="46444-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="46444-499">Zawsze `Cache-Control` , gdy nagłówki żądań klientów są brane pod uwagę, Jeśli rozważasz cel buforowania http.</span><span class="sxs-lookup"><span data-stu-id="46444-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="46444-500">Zgodnie z oficjalną specyfikacją buforowanie jest przeznaczone do zmniejszenia opóźnień i obciążenia sieci w celu zaspokojenia żądań w sieci klientów, serwerów proxy i serwerów.</span><span class="sxs-lookup"><span data-stu-id="46444-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="46444-501">Nie musi to być sposób sterowania obciążeniem na serwerze źródłowym.</span><span class="sxs-lookup"><span data-stu-id="46444-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="46444-502">Nie ma kontroli nad tym zachowaniem buforowania podczas korzystania z [oprogramowania pośredniczącego buforowania odpowiedzi](xref:performance/caching/middleware) , ponieważ oprogramowanie pośredniczące jest zgodne z oficjalną specyfikacją buforowania.</span><span class="sxs-lookup"><span data-stu-id="46444-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="46444-503">[Planowane ulepszenia oprogramowania pośredniczącego](https://github.com/dotnet/AspNetCore/issues/2612) umożliwiają skonfigurowanie oprogramowania pośredniczącego do ignorowania `Cache-Control` nagłówka żądania podczas decydowania o konieczności przeprowadzenia buforowanej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="46444-504">Planowane usprawnienia zapewniają możliwość lepszego sterowania obciążeniem serwera.</span><span class="sxs-lookup"><span data-stu-id="46444-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="46444-505">Inna technologia buforowania w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46444-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="46444-506">Buforowanie w pamięci</span><span class="sxs-lookup"><span data-stu-id="46444-506">In-memory caching</span></span>

<span data-ttu-id="46444-507">Buforowanie w pamięci używa pamięci serwera do przechowywania buforowanych danych.</span><span class="sxs-lookup"><span data-stu-id="46444-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="46444-508">Ten typ buforowania jest odpowiedni dla jednego serwera lub wielu serwerów używających *sesji programu Sticky Notes*.</span><span class="sxs-lookup"><span data-stu-id="46444-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="46444-509">Sesje programu Sticky Notes oznaczają, że żądania od klienta są zawsze kierowane do tego samego serwera w celu przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="46444-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="46444-510">Aby uzyskać więcej informacji, zobacz <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="46444-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="46444-511">Rozproszona pamięć podręczna</span><span class="sxs-lookup"><span data-stu-id="46444-511">Distributed Cache</span></span>

<span data-ttu-id="46444-512">Użyj rozproszonej pamięci podręcznej do przechowywania danych w pamięci, gdy aplikacja jest hostowana w chmurze lub w farmie serwerów.</span><span class="sxs-lookup"><span data-stu-id="46444-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="46444-513">Pamięć podręczna jest współdzielona przez serwery, które przetwarzają żądania.</span><span class="sxs-lookup"><span data-stu-id="46444-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="46444-514">Klient może przesłać żądanie, które jest obsługiwane przez dowolny serwer w grupie, jeśli dane przechowywane w pamięci podręcznej dla klienta są dostępne.</span><span class="sxs-lookup"><span data-stu-id="46444-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="46444-515">ASP.NET Core współpracuje z rozproszonymi pamięciami podręcznymi SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)i [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .</span><span class="sxs-lookup"><span data-stu-id="46444-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="46444-516">Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="46444-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="46444-517">Pomocnik tagu pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="46444-517">Cache Tag Helper</span></span>

<span data-ttu-id="46444-518">Zawartość pamięci podręcznej ze widoku MVC lub Razor strony z pomocnikiem znacznika pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="46444-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="46444-519">Pomocnik tagu pamięci podręcznej używa buforowania w pamięci do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="46444-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="46444-520">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="46444-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="46444-521">Pomocnik tagu rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="46444-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="46444-522">Zawartość pamięci podręcznej ze widoku MVC lub ze Razor strony w scenariuszach rozproszonej chmury lub kolektywu serwerów sieci Web za pomocą pomocnika tagów rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="46444-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="46444-523">Pomocnik tagów rozproszonej pamięci podręcznej używa SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)lub [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="46444-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="46444-524">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="46444-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="46444-525">ResponseCache — atrybut</span><span class="sxs-lookup"><span data-stu-id="46444-525">ResponseCache attribute</span></span>

<span data-ttu-id="46444-526"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Określa parametry niezbędne do ustawiania odpowiednich nagłówków w pamięci podręcznej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="46444-527">Wyłącz buforowanie zawartości zawierającej informacje dla uwierzytelnionych klientów.</span><span class="sxs-lookup"><span data-stu-id="46444-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="46444-528">Buforowanie powinno być włączone tylko dla zawartości, która nie zmienia się na podstawie tożsamości użytkownika ani od tego, czy użytkownik jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="46444-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="46444-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>zmienia zapisaną odpowiedź przez wartości podanej listy kluczy zapytań.</span><span class="sxs-lookup"><span data-stu-id="46444-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="46444-530">Gdy podano jedną wartość `*` , oprogramowanie pośredniczące zmienia odpowiedzi przez wszystkie parametry ciągu zapytania żądania.</span><span class="sxs-lookup"><span data-stu-id="46444-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="46444-531">Aby ustawić właściwość, należy włączyć [oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="46444-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="46444-532">W przeciwnym razie zostanie zgłoszony wyjątek czasu wykonywania.</span><span class="sxs-lookup"><span data-stu-id="46444-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="46444-533">Brak odpowiadającego mu nagłówka HTTP dla <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> właściwości.</span><span class="sxs-lookup"><span data-stu-id="46444-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="46444-534">Właściwość jest funkcją HTTP, która jest obsługiwana przez oprogramowanie pośredniczące buforowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="46444-535">Aby oprogramowanie pośredniczące obsługiwało buforowaną odpowiedź, ciąg zapytania i wartość ciągu zapytania muszą być zgodne z poprzednim żądaniem.</span><span class="sxs-lookup"><span data-stu-id="46444-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="46444-536">Rozważmy na przykład sekwencję żądań i wyniki przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="46444-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="46444-537">Żądanie</span><span class="sxs-lookup"><span data-stu-id="46444-537">Request</span></span>                          | <span data-ttu-id="46444-538">Wynik</span><span class="sxs-lookup"><span data-stu-id="46444-538">Result</span></span>                    |
| ---
<span data-ttu-id="46444-539">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-540">'Blazor'</span></span>
- <span data-ttu-id="46444-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-541">'Identity'</span></span>
- <span data-ttu-id="46444-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-543">'Razor'</span></span>
- <span data-ttu-id="46444-544">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-545">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-546">'Blazor'</span></span>
- <span data-ttu-id="46444-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-547">'Identity'</span></span>
- <span data-ttu-id="46444-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-549">'Razor'</span></span>
- <span data-ttu-id="46444-550">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-551">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-552">'Blazor'</span></span>
- <span data-ttu-id="46444-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-553">'Identity'</span></span>
- <span data-ttu-id="46444-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-555">'Razor'</span></span>
- <span data-ttu-id="46444-556">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-557">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-558">'Blazor'</span></span>
- <span data-ttu-id="46444-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-559">'Identity'</span></span>
- <span data-ttu-id="46444-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-561">'Razor'</span></span>
- <span data-ttu-id="46444-562">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-563">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-564">'Blazor'</span></span>
- <span data-ttu-id="46444-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-565">'Identity'</span></span>
- <span data-ttu-id="46444-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-567">'Razor'</span></span>
- <span data-ttu-id="46444-568">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-569">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-570">'Blazor'</span></span>
- <span data-ttu-id="46444-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-571">'Identity'</span></span>
- <span data-ttu-id="46444-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-573">'Razor'</span></span>
- <span data-ttu-id="46444-574">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-575">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-576">'Blazor'</span></span>
- <span data-ttu-id="46444-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-577">'Identity'</span></span>
- <span data-ttu-id="46444-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-579">'Razor'</span></span>
- <span data-ttu-id="46444-580">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-581">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-582">'Blazor'</span></span>
- <span data-ttu-id="46444-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-583">'Identity'</span></span>
- <span data-ttu-id="46444-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-585">'Razor'</span></span>
- <span data-ttu-id="46444-586">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-587">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-588">'Blazor'</span></span>
- <span data-ttu-id="46444-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-589">'Identity'</span></span>
- <span data-ttu-id="46444-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-591">'Razor'</span></span>
- <span data-ttu-id="46444-592">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-593">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-594">'Blazor'</span></span>
- <span data-ttu-id="46444-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-595">'Identity'</span></span>
- <span data-ttu-id="46444-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-597">'Razor'</span></span>
- <span data-ttu-id="46444-598">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-599">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-600">'Blazor'</span></span>
- <span data-ttu-id="46444-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-601">'Identity'</span></span>
- <span data-ttu-id="46444-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-603">'Razor'</span></span>
- <span data-ttu-id="46444-604">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-605">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-606">'Blazor'</span></span>
- <span data-ttu-id="46444-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-607">'Identity'</span></span>
- <span data-ttu-id="46444-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-609">'Razor'</span></span>
- <span data-ttu-id="46444-610">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-611">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-612">'Blazor'</span></span>
- <span data-ttu-id="46444-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-613">'Identity'</span></span>
- <span data-ttu-id="46444-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-615">'Razor'</span></span>
- <span data-ttu-id="46444-616">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-617">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-618">'Blazor'</span></span>
- <span data-ttu-id="46444-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-619">'Identity'</span></span>
- <span data-ttu-id="46444-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-621">'Razor'</span></span>
- <span data-ttu-id="46444-622">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-622">'SignalR' uid:</span></span> 

<span data-ttu-id="46444-623">---------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-624">'Blazor'</span></span>
- <span data-ttu-id="46444-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-625">'Identity'</span></span>
- <span data-ttu-id="46444-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-627">'Razor'</span></span>
- <span data-ttu-id="46444-628">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-629">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-630">'Blazor'</span></span>
- <span data-ttu-id="46444-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-631">'Identity'</span></span>
- <span data-ttu-id="46444-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-633">'Razor'</span></span>
- <span data-ttu-id="46444-634">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-635">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-636">'Blazor'</span></span>
- <span data-ttu-id="46444-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-637">'Identity'</span></span>
- <span data-ttu-id="46444-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-639">'Razor'</span></span>
- <span data-ttu-id="46444-640">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-641">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-642">'Blazor'</span></span>
- <span data-ttu-id="46444-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-643">'Identity'</span></span>
- <span data-ttu-id="46444-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-645">'Razor'</span></span>
- <span data-ttu-id="46444-646">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-647">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-648">'Blazor'</span></span>
- <span data-ttu-id="46444-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-649">'Identity'</span></span>
- <span data-ttu-id="46444-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-651">'Razor'</span></span>
- <span data-ttu-id="46444-652">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-653">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-654">'Blazor'</span></span>
- <span data-ttu-id="46444-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-655">'Identity'</span></span>
- <span data-ttu-id="46444-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-657">'Razor'</span></span>
- <span data-ttu-id="46444-658">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-659">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-660">'Blazor'</span></span>
- <span data-ttu-id="46444-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-661">'Identity'</span></span>
- <span data-ttu-id="46444-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-663">'Razor'</span></span>
- <span data-ttu-id="46444-664">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-665">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-666">'Blazor'</span></span>
- <span data-ttu-id="46444-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-667">'Identity'</span></span>
- <span data-ttu-id="46444-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-669">'Razor'</span></span>
- <span data-ttu-id="46444-670">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-671">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-672">'Blazor'</span></span>
- <span data-ttu-id="46444-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-673">'Identity'</span></span>
- <span data-ttu-id="46444-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-675">'Razor'</span></span>
- <span data-ttu-id="46444-676">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="46444-677">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="46444-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="46444-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="46444-678">'Blazor'</span></span>
- <span data-ttu-id="46444-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="46444-679">'Identity'</span></span>
- <span data-ttu-id="46444-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="46444-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="46444-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="46444-681">'Razor'</span></span>
- <span data-ttu-id="46444-682">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="46444-682">'SignalR' uid:</span></span> 

<span data-ttu-id="46444-683">------------- | | `http://example.com?key1=value1` | Zwrócone z serwera.</span><span class="sxs-lookup"><span data-stu-id="46444-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="46444-684">| | `http://example.com?key1=value1` | Zwrócone z oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="46444-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="46444-685">| | `http://example.com?key1=value2` | Zwrócone z serwera.</span><span class="sxs-lookup"><span data-stu-id="46444-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="46444-686">Pierwsze żądanie jest zwracane przez serwer i w pamięci podręcznej w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="46444-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="46444-687">Drugie żądanie jest zwracane przez oprogramowanie pośredniczące, ponieważ ciąg zapytania pasuje do poprzedniego żądania.</span><span class="sxs-lookup"><span data-stu-id="46444-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="46444-688">Trzecie żądanie nie znajduje się w pamięci podręcznej pośredniczącej, ponieważ wartość ciągu zapytania nie pasuje do poprzedniego żądania.</span><span class="sxs-lookup"><span data-stu-id="46444-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="46444-689">Służy <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> do konfigurowania i tworzenia (za pośrednictwem <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="46444-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="46444-690">`ResponseCacheFilter`Wykonuje zadanie aktualizowania odpowiednich nagłówków HTTP i funkcji odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="46444-691">Filtr:</span><span class="sxs-lookup"><span data-stu-id="46444-691">The filter:</span></span>

* <span data-ttu-id="46444-692">Usuwa wszystkie istniejące nagłówki dla `Vary` , `Cache-Control` i `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="46444-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="46444-693">Zapisuje odpowiednie nagłówki na podstawie właściwości ustawionych w <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="46444-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="46444-694">Aktualizuje funkcję HTTP buforowania odpowiedzi, jeśli <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="46444-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="46444-695">Zmienia</span><span class="sxs-lookup"><span data-stu-id="46444-695">Vary</span></span>

<span data-ttu-id="46444-696">Ten nagłówek jest zapisywana tylko wtedy, gdy <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Właściwość jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="46444-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="46444-697">Właściwość ustawiona na `Vary` wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="46444-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="46444-698">Poniższy przykład używa <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="46444-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="46444-699">Za pomocą przykładowej aplikacji Wyświetl nagłówki odpowiedzi za pomocą narzędzi sieciowych przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="46444-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="46444-700">Następujące nagłówki odpowiedzi są wysyłane z odpowiedzią na stronę Cache1:</span><span class="sxs-lookup"><span data-stu-id="46444-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="46444-701">NoStore i Location. None</span><span class="sxs-lookup"><span data-stu-id="46444-701">NoStore and Location.None</span></span>

<span data-ttu-id="46444-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>przesłania większość innych właściwości.</span><span class="sxs-lookup"><span data-stu-id="46444-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="46444-703">Gdy ta właściwość jest ustawiona na `true` , `Cache-Control` nagłówek jest ustawiony na `no-store` .</span><span class="sxs-lookup"><span data-stu-id="46444-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="46444-704">Jeśli <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> jest ustawiona na `None` :</span><span class="sxs-lookup"><span data-stu-id="46444-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="46444-705">`Cache-Control`jest ustawiony na `no-store,no-cache` .</span><span class="sxs-lookup"><span data-stu-id="46444-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="46444-706">`Pragma`jest ustawiony na `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="46444-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="46444-707">Jeśli <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> jest `false` i <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ma `None` `Cache-Control` wartość, i `Pragma` są ustawione na `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="46444-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="46444-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>jest zwykle ustawiony na `true` dla stron błędów.</span><span class="sxs-lookup"><span data-stu-id="46444-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="46444-709">Strona Cache2 w przykładowej aplikacji generuje nagłówki odpowiedzi, które instruują klienta, aby nie przechowywał odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="46444-710">Przykładowa aplikacja zwraca stronę Cache2 z następującymi nagłówkami:</span><span class="sxs-lookup"><span data-stu-id="46444-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="46444-711">Lokalizacja i czas trwania</span><span class="sxs-lookup"><span data-stu-id="46444-711">Location and Duration</span></span>

<span data-ttu-id="46444-712">Aby włączyć buforowanie, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> należy ustawić wartość dodatnią i <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> musi być `Any` (domyślna) lub `Client` .</span><span class="sxs-lookup"><span data-stu-id="46444-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="46444-713">Struktura ustawia `Cache-Control` nagłówek na wartość lokalizacji, po której następuje `max-age` odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="46444-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="46444-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>Opcje `Any` i `Client` przetłumaczą `Cache-Control` odpowiednio wartości nagłówka `public` i `private` .</span><span class="sxs-lookup"><span data-stu-id="46444-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="46444-715">Jak zostało to opisane w sekcji [NoStore and Location. None](#nostore-and-locationnone) , <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ustawienie `None` Ustawia zarówno, jak `Cache-Control` i `Pragma` nagłówki `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="46444-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="46444-716">`Location.Any`( `Cache-Control` ustawienie wartości `public` ) oznacza, że *klient lub dowolny pośredni serwer proxy* może buforować wartość, w tym [oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="46444-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="46444-717">`Location.Client`( `Cache-Control` ustawienie to `private` ) wskazuje, że *tylko klient* może buforować wartość.</span><span class="sxs-lookup"><span data-stu-id="46444-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="46444-718">Żadna pośrednia pamięć podręczna powinna buforować wartość, w tym [oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="46444-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="46444-719">Nagłówki kontroli pamięci podręcznej jedynie zapewniają wskazówki klientom i pośrednim serwerom proxy, kiedy i w jaki sposób należy buforować odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46444-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="46444-720">Nie ma gwarancji, że klienci i serwery proxy będą przestrzegać [specyfikacji buforowania HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="46444-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="46444-721">[Oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) zawsze jest zgodne z regułami buforowania ustanowionymi przez specyfikację.</span><span class="sxs-lookup"><span data-stu-id="46444-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="46444-722">Poniższy przykład przedstawia model strony Cache3 z przykładowej aplikacji oraz nagłówki utworzone przez ustawienie <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> i opuszczające <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="46444-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="46444-723">Przykładowa aplikacja zwraca stronę Cache3 z następującym nagłówkiem:</span><span class="sxs-lookup"><span data-stu-id="46444-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="46444-724">Profile pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="46444-724">Cache profiles</span></span>

<span data-ttu-id="46444-725">Zamiast duplikowania ustawień pamięci podręcznej odpowiedzi w wielu atrybutach akcji kontrolera, profile pamięci podręcznej można skonfigurować jako opcje podczas konfigurowania MVC/ Razor Pages in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="46444-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="46444-726">Wartości Znalezione w profilu pamięci podręcznej, do którego istnieje odwołanie, są używane jako wartości domyślne przez <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> i są zastępowane przez wszystkie właściwości określone w atrybucie.</span><span class="sxs-lookup"><span data-stu-id="46444-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="46444-727">Skonfiguruj profil pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="46444-727">Set up a cache profile.</span></span> <span data-ttu-id="46444-728">Poniższy przykład przedstawia 30-sekundowy profil pamięci podręcznej w przykładowej aplikacji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="46444-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="46444-729">Model strony Cache4 aplikacji przykładowej odwołuje się do `Default30` profilu pamięci podręcznej:</span><span class="sxs-lookup"><span data-stu-id="46444-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="46444-730"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Można zastosować do:</span><span class="sxs-lookup"><span data-stu-id="46444-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="46444-731">Procedury obsługi stron (klasy): nie można zastosować atrybutów do metod obsługi.</span><span class="sxs-lookup"><span data-stu-id="46444-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="46444-732">Kontrolery MVC (klasy).</span><span class="sxs-lookup"><span data-stu-id="46444-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="46444-733">Akcje MVC (metody): atrybuty poziomu metody zastępują ustawienia określone w atrybutach na poziomie klasy.</span><span class="sxs-lookup"><span data-stu-id="46444-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="46444-734">Otrzymany nagłówek zastosowany do odpowiedzi strony Cache4 przez `Default30` profil pamięci podręcznej:</span><span class="sxs-lookup"><span data-stu-id="46444-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="46444-735">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="46444-735">Additional resources</span></span>

* [<span data-ttu-id="46444-736">Przechowywanie odpowiedzi w pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="46444-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="46444-737">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="46444-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

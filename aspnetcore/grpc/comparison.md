---
<span data-ttu-id="2d0a7-101">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-102">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-103">'Identity'</span></span>
- <span data-ttu-id="2d0a7-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-105">'Razor'</span></span>
- <span data-ttu-id="2d0a7-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="2d0a7-107">Porównanie usług gRPC za pomocą interfejsów API protokołu HTTP</span><span class="sxs-lookup"><span data-stu-id="2d0a7-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="2d0a7-108">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2d0a7-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="2d0a7-109">W tym artykule wyjaśniono, w jaki sposób [usługi gRPC Services](https://grpc.io/docs/guides/) porównują interfejsy API protokołu HTTP z JSON (w tym ASP.NET Core [interfejsy API sieci Web](xref:web-api/index))</span><span class="sxs-lookup"><span data-stu-id="2d0a7-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="2d0a7-110">Technologia używana do udostępniania interfejsu API dla aplikacji jest ważnym wyborem, a gRPC oferuje unikatowe korzyści w porównaniu do interfejsów API protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="2d0a7-111">W tym artykule omówiono mocne i słabe zalety gRPC oraz zalecane scenariusze dotyczące korzystania z gRPC przez inne technologie.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="2d0a7-112">Porównanie wysokiego poziomu</span><span class="sxs-lookup"><span data-stu-id="2d0a7-112">High-level comparison</span></span>

<span data-ttu-id="2d0a7-113">Poniższa tabela zawiera porównanie funkcji między gRPC i interfejsami API protokołu HTTP z kodem JSON.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="2d0a7-114">Cecha</span><span class="sxs-lookup"><span data-stu-id="2d0a7-114">Feature</span></span>          | <span data-ttu-id="2d0a7-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="2d0a7-115">gRPC</span></span>                                               | <span data-ttu-id="2d0a7-116">Interfejsy API protokołu HTTP z JSON</span><span class="sxs-lookup"><span data-stu-id="2d0a7-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="2d0a7-117">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-118">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-119">'Identity'</span></span>
- <span data-ttu-id="2d0a7-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-121">'Razor'</span></span>
- <span data-ttu-id="2d0a7-122">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-123">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-124">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-125">'Identity'</span></span>
- <span data-ttu-id="2d0a7-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-127">'Razor'</span></span>
- <span data-ttu-id="2d0a7-128">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-129">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-130">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-131">'Identity'</span></span>
- <span data-ttu-id="2d0a7-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-133">'Razor'</span></span>
- <span data-ttu-id="2d0a7-134">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-135">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-136">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-137">'Identity'</span></span>
- <span data-ttu-id="2d0a7-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-139">'Razor'</span></span>
- <span data-ttu-id="2d0a7-140">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-141">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-142">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-143">'Identity'</span></span>
- <span data-ttu-id="2d0a7-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-145">'Razor'</span></span>
- <span data-ttu-id="2d0a7-146">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-147">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-148">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-149">'Identity'</span></span>
- <span data-ttu-id="2d0a7-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-151">'Razor'</span></span>
- <span data-ttu-id="2d0a7-152">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-152">'SignalR' uid:</span></span> 

<span data-ttu-id="2d0a7-153">-------- | ---title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-154">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-155">'Identity'</span></span>
- <span data-ttu-id="2d0a7-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-157">'Razor'</span></span>
- <span data-ttu-id="2d0a7-158">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-159">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-160">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-161">'Identity'</span></span>
- <span data-ttu-id="2d0a7-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-163">'Razor'</span></span>
- <span data-ttu-id="2d0a7-164">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-165">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-166">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-167">'Identity'</span></span>
- <span data-ttu-id="2d0a7-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-169">'Razor'</span></span>
- <span data-ttu-id="2d0a7-170">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-171">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-172">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-173">'Identity'</span></span>
- <span data-ttu-id="2d0a7-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-175">'Razor'</span></span>
- <span data-ttu-id="2d0a7-176">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-177">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-178">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-179">'Identity'</span></span>
- <span data-ttu-id="2d0a7-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-181">'Razor'</span></span>
- <span data-ttu-id="2d0a7-182">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-183">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-184">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-185">'Identity'</span></span>
- <span data-ttu-id="2d0a7-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-187">'Razor'</span></span>
- <span data-ttu-id="2d0a7-188">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-189">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-190">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-191">'Identity'</span></span>
- <span data-ttu-id="2d0a7-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-193">'Razor'</span></span>
- <span data-ttu-id="2d0a7-194">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-195">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-196">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-197">'Identity'</span></span>
- <span data-ttu-id="2d0a7-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-199">'Razor'</span></span>
- <span data-ttu-id="2d0a7-200">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-201">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-202">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-203">'Identity'</span></span>
- <span data-ttu-id="2d0a7-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-205">'Razor'</span></span>
- <span data-ttu-id="2d0a7-206">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-207">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-208">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-209">'Identity'</span></span>
- <span data-ttu-id="2d0a7-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-211">'Razor'</span></span>
- <span data-ttu-id="2d0a7-212">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-213">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-214">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-215">'Identity'</span></span>
- <span data-ttu-id="2d0a7-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-217">'Razor'</span></span>
- <span data-ttu-id="2d0a7-218">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-219">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-220">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-221">'Identity'</span></span>
- <span data-ttu-id="2d0a7-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-223">'Razor'</span></span>
- <span data-ttu-id="2d0a7-224">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-225">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-226">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-227">'Identity'</span></span>
- <span data-ttu-id="2d0a7-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-229">'Razor'</span></span>
- <span data-ttu-id="2d0a7-230">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-231">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-232">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-233">'Identity'</span></span>
- <span data-ttu-id="2d0a7-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-235">'Razor'</span></span>
- <span data-ttu-id="2d0a7-236">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-237">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-238">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-239">'Identity'</span></span>
- <span data-ttu-id="2d0a7-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-241">'Razor'</span></span>
- <span data-ttu-id="2d0a7-242">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-243">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-244">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-245">'Identity'</span></span>
- <span data-ttu-id="2d0a7-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-247">'Razor'</span></span>
- <span data-ttu-id="2d0a7-248">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-249">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-250">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-251">'Identity'</span></span>
- <span data-ttu-id="2d0a7-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-253">'Razor'</span></span>
- <span data-ttu-id="2d0a7-254">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-255">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-256">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-257">'Identity'</span></span>
- <span data-ttu-id="2d0a7-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-259">'Razor'</span></span>
- <span data-ttu-id="2d0a7-260">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-261">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-262">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-263">'Identity'</span></span>
- <span data-ttu-id="2d0a7-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-265">'Razor'</span></span>
- <span data-ttu-id="2d0a7-266">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-267">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-268">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-269">'Identity'</span></span>
- <span data-ttu-id="2d0a7-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-271">'Razor'</span></span>
- <span data-ttu-id="2d0a7-272">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-273">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-274">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-275">'Identity'</span></span>
- <span data-ttu-id="2d0a7-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-277">'Razor'</span></span>
- <span data-ttu-id="2d0a7-278">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-279">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-280">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-281">'Identity'</span></span>
- <span data-ttu-id="2d0a7-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-283">'Razor'</span></span>
- <span data-ttu-id="2d0a7-284">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-285">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-286">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-287">'Identity'</span></span>
- <span data-ttu-id="2d0a7-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-289">'Razor'</span></span>
- <span data-ttu-id="2d0a7-290">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-290">'SignalR' uid:</span></span> 

<span data-ttu-id="2d0a7-291">------------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-292">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-293">'Identity'</span></span>
- <span data-ttu-id="2d0a7-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-295">'Razor'</span></span>
- <span data-ttu-id="2d0a7-296">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-297">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-298">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-299">'Identity'</span></span>
- <span data-ttu-id="2d0a7-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-301">'Razor'</span></span>
- <span data-ttu-id="2d0a7-302">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-303">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-304">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-305">'Identity'</span></span>
- <span data-ttu-id="2d0a7-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-307">'Razor'</span></span>
- <span data-ttu-id="2d0a7-308">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-309">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-310">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-311">'Identity'</span></span>
- <span data-ttu-id="2d0a7-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-313">'Razor'</span></span>
- <span data-ttu-id="2d0a7-314">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-315">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-316">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-317">'Identity'</span></span>
- <span data-ttu-id="2d0a7-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-319">'Razor'</span></span>
- <span data-ttu-id="2d0a7-320">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-321">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-322">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-323">'Identity'</span></span>
- <span data-ttu-id="2d0a7-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-325">'Razor'</span></span>
- <span data-ttu-id="2d0a7-326">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-327">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-328">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-329">'Identity'</span></span>
- <span data-ttu-id="2d0a7-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-331">'Razor'</span></span>
- <span data-ttu-id="2d0a7-332">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-333">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-334">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-335">'Identity'</span></span>
- <span data-ttu-id="2d0a7-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-337">'Razor'</span></span>
- <span data-ttu-id="2d0a7-338">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-339">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-340">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-341">'Identity'</span></span>
- <span data-ttu-id="2d0a7-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-343">'Razor'</span></span>
- <span data-ttu-id="2d0a7-344">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-345">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-346">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-347">'Identity'</span></span>
- <span data-ttu-id="2d0a7-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-349">'Razor'</span></span>
- <span data-ttu-id="2d0a7-350">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-351">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-352">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-353">'Identity'</span></span>
- <span data-ttu-id="2d0a7-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-355">'Razor'</span></span>
- <span data-ttu-id="2d0a7-356">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d0a7-357">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d0a7-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-358">'Blazor'</span></span>
- <span data-ttu-id="2d0a7-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-359">'Identity'</span></span>
- <span data-ttu-id="2d0a7-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d0a7-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d0a7-361">'Razor'</span></span>
- <span data-ttu-id="2d0a7-362">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-362">'SignalR' uid:</span></span> 

<span data-ttu-id="2d0a7-363">--------------- | | Kontrakt | Wymagane (*. proto*) | Opcjonalnie (OpenAPI) | | Protokół | HTTP/2 | HTTP | | Ładunek | [Protobuf (mały, binarny)](#performance) | JSON (duże, czytelne dla ludzi) | | Prescriptiveness | [Specyfikacja Strict](#strict-specification) | Sypki.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="2d0a7-364">Wszystkie protokoły HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-364">Any HTTP is valid.</span></span>     <span data-ttu-id="2d0a7-365">| | Przesyłanie strumieniowe | [Klient, serwer, dwukierunkowa](#streaming) | Klient, serwer | | Obsługa przeglądarki | [Nie (wymaga GRPC-Web)](#limited-browser-support) | Tak | | Zabezpieczenia | Transport (TLS) | Transport (TLS) | | Generowanie kodu klienta | [Tak](#code-generation) | OpenAPI + narzędzia innych firm |</span><span class="sxs-lookup"><span data-stu-id="2d0a7-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="2d0a7-366">mocne gRPC</span><span class="sxs-lookup"><span data-stu-id="2d0a7-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="2d0a7-367">Wydajność</span><span class="sxs-lookup"><span data-stu-id="2d0a7-367">Performance</span></span>

<span data-ttu-id="2d0a7-368">komunikaty gRPC są serializowane przy użyciu [protobuf](https://developers.google.com/protocol-buffers/docs/overview), wydajnego formatu komunikatów binarnych.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="2d0a7-369">Protobuf się bardzo szybko na serwerze i kliencie.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="2d0a7-370">Serializacja protobuf skutkuje niewielkimi ładunekmi komunikatów, co jest ważne w ograniczonych scenariuszach dotyczących przepustowości, takich jak aplikacje mobilne.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="2d0a7-371">gRPC została zaprojektowana dla protokołu HTTP/2, która stanowi znaczną wersję protokołu HTTP, która zapewnia znaczący wpływ na wydajność w porównaniu z protokołem HTTP 1. x:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="2d0a7-372">Binarne ramki i kompresja.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-372">Binary framing and compression.</span></span> <span data-ttu-id="2d0a7-373">Protokół HTTP/2 jest kompaktowy i wydajny zarówno podczas wysyłania, jak i otrzymywania.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="2d0a7-374">Multipleksowanie wielu wywołań HTTP/2 za pośrednictwem jednego połączenia TCP.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="2d0a7-375">Multipleksowanie eliminuje [blokowanie głowy](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span><span class="sxs-lookup"><span data-stu-id="2d0a7-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="2d0a7-376">Protokół HTTP/2 nie jest wyłączny do gRPC.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="2d0a7-377">Wiele typów żądań, w tym interfejsy API protokołu HTTP z notacją JSON, może korzystać z protokołu HTTP/2 i korzystać z jego ulepszeń wydajności.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="2d0a7-378">Generowanie kodu</span><span class="sxs-lookup"><span data-stu-id="2d0a7-378">Code generation</span></span>

<span data-ttu-id="2d0a7-379">Wszystkie struktury gRPC zapewniają obsługę pierwszej klasy w celu generowania kodu.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="2d0a7-380">Podstawowy plik do gRPC Development to [plik. proto](https://developers.google.com/protocol-buffers/docs/proto3), który definiuje kontrakt usług gRPC Services i messages.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="2d0a7-381">Z tego pliku struktury gRPC w kodzie generują klasę bazową usługi, komunikaty i kompletny klient.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="2d0a7-382">Udostępniając plik *. proto* między serwerem a klientem, można wygenerować komunikaty i kod klienta na końcu.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="2d0a7-383">Generowanie kodu klienta eliminuje duplikowanie komunikatów na kliencie i serwerze, a następnie tworzy klienta o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="2d0a7-384">Nie trzeba pisać klienta powoduje oszczędność czasu projektowania w aplikacjach z wieloma usługami.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="2d0a7-385">Specyfikacja Strict</span><span class="sxs-lookup"><span data-stu-id="2d0a7-385">Strict specification</span></span>

<span data-ttu-id="2d0a7-386">Specyfikacja formalna dla interfejsu API protokołu HTTP with JSON nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="2d0a7-387">Deweloperzy zanotują najlepszy format adresów URL, czasowników HTTP i kodów odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="2d0a7-388">[Specyfikacja gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) jest przedskryptowa o formacie, którego musi przestrzegać usługa gRPC.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="2d0a7-389">gRPC eliminuje debatę i zapisuje czas dewelopera, ponieważ gRPC jest spójny dla wielu platform i implementacji.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="2d0a7-390">Przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="2d0a7-390">Streaming</span></span>

<span data-ttu-id="2d0a7-391">Protokół HTTP/2 stanowi podstawę do długotrwałych strumieni komunikacji w czasie rzeczywistym.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="2d0a7-392">gRPC zapewnia wsparcie pierwszej klasy do przesyłania strumieniowego za pośrednictwem protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="2d0a7-393">Usługa gRPC obsługuje wszystkie kombinacje przesyłania strumieniowego:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="2d0a7-394">Jednoargumentowy (bez przesyłania strumieniowego)</span><span class="sxs-lookup"><span data-stu-id="2d0a7-394">Unary (no streaming)</span></span>
* <span data-ttu-id="2d0a7-395">Przesyłanie strumieniowe z serwera do klienta</span><span class="sxs-lookup"><span data-stu-id="2d0a7-395">Server to client streaming</span></span>
* <span data-ttu-id="2d0a7-396">Przesyłanie strumieniowe klienta do serwera</span><span class="sxs-lookup"><span data-stu-id="2d0a7-396">Client to server streaming</span></span>
* <span data-ttu-id="2d0a7-397">Dwukierunkowe przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="2d0a7-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="2d0a7-398">Termin/limity czasu i anulowanie</span><span class="sxs-lookup"><span data-stu-id="2d0a7-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="2d0a7-399">gRPC umożliwia klientom określenie, jak długo czekają na ukończenie zdalnego wywoływania procedur.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="2d0a7-400">[Ostateczny termin](https://grpc.io/blog/deadlines) jest wysyłany do serwera, a serwer może zdecydować, jakie działania należy podjąć, jeśli przekracza termin ostateczny.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="2d0a7-401">Na przykład serwer może anulować żądania gRPC/HTTP/Database w trakcie limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="2d0a7-402">Propagowanie terminu i anulowania za pomocą podrzędnych wywołań gRPC ułatwia wymuszenie limitów użycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="2d0a7-403">gRPC zalecane scenariusze</span><span class="sxs-lookup"><span data-stu-id="2d0a7-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="2d0a7-404">gRPC doskonale nadaje się do następujących scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="2d0a7-405">**Mikrousługi**: gRPC został zaprojektowany z myślą o małym opóźnieniu i komunikacji o dużej przepływności.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="2d0a7-406">gRPC doskonale nadaje się do lekkich mikrousług, w których wydajność jest krytyczna.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="2d0a7-407">**Komunikacja punkt-punkt w czasie rzeczywistym**: gRPC ma doskonałą obsługę przesyłania strumieniowego dwukierunkowego.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="2d0a7-408">usługi gRPC umożliwiają wypychanie komunikatów w czasie rzeczywistym bez sondowania.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="2d0a7-409">**Środowiska Polyglot**: narzędzia gRPC obsługują wszystkie popularne języki deweloperskie, co sprawia, że gRPC to dobry wybór w środowiskach wielojęzycznych.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="2d0a7-410">**Środowiska ograniczone sieci**: komunikaty gRPC są serializowane z protobuf, formatem uproszczonego komunikatu.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="2d0a7-411">Komunikat gRPC jest zawsze krótszy niż odpowiedni komunikat JSON.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="2d0a7-412">słabe gRPC</span><span class="sxs-lookup"><span data-stu-id="2d0a7-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="2d0a7-413">Ograniczona obsługa przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2d0a7-413">Limited browser support</span></span>

<span data-ttu-id="2d0a7-414">Obecnie nie można bezpośrednio wywołać usługi gRPC z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="2d0a7-415">gRPC intensywnie używa funkcji protokołu HTTP/2 i żadna przeglądarka nie zapewnia poziomu kontroli wymaganego w przypadku żądań sieci Web do obsługi klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="2d0a7-416">Na przykład przeglądarki nie zezwalają obiektowi wywołującemu na użycie protokołu HTTP/2 lub zapewniają dostęp do bazowych ramek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="2d0a7-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) to dodatkowa technologia od zespołu gRPC, która zapewnia ograniczoną obsługę gRPC w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="2d0a7-418">gRPC-Web składa się z dwóch części: klienta JavaScript obsługującego wszystkie nowoczesne przeglądarki i serwer proxy sieci Web gRPC na serwerze.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="2d0a7-419">Klient gRPC-Web wywołuje serwer proxy, a serwer proxy przekaże żądania gRPC do serwera gRPC.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="2d0a7-420">Nie wszystkie funkcje gRPC są obsługiwane przez gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="2d0a7-421">Przesyłanie strumieniowe klienta i dwukierunkowego nie jest obsługiwane i ma ograniczoną obsługę przesyłania strumieniowego serwera.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="2d0a7-422">Platforma .NET Core ma eksperymentalną obsługę gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="2d0a7-423"><xref:grpc/browser>Aby uzyskać więcej informacji, odwiedź stronę.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="2d0a7-424">Nie można odczytać ludzi</span><span class="sxs-lookup"><span data-stu-id="2d0a7-424">Not human readable</span></span>

<span data-ttu-id="2d0a7-425">Żądania interfejsu API protokołu HTTP są wysyłane jako tekst i mogą być odczytywane i tworzone przez człowieka.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="2d0a7-426">wiadomości gRPC są domyślnie kodowane przy użyciu protobuf.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="2d0a7-427">Gdy protobuf jest wydajny do wysyłania i odbierania, jego format binarny nie jest czytelny.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="2d0a7-428">Protobuf wymaga, aby opis interfejsu komunikatu określony w pliku *. proto* w celu poprawnego deserializacji.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="2d0a7-429">Dodatkowe narzędzia są wymagane do analizowania ładunków protobuf w sieci oraz do ręcznego tworzenia żądań.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="2d0a7-430">Funkcje takie jak [odbicie serwera](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) i [Narzędzie wiersza polecenia gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) istnieją, aby ułatwić wykonywanie binarnych komunikatów protobuf.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="2d0a7-431">Ponadto komunikaty protobuf obsługują [konwersję do i z formatu JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span><span class="sxs-lookup"><span data-stu-id="2d0a7-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="2d0a7-432">Wbudowana konwersja JSON zapewnia wydajny sposób konwersji komunikatów protobuf na i z formularza, który można odczytać przez człowieka podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="2d0a7-433">Scenariusze dotyczące platformy alternatywnej</span><span class="sxs-lookup"><span data-stu-id="2d0a7-433">Alternative framework scenarios</span></span>

<span data-ttu-id="2d0a7-434">Inne struktury są zalecane w porównaniu z gRPC w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="2d0a7-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="2d0a7-435">**Interfejsy API dostępne dla przeglądarki**: gRPC nie jest w pełni obsługiwane w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="2d0a7-436">gRPC — sieć Web może oferować pomoc techniczną przeglądarki, ale ma ograniczenia i wprowadza serwer proxy serwera.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="2d0a7-437">**Rozgłaszanie komunikacji**w czasie rzeczywistym: gRPC obsługuje komunikację w czasie rzeczywistym za pośrednictwem przesyłania strumieniowego, ale pojęcie rozgłaszania komunikatów do zarejestrowanych połączeń nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="2d0a7-438">Na przykład w scenariuszu pokoju rozmów, w którym nowe wiadomości czatu powinny być wysyłane do wszystkich klientów w pokoju rozmowy, każde wywołanie gRPC jest wymagane do narzucania strumieniowego przesyłania nowych komunikatów rozmowy do klienta.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="2d0a7-439">[SignalR](xref:signalr/introduction)jest przydatną strukturą dla tego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="2d0a7-440">ma koncepcję trwałych połączeń i wbudowaną obsługę rozgłaszania komunikatów.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="2d0a7-441">**Komunikacja między procesami**: proces musi obsługiwać serwer HTTP/2 w celu akceptowania przychodzących wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="2d0a7-442">W przypadku systemu Windows [potoki](/dotnet/standard/io/pipe-operations) komunikacji między procesami to szybka i lekka Metoda komunikacji.</span><span class="sxs-lookup"><span data-stu-id="2d0a7-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d0a7-443">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2d0a7-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

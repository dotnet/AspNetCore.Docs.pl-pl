---
<span data-ttu-id="594e1-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-102">'Blazor'</span></span>
- <span data-ttu-id="594e1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-103">'Identity'</span></span>
- <span data-ttu-id="594e1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-105">'Razor'</span></span>
- <span data-ttu-id="594e1-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="594e1-107">Ponowne zapisywanie przez adres URL oprogramowania pośredniczącego w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="594e1-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="594e1-108">Autor [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="594e1-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="594e1-109">Ten dokument zawiera wprowadzenie do ponownego zapisywania adresów URL z instrukcjami dotyczącymi sposobu korzystania z programu pośredniczącego do ponownego zapisywania adresów URL w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="594e1-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="594e1-110">Ponowne zapisywanie adresu URL to czynność modyfikacji adresów URL żądań na podstawie co najmniej jednej wstępnie zdefiniowanej reguły.</span><span class="sxs-lookup"><span data-stu-id="594e1-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="594e1-111">Ponowne zapisywanie adresów URL powoduje utworzenie abstrakcji między lokalizacjami zasobów a ich adresami, dzięki czemu lokalizacje i adresy nie są ściśle połączone.</span><span class="sxs-lookup"><span data-stu-id="594e1-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="594e1-112">Ponowne zapisywanie adresów URL jest cenne w kilku scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="594e1-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="594e1-113">Przenoszenie lub zastępowanie zasobów serwera tymczasowo lub trwale i utrzymuje stałe lokalizatory dla tych zasobów.</span><span class="sxs-lookup"><span data-stu-id="594e1-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="594e1-114">Podziel przetwarzanie żądań między różne aplikacje lub w obszarach jednej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="594e1-115">Usuwanie, Dodawanie lub organizowanie segmentów adresów URL w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="594e1-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="594e1-116">Optymalizuj publiczne adresy URL dla optymalizacji aparatu wyszukiwania (wyszukiwarka).</span><span class="sxs-lookup"><span data-stu-id="594e1-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="594e1-117">Zezwól na używanie przyjaznych publicznych adresów URL, aby ułatwić odwiedzającym zapowiadanie zawartości zwróconej przez żądanie zasobu.</span><span class="sxs-lookup"><span data-stu-id="594e1-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="594e1-118">Przekieruj niezabezpieczone żądania do bezpiecznych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="594e1-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="594e1-119">Zapobiegaj hotlinking, gdzie lokacja zewnętrzna używa hostowanego zasobu statycznego w innej lokacji przez połączenie elementu zawartości z własną zawartością.</span><span class="sxs-lookup"><span data-stu-id="594e1-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="594e1-120">Ponowne zapisywanie adresów URL może zmniejszyć wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="594e1-121">Jeśli to możliwe, należy ograniczyć liczbę i złożoność reguł.</span><span class="sxs-lookup"><span data-stu-id="594e1-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="594e1-122">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="594e1-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="594e1-123">Przekierowywanie adresów URL i ponowne zapisywanie adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="594e1-124">Różnica między *przekierowaniami adresów URL* i *przepisaniem adresów URL* jest subtelna, ale ma ważne konsekwencje dla udostępniania zasobów klientom.</span><span class="sxs-lookup"><span data-stu-id="594e1-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="594e1-125">Ponowne zapisywanie oprogramowania pośredniczącego w usłudze ASP.NET Core jest w stanie sprostać potrzebom obu tych elementów.</span><span class="sxs-lookup"><span data-stu-id="594e1-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="594e1-126">*Przekierowanie adresu URL* obejmuje operację po stronie klienta, w której klient otrzymuje dostęp do zasobu pod innym adresem niż pierwotnie żądany klient.</span><span class="sxs-lookup"><span data-stu-id="594e1-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="594e1-127">Wymaga to przeprowadzenia rundy na serwerze.</span><span class="sxs-lookup"><span data-stu-id="594e1-127">This requires a round trip to the server.</span></span> <span data-ttu-id="594e1-128">Adres URL przekierowania zwracany do klienta pojawia się na pasku adresu przeglądarki, gdy klient wysyła nowe żądanie dla zasobu.</span><span class="sxs-lookup"><span data-stu-id="594e1-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="594e1-129">W `/resource` przypadku *przekierowania* do programu `/different-resource` serwer odpowiada, że klient powinien uzyskać zasób przy `/different-resource` użyciu kodu stanu wskazującego, że przekierowanie jest tymczasowe lub trwałe.</span><span class="sxs-lookup"><span data-stu-id="594e1-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Punkt końcowy usługi WebAPI został tymczasowo zmieniony z wersji 1 (v1) do wersji 2 (v2) na serwerze.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="594e1-135">Podczas przekierowywania żądań do innego adresu URL wskaż, czy przekierowanie jest trwałe, czy tymczasowe, określając kod stanu z odpowiedzią:</span><span class="sxs-lookup"><span data-stu-id="594e1-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="594e1-136">*301-przesunięty* kod stanu jest używany, gdy zasób ma nowy, stały adres URL i chcesz wydać klientowi, że wszystkie przyszłe żądania dla zasobu powinny używać nowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="594e1-137">*Klient może buforować i ponownie używać odpowiedzi po odebraniu kodu stanu 301.*</span><span class="sxs-lookup"><span data-stu-id="594e1-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="594e1-138">Kod stanu *znaleziony przez 302* jest używany, gdy przekierowywanie jest tymczasowe lub zwykle może ulec zmianie.</span><span class="sxs-lookup"><span data-stu-id="594e1-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="594e1-139">Kod stanu 302 wskazuje na klienta, aby nie przechowywać adresu URL i używać go w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="594e1-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="594e1-140">Aby uzyskać więcej informacji na temat kodów stanu, zobacz [RFC 2616: definicje kodów stanu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="594e1-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="594e1-141">Ponowne *Zapisywanie adresów URL* jest operacją po stronie serwera, która dostarcza zasób z innego adresu zasobu niż żądany klient.</span><span class="sxs-lookup"><span data-stu-id="594e1-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="594e1-142">Ponowne zapisywanie adresu URL nie wymaga przejazdu na serwer.</span><span class="sxs-lookup"><span data-stu-id="594e1-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="594e1-143">Zwrotny adres URL nie jest zwracany do klienta i nie jest wyświetlany na pasku adresu przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="594e1-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="594e1-144">Jeśli `/resource` program zostanie ponownie *zapisany* w programie `/different-resource` , serwer *wewnętrznie* pobiera i zwraca zasób w `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="594e1-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="594e1-145">Mimo że klient może być w stanie pobrać zasób przy zapisywanym adresie URL, klient nie ma informacji o tym, że zasób istnieje pod adresem URL, gdy wysyła żądanie i otrzymuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="594e1-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Punkt końcowy usługi WebAPI został zmieniony z wersji 1 (v1) na wersję 2 (v2) na serwerze.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="594e1-150">Przykładowa aplikacja do ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-150">URL rewriting sample app</span></span>

<span data-ttu-id="594e1-151">Możesz zapoznać się z funkcjami zapisywania oprogramowania pośredniczącego za pomocą [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="594e1-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="594e1-152">Aplikacja stosuje reguły przekierowania i ponownego zapisywania oraz pokazuje przekierowany lub ponownie zapisany adres URL dla kilku scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="594e1-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="594e1-153">Kiedy używać oprogramowania pośredniczącego ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="594e1-154">Używaj ponownego zapisywania adresów URL, gdy nie możesz użyć następujących metod:</span><span class="sxs-lookup"><span data-stu-id="594e1-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="594e1-155">Moduł ponownego zapisywania adresów URL z usługami IIS w systemie Windows Server</span><span class="sxs-lookup"><span data-stu-id="594e1-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="594e1-156">Moduł Apache mod_rewrite na serwerze Apache</span><span class="sxs-lookup"><span data-stu-id="594e1-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="594e1-157">Ponowne zapisywanie adresów URL w witrynie Nginx</span><span class="sxs-lookup"><span data-stu-id="594e1-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="594e1-158">Należy również użyć oprogramowania pośredniczącego, gdy aplikacja jest hostowana na [serwerze HTTP. sys](xref:fundamentals/servers/httpsys) (wcześniej nazywanej webListener).</span><span class="sxs-lookup"><span data-stu-id="594e1-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="594e1-159">Główne przyczyny używania technologii zapisywania adresów URL opartych na serwerze w usługach IIS, Apache i Nginx są następujące:</span><span class="sxs-lookup"><span data-stu-id="594e1-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="594e1-160">Oprogramowanie pośredniczące nie obsługuje pełnych funkcji tych modułów.</span><span class="sxs-lookup"><span data-stu-id="594e1-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="594e1-161">Niektóre funkcje modułów serwera nie współpracują z projektami ASP.NET Core, takimi jak `IsFile` i `IsDirectory` ograniczeniami modułu ponownego zapisywania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="594e1-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="594e1-162">W tych scenariuszach zamiast tego należy użyć oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="594e1-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="594e1-163">Wydajność oprogramowania pośredniczącego prawdopodobnie nie jest zgodna z tymi modułami.</span><span class="sxs-lookup"><span data-stu-id="594e1-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="594e1-164">Testy porównawcze są jedynym sposobem, aby wiedzieć, że metoda obniża wydajność w najbardziej lub niewielkim stopniu wydajności.</span><span class="sxs-lookup"><span data-stu-id="594e1-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="594e1-165">Pakiet</span><span class="sxs-lookup"><span data-stu-id="594e1-165">Package</span></span>

<span data-ttu-id="594e1-166">Oprogramowanie pośredniczące ponownego zapisywania adresów URL jest dostarczane przez pakiet [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) , który jest niejawnie uwzględniony w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="594e1-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="594e1-167">Rozszerzenie i opcje</span><span class="sxs-lookup"><span data-stu-id="594e1-167">Extension and options</span></span>

<span data-ttu-id="594e1-168">Ustanów reguły ponownego zapisywania i przekierowywania adresów URL, tworząc wystąpienie klasy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) z metodami rozszerzającymi dla każdej z reguł ponownego zapisywania.</span><span class="sxs-lookup"><span data-stu-id="594e1-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="594e1-169">Łączenie wielu reguł w kolejności, w jakiej mają być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="594e1-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="594e1-170">`RewriteOptions`Są one przesyłane do programu pośredniczącego na potrzeby ponownego zapisywania adresów URL, ponieważ są dodawane do potoku żądania przy użyciu <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="594e1-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="594e1-171">Przekieruj do sieci Web inne niż www</span><span class="sxs-lookup"><span data-stu-id="594e1-171">Redirect non-www to www</span></span>

<span data-ttu-id="594e1-172">Trzy opcje Zezwalaj aplikacji na Przekierowywanie żądań, które nie są `www` żądaniami do `www` :</span><span class="sxs-lookup"><span data-stu-id="594e1-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="594e1-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trwale Przekieruj żądanie do domeny podrzędnej, `www` Jeśli żądanie jest inne niż `www` .</span><span class="sxs-lookup"><span data-stu-id="594e1-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="594e1-174">Przekierowuje kod stanu [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="594e1-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="594e1-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Przekieruj żądanie do domeny podrzędnej `www` , jeśli żądanie przychodzące jest inne niż `www` .</span><span class="sxs-lookup"><span data-stu-id="594e1-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="594e1-176">Przekierowuje kod stanu [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="594e1-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="594e1-177">Przeciążenie umożliwia podanie kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="594e1-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="594e1-178">Użyj pola <xref:Microsoft.AspNetCore.Http.StatusCodes> klasy do przypisania kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="594e1-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="594e1-179">Przekierowywanie adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-179">URL redirect</span></span>

<span data-ttu-id="594e1-180">Użyj <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> , aby przekierować żądania.</span><span class="sxs-lookup"><span data-stu-id="594e1-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="594e1-181">Pierwszy parametr zawiera wyrażenie regularne do dopasowania na ścieżce przychodzącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="594e1-182">Drugi parametr jest ciągiem zamiennym.</span><span class="sxs-lookup"><span data-stu-id="594e1-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="594e1-183">Trzeci parametr, jeśli obecny, określa kod stanu.</span><span class="sxs-lookup"><span data-stu-id="594e1-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="594e1-184">Jeśli nie określisz kodu stanu, kod stanu zostanie zmieniony na *302-znaleziono*, co oznacza, że zasób jest tymczasowo przenoszony lub zastępowany.</span><span class="sxs-lookup"><span data-stu-id="594e1-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="594e1-185">W przeglądarce z włączonymi narzędziami deweloperskimi Utwórz żądanie do przykładowej aplikacji ze ścieżką `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="594e1-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="594e1-186">Wyrażenie regularne dopasowuje ścieżkę żądania w `redirect-rule/(.*)` , a ścieżka jest zastępowana przez `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="594e1-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="594e1-187">Adres URL przekierowania jest wysyłany z powrotem do klienta z kodem stanu *znalezionym przez 302* .</span><span class="sxs-lookup"><span data-stu-id="594e1-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="594e1-188">Przeglądarka tworzy nowe żądanie w adresie URL przekierowania, który jest wyświetlany na pasku adresu przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="594e1-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="594e1-189">Ponieważ w adresie URL przekierowania nie ma reguł pasujących do przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="594e1-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="594e1-190">Drugie żądanie odbiera odpowiedź *200-OK* z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="594e1-191">Treść odpowiedzi zawiera adres URL przekierowania.</span><span class="sxs-lookup"><span data-stu-id="594e1-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="594e1-192">Po *przekierowaniu*adresu URL do serwera zostanie przeprowadzona runda.</span><span class="sxs-lookup"><span data-stu-id="594e1-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="594e1-193">Należy zachować ostrożność podczas ustanawiania reguł przekierowań.</span><span class="sxs-lookup"><span data-stu-id="594e1-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="594e1-194">Reguły przekierowania są oceniane dla każdego żądania do aplikacji, w tym po przekierowaniu.</span><span class="sxs-lookup"><span data-stu-id="594e1-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="594e1-195">Można łatwo przypadkowo utworzyć *pętlę nieskończonych przekierowań*.</span><span class="sxs-lookup"><span data-stu-id="594e1-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="594e1-196">Oryginalne żądanie:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="594e1-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="594e1-198">Część wyrażenia zawartego w nawiasach jest nazywana *grupą przechwytywania*.</span><span class="sxs-lookup"><span data-stu-id="594e1-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="594e1-199">Kropka ( `.` ) wyrażenia oznacza *dopasowanie dowolnego znaku*.</span><span class="sxs-lookup"><span data-stu-id="594e1-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="594e1-200">Gwiazdka ( `*` ) oznacza *Dopasowanie znaku poprzedzającego zero lub więcej razy*.</span><span class="sxs-lookup"><span data-stu-id="594e1-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="594e1-201">W związku z tym, ostatnie dwa segmenty ścieżki adresu URL, `1234/5678` są przechwytywane przez grupę przechwytywania `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="594e1-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="594e1-202">Każda wartość podaną w adresie URL żądania po `redirect-rule/` przechwyceniu przez tę pojedynczą grupę przechwytywania.</span><span class="sxs-lookup"><span data-stu-id="594e1-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="594e1-203">W ciągu zamiennym przechwycone grupy są wstawiane do ciągu z symbolem dolara ( `$` ), po którym następuje numer sekwencji przechwytywania.</span><span class="sxs-lookup"><span data-stu-id="594e1-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="594e1-204">Pierwsza wartość grupy przechwytywania jest pobierana z `$1` , sekunda z `$2` i są dalej sekwencją dla grup przechwytywania w wyrażeniach regularnych.</span><span class="sxs-lookup"><span data-stu-id="594e1-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="594e1-205">W aplikacji przykładowej wyrażenie regularne ma tylko jedną przechwyconą grupę, więc w ciągu zamiennym istnieje tylko jedna grupa wstrzykiwana, która jest `$1` .</span><span class="sxs-lookup"><span data-stu-id="594e1-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="594e1-206">Gdy reguła zostanie zastosowana, adres URL zmieni się `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="594e1-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="594e1-207">Przekierowanie adresu URL do bezpiecznego punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="594e1-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="594e1-208">Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> do przekierowywania żądań HTTP do tego samego hosta i ścieżki przy użyciu protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="594e1-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="594e1-209">Jeśli nie podano kodu stanu, oprogramowanie pośredniczące domyślnie zostanie *znalezione na 302*.</span><span class="sxs-lookup"><span data-stu-id="594e1-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="594e1-210">Jeśli port nie jest podany:</span><span class="sxs-lookup"><span data-stu-id="594e1-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="594e1-211">Ustawienia domyślne oprogramowania pośredniczącego `null` .</span><span class="sxs-lookup"><span data-stu-id="594e1-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="594e1-212">Schemat zmienia się na `https` (protokół https), a klient uzyskuje dostęp do zasobu na porcie 443.</span><span class="sxs-lookup"><span data-stu-id="594e1-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="594e1-213">Poniższy przykład pokazuje, jak ustawić kod stanu na *301 — trwale przeniesiony* i zmienić port na 5001.</span><span class="sxs-lookup"><span data-stu-id="594e1-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="594e1-214">Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> do przekierowywania niezabezpieczonych żądań do tego samego hosta i ścieżki z bezpiecznym protokołem HTTPS na porcie 443.</span><span class="sxs-lookup"><span data-stu-id="594e1-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="594e1-215">Oprogramowanie pośredniczące ustawia kod stanu na *301 — trwale przeniesiony*.</span><span class="sxs-lookup"><span data-stu-id="594e1-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="594e1-216">Podczas przekierowywania do bezpiecznego punktu końcowego bez wymagania dla dodatkowych reguł przekierowywania zalecamy używanie oprogramowania pośredniczącego do przekierowania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="594e1-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="594e1-217">Aby uzyskać więcej informacji, zobacz temat [Wymuś https](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="594e1-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="594e1-218">Przykładowa aplikacja jest w stanie demonstrować, jak korzystać z `AddRedirectToHttps` lub `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="594e1-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="594e1-219">Dodaj metodę rozszerzenia do `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="594e1-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="594e1-220">Wprowadź niezabezpieczone żądanie do aplikacji pod dowolnym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="594e1-221">Odrzuć ostrzeżenie o zabezpieczeniach przeglądarki, że certyfikat z podpisem własnym jest niezaufany lub Utwórz wyjątek, aby zaufać certyfikatowi.</span><span class="sxs-lookup"><span data-stu-id="594e1-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="594e1-222">Oryginalne żądanie przy użyciu `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="594e1-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="594e1-224">Oryginalne żądanie przy użyciu `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="594e1-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="594e1-226">Regenerowanie adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-226">URL rewrite</span></span>

<span data-ttu-id="594e1-227">Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> do tworzenia reguły ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="594e1-228">Pierwszy parametr zawiera wyrażenie regularne do dopasowania w przychodzącej ścieżce adresu URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="594e1-229">Drugi parametr jest ciągiem zamiennym.</span><span class="sxs-lookup"><span data-stu-id="594e1-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="594e1-230">Trzeci parametr, `skipRemainingRules: {true|false}` , wskazuje na oprogramowanie pośredniczące, niezależnie od tego, czy pominięcia dodatkowych reguł ponownego zapisu w przypadku zastosowania bieżącej reguły.</span><span class="sxs-lookup"><span data-stu-id="594e1-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="594e1-231">Oryginalne żądanie:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="594e1-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądania i odpowiedzi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="594e1-233">W karatach ( `^` ) na początku wyrażenia oznacza to, że dopasowanie rozpoczyna się na początku ścieżki URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="594e1-234">W poprzednim przykładzie z regułą przekierowania `redirect-rule/(.*)` nie ma żadnych karatów ( `^` ) na początku wyrażenia regularnego.</span><span class="sxs-lookup"><span data-stu-id="594e1-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="594e1-235">W związku z tym wszystkie znaki mogą poprzedzać `redirect-rule/` w ścieżce pomyślne dopasowanie.</span><span class="sxs-lookup"><span data-stu-id="594e1-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="594e1-236">Ścieżka</span><span class="sxs-lookup"><span data-stu-id="594e1-236">Path</span></span>                               | <span data-ttu-id="594e1-237">Dopasowanie</span><span class="sxs-lookup"><span data-stu-id="594e1-237">Match</span></span> |
| ---
<span data-ttu-id="594e1-238">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-239">'Blazor'</span></span>
- <span data-ttu-id="594e1-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-240">'Identity'</span></span>
- <span data-ttu-id="594e1-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-242">'Razor'</span></span>
- <span data-ttu-id="594e1-243">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-244">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-245">'Blazor'</span></span>
- <span data-ttu-id="594e1-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-246">'Identity'</span></span>
- <span data-ttu-id="594e1-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-248">'Razor'</span></span>
- <span data-ttu-id="594e1-249">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-250">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-251">'Blazor'</span></span>
- <span data-ttu-id="594e1-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-252">'Identity'</span></span>
- <span data-ttu-id="594e1-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-254">'Razor'</span></span>
- <span data-ttu-id="594e1-255">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-256">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-257">'Blazor'</span></span>
- <span data-ttu-id="594e1-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-258">'Identity'</span></span>
- <span data-ttu-id="594e1-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-260">'Razor'</span></span>
- <span data-ttu-id="594e1-261">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-262">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-263">'Blazor'</span></span>
- <span data-ttu-id="594e1-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-264">'Identity'</span></span>
- <span data-ttu-id="594e1-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-266">'Razor'</span></span>
- <span data-ttu-id="594e1-267">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-268">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-269">'Blazor'</span></span>
- <span data-ttu-id="594e1-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-270">'Identity'</span></span>
- <span data-ttu-id="594e1-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-272">'Razor'</span></span>
- <span data-ttu-id="594e1-273">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-274">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-275">'Blazor'</span></span>
- <span data-ttu-id="594e1-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-276">'Identity'</span></span>
- <span data-ttu-id="594e1-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-278">'Razor'</span></span>
- <span data-ttu-id="594e1-279">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-280">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-281">'Blazor'</span></span>
- <span data-ttu-id="594e1-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-282">'Identity'</span></span>
- <span data-ttu-id="594e1-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-284">'Razor'</span></span>
- <span data-ttu-id="594e1-285">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-286">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-287">'Blazor'</span></span>
- <span data-ttu-id="594e1-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-288">'Identity'</span></span>
- <span data-ttu-id="594e1-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-290">'Razor'</span></span>
- <span data-ttu-id="594e1-291">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-292">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-293">'Blazor'</span></span>
- <span data-ttu-id="594e1-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-294">'Identity'</span></span>
- <span data-ttu-id="594e1-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-296">'Razor'</span></span>
- <span data-ttu-id="594e1-297">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-298">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-299">'Blazor'</span></span>
- <span data-ttu-id="594e1-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-300">'Identity'</span></span>
- <span data-ttu-id="594e1-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-302">'Razor'</span></span>
- <span data-ttu-id="594e1-303">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-304">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-305">'Blazor'</span></span>
- <span data-ttu-id="594e1-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-306">'Identity'</span></span>
- <span data-ttu-id="594e1-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-308">'Razor'</span></span>
- <span data-ttu-id="594e1-309">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-310">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-311">'Blazor'</span></span>
- <span data-ttu-id="594e1-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-312">'Identity'</span></span>
- <span data-ttu-id="594e1-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-314">'Razor'</span></span>
- <span data-ttu-id="594e1-315">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-316">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-317">'Blazor'</span></span>
- <span data-ttu-id="594e1-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-318">'Identity'</span></span>
- <span data-ttu-id="594e1-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-320">'Razor'</span></span>
- <span data-ttu-id="594e1-321">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-322">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-323">'Blazor'</span></span>
- <span data-ttu-id="594e1-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-324">'Identity'</span></span>
- <span data-ttu-id="594e1-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-326">'Razor'</span></span>
- <span data-ttu-id="594e1-327">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-327">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Tak | | `/my-cool-redirect-rule/1234/5678` | Tak | | `/anotherredirect-rule/1234/5678`  | Tak |</span><span class="sxs-lookup"><span data-stu-id="594e1-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="594e1-329">Reguła ponownego zapisywania, `^rewrite-rule/(\d+)/(\d+)` ,, dopasowuje się tylko do ścieżek, jeśli zaczynają się od `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="594e1-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="594e1-330">W poniższej tabeli należy zwrócić uwagę na różnicę.</span><span class="sxs-lookup"><span data-stu-id="594e1-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="594e1-331">Ścieżka</span><span class="sxs-lookup"><span data-stu-id="594e1-331">Path</span></span>                              | <span data-ttu-id="594e1-332">Dopasowanie</span><span class="sxs-lookup"><span data-stu-id="594e1-332">Match</span></span> |
| ---
<span data-ttu-id="594e1-333">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-334">'Blazor'</span></span>
- <span data-ttu-id="594e1-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-335">'Identity'</span></span>
- <span data-ttu-id="594e1-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-337">'Razor'</span></span>
- <span data-ttu-id="594e1-338">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-339">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-340">'Blazor'</span></span>
- <span data-ttu-id="594e1-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-341">'Identity'</span></span>
- <span data-ttu-id="594e1-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-343">'Razor'</span></span>
- <span data-ttu-id="594e1-344">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-345">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-346">'Blazor'</span></span>
- <span data-ttu-id="594e1-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-347">'Identity'</span></span>
- <span data-ttu-id="594e1-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-349">'Razor'</span></span>
- <span data-ttu-id="594e1-350">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-351">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-352">'Blazor'</span></span>
- <span data-ttu-id="594e1-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-353">'Identity'</span></span>
- <span data-ttu-id="594e1-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-355">'Razor'</span></span>
- <span data-ttu-id="594e1-356">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-357">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-358">'Blazor'</span></span>
- <span data-ttu-id="594e1-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-359">'Identity'</span></span>
- <span data-ttu-id="594e1-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-361">'Razor'</span></span>
- <span data-ttu-id="594e1-362">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-363">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-364">'Blazor'</span></span>
- <span data-ttu-id="594e1-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-365">'Identity'</span></span>
- <span data-ttu-id="594e1-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-367">'Razor'</span></span>
- <span data-ttu-id="594e1-368">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-369">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-370">'Blazor'</span></span>
- <span data-ttu-id="594e1-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-371">'Identity'</span></span>
- <span data-ttu-id="594e1-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-373">'Razor'</span></span>
- <span data-ttu-id="594e1-374">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-375">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-376">'Blazor'</span></span>
- <span data-ttu-id="594e1-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-377">'Identity'</span></span>
- <span data-ttu-id="594e1-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-379">'Razor'</span></span>
- <span data-ttu-id="594e1-380">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-381">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-382">'Blazor'</span></span>
- <span data-ttu-id="594e1-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-383">'Identity'</span></span>
- <span data-ttu-id="594e1-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-385">'Razor'</span></span>
- <span data-ttu-id="594e1-386">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-387">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-388">'Blazor'</span></span>
- <span data-ttu-id="594e1-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-389">'Identity'</span></span>
- <span data-ttu-id="594e1-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-391">'Razor'</span></span>
- <span data-ttu-id="594e1-392">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-393">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-394">'Blazor'</span></span>
- <span data-ttu-id="594e1-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-395">'Identity'</span></span>
- <span data-ttu-id="594e1-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-397">'Razor'</span></span>
- <span data-ttu-id="594e1-398">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-399">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-400">'Blazor'</span></span>
- <span data-ttu-id="594e1-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-401">'Identity'</span></span>
- <span data-ttu-id="594e1-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-403">'Razor'</span></span>
- <span data-ttu-id="594e1-404">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-405">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-406">'Blazor'</span></span>
- <span data-ttu-id="594e1-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-407">'Identity'</span></span>
- <span data-ttu-id="594e1-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-409">'Razor'</span></span>
- <span data-ttu-id="594e1-410">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-411">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-412">'Blazor'</span></span>
- <span data-ttu-id="594e1-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-413">'Identity'</span></span>
- <span data-ttu-id="594e1-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-415">'Razor'</span></span>
- <span data-ttu-id="594e1-416">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-416">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Tak | | `/my-cool-rewrite-rule/1234/5678` | Nie | | `/anotherrewrite-rule/1234/5678`  | Nie |</span><span class="sxs-lookup"><span data-stu-id="594e1-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="594e1-418">Po `^rewrite-rule/` części wyrażenia istnieją dwie grupy przechwytywania `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="594e1-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="594e1-419">Oznaczenia `\d` *są zgodne z cyfrą (* cyfrą).</span><span class="sxs-lookup"><span data-stu-id="594e1-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="594e1-420">Znak plus ( `+` ) oznacza *dopasowanie co najmniej jednego znaku poprzedzającego*.</span><span class="sxs-lookup"><span data-stu-id="594e1-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="594e1-421">W związku z tym adres URL musi zawierać numer, po którym następuje ukośnik, po którym następuje kolejny numer.</span><span class="sxs-lookup"><span data-stu-id="594e1-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="594e1-422">Te grupy przechwytywania są wstrzykiwane do zarejestrowanego adresu URL jako `$1` i `$2` .</span><span class="sxs-lookup"><span data-stu-id="594e1-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="594e1-423">Ciąg zastępczy reguły ponownego zapisu umieszcza przechwycone grupy w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="594e1-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="594e1-424">Żądana ścieżka `/rewrite-rule/1234/5678` jest ponownie zapisywana, aby uzyskać zasób w `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="594e1-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="594e1-425">Jeśli ciąg zapytania jest obecny w oryginalnym żądaniu, jest zachowywany, gdy adres URL zostanie ponownie zapisany.</span><span class="sxs-lookup"><span data-stu-id="594e1-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="594e1-426">Serwer nie może uzyskać dostępu do zasobów.</span><span class="sxs-lookup"><span data-stu-id="594e1-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="594e1-427">Jeśli zasób istnieje, jest pobierany i zwracany do klienta przy użyciu kodu stanu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="594e1-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="594e1-428">Ponieważ klient nie jest przekierowywany, adres URL na pasku adresu przeglądarki nie jest zmieniany.</span><span class="sxs-lookup"><span data-stu-id="594e1-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="594e1-429">Klienci nie mogą wykryć, czy na serwerze wystąpiła operacja ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="594e1-430">Używaj `skipRemainingRules: true` zawsze, gdy jest to możliwe, ponieważ reguły uzgadniania są wyliczane i zwiększają czas odpowiedzi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="594e1-431">Dla najszybszej odpowiedzi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="594e1-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="594e1-432">Zamów reguły ponownego zapisu z najczęściej dopasowanej reguły do najmniej często dopasowanej reguły.</span><span class="sxs-lookup"><span data-stu-id="594e1-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="594e1-433">Pomiń przetwarzanie pozostałych reguł w przypadku wystąpienia dopasowania i nie jest wymagane żadne dodatkowe przetwarzanie reguły.</span><span class="sxs-lookup"><span data-stu-id="594e1-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="594e1-434">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="594e1-434">Apache mod_rewrite</span></span>

<span data-ttu-id="594e1-435">Zastosuj reguły mod_rewrite Apache za pomocą programu <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="594e1-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="594e1-436">Upewnij się, że plik reguł został wdrożony razem z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="594e1-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="594e1-437">Aby uzyskać więcej informacji i przykłady reguł mod_rewrite, zobacz [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="594e1-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="594e1-438">A służy <xref:System.IO.StreamReader> do odczytywania reguł z pliku reguł *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="594e1-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="594e1-439">Przykładowa aplikacja przekierowuje żądania z `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="594e1-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="594e1-440">Kod stanu odpowiedzi to *302 — znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="594e1-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="594e1-441">Oryginalne żądanie:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="594e1-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="594e1-443">Oprogramowanie pośredniczące obsługuje następujące zmienne serwera Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="594e1-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="594e1-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="594e1-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="594e1-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="594e1-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="594e1-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="594e1-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="594e1-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="594e1-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="594e1-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="594e1-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="594e1-449">HTTP_HOST</span></span>
* <span data-ttu-id="594e1-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="594e1-450">HTTP_REFERER</span></span>
* <span data-ttu-id="594e1-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="594e1-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="594e1-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="594e1-452">HTTPS</span></span>
* <span data-ttu-id="594e1-453">If</span><span class="sxs-lookup"><span data-stu-id="594e1-453">IPV6</span></span>
* <span data-ttu-id="594e1-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="594e1-454">QUERY_STRING</span></span>
* <span data-ttu-id="594e1-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="594e1-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="594e1-456">REMOTE_PORT</span></span>
* <span data-ttu-id="594e1-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="594e1-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="594e1-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="594e1-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="594e1-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="594e1-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="594e1-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="594e1-460">REQUEST_URI</span></span>
* <span data-ttu-id="594e1-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="594e1-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="594e1-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-462">SERVER_ADDR</span></span>
* <span data-ttu-id="594e1-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="594e1-463">SERVER_PORT</span></span>
* <span data-ttu-id="594e1-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="594e1-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="594e1-465">CZAS</span><span class="sxs-lookup"><span data-stu-id="594e1-465">TIME</span></span>
* <span data-ttu-id="594e1-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="594e1-466">TIME_DAY</span></span>
* <span data-ttu-id="594e1-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="594e1-467">TIME_HOUR</span></span>
* <span data-ttu-id="594e1-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="594e1-468">TIME_MIN</span></span>
* <span data-ttu-id="594e1-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="594e1-469">TIME_MON</span></span>
* <span data-ttu-id="594e1-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="594e1-470">TIME_SEC</span></span>
* <span data-ttu-id="594e1-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="594e1-471">TIME_WDAY</span></span>
* <span data-ttu-id="594e1-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="594e1-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="594e1-473">Reguły modułu ponownego zapisywania adresów URL usług IIS</span><span class="sxs-lookup"><span data-stu-id="594e1-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="594e1-474">Aby użyć tego samego zestawu reguł, który ma zastosowanie do modułu ponowne zapisywanie adresów URL usług IIS, użyj <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="594e1-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="594e1-475">Upewnij się, że plik reguł został wdrożony razem z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="594e1-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="594e1-476">Nie należy kierować oprogramowanie pośredniczące do korzystania z pliku *Web. config* aplikacji podczas uruchamiania w usługach IIS systemu Windows Server.</span><span class="sxs-lookup"><span data-stu-id="594e1-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="594e1-477">W przypadku usług IIS te reguły powinny być przechowywane poza plikiem *Web. config* aplikacji, aby uniknąć konfliktów z modułem ponownego zapisywania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="594e1-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="594e1-478">Aby uzyskać więcej informacji i przykłady reguł modułu ponownego zapisywania adresów URL usług IIS, zobacz [using URL Rewrite module 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) i [informacje konfiguracyjne modułu ponownego zapisywania adresu URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="594e1-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="594e1-479">A służy <xref:System.IO.StreamReader> do odczytywania reguł z pliku reguł *IISUrlRewrite. XML* :</span><span class="sxs-lookup"><span data-stu-id="594e1-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="594e1-480">Przykładowa aplikacja ponownie zapisuje żądania z `/iis-rules-rewrite/(.*)` programu do `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="594e1-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="594e1-481">Odpowiedź jest wysyłana do klienta z kodem stanu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="594e1-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="594e1-482">Oryginalne żądanie:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="594e1-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądania i odpowiedzi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="594e1-484">Jeśli masz aktywny moduł ponownego zapisywania usług IIS z skonfigurowanymi regułami na poziomie serwera, które byłyby wpływać na aplikację na niepożądane sposoby, możesz wyłączyć moduł ponownego zapisywania usług IIS dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="594e1-485">Aby uzyskać więcej informacji, zobacz [wyłączanie modułów IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="594e1-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="594e1-486">Nieobsługiwane funkcje</span><span class="sxs-lookup"><span data-stu-id="594e1-486">Unsupported features</span></span>

<span data-ttu-id="594e1-487">Oprogramowanie pośredniczące wydane z ASP.NET Core 2. x nie obsługuje następujących funkcji modułu ponownego zapisywania adresów URL usług IIS:</span><span class="sxs-lookup"><span data-stu-id="594e1-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="594e1-488">Reguły ruchu wychodzącego</span><span class="sxs-lookup"><span data-stu-id="594e1-488">Outbound Rules</span></span>
* <span data-ttu-id="594e1-489">Niestandardowe zmienne serwera</span><span class="sxs-lookup"><span data-stu-id="594e1-489">Custom Server Variables</span></span>
* <span data-ttu-id="594e1-490">Symbole wieloznaczne</span><span class="sxs-lookup"><span data-stu-id="594e1-490">Wildcards</span></span>
* <span data-ttu-id="594e1-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="594e1-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="594e1-492">Obsługiwane Zmienne serwera</span><span class="sxs-lookup"><span data-stu-id="594e1-492">Supported server variables</span></span>

<span data-ttu-id="594e1-493">Oprogramowanie pośredniczące obsługuje następujące zmienne serwera modułu ponownego zapisywania adresów URL usług IIS:</span><span class="sxs-lookup"><span data-stu-id="594e1-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="594e1-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="594e1-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="594e1-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="594e1-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="594e1-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="594e1-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="594e1-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="594e1-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="594e1-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="594e1-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="594e1-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="594e1-499">HTTP_HOST</span></span>
* <span data-ttu-id="594e1-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="594e1-500">HTTP_REFERER</span></span>
* <span data-ttu-id="594e1-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="594e1-501">HTTP_URL</span></span>
* <span data-ttu-id="594e1-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="594e1-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="594e1-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="594e1-503">HTTPS</span></span>
* <span data-ttu-id="594e1-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="594e1-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="594e1-505">QUERY_STRING</span></span>
* <span data-ttu-id="594e1-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="594e1-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="594e1-507">REMOTE_PORT</span></span>
* <span data-ttu-id="594e1-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="594e1-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="594e1-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="594e1-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="594e1-510">Możesz również uzyskać <xref:Microsoft.Extensions.FileProviders.IFileProvider> za pośrednictwem a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="594e1-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="594e1-511">Takie podejście może zapewnić większą elastyczność lokalizacji plików reguł ponownego zapisywania.</span><span class="sxs-lookup"><span data-stu-id="594e1-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="594e1-512">Upewnij się, że pliki reguł ponownego zapisywania są wdrożone na serwerze pod podaną ścieżką.</span><span class="sxs-lookup"><span data-stu-id="594e1-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="594e1-513">Reguła oparta na metodzie</span><span class="sxs-lookup"><span data-stu-id="594e1-513">Method-based rule</span></span>

<span data-ttu-id="594e1-514">Użyj <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> , aby zaimplementować własną logikę reguł w metodzie.</span><span class="sxs-lookup"><span data-stu-id="594e1-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="594e1-515">`Add`udostępnia <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , który udostępnia do <xref:Microsoft.AspNetCore.Http.HttpContext> użycia w Twojej metodzie.</span><span class="sxs-lookup"><span data-stu-id="594e1-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="594e1-516">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) określa sposób obsługi dodatkowego przetwarzania potoku.</span><span class="sxs-lookup"><span data-stu-id="594e1-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="594e1-517">Ustaw wartość na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> pól opisanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="594e1-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="594e1-518">Akcja</span><span class="sxs-lookup"><span data-stu-id="594e1-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="594e1-519">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-520">'Blazor'</span></span>
- <span data-ttu-id="594e1-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-521">'Identity'</span></span>
- <span data-ttu-id="594e1-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-523">'Razor'</span></span>
- <span data-ttu-id="594e1-524">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-525">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-526">'Blazor'</span></span>
- <span data-ttu-id="594e1-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-527">'Identity'</span></span>
- <span data-ttu-id="594e1-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-529">'Razor'</span></span>
- <span data-ttu-id="594e1-530">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-531">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-532">'Blazor'</span></span>
- <span data-ttu-id="594e1-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-533">'Identity'</span></span>
- <span data-ttu-id="594e1-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-535">'Razor'</span></span>
- <span data-ttu-id="594e1-536">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-537">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-538">'Blazor'</span></span>
- <span data-ttu-id="594e1-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-539">'Identity'</span></span>
- <span data-ttu-id="594e1-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-541">'Razor'</span></span>
- <span data-ttu-id="594e1-542">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-543">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-544">'Blazor'</span></span>
- <span data-ttu-id="594e1-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-545">'Identity'</span></span>
- <span data-ttu-id="594e1-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-547">'Razor'</span></span>
- <span data-ttu-id="594e1-548">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-549">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-550">'Blazor'</span></span>
- <span data-ttu-id="594e1-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-551">'Identity'</span></span>
- <span data-ttu-id="594e1-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-553">'Razor'</span></span>
- <span data-ttu-id="594e1-554">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-555">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-556">'Blazor'</span></span>
- <span data-ttu-id="594e1-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-557">'Identity'</span></span>
- <span data-ttu-id="594e1-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-559">'Razor'</span></span>
- <span data-ttu-id="594e1-560">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-561">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-562">'Blazor'</span></span>
- <span data-ttu-id="594e1-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-563">'Identity'</span></span>
- <span data-ttu-id="594e1-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-565">'Razor'</span></span>
- <span data-ttu-id="594e1-566">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-567">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-568">'Blazor'</span></span>
- <span data-ttu-id="594e1-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-569">'Identity'</span></span>
- <span data-ttu-id="594e1-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-571">'Razor'</span></span>
- <span data-ttu-id="594e1-572">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-573">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-574">'Blazor'</span></span>
- <span data-ttu-id="594e1-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-575">'Identity'</span></span>
- <span data-ttu-id="594e1-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-577">'Razor'</span></span>
- <span data-ttu-id="594e1-578">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-579">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-580">'Blazor'</span></span>
- <span data-ttu-id="594e1-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-581">'Identity'</span></span>
- <span data-ttu-id="594e1-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-583">'Razor'</span></span>
- <span data-ttu-id="594e1-584">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-585">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-586">'Blazor'</span></span>
- <span data-ttu-id="594e1-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-587">'Identity'</span></span>
- <span data-ttu-id="594e1-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-589">'Razor'</span></span>
- <span data-ttu-id="594e1-590">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-591">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-592">'Blazor'</span></span>
- <span data-ttu-id="594e1-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-593">'Identity'</span></span>
- <span data-ttu-id="594e1-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-595">'Razor'</span></span>
- <span data-ttu-id="594e1-596">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-597">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-598">'Blazor'</span></span>
- <span data-ttu-id="594e1-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-599">'Identity'</span></span>
- <span data-ttu-id="594e1-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-601">'Razor'</span></span>
- <span data-ttu-id="594e1-602">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-603">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-604">'Blazor'</span></span>
- <span data-ttu-id="594e1-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-605">'Identity'</span></span>
- <span data-ttu-id="594e1-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-607">'Razor'</span></span>
- <span data-ttu-id="594e1-608">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-609">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-610">'Blazor'</span></span>
- <span data-ttu-id="594e1-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-611">'Identity'</span></span>
- <span data-ttu-id="594e1-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-613">'Razor'</span></span>
- <span data-ttu-id="594e1-614">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-614">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-615">------------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-616">'Blazor'</span></span>
- <span data-ttu-id="594e1-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-617">'Identity'</span></span>
- <span data-ttu-id="594e1-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-619">'Razor'</span></span>
- <span data-ttu-id="594e1-620">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-621">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-622">'Blazor'</span></span>
- <span data-ttu-id="594e1-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-623">'Identity'</span></span>
- <span data-ttu-id="594e1-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-625">'Razor'</span></span>
- <span data-ttu-id="594e1-626">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-627">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-628">'Blazor'</span></span>
- <span data-ttu-id="594e1-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-629">'Identity'</span></span>
- <span data-ttu-id="594e1-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-631">'Razor'</span></span>
- <span data-ttu-id="594e1-632">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-633">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-634">'Blazor'</span></span>
- <span data-ttu-id="594e1-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-635">'Identity'</span></span>
- <span data-ttu-id="594e1-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-637">'Razor'</span></span>
- <span data-ttu-id="594e1-638">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-639">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-640">'Blazor'</span></span>
- <span data-ttu-id="594e1-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-641">'Identity'</span></span>
- <span data-ttu-id="594e1-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-643">'Razor'</span></span>
- <span data-ttu-id="594e1-644">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-645">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-646">'Blazor'</span></span>
- <span data-ttu-id="594e1-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-647">'Identity'</span></span>
- <span data-ttu-id="594e1-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-649">'Razor'</span></span>
- <span data-ttu-id="594e1-650">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-651">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-652">'Blazor'</span></span>
- <span data-ttu-id="594e1-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-653">'Identity'</span></span>
- <span data-ttu-id="594e1-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-655">'Razor'</span></span>
- <span data-ttu-id="594e1-656">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-657">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-658">'Blazor'</span></span>
- <span data-ttu-id="594e1-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-659">'Identity'</span></span>
- <span data-ttu-id="594e1-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-661">'Razor'</span></span>
- <span data-ttu-id="594e1-662">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-663">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-664">'Blazor'</span></span>
- <span data-ttu-id="594e1-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-665">'Identity'</span></span>
- <span data-ttu-id="594e1-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-667">'Razor'</span></span>
- <span data-ttu-id="594e1-668">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-669">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-670">'Blazor'</span></span>
- <span data-ttu-id="594e1-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-671">'Identity'</span></span>
- <span data-ttu-id="594e1-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-673">'Razor'</span></span>
- <span data-ttu-id="594e1-674">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-675">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-676">'Blazor'</span></span>
- <span data-ttu-id="594e1-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-677">'Identity'</span></span>
- <span data-ttu-id="594e1-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-679">'Razor'</span></span>
- <span data-ttu-id="594e1-680">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-681">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-682">'Blazor'</span></span>
- <span data-ttu-id="594e1-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-683">'Identity'</span></span>
- <span data-ttu-id="594e1-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-685">'Razor'</span></span>
- <span data-ttu-id="594e1-686">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-687">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-688">'Blazor'</span></span>
- <span data-ttu-id="594e1-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-689">'Identity'</span></span>
- <span data-ttu-id="594e1-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-691">'Razor'</span></span>
- <span data-ttu-id="594e1-692">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-693">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-694">'Blazor'</span></span>
- <span data-ttu-id="594e1-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-695">'Identity'</span></span>
- <span data-ttu-id="594e1-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-697">'Razor'</span></span>
- <span data-ttu-id="594e1-698">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-699">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-700">'Blazor'</span></span>
- <span data-ttu-id="594e1-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-701">'Identity'</span></span>
- <span data-ttu-id="594e1-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-703">'Razor'</span></span>
- <span data-ttu-id="594e1-704">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-705">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-706">'Blazor'</span></span>
- <span data-ttu-id="594e1-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-707">'Identity'</span></span>
- <span data-ttu-id="594e1-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-709">'Razor'</span></span>
- <span data-ttu-id="594e1-710">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-711">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-712">'Blazor'</span></span>
- <span data-ttu-id="594e1-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-713">'Identity'</span></span>
- <span data-ttu-id="594e1-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-715">'Razor'</span></span>
- <span data-ttu-id="594e1-716">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-717">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-718">'Blazor'</span></span>
- <span data-ttu-id="594e1-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-719">'Identity'</span></span>
- <span data-ttu-id="594e1-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-721">'Razor'</span></span>
- <span data-ttu-id="594e1-722">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-723">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-724">'Blazor'</span></span>
- <span data-ttu-id="594e1-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-725">'Identity'</span></span>
- <span data-ttu-id="594e1-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-727">'Razor'</span></span>
- <span data-ttu-id="594e1-728">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-729">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-730">'Blazor'</span></span>
- <span data-ttu-id="594e1-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-731">'Identity'</span></span>
- <span data-ttu-id="594e1-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-733">'Razor'</span></span>
- <span data-ttu-id="594e1-734">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-735">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-736">'Blazor'</span></span>
- <span data-ttu-id="594e1-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-737">'Identity'</span></span>
- <span data-ttu-id="594e1-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-739">'Razor'</span></span>
- <span data-ttu-id="594e1-740">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-741">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-742">'Blazor'</span></span>
- <span data-ttu-id="594e1-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-743">'Identity'</span></span>
- <span data-ttu-id="594e1-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-745">'Razor'</span></span>
- <span data-ttu-id="594e1-746">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-747">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-748">'Blazor'</span></span>
- <span data-ttu-id="594e1-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-749">'Identity'</span></span>
- <span data-ttu-id="594e1-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-751">'Razor'</span></span>
- <span data-ttu-id="594e1-752">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-753">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-754">'Blazor'</span></span>
- <span data-ttu-id="594e1-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-755">'Identity'</span></span>
- <span data-ttu-id="594e1-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-757">'Razor'</span></span>
- <span data-ttu-id="594e1-758">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-759">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-760">'Blazor'</span></span>
- <span data-ttu-id="594e1-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-761">'Identity'</span></span>
- <span data-ttu-id="594e1-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-763">'Razor'</span></span>
- <span data-ttu-id="594e1-764">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-765">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-766">'Blazor'</span></span>
- <span data-ttu-id="594e1-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-767">'Identity'</span></span>
- <span data-ttu-id="594e1-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-769">'Razor'</span></span>
- <span data-ttu-id="594e1-770">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-771">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-772">'Blazor'</span></span>
- <span data-ttu-id="594e1-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-773">'Identity'</span></span>
- <span data-ttu-id="594e1-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-775">'Razor'</span></span>
- <span data-ttu-id="594e1-776">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-777">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-778">'Blazor'</span></span>
- <span data-ttu-id="594e1-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-779">'Identity'</span></span>
- <span data-ttu-id="594e1-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-781">'Razor'</span></span>
- <span data-ttu-id="594e1-782">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-783">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-784">'Blazor'</span></span>
- <span data-ttu-id="594e1-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-785">'Identity'</span></span>
- <span data-ttu-id="594e1-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-787">'Razor'</span></span>
- <span data-ttu-id="594e1-788">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-789">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-790">'Blazor'</span></span>
- <span data-ttu-id="594e1-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-791">'Identity'</span></span>
- <span data-ttu-id="594e1-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-793">'Razor'</span></span>
- <span data-ttu-id="594e1-794">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-794">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-795">-------------------------------- | | `RuleResult.ContinueRules`(ustawienie domyślne) | Kontynuuj stosowanie reguł.</span><span class="sxs-lookup"><span data-stu-id="594e1-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="594e1-796">| | `RuleResult.EndResponse`             | Zatrzymaj stosowanie reguł i Wyślij odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="594e1-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="594e1-797">| | `RuleResult.SkipRemainingRules`      | Zatrzymaj stosowanie reguł i Wyślij kontekst do następnego oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="594e1-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="594e1-798">Przykładowa aplikacja przedstawia metodę, która przekierowuje żądania dla ścieżek kończących się na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="594e1-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="594e1-799">Jeśli zostanie wysłane żądanie `/file.xml` , żądanie jest przekierowywane do `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="594e1-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="594e1-800">Kod stanu jest ustawiony na *301 — trwale przeniesiony*.</span><span class="sxs-lookup"><span data-stu-id="594e1-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="594e1-801">Gdy przeglądarka wykonuje nowe żądanie dla */XmlFiles/File.XML*, oprogramowanie pośredniczące plików statycznych zachowuje ten plik na kliencie z folderu *wwwroot/XmlFiles* .</span><span class="sxs-lookup"><span data-stu-id="594e1-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="594e1-802">W przypadku przekierowania jawnie ustaw kod stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="594e1-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="594e1-803">W przeciwnym razie zwracany jest kod stanu *200-OK* i przekierowanie nie wystąpi na kliencie.</span><span class="sxs-lookup"><span data-stu-id="594e1-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="594e1-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="594e1-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="594e1-805">Takie podejście może również ponownie zapisywać żądania.</span><span class="sxs-lookup"><span data-stu-id="594e1-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="594e1-806">Przykładowa aplikacja pokazuje, jak ponownie napisać ścieżkę do dowolnego żądania pliku tekstowego, aby zapewnić obsługę pliku tekstowego *pliku. txt* z folderu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="594e1-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="594e1-807">Oprogramowanie pośredniczące plików statycznych obsługuje plik na podstawie zaktualizowanej ścieżki żądania:</span><span class="sxs-lookup"><span data-stu-id="594e1-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="594e1-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="594e1-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="594e1-809">Reguła oparta na IRule</span><span class="sxs-lookup"><span data-stu-id="594e1-809">IRule-based rule</span></span>

<span data-ttu-id="594e1-810">Użyj, <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> Aby użyć logiki reguły w klasie, która implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> interfejs.</span><span class="sxs-lookup"><span data-stu-id="594e1-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="594e1-811">`IRule`zapewnia większą elastyczność w porównaniu z użyciem metody opartej na metodzie.</span><span class="sxs-lookup"><span data-stu-id="594e1-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="594e1-812">Klasa implementacji może zawierać konstruktora, który umożliwia przekazywanie parametrów dla <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metody.</span><span class="sxs-lookup"><span data-stu-id="594e1-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="594e1-813">Wartości parametrów w aplikacji przykładowej dla `extension` i `newPath` są sprawdzane pod kątem spełnienia kilku warunków.</span><span class="sxs-lookup"><span data-stu-id="594e1-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="594e1-814">`extension`Musi zawierać wartość i musi mieć wartość *. png*, *. jpg*lub *. gif*.</span><span class="sxs-lookup"><span data-stu-id="594e1-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="594e1-815">Jeśli `newPath` jest nieprawidłowa, <xref:System.ArgumentException> zostanie zgłoszony.</span><span class="sxs-lookup"><span data-stu-id="594e1-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="594e1-816">Jeśli zostanie wysłane żądanie dotyczące pliku *Image. png*, żądanie jest przekierowywane do `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="594e1-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="594e1-817">Jeśli zostanie wysłane żądanie do *obrazu. jpg*, żądanie jest przekierowywane do `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="594e1-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="594e1-818">Kod stanu jest ustawiony na *301 — trwale przeniesiony*, a `context.Result` ustawienie jest ustawione na zatrzymanie przetwarzania reguł i wysłanie odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="594e1-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="594e1-819">Oryginalne żądanie:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="594e1-819">Original Request: `/image.png`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi dla pliku Image. png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="594e1-821">Oryginalne żądanie:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="594e1-821">Original Request: `/image.jpg`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi dla obrazu. jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="594e1-823">Przykłady wyrażeń regularnych</span><span class="sxs-lookup"><span data-stu-id="594e1-823">Regex examples</span></span>

| <span data-ttu-id="594e1-824">Cel</span><span class="sxs-lookup"><span data-stu-id="594e1-824">Goal</span></span> | <span data-ttu-id="594e1-825">Ciąg wyrażenia regularnego &</span><span class="sxs-lookup"><span data-stu-id="594e1-825">Regex String &</span></span><br><span data-ttu-id="594e1-826">Przykład dopasowania</span><span class="sxs-lookup"><span data-stu-id="594e1-826">Match Example</span></span> | <span data-ttu-id="594e1-827">& ciągu zamiennego</span><span class="sxs-lookup"><span data-stu-id="594e1-827">Replacement String &</span></span><br><span data-ttu-id="594e1-828">Przykład danych wyjściowych</span><span class="sxs-lookup"><span data-stu-id="594e1-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="594e1-829">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-830">'Blazor'</span></span>
- <span data-ttu-id="594e1-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-831">'Identity'</span></span>
- <span data-ttu-id="594e1-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-833">'Razor'</span></span>
- <span data-ttu-id="594e1-834">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-835">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-836">'Blazor'</span></span>
- <span data-ttu-id="594e1-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-837">'Identity'</span></span>
- <span data-ttu-id="594e1-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-839">'Razor'</span></span>
- <span data-ttu-id="594e1-840">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-841">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-842">'Blazor'</span></span>
- <span data-ttu-id="594e1-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-843">'Identity'</span></span>
- <span data-ttu-id="594e1-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-845">'Razor'</span></span>
- <span data-ttu-id="594e1-846">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-847">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-848">'Blazor'</span></span>
- <span data-ttu-id="594e1-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-849">'Identity'</span></span>
- <span data-ttu-id="594e1-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-851">'Razor'</span></span>
- <span data-ttu-id="594e1-852">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-853">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-854">'Blazor'</span></span>
- <span data-ttu-id="594e1-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-855">'Identity'</span></span>
- <span data-ttu-id="594e1-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-857">'Razor'</span></span>
- <span data-ttu-id="594e1-858">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-859">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-860">'Blazor'</span></span>
- <span data-ttu-id="594e1-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-861">'Identity'</span></span>
- <span data-ttu-id="594e1-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-863">'Razor'</span></span>
- <span data-ttu-id="594e1-864">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-865">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-866">'Blazor'</span></span>
- <span data-ttu-id="594e1-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-867">'Identity'</span></span>
- <span data-ttu-id="594e1-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-869">'Razor'</span></span>
- <span data-ttu-id="594e1-870">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-871">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-872">'Blazor'</span></span>
- <span data-ttu-id="594e1-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-873">'Identity'</span></span>
- <span data-ttu-id="594e1-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-875">'Razor'</span></span>
- <span data-ttu-id="594e1-876">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-877">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-878">'Blazor'</span></span>
- <span data-ttu-id="594e1-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-879">'Identity'</span></span>
- <span data-ttu-id="594e1-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-881">'Razor'</span></span>
- <span data-ttu-id="594e1-882">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-883">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-884">'Blazor'</span></span>
- <span data-ttu-id="594e1-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-885">'Identity'</span></span>
- <span data-ttu-id="594e1-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-887">'Razor'</span></span>
- <span data-ttu-id="594e1-888">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-889">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-890">'Blazor'</span></span>
- <span data-ttu-id="594e1-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-891">'Identity'</span></span>
- <span data-ttu-id="594e1-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-893">'Razor'</span></span>
- <span data-ttu-id="594e1-894">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-895">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-896">'Blazor'</span></span>
- <span data-ttu-id="594e1-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-897">'Identity'</span></span>
- <span data-ttu-id="594e1-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-899">'Razor'</span></span>
- <span data-ttu-id="594e1-900">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-901">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-902">'Blazor'</span></span>
- <span data-ttu-id="594e1-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-903">'Identity'</span></span>
- <span data-ttu-id="594e1-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-905">'Razor'</span></span>
- <span data-ttu-id="594e1-906">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-906">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-907">---------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-908">'Blazor'</span></span>
- <span data-ttu-id="594e1-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-909">'Identity'</span></span>
- <span data-ttu-id="594e1-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-911">'Razor'</span></span>
- <span data-ttu-id="594e1-912">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-913">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-914">'Blazor'</span></span>
- <span data-ttu-id="594e1-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-915">'Identity'</span></span>
- <span data-ttu-id="594e1-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-917">'Razor'</span></span>
- <span data-ttu-id="594e1-918">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-919">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-920">'Blazor'</span></span>
- <span data-ttu-id="594e1-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-921">'Identity'</span></span>
- <span data-ttu-id="594e1-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-923">'Razor'</span></span>
- <span data-ttu-id="594e1-924">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-925">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-926">'Blazor'</span></span>
- <span data-ttu-id="594e1-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-927">'Identity'</span></span>
- <span data-ttu-id="594e1-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-929">'Razor'</span></span>
- <span data-ttu-id="594e1-930">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-931">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-932">'Blazor'</span></span>
- <span data-ttu-id="594e1-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-933">'Identity'</span></span>
- <span data-ttu-id="594e1-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-935">'Razor'</span></span>
- <span data-ttu-id="594e1-936">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-937">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-938">'Blazor'</span></span>
- <span data-ttu-id="594e1-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-939">'Identity'</span></span>
- <span data-ttu-id="594e1-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-941">'Razor'</span></span>
- <span data-ttu-id="594e1-942">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-943">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-944">'Blazor'</span></span>
- <span data-ttu-id="594e1-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-945">'Identity'</span></span>
- <span data-ttu-id="594e1-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-947">'Razor'</span></span>
- <span data-ttu-id="594e1-948">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-949">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-950">'Blazor'</span></span>
- <span data-ttu-id="594e1-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-951">'Identity'</span></span>
- <span data-ttu-id="594e1-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-953">'Razor'</span></span>
- <span data-ttu-id="594e1-954">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-955">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-956">'Blazor'</span></span>
- <span data-ttu-id="594e1-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-957">'Identity'</span></span>
- <span data-ttu-id="594e1-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-959">'Razor'</span></span>
- <span data-ttu-id="594e1-960">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-961">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-962">'Blazor'</span></span>
- <span data-ttu-id="594e1-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-963">'Identity'</span></span>
- <span data-ttu-id="594e1-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-965">'Razor'</span></span>
- <span data-ttu-id="594e1-966">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-967">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-968">'Blazor'</span></span>
- <span data-ttu-id="594e1-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-969">'Identity'</span></span>
- <span data-ttu-id="594e1-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-971">'Razor'</span></span>
- <span data-ttu-id="594e1-972">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-973">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-974">'Blazor'</span></span>
- <span data-ttu-id="594e1-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-975">'Identity'</span></span>
- <span data-ttu-id="594e1-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-977">'Razor'</span></span>
- <span data-ttu-id="594e1-978">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-979">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-980">'Blazor'</span></span>
- <span data-ttu-id="594e1-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-981">'Identity'</span></span>
- <span data-ttu-id="594e1-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-983">'Razor'</span></span>
- <span data-ttu-id="594e1-984">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-985">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-986">'Blazor'</span></span>
- <span data-ttu-id="594e1-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-987">'Identity'</span></span>
- <span data-ttu-id="594e1-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-989">'Razor'</span></span>
- <span data-ttu-id="594e1-990">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-991">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-992">'Blazor'</span></span>
- <span data-ttu-id="594e1-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-993">'Identity'</span></span>
- <span data-ttu-id="594e1-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-995">'Razor'</span></span>
- <span data-ttu-id="594e1-996">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-997">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-998">'Blazor'</span></span>
- <span data-ttu-id="594e1-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-999">'Identity'</span></span>
- <span data-ttu-id="594e1-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1001">'Razor'</span></span>
- <span data-ttu-id="594e1-1002">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1003">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1004">'Blazor'</span></span>
- <span data-ttu-id="594e1-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1005">'Identity'</span></span>
- <span data-ttu-id="594e1-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1007">'Razor'</span></span>
- <span data-ttu-id="594e1-1008">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-1009">------------------- | | Zapisz ścieżkę do ciągu QueryString |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="594e1-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="594e1-1010">`/path?var1=abc&var2=123`| | Ukośnik na końcu paska |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="594e1-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="594e1-1011">`/path`| | Wymuś końcowy ukośnika |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="594e1-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="594e1-1012">`/path/`| | Unikaj ponownego zapisywania konkretnych żądań | `^(.*)(?<!\.axd)$`lub`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="594e1-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="594e1-1013">Opcję`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="594e1-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="594e1-1014">Znaleziono`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="594e1-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="594e1-1015">`/resource.axd`| | Zmień rozmieszczenie segmentów adresu URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="594e1-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="594e1-1016">`path/3/2/1`| | Zastępowanie segmentu adresu URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="594e1-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="594e1-1017">Ten dokument zawiera wprowadzenie do ponownego zapisywania adresów URL z instrukcjami dotyczącymi sposobu korzystania z programu pośredniczącego do ponownego zapisywania adresów URL w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="594e1-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="594e1-1018">Ponowne zapisywanie adresu URL to czynność modyfikacji adresów URL żądań na podstawie co najmniej jednej wstępnie zdefiniowanej reguły.</span><span class="sxs-lookup"><span data-stu-id="594e1-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="594e1-1019">Ponowne zapisywanie adresów URL powoduje utworzenie abstrakcji między lokalizacjami zasobów a ich adresami, dzięki czemu lokalizacje i adresy nie są ściśle połączone.</span><span class="sxs-lookup"><span data-stu-id="594e1-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="594e1-1020">Ponowne zapisywanie adresów URL jest cenne w kilku scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="594e1-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="594e1-1021">Przenoszenie lub zastępowanie zasobów serwera tymczasowo lub trwale i utrzymuje stałe lokalizatory dla tych zasobów.</span><span class="sxs-lookup"><span data-stu-id="594e1-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="594e1-1022">Podziel przetwarzanie żądań między różne aplikacje lub w obszarach jednej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="594e1-1023">Usuwanie, Dodawanie lub organizowanie segmentów adresów URL w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="594e1-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="594e1-1024">Optymalizuj publiczne adresy URL dla optymalizacji aparatu wyszukiwania (wyszukiwarka).</span><span class="sxs-lookup"><span data-stu-id="594e1-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="594e1-1025">Zezwól na używanie przyjaznych publicznych adresów URL, aby ułatwić odwiedzającym zapowiadanie zawartości zwróconej przez żądanie zasobu.</span><span class="sxs-lookup"><span data-stu-id="594e1-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="594e1-1026">Przekieruj niezabezpieczone żądania do bezpiecznych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="594e1-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="594e1-1027">Zapobiegaj hotlinking, gdzie lokacja zewnętrzna używa hostowanego zasobu statycznego w innej lokacji przez połączenie elementu zawartości z własną zawartością.</span><span class="sxs-lookup"><span data-stu-id="594e1-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="594e1-1028">Ponowne zapisywanie adresów URL może zmniejszyć wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="594e1-1029">Jeśli to możliwe, należy ograniczyć liczbę i złożoność reguł.</span><span class="sxs-lookup"><span data-stu-id="594e1-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="594e1-1030">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="594e1-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="594e1-1031">Przekierowywanie adresów URL i ponowne zapisywanie adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="594e1-1032">Różnica między *przekierowaniami adresów URL* i *przepisaniem adresów URL* jest subtelna, ale ma ważne konsekwencje dla udostępniania zasobów klientom.</span><span class="sxs-lookup"><span data-stu-id="594e1-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="594e1-1033">Ponowne zapisywanie oprogramowania pośredniczącego w usłudze ASP.NET Core jest w stanie sprostać potrzebom obu tych elementów.</span><span class="sxs-lookup"><span data-stu-id="594e1-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="594e1-1034">*Przekierowanie adresu URL* obejmuje operację po stronie klienta, w której klient otrzymuje dostęp do zasobu pod innym adresem niż pierwotnie żądany klient.</span><span class="sxs-lookup"><span data-stu-id="594e1-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="594e1-1035">Wymaga to przeprowadzenia rundy na serwerze.</span><span class="sxs-lookup"><span data-stu-id="594e1-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="594e1-1036">Adres URL przekierowania zwracany do klienta pojawia się na pasku adresu przeglądarki, gdy klient wysyła nowe żądanie dla zasobu.</span><span class="sxs-lookup"><span data-stu-id="594e1-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="594e1-1037">W `/resource` przypadku *przekierowania* do programu `/different-resource` serwer odpowiada, że klient powinien uzyskać zasób przy `/different-resource` użyciu kodu stanu wskazującego, że przekierowanie jest tymczasowe lub trwałe.</span><span class="sxs-lookup"><span data-stu-id="594e1-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Punkt końcowy usługi WebAPI został tymczasowo zmieniony z wersji 1 (v1) do wersji 2 (v2) na serwerze.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="594e1-1043">Podczas przekierowywania żądań do innego adresu URL wskaż, czy przekierowanie jest trwałe, czy tymczasowe, określając kod stanu z odpowiedzią:</span><span class="sxs-lookup"><span data-stu-id="594e1-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="594e1-1044">*301-przesunięty* kod stanu jest używany, gdy zasób ma nowy, stały adres URL i chcesz wydać klientowi, że wszystkie przyszłe żądania dla zasobu powinny używać nowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="594e1-1045">*Klient może buforować i ponownie używać odpowiedzi po odebraniu kodu stanu 301.*</span><span class="sxs-lookup"><span data-stu-id="594e1-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="594e1-1046">Kod stanu *znaleziony przez 302* jest używany, gdy przekierowywanie jest tymczasowe lub zwykle może ulec zmianie.</span><span class="sxs-lookup"><span data-stu-id="594e1-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="594e1-1047">Kod stanu 302 wskazuje na klienta, aby nie przechowywać adresu URL i używać go w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="594e1-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="594e1-1048">Aby uzyskać więcej informacji na temat kodów stanu, zobacz [RFC 2616: definicje kodów stanu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="594e1-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="594e1-1049">Ponowne *Zapisywanie adresów URL* jest operacją po stronie serwera, która dostarcza zasób z innego adresu zasobu niż żądany klient.</span><span class="sxs-lookup"><span data-stu-id="594e1-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="594e1-1050">Ponowne zapisywanie adresu URL nie wymaga przejazdu na serwer.</span><span class="sxs-lookup"><span data-stu-id="594e1-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="594e1-1051">Zwrotny adres URL nie jest zwracany do klienta i nie jest wyświetlany na pasku adresu przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="594e1-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="594e1-1052">Jeśli `/resource` program zostanie ponownie *zapisany* w programie `/different-resource` , serwer *wewnętrznie* pobiera i zwraca zasób w `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="594e1-1053">Mimo że klient może być w stanie pobrać zasób przy zapisywanym adresie URL, klient nie ma informacji o tym, że zasób istnieje pod adresem URL, gdy wysyła żądanie i otrzymuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="594e1-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Punkt końcowy usługi WebAPI został zmieniony z wersji 1 (v1) na wersję 2 (v2) na serwerze.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="594e1-1058">Przykładowa aplikacja do ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1058">URL rewriting sample app</span></span>

<span data-ttu-id="594e1-1059">Możesz zapoznać się z funkcjami zapisywania oprogramowania pośredniczącego za pomocą [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="594e1-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="594e1-1060">Aplikacja stosuje reguły przekierowania i ponownego zapisywania oraz pokazuje przekierowany lub ponownie zapisany adres URL dla kilku scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="594e1-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="594e1-1061">Kiedy używać oprogramowania pośredniczącego ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="594e1-1062">Używaj ponownego zapisywania adresów URL, gdy nie możesz użyć następujących metod:</span><span class="sxs-lookup"><span data-stu-id="594e1-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="594e1-1063">Moduł ponownego zapisywania adresów URL z usługami IIS w systemie Windows Server</span><span class="sxs-lookup"><span data-stu-id="594e1-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="594e1-1064">Moduł Apache mod_rewrite na serwerze Apache</span><span class="sxs-lookup"><span data-stu-id="594e1-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="594e1-1065">Ponowne zapisywanie adresów URL w witrynie Nginx</span><span class="sxs-lookup"><span data-stu-id="594e1-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="594e1-1066">Należy również użyć oprogramowania pośredniczącego, gdy aplikacja jest hostowana na [serwerze HTTP. sys](xref:fundamentals/servers/httpsys) (wcześniej nazywanej webListener).</span><span class="sxs-lookup"><span data-stu-id="594e1-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="594e1-1067">Główne przyczyny używania technologii zapisywania adresów URL opartych na serwerze w usługach IIS, Apache i Nginx są następujące:</span><span class="sxs-lookup"><span data-stu-id="594e1-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="594e1-1068">Oprogramowanie pośredniczące nie obsługuje pełnych funkcji tych modułów.</span><span class="sxs-lookup"><span data-stu-id="594e1-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="594e1-1069">Niektóre funkcje modułów serwera nie współpracują z projektami ASP.NET Core, takimi jak `IsFile` i `IsDirectory` ograniczeniami modułu ponownego zapisywania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="594e1-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="594e1-1070">W tych scenariuszach zamiast tego należy użyć oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="594e1-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="594e1-1071">Wydajność oprogramowania pośredniczącego prawdopodobnie nie jest zgodna z tymi modułami.</span><span class="sxs-lookup"><span data-stu-id="594e1-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="594e1-1072">Testy porównawcze są jedynym sposobem, aby wiedzieć, że metoda obniża wydajność w najbardziej lub niewielkim stopniu wydajności.</span><span class="sxs-lookup"><span data-stu-id="594e1-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="594e1-1073">Pakiet</span><span class="sxs-lookup"><span data-stu-id="594e1-1073">Package</span></span>

<span data-ttu-id="594e1-1074">Aby uwzględnić oprogramowanie pośredniczące w projekcie, Dodaj odwołanie do pakietu do [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) w pliku projektu, który zawiera pakiet [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) .</span><span class="sxs-lookup"><span data-stu-id="594e1-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="594e1-1075">Gdy nie korzystasz z `Microsoft.AspNetCore.App` pakietu, Dodaj odwołanie do projektu do `Microsoft.AspNetCore.Rewrite` pakietu.</span><span class="sxs-lookup"><span data-stu-id="594e1-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="594e1-1076">Rozszerzenie i opcje</span><span class="sxs-lookup"><span data-stu-id="594e1-1076">Extension and options</span></span>

<span data-ttu-id="594e1-1077">Ustanów reguły ponownego zapisywania i przekierowywania adresów URL, tworząc wystąpienie klasy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) z metodami rozszerzającymi dla każdej z reguł ponownego zapisywania.</span><span class="sxs-lookup"><span data-stu-id="594e1-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="594e1-1078">Łączenie wielu reguł w kolejności, w jakiej mają być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="594e1-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="594e1-1079">`RewriteOptions`Są one przesyłane do programu pośredniczącego na potrzeby ponownego zapisywania adresów URL, ponieważ są dodawane do potoku żądania przy użyciu <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="594e1-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="594e1-1080">Przekieruj do sieci Web inne niż www</span><span class="sxs-lookup"><span data-stu-id="594e1-1080">Redirect non-www to www</span></span>

<span data-ttu-id="594e1-1081">Trzy opcje Zezwalaj aplikacji na Przekierowywanie żądań, które nie są `www` żądaniami do `www` :</span><span class="sxs-lookup"><span data-stu-id="594e1-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="594e1-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trwale Przekieruj żądanie do domeny podrzędnej, `www` Jeśli żądanie jest inne niż `www` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="594e1-1083">Przekierowuje kod stanu [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="594e1-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="594e1-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Przekieruj żądanie do domeny podrzędnej `www` , jeśli żądanie przychodzące jest inne niż `www` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="594e1-1085">Przekierowuje kod stanu [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="594e1-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="594e1-1086">Przeciążenie umożliwia podanie kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="594e1-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="594e1-1087">Użyj pola <xref:Microsoft.AspNetCore.Http.StatusCodes> klasy do przypisania kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="594e1-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="594e1-1088">Przekierowywanie adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1088">URL redirect</span></span>

<span data-ttu-id="594e1-1089">Użyj <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> , aby przekierować żądania.</span><span class="sxs-lookup"><span data-stu-id="594e1-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="594e1-1090">Pierwszy parametr zawiera wyrażenie regularne do dopasowania na ścieżce przychodzącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="594e1-1091">Drugi parametr jest ciągiem zamiennym.</span><span class="sxs-lookup"><span data-stu-id="594e1-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="594e1-1092">Trzeci parametr, jeśli obecny, określa kod stanu.</span><span class="sxs-lookup"><span data-stu-id="594e1-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="594e1-1093">Jeśli nie określisz kodu stanu, kod stanu zostanie zmieniony na *302-znaleziono*, co oznacza, że zasób jest tymczasowo przenoszony lub zastępowany.</span><span class="sxs-lookup"><span data-stu-id="594e1-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="594e1-1094">W przeglądarce z włączonymi narzędziami deweloperskimi Utwórz żądanie do przykładowej aplikacji ze ścieżką `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="594e1-1095">Wyrażenie regularne dopasowuje ścieżkę żądania w `redirect-rule/(.*)` , a ścieżka jest zastępowana przez `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="594e1-1096">Adres URL przekierowania jest wysyłany z powrotem do klienta z kodem stanu *znalezionym przez 302* .</span><span class="sxs-lookup"><span data-stu-id="594e1-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="594e1-1097">Przeglądarka tworzy nowe żądanie w adresie URL przekierowania, który jest wyświetlany na pasku adresu przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="594e1-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="594e1-1098">Ponieważ w adresie URL przekierowania nie ma reguł pasujących do przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="594e1-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="594e1-1099">Drugie żądanie odbiera odpowiedź *200-OK* z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="594e1-1100">Treść odpowiedzi zawiera adres URL przekierowania.</span><span class="sxs-lookup"><span data-stu-id="594e1-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="594e1-1101">Po *przekierowaniu*adresu URL do serwera zostanie przeprowadzona runda.</span><span class="sxs-lookup"><span data-stu-id="594e1-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="594e1-1102">Należy zachować ostrożność podczas ustanawiania reguł przekierowań.</span><span class="sxs-lookup"><span data-stu-id="594e1-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="594e1-1103">Reguły przekierowania są oceniane dla każdego żądania do aplikacji, w tym po przekierowaniu.</span><span class="sxs-lookup"><span data-stu-id="594e1-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="594e1-1104">Można łatwo przypadkowo utworzyć *pętlę nieskończonych przekierowań*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="594e1-1105">Oryginalne żądanie:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="594e1-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="594e1-1107">Część wyrażenia zawartego w nawiasach jest nazywana *grupą przechwytywania*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="594e1-1108">Kropka ( `.` ) wyrażenia oznacza *dopasowanie dowolnego znaku*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="594e1-1109">Gwiazdka ( `*` ) oznacza *Dopasowanie znaku poprzedzającego zero lub więcej razy*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="594e1-1110">W związku z tym, ostatnie dwa segmenty ścieżki adresu URL, `1234/5678` są przechwytywane przez grupę przechwytywania `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="594e1-1111">Każda wartość podaną w adresie URL żądania po `redirect-rule/` przechwyceniu przez tę pojedynczą grupę przechwytywania.</span><span class="sxs-lookup"><span data-stu-id="594e1-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="594e1-1112">W ciągu zamiennym przechwycone grupy są wstawiane do ciągu z symbolem dolara ( `$` ), po którym następuje numer sekwencji przechwytywania.</span><span class="sxs-lookup"><span data-stu-id="594e1-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="594e1-1113">Pierwsza wartość grupy przechwytywania jest pobierana z `$1` , sekunda z `$2` i są dalej sekwencją dla grup przechwytywania w wyrażeniach regularnych.</span><span class="sxs-lookup"><span data-stu-id="594e1-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="594e1-1114">W aplikacji przykładowej wyrażenie regularne ma tylko jedną przechwyconą grupę, więc w ciągu zamiennym istnieje tylko jedna grupa wstrzykiwana, która jest `$1` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="594e1-1115">Gdy reguła zostanie zastosowana, adres URL zmieni się `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="594e1-1116">Przekierowanie adresu URL do bezpiecznego punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="594e1-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="594e1-1117">Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> do przekierowywania żądań HTTP do tego samego hosta i ścieżki przy użyciu protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="594e1-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="594e1-1118">Jeśli nie podano kodu stanu, oprogramowanie pośredniczące domyślnie zostanie *znalezione na 302*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="594e1-1119">Jeśli port nie jest podany:</span><span class="sxs-lookup"><span data-stu-id="594e1-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="594e1-1120">Ustawienia domyślne oprogramowania pośredniczącego `null` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="594e1-1121">Schemat zmienia się na `https` (protokół https), a klient uzyskuje dostęp do zasobu na porcie 443.</span><span class="sxs-lookup"><span data-stu-id="594e1-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="594e1-1122">Poniższy przykład pokazuje, jak ustawić kod stanu na *301 — trwale przeniesiony* i zmienić port na 5001.</span><span class="sxs-lookup"><span data-stu-id="594e1-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="594e1-1123">Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> do przekierowywania niezabezpieczonych żądań do tego samego hosta i ścieżki z bezpiecznym protokołem HTTPS na porcie 443.</span><span class="sxs-lookup"><span data-stu-id="594e1-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="594e1-1124">Oprogramowanie pośredniczące ustawia kod stanu na *301 — trwale przeniesiony*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="594e1-1125">Podczas przekierowywania do bezpiecznego punktu końcowego bez wymagania dla dodatkowych reguł przekierowywania zalecamy używanie oprogramowania pośredniczącego do przekierowania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="594e1-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="594e1-1126">Aby uzyskać więcej informacji, zobacz temat [Wymuś https](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="594e1-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="594e1-1127">Przykładowa aplikacja jest w stanie demonstrować, jak korzystać z `AddRedirectToHttps` lub `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="594e1-1128">Dodaj metodę rozszerzenia do `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="594e1-1129">Wprowadź niezabezpieczone żądanie do aplikacji pod dowolnym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="594e1-1130">Odrzuć ostrzeżenie o zabezpieczeniach przeglądarki, że certyfikat z podpisem własnym jest niezaufany lub Utwórz wyjątek, aby zaufać certyfikatowi.</span><span class="sxs-lookup"><span data-stu-id="594e1-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="594e1-1131">Oryginalne żądanie przy użyciu `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="594e1-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="594e1-1133">Oryginalne żądanie przy użyciu `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="594e1-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="594e1-1135">Regenerowanie adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1135">URL rewrite</span></span>

<span data-ttu-id="594e1-1136">Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> do tworzenia reguły ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="594e1-1137">Pierwszy parametr zawiera wyrażenie regularne do dopasowania w przychodzącej ścieżce adresu URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="594e1-1138">Drugi parametr jest ciągiem zamiennym.</span><span class="sxs-lookup"><span data-stu-id="594e1-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="594e1-1139">Trzeci parametr, `skipRemainingRules: {true|false}` , wskazuje na oprogramowanie pośredniczące, niezależnie od tego, czy pominięcia dodatkowych reguł ponownego zapisu w przypadku zastosowania bieżącej reguły.</span><span class="sxs-lookup"><span data-stu-id="594e1-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="594e1-1140">Oryginalne żądanie:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="594e1-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądania i odpowiedzi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="594e1-1142">W karatach ( `^` ) na początku wyrażenia oznacza to, że dopasowanie rozpoczyna się na początku ścieżki URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="594e1-1143">W poprzednim przykładzie z regułą przekierowania `redirect-rule/(.*)` nie ma żadnych karatów ( `^` ) na początku wyrażenia regularnego.</span><span class="sxs-lookup"><span data-stu-id="594e1-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="594e1-1144">W związku z tym wszystkie znaki mogą poprzedzać `redirect-rule/` w ścieżce pomyślne dopasowanie.</span><span class="sxs-lookup"><span data-stu-id="594e1-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="594e1-1145">Ścieżka</span><span class="sxs-lookup"><span data-stu-id="594e1-1145">Path</span></span>                               | <span data-ttu-id="594e1-1146">Dopasowanie</span><span class="sxs-lookup"><span data-stu-id="594e1-1146">Match</span></span> |
| ---
<span data-ttu-id="594e1-1147">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1148">'Blazor'</span></span>
- <span data-ttu-id="594e1-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1149">'Identity'</span></span>
- <span data-ttu-id="594e1-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1151">'Razor'</span></span>
- <span data-ttu-id="594e1-1152">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1153">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1154">'Blazor'</span></span>
- <span data-ttu-id="594e1-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1155">'Identity'</span></span>
- <span data-ttu-id="594e1-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1157">'Razor'</span></span>
- <span data-ttu-id="594e1-1158">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1159">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1160">'Blazor'</span></span>
- <span data-ttu-id="594e1-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1161">'Identity'</span></span>
- <span data-ttu-id="594e1-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1163">'Razor'</span></span>
- <span data-ttu-id="594e1-1164">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1165">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1166">'Blazor'</span></span>
- <span data-ttu-id="594e1-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1167">'Identity'</span></span>
- <span data-ttu-id="594e1-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1169">'Razor'</span></span>
- <span data-ttu-id="594e1-1170">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1171">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1172">'Blazor'</span></span>
- <span data-ttu-id="594e1-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1173">'Identity'</span></span>
- <span data-ttu-id="594e1-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1175">'Razor'</span></span>
- <span data-ttu-id="594e1-1176">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1177">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1178">'Blazor'</span></span>
- <span data-ttu-id="594e1-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1179">'Identity'</span></span>
- <span data-ttu-id="594e1-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1181">'Razor'</span></span>
- <span data-ttu-id="594e1-1182">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1183">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1184">'Blazor'</span></span>
- <span data-ttu-id="594e1-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1185">'Identity'</span></span>
- <span data-ttu-id="594e1-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1187">'Razor'</span></span>
- <span data-ttu-id="594e1-1188">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1189">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1190">'Blazor'</span></span>
- <span data-ttu-id="594e1-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1191">'Identity'</span></span>
- <span data-ttu-id="594e1-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1193">'Razor'</span></span>
- <span data-ttu-id="594e1-1194">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1195">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1196">'Blazor'</span></span>
- <span data-ttu-id="594e1-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1197">'Identity'</span></span>
- <span data-ttu-id="594e1-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1199">'Razor'</span></span>
- <span data-ttu-id="594e1-1200">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1201">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1202">'Blazor'</span></span>
- <span data-ttu-id="594e1-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1203">'Identity'</span></span>
- <span data-ttu-id="594e1-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1205">'Razor'</span></span>
- <span data-ttu-id="594e1-1206">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1207">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1208">'Blazor'</span></span>
- <span data-ttu-id="594e1-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1209">'Identity'</span></span>
- <span data-ttu-id="594e1-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1211">'Razor'</span></span>
- <span data-ttu-id="594e1-1212">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1213">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1214">'Blazor'</span></span>
- <span data-ttu-id="594e1-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1215">'Identity'</span></span>
- <span data-ttu-id="594e1-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1217">'Razor'</span></span>
- <span data-ttu-id="594e1-1218">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1219">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1220">'Blazor'</span></span>
- <span data-ttu-id="594e1-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1221">'Identity'</span></span>
- <span data-ttu-id="594e1-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1223">'Razor'</span></span>
- <span data-ttu-id="594e1-1224">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1225">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1226">'Blazor'</span></span>
- <span data-ttu-id="594e1-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1227">'Identity'</span></span>
- <span data-ttu-id="594e1-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1229">'Razor'</span></span>
- <span data-ttu-id="594e1-1230">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1231">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1232">'Blazor'</span></span>
- <span data-ttu-id="594e1-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1233">'Identity'</span></span>
- <span data-ttu-id="594e1-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1235">'Razor'</span></span>
- <span data-ttu-id="594e1-1236">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Tak | | `/my-cool-redirect-rule/1234/5678` | Tak | | `/anotherredirect-rule/1234/5678`  | Tak |</span><span class="sxs-lookup"><span data-stu-id="594e1-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="594e1-1238">Reguła ponownego zapisywania, `^rewrite-rule/(\d+)/(\d+)` ,, dopasowuje się tylko do ścieżek, jeśli zaczynają się od `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="594e1-1239">W poniższej tabeli należy zwrócić uwagę na różnicę.</span><span class="sxs-lookup"><span data-stu-id="594e1-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="594e1-1240">Ścieżka</span><span class="sxs-lookup"><span data-stu-id="594e1-1240">Path</span></span>                              | <span data-ttu-id="594e1-1241">Dopasowanie</span><span class="sxs-lookup"><span data-stu-id="594e1-1241">Match</span></span> |
| ---
<span data-ttu-id="594e1-1242">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1243">'Blazor'</span></span>
- <span data-ttu-id="594e1-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1244">'Identity'</span></span>
- <span data-ttu-id="594e1-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1246">'Razor'</span></span>
- <span data-ttu-id="594e1-1247">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1248">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1249">'Blazor'</span></span>
- <span data-ttu-id="594e1-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1250">'Identity'</span></span>
- <span data-ttu-id="594e1-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1252">'Razor'</span></span>
- <span data-ttu-id="594e1-1253">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1254">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1255">'Blazor'</span></span>
- <span data-ttu-id="594e1-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1256">'Identity'</span></span>
- <span data-ttu-id="594e1-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1258">'Razor'</span></span>
- <span data-ttu-id="594e1-1259">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1260">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1261">'Blazor'</span></span>
- <span data-ttu-id="594e1-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1262">'Identity'</span></span>
- <span data-ttu-id="594e1-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1264">'Razor'</span></span>
- <span data-ttu-id="594e1-1265">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1266">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1267">'Blazor'</span></span>
- <span data-ttu-id="594e1-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1268">'Identity'</span></span>
- <span data-ttu-id="594e1-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1270">'Razor'</span></span>
- <span data-ttu-id="594e1-1271">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1272">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1273">'Blazor'</span></span>
- <span data-ttu-id="594e1-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1274">'Identity'</span></span>
- <span data-ttu-id="594e1-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1276">'Razor'</span></span>
- <span data-ttu-id="594e1-1277">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1278">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1279">'Blazor'</span></span>
- <span data-ttu-id="594e1-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1280">'Identity'</span></span>
- <span data-ttu-id="594e1-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1282">'Razor'</span></span>
- <span data-ttu-id="594e1-1283">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1284">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1285">'Blazor'</span></span>
- <span data-ttu-id="594e1-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1286">'Identity'</span></span>
- <span data-ttu-id="594e1-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1288">'Razor'</span></span>
- <span data-ttu-id="594e1-1289">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1290">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1291">'Blazor'</span></span>
- <span data-ttu-id="594e1-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1292">'Identity'</span></span>
- <span data-ttu-id="594e1-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1294">'Razor'</span></span>
- <span data-ttu-id="594e1-1295">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1296">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1297">'Blazor'</span></span>
- <span data-ttu-id="594e1-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1298">'Identity'</span></span>
- <span data-ttu-id="594e1-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1300">'Razor'</span></span>
- <span data-ttu-id="594e1-1301">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1302">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1303">'Blazor'</span></span>
- <span data-ttu-id="594e1-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1304">'Identity'</span></span>
- <span data-ttu-id="594e1-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1306">'Razor'</span></span>
- <span data-ttu-id="594e1-1307">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1308">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1309">'Blazor'</span></span>
- <span data-ttu-id="594e1-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1310">'Identity'</span></span>
- <span data-ttu-id="594e1-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1312">'Razor'</span></span>
- <span data-ttu-id="594e1-1313">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1314">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1315">'Blazor'</span></span>
- <span data-ttu-id="594e1-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1316">'Identity'</span></span>
- <span data-ttu-id="594e1-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1318">'Razor'</span></span>
- <span data-ttu-id="594e1-1319">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1320">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1321">'Blazor'</span></span>
- <span data-ttu-id="594e1-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1322">'Identity'</span></span>
- <span data-ttu-id="594e1-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1324">'Razor'</span></span>
- <span data-ttu-id="594e1-1325">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Tak | | `/my-cool-rewrite-rule/1234/5678` | Nie | | `/anotherrewrite-rule/1234/5678`  | Nie |</span><span class="sxs-lookup"><span data-stu-id="594e1-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="594e1-1327">Po `^rewrite-rule/` części wyrażenia istnieją dwie grupy przechwytywania `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="594e1-1328">Oznaczenia `\d` *są zgodne z cyfrą (* cyfrą).</span><span class="sxs-lookup"><span data-stu-id="594e1-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="594e1-1329">Znak plus ( `+` ) oznacza *dopasowanie co najmniej jednego znaku poprzedzającego*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="594e1-1330">W związku z tym adres URL musi zawierać numer, po którym następuje ukośnik, po którym następuje kolejny numer.</span><span class="sxs-lookup"><span data-stu-id="594e1-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="594e1-1331">Te grupy przechwytywania są wstrzykiwane do zarejestrowanego adresu URL jako `$1` i `$2` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="594e1-1332">Ciąg zastępczy reguły ponownego zapisu umieszcza przechwycone grupy w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="594e1-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="594e1-1333">Żądana ścieżka `/rewrite-rule/1234/5678` jest ponownie zapisywana, aby uzyskać zasób w `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="594e1-1334">Jeśli ciąg zapytania jest obecny w oryginalnym żądaniu, jest zachowywany, gdy adres URL zostanie ponownie zapisany.</span><span class="sxs-lookup"><span data-stu-id="594e1-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="594e1-1335">Serwer nie może uzyskać dostępu do zasobów.</span><span class="sxs-lookup"><span data-stu-id="594e1-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="594e1-1336">Jeśli zasób istnieje, jest pobierany i zwracany do klienta przy użyciu kodu stanu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="594e1-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="594e1-1337">Ponieważ klient nie jest przekierowywany, adres URL na pasku adresu przeglądarki nie jest zmieniany.</span><span class="sxs-lookup"><span data-stu-id="594e1-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="594e1-1338">Klienci nie mogą wykryć, czy na serwerze wystąpiła operacja ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="594e1-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="594e1-1339">Używaj `skipRemainingRules: true` zawsze, gdy jest to możliwe, ponieważ reguły uzgadniania są wyliczane i zwiększają czas odpowiedzi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="594e1-1340">Dla najszybszej odpowiedzi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="594e1-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="594e1-1341">Zamów reguły ponownego zapisu z najczęściej dopasowanej reguły do najmniej często dopasowanej reguły.</span><span class="sxs-lookup"><span data-stu-id="594e1-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="594e1-1342">Pomiń przetwarzanie pozostałych reguł w przypadku wystąpienia dopasowania i nie jest wymagane żadne dodatkowe przetwarzanie reguły.</span><span class="sxs-lookup"><span data-stu-id="594e1-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="594e1-1343">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="594e1-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="594e1-1344">Zastosuj reguły mod_rewrite Apache za pomocą programu <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="594e1-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="594e1-1345">Upewnij się, że plik reguł został wdrożony razem z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="594e1-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="594e1-1346">Aby uzyskać więcej informacji i przykłady reguł mod_rewrite, zobacz [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="594e1-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="594e1-1347">A służy <xref:System.IO.StreamReader> do odczytywania reguł z pliku reguł *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="594e1-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="594e1-1348">Przykładowa aplikacja przekierowuje żądania z `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="594e1-1349">Kod stanu odpowiedzi to *302 — znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="594e1-1350">Oryginalne żądanie:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="594e1-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="594e1-1352">Oprogramowanie pośredniczące obsługuje następujące zmienne serwera Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="594e1-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="594e1-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="594e1-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="594e1-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="594e1-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="594e1-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="594e1-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="594e1-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="594e1-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="594e1-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="594e1-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="594e1-1358">HTTP_HOST</span></span>
* <span data-ttu-id="594e1-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="594e1-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="594e1-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="594e1-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="594e1-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="594e1-1361">HTTPS</span></span>
* <span data-ttu-id="594e1-1362">If</span><span class="sxs-lookup"><span data-stu-id="594e1-1362">IPV6</span></span>
* <span data-ttu-id="594e1-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="594e1-1363">QUERY_STRING</span></span>
* <span data-ttu-id="594e1-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="594e1-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="594e1-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="594e1-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="594e1-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="594e1-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="594e1-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="594e1-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="594e1-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="594e1-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="594e1-1369">REQUEST_URI</span></span>
* <span data-ttu-id="594e1-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="594e1-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="594e1-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="594e1-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="594e1-1372">SERVER_PORT</span></span>
* <span data-ttu-id="594e1-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="594e1-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="594e1-1374">CZAS</span><span class="sxs-lookup"><span data-stu-id="594e1-1374">TIME</span></span>
* <span data-ttu-id="594e1-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="594e1-1375">TIME_DAY</span></span>
* <span data-ttu-id="594e1-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="594e1-1376">TIME_HOUR</span></span>
* <span data-ttu-id="594e1-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="594e1-1377">TIME_MIN</span></span>
* <span data-ttu-id="594e1-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="594e1-1378">TIME_MON</span></span>
* <span data-ttu-id="594e1-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="594e1-1379">TIME_SEC</span></span>
* <span data-ttu-id="594e1-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="594e1-1380">TIME_WDAY</span></span>
* <span data-ttu-id="594e1-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="594e1-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="594e1-1382">Reguły modułu ponownego zapisywania adresów URL usług IIS</span><span class="sxs-lookup"><span data-stu-id="594e1-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="594e1-1383">Aby użyć tego samego zestawu reguł, który ma zastosowanie do modułu ponowne zapisywanie adresów URL usług IIS, użyj <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="594e1-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="594e1-1384">Upewnij się, że plik reguł został wdrożony razem z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="594e1-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="594e1-1385">Nie należy kierować oprogramowanie pośredniczące do korzystania z pliku *Web. config* aplikacji podczas uruchamiania w usługach IIS systemu Windows Server.</span><span class="sxs-lookup"><span data-stu-id="594e1-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="594e1-1386">W przypadku usług IIS te reguły powinny być przechowywane poza plikiem *Web. config* aplikacji, aby uniknąć konfliktów z modułem ponownego zapisywania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="594e1-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="594e1-1387">Aby uzyskać więcej informacji i przykłady reguł modułu ponownego zapisywania adresów URL usług IIS, zobacz [using URL Rewrite module 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) i [informacje konfiguracyjne modułu ponownego zapisywania adresu URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="594e1-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="594e1-1388">A służy <xref:System.IO.StreamReader> do odczytywania reguł z pliku reguł *IISUrlRewrite. XML* :</span><span class="sxs-lookup"><span data-stu-id="594e1-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="594e1-1389">Przykładowa aplikacja ponownie zapisuje żądania z `/iis-rules-rewrite/(.*)` programu do `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="594e1-1390">Odpowiedź jest wysyłana do klienta z kodem stanu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="594e1-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="594e1-1391">Oryginalne żądanie:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="594e1-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądania i odpowiedzi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="594e1-1393">Jeśli masz aktywny moduł ponownego zapisywania usług IIS z skonfigurowanymi regułami na poziomie serwera, które byłyby wpływać na aplikację na niepożądane sposoby, możesz wyłączyć moduł ponownego zapisywania usług IIS dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="594e1-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="594e1-1394">Aby uzyskać więcej informacji, zobacz [wyłączanie modułów IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="594e1-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="594e1-1395">Nieobsługiwane funkcje</span><span class="sxs-lookup"><span data-stu-id="594e1-1395">Unsupported features</span></span>

<span data-ttu-id="594e1-1396">Oprogramowanie pośredniczące wydane z ASP.NET Core 2. x nie obsługuje następujących funkcji modułu ponownego zapisywania adresów URL usług IIS:</span><span class="sxs-lookup"><span data-stu-id="594e1-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="594e1-1397">Reguły ruchu wychodzącego</span><span class="sxs-lookup"><span data-stu-id="594e1-1397">Outbound Rules</span></span>
* <span data-ttu-id="594e1-1398">Niestandardowe zmienne serwera</span><span class="sxs-lookup"><span data-stu-id="594e1-1398">Custom Server Variables</span></span>
* <span data-ttu-id="594e1-1399">Symbole wieloznaczne</span><span class="sxs-lookup"><span data-stu-id="594e1-1399">Wildcards</span></span>
* <span data-ttu-id="594e1-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="594e1-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="594e1-1401">Obsługiwane Zmienne serwera</span><span class="sxs-lookup"><span data-stu-id="594e1-1401">Supported server variables</span></span>

<span data-ttu-id="594e1-1402">Oprogramowanie pośredniczące obsługuje następujące zmienne serwera modułu ponownego zapisywania adresów URL usług IIS:</span><span class="sxs-lookup"><span data-stu-id="594e1-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="594e1-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="594e1-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="594e1-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="594e1-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="594e1-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="594e1-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="594e1-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="594e1-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="594e1-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="594e1-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="594e1-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="594e1-1408">HTTP_HOST</span></span>
* <span data-ttu-id="594e1-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="594e1-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="594e1-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1410">HTTP_URL</span></span>
* <span data-ttu-id="594e1-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="594e1-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="594e1-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="594e1-1412">HTTPS</span></span>
* <span data-ttu-id="594e1-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="594e1-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="594e1-1414">QUERY_STRING</span></span>
* <span data-ttu-id="594e1-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="594e1-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="594e1-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="594e1-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="594e1-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="594e1-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="594e1-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="594e1-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="594e1-1419">Możesz również uzyskać <xref:Microsoft.Extensions.FileProviders.IFileProvider> za pośrednictwem a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="594e1-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="594e1-1420">Takie podejście może zapewnić większą elastyczność lokalizacji plików reguł ponownego zapisywania.</span><span class="sxs-lookup"><span data-stu-id="594e1-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="594e1-1421">Upewnij się, że pliki reguł ponownego zapisywania są wdrożone na serwerze pod podaną ścieżką.</span><span class="sxs-lookup"><span data-stu-id="594e1-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="594e1-1422">Reguła oparta na metodzie</span><span class="sxs-lookup"><span data-stu-id="594e1-1422">Method-based rule</span></span>

<span data-ttu-id="594e1-1423">Użyj <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> , aby zaimplementować własną logikę reguł w metodzie.</span><span class="sxs-lookup"><span data-stu-id="594e1-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="594e1-1424">`Add`udostępnia <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , który udostępnia do <xref:Microsoft.AspNetCore.Http.HttpContext> użycia w Twojej metodzie.</span><span class="sxs-lookup"><span data-stu-id="594e1-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="594e1-1425">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) określa sposób obsługi dodatkowego przetwarzania potoku.</span><span class="sxs-lookup"><span data-stu-id="594e1-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="594e1-1426">Ustaw wartość na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> pól opisanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="594e1-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="594e1-1427">Akcja</span><span class="sxs-lookup"><span data-stu-id="594e1-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="594e1-1428">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1429">'Blazor'</span></span>
- <span data-ttu-id="594e1-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1430">'Identity'</span></span>
- <span data-ttu-id="594e1-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1432">'Razor'</span></span>
- <span data-ttu-id="594e1-1433">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1434">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1435">'Blazor'</span></span>
- <span data-ttu-id="594e1-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1436">'Identity'</span></span>
- <span data-ttu-id="594e1-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1438">'Razor'</span></span>
- <span data-ttu-id="594e1-1439">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1440">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1441">'Blazor'</span></span>
- <span data-ttu-id="594e1-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1442">'Identity'</span></span>
- <span data-ttu-id="594e1-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1444">'Razor'</span></span>
- <span data-ttu-id="594e1-1445">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1446">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1447">'Blazor'</span></span>
- <span data-ttu-id="594e1-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1448">'Identity'</span></span>
- <span data-ttu-id="594e1-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1450">'Razor'</span></span>
- <span data-ttu-id="594e1-1451">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1452">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1453">'Blazor'</span></span>
- <span data-ttu-id="594e1-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1454">'Identity'</span></span>
- <span data-ttu-id="594e1-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1456">'Razor'</span></span>
- <span data-ttu-id="594e1-1457">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1458">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1459">'Blazor'</span></span>
- <span data-ttu-id="594e1-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1460">'Identity'</span></span>
- <span data-ttu-id="594e1-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1462">'Razor'</span></span>
- <span data-ttu-id="594e1-1463">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1464">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1465">'Blazor'</span></span>
- <span data-ttu-id="594e1-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1466">'Identity'</span></span>
- <span data-ttu-id="594e1-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1468">'Razor'</span></span>
- <span data-ttu-id="594e1-1469">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1470">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1471">'Blazor'</span></span>
- <span data-ttu-id="594e1-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1472">'Identity'</span></span>
- <span data-ttu-id="594e1-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1474">'Razor'</span></span>
- <span data-ttu-id="594e1-1475">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1476">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1477">'Blazor'</span></span>
- <span data-ttu-id="594e1-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1478">'Identity'</span></span>
- <span data-ttu-id="594e1-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1480">'Razor'</span></span>
- <span data-ttu-id="594e1-1481">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1482">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1483">'Blazor'</span></span>
- <span data-ttu-id="594e1-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1484">'Identity'</span></span>
- <span data-ttu-id="594e1-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1486">'Razor'</span></span>
- <span data-ttu-id="594e1-1487">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1488">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1489">'Blazor'</span></span>
- <span data-ttu-id="594e1-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1490">'Identity'</span></span>
- <span data-ttu-id="594e1-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1492">'Razor'</span></span>
- <span data-ttu-id="594e1-1493">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1494">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1495">'Blazor'</span></span>
- <span data-ttu-id="594e1-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1496">'Identity'</span></span>
- <span data-ttu-id="594e1-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1498">'Razor'</span></span>
- <span data-ttu-id="594e1-1499">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1500">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1501">'Blazor'</span></span>
- <span data-ttu-id="594e1-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1502">'Identity'</span></span>
- <span data-ttu-id="594e1-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1504">'Razor'</span></span>
- <span data-ttu-id="594e1-1505">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1506">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1507">'Blazor'</span></span>
- <span data-ttu-id="594e1-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1508">'Identity'</span></span>
- <span data-ttu-id="594e1-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1510">'Razor'</span></span>
- <span data-ttu-id="594e1-1511">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1512">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1513">'Blazor'</span></span>
- <span data-ttu-id="594e1-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1514">'Identity'</span></span>
- <span data-ttu-id="594e1-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1516">'Razor'</span></span>
- <span data-ttu-id="594e1-1517">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1518">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1519">'Blazor'</span></span>
- <span data-ttu-id="594e1-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1520">'Identity'</span></span>
- <span data-ttu-id="594e1-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1522">'Razor'</span></span>
- <span data-ttu-id="594e1-1523">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-1524">------------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1525">'Blazor'</span></span>
- <span data-ttu-id="594e1-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1526">'Identity'</span></span>
- <span data-ttu-id="594e1-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1528">'Razor'</span></span>
- <span data-ttu-id="594e1-1529">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1530">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1531">'Blazor'</span></span>
- <span data-ttu-id="594e1-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1532">'Identity'</span></span>
- <span data-ttu-id="594e1-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1534">'Razor'</span></span>
- <span data-ttu-id="594e1-1535">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1536">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1537">'Blazor'</span></span>
- <span data-ttu-id="594e1-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1538">'Identity'</span></span>
- <span data-ttu-id="594e1-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1540">'Razor'</span></span>
- <span data-ttu-id="594e1-1541">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1542">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1543">'Blazor'</span></span>
- <span data-ttu-id="594e1-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1544">'Identity'</span></span>
- <span data-ttu-id="594e1-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1546">'Razor'</span></span>
- <span data-ttu-id="594e1-1547">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1548">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1549">'Blazor'</span></span>
- <span data-ttu-id="594e1-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1550">'Identity'</span></span>
- <span data-ttu-id="594e1-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1552">'Razor'</span></span>
- <span data-ttu-id="594e1-1553">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1554">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1555">'Blazor'</span></span>
- <span data-ttu-id="594e1-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1556">'Identity'</span></span>
- <span data-ttu-id="594e1-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1558">'Razor'</span></span>
- <span data-ttu-id="594e1-1559">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1560">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1561">'Blazor'</span></span>
- <span data-ttu-id="594e1-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1562">'Identity'</span></span>
- <span data-ttu-id="594e1-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1564">'Razor'</span></span>
- <span data-ttu-id="594e1-1565">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1566">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1567">'Blazor'</span></span>
- <span data-ttu-id="594e1-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1568">'Identity'</span></span>
- <span data-ttu-id="594e1-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1570">'Razor'</span></span>
- <span data-ttu-id="594e1-1571">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1572">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1573">'Blazor'</span></span>
- <span data-ttu-id="594e1-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1574">'Identity'</span></span>
- <span data-ttu-id="594e1-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1576">'Razor'</span></span>
- <span data-ttu-id="594e1-1577">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1578">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1579">'Blazor'</span></span>
- <span data-ttu-id="594e1-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1580">'Identity'</span></span>
- <span data-ttu-id="594e1-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1582">'Razor'</span></span>
- <span data-ttu-id="594e1-1583">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1584">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1585">'Blazor'</span></span>
- <span data-ttu-id="594e1-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1586">'Identity'</span></span>
- <span data-ttu-id="594e1-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1588">'Razor'</span></span>
- <span data-ttu-id="594e1-1589">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1590">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1591">'Blazor'</span></span>
- <span data-ttu-id="594e1-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1592">'Identity'</span></span>
- <span data-ttu-id="594e1-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1594">'Razor'</span></span>
- <span data-ttu-id="594e1-1595">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1596">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1597">'Blazor'</span></span>
- <span data-ttu-id="594e1-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1598">'Identity'</span></span>
- <span data-ttu-id="594e1-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1600">'Razor'</span></span>
- <span data-ttu-id="594e1-1601">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1602">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1603">'Blazor'</span></span>
- <span data-ttu-id="594e1-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1604">'Identity'</span></span>
- <span data-ttu-id="594e1-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1606">'Razor'</span></span>
- <span data-ttu-id="594e1-1607">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1608">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1609">'Blazor'</span></span>
- <span data-ttu-id="594e1-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1610">'Identity'</span></span>
- <span data-ttu-id="594e1-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1612">'Razor'</span></span>
- <span data-ttu-id="594e1-1613">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1614">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1615">'Blazor'</span></span>
- <span data-ttu-id="594e1-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1616">'Identity'</span></span>
- <span data-ttu-id="594e1-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1618">'Razor'</span></span>
- <span data-ttu-id="594e1-1619">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1620">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1621">'Blazor'</span></span>
- <span data-ttu-id="594e1-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1622">'Identity'</span></span>
- <span data-ttu-id="594e1-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1624">'Razor'</span></span>
- <span data-ttu-id="594e1-1625">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1626">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1627">'Blazor'</span></span>
- <span data-ttu-id="594e1-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1628">'Identity'</span></span>
- <span data-ttu-id="594e1-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1630">'Razor'</span></span>
- <span data-ttu-id="594e1-1631">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1632">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1633">'Blazor'</span></span>
- <span data-ttu-id="594e1-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1634">'Identity'</span></span>
- <span data-ttu-id="594e1-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1636">'Razor'</span></span>
- <span data-ttu-id="594e1-1637">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1638">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1639">'Blazor'</span></span>
- <span data-ttu-id="594e1-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1640">'Identity'</span></span>
- <span data-ttu-id="594e1-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1642">'Razor'</span></span>
- <span data-ttu-id="594e1-1643">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1644">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1645">'Blazor'</span></span>
- <span data-ttu-id="594e1-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1646">'Identity'</span></span>
- <span data-ttu-id="594e1-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1648">'Razor'</span></span>
- <span data-ttu-id="594e1-1649">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1650">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1651">'Blazor'</span></span>
- <span data-ttu-id="594e1-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1652">'Identity'</span></span>
- <span data-ttu-id="594e1-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1654">'Razor'</span></span>
- <span data-ttu-id="594e1-1655">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1656">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1657">'Blazor'</span></span>
- <span data-ttu-id="594e1-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1658">'Identity'</span></span>
- <span data-ttu-id="594e1-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1660">'Razor'</span></span>
- <span data-ttu-id="594e1-1661">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1662">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1663">'Blazor'</span></span>
- <span data-ttu-id="594e1-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1664">'Identity'</span></span>
- <span data-ttu-id="594e1-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1666">'Razor'</span></span>
- <span data-ttu-id="594e1-1667">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1668">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1669">'Blazor'</span></span>
- <span data-ttu-id="594e1-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1670">'Identity'</span></span>
- <span data-ttu-id="594e1-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1672">'Razor'</span></span>
- <span data-ttu-id="594e1-1673">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1674">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1675">'Blazor'</span></span>
- <span data-ttu-id="594e1-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1676">'Identity'</span></span>
- <span data-ttu-id="594e1-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1678">'Razor'</span></span>
- <span data-ttu-id="594e1-1679">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1680">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1681">'Blazor'</span></span>
- <span data-ttu-id="594e1-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1682">'Identity'</span></span>
- <span data-ttu-id="594e1-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1684">'Razor'</span></span>
- <span data-ttu-id="594e1-1685">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1686">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1687">'Blazor'</span></span>
- <span data-ttu-id="594e1-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1688">'Identity'</span></span>
- <span data-ttu-id="594e1-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1690">'Razor'</span></span>
- <span data-ttu-id="594e1-1691">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1692">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1693">'Blazor'</span></span>
- <span data-ttu-id="594e1-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1694">'Identity'</span></span>
- <span data-ttu-id="594e1-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1696">'Razor'</span></span>
- <span data-ttu-id="594e1-1697">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1698">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1699">'Blazor'</span></span>
- <span data-ttu-id="594e1-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1700">'Identity'</span></span>
- <span data-ttu-id="594e1-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1702">'Razor'</span></span>
- <span data-ttu-id="594e1-1703">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-1704">-------------------------------- | | `RuleResult.ContinueRules`(ustawienie domyślne) | Kontynuuj stosowanie reguł.</span><span class="sxs-lookup"><span data-stu-id="594e1-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="594e1-1705">| | `RuleResult.EndResponse`             | Zatrzymaj stosowanie reguł i Wyślij odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="594e1-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="594e1-1706">| | `RuleResult.SkipRemainingRules`      | Zatrzymaj stosowanie reguł i Wyślij kontekst do następnego oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="594e1-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="594e1-1707">Przykładowa aplikacja przedstawia metodę, która przekierowuje żądania dla ścieżek kończących się na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="594e1-1708">Jeśli zostanie wysłane żądanie `/file.xml` , żądanie jest przekierowywane do `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="594e1-1709">Kod stanu jest ustawiony na *301 — trwale przeniesiony*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="594e1-1710">Gdy przeglądarka wykonuje nowe żądanie dla */XmlFiles/File.XML*, oprogramowanie pośredniczące plików statycznych zachowuje ten plik na kliencie z folderu *wwwroot/XmlFiles* .</span><span class="sxs-lookup"><span data-stu-id="594e1-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="594e1-1711">W przypadku przekierowania jawnie ustaw kod stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="594e1-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="594e1-1712">W przeciwnym razie zwracany jest kod stanu *200-OK* i przekierowanie nie wystąpi na kliencie.</span><span class="sxs-lookup"><span data-stu-id="594e1-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="594e1-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="594e1-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="594e1-1714">Takie podejście może również ponownie zapisywać żądania.</span><span class="sxs-lookup"><span data-stu-id="594e1-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="594e1-1715">Przykładowa aplikacja pokazuje, jak ponownie napisać ścieżkę do dowolnego żądania pliku tekstowego, aby zapewnić obsługę pliku tekstowego *pliku. txt* z folderu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="594e1-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="594e1-1716">Oprogramowanie pośredniczące plików statycznych obsługuje plik na podstawie zaktualizowanej ścieżki żądania:</span><span class="sxs-lookup"><span data-stu-id="594e1-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="594e1-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="594e1-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="594e1-1718">Reguła oparta na IRule</span><span class="sxs-lookup"><span data-stu-id="594e1-1718">IRule-based rule</span></span>

<span data-ttu-id="594e1-1719">Użyj, <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> Aby użyć logiki reguły w klasie, która implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> interfejs.</span><span class="sxs-lookup"><span data-stu-id="594e1-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="594e1-1720">`IRule`zapewnia większą elastyczność w porównaniu z użyciem metody opartej na metodzie.</span><span class="sxs-lookup"><span data-stu-id="594e1-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="594e1-1721">Klasa implementacji może zawierać konstruktora, który umożliwia przekazywanie parametrów dla <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metody.</span><span class="sxs-lookup"><span data-stu-id="594e1-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="594e1-1722">Wartości parametrów w aplikacji przykładowej dla `extension` i `newPath` są sprawdzane pod kątem spełnienia kilku warunków.</span><span class="sxs-lookup"><span data-stu-id="594e1-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="594e1-1723">`extension`Musi zawierać wartość i musi mieć wartość *. png*, *. jpg*lub *. gif*.</span><span class="sxs-lookup"><span data-stu-id="594e1-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="594e1-1724">Jeśli `newPath` jest nieprawidłowa, <xref:System.ArgumentException> zostanie zgłoszony.</span><span class="sxs-lookup"><span data-stu-id="594e1-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="594e1-1725">Jeśli zostanie wysłane żądanie dotyczące pliku *Image. png*, żądanie jest przekierowywane do `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="594e1-1726">Jeśli zostanie wysłane żądanie do *obrazu. jpg*, żądanie jest przekierowywane do `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="594e1-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="594e1-1727">Kod stanu jest ustawiony na *301 — trwale przeniesiony*, a `context.Result` ustawienie jest ustawione na zatrzymanie przetwarzania reguł i wysłanie odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="594e1-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="594e1-1728">Oryginalne żądanie:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="594e1-1728">Original Request: `/image.png`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi dla pliku Image. png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="594e1-1730">Oryginalne żądanie:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="594e1-1730">Original Request: `/image.jpg`</span></span>

![Okno przeglądarki z Narzędzia deweloperskie śledzenia żądań i odpowiedzi dla obrazu. jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="594e1-1732">Przykłady wyrażeń regularnych</span><span class="sxs-lookup"><span data-stu-id="594e1-1732">Regex examples</span></span>

| <span data-ttu-id="594e1-1733">Cel</span><span class="sxs-lookup"><span data-stu-id="594e1-1733">Goal</span></span> | <span data-ttu-id="594e1-1734">Ciąg wyrażenia regularnego &</span><span class="sxs-lookup"><span data-stu-id="594e1-1734">Regex String &</span></span><br><span data-ttu-id="594e1-1735">Przykład dopasowania</span><span class="sxs-lookup"><span data-stu-id="594e1-1735">Match Example</span></span> | <span data-ttu-id="594e1-1736">& ciągu zamiennego</span><span class="sxs-lookup"><span data-stu-id="594e1-1736">Replacement String &</span></span><br><span data-ttu-id="594e1-1737">Przykład danych wyjściowych</span><span class="sxs-lookup"><span data-stu-id="594e1-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="594e1-1738">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1739">'Blazor'</span></span>
- <span data-ttu-id="594e1-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1740">'Identity'</span></span>
- <span data-ttu-id="594e1-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1742">'Razor'</span></span>
- <span data-ttu-id="594e1-1743">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1744">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1745">'Blazor'</span></span>
- <span data-ttu-id="594e1-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1746">'Identity'</span></span>
- <span data-ttu-id="594e1-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1748">'Razor'</span></span>
- <span data-ttu-id="594e1-1749">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1750">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1751">'Blazor'</span></span>
- <span data-ttu-id="594e1-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1752">'Identity'</span></span>
- <span data-ttu-id="594e1-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1754">'Razor'</span></span>
- <span data-ttu-id="594e1-1755">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1756">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1757">'Blazor'</span></span>
- <span data-ttu-id="594e1-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1758">'Identity'</span></span>
- <span data-ttu-id="594e1-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1760">'Razor'</span></span>
- <span data-ttu-id="594e1-1761">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1762">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1763">'Blazor'</span></span>
- <span data-ttu-id="594e1-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1764">'Identity'</span></span>
- <span data-ttu-id="594e1-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1766">'Razor'</span></span>
- <span data-ttu-id="594e1-1767">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1768">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1769">'Blazor'</span></span>
- <span data-ttu-id="594e1-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1770">'Identity'</span></span>
- <span data-ttu-id="594e1-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1772">'Razor'</span></span>
- <span data-ttu-id="594e1-1773">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1774">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1775">'Blazor'</span></span>
- <span data-ttu-id="594e1-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1776">'Identity'</span></span>
- <span data-ttu-id="594e1-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1778">'Razor'</span></span>
- <span data-ttu-id="594e1-1779">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1780">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1781">'Blazor'</span></span>
- <span data-ttu-id="594e1-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1782">'Identity'</span></span>
- <span data-ttu-id="594e1-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1784">'Razor'</span></span>
- <span data-ttu-id="594e1-1785">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1786">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1787">'Blazor'</span></span>
- <span data-ttu-id="594e1-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1788">'Identity'</span></span>
- <span data-ttu-id="594e1-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1790">'Razor'</span></span>
- <span data-ttu-id="594e1-1791">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1792">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1793">'Blazor'</span></span>
- <span data-ttu-id="594e1-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1794">'Identity'</span></span>
- <span data-ttu-id="594e1-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1796">'Razor'</span></span>
- <span data-ttu-id="594e1-1797">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1798">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1799">'Blazor'</span></span>
- <span data-ttu-id="594e1-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1800">'Identity'</span></span>
- <span data-ttu-id="594e1-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1802">'Razor'</span></span>
- <span data-ttu-id="594e1-1803">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1804">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1805">'Blazor'</span></span>
- <span data-ttu-id="594e1-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1806">'Identity'</span></span>
- <span data-ttu-id="594e1-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1808">'Razor'</span></span>
- <span data-ttu-id="594e1-1809">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1810">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1811">'Blazor'</span></span>
- <span data-ttu-id="594e1-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1812">'Identity'</span></span>
- <span data-ttu-id="594e1-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1814">'Razor'</span></span>
- <span data-ttu-id="594e1-1815">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-1816">---------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1817">'Blazor'</span></span>
- <span data-ttu-id="594e1-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1818">'Identity'</span></span>
- <span data-ttu-id="594e1-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1820">'Razor'</span></span>
- <span data-ttu-id="594e1-1821">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1822">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1823">'Blazor'</span></span>
- <span data-ttu-id="594e1-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1824">'Identity'</span></span>
- <span data-ttu-id="594e1-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1826">'Razor'</span></span>
- <span data-ttu-id="594e1-1827">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1828">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1829">'Blazor'</span></span>
- <span data-ttu-id="594e1-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1830">'Identity'</span></span>
- <span data-ttu-id="594e1-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1832">'Razor'</span></span>
- <span data-ttu-id="594e1-1833">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1834">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1835">'Blazor'</span></span>
- <span data-ttu-id="594e1-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1836">'Identity'</span></span>
- <span data-ttu-id="594e1-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1838">'Razor'</span></span>
- <span data-ttu-id="594e1-1839">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1840">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1841">'Blazor'</span></span>
- <span data-ttu-id="594e1-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1842">'Identity'</span></span>
- <span data-ttu-id="594e1-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1844">'Razor'</span></span>
- <span data-ttu-id="594e1-1845">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1846">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1847">'Blazor'</span></span>
- <span data-ttu-id="594e1-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1848">'Identity'</span></span>
- <span data-ttu-id="594e1-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1850">'Razor'</span></span>
- <span data-ttu-id="594e1-1851">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1852">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1853">'Blazor'</span></span>
- <span data-ttu-id="594e1-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1854">'Identity'</span></span>
- <span data-ttu-id="594e1-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1856">'Razor'</span></span>
- <span data-ttu-id="594e1-1857">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1858">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1859">'Blazor'</span></span>
- <span data-ttu-id="594e1-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1860">'Identity'</span></span>
- <span data-ttu-id="594e1-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1862">'Razor'</span></span>
- <span data-ttu-id="594e1-1863">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1864">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1865">'Blazor'</span></span>
- <span data-ttu-id="594e1-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1866">'Identity'</span></span>
- <span data-ttu-id="594e1-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1868">'Razor'</span></span>
- <span data-ttu-id="594e1-1869">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1870">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1871">'Blazor'</span></span>
- <span data-ttu-id="594e1-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1872">'Identity'</span></span>
- <span data-ttu-id="594e1-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1874">'Razor'</span></span>
- <span data-ttu-id="594e1-1875">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1876">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1877">'Blazor'</span></span>
- <span data-ttu-id="594e1-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1878">'Identity'</span></span>
- <span data-ttu-id="594e1-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1880">'Razor'</span></span>
- <span data-ttu-id="594e1-1881">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1882">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1883">'Blazor'</span></span>
- <span data-ttu-id="594e1-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1884">'Identity'</span></span>
- <span data-ttu-id="594e1-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1886">'Razor'</span></span>
- <span data-ttu-id="594e1-1887">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1888">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1889">'Blazor'</span></span>
- <span data-ttu-id="594e1-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1890">'Identity'</span></span>
- <span data-ttu-id="594e1-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1892">'Razor'</span></span>
- <span data-ttu-id="594e1-1893">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1894">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1895">'Blazor'</span></span>
- <span data-ttu-id="594e1-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1896">'Identity'</span></span>
- <span data-ttu-id="594e1-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1898">'Razor'</span></span>
- <span data-ttu-id="594e1-1899">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1900">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1901">'Blazor'</span></span>
- <span data-ttu-id="594e1-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1902">'Identity'</span></span>
- <span data-ttu-id="594e1-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1904">'Razor'</span></span>
- <span data-ttu-id="594e1-1905">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1906">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1907">'Blazor'</span></span>
- <span data-ttu-id="594e1-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1908">'Identity'</span></span>
- <span data-ttu-id="594e1-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1910">'Razor'</span></span>
- <span data-ttu-id="594e1-1911">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="594e1-1912">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="594e1-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="594e1-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1913">'Blazor'</span></span>
- <span data-ttu-id="594e1-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="594e1-1914">'Identity'</span></span>
- <span data-ttu-id="594e1-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="594e1-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="594e1-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="594e1-1916">'Razor'</span></span>
- <span data-ttu-id="594e1-1917">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="594e1-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="594e1-1918">------------------- | | Zapisz ścieżkę do ciągu QueryString |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="594e1-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="594e1-1919">`/path?var1=abc&var2=123`| | Ukośnik na końcu paska |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="594e1-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="594e1-1920">`/path`| | Wymuś końcowy ukośnika |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="594e1-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="594e1-1921">`/path/`| | Unikaj ponownego zapisywania konkretnych żądań | `^(.*)(?<!\.axd)$`lub`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="594e1-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="594e1-1922">Opcję`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="594e1-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="594e1-1923">Znaleziono`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="594e1-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="594e1-1924">`/resource.axd`| | Zmień rozmieszczenie segmentów adresu URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="594e1-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="594e1-1925">`path/3/2/1`| | Zastępowanie segmentu adresu URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="594e1-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="594e1-1926">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="594e1-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="594e1-1927">Wyrażenia regularne w programie .NET</span><span class="sxs-lookup"><span data-stu-id="594e1-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="594e1-1928">Język wyrażeń regularnych — Krótki przewodnik</span><span class="sxs-lookup"><span data-stu-id="594e1-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="594e1-1929">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="594e1-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="594e1-1930">Używanie modułu ponownego zapisywania adresu URL 2,0 (dla usług IIS)</span><span class="sxs-lookup"><span data-stu-id="594e1-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="594e1-1931">Odwołanie do konfiguracji modułu ponownego zapisywania adresu URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1931">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="594e1-1932">Forum ponownego zapisywania adresów URL usług IIS</span><span class="sxs-lookup"><span data-stu-id="594e1-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="594e1-1933">Zachowaj prostą strukturę adresu URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="594e1-1934">10 — porady i wskazówki dotyczące ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="594e1-1934">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="594e1-1935">Na ukośnik lub nie do ukośnika</span><span class="sxs-lookup"><span data-stu-id="594e1-1935">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)

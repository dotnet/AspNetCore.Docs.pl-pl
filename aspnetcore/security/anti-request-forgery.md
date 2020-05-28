---
<span data-ttu-id="a462d-101">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-102">'Blazor'</span></span>
- <span data-ttu-id="a462d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-103">'Identity'</span></span>
- <span data-ttu-id="a462d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-105">'Razor'</span></span>
- <span data-ttu-id="a462d-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-106">'SignalR' uid:</span></span> 

---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="a462d-107">Uniemożliwiaj ataki między witrynami (XSRF/CSRF) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a462d-107">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="a462d-108">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a462d-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a462d-109">Sfałszowanie żądań między lokacjami (znane również jako XSRF lub CSRF) jest atakiem na aplikacje hostowane w sieci Web, dzięki czemu złośliwa aplikacja internetowa może mieć wpływ na interakcję między przeglądarką klienta a aplikacją sieci Web, która ufa tej przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="a462d-109">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="a462d-110">Te ataki są możliwe, ponieważ przeglądarki sieci Web wysyłają automatyczne typy tokenów uwierzytelniania przy użyciu każdego żądania do witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a462d-110">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="a462d-111">Ta forma wykorzystania jest również znana jako *atak dwukrotnego kliknięcia* lub *sesji* , ponieważ atakujący wykorzystuje wcześniej uwierzytelnioną sesję użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a462d-111">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="a462d-112">Przykład ataku CSRF:</span><span class="sxs-lookup"><span data-stu-id="a462d-112">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="a462d-113">Użytkownik loguje się `www.good-banking-site.com` przy użyciu uwierzytelniania formularzy.</span><span class="sxs-lookup"><span data-stu-id="a462d-113">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="a462d-114">Serwer uwierzytelnia użytkownika i wystawia odpowiedź obejmującą plik cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a462d-114">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="a462d-115">Lokacja jest narażona na ataki, ponieważ ufa każde żądanie odbierane z prawidłowym plikiem cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a462d-115">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="a462d-116">Użytkownik odwiedza złośliwą lokację `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="a462d-116">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="a462d-117">Złośliwa witryna programu `www.bad-crook-site.com` zawiera formularz HTML podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="a462d-117">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="a462d-118">Zwróć uwagę na to, że formularz `action` wpisuje w nieszkodliwej witrynie.</span><span class="sxs-lookup"><span data-stu-id="a462d-118">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="a462d-119">To jest część "wiele witryn" z CSRF.</span><span class="sxs-lookup"><span data-stu-id="a462d-119">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="a462d-120">Użytkownik wybiera przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="a462d-120">The user selects the submit button.</span></span> <span data-ttu-id="a462d-121">Przeglądarka wysyła żądanie i automatycznie uwzględnia plik cookie uwierzytelniania dla żądanej domeny `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="a462d-121">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="a462d-122">Żądanie jest uruchamiane na `www.good-banking-site.com` serwerze z kontekstem uwierzytelniania użytkownika i może wykonywać dowolną akcję, którą może wykonać uwierzytelniony użytkownik.</span><span class="sxs-lookup"><span data-stu-id="a462d-122">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="a462d-123">Oprócz scenariusza, w którym użytkownik wybiera przycisk, aby przesłać formularz, złośliwa witryna może:</span><span class="sxs-lookup"><span data-stu-id="a462d-123">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="a462d-124">Uruchom skrypt, który automatycznie przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="a462d-124">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="a462d-125">Wyślij formularz przesyłania w postaci żądania AJAX.</span><span class="sxs-lookup"><span data-stu-id="a462d-125">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="a462d-126">Ukryj formularz przy użyciu CSS.</span><span class="sxs-lookup"><span data-stu-id="a462d-126">Hide the form using CSS.</span></span>

<span data-ttu-id="a462d-127">Te alternatywne scenariusze nie wymagają żadnych działań ani danych wejściowych od użytkownika innego niż początkowo odwiedzają złośliwe witryny.</span><span class="sxs-lookup"><span data-stu-id="a462d-127">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="a462d-128">Korzystanie z protokołu HTTPS nie uniemożliwia ataku CSRF.</span><span class="sxs-lookup"><span data-stu-id="a462d-128">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="a462d-129">Złośliwa lokacja może wysłać `https://www.good-banking-site.com/` żądanie równie łatwo, jak może wysłać niezabezpieczone żądanie.</span><span class="sxs-lookup"><span data-stu-id="a462d-129">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="a462d-130">Niektóre ataki są ukierunkowanymi punktami końcowymi, które odpowiadają na żądania GET, w takim przypadku można użyć znacznika obrazu do wykonania akcji.</span><span class="sxs-lookup"><span data-stu-id="a462d-130">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="a462d-131">Ta forma ataku jest wspólna w witrynach forum, które zezwalają na obrazy, ale blokują kod JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a462d-131">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="a462d-132">Aplikacje, które zmieniają stan w żądaniach GET, gdzie zmienne lub zasoby są zmieniane, są narażone na złośliwe ataki.</span><span class="sxs-lookup"><span data-stu-id="a462d-132">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="a462d-133">**Żądania GET, które zmieniają stan są niezabezpieczone. Najlepszym rozwiązaniem jest nigdy nie zmieniaj stanu żądania GET.**</span><span class="sxs-lookup"><span data-stu-id="a462d-133">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="a462d-134">Możliwe jest ataki CSRF na aplikacje sieci Web, które używają plików cookie do uwierzytelniania, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="a462d-134">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="a462d-135">Przeglądarki przechowują pliki cookie wystawione przez aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="a462d-135">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="a462d-136">Przechowywane pliki cookie obejmują pliki cookie sesji dla użytkowników uwierzytelnionych.</span><span class="sxs-lookup"><span data-stu-id="a462d-136">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="a462d-137">Przeglądarki wysyłają do aplikacji sieci Web wszystkie pliki cookie skojarzone z domeną, niezależnie od tego, w jaki sposób żądanie do aplikacji zostało wygenerowane w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="a462d-137">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="a462d-138">Jednak ataki CSRF nie ograniczają się do korzystania z plików cookie.</span><span class="sxs-lookup"><span data-stu-id="a462d-138">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="a462d-139">Na przykład uwierzytelnianie podstawowe i szyfrowane jest również zagrożone.</span><span class="sxs-lookup"><span data-stu-id="a462d-139">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="a462d-140">Gdy użytkownik zaloguje się przy użyciu uwierzytelniania podstawowego lub szyfrowanego, przeglądarka automatycznie wyśle poświadczenia do momentu &dagger; zakończenia sesji.</span><span class="sxs-lookup"><span data-stu-id="a462d-140">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="a462d-141">&dagger;W tym kontekście *sesja* dotyczy sesji po stronie klienta, podczas której użytkownik jest uwierzytelniany.</span><span class="sxs-lookup"><span data-stu-id="a462d-141">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="a462d-142">Nie jest on związany z sesjami po stronie serwera lub [ASP.NET Core pośredniczących sesji](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="a462d-142">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="a462d-143">Użytkownicy mogą chronić przed lukami w CSRF, podejmując środki ostrożności:</span><span class="sxs-lookup"><span data-stu-id="a462d-143">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="a462d-144">Wyloguj się z aplikacji sieci Web po zakończeniu korzystania z nich.</span><span class="sxs-lookup"><span data-stu-id="a462d-144">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="a462d-145">Okresowe czyszczenie plików cookie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a462d-145">Clear browser cookies periodically.</span></span>

<span data-ttu-id="a462d-146">Jednak luki w zabezpieczeniach CSRF są zasadniczo problemem z aplikacją sieci Web, a nie użytkownikiem końcowym.</span><span class="sxs-lookup"><span data-stu-id="a462d-146">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="a462d-147">Podstawowe informacje uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a462d-147">Authentication fundamentals</span></span>

<span data-ttu-id="a462d-148">Uwierzytelnianie na podstawie plików cookie to popularna forma uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a462d-148">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="a462d-149">Systemy uwierzytelniania oparte na tokenach zwiększają popularność, szczególnie w przypadku aplikacji jednostronicowych (aplikacji jednostronicowych).</span><span class="sxs-lookup"><span data-stu-id="a462d-149">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="a462d-150">Uwierzytelnianie na podstawie plików cookie</span><span class="sxs-lookup"><span data-stu-id="a462d-150">Cookie-based authentication</span></span>

<span data-ttu-id="a462d-151">Gdy użytkownik jest uwierzytelniany przy użyciu nazwy użytkownika i hasła, zostanie wystawiony token zawierający bilet uwierzytelniania, który może być używany do uwierzytelniania i autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a462d-151">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="a462d-152">Token jest przechowywany jako plik cookie, który jest dołączany do każdego żądania przez klienta.</span><span class="sxs-lookup"><span data-stu-id="a462d-152">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="a462d-153">Generowanie i sprawdzanie poprawności tego pliku cookie jest wykonywane przez oprogramowanie pośredniczące uwierzytelniania plików cookie.</span><span class="sxs-lookup"><span data-stu-id="a462d-153">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="a462d-154">[Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) deserializacji podmiotu zabezpieczeń użytkownika do zaszyfrowanego pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="a462d-154">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="a462d-155">W kolejnych żądaniach oprogramowanie pośredniczące weryfikuje plik cookie, ponownie tworzy podmiot zabezpieczeń i przypisuje podmiotowi zabezpieczeń do właściwości [użytkownika](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="a462d-155">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="a462d-156">Uwierzytelnianie oparte na tokenach</span><span class="sxs-lookup"><span data-stu-id="a462d-156">Token-based authentication</span></span>

<span data-ttu-id="a462d-157">Po uwierzytelnieniu użytkownika są wystawiane tokeny (nie token antysfałszowany).</span><span class="sxs-lookup"><span data-stu-id="a462d-157">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="a462d-158">Token zawiera informacje o użytkowniku w formie [oświadczeń](/dotnet/framework/security/claims-based-identity-model) lub Token odwołania, który wskazuje, że stan użytkownika w aplikacji jest obsługiwany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="a462d-158">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="a462d-159">Gdy użytkownik próbuje uzyskać dostęp do zasobu wymagającego uwierzytelnienia, token jest wysyłany do aplikacji z dodatkowym nagłówkiem autoryzacji w postaci tokenu okaziciela.</span><span class="sxs-lookup"><span data-stu-id="a462d-159">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="a462d-160">Powoduje to bezstanową aplikację.</span><span class="sxs-lookup"><span data-stu-id="a462d-160">This makes the app stateless.</span></span> <span data-ttu-id="a462d-161">W każdym kolejnym żądaniu token jest przesyłany do żądania weryfikacji po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="a462d-161">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="a462d-162">Ten token nie jest *szyfrowany*; jest ona *zaszyfrowana*.</span><span class="sxs-lookup"><span data-stu-id="a462d-162">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="a462d-163">Na serwerze token jest zdekodowany w celu uzyskania dostępu do informacji.</span><span class="sxs-lookup"><span data-stu-id="a462d-163">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="a462d-164">Aby wysłać token w kolejnych żądaniach, Zapisz token w lokalnym magazynie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a462d-164">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="a462d-165">Nie należy obawiać się o luki w zabezpieczeniach CSRF, jeśli token jest przechowywany w lokalnym magazynie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a462d-165">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="a462d-166">CSRF jest problemem, gdy token jest przechowywany w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="a462d-166">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="a462d-167">Aby uzyskać więcej informacji, zobacz przykładowy kod rozchód spa w usłudze GitHub [dodaje dwa pliki cookie](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="a462d-167">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="a462d-168">Wiele aplikacji hostowanych w jednej domenie</span><span class="sxs-lookup"><span data-stu-id="a462d-168">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="a462d-169">Udostępnione środowiska hostingu są podatne na przejmowanie sesji, CSRF logowania i inne ataki.</span><span class="sxs-lookup"><span data-stu-id="a462d-169">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="a462d-170">Chociaż `example1.contoso.net` i `example2.contoso.net` są różnymi hostami, istnieje niejawna relacja zaufania między hostami w `*.contoso.net` domenie.</span><span class="sxs-lookup"><span data-stu-id="a462d-170">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="a462d-171">Niejawne relacje zaufania umożliwiają potencjalnie niezaufanym hostom wpływanie na wszystkie inne pliki cookie (zasady tego samego źródła, które zarządza żądaniami AJAX, nie zawsze mają zastosowanie do plików cookie HTTP).</span><span class="sxs-lookup"><span data-stu-id="a462d-171">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="a462d-172">Ataki wykorzystujące zaufane pliki cookie między aplikacjami hostowanymi w tej samej domenie można zablokować, nie udostępniając domen.</span><span class="sxs-lookup"><span data-stu-id="a462d-172">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="a462d-173">Gdy każda aplikacja jest hostowana we własnej domenie, nie istnieje niejawna relacja zaufania plików cookie do wykorzystania.</span><span class="sxs-lookup"><span data-stu-id="a462d-173">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="a462d-174">Konfiguracja antysfałszowana ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a462d-174">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="a462d-175">ASP.NET Core implementuje ochronę przed fałszowaniem za pomocą [ASP.NET Core ochrony danych](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="a462d-175">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="a462d-176">Stos ochrony danych musi być skonfigurowany do pracy w farmie serwerów.</span><span class="sxs-lookup"><span data-stu-id="a462d-176">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="a462d-177">Aby uzyskać więcej informacji, zobacz [Konfigurowanie ochrony danych](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="a462d-177">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a462d-178">Oprogramowanie pośredniczące przed fałszowaniem jest dodawane do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) , gdy jeden z następujących interfejsów API jest wywoływany w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a462d-178">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a462d-179">Oprogramowanie pośredniczące przed fałszowaniem jest dodawane do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) , gdy <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> jest wywoływana w`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="a462d-179">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="a462d-180">W ASP.NET Core 2,0 lub nowszej [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wprowadza do elementów formularza HTML tokeny zabezpieczające przed fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="a462d-180">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="a462d-181">Następujące znaczniki w Razor pliku automatycznie generują tokeny zabezpieczające przed fałszerstwem:</span><span class="sxs-lookup"><span data-stu-id="a462d-181">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="a462d-182">Podobnie [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) domyślnie generuje tokeny zabezpieczające przed fałszerstwem, jeśli nie można pobrać metody formularza.</span><span class="sxs-lookup"><span data-stu-id="a462d-182">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="a462d-183">Automatyczne generowanie tokenów antysfałszowanych dla elementów formularza HTML występuje, gdy `<form>` tag zawiera `method="post"` atrybut i jeden z następujących warunków jest spełniony:</span><span class="sxs-lookup"><span data-stu-id="a462d-183">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="a462d-184">Atrybut Action jest pusty ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="a462d-184">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="a462d-185">Nie podano atrybutu Action ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="a462d-185">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="a462d-186">Automatyczne generowanie tokenów antysfałszowanych dla elementów formularza HTML może być wyłączone:</span><span class="sxs-lookup"><span data-stu-id="a462d-186">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="a462d-187">Jawnie wyłącz tokeny zabezpieczające przed fałszowaniem z `asp-antiforgery` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="a462d-187">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="a462d-188">Element form jest wyłączany z pomocników tagów przy użyciu [symbolu Autowypełniania tagów!](xref:mvc/views/tag-helpers/intro#opt-out)</span><span class="sxs-lookup"><span data-stu-id="a462d-188">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="a462d-189">Usuń `FormTagHelper` z widoku.</span><span class="sxs-lookup"><span data-stu-id="a462d-189">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="a462d-190">`FormTagHelper`Można usunąć z widoku, dodając do widoku następującą dyrektywę Razor :</span><span class="sxs-lookup"><span data-stu-id="a462d-190">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="a462d-191">[ Razor Strony](xref:razor-pages/index) są automatycznie chronione przed XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="a462d-191">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="a462d-192">Aby uzyskać więcej informacji, zobacz [XSRF/CSRF i Razor Pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="a462d-192">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="a462d-193">Najbardziej typowym podejściem do obrony przed atakami CSRF jest użycie *wzorca tokenu synchronizatora* (STP).</span><span class="sxs-lookup"><span data-stu-id="a462d-193">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="a462d-194">Wartość STP jest używana, gdy użytkownik żąda strony z danymi formularza:</span><span class="sxs-lookup"><span data-stu-id="a462d-194">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="a462d-195">Serwer wysyła do klienta token skojarzony z tożsamością bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a462d-195">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="a462d-196">Klient wysyła do serwera token z powrotem w celu weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="a462d-196">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="a462d-197">Jeśli serwer odbiera token, który nie jest zgodny z tożsamością uwierzytelnionego użytkownika, żądanie zostanie odrzucone.</span><span class="sxs-lookup"><span data-stu-id="a462d-197">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="a462d-198">Token jest unikatowy i nieprzewidywalny.</span><span class="sxs-lookup"><span data-stu-id="a462d-198">The token is unique and unpredictable.</span></span> <span data-ttu-id="a462d-199">Token może również służyć do zapewnienia prawidłowej sekwencji serii żądań (na przykład w celu zapewnienia sekwencji żądań: Strona 1 > stronie 2 > stronie 3).</span><span class="sxs-lookup"><span data-stu-id="a462d-199">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="a462d-200">Wszystkie formularze w ASP.NET Core MVC i Razor Szablony stron generują tokeny zabezpieczające przed fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="a462d-200">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="a462d-201">Poniższa para przykładów widoku generuje tokeny zabezpieczające przed fałszerstwem:</span><span class="sxs-lookup"><span data-stu-id="a462d-201">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="a462d-202">Jawnie Dodaj token antysfałszowany do `<form>` elementu bez korzystania z pomocników tagów, korzystając z pomocy języka HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="a462d-202">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="a462d-203">W każdym z powyższych przypadków ASP.NET Core dodaje ukryte pole formularza podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="a462d-203">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="a462d-204">ASP.NET Core zawiera trzy [filtry](xref:mvc/controllers/filters) do pracy z tokenami antyfałszowanymi:</span><span class="sxs-lookup"><span data-stu-id="a462d-204">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="a462d-205">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="a462d-205">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="a462d-206">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="a462d-206">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="a462d-207">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="a462d-207">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="a462d-208">Opcje samopodrabiania</span><span class="sxs-lookup"><span data-stu-id="a462d-208">Antiforgery options</span></span>

<span data-ttu-id="a462d-209">Dostosuj [Opcje antysfałszowane](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a462d-209">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="a462d-210">&dagger;Ustaw właściwości przeciwpodrabiane `Cookie` przy użyciu właściwości klasy [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="a462d-210">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="a462d-211">Opcja</span><span class="sxs-lookup"><span data-stu-id="a462d-211">Option</span></span> | <span data-ttu-id="a462d-212">Opis</span><span class="sxs-lookup"><span data-stu-id="a462d-212">Description</span></span> |
| ---
<span data-ttu-id="a462d-213">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-213">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-214">'Blazor'</span></span>
- <span data-ttu-id="a462d-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-215">'Identity'</span></span>
- <span data-ttu-id="a462d-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-217">'Razor'</span></span>
- <span data-ttu-id="a462d-218">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-218">'SignalR' uid:</span></span> 

<span data-ttu-id="a462d-219">--- | ---title: Author: Description: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-219">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-220">'Blazor'</span></span>
- <span data-ttu-id="a462d-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-221">'Identity'</span></span>
- <span data-ttu-id="a462d-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-223">'Razor'</span></span>
- <span data-ttu-id="a462d-224">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a462d-225">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-225">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-226">'Blazor'</span></span>
- <span data-ttu-id="a462d-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-227">'Identity'</span></span>
- <span data-ttu-id="a462d-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-229">'Razor'</span></span>
- <span data-ttu-id="a462d-230">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a462d-231">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-231">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-232">'Blazor'</span></span>
- <span data-ttu-id="a462d-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-233">'Identity'</span></span>
- <span data-ttu-id="a462d-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-235">'Razor'</span></span>
- <span data-ttu-id="a462d-236">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-236">'SignalR' uid:</span></span> 

<span data-ttu-id="a462d-237">------ | | [Plik cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Określa ustawienia używane do tworzenia plików cookie z fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="a462d-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="a462d-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nazwa ukrytego pola formularza używanego przez system antysfałszowany do renderowania tokenów antysfałszowanych w widokach.</span><span class="sxs-lookup"><span data-stu-id="a462d-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="a462d-239">| | [Nagłówekname](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nazwa nagłówka używanego przez system antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="a462d-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="a462d-240">Jeśli `null` System uwzględnia tylko dane formularza.</span><span class="sxs-lookup"><span data-stu-id="a462d-240">If `null`, the system considers only form data.</span></span> <span data-ttu-id="a462d-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Określa, czy należy pominąć generowanie `X-Frame-Options` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="a462d-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="a462d-242">Domyślnie nagłówek jest generowany z wartością "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="a462d-242">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="a462d-243">Wartość domyślna to `false` .</span><span class="sxs-lookup"><span data-stu-id="a462d-243">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="a462d-244">Opcja</span><span class="sxs-lookup"><span data-stu-id="a462d-244">Option</span></span> | <span data-ttu-id="a462d-245">Opis</span><span class="sxs-lookup"><span data-stu-id="a462d-245">Description</span></span> |
| ---
<span data-ttu-id="a462d-246">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-246">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-247">'Blazor'</span></span>
- <span data-ttu-id="a462d-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-248">'Identity'</span></span>
- <span data-ttu-id="a462d-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-250">'Razor'</span></span>
- <span data-ttu-id="a462d-251">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-251">'SignalR' uid:</span></span> 

<span data-ttu-id="a462d-252">--- | ---title: Author: Description: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-252">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-253">'Blazor'</span></span>
- <span data-ttu-id="a462d-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-254">'Identity'</span></span>
- <span data-ttu-id="a462d-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-256">'Razor'</span></span>
- <span data-ttu-id="a462d-257">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a462d-258">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-258">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-259">'Blazor'</span></span>
- <span data-ttu-id="a462d-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-260">'Identity'</span></span>
- <span data-ttu-id="a462d-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-262">'Razor'</span></span>
- <span data-ttu-id="a462d-263">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a462d-264">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a462d-264">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a462d-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a462d-265">'Blazor'</span></span>
- <span data-ttu-id="a462d-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a462d-266">'Identity'</span></span>
- <span data-ttu-id="a462d-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a462d-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="a462d-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a462d-268">'Razor'</span></span>
- <span data-ttu-id="a462d-269">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="a462d-269">'SignalR' uid:</span></span> 

<span data-ttu-id="a462d-270">------ | | [Plik cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Określa ustawienia używane do tworzenia plików cookie z fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="a462d-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="a462d-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Domena pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="a462d-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | The domain of the cookie.</span></span> <span data-ttu-id="a462d-272">Wartość domyślna to `null` .</span><span class="sxs-lookup"><span data-stu-id="a462d-272">Defaults to `null`.</span></span> <span data-ttu-id="a462d-273">Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="a462d-273">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="a462d-274">Zalecaną alternatywą jest plik cookie. domena.</span><span class="sxs-lookup"><span data-stu-id="a462d-274">The recommended alternative is Cookie.Domain.</span></span> <span data-ttu-id="a462d-275">| | [Plik CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Nazwa pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="a462d-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | The name of the cookie.</span></span> <span data-ttu-id="a462d-276">Jeśli nie zostanie ustawiona, system generuje unikatową nazwę rozpoczynającą się od [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. przed fałszerstwem ").</span><span class="sxs-lookup"><span data-stu-id="a462d-276">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="a462d-277">Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="a462d-277">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="a462d-278">Zalecaną alternatywą jest Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="a462d-278">The recommended alternative is Cookie.Name.</span></span> <span data-ttu-id="a462d-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Ścieżka ustawiona w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="a462d-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | The path set on the cookie.</span></span> <span data-ttu-id="a462d-280">Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="a462d-280">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="a462d-281">Zalecaną alternatywą jest plik cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="a462d-281">The recommended alternative is Cookie.Path.</span></span> <span data-ttu-id="a462d-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nazwa ukrytego pola formularza używanego przez system antysfałszowany do renderowania tokenów antysfałszowanych w widokach.</span><span class="sxs-lookup"><span data-stu-id="a462d-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="a462d-283">| | [Nagłówekname](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nazwa nagłówka używanego przez system antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="a462d-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="a462d-284">Jeśli `null` System uwzględnia tylko dane formularza.</span><span class="sxs-lookup"><span data-stu-id="a462d-284">If `null`, the system considers only form data.</span></span> <span data-ttu-id="a462d-285">| | [RequireSSL](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Określa, czy protokół HTTPS jest wymagany przez system antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="a462d-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="a462d-286">Jeśli `true` żądania inne niż HTTPS kończą się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="a462d-286">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="a462d-287">Wartość domyślna to `false` .</span><span class="sxs-lookup"><span data-stu-id="a462d-287">Defaults to `false`.</span></span> <span data-ttu-id="a462d-288">Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="a462d-288">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="a462d-289">Zalecaną alternatywą jest ustawienie pliku cookie. SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="a462d-289">The recommended alternative is to set Cookie.SecurePolicy.</span></span> <span data-ttu-id="a462d-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Określa, czy należy pominąć generowanie `X-Frame-Options` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="a462d-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="a462d-291">Domyślnie nagłówek jest generowany z wartością "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="a462d-291">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="a462d-292">Wartość domyślna to `false` .</span><span class="sxs-lookup"><span data-stu-id="a462d-292">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="a462d-293">Aby uzyskać więcej informacji, zobacz [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="a462d-293">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="a462d-294">Konfigurowanie funkcji antysfałszowanych za pomocą IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="a462d-294">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="a462d-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) udostępnia interfejs API do konfigurowania funkcji antysfałszowanych.</span><span class="sxs-lookup"><span data-stu-id="a462d-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="a462d-296">`IAntiforgery`można żądać w `Configure` metodzie `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="a462d-296">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="a462d-297">W poniższym przykładzie użyto oprogramowania pośredniczącego ze strony głównej aplikacji do wygenerowania tokenu antysfałszowanego i wysłania go w odpowiedzi jako plik cookie (przy użyciu domyślnej konwencji nazewnictwa kątowego opisanej w dalszej części tego tematu):</span><span class="sxs-lookup"><span data-stu-id="a462d-297">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="a462d-298">Wymagaj weryfikacji przed fałszerstwem</span><span class="sxs-lookup"><span data-stu-id="a462d-298">Require antiforgery validation</span></span>

<span data-ttu-id="a462d-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) to filtr akcji, który można zastosować do poszczególnych akcji, kontrolera lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="a462d-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="a462d-300">Żądania wykonane do akcji, do których zastosowano ten filtr są blokowane, chyba że żądanie zawiera prawidłowy token antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="a462d-300">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="a462d-301">Ten `ValidateAntiForgeryToken` atrybut wymaga tokenu dla żądań do metod akcji, które to oznacza, w tym żądań HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="a462d-301">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="a462d-302">Jeśli `ValidateAntiForgeryToken` atrybut jest stosowany w kontrolerach aplikacji, można go zastąpić `IgnoreAntiforgeryToken` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="a462d-302">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="a462d-303">ASP.NET Core nie obsługuje dodawania tokenów antysfałszowanych w celu automatycznego uzyskiwania żądań.</span><span class="sxs-lookup"><span data-stu-id="a462d-303">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="a462d-304">Automatycznie Weryfikuj tokeny antysfałszowane wyłącznie dla niebezpiecznych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="a462d-304">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="a462d-305">Aplikacje ASP.NET Core nie generują tokenów antysfałszowanych dla bezpiecznych metod HTTP (GET, głowy, OPTIONS i TRACE).</span><span class="sxs-lookup"><span data-stu-id="a462d-305">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="a462d-306">Zamiast szeroko stosowanego `ValidateAntiForgeryToken` atrybutu, a następnie zastępując go atrybutami `IgnoreAntiforgeryToken` , można użyć atrybutu [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) .</span><span class="sxs-lookup"><span data-stu-id="a462d-306">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="a462d-307">Ten atrybut działa identycznie z `ValidateAntiForgeryToken` atrybutem, z tą różnicą, że nie wymaga tokenów dla żądań wysyłanych przy użyciu następujących metod http:</span><span class="sxs-lookup"><span data-stu-id="a462d-307">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="a462d-308">GET</span><span class="sxs-lookup"><span data-stu-id="a462d-308">GET</span></span>
* <span data-ttu-id="a462d-309">HEAD</span><span class="sxs-lookup"><span data-stu-id="a462d-309">HEAD</span></span>
* <span data-ttu-id="a462d-310">Opcje</span><span class="sxs-lookup"><span data-stu-id="a462d-310">OPTIONS</span></span>
* <span data-ttu-id="a462d-311">TRACE</span><span class="sxs-lookup"><span data-stu-id="a462d-311">TRACE</span></span>

<span data-ttu-id="a462d-312">Zalecamy użycie `AutoValidateAntiforgeryToken` ogólnie dla scenariuszy innych niż interfejsy API.</span><span class="sxs-lookup"><span data-stu-id="a462d-312">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="a462d-313">Gwarantuje to, że akcje wykonywane domyślnie są chronione.</span><span class="sxs-lookup"><span data-stu-id="a462d-313">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="a462d-314">Alternatywą jest ignorowanie tokenów antysfałszowanych domyślnie, chyba że `ValidateAntiForgeryToken` jest on stosowany do poszczególnych metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a462d-314">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="a462d-315">Bardziej prawdopodobnie w tym scenariuszu, aby metoda po akcji nie była chroniona przez pomyłkę, pozostawiając aplikację narażoną na ataki CSRF.</span><span class="sxs-lookup"><span data-stu-id="a462d-315">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="a462d-316">Wszystkie wpisy powinny wysyłać token antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="a462d-316">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="a462d-317">Interfejsy API nie mają mechanizmu automatycznego do wysyłania niezwiązanego z plikiem cookie części tokenu.</span><span class="sxs-lookup"><span data-stu-id="a462d-317">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="a462d-318">Implementacja prawdopodobnie zależy od implementacji kodu klienta.</span><span class="sxs-lookup"><span data-stu-id="a462d-318">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="a462d-319">Poniżej przedstawiono kilka przykładów:</span><span class="sxs-lookup"><span data-stu-id="a462d-319">Some examples are shown below:</span></span>

<span data-ttu-id="a462d-320">Przykład na poziomie klasy:</span><span class="sxs-lookup"><span data-stu-id="a462d-320">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="a462d-321">Przykład globalny:</span><span class="sxs-lookup"><span data-stu-id="a462d-321">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a462d-322">Services. AddMvc (opcje => opcje. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));</span><span class="sxs-lookup"><span data-stu-id="a462d-322">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="a462d-323">Zastąp atrybuty globalnym lub kontrolerem antyfałszowanym</span><span class="sxs-lookup"><span data-stu-id="a462d-323">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="a462d-324">Filtr [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) służy do eliminacji potrzeb tokenu antysfałszowanego dla danej akcji (lub kontrolera).</span><span class="sxs-lookup"><span data-stu-id="a462d-324">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="a462d-325">Po zastosowaniu ten filtr przesłania `ValidateAntiForgeryToken` i `AutoValidateAntiforgeryToken` filtry określone na wyższym poziomie (globalnie lub na kontrolerze).</span><span class="sxs-lookup"><span data-stu-id="a462d-325">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="a462d-326">Odśwież tokeny po uwierzytelnieniu</span><span class="sxs-lookup"><span data-stu-id="a462d-326">Refresh tokens after authentication</span></span>

<span data-ttu-id="a462d-327">Tokeny należy odświeżyć po uwierzytelnieniu użytkownika przez przekierowanie użytkownika do strony widoku lub strony Razor .</span><span class="sxs-lookup"><span data-stu-id="a462d-327">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="a462d-328">JavaScript, AJAX i aplikacji jednostronicowych</span><span class="sxs-lookup"><span data-stu-id="a462d-328">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="a462d-329">W tradycyjnych aplikacjach opartych na języku HTML tokeny zabezpieczające są przesyłane do serwera przy użyciu ukrytych pól formularzy.</span><span class="sxs-lookup"><span data-stu-id="a462d-329">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="a462d-330">W nowoczesnych aplikacjach opartych na języku JavaScript i aplikacji jednostronicowych wiele żądań jest wykonywanych programowo.</span><span class="sxs-lookup"><span data-stu-id="a462d-330">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="a462d-331">Te żądania AJAX mogą używać innych technik (takich jak nagłówki żądań lub pliki cookie) do wysyłania tokenu.</span><span class="sxs-lookup"><span data-stu-id="a462d-331">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="a462d-332">Jeśli pliki cookie są używane do przechowywania tokenów uwierzytelniania i uwierzytelniania żądań interfejsu API na serwerze, CSRF jest potencjalnym problemem.</span><span class="sxs-lookup"><span data-stu-id="a462d-332">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="a462d-333">Jeśli magazyn lokalny jest używany do przechowywania tokenu, luka w zabezpieczeniach CSRF może zostać wyeliminowana, ponieważ wartości z magazynu lokalnego nie są automatycznie wysyłane do serwera przy użyciu każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="a462d-333">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="a462d-334">W tym celu należy użyć magazynu lokalnego do przechowywania na kliencie tokenu antysfałszowanego i wysyłania tokenu jako nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="a462d-334">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="a462d-335">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a462d-335">JavaScript</span></span>

<span data-ttu-id="a462d-336">Przy użyciu języka JavaScript z widokami token można utworzyć przy użyciu usługi z poziomu widoku.</span><span class="sxs-lookup"><span data-stu-id="a462d-336">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="a462d-337">Wsuń usługę [Microsoft. AspNetCore. antyfałszerstwe. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) do widoku i Wywołaj [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="a462d-337">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="a462d-338">Takie podejście eliminuje konieczność bezpośredniej obsługi ustawień plików cookie z serwera lub odczytywanie ich z klienta programu.</span><span class="sxs-lookup"><span data-stu-id="a462d-338">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="a462d-339">Poprzedni przykład używa języka JavaScript do odczytywania wartości pola ukrytego dla nagłówka z WPISem AJAX.</span><span class="sxs-lookup"><span data-stu-id="a462d-339">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="a462d-340">Język JavaScript może również uzyskiwać dostęp do tokenów w plikach cookie i używać zawartości pliku cookie do tworzenia nagłówka z wartością tokenu.</span><span class="sxs-lookup"><span data-stu-id="a462d-340">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="a462d-341">Zakładając, że żądanie skryptu wysłano token w nagłówku o nazwie `X-CSRF-TOKEN` , skonfiguruj usługę antysfałszowaną, aby wyszukać `X-CSRF-TOKEN` Nagłówek:</span><span class="sxs-lookup"><span data-stu-id="a462d-341">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="a462d-342">Poniższy przykład używa języka JavaScript, aby wykonać żądanie AJAX z odpowiednim nagłówkiem:</span><span class="sxs-lookup"><span data-stu-id="a462d-342">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="a462d-343">AngularJS</span><span class="sxs-lookup"><span data-stu-id="a462d-343">AngularJS</span></span>

<span data-ttu-id="a462d-344">AngularJS używa konwencji do adresowania CSRF.</span><span class="sxs-lookup"><span data-stu-id="a462d-344">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="a462d-345">Jeśli serwer wysyła plik cookie o nazwie `XSRF-TOKEN` , `$http` Usługa AngularJS dodaje wartość cookie do nagłówka podczas wysyłania żądania do serwera.</span><span class="sxs-lookup"><span data-stu-id="a462d-345">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="a462d-346">Ten proces jest automatyczny.</span><span class="sxs-lookup"><span data-stu-id="a462d-346">This process is automatic.</span></span> <span data-ttu-id="a462d-347">Nagłówek nie musi być jawnie ustawiony na kliencie.</span><span class="sxs-lookup"><span data-stu-id="a462d-347">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="a462d-348">Nazwa nagłówka to `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="a462d-348">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="a462d-349">Serwer powinien wykryć ten nagłówek i zweryfikować jego zawartość.</span><span class="sxs-lookup"><span data-stu-id="a462d-349">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="a462d-350">Aby program ASP.NET Core API działał z tą konwencją podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a462d-350">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="a462d-351">Skonfiguruj aplikację, aby zapewnić token w pliku cookie o nazwie `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="a462d-351">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="a462d-352">Skonfiguruj usługę antysfałszowaną, aby wyszukać nagłówek o nazwie `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="a462d-352">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="a462d-353">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a462d-353">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="a462d-354">Zwiększ fałszerstwo</span><span class="sxs-lookup"><span data-stu-id="a462d-354">Extend antiforgery</span></span>

<span data-ttu-id="a462d-355">Typ [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) umożliwia deweloperom zwiększenie zachowania systemu antyCSRFowego przez dwukrotne wyzwolenie dodatkowych danych w każdym tokenie.</span><span class="sxs-lookup"><span data-stu-id="a462d-355">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="a462d-356">Metoda [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) jest wywoływana za każdym razem, gdy generowany jest token pola, a zwracana wartość jest osadzona w wygenerowanym tokenie.</span><span class="sxs-lookup"><span data-stu-id="a462d-356">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="a462d-357">Realizator może zwrócić sygnaturę czasową, identyfikator jednorazowy lub inną wartość, a następnie wywołać [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) , aby zweryfikować te dane po sprawdzeniu poprawności tokenu.</span><span class="sxs-lookup"><span data-stu-id="a462d-357">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="a462d-358">Nazwa użytkownika klienta jest już osadzona w wygenerowanych tokenach, więc nie trzeba uwzględniać tych informacji.</span><span class="sxs-lookup"><span data-stu-id="a462d-358">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="a462d-359">Jeśli token zawiera dane uzupełniające, ale nie `IAntiForgeryAdditionalDataProvider` jest skonfigurowany, dane uzupełniające nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="a462d-359">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a462d-360">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a462d-360">Additional resources</span></span>

* <span data-ttu-id="a462d-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) w [programie Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="a462d-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>

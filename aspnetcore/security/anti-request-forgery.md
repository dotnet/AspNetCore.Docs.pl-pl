---
title: Uniemożliwiaj ataki między witrynami (XSRF/CSRF) w ASP.NET Core
author: steve-smith
description: Dowiedz się, jak zapobiegać atakom na aplikacje sieci Web, w których złośliwa witryna sieci Web może mieć wpływ na interakcję między przeglądarką klienta a aplikacją.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: security/anti-request-forgery
ms.openlocfilehash: d0cce4f48151ab56774ab28eb6d89a687b3747af
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635128"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="1d1e6-103">Uniemożliwiaj ataki między witrynami (XSRF/CSRF) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d1e6-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="1d1e6-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1d1e6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1d1e6-105">Sfałszowanie żądań między lokacjami (znane również jako XSRF lub CSRF) jest atakiem na aplikacje hostowane w sieci Web, dzięki czemu złośliwa aplikacja internetowa może mieć wpływ na interakcję między przeglądarką klienta a aplikacją sieci Web, która ufa tej przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="1d1e6-106">Te ataki są możliwe, ponieważ przeglądarki sieci Web wysyłają automatyczne typy tokenów uwierzytelniania przy użyciu każdego żądania do witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="1d1e6-107">Ta forma wykorzystania jest również znana jako *atak dwukrotnego kliknięcia* lub *sesji* , ponieważ atakujący wykorzystuje wcześniej uwierzytelnioną sesję użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="1d1e6-108">Przykład ataku CSRF:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="1d1e6-109">Użytkownik loguje się `www.good-banking-site.com` przy użyciu uwierzytelniania formularzy.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="1d1e6-110">Serwer uwierzytelnia użytkownika i wystawia odpowiedź obejmującą uwierzytelnianie cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="1d1e6-111">Lokacja jest narażona na ataki, ponieważ ufa każde żądanie otrzymane z prawidłowym uwierzytelnianiem cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="1d1e6-112">Użytkownik odwiedza złośliwą lokację `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="1d1e6-113">Złośliwa witryna programu `www.bad-crook-site.com` zawiera formularz HTML podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="1d1e6-114">Zwróć uwagę na to, że formularz `action` wpisuje w nieszkodliwej witrynie.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="1d1e6-115">To jest część "wiele witryn" z CSRF.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="1d1e6-116">Użytkownik wybiera przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-116">The user selects the submit button.</span></span> <span data-ttu-id="1d1e6-117">Przeglądarka wykonuje żądanie i automatycznie uwzględnia uwierzytelnianie cookie dla żądanych domen `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="1d1e6-118">Żądanie jest uruchamiane na `www.good-banking-site.com` serwerze z kontekstem uwierzytelniania użytkownika i może wykonywać dowolną akcję, którą może wykonać uwierzytelniony użytkownik.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="1d1e6-119">Oprócz scenariusza, w którym użytkownik wybiera przycisk, aby przesłać formularz, złośliwa witryna może:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="1d1e6-120">Uruchom skrypt, który automatycznie przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="1d1e6-121">Wyślij formularz przesyłania w postaci żądania AJAX.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="1d1e6-122">Ukryj formularz przy użyciu CSS.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-122">Hide the form using CSS.</span></span>

<span data-ttu-id="1d1e6-123">Te alternatywne scenariusze nie wymagają żadnych działań ani danych wejściowych od użytkownika innego niż początkowo odwiedzają złośliwe witryny.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="1d1e6-124">Korzystanie z protokołu HTTPS nie uniemożliwia ataku CSRF.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="1d1e6-125">Złośliwa lokacja może wysłać `https://www.good-banking-site.com/` żądanie równie łatwo, jak może wysłać niezabezpieczone żądanie.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="1d1e6-126">Niektóre ataki są ukierunkowanymi punktami końcowymi, które odpowiadają na żądania GET, w takim przypadku można użyć znacznika obrazu do wykonania akcji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="1d1e6-127">Ta forma ataku jest wspólna w witrynach forum, które zezwalają na obrazy, ale blokują kod JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="1d1e6-128">Aplikacje, które zmieniają stan w żądaniach GET, gdzie zmienne lub zasoby są zmieniane, są narażone na złośliwe ataki.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="1d1e6-129">**Żądania GET, które zmieniają stan są niezabezpieczone. Najlepszym rozwiązaniem jest nigdy nie zmieniaj stanu żądania GET.**</span><span class="sxs-lookup"><span data-stu-id="1d1e6-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="1d1e6-130">CSRF ataki są dostępne dla aplikacji sieci Web, które używają usługi cookie s do uwierzytelniania, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="1d1e6-131">Przeglądarki są przechowywane cookie przez aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="1d1e6-132">Przechowywane cookie s obejmują sesje cookie dla uwierzytelnionych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="1d1e6-133">Przeglądarki wysyłają cookie do aplikacji sieci Web wszystkie żądania skojarzone z domeną, niezależnie od tego, w jaki sposób żądanie do aplikacji zostało wygenerowane w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="1d1e6-134">Jednak ataki CSRF nie ograniczają się do korzystania z programu cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="1d1e6-135">Na przykład uwierzytelnianie podstawowe i szyfrowane jest również zagrożone.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="1d1e6-136">Gdy użytkownik zaloguje się przy użyciu uwierzytelniania podstawowego lub szyfrowanego, przeglądarka automatycznie wyśle poświadczenia do momentu &dagger; zakończenia sesji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="1d1e6-137">&dagger;W tym kontekście *sesja* dotyczy sesji po stronie klienta, podczas której użytkownik jest uwierzytelniany.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="1d1e6-138">Nie jest on związany z sesjami po stronie serwera lub [ASP.NET Core pośredniczących sesji](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="1d1e6-139">Użytkownicy mogą chronić przed lukami w CSRF, podejmując środki ostrożności:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="1d1e6-140">Wyloguj się z aplikacji sieci Web po zakończeniu korzystania z nich.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="1d1e6-141">cookieOkresowe Czyszczenie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="1d1e6-142">Jednak luki w zabezpieczeniach CSRF są zasadniczo problemem z aplikacją sieci Web, a nie użytkownikiem końcowym.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="1d1e6-143">Podstawy uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1d1e6-143">Authentication fundamentals</span></span>

<span data-ttu-id="1d1e6-144">Cookieuwierzytelnianie oparte na programie to popularna forma uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="1d1e6-145">Systemy uwierzytelniania oparte na tokenach zwiększają popularność, szczególnie w przypadku aplikacji jednostronicowych (aplikacji jednostronicowych).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="1d1e6-146">Cookieuwierzytelnianie oparte na usłudze</span><span class="sxs-lookup"><span data-stu-id="1d1e6-146">Cookie-based authentication</span></span>

<span data-ttu-id="1d1e6-147">Gdy użytkownik jest uwierzytelniany przy użyciu nazwy użytkownika i hasła, zostanie wystawiony token zawierający bilet uwierzytelniania, który może być używany do uwierzytelniania i autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="1d1e6-148">Token jest przechowywany jako cookie towarzyszący każdemu żądania przez klienta.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="1d1e6-149">Generowanie i sprawdzanie poprawności cookie jest wykonywane przez Cookie oprogramowanie pośredniczące uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="1d1e6-150">[Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) deserializacji podmiotu zabezpieczeń użytkownika do zaszyfrowanego cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="1d1e6-151">Na kolejnych żądaniach oprogramowanie pośredniczące sprawdza, czy cookie ponownie tworzy podmiot zabezpieczeń i przypisuje podmiotowi zabezpieczeń do właściwości [użytkownika](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="1d1e6-152">Uwierzytelnianie oparte na tokenach</span><span class="sxs-lookup"><span data-stu-id="1d1e6-152">Token-based authentication</span></span>

<span data-ttu-id="1d1e6-153">Po uwierzytelnieniu użytkownika są wystawiane tokeny (nie token antysfałszowany).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="1d1e6-154">Token zawiera informacje o użytkowniku w formie [oświadczeń](/dotnet/framework/security/claims-based-identity-model) lub Token odwołania, który wskazuje, że stan użytkownika w aplikacji jest obsługiwany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="1d1e6-155">Gdy użytkownik próbuje uzyskać dostęp do zasobu wymagającego uwierzytelnienia, token jest wysyłany do aplikacji z dodatkowym nagłówkiem autoryzacji w postaci tokenu okaziciela.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="1d1e6-156">Powoduje to bezstanową aplikację.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-156">This makes the app stateless.</span></span> <span data-ttu-id="1d1e6-157">W każdym kolejnym żądaniu token jest przesyłany do żądania weryfikacji po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="1d1e6-158">Ten token nie jest *szyfrowany*; jest ona *zaszyfrowana*.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="1d1e6-159">Na serwerze token jest zdekodowany w celu uzyskania dostępu do informacji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="1d1e6-160">Aby wysłać token w kolejnych żądaniach, Zapisz token w lokalnym magazynie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="1d1e6-161">Nie należy obawiać się o luki w zabezpieczeniach CSRF, jeśli token jest przechowywany w lokalnym magazynie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="1d1e6-162">CSRF jest problemem, gdy token jest przechowywany w cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="1d1e6-163">Aby uzyskać więcej informacji, zobacz przykładowy kod rozchód spa w usłudze GitHub [dodaje dwa cookie s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="1d1e6-164">Wiele aplikacji hostowanych w jednej domenie</span><span class="sxs-lookup"><span data-stu-id="1d1e6-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="1d1e6-165">Udostępnione środowiska hostingu są podatne na przejmowanie sesji, CSRF logowania i inne ataki.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="1d1e6-166">Chociaż `example1.contoso.net` i `example2.contoso.net` są różnymi hostami, istnieje niejawna relacja zaufania między hostami w `*.contoso.net` domenie.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="1d1e6-167">Ta niejawna relacja zaufania umożliwia niezaufanym hostom wpływanie na wszystkie inne osoby cookie (zasady tego samego źródła, które zarządza żądaniami AJAX, nie muszą mieć zastosowania do protokołu HTTP cookie s).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="1d1e6-168">Ataki wykorzystujące zaufane cookie elementy w aplikacjach hostowanych w tej samej domenie mogą uniemożliwiać udostępnianie domen.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="1d1e6-169">Gdy każda aplikacja jest hostowana we własnej domenie, nie istnieje niejawna cookie relacja zaufania do wykorzystania.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="1d1e6-170">Konfiguracja antysfałszowana ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d1e6-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="1d1e6-171">ASP.NET Core implementuje ochronę przed fałszowaniem za pomocą [ASP.NET Core ochrony danych](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="1d1e6-172">Stos ochrony danych musi być skonfigurowany do pracy w farmie serwerów.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="1d1e6-173">Aby uzyskać więcej informacji, zobacz [Konfigurowanie ochrony danych](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d1e6-174">Oprogramowanie pośredniczące przed fałszowaniem jest dodawane do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) , gdy jeden z następujących interfejsów API jest wywoływany w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1d1e6-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d1e6-175">Oprogramowanie pośredniczące przed fałszowaniem jest dodawane do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) , gdy <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> jest wywoływana w `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="1d1e6-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="1d1e6-176">W ASP.NET Core 2,0 lub nowszej [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wprowadza do elementów formularza HTML tokeny zabezpieczające przed fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="1d1e6-177">Następujące znaczniki w Razor pliku automatycznie generują tokeny zabezpieczające przed fałszerstwem:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="1d1e6-178">Podobnie [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) domyślnie generuje tokeny zabezpieczające przed fałszerstwem, jeśli nie można pobrać metody formularza.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="1d1e6-179">Automatyczne generowanie tokenów antysfałszowanych dla elementów formularza HTML występuje, gdy `<form>` tag zawiera `method="post"` atrybut i jeden z następujących warunków jest spełniony:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="1d1e6-180">Atrybut Action jest pusty ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="1d1e6-181">Nie podano atrybutu Action ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="1d1e6-182">Automatyczne generowanie tokenów antysfałszowanych dla elementów formularza HTML może być wyłączone:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="1d1e6-183">Jawnie wyłącz tokeny zabezpieczające przed fałszowaniem z `asp-antiforgery` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="1d1e6-184">Element form jest wyłączany z pomocników tagów przy użyciu [symbolu Autowypełniania tagów!](xref:mvc/views/tag-helpers/intro#opt-out)</span><span class="sxs-lookup"><span data-stu-id="1d1e6-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="1d1e6-185">Usuń `FormTagHelper` z widoku.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="1d1e6-186">`FormTagHelper`Można usunąć z widoku, dodając do widoku następującą dyrektywę Razor :</span><span class="sxs-lookup"><span data-stu-id="1d1e6-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="1d1e6-187">[ Razor Strony](xref:razor-pages/index) są automatycznie chronione przed XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="1d1e6-188">Aby uzyskać więcej informacji, zobacz [XSRF/CSRF i Razor Pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="1d1e6-189">Najbardziej typowym podejściem do obrony przed atakami CSRF jest użycie *wzorca tokenu synchronizatora* (STP).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="1d1e6-190">Wartość STP jest używana, gdy użytkownik żąda strony z danymi formularza:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="1d1e6-191">Serwer wysyła do klienta token skojarzony z tożsamością bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="1d1e6-192">Klient wysyła do serwera token z powrotem w celu weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="1d1e6-193">Jeśli serwer odbiera token, który nie jest zgodny z tożsamością uwierzytelnionego użytkownika, żądanie zostanie odrzucone.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="1d1e6-194">Token jest unikatowy i nieprzewidywalny.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="1d1e6-195">Token może również służyć do zapewnienia prawidłowej sekwencji serii żądań (na przykład w celu zapewnienia sekwencji żądań: Strona 1 > stronie 2 > stronie 3).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="1d1e6-196">Wszystkie formularze w ASP.NET Core MVC i Razor Szablony stron generują tokeny zabezpieczające przed fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="1d1e6-197">Poniższa para przykładów widoku generuje tokeny zabezpieczające przed fałszerstwem:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="1d1e6-198">Jawnie Dodaj token antysfałszowany do `<form>` elementu bez korzystania z pomocników tagów, korzystając z pomocy języka HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="1d1e6-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="1d1e6-199">W każdym z powyższych przypadków ASP.NET Core dodaje ukryte pole formularza podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="1d1e6-200">ASP.NET Core zawiera trzy [filtry](xref:mvc/controllers/filters) do pracy z tokenami antyfałszowanymi:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="1d1e6-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="1d1e6-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="1d1e6-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="1d1e6-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="1d1e6-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="1d1e6-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="1d1e6-204">Opcje samopodrabiania</span><span class="sxs-lookup"><span data-stu-id="1d1e6-204">Antiforgery options</span></span>

<span data-ttu-id="1d1e6-205">Dostosuj [Opcje antysfałszowane](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1d1e6-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="1d1e6-206">&dagger;Ustaw właściwości przeciwpodrabiane `Cookie` przy użyciu właściwości klasy [ Cookie konstruktora](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="1d1e6-207">Opcja</span><span class="sxs-lookup"><span data-stu-id="1d1e6-207">Option</span></span> | <span data-ttu-id="1d1e6-208">Opis</span><span class="sxs-lookup"><span data-stu-id="1d1e6-208">Description</span></span> |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="1d1e6-209">Określa ustawienia używane do tworzenia przed fałszerstwem cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-209">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="1d1e6-210">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="1d1e6-210">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="1d1e6-211">Nazwa ukrytego pola formularza używanego przez system antysfałszowany do renderowania tokenów antysfałszowanych w widokach.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-211">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="1d1e6-212">Nagłówek nagłówka</span><span class="sxs-lookup"><span data-stu-id="1d1e6-212">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="1d1e6-213">Nazwa nagłówka używanego przez system antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-213">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="1d1e6-214">Jeśli `null` System uwzględnia tylko dane formularza.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-214">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="1d1e6-215">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="1d1e6-215">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="1d1e6-216">Określa, czy należy pominąć generowanie `X-Frame-Options` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-216">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="1d1e6-217">Domyślnie nagłówek jest generowany z wartością "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="1d1e6-217">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="1d1e6-218">Wartość domyślna to `false` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-218">Defaults to `false`.</span></span> |

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

| <span data-ttu-id="1d1e6-219">Opcja</span><span class="sxs-lookup"><span data-stu-id="1d1e6-219">Option</span></span> | <span data-ttu-id="1d1e6-220">Opis</span><span class="sxs-lookup"><span data-stu-id="1d1e6-220">Description</span></span> |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="1d1e6-221">Określa ustawienia używane do tworzenia przed fałszerstwem cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-221">Determines the settings used to create the antiforgery cookies.</span></span> |
| <span data-ttu-id="1d1e6-222">[CookieDomena](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain)</span><span class="sxs-lookup"><span data-stu-id="1d1e6-222">[CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain)</span></span> | <span data-ttu-id="1d1e6-223">Domena cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-223">The domain of the cookie.</span></span> <span data-ttu-id="1d1e6-224">Wartość domyślna to `null` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-224">Defaults to `null`.</span></span> <span data-ttu-id="1d1e6-225">Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-225">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="1d1e6-226">Zalecaną alternatywą jest Cookie . Domeny.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-226">The recommended alternative is Cookie.Domain.</span></span> |
| <span data-ttu-id="1d1e6-227">[CookieNazwa](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename)</span><span class="sxs-lookup"><span data-stu-id="1d1e6-227">[CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename)</span></span> | <span data-ttu-id="1d1e6-228">Nazwa elementu cookie.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-228">The name of the cookie.</span></span> <span data-ttu-id="1d1e6-229">Jeśli nie zostanie ustawiona, system generuje unikatową nazwę rozpoczynającą się od [domyślnego Cookie prefiksu](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. przed fałszerstwem ").</span><span class="sxs-lookup"><span data-stu-id="1d1e6-229">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="1d1e6-230">Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-230">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="1d1e6-231">Zalecaną alternatywą jest Cookie . Nazwij.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-231">The recommended alternative is Cookie.Name.</span></span> |
| <span data-ttu-id="1d1e6-232">[CookieŚcieżka](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath)</span><span class="sxs-lookup"><span data-stu-id="1d1e6-232">[CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath)</span></span> | <span data-ttu-id="1d1e6-233">Ścieżka ustawiona na cookie .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-233">The path set on the cookie.</span></span> <span data-ttu-id="1d1e6-234">Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-234">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="1d1e6-235">Zalecaną alternatywą jest Cookie . Ścieżka.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-235">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="1d1e6-236">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="1d1e6-236">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="1d1e6-237">Nazwa ukrytego pola formularza używanego przez system antysfałszowany do renderowania tokenów antysfałszowanych w widokach.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-237">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="1d1e6-238">Nagłówek nagłówka</span><span class="sxs-lookup"><span data-stu-id="1d1e6-238">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="1d1e6-239">Nazwa nagłówka używanego przez system antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-239">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="1d1e6-240">Jeśli `null` System uwzględnia tylko dane formularza.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-240">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="1d1e6-241">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="1d1e6-241">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="1d1e6-242">Określa, czy protokół HTTPS jest wymagany przez system antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-242">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="1d1e6-243">Jeśli `true` żądania inne niż HTTPS kończą się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-243">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="1d1e6-244">Wartość domyślna to `false` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-244">Defaults to `false`.</span></span> <span data-ttu-id="1d1e6-245">Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-245">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="1d1e6-246">Zalecaną alternatywą jest ustawienie Cookie . SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-246">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="1d1e6-247">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="1d1e6-247">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="1d1e6-248">Określa, czy należy pominąć generowanie `X-Frame-Options` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-248">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="1d1e6-249">Domyślnie nagłówek jest generowany z wartością "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="1d1e6-249">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="1d1e6-250">Wartość domyślna to `false` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-250">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="1d1e6-251">Aby uzyskać więcej informacji, zobacz [ Cookie AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-251">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="1d1e6-252">Konfigurowanie funkcji antysfałszowanych za pomocą IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="1d1e6-252">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="1d1e6-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) udostępnia interfejs API do konfigurowania funkcji antysfałszowanych.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="1d1e6-254">`IAntiforgery` można żądać w `Configure` metodzie `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-254">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="1d1e6-255">W poniższym przykładzie użyto oprogramowania pośredniczącego ze strony głównej aplikacji do wygenerowania tokenu antysfałszowanego i wysłania go w odpowiedzi jako cookie (przy użyciu domyślnej konwencji nazewnictwa kątowego opisanej w dalszej części tego tematu):</span><span class="sxs-lookup"><span data-stu-id="1d1e6-255">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

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

### <a name="require-antiforgery-validation"></a><span data-ttu-id="1d1e6-256">Wymagaj weryfikacji przed fałszerstwem</span><span class="sxs-lookup"><span data-stu-id="1d1e6-256">Require antiforgery validation</span></span>

<span data-ttu-id="1d1e6-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) to filtr akcji, który można zastosować do poszczególnych akcji, kontrolera lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="1d1e6-258">Żądania wykonane do akcji, do których zastosowano ten filtr są blokowane, chyba że żądanie zawiera prawidłowy token antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-258">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="1d1e6-259">Ten `ValidateAntiForgeryToken` atrybut wymaga tokenu dla żądań do metod akcji, które to oznacza, w tym żądań HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-259">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="1d1e6-260">Jeśli `ValidateAntiForgeryToken` atrybut jest stosowany w kontrolerach aplikacji, można go zastąpić `IgnoreAntiforgeryToken` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-260">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="1d1e6-261">ASP.NET Core nie obsługuje dodawania tokenów antysfałszowanych w celu automatycznego uzyskiwania żądań.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-261">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="1d1e6-262">Automatycznie Weryfikuj tokeny antysfałszowane wyłącznie dla niebezpiecznych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="1d1e6-262">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="1d1e6-263">Aplikacje ASP.NET Core nie generują tokenów antysfałszowanych dla bezpiecznych metod HTTP (GET, głowy, OPTIONS i TRACE).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-263">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="1d1e6-264">Zamiast szeroko stosowanego `ValidateAntiForgeryToken` atrybutu, a następnie zastępując go atrybutami `IgnoreAntiforgeryToken` , można użyć atrybutu [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-264">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="1d1e6-265">Ten atrybut działa identycznie z `ValidateAntiForgeryToken` atrybutem, z tą różnicą, że nie wymaga tokenów dla żądań wysyłanych przy użyciu następujących metod http:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-265">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="1d1e6-266">GET</span><span class="sxs-lookup"><span data-stu-id="1d1e6-266">GET</span></span>
* <span data-ttu-id="1d1e6-267">HEAD</span><span class="sxs-lookup"><span data-stu-id="1d1e6-267">HEAD</span></span>
* <span data-ttu-id="1d1e6-268">Opcje</span><span class="sxs-lookup"><span data-stu-id="1d1e6-268">OPTIONS</span></span>
* <span data-ttu-id="1d1e6-269">TRACE</span><span class="sxs-lookup"><span data-stu-id="1d1e6-269">TRACE</span></span>

<span data-ttu-id="1d1e6-270">Zalecamy użycie `AutoValidateAntiforgeryToken` ogólnie dla scenariuszy innych niż interfejsy API.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-270">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="1d1e6-271">Gwarantuje to, że akcje wykonywane domyślnie są chronione.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-271">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="1d1e6-272">Alternatywą jest ignorowanie tokenów antysfałszowanych domyślnie, chyba że `ValidateAntiForgeryToken` jest on stosowany do poszczególnych metod akcji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-272">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="1d1e6-273">Bardziej prawdopodobnie w tym scenariuszu, aby metoda po akcji nie była chroniona przez pomyłkę, pozostawiając aplikację narażoną na ataki CSRF.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-273">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="1d1e6-274">Wszystkie wpisy powinny wysyłać token antysfałszowany.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-274">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="1d1e6-275">Interfejsy API nie mają mechanizmu automatycznego do wysyłania niebędących cookie częścią tokenu.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-275">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="1d1e6-276">Implementacja prawdopodobnie zależy od implementacji kodu klienta.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-276">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="1d1e6-277">Poniżej przedstawiono kilka przykładów:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-277">Some examples are shown below:</span></span>

<span data-ttu-id="1d1e6-278">Przykład na poziomie klasy:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-278">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="1d1e6-279">Przykład globalny:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-279">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d1e6-280">Services. AddMvc (opcje => opcje. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));</span><span class="sxs-lookup"><span data-stu-id="1d1e6-280">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="1d1e6-281">Zastąp atrybuty globalnym lub kontrolerem antyfałszowanym</span><span class="sxs-lookup"><span data-stu-id="1d1e6-281">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="1d1e6-282">Filtr [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) służy do eliminacji potrzeb tokenu antysfałszowanego dla danej akcji (lub kontrolera).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-282">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="1d1e6-283">Po zastosowaniu ten filtr przesłania `ValidateAntiForgeryToken` i `AutoValidateAntiforgeryToken` filtry określone na wyższym poziomie (globalnie lub na kontrolerze).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-283">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="1d1e6-284">Odśwież tokeny po uwierzytelnieniu</span><span class="sxs-lookup"><span data-stu-id="1d1e6-284">Refresh tokens after authentication</span></span>

<span data-ttu-id="1d1e6-285">Tokeny należy odświeżyć po uwierzytelnieniu użytkownika przez przekierowanie użytkownika do strony widoku lub strony Razor .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-285">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="1d1e6-286">JavaScript, AJAX i aplikacji jednostronicowych</span><span class="sxs-lookup"><span data-stu-id="1d1e6-286">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="1d1e6-287">W tradycyjnych aplikacjach opartych na języku HTML tokeny zabezpieczające są przesyłane do serwera przy użyciu ukrytych pól formularzy.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-287">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="1d1e6-288">W nowoczesnych aplikacjach opartych na języku JavaScript i aplikacji jednostronicowych wiele żądań jest wykonywanych programowo.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-288">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="1d1e6-289">Te żądania AJAX mogą używać innych technik (takich jak nagłówki żądań lub cookie s) do wysyłania tokenu.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-289">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="1d1e6-290">Jeśli cookie s są używane do przechowywania tokenów uwierzytelniania i uwierzytelniania żądań interfejsu API na serwerze, CSRF jest potencjalnym problemem.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-290">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="1d1e6-291">Jeśli magazyn lokalny jest używany do przechowywania tokenu, luka w zabezpieczeniach CSRF może zostać wyeliminowana, ponieważ wartości z magazynu lokalnego nie są automatycznie wysyłane do serwera przy użyciu każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-291">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="1d1e6-292">W tym celu należy użyć magazynu lokalnego do przechowywania na kliencie tokenu antysfałszowanego i wysyłania tokenu jako nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-292">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="1d1e6-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="1d1e6-293">JavaScript</span></span>

<span data-ttu-id="1d1e6-294">Przy użyciu języka JavaScript z widokami token można utworzyć przy użyciu usługi z poziomu widoku.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-294">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="1d1e6-295">Wsuń usługę [Microsoft. AspNetCore. antyfałszerstwe. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) do widoku i Wywołaj [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="1d1e6-295">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="1d1e6-296">Takie podejście eliminuje konieczność bezpośredniej obsługi ustawień cookie z serwera lub odczytywania ich przez klienta.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-296">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="1d1e6-297">Poprzedni przykład używa języka JavaScript do odczytywania wartości pola ukrytego dla nagłówka z WPISem AJAX.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-297">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="1d1e6-298">Kod JavaScript może również uzyskiwać dostęp do tokenów w cookie s i używać cookie zawartości do tworzenia nagłówka z wartością tokenu.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-298">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="1d1e6-299">Zakładając, że żądanie skryptu wysłano token w nagłówku o nazwie `X-CSRF-TOKEN` , skonfiguruj usługę antysfałszowaną, aby wyszukać `X-CSRF-TOKEN` Nagłówek:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-299">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="1d1e6-300">Poniższy przykład używa języka JavaScript, aby wykonać żądanie AJAX z odpowiednim nagłówkiem:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-300">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

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

### <a name="angularjs"></a><span data-ttu-id="1d1e6-301">AngularJS</span><span class="sxs-lookup"><span data-stu-id="1d1e6-301">AngularJS</span></span>

<span data-ttu-id="1d1e6-302">AngularJS używa konwencji do adresowania CSRF.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-302">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="1d1e6-303">Jeśli serwer wysyła plik cookie o nazwie `XSRF-TOKEN` , `$http` Usługa AngularJS dodaje cookie wartość do nagłówka podczas wysyłania żądania do serwera.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-303">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="1d1e6-304">Ten proces jest automatyczny.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-304">This process is automatic.</span></span> <span data-ttu-id="1d1e6-305">Nagłówek nie musi być jawnie ustawiony na kliencie.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-305">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="1d1e6-306">Nazwa nagłówka to `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-306">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="1d1e6-307">Serwer powinien wykryć ten nagłówek i zweryfikować jego zawartość.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-307">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="1d1e6-308">Aby program ASP.NET Core API działał z tą konwencją podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1d1e6-308">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="1d1e6-309">Skonfiguruj aplikację, aby zapewnić token w cookie wywołaniu `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-309">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="1d1e6-310">Skonfiguruj usługę antysfałszowaną, aby wyszukać nagłówek o nazwie `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="1d1e6-310">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

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

<span data-ttu-id="1d1e6-311">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1d1e6-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="1d1e6-312">Zwiększ fałszerstwo</span><span class="sxs-lookup"><span data-stu-id="1d1e6-312">Extend antiforgery</span></span>

<span data-ttu-id="1d1e6-313">Typ [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) umożliwia deweloperom zwiększenie zachowania systemu antyCSRFowego przez dwukrotne wyzwolenie dodatkowych danych w każdym tokenie.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-313">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="1d1e6-314">Metoda [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) jest wywoływana za każdym razem, gdy generowany jest token pola, a zwracana wartość jest osadzona w wygenerowanym tokenie.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-314">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="1d1e6-315">Realizator może zwrócić sygnaturę czasową, identyfikator jednorazowy lub inną wartość, a następnie wywołać [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) , aby zweryfikować te dane po sprawdzeniu poprawności tokenu.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-315">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="1d1e6-316">Nazwa użytkownika klienta jest już osadzona w wygenerowanych tokenach, więc nie trzeba uwzględniać tych informacji.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-316">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="1d1e6-317">Jeśli token zawiera dane uzupełniające, ale nie `IAntiForgeryAdditionalDataProvider` jest skonfigurowany, dane uzupełniające nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="1d1e6-317">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d1e6-318">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1d1e6-318">Additional resources</span></span>

* <span data-ttu-id="1d1e6-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) w [programie Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="1d1e6-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>

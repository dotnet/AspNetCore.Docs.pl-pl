---
title: Omówienie uwierzytelniania ASP.NET Core
author: mjrousos
description: Informacje o uwierzytelnianiu w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
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
uid: security/authentication/index
ms.openlocfilehash: 2511d68dd049ca47dea002d8960e7d2fd0207383
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632645"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="2fa74-103">Omówienie uwierzytelniania ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fa74-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="2fa74-104">Według [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="2fa74-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="2fa74-105">Uwierzytelnianie to proces określania tożsamości użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2fa74-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="2fa74-106">[Autoryzacja](xref:security/authorization/introduction) to proces ustalania, czy użytkownik ma dostęp do zasobu.</span><span class="sxs-lookup"><span data-stu-id="2fa74-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="2fa74-107">W ASP.NET Core uwierzytelnianie jest obsługiwane przez `IAuthenticationService` program, który jest używany przez [oprogramowanie pośredniczące](xref:fundamentals/middleware/index)uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="2fa74-108">Usługa uwierzytelniania używa zarejestrowanych programów obsługi uwierzytelniania do ukończenia akcji związanych z uwierzytelnianiem.</span><span class="sxs-lookup"><span data-stu-id="2fa74-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="2fa74-109">Przykłady akcji związanych z uwierzytelnianiem obejmują:</span><span class="sxs-lookup"><span data-stu-id="2fa74-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="2fa74-110">Uwierzytelnianie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2fa74-110">Authenticating a user.</span></span>
* <span data-ttu-id="2fa74-111">Odpowiada, gdy nieuwierzytelniony użytkownik próbuje uzyskać dostęp do zasobu z ograniczeniami.</span><span class="sxs-lookup"><span data-stu-id="2fa74-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="2fa74-112">Zarejestrowane programy obsługi uwierzytelniania i ich opcje konfiguracji są nazywane "schematami".</span><span class="sxs-lookup"><span data-stu-id="2fa74-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="2fa74-113">Schematy uwierzytelniania są określane przez zarejestrowanie usług uwierzytelniania w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fa74-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="2fa74-114">Wywołując metodę rozszerzenia specyficzną dla schematu po wywołaniu metody (np `services.AddAuthentication` `AddJwtBearer` `AddCookie` . lub, na przykład).</span><span class="sxs-lookup"><span data-stu-id="2fa74-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="2fa74-115">Te metody rozszerzające używają [AuthenticationBuilder. AddSchema](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) do rejestrowania schematów przy użyciu odpowiednich ustawień.</span><span class="sxs-lookup"><span data-stu-id="2fa74-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="2fa74-116">Rzadziej, przez wywołanie [AuthenticationBuilder. AddSchema](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="2fa74-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="2fa74-117">Na przykład poniższy kod rejestruje usługi uwierzytelniania i programy obsługi programu cookie oraz schematy uwierzytelniania okaziciela JWT:</span><span class="sxs-lookup"><span data-stu-id="2fa74-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="2fa74-118">`AddAuthentication`Parametr `JwtBearerDefaults.AuthenticationScheme` jest nazwą schematu, który ma być używany domyślnie, gdy określony schemat nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="2fa74-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="2fa74-119">W przypadku użycia wielu schematów zasady autoryzacji (lub atrybuty autoryzacji) mogą [określać schemat uwierzytelniania (lub schematy),](xref:security/authorization/limitingidentitybyscheme) od których zależą w celu uwierzytelnienia użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2fa74-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="2fa74-120">W powyższym przykładzie cookie schemat uwierzytelniania może być używany przez określenie jego nazwy ( `CookieAuthenticationDefaults.AuthenticationScheme` Domyślnie, chociaż przy wywoływaniu można podać inną nazwę `AddCookie` ).</span><span class="sxs-lookup"><span data-stu-id="2fa74-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="2fa74-121">W niektórych przypadkach wywołanie `AddAuthentication` jest wykonywane automatycznie przez inne metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2fa74-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="2fa74-122">Na przykład podczas korzystania z [ASP.NET Core Identity](xref:security/authentication/identity) programu `AddAuthentication` jest wywoływana wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="2fa74-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="2fa74-123">Oprogramowanie pośredniczące uwierzytelniania jest dodawane w programie `Startup.Configure` przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> metody rozszerzenia w aplikacji `IApplicationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2fa74-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="2fa74-124">Wywołanie `UseAuthentication` rejestruje oprogramowanie pośredniczące, które używa poprzednio zarejestrowanego schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="2fa74-125">Wywołaj `UseAuthentication` przed każdym oprogramowanie pośredniczące zależne od użytkowników, którzy są uwierzytelniani.</span><span class="sxs-lookup"><span data-stu-id="2fa74-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="2fa74-126">W przypadku korzystania z routingu punktów końcowych wywołanie `UseAuthentication` musi być następujące:</span><span class="sxs-lookup"><span data-stu-id="2fa74-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="2fa74-127">Po `UseRouting` , tak aby informacje o trasie były dostępne na potrzeby podejmowania decyzji dotyczących uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="2fa74-128">Wcześniej `UseEndpoints` , aby użytkownicy mieli uwierzytelnieni przed uzyskaniem dostępu do punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="2fa74-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="2fa74-129">Pojęcia związane z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="2fa74-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="2fa74-130">Schemat uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2fa74-130">Authentication scheme</span></span>

<span data-ttu-id="2fa74-131">Schematem uwierzytelniania jest nazwa, która odnosi się do:</span><span class="sxs-lookup"><span data-stu-id="2fa74-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="2fa74-132">Procedura obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-132">An authentication handler.</span></span>
* <span data-ttu-id="2fa74-133">Opcje konfigurowania określonego wystąpienia programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="2fa74-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="2fa74-134">Schematy są przydatne jako mechanizm do odwoływania się do zachowań uwierzytelnianie, wyzwanie i Zabroń skojarzonej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="2fa74-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="2fa74-135">Na przykład zasady autoryzacji mogą używać nazw schematów, aby określić, który schemat uwierzytelniania (lub schematy) powinien zostać użyty do uwierzytelnienia użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2fa74-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="2fa74-136">Podczas konfigurowania uwierzytelniania często można określić domyślny schemat uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="2fa74-137">Domyślny schemat jest używany, chyba że zasób żąda określonego schematu.</span><span class="sxs-lookup"><span data-stu-id="2fa74-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="2fa74-138">Możliwe jest również:</span><span class="sxs-lookup"><span data-stu-id="2fa74-138">It's also possible to:</span></span>

* <span data-ttu-id="2fa74-139">Określ różne domyślne schematy do użycia w akcjach uwierzytelniania, wyzwania i zabraniania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="2fa74-140">Połącz wiele schematów przy użyciu [schematów zasad](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="2fa74-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="2fa74-141">Procedura obsługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2fa74-141">Authentication handler</span></span>

<span data-ttu-id="2fa74-142">Procedura obsługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="2fa74-142">An authentication handler:</span></span>

* <span data-ttu-id="2fa74-143">Jest typem, który implementuje zachowanie schematu.</span><span class="sxs-lookup"><span data-stu-id="2fa74-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="2fa74-144">Jest pochodną <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> lub <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="2fa74-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="2fa74-145">Ma główną odpowiedzialność za uwierzytelnianie użytkowników.</span><span class="sxs-lookup"><span data-stu-id="2fa74-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="2fa74-146">W oparciu o konfigurację schematu uwierzytelniania i kontekst żądania przychodzącego, programy obsługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="2fa74-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="2fa74-147">Konstruowanie <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> obiektów reprezentujących tożsamość użytkownika w przypadku pomyślnego uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="2fa74-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="2fa74-148">Zwróć "Brak wyniku" lub "Niepowodzenie", jeśli uwierzytelnianie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="2fa74-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="2fa74-149">Mają metody dla akcji wyzwania i Zabraniaj, gdy użytkownicy próbują uzyskać dostęp do zasobów:</span><span class="sxs-lookup"><span data-stu-id="2fa74-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="2fa74-150">Dostęp do nich nie jest autoryzowany (Zabroń).</span><span class="sxs-lookup"><span data-stu-id="2fa74-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="2fa74-151">Gdy nie są uwierzytelniane (wyzwanie).</span><span class="sxs-lookup"><span data-stu-id="2fa74-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="2fa74-152">Uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="2fa74-152">Authenticate</span></span>

<span data-ttu-id="2fa74-153">Akcja uwierzytelniania schematu uwierzytelniania jest odpowiedzialna za konstruowanie tożsamości użytkownika na podstawie kontekstu żądania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="2fa74-154">Zwraca <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> informację o tym, czy uwierzytelnianie zakończyło się pomyślnie, a jeśli tak, tożsamość użytkownika w biletu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="2fa74-155">Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="2fa74-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="2fa74-156">Przykłady uwierzytelniania obejmują:</span><span class="sxs-lookup"><span data-stu-id="2fa74-156">Authenticate examples include:</span></span>

* <span data-ttu-id="2fa74-157">cookieSchemat uwierzytelniania, który konstruuje tożsamość użytkownika z cookie s.</span><span class="sxs-lookup"><span data-stu-id="2fa74-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="2fa74-158">Schemat okaziciela JWT deserializacji i weryfikacji tokenu okaziciela JWT w celu utworzenia tożsamości użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2fa74-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="2fa74-159">Zadanie</span><span class="sxs-lookup"><span data-stu-id="2fa74-159">Challenge</span></span>

<span data-ttu-id="2fa74-160">Wyzwanie uwierzytelniania jest wywoływane przez autoryzację, gdy nieuwierzytelniony użytkownik żąda punktu końcowego wymagającego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="2fa74-161">Jest wystawiane wyzwanie uwierzytelniania, na przykład gdy użytkownik anonimowy żąda zasobu z ograniczeniami lub klika łącze logowania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="2fa74-162">Autoryzacja wywołuje wyzwanie przy użyciu określonych schematów uwierzytelniania lub wartość domyślną, jeśli nie została określona.</span><span class="sxs-lookup"><span data-stu-id="2fa74-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="2fa74-163">Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="2fa74-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="2fa74-164">Przykłady wyzwania uwierzytelniania obejmują:</span><span class="sxs-lookup"><span data-stu-id="2fa74-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="2fa74-165">cookieSchemat uwierzytelniania przekierowuje użytkownika do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="2fa74-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="2fa74-166">Schemat okaziciela JWT zwracający wynik 401 z `www-authenticate: bearer` nagłówkiem.</span><span class="sxs-lookup"><span data-stu-id="2fa74-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="2fa74-167">Akcja wyzwania powinna dać użytkownikowi informacje o mechanizmie uwierzytelniania używanym do uzyskiwania dostępu do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="2fa74-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="2fa74-168">Uniemożliwia</span><span class="sxs-lookup"><span data-stu-id="2fa74-168">Forbid</span></span>

<span data-ttu-id="2fa74-169">Akcja zabraniania schematu uwierzytelniania jest wywoływana przez autoryzację, gdy uwierzytelniony użytkownik próbuje uzyskać dostęp do zasobu, do którego nie ma dostępu.</span><span class="sxs-lookup"><span data-stu-id="2fa74-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="2fa74-170">Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="2fa74-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="2fa74-171">Przykładem zabraniania uwierzytelniania są:</span><span class="sxs-lookup"><span data-stu-id="2fa74-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="2fa74-172">cookieSchemat uwierzytelniania przekierowuje użytkownika do strony wskazującej dostęp był zabroniony.</span><span class="sxs-lookup"><span data-stu-id="2fa74-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="2fa74-173">Schemat okaziciela JWT zwracający wynik 403.</span><span class="sxs-lookup"><span data-stu-id="2fa74-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="2fa74-174">Niestandardowy schemat uwierzytelniania przekierowuje do strony, na której użytkownik może zażądać dostępu do zasobu.</span><span class="sxs-lookup"><span data-stu-id="2fa74-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="2fa74-175">Akcja Zabroń może pozwolić użytkownikowi na:</span><span class="sxs-lookup"><span data-stu-id="2fa74-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="2fa74-176">Są uwierzytelniane.</span><span class="sxs-lookup"><span data-stu-id="2fa74-176">They are authenticated.</span></span>
* <span data-ttu-id="2fa74-177">Nie mogą uzyskać dostępu do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="2fa74-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="2fa74-178">Poniżej znajdują się linki dotyczące różnic między wyzwaniem i zabranianiem:</span><span class="sxs-lookup"><span data-stu-id="2fa74-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="2fa74-179">[Wyzwania i Zabroń z obsługą zasobów operacyjnych](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="2fa74-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="2fa74-180">[Różnice między wyzwaniem i zabranianiem](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="2fa74-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="2fa74-181">Dostawcy uwierzytelniania na dzierżawcę</span><span class="sxs-lookup"><span data-stu-id="2fa74-181">Authentication providers per tenant</span></span>

<span data-ttu-id="2fa74-182">Platforma ASP.NET Core nie ma wbudowanego rozwiązania do uwierzytelniania wielodostępnego.</span><span class="sxs-lookup"><span data-stu-id="2fa74-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="2fa74-183">Chociaż jest to możliwe, aby klienci mogli pisać jedną, przy użyciu wbudowanych funkcji, zalecamy, aby w tym celu przyjrzeć się klientom na [rdzeń](https://www.orchardcore.net/) .</span><span class="sxs-lookup"><span data-stu-id="2fa74-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="2fa74-184">Rdzeń sadu:</span><span class="sxs-lookup"><span data-stu-id="2fa74-184">Orchard Core is:</span></span>

* <span data-ttu-id="2fa74-185">Modularna i wielodostępna platforma aplikacji Open Source skompilowana przy użyciu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fa74-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="2fa74-186">System zarządzania zawartością (CMS) zbudowany na podstawie tej struktury aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2fa74-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="2fa74-187">Zobacz [podstawowe źródło sadu](https://github.com/OrchardCMS/OrchardCore) dla przykładu dostawców uwierzytelniania na dzierżawcę.</span><span class="sxs-lookup"><span data-stu-id="2fa74-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fa74-188">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2fa74-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="2fa74-189">Globalnie Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="2fa74-189">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)
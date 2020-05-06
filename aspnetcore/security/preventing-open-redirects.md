---
title: Zapobiegaj atakom typu "Open redirect" w ASP.NET Core
author: ardalis
description: Pokazuje, jak zapobiec atakom typu "Open redirect" w aplikacji ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: ad4c9806146567b6ef1f5e78eaeca96cb649c1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774395"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="3a531-103">Zapobiegaj atakom typu "Open redirect" w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a531-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="3a531-104">Aplikacja sieci Web, która przekierowuje do adresu URL, który jest określony za pośrednictwem żądania, takiego jak QueryString lub dane formularza, może zostać naruszona w celu przekierowania użytkowników do zewnętrznego, złośliwego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3a531-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="3a531-105">Takie manipulowanie nazywa się atakiem typu Open przekierowania.</span><span class="sxs-lookup"><span data-stu-id="3a531-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="3a531-106">Za każdym razem, gdy logika aplikacji przekieruje do określonego adresu URL, należy sprawdzić, czy adres URL przekierowania nie został naruszony.</span><span class="sxs-lookup"><span data-stu-id="3a531-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="3a531-107">ASP.NET Core ma wbudowaną funkcję ułatwiającą ochronę aplikacji przed atakami typu Open redirect (nazywanymi także otwartym przekierowaniem).</span><span class="sxs-lookup"><span data-stu-id="3a531-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="3a531-108">Co to jest atak typu "Open redirect"?</span><span class="sxs-lookup"><span data-stu-id="3a531-108">What is an open redirect attack?</span></span>

<span data-ttu-id="3a531-109">Aplikacje sieci Web często przekierowują użytkowników na stronę logowania, gdy uzyskują dostęp do zasobów wymagających uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="3a531-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="3a531-110">Przekierowania zwykle zawiera parametr `returnUrl` QueryString, dzięki czemu użytkownik może zostać zwrócony do pierwotnie żądanego adresu URL po pomyślnym zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="3a531-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="3a531-111">Po uwierzytelnieniu użytkownik zostanie przekierowany do adresu URL, na który pierwotnie żądał.</span><span class="sxs-lookup"><span data-stu-id="3a531-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="3a531-112">Ponieważ docelowy adres URL jest określony w ciągu kwerendy żądania, złośliwy użytkownik może naruszać ten ciąg.</span><span class="sxs-lookup"><span data-stu-id="3a531-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="3a531-113">Ciąg QueryString z naruszonymi komunikatami może pozwolić witrynie na przekierowanie użytkownika do zewnętrznej, szkodliwej lokacji.</span><span class="sxs-lookup"><span data-stu-id="3a531-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="3a531-114">Ta technika jest nazywana atakiem typu Open redirect (lub przekierowaniem).</span><span class="sxs-lookup"><span data-stu-id="3a531-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="3a531-115">Przykład ataku</span><span class="sxs-lookup"><span data-stu-id="3a531-115">An example attack</span></span>

<span data-ttu-id="3a531-116">Złośliwy użytkownik może stworzyć atak przeznaczony do zezwalania złośliwemu użytkownikowi na dostęp do poświadczeń lub informacji poufnych.</span><span class="sxs-lookup"><span data-stu-id="3a531-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="3a531-117">Aby rozpocząć atak, złośliwy użytkownik zakończył pracę użytkownika w celu kliknięcia linku do strony logowania do witryny z wartością `returnUrl` QueryString, która została dodana do adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3a531-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="3a531-118">Rozważmy na przykład aplikację, `contoso.com` która obejmuje stronę logowania w witrynie. `http://contoso.com/Account/LogOn?returnUrl=/Home/About`</span><span class="sxs-lookup"><span data-stu-id="3a531-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="3a531-119">Atakujący wykonuje następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="3a531-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="3a531-120">Użytkownik klika złośliwe łącze do `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (drugi adres URL to "contoso**1**. com", a nie "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="3a531-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="3a531-121">Logowanie użytkownika zakończyło się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="3a531-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="3a531-122">Użytkownik zostanie przekierowany (przez lokację) do `http://contoso1.com/Account/LogOn` programu (złośliwa lokacja, która wygląda tak samo jak prawdziwa witryna).</span><span class="sxs-lookup"><span data-stu-id="3a531-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="3a531-123">Użytkownik loguje się ponownie (podając złośliwe witryny jako poświadczenia) i zostaje przekierowany z powrotem do rzeczywistej lokacji.</span><span class="sxs-lookup"><span data-stu-id="3a531-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="3a531-124">Użytkownik prawdopodobnie zauważa, że pierwsza próba zalogowania nie powiodła się, a druga próba zakończyła się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="3a531-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="3a531-125">Użytkownik najprawdopodobniej nie będzie świadomy naruszenia bezpieczeństwa poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="3a531-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Proces ataku typu Otwórz przekierowanie](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="3a531-127">Oprócz stron logowania niektóre lokacje udostępniają strony lub punkty końcowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="3a531-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="3a531-128">Wyobraź sobie, `/Home/Redirect`że aplikacja zawiera stronę z otwartym przekierowaniem.</span><span class="sxs-lookup"><span data-stu-id="3a531-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="3a531-129">Osoba atakująca może utworzyć na przykład link w wiadomości e-mail `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span><span class="sxs-lookup"><span data-stu-id="3a531-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="3a531-130">Typowy użytkownik zobaczy adres URL i rozpocznie się po jego nazwie.</span><span class="sxs-lookup"><span data-stu-id="3a531-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="3a531-131">Zaufanie to spowoduje kliknięcie linku.</span><span class="sxs-lookup"><span data-stu-id="3a531-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="3a531-132">Otwarte przekierowanie spowoduje następnie wysłanie użytkownika do witryny wyłudzania informacji, która wygląda identycznie, a użytkownik może zalogować się do Twojej witryny.</span><span class="sxs-lookup"><span data-stu-id="3a531-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="3a531-133">Ochrona przed atakami typu Open redirect</span><span class="sxs-lookup"><span data-stu-id="3a531-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="3a531-134">Podczas tworzenia aplikacji sieci Web Traktuj wszystkie dane dostarczone przez użytkownika jako niezaufane.</span><span class="sxs-lookup"><span data-stu-id="3a531-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="3a531-135">Jeśli aplikacja zawiera funkcję, która przekierowuje użytkownika na podstawie zawartości adresu URL, należy się upewnić, że takie przekierowania są wykonywane tylko lokalnie w aplikacji (lub na znanym adresie URL, nie na żadnym z adresów URL, które mogą być dostarczone w ciągu QueryString).</span><span class="sxs-lookup"><span data-stu-id="3a531-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="3a531-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="3a531-136">LocalRedirect</span></span>

<span data-ttu-id="3a531-137">Użyj metody `LocalRedirect` pomocnika z klasy bazowej `Controller` :</span><span class="sxs-lookup"><span data-stu-id="3a531-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="3a531-138">`LocalRedirect`zgłasza wyjątek w przypadku określenia nielokalnego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3a531-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="3a531-139">W przeciwnym razie zachowuje się tak samo jak `Redirect` Metoda.</span><span class="sxs-lookup"><span data-stu-id="3a531-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="3a531-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="3a531-140">IsLocalUrl</span></span>

<span data-ttu-id="3a531-141">Użyj metody [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) , aby przetestować adresy URL przed przekierowaniem:</span><span class="sxs-lookup"><span data-stu-id="3a531-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="3a531-142">Poniższy przykład pokazuje, jak sprawdzić, czy adres URL jest lokalny przed przekierowaniem.</span><span class="sxs-lookup"><span data-stu-id="3a531-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="3a531-143">`IsLocalUrl` Metoda chroni użytkowników przed przypadkowym przekierowaniem do złośliwej witryny.</span><span class="sxs-lookup"><span data-stu-id="3a531-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="3a531-144">Można rejestrować szczegółowe informacje o adresie URL, który został podany w przypadku podania nielokalnego adresu URL w sytuacji, w której oczekiwano lokalnego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3a531-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="3a531-145">Adresy URL przekierowania rejestrowania mogą pomóc w diagnozowaniu ataków przekierowania.</span><span class="sxs-lookup"><span data-stu-id="3a531-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>

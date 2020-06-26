---
title: Pomocnicy tagów w formularzach w ASP.NET Core
author: rick-anderson
description: Opisuje wbudowane pomocnicy tagów używane z formularzami.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/working-with-forms
ms.openlocfilehash: 06d5d6e2db1cf3ccb148c24c40c264016d47a0ed
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406592"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="bf43a-103">Pomocnicy tagów w formularzach w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf43a-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="bf43a-104">Autor [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette)i [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="bf43a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="bf43a-105">Ten dokument przedstawia pracę z formularzami i elementami HTML często używanymi w formularzu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="bf43a-106">Element HTML [form](https://www.w3.org/TR/html401/interact/forms.html) zawiera podstawowe mechanizmy aplikacje sieci Web używane do publikowania danych na serwerze.</span><span class="sxs-lookup"><span data-stu-id="bf43a-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="bf43a-107">W większości tego dokumentu opisano [pomocników tagów](tag-helpers/intro.md) i sposób, w jaki mogą one pomóc w produktywnym tworzeniu niezawodnych formularzy HTML.</span><span class="sxs-lookup"><span data-stu-id="bf43a-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="bf43a-108">Zalecamy zapoznanie [się z wprowadzeniem do pomocników tagów](tag-helpers/intro.md) przed przeczytaniem tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="bf43a-109">W wielu przypadkach pomocników HTML udostępniają alternatywne podejście do osoby pomagającej z określonym znacznikiem, ale ważne jest, aby rozpoznać, że pomocnicy tagów nie zastępują pomocników HTML i nie ma pomocy dla tagów dla każdego pomocnika HTML.</span><span class="sxs-lookup"><span data-stu-id="bf43a-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="bf43a-110">Gdy istnieje alternatywa dla pomocnika HTML, jest ona wspomniana.</span><span class="sxs-lookup"><span data-stu-id="bf43a-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="bf43a-111">Pomocnik tagów formularza</span><span class="sxs-lookup"><span data-stu-id="bf43a-111">The Form Tag Helper</span></span>

<span data-ttu-id="bf43a-112">Pomocnik tagu [formularza](https://www.w3.org/TR/html401/interact/forms.html) :</span><span class="sxs-lookup"><span data-stu-id="bf43a-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="bf43a-113">Generuje [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` wartość atrybutu HTML dla akcji kontrolera MVC lub nazwanej trasy</span><span class="sxs-lookup"><span data-stu-id="bf43a-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="bf43a-114">Generuje [token weryfikacji ukrytego żądania](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , aby zapobiec fałszerstwu żądania między lokacjami (gdy jest używany z `[ValidateAntiForgeryToken]` atrybutem w metodzie akcji post protokołu HTTP).</span><span class="sxs-lookup"><span data-stu-id="bf43a-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="bf43a-115">Udostępnia `asp-route-<Parameter Name>` atrybut, gdzie `<Parameter Name>` jest dodawany do wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="bf43a-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="bf43a-116">`routeValues`Parametry do `Html.BeginForm` i `Html.BeginRouteForm` zapewniają podobną funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="bf43a-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="bf43a-117">Ma alternatywę pomocnika HTML `Html.BeginForm` i`Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="bf43a-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="bf43a-118">Przykład:</span><span class="sxs-lookup"><span data-stu-id="bf43a-118">Sample:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="bf43a-119">Pomocnik tagu formularza powyżej generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-119">The Form Tag Helper above generates the following HTML:</span></span>

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="bf43a-120">Środowisko uruchomieniowe MVC generuje `action` wartość atrybutu na podstawie atrybutów pomocnika tagów `asp-controller` i `asp-action` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="bf43a-121">Pomocnik tagu formularza generuje również [token weryfikacji ukrytego żądania](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , aby zapobiec fałszerstwu żądania między lokacjami (gdy jest używany z `[ValidateAntiForgeryToken]` atrybutem w metodzie akcji post protokołu HTTP).</span><span class="sxs-lookup"><span data-stu-id="bf43a-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="bf43a-122">Ochrona czystej formy HTML z poziomu fałszerstwa żądań między lokacjami jest trudna, ponieważ pomocnik tagów formularza udostępnia tę usługę.</span><span class="sxs-lookup"><span data-stu-id="bf43a-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="bf43a-123">Używanie nazwanej trasy</span><span class="sxs-lookup"><span data-stu-id="bf43a-123">Using a named route</span></span>

<span data-ttu-id="bf43a-124">`asp-route`Atrybut pomocnika tagów może również generować znaczniki dla atrybutu HTML `action` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="bf43a-125">Aplikacja o [trasie](../../fundamentals/routing.md) o nazwie `register` może korzystać z następującego znacznika na stronie rejestracji:</span><span class="sxs-lookup"><span data-stu-id="bf43a-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="bf43a-126">Wiele widoków w folderze *widoki/konto* (generowane podczas tworzenia nowej aplikacji sieci Web przy użyciu *poszczególnych kont użytkowników*) zawiera atrybut [ASP-Route-ReturnUrl](xref:mvc/views/working-with-forms) :</span><span class="sxs-lookup"><span data-stu-id="bf43a-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="bf43a-127">Przy użyciu wbudowanych szablonów `returnUrl` jest wypełniany automatycznie tylko wtedy, gdy użytkownik próbuje uzyskać dostęp do autoryzowanego zasobu, ale nie jest uwierzytelniany ani autoryzowany.</span><span class="sxs-lookup"><span data-stu-id="bf43a-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="bf43a-128">W przypadku próby uzyskania nieautoryzowanego dostępu oprogramowanie pośredniczące zabezpieczeń przekierowuje użytkownika do strony logowania z `returnUrl` zestawem.</span><span class="sxs-lookup"><span data-stu-id="bf43a-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="bf43a-129">Pomocnik tagów akcji formularza</span><span class="sxs-lookup"><span data-stu-id="bf43a-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="bf43a-130">Pomocnik tagów akcji formularza generuje `formaction` atrybut dla wygenerowanego `<button ...>` tagu or `<input type="image" ...>` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="bf43a-131">`formaction`Atrybut określa, w którym formularzu są przesyłane dane.</span><span class="sxs-lookup"><span data-stu-id="bf43a-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="bf43a-132">Tworzy powiązanie z [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elementami typu `image` i [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elementów.</span><span class="sxs-lookup"><span data-stu-id="bf43a-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="bf43a-133">Pomocnik tagów akcji formularza umożliwia użycie kilku atrybutów [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` do kontrolowania tego, jakie `formaction` łącze jest generowane dla odpowiedniego elementu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="bf43a-134">Obsługiwane atrybuty [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) do sterowania wartością `formaction` :</span><span class="sxs-lookup"><span data-stu-id="bf43a-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="bf43a-135">Atrybut</span><span class="sxs-lookup"><span data-stu-id="bf43a-135">Attribute</span></span>|<span data-ttu-id="bf43a-136">Opis</span><span class="sxs-lookup"><span data-stu-id="bf43a-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="bf43a-137">ASP-Controller</span><span class="sxs-lookup"><span data-stu-id="bf43a-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="bf43a-138">Nazwa kontrolera.</span><span class="sxs-lookup"><span data-stu-id="bf43a-138">The name of the controller.</span></span>|
|[<span data-ttu-id="bf43a-139">ASP — akcja</span><span class="sxs-lookup"><span data-stu-id="bf43a-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="bf43a-140">Nazwa metody akcji.</span><span class="sxs-lookup"><span data-stu-id="bf43a-140">The name of the action method.</span></span>|
|[<span data-ttu-id="bf43a-141">obszar ASP</span><span class="sxs-lookup"><span data-stu-id="bf43a-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="bf43a-142">Nazwa obszaru.</span><span class="sxs-lookup"><span data-stu-id="bf43a-142">The name of the area.</span></span>|
|[<span data-ttu-id="bf43a-143">ASP — Strona</span><span class="sxs-lookup"><span data-stu-id="bf43a-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="bf43a-144">Nazwa Razor strony.</span><span class="sxs-lookup"><span data-stu-id="bf43a-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="bf43a-145">ASP — obsługa stron</span><span class="sxs-lookup"><span data-stu-id="bf43a-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="bf43a-146">Nazwa Razor procedury obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="bf43a-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="bf43a-147">ASP — Route</span><span class="sxs-lookup"><span data-stu-id="bf43a-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="bf43a-148">Nazwa trasy.</span><span class="sxs-lookup"><span data-stu-id="bf43a-148">The name of the route.</span></span>|
|[<span data-ttu-id="bf43a-149">ASP-Route-{Value}</span><span class="sxs-lookup"><span data-stu-id="bf43a-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="bf43a-150">Wartość trasy pojedynczego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bf43a-150">A single URL route value.</span></span> <span data-ttu-id="bf43a-151">Na przykład `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="bf43a-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="bf43a-152">ASP — wszystkie trasy — dane</span><span class="sxs-lookup"><span data-stu-id="bf43a-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="bf43a-153">Wszystkie wartości trasy.</span><span class="sxs-lookup"><span data-stu-id="bf43a-153">All route values.</span></span>|
|[<span data-ttu-id="bf43a-154">ASP — fragment</span><span class="sxs-lookup"><span data-stu-id="bf43a-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="bf43a-155">Fragment adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bf43a-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="bf43a-156">Przykład przesłania do kontrolera</span><span class="sxs-lookup"><span data-stu-id="bf43a-156">Submit to controller example</span></span>

<span data-ttu-id="bf43a-157">Następujące znaczniki przesłaniają formularz do `Index` akcji, `HomeController` gdy wybrane jest wejście lub przycisk:</span><span class="sxs-lookup"><span data-stu-id="bf43a-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="bf43a-158">Poprzednia Adiustacja generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="bf43a-159">Prześlij do przykładowej strony</span><span class="sxs-lookup"><span data-stu-id="bf43a-159">Submit to page example</span></span>

<span data-ttu-id="bf43a-160">Następujący znak przesłania formularz do `About` Razor strony:</span><span class="sxs-lookup"><span data-stu-id="bf43a-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="bf43a-161">Poprzednia Adiustacja generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="bf43a-162">Prześlij do przykładu trasy</span><span class="sxs-lookup"><span data-stu-id="bf43a-162">Submit to route example</span></span>

<span data-ttu-id="bf43a-163">Rozważ użycie `/Home/Test` punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="bf43a-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="bf43a-164">Poniższe znaczniki przesłaniają formularz do `/Home/Test` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="bf43a-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="bf43a-165">Poprzednia Adiustacja generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="bf43a-166">Pomocnik tagu wejściowego</span><span class="sxs-lookup"><span data-stu-id="bf43a-166">The Input Tag Helper</span></span>

<span data-ttu-id="bf43a-167">Pomocnik tagu wejściowego tworzy powiązanie elementu HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) z wyrażeniem modelu w widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="bf43a-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="bf43a-168">Składnia:</span><span class="sxs-lookup"><span data-stu-id="bf43a-168">Syntax:</span></span>

```cshtml
<input asp-for="<Expression Name>">
```

<span data-ttu-id="bf43a-169">Pomocnik tagu wejściowego:</span><span class="sxs-lookup"><span data-stu-id="bf43a-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="bf43a-170">Generuje `id` `name` atrybuty HTML dla nazwy wyrażenia określonego w `asp-for` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="bf43a-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="bf43a-171">`asp-for="Property1.Property2"`jest odpowiednikiem `m => m.Property1.Property2` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="bf43a-172">Nazwa wyrażenia jest używana jako `asp-for` wartość atrybutu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="bf43a-173">Aby uzyskać dodatkowe informacje, zobacz sekcję [nazwy wyrażeń](#expression-names) .</span><span class="sxs-lookup"><span data-stu-id="bf43a-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="bf43a-174">Ustawia `type` wartość atrybutu HTML na podstawie atrybutów typu modelu i [adnotacji danych](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) zastosowanych do właściwości model</span><span class="sxs-lookup"><span data-stu-id="bf43a-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="bf43a-175">Nie zastępuje `type` wartości atrybutu HTML, gdy jest określony</span><span class="sxs-lookup"><span data-stu-id="bf43a-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="bf43a-176">Generuje atrybuty walidacji [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) z atrybutów [adnotacji danych](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) zastosowanych do właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="bf43a-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="bf43a-177">Ma funkcję pomocnika HTML, która pokrywa się z `Html.TextBoxFor` i `Html.EditorFor` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="bf43a-178">Szczegóły można znaleźć w sekcji pomocnik **HTML — alternatywy dla tagów wejściowych** .</span><span class="sxs-lookup"><span data-stu-id="bf43a-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="bf43a-179">Zapewnia silne wpisywanie.</span><span class="sxs-lookup"><span data-stu-id="bf43a-179">Provides strong typing.</span></span> <span data-ttu-id="bf43a-180">Jeśli nazwa właściwości ulegnie zmianie i nie zostanie zaktualizowany pomocnika tagów, zostanie wyświetlony komunikat o błędzie podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="bf43a-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="bf43a-181">`Input`Pomocnik tagów ustawia ATRYBUT HTML w `type` oparciu o typ .NET.</span><span class="sxs-lookup"><span data-stu-id="bf43a-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="bf43a-182">W poniższej tabeli wymieniono niektóre typowe typy .NET i wygenerowany typ HTML (nie każdy typ .NET jest wyświetlany).</span><span class="sxs-lookup"><span data-stu-id="bf43a-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="bf43a-183">Typ .NET</span><span class="sxs-lookup"><span data-stu-id="bf43a-183">.NET type</span></span>|<span data-ttu-id="bf43a-184">Typ danych wejściowych</span><span class="sxs-lookup"><span data-stu-id="bf43a-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="bf43a-185">Wartość logiczna</span><span class="sxs-lookup"><span data-stu-id="bf43a-185">Bool</span></span>|<span data-ttu-id="bf43a-186">Type = "CheckBox"</span><span class="sxs-lookup"><span data-stu-id="bf43a-186">type="checkbox"</span></span>|
|<span data-ttu-id="bf43a-187">Ciąg</span><span class="sxs-lookup"><span data-stu-id="bf43a-187">String</span></span>|<span data-ttu-id="bf43a-188">Type = "text"</span><span class="sxs-lookup"><span data-stu-id="bf43a-188">type="text"</span></span>|
|<span data-ttu-id="bf43a-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="bf43a-189">DateTime</span></span>|<span data-ttu-id="bf43a-190">Type =["DateTime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="bf43a-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="bf43a-191">Byte</span><span class="sxs-lookup"><span data-stu-id="bf43a-191">Byte</span></span>|<span data-ttu-id="bf43a-192">Type = "number"</span><span class="sxs-lookup"><span data-stu-id="bf43a-192">type="number"</span></span>|
|<span data-ttu-id="bf43a-193">int</span><span class="sxs-lookup"><span data-stu-id="bf43a-193">Int</span></span>|<span data-ttu-id="bf43a-194">Type = "number"</span><span class="sxs-lookup"><span data-stu-id="bf43a-194">type="number"</span></span>|
|<span data-ttu-id="bf43a-195">Pojedyncza, Podwójna</span><span class="sxs-lookup"><span data-stu-id="bf43a-195">Single, Double</span></span>|<span data-ttu-id="bf43a-196">Type = "number"</span><span class="sxs-lookup"><span data-stu-id="bf43a-196">type="number"</span></span>|

<span data-ttu-id="bf43a-197">W poniższej tabeli przedstawiono niektóre typowe atrybuty [adnotacji danych](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) , które pomocnik tagów wejściowych będzie mapowany do określonych typów danych wejściowych (nie każdy atrybut walidacji jest wymieniony):</span><span class="sxs-lookup"><span data-stu-id="bf43a-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="bf43a-198">Atrybut</span><span class="sxs-lookup"><span data-stu-id="bf43a-198">Attribute</span></span>|<span data-ttu-id="bf43a-199">Typ danych wejściowych</span><span class="sxs-lookup"><span data-stu-id="bf43a-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="bf43a-200">EmailAddress</span><span class="sxs-lookup"><span data-stu-id="bf43a-200">[EmailAddress]</span></span>|<span data-ttu-id="bf43a-201">Type = "e-mail"</span><span class="sxs-lookup"><span data-stu-id="bf43a-201">type="email"</span></span>|
|<span data-ttu-id="bf43a-202">[URL]</span><span class="sxs-lookup"><span data-stu-id="bf43a-202">[Url]</span></span>|<span data-ttu-id="bf43a-203">Type = "URL"</span><span class="sxs-lookup"><span data-stu-id="bf43a-203">type="url"</span></span>|
|<span data-ttu-id="bf43a-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="bf43a-204">[HiddenInput]</span></span>|<span data-ttu-id="bf43a-205">Type = "Hidden"</span><span class="sxs-lookup"><span data-stu-id="bf43a-205">type="hidden"</span></span>|
|<span data-ttu-id="bf43a-206">Połączenia</span><span class="sxs-lookup"><span data-stu-id="bf43a-206">[Phone]</span></span>|<span data-ttu-id="bf43a-207">Type = "Tel"</span><span class="sxs-lookup"><span data-stu-id="bf43a-207">type="tel"</span></span>|
|<span data-ttu-id="bf43a-208">[DataType (DataType. Password)]</span><span class="sxs-lookup"><span data-stu-id="bf43a-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="bf43a-209">Type = "Password"</span><span class="sxs-lookup"><span data-stu-id="bf43a-209">type="password"</span></span>|
|<span data-ttu-id="bf43a-210">[DataType (DataType. Date)]</span><span class="sxs-lookup"><span data-stu-id="bf43a-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="bf43a-211">Type = "date"</span><span class="sxs-lookup"><span data-stu-id="bf43a-211">type="date"</span></span>|
|<span data-ttu-id="bf43a-212">[DataType (DataType. Time)]</span><span class="sxs-lookup"><span data-stu-id="bf43a-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="bf43a-213">Type = "Time"</span><span class="sxs-lookup"><span data-stu-id="bf43a-213">type="time"</span></span>|

<span data-ttu-id="bf43a-214">Przykład:</span><span class="sxs-lookup"><span data-stu-id="bf43a-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="bf43a-215">Kod powyżej generuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-215">The code above generates the following HTML:</span></span>

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="bf43a-216">Adnotacje danych zastosowane do `Email` właściwości i `Password` generują metadane w modelu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="bf43a-217">Pomocnik tagu wejściowego korzysta z metadanych modelu i tworzy atrybuty [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (zobacz [Walidacja modelu](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="bf43a-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="bf43a-218">Te atrybuty opisują moduły walidacji do dołączenia do pól wejściowych.</span><span class="sxs-lookup"><span data-stu-id="bf43a-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="bf43a-219">Zapewnia to niezauważalne sprawdzanie poprawności HTML5 i [jQuery](https://jquery.com/) .</span><span class="sxs-lookup"><span data-stu-id="bf43a-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="bf43a-220">Atrybuty niezauważalne mają format `data-val-rule="Error Message"` , gdzie rule jest nazwą Reguły walidacji (np `data-val-required` ., `data-val-email` `data-val-maxlength` itp.) Jeśli w atrybucie jest podany komunikat o błędzie, jest on wyświetlany jako wartość `data-val-rule` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="bf43a-221">Istnieją także atrybuty formularza `data-val-ruleName-argumentName="argumentValue"` , które zawierają dodatkowe szczegóły dotyczące reguły, na przykład `data-val-maxlength-max="1024"` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="bf43a-222">Alternatywa pomocnika HTML dla pomocnika tagów wejściowych</span><span class="sxs-lookup"><span data-stu-id="bf43a-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="bf43a-223">`Html.TextBox`, `Html.TextBoxFor` `Html.Editor` i `Html.EditorFor` mają nakładające się funkcje pomocnika tagów wejściowych.</span><span class="sxs-lookup"><span data-stu-id="bf43a-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="bf43a-224">Pomocnik tagu wejściowego automatycznie ustawi `type` atrybut `Html.TextBox` i `Html.TextBoxFor` nie będzie.</span><span class="sxs-lookup"><span data-stu-id="bf43a-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="bf43a-225">`Html.Editor`i `Html.EditorFor` Obsługa kolekcji, złożonych obiektów i szablonów; pomocnik tagów wejściowych nie.</span><span class="sxs-lookup"><span data-stu-id="bf43a-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="bf43a-226">Pomocnik tagu wejściowego `Html.EditorFor` i `Html.TextBoxFor` ma silnie wpisane (używa wyrażeń lambda); `Html.TextBox` i `Html.Editor` nie są (używają nazw wyrażeń).</span><span class="sxs-lookup"><span data-stu-id="bf43a-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="bf43a-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="bf43a-227">HtmlAttributes</span></span>

<span data-ttu-id="bf43a-228">`@Html.Editor()`i `@Html.EditorFor()` Użyj wpisu specjalnego `ViewDataDictionary` o nazwie `htmlAttributes` przy wykonywaniu ich domyślnych szablonów.</span><span class="sxs-lookup"><span data-stu-id="bf43a-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="bf43a-229">To zachowanie jest opcjonalnie rozszerzane za pomocą `additionalViewData` parametrów.</span><span class="sxs-lookup"><span data-stu-id="bf43a-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="bf43a-230">W kluczu "htmlAttributes" nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="bf43a-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="bf43a-231">Klucz "htmlAttributes" jest obsługiwany w podobny sposób, `htmlAttributes` jak obiekt przesłany do pomocników wejściowych, takich jak `@Html.TextBox()` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="bf43a-232">Nazwy wyrażeń</span><span class="sxs-lookup"><span data-stu-id="bf43a-232">Expression names</span></span>

<span data-ttu-id="bf43a-233">`asp-for`Wartość atrybutu jest `ModelExpression` i prawej strony wyrażenia lambda.</span><span class="sxs-lookup"><span data-stu-id="bf43a-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="bf43a-234">W związku z tym, `asp-for="Property1"` zostanie `m => m.Property1` w wygenerowanym kodzie, dlatego nie musisz poprzedzać prefiksem `Model` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="bf43a-235">Można użyć \@ znaku "", aby rozpocząć wyrażenie śródwierszowe i przejść przed `m.` :</span><span class="sxs-lookup"><span data-stu-id="bf43a-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

<span data-ttu-id="bf43a-236">Generuje następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="bf43a-236">Generates the following:</span></span>

```html
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="bf43a-237">Właściwości kolekcji `asp-for="CollectionProperty[23].Member"` generują taką samą nazwę jak `asp-for="CollectionProperty[i].Member"` gdy `i` ma wartość `23` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="bf43a-238">Gdy ASP.NET Core MVC oblicza wartość `ModelExpression` , sprawdza kilka źródeł, w tym `ModelState` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="bf43a-239">Weź pod uwagę `<input type="text" asp-for="@Name">` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="bf43a-240">Atrybut obliczeniowy `value` jest pierwszą wartością o wartości innej niż null z:</span><span class="sxs-lookup"><span data-stu-id="bf43a-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="bf43a-241">`ModelState`wpis z kluczem "name".</span><span class="sxs-lookup"><span data-stu-id="bf43a-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="bf43a-242">Wynik wyrażenia `Model.Name` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="bf43a-243">Nawigowanie po właściwościach podrzędnych</span><span class="sxs-lookup"><span data-stu-id="bf43a-243">Navigating child properties</span></span>

<span data-ttu-id="bf43a-244">Możesz również przejść do właściwości podrzędnych przy użyciu ścieżki właściwości modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="bf43a-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="bf43a-245">Rozważmy bardziej złożoną klasę modelu, która zawiera `Address` Właściwość podrzędną.</span><span class="sxs-lookup"><span data-stu-id="bf43a-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="bf43a-246">W widoku są powiązane `Address.AddressLine1` :</span><span class="sxs-lookup"><span data-stu-id="bf43a-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="bf43a-247">Następujący kod HTML jest generowany dla `Address.AddressLine1` :</span><span class="sxs-lookup"><span data-stu-id="bf43a-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="bf43a-248">Nazwy wyrażeń i kolekcje</span><span class="sxs-lookup"><span data-stu-id="bf43a-248">Expression names and Collections</span></span>

<span data-ttu-id="bf43a-249">Przykładowy model zawierający tablicę `Colors` :</span><span class="sxs-lookup"><span data-stu-id="bf43a-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="bf43a-250">Metoda akcji:</span><span class="sxs-lookup"><span data-stu-id="bf43a-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

<span data-ttu-id="bf43a-251">Poniżej Razor pokazano, jak uzyskać dostęp do określonego `Color` elementu:</span><span class="sxs-lookup"><span data-stu-id="bf43a-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="bf43a-252">Szablon *widoki/Shared/EditorTemplates/String. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bf43a-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="bf43a-253">Przykład przy użyciu `List<T>` :</span><span class="sxs-lookup"><span data-stu-id="bf43a-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="bf43a-254">Poniżej Razor pokazano, jak wykonać iterację kolekcji:</span><span class="sxs-lookup"><span data-stu-id="bf43a-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="bf43a-255">Szablon *widoki/Shared/EditorTemplates/ToDoItem. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bf43a-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="bf43a-256">`foreach`należy użyć, jeśli jest to możliwe, gdy wartość ma być używana w `asp-for` lub `Html.DisplayFor` równoważnym kontekście.</span><span class="sxs-lookup"><span data-stu-id="bf43a-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="bf43a-257">Ogólnie rzecz biorąc, `for` jest lepiej niż (jeśli jest to możliwe `foreach` ), ponieważ nie musi alokować modułu wyliczającego, ale Ocena indeksatora w wyrażeniu LINQ może być kosztowna i powinna być zminimalizowana.</span><span class="sxs-lookup"><span data-stu-id="bf43a-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="bf43a-258">Powyższy przykładowy kod z komentarzem pokazuje, jak zastąpić wyrażenie lambda `@` operatorem, aby uzyskać dostęp do każdego `ToDoItem` z nich na liście.</span><span class="sxs-lookup"><span data-stu-id="bf43a-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="bf43a-259">Pomocnik tagów TextArea</span><span class="sxs-lookup"><span data-stu-id="bf43a-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="bf43a-260">`Textarea Tag Helper`Pomocnik tagów jest podobny do pomocnika tagu wejściowego.</span><span class="sxs-lookup"><span data-stu-id="bf43a-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="bf43a-261">Generuje `id` atrybuty i `name` i atrybuty walidacji danych z modelu dla [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) elementu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="bf43a-262">Zapewnia silne wpisywanie.</span><span class="sxs-lookup"><span data-stu-id="bf43a-262">Provides strong typing.</span></span>

* <span data-ttu-id="bf43a-263">Alternatywa dla pomocnika HTML:`Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="bf43a-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="bf43a-264">Przykład:</span><span class="sxs-lookup"><span data-stu-id="bf43a-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="bf43a-265">Następujący kod HTML jest generowany:</span><span class="sxs-lookup"><span data-stu-id="bf43a-265">The following HTML is generated:</span></span>

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="bf43a-266">Pomocnik tagów etykiety</span><span class="sxs-lookup"><span data-stu-id="bf43a-266">The Label Tag Helper</span></span>

* <span data-ttu-id="bf43a-267">Generuje podpis etykiety i `for` atrybut w [\<label>](https://www.w3.org/wiki/HTML/Elements/label) elemencie dla nazwy wyrażenia</span><span class="sxs-lookup"><span data-stu-id="bf43a-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="bf43a-268">Alternatywa dla pomocnika HTML: `Html.LabelFor` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="bf43a-269">`Label Tag Helper`Zapewnia następujące korzyści w stosunku do czystego elementu etykiety HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="bf43a-270">Wartość etykiety opisowej jest automatycznie pobierana z `Display` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="bf43a-271">Zamierzona nazwa wyświetlana może ulec zmianie z upływem czasu, a kombinacja `Display` pomocnika tagów atrybutów i etykiet będzie stosowała `Display` wszędzie tam, gdzie jest używana.</span><span class="sxs-lookup"><span data-stu-id="bf43a-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="bf43a-272">Mniej znaczników w kodzie źródłowym</span><span class="sxs-lookup"><span data-stu-id="bf43a-272">Less markup in source code</span></span>

* <span data-ttu-id="bf43a-273">Silne pisanie przy użyciu właściwości model.</span><span class="sxs-lookup"><span data-stu-id="bf43a-273">Strong typing with the model property.</span></span>

<span data-ttu-id="bf43a-274">Przykład:</span><span class="sxs-lookup"><span data-stu-id="bf43a-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="bf43a-275">Następujący kod HTML jest generowany dla `<label>` elementu:</span><span class="sxs-lookup"><span data-stu-id="bf43a-275">The following HTML is generated for the `<label>` element:</span></span>

```html
<label for="Email">Email Address</label>
```

<span data-ttu-id="bf43a-276">Pomocnik tagu etykiety wygenerował `for` wartość atrybutu "e-mail", który jest identyfikatorem skojarzonym z `<input>` elementem.</span><span class="sxs-lookup"><span data-stu-id="bf43a-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="bf43a-277">Pomocnicy tagów generują spójne `id` i `for` elementy, aby mogły być prawidłowo skojarzone.</span><span class="sxs-lookup"><span data-stu-id="bf43a-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="bf43a-278">Podpis w tym przykładzie pochodzi z `Display` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="bf43a-279">Jeśli model nie zawiera `Display` atrybutu, podpis będzie nazwą właściwości wyrażenia.</span><span class="sxs-lookup"><span data-stu-id="bf43a-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="bf43a-280">Pomocnicy tagów walidacji</span><span class="sxs-lookup"><span data-stu-id="bf43a-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="bf43a-281">Istnieją dwa pomocnicy tagów sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="bf43a-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="bf43a-282">`Validation Message Tag Helper`(Który wyświetla komunikat weryfikacyjny dla pojedynczej właściwości w modelu) i `Validation Summary Tag Helper` (która wyświetla podsumowanie błędów walidacji).</span><span class="sxs-lookup"><span data-stu-id="bf43a-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="bf43a-283">`Input Tag Helper`Dodaje atrybuty walidacji po stronie klienta HTML5 do elementów wejściowych na podstawie atrybutów adnotacji danych klas modelu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="bf43a-284">Sprawdzanie poprawności jest również wykonywane na serwerze.</span><span class="sxs-lookup"><span data-stu-id="bf43a-284">Validation is also performed on the server.</span></span> <span data-ttu-id="bf43a-285">Pomocnik tagów walidacji wyświetla te komunikaty o błędach w przypadku wystąpienia błędu walidacji.</span><span class="sxs-lookup"><span data-stu-id="bf43a-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="bf43a-286">Pomocnik tagu komunikatu weryfikacji</span><span class="sxs-lookup"><span data-stu-id="bf43a-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="bf43a-287">Dodaje atrybut [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` do elementu [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) , który dołącza komunikaty o błędach walidacji w polu wejściowym określonej właściwości modelu.  </span><span class="sxs-lookup"><span data-stu-id="bf43a-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="bf43a-288">Gdy wystąpi błąd walidacji po stronie klienta, w elemencie [jQuery](https://jquery.com/) zostanie wyświetlony komunikat o błędzie `<span>` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="bf43a-289">Sprawdzanie poprawności odbywa się również na serwerze.</span><span class="sxs-lookup"><span data-stu-id="bf43a-289">Validation also takes place on the server.</span></span> <span data-ttu-id="bf43a-290">Klienci mogą mieć wyłączone skrypty JavaScript, a niektóre sprawdzanie poprawności można wykonać tylko po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="bf43a-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="bf43a-291">Alternatywa dla pomocnika HTML:`Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="bf43a-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="bf43a-292">`Validation Message Tag Helper`Jest używany z `asp-validation-for` atrybutem elementu [zakresu](https://developer.mozilla.org/docs/Web/HTML/Element/span) html.</span><span class="sxs-lookup"><span data-stu-id="bf43a-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```cshtml
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="bf43a-293">Pomocnik tagu komunikatu weryfikacji wygeneruje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="bf43a-294">Zwykle używasz `Validation Message Tag Helper` `Input` pomocnika tagu dla tej samej właściwości.</span><span class="sxs-lookup"><span data-stu-id="bf43a-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="bf43a-295">Spowoduje to wyświetlenie wszystkich komunikatów o błędach weryfikacji obok danych wejściowych, które spowodowały błąd.</span><span class="sxs-lookup"><span data-stu-id="bf43a-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="bf43a-296">Musisz mieć widok z prawidłowymi odwołaniami do skryptów JavaScript i [jQuery](https://jquery.com/) na potrzeby weryfikacji po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="bf43a-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="bf43a-297">Aby uzyskać więcej informacji, zobacz [Walidacja modelu](../models/validation.md) .</span><span class="sxs-lookup"><span data-stu-id="bf43a-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="bf43a-298">Gdy wystąpi błąd walidacji po stronie serwera (na przykład gdy istnieje niestandardowa Walidacja po stronie serwera lub weryfikacja po stronie klienta jest wyłączona), MVC umieszcza ten komunikat o błędzie jako treść `<span>` elementu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="bf43a-299">Pomocnik tagów podsumowania walidacji</span><span class="sxs-lookup"><span data-stu-id="bf43a-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="bf43a-300">`<div>`Elementy docelowe z `asp-validation-summary` atrybutem</span><span class="sxs-lookup"><span data-stu-id="bf43a-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="bf43a-301">Alternatywa dla pomocnika HTML:`@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="bf43a-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="bf43a-302">Służy `Validation Summary Tag Helper` do wyświetlania podsumowania komunikatów weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="bf43a-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="bf43a-303">`asp-validation-summary`Wartość atrybutu może być dowolną z następujących:</span><span class="sxs-lookup"><span data-stu-id="bf43a-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="bf43a-304">ASP-Walidacja — podsumowanie</span><span class="sxs-lookup"><span data-stu-id="bf43a-304">asp-validation-summary</span></span>|<span data-ttu-id="bf43a-305">Wyświetlane komunikaty weryfikacji</span><span class="sxs-lookup"><span data-stu-id="bf43a-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="bf43a-306">Podsumowania walidacji. All</span><span class="sxs-lookup"><span data-stu-id="bf43a-306">ValidationSummary.All</span></span>|<span data-ttu-id="bf43a-307">Poziom właściwości i modelu</span><span class="sxs-lookup"><span data-stu-id="bf43a-307">Property and model level</span></span>|
|<span data-ttu-id="bf43a-308">Podsumowania walidacji. ModelOnly</span><span class="sxs-lookup"><span data-stu-id="bf43a-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="bf43a-309">Model</span><span class="sxs-lookup"><span data-stu-id="bf43a-309">Model</span></span>|
|<span data-ttu-id="bf43a-310">Podsumowania walidacji. None</span><span class="sxs-lookup"><span data-stu-id="bf43a-310">ValidationSummary.None</span></span>|<span data-ttu-id="bf43a-311">Brak</span><span class="sxs-lookup"><span data-stu-id="bf43a-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="bf43a-312">Przykład</span><span class="sxs-lookup"><span data-stu-id="bf43a-312">Sample</span></span>

<span data-ttu-id="bf43a-313">W poniższym przykładzie model danych ma `DataAnnotation` atrybuty, które generują komunikaty o błędach walidacji w `<input>` elemencie.</span><span class="sxs-lookup"><span data-stu-id="bf43a-313">In the following example, the data model has `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="bf43a-314">Gdy wystąpi błąd walidacji, pomocnik tagów walidacji wyświetli komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="bf43a-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="bf43a-315">Wygenerowany kod HTML (gdy model jest prawidłowy):</span><span class="sxs-lookup"><span data-stu-id="bf43a-315">The generated HTML (when the model is valid):</span></span>

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="bf43a-316">Pomocnik wybierania tagu</span><span class="sxs-lookup"><span data-stu-id="bf43a-316">The Select Tag Helper</span></span>

* <span data-ttu-id="bf43a-317">Generuje elementy [opcji](https://www.w3.org/wiki/HTML/Elements/option) [zaznaczania](https://www.w3.org/wiki/HTML/Elements/select) i skojarzone dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="bf43a-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="bf43a-318">Ma alternatywę pomocnika HTML `Html.DropDownListFor` i`Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="bf43a-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="bf43a-319">`Select Tag Helper` `asp-for` Określa nazwę właściwości modelu dla elementu [SELECT](https://www.w3.org/wiki/HTML/Elements/select) i `asp-items` określa elementy [opcji](https://www.w3.org/wiki/HTML/Elements/option) .</span><span class="sxs-lookup"><span data-stu-id="bf43a-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="bf43a-320">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="bf43a-320">For example:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="bf43a-321">Przykład:</span><span class="sxs-lookup"><span data-stu-id="bf43a-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="bf43a-322">`Index`Metoda inicjuje `CountryViewModel` , ustawia wybrany kraj i przekazuje go do `Index` widoku.</span><span class="sxs-lookup"><span data-stu-id="bf43a-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="bf43a-323">Metoda POST protokołu HTTP `Index` wyświetla zaznaczenie:</span><span class="sxs-lookup"><span data-stu-id="bf43a-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="bf43a-324">`Index`Widok:</span><span class="sxs-lookup"><span data-stu-id="bf43a-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="bf43a-325">Który generuje następujący kod HTML (z wybranym "CA"):</span><span class="sxs-lookup"><span data-stu-id="bf43a-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="bf43a-326">Nie zalecamy używania `ViewBag` ani `ViewData` z pomocnikiem SELECT tag.</span><span class="sxs-lookup"><span data-stu-id="bf43a-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="bf43a-327">Model widoku jest bardziej niezawodny przy udostępnianiu metadanych MVC i ogólnie mniej problematycznych.</span><span class="sxs-lookup"><span data-stu-id="bf43a-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="bf43a-328">`asp-for`Wartość atrybutu jest szczególnym przypadkiem i nie wymaga `Model` prefiksu, inne atrybuty pomocnika tagów to (na przykład `asp-items` ).</span><span class="sxs-lookup"><span data-stu-id="bf43a-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="bf43a-329">Stałe powiązania</span><span class="sxs-lookup"><span data-stu-id="bf43a-329">Enum binding</span></span>

<span data-ttu-id="bf43a-330">Jest to często wygodne do użycia `<select>` z `enum` właściwością i generują `SelectListItem` elementy z `enum` wartości.</span><span class="sxs-lookup"><span data-stu-id="bf43a-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="bf43a-331">Przykład:</span><span class="sxs-lookup"><span data-stu-id="bf43a-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="bf43a-332">`GetEnumSelectList`Metoda generuje `SelectList` obiekt dla wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="bf43a-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="bf43a-333">Można oznaczyć listę modułów wyliczających `Display` atrybutem, aby uzyskać bogatszy interfejs użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bf43a-333">You can mark your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="bf43a-334">Następujący kod HTML jest generowany:</span><span class="sxs-lookup"><span data-stu-id="bf43a-334">The following HTML is generated:</span></span>

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="bf43a-335">Grupa opcji</span><span class="sxs-lookup"><span data-stu-id="bf43a-335">Option Group</span></span>

<span data-ttu-id="bf43a-336">Element HTML [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) jest generowany, gdy model widoku zawiera jeden lub więcej `SelectListGroup` obiektów.</span><span class="sxs-lookup"><span data-stu-id="bf43a-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="bf43a-337">`CountryViewModelGroup`Grupuje `SelectListItem` elementy do grup "Ameryka Północna" i "Europa":</span><span class="sxs-lookup"><span data-stu-id="bf43a-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="bf43a-338">Poniżej przedstawiono dwie grupy:</span><span class="sxs-lookup"><span data-stu-id="bf43a-338">The two groups are shown below:</span></span>

![przykład grupy opcji](working-with-forms/_static/grp.png)

<span data-ttu-id="bf43a-340">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-340">The generated HTML:</span></span>

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="bf43a-341">Wybór wielokrotny</span><span class="sxs-lookup"><span data-stu-id="bf43a-341">Multiple select</span></span>

<span data-ttu-id="bf43a-342">Pomocnik Wybierz tag automatycznie generuje atrybut [wielokrotne = "Multiple"](https://w3c.github.io/html-reference/select.html) , jeśli właściwość określona w `asp-for` atrybucie jest `IEnumerable` .</span><span class="sxs-lookup"><span data-stu-id="bf43a-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="bf43a-343">Na przykład, uwzględniając następujący model:</span><span class="sxs-lookup"><span data-stu-id="bf43a-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="bf43a-344">Z następującym widokiem:</span><span class="sxs-lookup"><span data-stu-id="bf43a-344">With the following view:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="bf43a-345">Generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-345">Generates the following HTML:</span></span>

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="bf43a-346">Brak zaznaczenia</span><span class="sxs-lookup"><span data-stu-id="bf43a-346">No selection</span></span>

<span data-ttu-id="bf43a-347">Jeśli znajdziesz samodzielnie opcję "nie określono" na wielu stronach, możesz utworzyć szablon, aby wyeliminować powtarzanie kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="bf43a-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="bf43a-348">Szablon *widoki/Shared/EditorTemplates/CountryViewModel. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bf43a-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="bf43a-349">Dodawanie [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elementów HTML nie jest ograniczone do *żadnego przypadku zaznaczenia* .</span><span class="sxs-lookup"><span data-stu-id="bf43a-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="bf43a-350">Na przykład następująca metoda widok i akcja spowoduje wygenerowanie kodu HTML podobnego do powyższego kodu:</span><span class="sxs-lookup"><span data-stu-id="bf43a-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="bf43a-351">`<option>`Zostanie wybrany prawidłowy element (zawierający `selected="selected"` atrybut) w zależności od bieżącej `Country` wartości.</span><span class="sxs-lookup"><span data-stu-id="bf43a-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="bf43a-352">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bf43a-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="bf43a-353">Element formularza HTML</span><span class="sxs-lookup"><span data-stu-id="bf43a-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="bf43a-354">Token weryfikacji żądania</span><span class="sxs-lookup"><span data-stu-id="bf43a-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="bf43a-355">IAttributeAdapter, interfejs</span><span class="sxs-lookup"><span data-stu-id="bf43a-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="bf43a-356">Fragmenty kodu dla tego dokumentu</span><span class="sxs-lookup"><span data-stu-id="bf43a-356">Code snippets for this document</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)

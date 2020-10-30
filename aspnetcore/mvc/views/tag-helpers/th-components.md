---
title: Oznacz składniki pomocnika w ASP.NET Core
author: scottaddie
description: Informacje o składnikach pomocników tagów i sposobach ich użycia w ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
no-loc:
- appsettings.json
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 15bddd8ce18546bef7ee7e6ec2e32e369d0858a3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060563"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Oznacz składniki pomocnika w ASP.NET Core

Przez [Scott Addie](https://twitter.com/Scott_Addie) i [Fiyaz bin Hasan](https://github.com/fiyazbinhasan)

Składnik pomocnika tagów to pomocnik tagów, który umożliwia warunkowe modyfikowanie lub Dodawanie elementów HTML z kodu po stronie serwera. Ta funkcja jest dostępna w ASP.NET Core 2,0 lub nowszej.

ASP.NET Core obejmuje dwa wbudowane składniki pomocnika tagów: `head` i `body` . Znajdują się one w <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> przestrzeni nazw i mogą być używane zarówno w MVC, jak i Razor stronach. Składniki pomocnika tagów nie wymagają rejestracji w aplikacji w *_ViewImports. cshtml* .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Przypadki zastosowań

Dwa typowe przypadki użycia składników pomocnika tagów obejmują:

1. [Wprowadzanie `<link>` do `<head>` .](#inject-into-html-head-element)
1. [Wprowadzanie `<script>` do `<body>` .](#inject-into-html-body-element)

W poniższych sekcjach opisano te przypadki użycia.

### <a name="inject-into-html-head-element"></a>Wsuń do elementu głównego HTML

Wewnątrz elementu HTML `<head>` pliki CSS są zwykle importowane z `<link>` elementem HTML. Poniższy kod `<link>` wprowadza element do `<head>` elementu przy użyciu `head` składnika pomocnika tagów:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

Powyższy kod ma następujące działanie:

* `AddressStyleTagHelperComponent` implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent> . Streszczenie:
  * Umożliwia inicjowanie klasy z <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext> .
  * Umożliwia używanie składników pomocnika tagów do dodawania lub modyfikowania elementów HTML.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*>Właściwość definiuje kolejność, w jakiej składniki są renderowane. `Order` jest konieczne, gdy w aplikacji istnieje wiele użycia składników pomocnika tagów.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> porównuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> wartość właściwości kontekstu wykonania z `head` . Jeśli wynikiem porównania jest wartość true, zawartość `_style` pola jest wstrzykiwana do `<head>` elementu HTML.

### <a name="inject-into-html-body-element"></a>Wsuń do elementu treści HTML

`body`Składnik pomocnika tagów może wstrzyknąć `<script>` element do `<body>` elementu. Poniższy kod ilustruje tę technikę:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

Do przechowywania elementu używany jest oddzielny plik HTML `<script>` . Plik HTML sprawia, że kod jest bardziej przejrzysty i bardziej czytelny. Poprzedni kod odczytuje zawartość *TagHelpers/templates/AddressToolTipScript.html* i dołącza ją do danych wyjściowych pomocnika tagów. Plik *AddressToolTipScript.html* zawiera następujące znaczniki:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Poprzedni kod tworzy element [widget etykietki narzędzia Bootstrap](https://getbootstrap.com/docs/3.3/javascript/#tooltips) dla każdego `<address>` elementu, który zawiera `printable` atrybut. Efekt jest widoczny, gdy wskaźnik myszy znajduje się nad elementem.

## <a name="register-a-component"></a>Rejestrowanie składnika

Składnik pomocnika tagów należy dodać do kolekcji składników pomocnika tagów aplikacji. Istnieją trzy sposoby dodawania do kolekcji:

* [Rejestracja za pośrednictwem kontenera usług](#registration-via-services-container)
* [Rejestracja za pośrednictwem Razor pliku](#registration-via-razor-file)
* [Rejestracja za pośrednictwem modelu lub kontrolera strony](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Rejestracja za pośrednictwem kontenera usług

Jeśli Klasa składnika pomocnika tagów nie jest zarządzana za pomocą programu <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> , musi być zarejestrowana w systemie [wtrysku zależności (di)](xref:fundamentals/dependency-injection) . Poniższy `Startup.ConfigureServices` kod rejestruje `AddressStyleTagHelperComponent` `AddressScriptTagHelperComponent` klasy i z [przejściowym okresem istnienia](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a>Rejestracja za pośrednictwem Razor pliku

Jeśli składnik pomocnika tagów nie jest zarejestrowany przy użyciu DI, można go zarejestrować ze Razor strony stron lub widoku MVC. Ta technika służy do kontrolowania wstrzykniętego znacznika i kolejności wykonywania składników z Razor pliku.

`ITagHelperComponentManager` służy do dodawania składników pomocnika tagów lub usuwania ich z aplikacji. Poniższy kod ilustruje tę technikę `AddressTagHelperComponent` :

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

Powyższy kod ma następujące działanie:

* `@inject`Dyrektywa zawiera wystąpienie `ITagHelperComponentManager` . Wystąpienie jest przypisane do zmiennej o nazwie w `manager` celu uzyskania dostępu do Razor pliku.
* Wystąpienie `AddressTagHelperComponent` jest dodawane do kolekcji składników pomocnika tagów aplikacji.

`AddressTagHelperComponent` zmodyfikowano, aby pomieścić konstruktora, który akceptuje `markup` `order` Parametry i:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Podany `markup` parametr jest używany w `ProcessAsync` następujący sposób:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Rejestracja za pośrednictwem modelu lub kontrolera strony

Jeśli składnik pomocnika tagów nie jest zarejestrowany w programie DI, może być zarejestrowany z poziomu Razor modelu strony stron lub kontrolera MVC. Ta technika jest przydatna do oddzielania logiki języka C# z Razor plików.

Iniekcja konstruktora jest używana w celu uzyskania dostępu do wystąpienia `ITagHelperComponentManager` . Składnik pomocnika tagów jest dodawany do kolekcji składników pomocnika tagów wystąpienia. Poniższy Razor model strony przedstawia tę technikę `AddressTagHelperComponent` :

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

Powyższy kod ma następujące działanie:

* Iniekcja konstruktora jest używana w celu uzyskania dostępu do wystąpienia `ITagHelperComponentManager` .
* Wystąpienie `AddressTagHelperComponent` jest dodawane do kolekcji składników pomocnika tagów aplikacji.

## <a name="create-a-component"></a>Tworzenie składnika

Aby utworzyć składnik pomocnika tagów niestandardowych:

* Utwórz klasę publiczną wyprowadzaną z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> .
* Zastosuj [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) atrybut do klasy. Określ nazwę docelowego elementu HTML.
* *Opcjonalne* : Zastosuj [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) atrybut do klasy, aby pominąć wyświetlanie typu w IntelliSense.

Poniższy kod tworzy składnik pomocnika tagów niestandardowych, który jest przeznaczony dla `<address>` elementu HTML:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Użyj `address` składnika pomocnika tagów niestandardowych, aby wstrzyknąć znacznik HTML w następujący sposób:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

Poprzednia `ProcessAsync` metoda wprowadza kod HTML udostępniony do <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> zgodnego `<address>` elementu. Iniekcja występuje, gdy:

* Wartość właściwości kontekstu wykonania `TagName` jest równa `address` .
* Odpowiadający `<address>` element ma `printable` atrybut.

Na przykład, `if` instrukcja daje w wyniku wartość true podczas przetwarzania następującego `<address>` elementu:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>

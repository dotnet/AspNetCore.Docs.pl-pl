---
title: Pomocnik tagów częściowych w ASP.NET Core
author: scottaddie
description: Odkryj pomocnika tagów częściowej ASP.NET Core i rolę, w której każda z jego atrybutów jest odtwarzana w wyniku renderowania częściowego widoku.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: d3207969dfbeb9a81e0da88f38a38c6889bbfba9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775872"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>Pomocnik tagów częściowych w ASP.NET Core

Przez [Scott Addie](https://github.com/scottaddie)

Aby zapoznać się z omówieniem pomocników tagów, <xref:mvc/views/tag-helpers/intro>Zobacz.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Omówienie

Pomocnik tagów częściowych służy do renderowania [widoku częściowego](xref:mvc/views/partial) na stronach Razor i w aplikacjach MVC. Weź pod uwagę, że:

* Wymaga ASP.NET Core 2,1 lub nowszego.
* Jest alternatywną [składnią pomocnika HTML](xref:mvc/views/partial#reference-a-partial-view).
* Renderuje widok częściowy asynchronicznie.

Opcje pomocnika HTML do renderowania widoku częściowego obejmują:

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

Model *produktu* jest używany w przykładach w tym dokumencie:

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

Poniżej znajduje się spis atrybutów pomocnika tagów częściowych.

## <a name="name"></a>name

`name` Atrybut jest wymagany. Wskazuje nazwę lub ścieżkę widoku częściowego, który ma być renderowany. Po podaniu nazwy widoku częściowego zostanie zainicjowany proces [odnajdywania widoku](xref:mvc/views/overview#view-discovery) . Ten proces jest pomijany, gdy podano jawną ścieżkę. Wszystkie akceptowalne `name` wartości można znaleźć w sekcji [odnajdywanie widoku częściowego](xref:mvc/views/partial#partial-view-discovery).

Następujące oznakowanie używa jawnej ścieżki, wskazując, że *_ProductPartial. cshtml* ma być załadowany z folderu *udostępnionego* . Użycie atrybutu [for](#for) powoduje przekazanie modelu do widoku częściowego w celu powiązania.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>for

Ten `for` atrybut przypisuje [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) do oceny względem bieżącego modelu. A `ModelExpression` wnioskuje `@Model.` składnię. Na przykład, `for="Product"` można użyć zamiast `for="@Model.Product"`. To domyślne zachowanie wnioskowania jest zastępowane przy użyciu `@` symbolu do zdefiniowania wyrażenia wbudowanego.

Następujące znaczniki są ładowane *_ProductPartial. cshtml*:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

Widok częściowy jest powiązany z `Product` właściwością skojarzonego modelu strony:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>model

Ten `model` atrybut przypisuje wystąpienie modelu do przekazania do widoku częściowego. Nie `model` można używać atrybutu z atrybutem [for](#for) .

W poniższym znaczniku jest tworzone wystąpienie `Product` nowego obiektu i przekazanie do niego `model` atrybutu w celu powiązania:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>Widok — dane

`view-data` Atrybut przypisuje [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) do przekazania do widoku częściowego. Poniższy znacznik sprawia, że cała kolekcja ViewData jest dostępna dla widoku częściowego:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

W poprzednim kodzie wartość `IsNumberReadOnly` klucza jest ustawiona na `true` i dodana do kolekcji Viewdata. W związku z `ViewData["IsNumberReadOnly"]` tym, jest dostępny w ramach następującego widoku częściowego:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

W tym przykładzie wartość `ViewData["IsNumberReadOnly"]` określa, czy pole *liczbowe* jest wyświetlane jako tylko do odczytu.

## <a name="migrate-from-an-html-helper"></a>Migrowanie z pomocnika HTML

Rozważmy następujący przykładowy asynchroniczny pomocnik HTML. Kolekcja produktów jest powtarzana i wyświetlana. Na pierwszy `PartialAsync` parametr metody jest ładowany widok częściowy *_ProductPartial. cshtml* . Wystąpienie `Product` modelu jest przekazane do widoku częściowego w celu powiązania.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

Poniższy pomocnik tagów częściowych osiąga takie samo asynchroniczne zachowanie renderowania, jak pomocnik `PartialAsync` html. `model` Atrybutowi przypisano `Product` wystąpienie modelu dla powiązania do widoku częściowego.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>

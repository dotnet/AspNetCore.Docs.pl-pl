---
title: Pomocnik tagu linku w ASP.NET Core
author: rick-anderson
ms.author: riande
description: Odkryj atrybuty pomocnika znacznika łącza ASP.NET Core i rolę, jaką każdy atrybut odgrywa w rozszerzeniu zachowania taga HTML tag.
ms.custom: mvc
ms.date: 09/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: ac9f6449e2b7b135318ecf116e1dba7b33ddff83
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212400"
---
# <a name="link-tag-helper-in-aspnet-core"></a>Pomocnik tagu linku w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Pomocnik tagu linku](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generuje łącze do podstawowego lub przywróconego pliku CSS. Zazwyczaj podstawowy plik CSS znajduje się w [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Pomocnik tagu linku umożliwia określenie sieci CDN dla pliku CSS i rezerwę, gdy sieć CDN jest niedostępna. Pomocnik tagu linku zapewnia wydajność sieci CDN z niezawodną obsługą hostingu lokalnego.

Poniższe Razor znaczniki przedstawiają `head` element pliku układu utworzonego za pomocą szablonu ASP.NET Core Web App:

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

Następujące elementy są renderowane HTML z poprzedniego kodu (w środowisku innym niż programowanie):

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

W poprzednim kodzie pomocnik tagu linku wygenerował `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element i następujący kod JavaScript, który jest używany do weryfikowania żądanego pliku *Bootstrap. min. css* jest dostępny w sieci CDN. W takim przypadku plik CSS był dostępny, aby pomocnik tagów wygenerował `<link />` element z plikiem CSS usługi CDN.

## <a name="commonly-used-link-tag-helper-attributes"></a>Atrybuty pomocnika często używanych tagów linków

Zobacz [pomocnika tagów linków](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) dla wszystkich atrybutów pomocnika tagów linków, właściwości i metod.

### <a name="href"></a>Tag

Preferowany adres połączonego zasobu. Adres jest przekazaniem do wygenerowanego kodu HTML we wszystkich przypadkach.

### <a name="asp-fallback-href"></a>ASP-Fallback-href

Adres URL arkusza stylów CSS, do którego ma nastąpić powrót w przypadku niepowodzenia podstawowego adresu URL.

### <a name="asp-fallback-test-class"></a>ASP-Fallback-test-Klasa

Nazwa klasy zdefiniowana w arkuszu stylów do użycia w teście Fallback. Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>ASP-Fallback-test-Property

Nazwa właściwości CSS do użycia w teście Fallback. Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>ASP — wartość testowa

Wartość właściwości CSS do użycia dla testu powrotu. Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>

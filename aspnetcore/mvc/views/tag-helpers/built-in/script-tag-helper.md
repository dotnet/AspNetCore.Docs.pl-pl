---
title: Pomocnik tagu skryptu w ASP.NET Core
author: rick-anderson
ms.author: riande
description: Odkryj atrybuty pomocnika tagów ASP.NET Core i rolę, jaką każdy atrybut odgrywa w rozszerzeniu zachowania tagu skryptu HTML.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- appsettings.json
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053504"
---
# <a name="script-tag-helper-in-aspnet-core"></a>Pomocnik tagu skryptu w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Pomocnik tagu skryptu](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generuje link do pliku skryptu podstawowego lub odwracania. Zazwyczaj podstawowy plik skryptu znajduje się w [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Pomocnik tagu skryptu pozwala określić sieć CDN dla pliku skryptu i rezerwę, gdy sieć CDN nie jest dostępna. Pomocnik tagów skryptu zapewnia wydajność sieci CDN z niezawodną obsługą hostingu lokalnego.

Poniższy Razor znacznik pokazuje `script` element z rezerwą:

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

Nie używaj `<script>` atrybutu [Ustąp](https://developer.mozilla.org/docs/Web/HTML/Element/script) elementu, aby odroczyć ładowanie skryptu CDN. Pomocnik tagów skryptu renderuje kod JavaScript, który natychmiast wykonuje wyrażenie [ASP-Fallback-test](#asp-fallback-test) . Wyrażenie nie powiedzie się, jeśli ładowanie skryptu sieci CDN zostało odroczone.

## <a name="commonly-used-script-tag-helper-attributes"></a>Atrybuty pomocnika często używanych tagów skryptu

Zobacz [pomocnik tagów skryptów](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) dla wszystkich atrybutów pomocnika tagów skryptu, właściwości i metod.

### <a name="asp-fallback-test"></a>ASP — test Fallback

Metoda skryptu zdefiniowana w podstawowym skrypcie do użycia dla testu powrotu. Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.

### <a name="asp-fallback-src"></a>ASP — Fallback-src

Adres URL tagu skryptu, do którego ma zostać nastąpi powrót w przypadku niepowodzenia pierwszego planu. Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>

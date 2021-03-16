---
title: Łączenie i zminifikować zasobów statycznych w ASP.NET Core
author: scottaddie
description: Dowiedz się, jak zoptymalizować zasoby statyczne w ASP.NET Core aplikacji sieci Web przez zastosowanie technik tworzenia i minifikacja.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/14/2021
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: d594bbf277907e22b0299b0451e480e9d533d506
ms.sourcegitcommit: 00368bb6a5420983beaced5b62dabc1f94abdeba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103557806"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Łączenie i zminifikować zasobów statycznych w ASP.NET Core

Przez [Scott Addie](https://twitter.com/Scott_Addie) i [David sosny](https://twitter.com/davidpine7)

W tym artykule wyjaśniono zalety stosowania funkcji tworzenia i minifikacja, w tym ich używania z aplikacjami sieci Web ASP.NET Core.

## <a name="what-is-bundling-and-minification"></a>Co to jest rozdziały i minifikacja

Tworzenie i minifikacja to dwie różne optymalizacje wydajności, które można zastosować w aplikacji sieci Web. Używane razem, grupując i minifikacja poprawić wydajność poprzez zmniejszenie liczby żądań serwera i zmniejszenie rozmiaru żądanych zasobów statycznych.

Zgrupowanie i minifikacja przede wszystkim zwiększy czas ładowania żądania pierwszej strony. Po zażądaniu strony sieci Web przeglądarka buforuje statyczne zasoby (JavaScript, CSS i obrazy). Dlatego zgrupowanie i minifikacja nie poprawiaj wydajności podczas żądania tej samej strony lub stron w tej samej lokacji, w której zażądają tych samych zasobów. Jeśli Nagłówek Expires nie jest poprawnie ustawiony na elementach zawartości i jeśli nie jest używane minifikacja i nie jest używany, heurystyka Aktualności przeglądarki Oznacz zasoby jako przestarzałe po kilku dniach. Ponadto przeglądarka wymaga żądania weryfikacji dla każdego elementu zawartości. W takim przypadku zgrupowanie i minifikacja zapewnia poprawę wydajności nawet po pierwszym żądaniu strony.

### <a name="bundling"></a>Tworzenia pakietów

Tworzenie pakietów pozwala łączyć wiele plików w jeden plik. Zgrupowanie zmniejsza liczbę żądań serwera, które są niezbędne do renderowania zasobów sieci Web, takich jak strona sieci Web. Można utworzyć dowolną liczbę pojedynczych pakietów przeznaczonych dla CSS, JavaScript itd. Mniej plików oznacza mniejszą liczbę żądań HTTP z przeglądarki do serwera lub z usługi dostarczającej aplikację. Powoduje to zwiększenie wydajności pierwszej strony.

### <a name="minification"></a>Minifikacja

Minifikacja usuwa zbędne znaki z kodu bez zmiany funkcjonalności. Wynikiem jest znaczny spadek rozmiaru żądanych zasobów (takich jak CSS, obrazy i pliki JavaScript). Typowe efekty uboczne minifikacja obejmują skracanie nazw zmiennych do jednego znaku oraz usuwanie komentarzy i niepotrzebnych białych znaków.

Weź pod uwagę następującą funkcję języka JavaScript:

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
    ///<signature>
    ///<summary> Adds an alt tab to the image
    // </summary>
    //<param name="imgElement" type="String">The image selector.</param>
    //<param name="ContextForImage" type="String">The image context.</param>
    ///</signature>
    var imageElement = $(imageTagAndImageID, imageContext);
    imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

Minifikacja zmniejsza funkcję do następujących:

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

Oprócz usuwania komentarzy i niepotrzebnych białych znaków, nazwy następujących parametrów i zmiennych zostały zmienione w następujący sposób:

Oryginalne | Zmiany
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Wpływ tworzenia i minifikacja

W poniższej tabeli przedstawiono różnice między pojedynczym ładowaniem zasobów i użyciem grupowania i minifikacja:

Akcja | Z B/M | Bez B/M | Zmiana
--- | :---: | :---: | :---:
Żądania plików  | 7   | 18     | 157%
Przeniesiono KB | 156 | 264,68 | 70%
Czas ładowania (MS) | 885 | 2360   | 167%

Przeglądarki są dość pełne w odniesieniu do nagłówków żądań HTTP. Metryka całkowita liczba wysłanych bajtów osiągnęła znaczącą redukcję podczas grupowania. Czas ładowania przedstawia znaczącą poprawę, jednak ten przykład jest uruchamiany lokalnie. W przypadku korzystania z funkcji grupowania i minifikacja z zasobami transferowanymi za pośrednictwem sieci są osiągane większe zyski wydajności.

## <a name="choose-a-bundling-and-minification-strategy"></a>Wybierz strategię tworzenia i minifikacja

ASP.NET Core jest zgodna z programem weboptimize, minifikacja i rozwiązaniem Open Source. Aby uzyskać instrukcje dotyczące konfigurowania i przykładowych projektów, zobacz temat [Weboptimize](https://github.com/ligershark/WebOptimizer). ASP.NET Core nie zapewnia natywnego tworzenia i minifikacja rozwiązania.

Narzędzia innych firm, takie jak [Gulp](https://gulpjs.com) i [WebPack](https://webpack.js.org), zapewniają automatyzację przepływu pracy do tworzenia i minifikacja, a także Zaznaczanie błędów i optymalizację obrazu. Korzystając z konstrukcji i minifikacja w czasie projektowania, pliki zminimalizowanego są tworzone przed wdrożeniem aplikacji. Przydzielenie i minifikacja przed wdrożeniem zapewnia zalety mniejszego obciążenia serwera. Należy jednak pamiętać, że konstrukcja czasu projektowania i minifikacja zwiększa złożoność kompilacji i działa tylko z plikami statycznymi.

## <a name="environment-based-bundling-and-minification"></a>Tworzenie i minifikacja oparte na środowisku

Najlepszym rozwiązaniem jest użycie w środowisku produkcyjnym plików z pakietem i zminimalizowanego aplikacji. Podczas opracowywania oryginalne pliki ułatwiają debugowanie aplikacji.

Określ pliki do uwzględnienia na stronach przy użyciu [pomocnika tagów środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) w widokach. Pomocnik tagów środowiska renderuje jego zawartość tylko w przypadku uruchamiania w określonych [środowiskach](xref:fundamentals/environments).

Następujący `environment` tag renderuje nieprzetworzone pliki CSS podczas działania w `Development` środowisku:

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment include="Development">
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    <link rel="stylesheet" href="~/css/site.css" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

Poniższy `environment` tag renderuje powiązane i zminimalizowanego pliki CSS, gdy działa w środowisku innym niż `Development` . Na przykład uruchomienie w programie `Production` lub `Staging` wyzwala renderowanie tych arkuszy stylów:

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment exclude="Development">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Używanie wielu środowisk](xref:fundamentals/environments)
* [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro)

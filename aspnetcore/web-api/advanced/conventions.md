---
title: Korzystanie z Konwencji interfejsu API sieci Web
author: pranavkm
description: Dowiedz się więcej na temat Konwencji interfejsu API sieci Web w ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/conventions
ms.openlocfilehash: e18a2096c3b9fa788ba2a9713d0f25c1e76af2db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019966"
---
# <a name="use-web-api-conventions"></a>Korzystanie z Konwencji interfejsu API sieci Web

Autorzy [Pranav Krishnamoorthy](https://github.com/pranavkm) i [Scott Addie](https://github.com/scottaddie)

ASP.NET Core 2,2 i nowsze zawierają sposób wyodrębnienia typowej [dokumentacji interfejsu API](xref:tutorials/web-api-help-pages-using-swagger) i zastosowania jej do wielu akcji, kontrolerów lub wszystkich kontrolerów w ramach zestawu. Konwencje internetowego interfejsu API stanowią podstawę do dekorowania nazwy poszczególnych akcji z [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) .

Konwencja umożliwia:

* Zdefiniuj najpopularniejsze typy zwracane i kody stanu zwrócone przez określony typ akcji.
* Zidentyfikuj akcje odbiegające od zdefiniowanego standardu.

ASP.NET Core MVC 2,2 i nowsze zawierają zestaw Konwencji domyślnych w programie <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName> . Konwencje są oparte na kontrolerze (*ValuesController.cs*), który znajduje się w szablonie projektu **interfejsu API** ASP.NET Core. Jeśli akcje są zgodne ze wzorcami w szablonie, należy pomyślnie korzystać z domyślnych Konwencji. Jeśli domyślne konwencje nie spełniają Twoich potrzeb, zobacz [Tworzenie Konwencji internetowego interfejsu API](#create-web-api-conventions).

W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumie konwencje. `ApiExplorer`jest abstrakcją MVC do komunikowania się z [openapi](https://www.openapis.org/) (znanego również jako Swagger) generatorami dokumentów. Atrybuty z stosowanej Konwencji są skojarzone z akcją i są zawarte w dokumentacji OpenAPI akcji. [Analizatory interfejsów API](xref:web-api/advanced/analyzers) są również zrozumiałe Konwencji. Jeśli akcja jest niekonwencjonalny (na przykład zwraca kod stanu, który nie jest udokumentowany przez zastosowana Konwencja), ostrzeżenie zachęca do dokumentowania kodu stanu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Zastosuj konwencje internetowego interfejsu API

Konwencje nie tworzą; Każda akcja może być skojarzona z dokładnie jedną Konwencją. Bardziej szczegółowe konwencje mają pierwszeństwo przed określonymi konwencjami. Zaznaczenie jest niedeterministyczne, jeśli co najmniej dwie konwencje o takim samym priorytecie mają zastosowanie do akcji. Następujące opcje są stosowane w celu zastosowania konwencji do akcji, od najbardziej do najmniej określonych:

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash;Stosuje się do poszczególnych akcji i określa typ Konwencji oraz metodę Konwencji, która ma zastosowanie.

    W poniższym przykładzie metoda Konwencji domyślnego typu Konwencji `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` jest stosowana do `Update` akcji:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put`Metoda Konwencji stosuje następujące atrybuty do akcji:

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    Aby uzyskać więcej informacji na temat `[ProducesDefaultResponseType]` , zobacz [Domyślna odpowiedź](https://swagger.io/docs/specification/describing-responses/#default).

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`zastosowane do kontrolera &mdash; stosuje określony typ Konwencji do wszystkich akcji na kontrolerze. Metoda Konwencji jest oznaczona przy użyciu wskazówek, które określają akcje, których dotyczy Metoda Konwencji. Aby uzyskać więcej informacji na temat wskazówek, zobacz [Tworzenie Konwencji interfejsu API sieci Web](#create-web-api-conventions)).

    W poniższym przykładzie domyślny zestaw Konwencji jest stosowany do wszystkich akcji w *ContactsConventionController*:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`zastosowane do zestawu &mdash; stosuje określony typ Konwencji do wszystkich kontrolerów w bieżącym zestawie. Zgodnie z zaleceniem Zastosuj atrybuty na poziomie zestawu w pliku *Startup.cs* .

    W poniższym przykładzie domyślny zestaw Konwencji jest stosowany do wszystkich kontrolerów w zestawie:

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>Tworzenie Konwencji interfejsu API sieci Web

Jeśli domyślne konwencje interfejsów API nie spełniają Twoich potrzeb, należy utworzyć własne konwencje. Konwencja:

* Typ statyczny z metodami.
* Możliwość definiowania [typów odpowiedzi](#response-types) i [wymagań dotyczących nazewnictwa](#naming-requirements) w akcjach.

### <a name="response-types"></a>Typy odpowiedzi

Te metody są opatrzone adnotacją `[ProducesResponseType]` lub `[ProducesDefaultResponseType]` atrybutami. Przykład:

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

W przypadku braku szczegółowych atrybutów metadanych zastosowanie tej Konwencji do zestawu wymusza, że:

* Metoda Konwencji ma zastosowanie do dowolnej akcji o nazwie `Find` .
* `id`W akcji występuje parametr o nazwie `Find` .

### <a name="naming-requirements"></a>Wymagania dotyczące nazewnictwa

`[ApiConventionNameMatch]`Atrybuty i `[ApiConventionTypeMatch]` mogą być stosowane do metody Konwencji, która określa akcje, do których mają zastosowanie. Przykład:

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

W powyższym przykładzie:

* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix`Opcja stosowana do metody wskazuje, że Konwencja pasuje do każdej akcji poprzedzonej prefiksem "Find". Przykłady akcji dopasowania obejmują `Find` , `FindPet` , i `FindById` .
* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix`Zastosowanie do parametru wskazuje, że Konwencja dopasowuje metody z dokładnie jednym parametrem kończącym się identyfikatorem sufiksu. Przykłady obejmują parametry, takie jak `id` lub `petId` . `ApiConventionTypeMatch`można w podobny sposób zastosować do typów, aby ograniczyć typ parametru. `params[]`Argument wskazuje pozostałe parametry, które nie muszą być jawnie dopasowane.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>

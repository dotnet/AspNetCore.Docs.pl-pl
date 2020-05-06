---
title: Pomocnik tagu pamięci podręcznej w ASP.NET Core MVC
author: pkellner
description: Dowiedz się, jak używać pomocnika tagów pamięci podręcznej.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: ced10a7b7b221188fdac2a4e3c54f66292110ece
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773945"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Pomocnik tagu pamięci podręcznej w ASP.NET Core MVC

Według [Peterowi Kellner](https://peterkellner.net)

Pomocnik tagu pamięci podręcznej umożliwia zwiększenie wydajności aplikacji ASP.NET Core przez buforowanie jej zawartości dla dostawcy wewnętrznej ASP.NET Core pamięci podręcznej.

Aby zapoznać się z omówieniem pomocników tagów, <xref:mvc/views/tag-helpers/intro>Zobacz.

Następujący znacznik Razor buforuje bieżącą datę:

```cshtml
<cache>@DateTime.Now</cache>
```

Pierwsze żądanie do strony zawierającej pomocnika tagów wyświetla bieżącą datę. Dodatkowe żądania pokazują wartość buforowaną, aż do momentu wygaśnięcia pamięci podręcznej (domyślnie 20 minut) lub do momentu wykluczenia z pamięci podręcznej.

## <a name="cache-tag-helper-attributes"></a>Atrybuty pomocnika tagu pamięci podręcznej

### <a name="enabled"></a>enabled

| Typ atrybutu  | Przykłady        | Domyślne |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`enabled`Określa, czy zawartość umieszczona w buforze znacznika pamięci podręcznej jest buforowana. Wartość domyślna to `true`. Jeśli jest ustawiona `false`na, renderowane dane wyjściowe **nie** są buforowane.

Przykład:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>Data wygaśnięcia

| Typ atrybutu   | Przykład                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on`Ustawia bezwzględną datę wygaśnięcia dla elementu w pamięci podręcznej.

Poniższy przykład pamięci podręcznej zawartości pomocnika tagów pamięci podręcznej do 5:02 PM dnia 29 stycznia 2025:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>wygasa — po

| Typ atrybutu | Przykład                      | Domyślne    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 minut |

`expires-after`Ustawia długość czasu od pierwszego żądania do buforowania zawartości.

Przykład:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Aparat widoku Razor ustawia wartość domyślną `expires-after` na 20 minut.

### <a name="expires-sliding"></a>wygasa — przesuwanie

| Typ atrybutu | Przykład                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Określa czas, w którym wpis pamięci podręcznej powinien zostać wykluczony, jeśli nie uzyskano dostępu do jego wartości.

Przykład:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>Zróżnicuj według nagłówka

| Typ atrybutu | Przykłady                                    |
| -------------- | ------------------------------------------- |
| String         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header`akceptuje rozdzielaną przecinkami listę wartości nagłówka, które wyzwalają Odświeżanie pamięci podręcznej, gdy zmieniają się.

Poniższy przykład monitoruje wartość `User-Agent`nagłówka. Przykład buforuje zawartość dla każdej innej `User-Agent` prezentowanej na serwerze sieci Web:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>Zróżnicuj według zapytania

| Typ atrybutu | Przykłady             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-query`akceptuje listę rozdzielonych przecinkami <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> w ciągu zapytania (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>), które wyzwalają Odświeżanie pamięci podręcznej, gdy zostanie zmieniona wartość dowolnego z wymienionych kluczy.

Poniższy przykład monitoruje wartości `Make` i. `Model` Przykład buforuje zawartość dla każdej innej `Make` i `Model` prezentowanej na serwerze sieci Web:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>Zróżnicuj według trasy

| Typ atrybutu | Przykłady             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-route`akceptuje rozdzielaną przecinkami listę nazw parametrów trasy, które wyzwalają Odświeżanie pamięci podręcznej, gdy wartość parametru dane trasy zostanie zmieniona.

Przykład:

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index. cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a>Różne pliki cookie

| Typ atrybutu | Przykłady                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| String         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie`akceptuje rozdzielaną przecinkami listę nazw cookie, które wyzwalają Odświeżanie pamięci podręcznej, gdy zmieniają się wartości plików cookie.

Poniższy przykład monitoruje plik cookie skojarzony z tożsamością ASP.NET Core. Po uwierzytelnieniu użytkownika zmiana w pliku cookie tożsamości wyzwala odświeżenie pamięci podręcznej:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>Zróżnicuj według użytkownika

| Typ atrybutu  | Przykłady        | Domyślne |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`vary-by-user`Określa, czy pamięć podręczna jest resetowana, gdy zostanie zmieniony zalogowany użytkownik (lub podmiot zabezpieczeń kontekstu). Bieżący użytkownik jest również znany jako podmiot zabezpieczeń kontekstu żądania i może być wyświetlany w widoku Razor przez odwołanie `@User.Identity.Name`.

Poniższy przykład monitoruje bieżącego zalogowanego użytkownika, aby wyzwolić Odświeżanie pamięci podręcznej:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Użycie tego atrybutu zachowuje zawartość w pamięci podręcznej przez proces logowania i wylogowywania. Gdy wartość jest ustawiona na `true`, cykl uwierzytelniania unieważnia pamięć podręczną dla uwierzytelnionego użytkownika. Pamięć podręczna jest unieważniona, ponieważ podczas uwierzytelniania użytkownika jest generowana Nowa unikatowa wartość cookie. Pamięć podręczna jest utrzymywana dla stanu anonimowego, gdy plik cookie nie istnieje lub plik cookie utracił ważność. Jeśli użytkownik **nie** jest uwierzytelniony, pamięć podręczna jest utrzymywana.

### <a name="vary-by"></a>Zróżnicuj według

| Typ atrybutu | Przykład  |
| -------------- | -------- |
| String         | `@Model` |

`vary-by`umożliwia dostosowanie danych przechowywanych w pamięci podręcznej. Gdy obiekt, do którego odwołuje się wartość ciągu atrybutu, zmienia się zawartość pomocnika tagu pamięci podręcznej. Często łączenie ciągów wartości modelu jest przypisywane do tego atrybutu. Efektywnie jest to scenariusz, w którym aktualizacja dowolnej z połączonych wartości unieważnia pamięć podręczną.

W poniższym przykładzie przyjęto założenie, że metoda kontrolera renderuje widok sumuje wartość całkowitą dwóch parametrów `myParam1` trasy `myParam2`, i zwraca sumę jako właściwość pojedynczego modelu. Po zmianie tej sumy zawartość pomocnika tagu pamięci podręcznej jest renderowana i buforowana ponownie.  

Działanie:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index. cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| Typ atrybutu      | Przykłady                               | Domyślne  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority`zapewnia wskazówki dotyczące wykluczenia pamięci podręcznej dla wbudowanego dostawcy pamięci podręcznej. Serwer sieci Web najpierw wyklucza `Low` wpisy pamięci podręcznej, gdy jest on w trakcie jego naciskania pamięci.

Przykład:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Ten `priority` atrybut nie gwarantuje określonego poziomu przechowywania pamięci podręcznej. `CacheItemPriority`jest tylko sugestią. Ustawienie tego atrybutu na `NeverRemove` wartość nie gwarantuje, że buforowane elementy są zawsze zachowywane. Aby uzyskać więcej informacji, zobacz tematy w sekcji [dodatkowe zasoby](#additional-resources) .

Pomocnik tagu pamięci podręcznej jest zależny od [usługi pamięci podręcznej](xref:performance/caching/memory). Pomocnik tagu pamięci podręcznej dodaje usługę, jeśli nie została dodana.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>

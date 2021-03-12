---
title: Niestandardowe powiązanie modelu w ASP.NET Core
author: ardalis
description: Dowiedz się, jak powiązanie modelu umożliwia akcjom kontrolera bezpośrednie działanie z typami modeli w ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: b674d3a034124a26e2e76f2a7f0220e3f0ecea56
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588773"
---
# <a name="custom-model-binding-in-aspnet-core"></a>Niestandardowe powiązanie modelu w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Steve Kowalski](https://ardalis.com/) i [Kirka Larkin](https://twitter.com/serpent5)

Powiązanie modelu umożliwia działanie kontrolera do pracy bezpośrednio z typami modeli (przekazane jako argumenty metod), a nie żądaniami HTTP. Mapowanie między danymi żądań przychodzących a modelami aplikacji jest obsługiwane przez program Binders. Deweloperzy mogą rozszerzać wbudowaną funkcję powiązania modelu, implementując niestandardowe powiązania modelu (zazwyczaj nie trzeba pisać własnego dostawcy).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Domyślne ograniczenia spinacza modelu

Domyślne powiązania modeli obsługują większość wspólnych typów danych .NET Core i powinny spełniać większość potrzeb deweloperów. Oczekują one powiązania danych tekstowych na podstawie żądania bezpośrednio z typami modeli. Może być konieczne przekształcenie danych wejściowych przed ich powiązaniem. Na przykład, jeśli masz klucz, którego można użyć do wyszukiwania danych modelu. Do pobierania danych opartych na kluczu można użyć spinacza modelu niestandardowego.

## <a name="model-binding-review"></a>Przegląd powiązań modelu

Powiązanie modelu używa określonych definicji dla typów, w których działa. *Typ prosty* jest konwertowany z pojedynczego ciągu w danych wejściowych. *Typ złożony* jest konwertowany z wielu wartości wejściowych. Struktura określa różnice w zależności od istnienia `TypeConverter` . Zalecamy utworzenie konwertera typów, jeśli istnieje proste `string`  ->  `SomeType` Mapowanie, które nie wymaga zasobów zewnętrznych.

Przed utworzeniem własnego spinacza modelu niestandardowego warto przejrzeć sposób implementacji istniejących spinaczy modelu. Rozważ, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> który może być używany do konwertowania ciągów zakodowanych algorytmem Base64 na tablice bajtowe. Tablice bajtowe są często przechowywane jako pliki lub pola obiektów BLOB bazy danych.

### <a name="working-with-the-bytearraymodelbinder"></a>Praca z ByteArrayModelBinder

Ciągi kodowane algorytmem Base64 mogą służyć do reprezentowania danych binarnych. Na przykład obraz może być zakodowany jako ciąg. Przykład zawiera obraz jako ciąg zakodowany algorytmem Base64 w [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC może przyjmować ciąg zakodowany algorytmem Base64 i używać go, `ByteArrayModelBinder` Aby przekonwertować go na tablicę bajtów. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider>Mapowania `byte[]` argumentów do `ByteArrayModelBinder` :

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

Podczas tworzenia własnego spinacza modelu niestandardowego można zaimplementować własny `IModelBinderProvider` Typ lub użyć <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .

Poniższy przykład pokazuje, jak użyć `ByteArrayModelBinder` do przekonwertowania ciągu zakodowanego algorytmem Base64 na `byte[]` a i zapisać wynik do pliku:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Można OPUBLIKOWAĆ ciąg zakodowany algorytmem Base64 w tej metodzie interfejsu API za pomocą narzędzia, takiego jak [Poster](https://www.getpostman.com/):

![Postman](custom-model-binding/images/postman.png "Postman")

Tak długo, jak spinacz może powiązać dane żądania do odpowiednio nazwanych właściwości lub argumentów, powiązanie modelu zakończy się pomyślnie. Poniższy przykład przedstawia sposób użycia `ByteArrayModelBinder` z modelem widoku:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a>Przykładowy spinacz modelu niestandardowego

W tej sekcji zaimplementujemy niestandardowy spinacz modelu, który:

- Konwertuje przychodzące dane żądania na argumenty klucza o jednoznacznie określonym typie.
- Używa Entity Framework Core, aby pobrać skojarzoną jednostkę.
- Przekazuje skojarzoną jednostkę jako argument do metody akcji.

Poniższy przykład używa `ModelBinder` atrybutu w `Author` modelu:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

W poprzednim kodzie `ModelBinder` atrybut określa typ `IModelBinder` , który powinien być używany do wiązania `Author` parametrów akcji.

Następująca `AuthorEntityBinder` Klasa wiąże `Author` parametr przez pobranie jednostki ze źródła danych przy użyciu Entity Framework Core i `authorId` :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> Poprzednia `AuthorEntityBinder` Klasa jest przeznaczona do zilustrowania niestandardowego spinacza modelu. Klasa nie jest przeznaczona do zilustrowania najlepszych rozwiązań dotyczących scenariusza wyszukiwania. W celu wyszukania należy powiązać `authorId` bazę danych i zbadać ją w metodzie akcji. To podejście oddziela błędy powiązań modelu z `NotFound` przypadków.

Poniższy kod pokazuje, jak używać `AuthorEntityBinder` w metodzie akcji:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

Ten `ModelBinder` atrybut może służyć do stosowania `AuthorEntityBinder` parametrów do, które nie używają Konwencji domyślnych:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

W tym przykładzie, ponieważ nazwa argumentu nie jest wartością domyślną, jest `authorId` określona w parametrze przy użyciu `ModelBinder` atrybutu. Zarówno kontroler, jak i Metoda akcji są uproszczone w porównaniu z wyszukiwaniem jednostki w metodzie akcji. Logika pobierania autora przy użyciu Entity Framework Core jest przenoszona do spinacza modelu. Może to być znaczące uproszczenie, gdy istnieje kilka metod, które są powiązane z `Author` modelem.

Można zastosować `ModelBinder` atrybut do poszczególnych właściwości modelu (na przykład na ViewModel) lub do parametrów metody akcji, aby określić określony spinacz modelu lub nazwę modelu dla tylko tego typu lub akcji.

### <a name="implementing-a-modelbinderprovider"></a>Implementowanie elementu ModelBinderProvider

Zamiast stosować atrybut, można zaimplementować `IModelBinderProvider` . Jest to sposób implementacji wbudowanych powiązań struktury. Po określeniu typu, na którym działa Twój spinacz, należy określić typ argumentu, który produkuje, a **nie** dane wejściowe zaakceptowane przez spinacz. Następujący dostawca programu Binder współpracuje z `AuthorEntityBinder` . Po dodaniu do kolekcji dostawców MVC nie trzeba używać `ModelBinder` atrybutów z `Author` `Author` parametrami lub typem.

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Uwaga: Poprzedni kod zwraca wartość `BinderTypeModelBinder` . `BinderTypeModelBinder` działa jako fabryka dla segregatorów modelu i zapewnia iniekcję zależności (DI). `AuthorEntityBinder`Program wymaga dostępu EF Core. Użyj, `BinderTypeModelBinder` Jeśli model spinacza wymaga usług z di.

Aby użyć niestandardowego dostawcy segregatorów modelu, Dodaj go w `ConfigureServices` :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

Podczas oceniania powiązań modelu kolekcja dostawców jest sprawdzana w podanej kolejności. Używany jest pierwszy dostawca, który zwraca spinacz zgodny z modelem wejściowym. Dodanie dostawcy do końca kolekcji może więc spowodować wywołanie wbudowanego spinacza modelu, zanim niestandardowy spinacz będzie miał szansę. W tym przykładzie niestandardowy dostawca zostanie dodany do początku kolekcji, aby upewnić się, że jest on zawsze używany dla `Author` argumentów akcji.

### <a name="polymorphic-model-binding"></a>Powiązanie modelu polimorficznego

Powiązanie z różnymi modelami typów pochodnych jest znane jako powiązanie modelu polimorficzne. Powiązanie modelu niestandardowego polimorficzne jest wymagane, gdy wartość żądania musi być powiązana z konkretnym typem modelu pochodnego. Powiązanie modelu polimorficznego:

* Nie jest typowy dla interfejsu API REST, który jest przeznaczony do współpracy ze wszystkimi językami.
* Utrudniają powody dotyczące modeli powiązanych.

Jeśli jednak aplikacja wymaga powiązania modelu polimorficznego, implementacja może wyglądać podobnie do następującego kodu:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Zalecenia i najlepsze rozwiązania

Niestandardowe powiązania modelu:

- Nie należy próbować ustawiać kodów stanu ani zwracać wyników (na przykład nie znaleziono 404). Jeśli powiązanie modelu nie powiedzie się, [Filtr akcji](xref:mvc/controllers/filters) lub logika w samej metodzie akcji powinien obsłużyć błąd.
- Są najbardziej przydatne do eliminowania powtarzających się kodu i krzyżowego obaw z metod akcji.
- Zwykle nie należy używać do konwertowania ciągu na typ niestandardowy, a <xref:System.ComponentModel.TypeConverter> jest zazwyczaj lepszym rozwiązaniem.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Przez [Steve Smith](https://ardalis.com/)

Powiązanie modelu umożliwia działanie kontrolera do pracy bezpośrednio z typami modeli (przekazane jako argumenty metod), a nie żądaniami HTTP. Mapowanie między danymi żądań przychodzących a modelami aplikacji jest obsługiwane przez program Binders. Deweloperzy mogą rozszerzać wbudowaną funkcję powiązania modelu, implementując niestandardowe powiązania modelu (zazwyczaj nie trzeba pisać własnego dostawcy).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Domyślne ograniczenia spinacza modelu

Domyślne powiązania modeli obsługują większość wspólnych typów danych .NET Core i powinny spełniać większość potrzeb deweloperów. Oczekują one powiązania danych tekstowych na podstawie żądania bezpośrednio z typami modeli. Może być konieczne przekształcenie danych wejściowych przed ich powiązaniem. Na przykład, jeśli masz klucz, którego można użyć do wyszukiwania danych modelu. Do pobierania danych opartych na kluczu można użyć spinacza modelu niestandardowego.

## <a name="model-binding-review"></a>Przegląd powiązań modelu

Powiązanie modelu używa określonych definicji dla typów, w których działa. *Typ prosty* jest konwertowany z pojedynczego ciągu w danych wejściowych. *Typ złożony* jest konwertowany z wielu wartości wejściowych. Struktura określa różnice w zależności od istnienia `TypeConverter` . Zalecamy utworzenie konwertera typów, jeśli istnieje proste `string`  ->  `SomeType` Mapowanie, które nie wymaga zasobów zewnętrznych.

Przed utworzeniem własnego spinacza modelu niestandardowego warto przejrzeć sposób implementacji istniejących spinaczy modelu. Rozważ, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> który może być używany do konwertowania ciągów zakodowanych algorytmem Base64 na tablice bajtowe. Tablice bajtowe są często przechowywane jako pliki lub pola obiektów BLOB bazy danych.

### <a name="working-with-the-bytearraymodelbinder"></a>Praca z ByteArrayModelBinder

Ciągi kodowane algorytmem Base64 mogą służyć do reprezentowania danych binarnych. Na przykład obraz może być zakodowany jako ciąg. Przykład zawiera obraz jako ciąg zakodowany algorytmem Base64 w [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC może przyjmować ciąg zakodowany algorytmem Base64 i używać go, `ByteArrayModelBinder` Aby przekonwertować go na tablicę bajtów. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider>Mapowania `byte[]` argumentów do `ByteArrayModelBinder` :

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

Podczas tworzenia własnego spinacza modelu niestandardowego można zaimplementować własny `IModelBinderProvider` Typ lub użyć <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .

Poniższy przykład pokazuje, jak użyć `ByteArrayModelBinder` do przekonwertowania ciągu zakodowanego algorytmem Base64 na `byte[]` a i zapisać wynik do pliku:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

Można OPUBLIKOWAĆ ciąg zakodowany algorytmem Base64 w tej metodzie interfejsu API za pomocą narzędzia, takiego jak [Poster](https://www.getpostman.com/):

![Postman](custom-model-binding/images/postman.png "Postman")

Tak długo, jak spinacz może powiązać dane żądania do odpowiednio nazwanych właściwości lub argumentów, powiązanie modelu zakończy się pomyślnie. Poniższy przykład przedstawia sposób użycia `ByteArrayModelBinder` z modelem widoku:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Przykładowy spinacz modelu niestandardowego

W tej sekcji zaimplementujemy niestandardowy spinacz modelu, który:

- Konwertuje przychodzące dane żądania na argumenty klucza o jednoznacznie określonym typie.
- Używa Entity Framework Core, aby pobrać skojarzoną jednostkę.
- Przekazuje skojarzoną jednostkę jako argument do metody akcji.

Poniższy przykład używa `ModelBinder` atrybutu w `Author` modelu:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

W poprzednim kodzie `ModelBinder` atrybut określa typ `IModelBinder` , który powinien być używany do wiązania `Author` parametrów akcji.

Następująca `AuthorEntityBinder` Klasa wiąże `Author` parametr przez pobranie jednostki ze źródła danych przy użyciu Entity Framework Core i `authorId` :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> Poprzednia `AuthorEntityBinder` Klasa jest przeznaczona do zilustrowania niestandardowego spinacza modelu. Klasa nie jest przeznaczona do zilustrowania najlepszych rozwiązań dotyczących scenariusza wyszukiwania. W celu wyszukania należy powiązać `authorId` bazę danych i zbadać ją w metodzie akcji. To podejście oddziela błędy powiązań modelu z `NotFound` przypadków.

Poniższy kod pokazuje, jak używać `AuthorEntityBinder` w metodzie akcji:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

Ten `ModelBinder` atrybut może służyć do stosowania `AuthorEntityBinder` parametrów do, które nie używają Konwencji domyślnych:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

W tym przykładzie, ponieważ nazwa argumentu nie jest wartością domyślną, jest `authorId` określona w parametrze przy użyciu `ModelBinder` atrybutu. Zarówno kontroler, jak i Metoda akcji są uproszczone w porównaniu z wyszukiwaniem jednostki w metodzie akcji. Logika pobierania autora przy użyciu Entity Framework Core jest przenoszona do spinacza modelu. Może to być znaczące uproszczenie, gdy istnieje kilka metod, które są powiązane z `Author` modelem.

Można zastosować `ModelBinder` atrybut do poszczególnych właściwości modelu (na przykład na ViewModel) lub do parametrów metody akcji, aby określić określony spinacz modelu lub nazwę modelu dla tylko tego typu lub akcji.

### <a name="implementing-a-modelbinderprovider"></a>Implementowanie elementu ModelBinderProvider

Zamiast stosować atrybut, można zaimplementować `IModelBinderProvider` . Jest to sposób implementacji wbudowanych powiązań struktury. Po określeniu typu, na którym działa Twój spinacz, należy określić typ argumentu, który produkuje, a **nie** dane wejściowe zaakceptowane przez spinacz. Następujący dostawca programu Binder współpracuje z `AuthorEntityBinder` . Po dodaniu do kolekcji dostawców MVC nie trzeba używać `ModelBinder` atrybutów z `Author` `Author` parametrami lub typem.

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Uwaga: Poprzedni kod zwraca wartość `BinderTypeModelBinder` . `BinderTypeModelBinder` działa jako fabryka dla segregatorów modelu i zapewnia iniekcję zależności (DI). `AuthorEntityBinder`Program wymaga dostępu EF Core. Użyj, `BinderTypeModelBinder` Jeśli model spinacza wymaga usług z di.

Aby użyć niestandardowego dostawcy segregatorów modelu, Dodaj go w `ConfigureServices` :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

Podczas oceniania powiązań modelu kolekcja dostawców jest sprawdzana w podanej kolejności. Używany jest pierwszy dostawca zwracający spinacz. Dodanie swojego dostawcy do końca kolekcji może spowodować wywołanie wbudowanego spinacza modelu, zanim niestandardowy spinacz będzie miał szansę. W tym przykładzie niestandardowy dostawca zostanie dodany do początku kolekcji, aby upewnić się, że jest on używany dla `Author` argumentów akcji.

### <a name="polymorphic-model-binding"></a>Powiązanie modelu polimorficznego

Powiązanie z różnymi modelami typów pochodnych jest znane jako powiązanie modelu polimorficzne. Powiązanie modelu niestandardowego polimorficzne jest wymagane, gdy wartość żądania musi być powiązana z konkretnym typem modelu pochodnego. Powiązanie modelu polimorficznego:

* Nie jest typowy dla interfejsu API REST, który jest przeznaczony do współpracy ze wszystkimi językami.
* Utrudniają powody dotyczące modeli powiązanych.

Jeśli jednak aplikacja wymaga powiązania modelu polimorficznego, implementacja może wyglądać podobnie do następującego kodu:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Zalecenia i najlepsze rozwiązania

Niestandardowe powiązania modelu:

- Nie należy próbować ustawiać kodów stanu ani zwracać wyników (na przykład nie znaleziono 404). Jeśli powiązanie modelu nie powiedzie się, [Filtr akcji](xref:mvc/controllers/filters) lub logika w samej metodzie akcji powinien obsłużyć błąd.
- Są najbardziej przydatne do eliminowania powtarzających się kodu i krzyżowego obaw z metod akcji.
- Zwykle nie należy używać do konwertowania ciągu na typ niestandardowy, a <xref:System.ComponentModel.TypeConverter> jest zazwyczaj lepszym rozwiązaniem.

::: moniker-end

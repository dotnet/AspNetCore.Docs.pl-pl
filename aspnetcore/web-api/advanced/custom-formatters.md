---
title: Niestandardowe elementy formatujące w ASP.NET Core Web API
author: rick-anderson
description: Dowiedz się, jak tworzyć i używać niestandardowych elementów formatujących dla interfejsów API sieci Web w programie ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 89fbb9d52d99d0eff6656eb6a5a9b4e1c01bc65c
ms.sourcegitcommit: 1833870ad0845326fb764fef1b530a07b9b5b099
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85347089"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Niestandardowe elementy formatujące w ASP.NET Core Web API

[Kirka Larkin](https://twitter.com/serpent5) i [Tomasz Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC obsługuje wymianę danych w interfejsach API sieci Web przy użyciu danych wejściowych i wyjściowych. Wejściowe elementy formatującego są używane przez [powiązanie modelu](xref:mvc/models/model-binding). Wyjściowe elementy formatujące są używane do [formatowania odpowiedzi](xref:web-api/advanced/formatting).

Struktura zawiera wbudowane, wejściowe i wyjściowe elementy formatujące dla JSON i XML. Udostępnia wbudowany program formatujący dane wyjściowe dla zwykłego tekstu, ale nie udostępnia wejściowego programu formatującego dla zwykłego tekstu.

W tym artykule pokazano, jak dodać obsługę dodatkowych formatów, tworząc niestandardowe elementy formatujące. Aby zapoznać się z przykładem niestandardowego programu formatującego wprowadzanie tekstu, zobacz [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) w witrynie GitHub.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Kiedy używać niestandardowych elementów formatujących

Użyj niestandardowego programu formatującego, aby dodać obsługę typu zawartości, który nie jest obsługiwany przez elementy formatujące Bult.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Omówienie korzystania z niestandardowego programu formatującego

Aby utworzyć niestandardowy program formatujący:

* W przypadku serializowania danych wysyłanych do klienta Utwórz klasę wyjściowego programu formatującego.
* Dla danych deserialzing otrzymanych od klienta Utwórz klasę danych wejściowych programu formatującego.
* Dodaj wystąpienia klas programu formatującego do `InputFormatters` kolekcji i `OutputFormatters` w [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

## <a name="how-to-create-a-custom-formatter-class"></a>Jak utworzyć niestandardową klasę programu formatującego

Aby utworzyć program formatujący:

* Utwórz klasę z odpowiedniej klasy bazowej. Przykładowa aplikacja pochodzi z <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> i <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Określ prawidłowe typy nośników i kodowania w konstruktorze.
* Zastąp <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> metody i.
* Zastąp <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> `WriteResponseBodyAsync` metody i.

Poniższy kod przedstawia `VcardOutputFormatter` klasę z [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Pochodny od odpowiedniej klasy bazowej

W przypadku typów multimediów tekstowych (na przykład vCard) pochodzi z klasy bazowej [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) lub [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) .

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

W przypadku typów binarnych pochodzi z klasy bazowej [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) lub [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) .

### <a name="specify-valid-media-types-and-encodings"></a>Określ prawidłowe typy multimediów i kodowania

W konstruktorze Określ prawidłowe typy nośników i kodowania przez dodanie do `SupportedMediaTypes` `SupportedEncodings` kolekcji i.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

Klasa programu formatującego **nie** może użyć iniekcji konstruktora dla jego zależności. Na przykład `ILogger<VcardOutputFormatter>` nie można dodać jako parametru do konstruktora. Aby uzyskać dostęp do usług, należy użyć obiektu kontekstu, który jest przesyłany do metod. Przykład kodu w tym artykule i [przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) pokazują, jak to zrobić.

### <a name="override-canreadtype-and-canwritetype"></a>Przesłoń element overridetype i element unwritetype

Określ typ do deserializacji lub serializacji z, zastępując `CanReadType` `CanWriteType` metody lub. Na przykład tworzenie tekstu vCard z `Contact` typu i na odwrót.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a>Metoda CanWriteResult

W niektórych scenariuszach `CanWriteResult` musi być zastąpiona, a nie `CanWriteType` . Użyj `CanWriteResult` , jeśli spełnione są następujące warunki:

* Metoda Action zwraca klasę modelu.
* Istnieją klasy pochodne, które mogą być zwracane w czasie wykonywania.
* Klasa pochodna zwrócona przez akcję musi być znana w czasie wykonywania.

Załóżmy na przykład, że metoda działania:

* Sygnatura zwraca `Person` Typ.
* Może zwracać `Student` lub `Instructor` Typ, który pochodzi od `Person` . 

Aby program formatujący obsługiwał tylko `Student` obiekty, Sprawdź typ [obiektu](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) w obiekcie kontekstu dostarczonym do `CanWriteResult` metody. Gdy metoda akcji zwraca `IActionResult` :

* Nie jest konieczne korzystanie z programu `CanWriteResult` .
* `CanWriteType`Metoda odbiera typ środowiska uruchomieniowego.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Zastąp ReadRequestBodyAsync i WriteResponseBodyAsync

Deserializacja lub Serializacja jest wykonywana w `ReadRequestBodyAsync` lub `WriteResponseBodyAsync` . Poniższy przykład pokazuje, jak uzyskać usługi z kontenera iniekcji zależności. Nie można uzyskać usług z parametrów konstruktora.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Jak skonfigurować MVC do używania niestandardowego programu formatującego

Aby użyć niestandardowego programu formatującego, Dodaj wystąpienie klasy programu formatującego do `InputFormatters` `OutputFormatters` kolekcji lub.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Elementy formatujące są oceniane w kolejności, w jakiej je wstawiasz. Pierwszeństwo ma pierwszy.

## <a name="the-completed-vcardinputformatter-class"></a>Klasa zakończona `VcardInputFormatter`

Poniższy kod przedstawia `VcardInputFormatter` klasę z [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a>Testowanie aplikacji

[Uruchom przykładową aplikację dla tego artykułu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), która implementuje podstawowe elementy formatujące dane wejściowe i wyjściowe w formacie vCard. Aplikacja odczytuje i zapisuje wizytówki vCard podobne do następujących:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Aby wyświetlić dane wyjściowe w formacie vCard, uruchom aplikację i Wyślij żądanie Get z nagłówkiem Akceptuj `text/vcard` do `https://localhost:5001/api/contacts` .

Aby dodać wizytówkę vCard do kolekcji kontaktów w pamięci:

* Wyślij `Post` żądanie do programu `/api/contacts` przy użyciu narzędzia, takiego jak Poster.
* Ustaw `Content-Type` nagłówek na `text/vcard` .
* Ustaw `vCard` tekst w treści, tak jak w poprzednim przykładzie.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>

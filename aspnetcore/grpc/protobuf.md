---
title: Tworzenie komunikatów protobuf dla aplikacji platformy .NET
author: jamesnk
description: Dowiedz się, jak tworzyć komunikaty protobuf dla aplikacji .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
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
uid: grpc/protobuf
ms.openlocfilehash: 60af1add9ae2f8b2b94bc19b65667d7af91fb122
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102669"
---
# <a name="create-protobuf-messages-for-net-apps"></a>Tworzenie komunikatów protobuf dla aplikacji platformy .NET

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk) i [Mark Rendle](https://twitter.com/markrendle)

gRPC używa [protobuf](https://developers.google.com/protocol-buffers) jako języka definicji interfejsu (IDL). Protobuf IDL to niezależny od języka format służący do określania komunikatów wysyłanych i odbieranych przez usługi gRPC. Komunikaty protobuf są zdefiniowane w `.proto` plikach. W tym dokumencie wyjaśniono, jak koncepcje protobuf są mapowane na platformę .NET.

## <a name="protobuf-messages"></a>Komunikaty Protobuf

Komunikaty są głównym obiektem transferu danych w protobuf. Są one koncepcyjnie podobne do klas platformy .NET.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

Poprzednia definicja komunikatu określa trzy pola jako pary nazwa-wartość. Podobnie jak we właściwościach typu .NET, każde pole ma nazwę i typ. Typ pola może być [typem wartości skalarnej protobuf](#scalar-value-types), np. `int32` lub innym komunikatem.

Oprócz nazwy, każde pole w definicji komunikatu ma unikatowy numer. Numery pól są używane do identyfikowania pól, gdy komunikat jest serializowany do protobuf. Serializowanie niewielkiej liczby jest szybsze niż Serializowanie całej nazwy pola. Ponieważ numery pól identyfikują pole, ważne jest, aby zachować ostrożność przy ich zmianie. Aby uzyskać więcej informacji na temat zmieniania komunikatów protobuf, zobacz <xref:grpc/versioning> .

Po skompilowaniu aplikacji narzędzia protobuf generują typy platformy .NET na podstawie `.proto` plików. `Person`Komunikat generuje klasę .NET:

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Więcej informacji na temat komunikatów protobuf można znaleźć w [przewodniku po języku protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).

## <a name="scalar-value-types"></a>Typy wartości skalarnych

Protobuf obsługuje zakres natywnych typów wartości skalarnych. W poniższej tabeli wymieniono wszystkie ich równoważne typy C#:

| Typ protobuf | Typ C#      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

### <a name="dates-and-times"></a>Daty i godziny

Natywne typy skalarne nie zapewniają wartości daty i godziny, równoważne z. SIECI <xref:System.DateTimeOffset> , <xref:System.DateTime> i <xref:System.TimeSpan> . Te typy można określić za pomocą niektórych rozszerzeń *znanych typów* protobuf. Rozszerzenia te zapewniają obsługę generowania kodu i środowiska uruchomieniowego dla złożonych typów pól na obsługiwanych platformach.

W poniższej tabeli przedstawiono typy daty i godziny:

| Typ .NET        | Nieznany typ protobuf    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

Wygenerowane właściwości klasy C# nie są typami dat i godzin platformy .NET. Właściwości używają `Timestamp` `Duration` klas i w `Google.Protobuf.WellKnownTypes` przestrzeni nazw. Klasy te zapewniają metody konwersji do i z `DateTimeOffset` , `DateTime` i `TimeSpan` .

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> `Timestamp`Typ działa z czasem UTC. `DateTimeOffset` wartości zawsze mają przesunięcie zero, a `DateTime.Kind` Właściwość jest zawsze `DateTimeKind.Utc` .

### <a name="nullable-types"></a>Typy dopuszczające wartości null

Generowanie kodu protobuf dla języka C# używa typów natywnych, takich jak `int` for `int32` . Dlatego wartości są zawsze uwzględniane i nie mogą być `null` .

W przypadku wartości, które wymagają jawności, na przykład `null` używania `int?` w kodzie C#, dobrze znane typy protobuf obejmują otoki, które są kompilowane do typów dopuszczających wartość null. Aby ich użyć, zaimportuj `wrappers.proto` do `.proto` pliku, tak jak w poniższym kodzie:

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

Protobuf używa typów platformy .NET dopuszczających wartości null, na przykład `int?` dla wygenerowanej właściwości wiadomości.

W poniższej tabeli przedstawiono pełną listę typów otoki z równoważnym typem języka C#:

| Typ C#   | Otoka dobrze znanego typu       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a>Miejsca dziesiętne

Protobuf nie obsługuje natywnie typu .NET `decimal` , tylko `double` i `float` . W projekcie protobuf istnieje trwająca dyskusja dotycząca możliwości dodawania standardowego typu dziesiętnego do dobrze znanych typów, z obsługą platformy dla języków i struktur, które je obsługują. Nic nie zostało jeszcze zaimplementowane.

Istnieje możliwość utworzenia definicji komunikatu reprezentującej `decimal` Typ, który działa w celu bezpiecznej serializacji między klientami i serwerami platformy .NET. Jednak deweloperzy na innych platformach będą musieli zrozumieć używany format i zaimplementować ich własny sposób obsługi.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Tworzenie niestandardowego typu dziesiętnego dla protobuf

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

`nanos`Pole reprezentuje wartości z `0.999_999_999` do `-0.999_999_999` . Na przykład `decimal` wartość `1.5m` będzie reprezentowana jako `{ units = 1, nanos = 500_000_000 }` . To dlatego, że `nanos` pole w tym przykładzie używa `sfixed32` typu, który koduje bardziej wydajne niż `int32` w przypadku większych wartości. Jeśli `units` pole jest ujemne, `nanos` pole powinno również być ujemne.

> [!NOTE]
> Istnieje wiele innych algorytmów kodowania `decimal` wartości jako ciągi bajtowe, ale ten komunikat jest łatwiejszy do zrozumienia niż którekolwiek z nich. Na różnych platformach nie ma to wpływ na wartości big-endian ani little-endian.

Konwersję między tym typem a typem BCL można `decimal` zaimplementować w języku C# w następujący sposób:

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a>Kolekcje

### <a name="lists"></a>Listy

Listy w protobuf są określane za pomocą `repeated` słowa kluczowego prefix w polu. Poniższy przykład pokazuje, jak utworzyć listę:

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

W wygenerowanym kodzie `repeated` pola są reprezentowane przez `Google.Protobuf.Collections.RepeatedField<T>` typ ogólny.

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>` implementuje <xref:System.Collections.Generic.IList%601> . Aby można było użyć zapytań LINQ lub przekonwertować je na tablicę lub listę. `RepeatedField<T>` Właściwości nie mają publicznej metody ustawiającej. Elementy należy dodać do istniejącej kolekcji.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>Słowniki

Typ .NET <xref:System.Collections.Generic.IDictionary%602> jest reprezentowany w protobuf przy użyciu `map<key_type, value_type>` .

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

W wygenerowanym kodzie .NET `map` pola są reprezentowane przez `Google.Protobuf.Collections.MapField<TKey, TValue>` typ ogólny. `MapField<TKey, TValue>` implementuje <xref:System.Collections.Generic.IDictionary%602> . Podobnie jak `repeated` właściwości, `map` właściwości nie mają publicznej metody ustawiającej. Elementy należy dodać do istniejącej kolekcji.

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a>Komunikaty niestrukturalne i warunkowe

Protobuf to format wiadomości w pierwszej kolejności. Komunikaty aplikacji, w tym jej pola i typy, muszą być określone w `.proto` plikach podczas kompilowania aplikacji. Kontrakt protobuf — pierwszy projekt jest dobry do wymuszania zawartości wiadomości, ale może ograniczyć scenariusze, w których nie jest wymagany ścisły kontrakt:

* Komunikaty z nieznanymi ładunkiem. Na przykład komunikat z polem, które może zawierać dowolny komunikat.
* Komunikaty warunkowe. Na przykład komunikat zwrócony z usługi gRPC może być wynikiem sukcesu lub wynikiem błędu.
* Wartości dynamiczne. Na przykład komunikat z polem zawierającym kolekcję wartości, podobny do formatu JSON.

Protobuf oferuje funkcje języka i typy do obsługi tych scenariuszy.

### <a name="any"></a>Dowolne

`Any`Typ umożliwia używanie komunikatów jako typów osadzonych bez ich `.proto` definicji. Aby użyć `Any` typu, zaimportuj `any.proto` .

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a>Oneof

`oneof` pola są funkcją języka. Kompilator obsługuje `oneof` słowo kluczowe, gdy generuje klasę komunikatów. Użycie `oneof` do określenia komunikatu odpowiedzi, który może zwrócić `Person` lub `Error` może wyglądać następująco:

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

Pola w `oneof` zestawie muszą mieć unikatowe numery pól w ogólnej deklaracji komunikatu.

W przypadku korzystania `oneof` z programu wygenerowany kod C# zawiera wyliczenie, które określa, które z pól zostały ustawione. Możesz przetestować Wyliczenie, aby znaleźć pole, które zostało ustawione. Pola, które nie ustawiają `null` wartości zwracanej lub domyślnej, zamiast zgłaszać wyjątek.

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a>Wartość

`Value`Typ reprezentuje wartość wpisaną dynamicznie. Może to być albo `null` Liczba, ciąg, wartość logiczna, słownik wartości ( `Struct` ) lub lista wartości ( `ValueList` ). `Value` jest dobrze znanym typem protobuf, który korzysta z wcześniej omówionej `oneof` funkcji. Aby użyć `Value` typu, zaimportuj `struct.proto` .

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

`Value`Bezpośrednie używanie może być pełne. Alternatywnym sposobem korzystania `Value` z programu jest wbudowana obsługa protobuf na potrzeby mapowania komunikatów do formatu JSON. Protobuf `JsonFormatter` i `JsonWriter` typy mogą być używane z dowolnym komunikatem protobuf. `Value` jest szczególnie dobrze dostosowany do konwersji do i z formatu JSON.

Jest to odpowiednik w formacie JSON poprzedniego kodu:

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przewodnik po języku protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>

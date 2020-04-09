---
title: Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC
author: juntaoluo
description: Dowiedz się więcej o dodawaniu, aktualizowaniu, usuwaniu i wystawianiu odwołań protobuf za pomocą globalnego narzędzia dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667336"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC

Przez [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`jest globalnym narzędziem .NET Core do zarządzania odwołaniami [protobuf (*.proto*)](xref:grpc/basics#proto-file) w ramach projektu gRPC .NET. Narzędzie może służyć do dodawania, odświeżania, usuwania i listy odniesień protobuf.

## <a name="installation"></a>Instalacja

Aby zainstalować `dotnet-grpc` narzędzie [.NET Core Global Tool](/dotnet/core/tools/global-tools), uruchom następujące polecenie:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Dodawanie odwołań

`dotnet-grpc`może służyć do dodawania odwołań `<Protobuf />` protobuf jako elementów do pliku *csproj:*

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Odwołania Protobuf są używane do generowania zasobów klienta i/lub serwera c#. Narzędzie `dotnet-grpc` może:

* Utwórz odwołanie Protobuf z plików lokalnych na dysku.
* Utwórz odwołanie Protobuf ze zdalnego pliku określonego przez adres URL.
* Upewnij się, że do projektu są dodawane poprawne zależności pakietu gRPC.

Na przykład `Grpc.AspNetCore` pakiet jest dodawany do aplikacji sieci web. `Grpc.AspNetCore`zawiera biblioteki serwerów i klientów gRPC oraz obsługę narzędzi. Alternatywnie `Grpc.Net.Client`, `Grpc.Tools` i `Google.Protobuf` pakiety, które zawierają tylko biblioteki klienta gRPC i obsługę narzędzi, są dodawane do aplikacji konsoli.

### <a name="add-file"></a>Dodaj plik

Polecenie `add-file` służy do dodawania plików lokalnych na dysku jako odwołań Protobuf. Ścieżki plików pod warunkiem:

* Może być względem bieżącego katalogu lub ścieżek bezwzględnych.
* Może zawierać dzikie karty do [globbingu](https://wikipedia.org/wiki/Glob_(programming))plików opartego na wzorcach .

Jeśli wszystkie pliki znajdują się poza `Link` katalogiem projektu, element jest `Protos` dodawany do wyświetlania pliku w folderze w programie Visual Studio.

### <a name="usage"></a>Sposób użycia

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argumenty

| Argument | Opis |
|-|-|
| files | Odwołania do pliku protobuf. Mogą to być ścieżki do glob dla lokalnych plików protobuf. |

#### <a name="options"></a>Opcje

| Opcja krótka | Opcja długa | Opis |
|-|-|-|
| -p | --projekt | Ścieżka do pliku projektu do działania. Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.
| -s | --usługi | Typ usług gRPC, które powinny być generowane. Jeśli `Default` jest `Both` określony, jest używany `Client` dla projektów sieci Web i jest używany dla projektów innych niż Web. Akceptowane wartości `Both`to `Client` `Default`, `None` `Server`, , , .
| -i | --additional-import-dirs | Dodatkowe katalogi, które mają być używane podczas rozwiązywania importu plików protobuf. Jest to oddzielona od średnikowa lista ścieżek.
| | --dostęp | Modyfikator dostępu do użycia dla wygenerowanych klas Języka C#. Wartością domyślną jest `Public`. Akceptowane wartości `Internal` są `Public`i .

### <a name="add-url"></a>Dodaj adres URL

Polecenie `add-url` służy do dodawania pliku zdalnego określonego przez źródłowy adres URL jako odwołanie protobuf. Aby określić, gdzie pobrać plik zdalny, należy podać ścieżkę pliku. Ścieżka pliku może być względem bieżącego katalogu lub ścieżki bezwzględnej. Jeśli ścieżka pliku znajduje się poza `Link` katalogiem projektu, element jest dodawany do wyświetlania pliku w folderze `Protos` wirtualnym w programie Visual Studio.

### <a name="usage"></a>Sposób użycia

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argumenty

| Argument | Opis |
|-|-|
| url | Adres URL do zdalnego pliku protobuf. |

#### <a name="options"></a>Opcje

| Opcja krótka | Opcja długa | Opis |
|-|-|-|
| -o | --output | Określa ścieżkę pobierania zdalnego pliku protobuf. Ta opcja jest wymagana.
| -p | --projekt | Ścieżka do pliku projektu do działania. Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.
| -s | --usługi | Typ usług gRPC, które powinny być generowane. Jeśli `Default` jest `Both` określony, jest używany `Client` dla projektów sieci Web i jest używany dla projektów innych niż Web. Akceptowane wartości `Both`to `Client` `Default`, `None` `Server`, , , .
| -i | --additional-import-dirs | Dodatkowe katalogi, które mają być używane podczas rozwiązywania importu plików protobuf. Jest to oddzielona od średnikowa lista ścieżek.
| | --dostęp | Modyfikator dostępu do użycia dla wygenerowanych klas Języka C#. Wartością `Public`domyślną jest . Akceptowane wartości `Internal` są `Public`i .

## <a name="remove"></a>Remove

Polecenie `remove` służy do usuwania odwołań Protobuf z pliku *csproj.* Polecenie akceptuje argumenty ścieżki i źródłowe adresy URL jako argumenty. Narzędzie:

* Usuwa tylko odwołanie Protobuf.
* Nie usuwa pliku *.proto,* nawet jeśli został pierwotnie pobrany ze zdalnego adresu URL.

### <a name="usage"></a>Sposób użycia

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argumenty

| Argument | Opis |
|-|-|
| odwołania | Adresy URL lub ścieżki plików odwołań protobuf do usunięcia. |

### <a name="options"></a>Opcje

| Opcja krótka | Opcja długa | Opis |
|-|-|-|
| -p | --projekt | Ścieżka do pliku projektu do działania. Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.

## <a name="refresh"></a>Odświeżanie

Polecenie `refresh` służy do aktualizowania odwołania zdalnego z najnowszą zawartością ze źródłowego adresu URL. Zarówno ścieżka pliku pobierania, jak i źródłowy adres URL mogą służyć do określania odwołania, które ma zostać zaktualizowane. Uwaga:

* Skróty zawartości pliku są porównywane w celu ustalenia, czy plik lokalny powinien zostać zaktualizowany.
* Nie są porównywane żadne informacje o sygnaturach czasowych.

Narzędzie zawsze zastępuje plik lokalny plikiem zdalnym, jeśli potrzebna jest aktualizacja.

### <a name="usage"></a>Sposób użycia

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argumenty

| Argument | Opis |
|-|-|
| odwołania | Adresy URL lub ścieżki plików do zdalnych odwołań protobuf, które powinny zostać zaktualizowane. Pozostaw ten argument pusty, aby odświeżyć wszystkie odwołania zdalne. |

### <a name="options"></a>Opcje

| Opcja krótka | Opcja długa | Opis |
|-|-|-|
| -p | --projekt | Ścieżka do pliku projektu do działania. Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.
| | --dry-run | Wyprowadza listę plików, które zostaną zaktualizowane bez pobierania nowej zawartości.

## <a name="list"></a>List

Polecenie `list` służy do wyświetlania wszystkich odwołań Protobuf w pliku projektu. Jeśli wszystkie wartości kolumny są wartościami domyślnymi, kolumna może zostać pominięta.

### <a name="usage"></a>Sposób użycia

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Opcje

| Opcja krótka | Opcja długa | Opis |
|-|-|-|
| -p | --projekt | Ścieżka do pliku projektu do działania. Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>

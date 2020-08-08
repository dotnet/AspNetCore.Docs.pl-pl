---
title: Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC
author: juntaoluo
description: Dowiedz się więcej na temat dodawania, aktualizowania, usuwania i wyświetlania protobuf odwołań za pomocą narzędzia dotnet-GRPC Global.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: d41958d586f54d5944af187933f2b0248f763171
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016132"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC

Przez [Jan Luo](https://github.com/juntaoluo)

`dotnet-grpc`jest globalnym narzędziem platformy .NET Core do zarządzania odwołaniami [protobuf (*. proto*)](xref:grpc/basics#proto-file) w ramach projektu .NET gRPC. Narzędzie może służyć do dodawania, odświeżania, usuwania i wyświetlania listy odwołań protobuf.

## <a name="installation"></a>Instalacja

Aby zainstalować `dotnet-grpc` [Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools), uruchom następujące polecenie:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Dodaj odwołania

`dotnet-grpc`może służyć do dodawania odwołań protobuf jako `<Protobuf />` elementów do pliku *. csproj* :

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Odwołania do protobuf są używane do generowania zasobów klienta i/lub serwera w języku C#. To `dotnet-grpc` Narzędzie może:

* Utwórz odwołanie protobuf z plików lokalnych na dysku.
* Utwórz odwołanie protobuf z pliku zdalnego określonego przez adres URL.
* Upewnij się, że odpowiednie zależności pakietu gRPC są dodawane do projektu.

Na przykład `Grpc.AspNetCore` pakiet zostanie dodany do aplikacji sieci Web. `Grpc.AspNetCore`zawiera biblioteki serwera gRPC i klienta oraz obsługę narzędzi. Alternatywnie, `Grpc.Net.Client` `Grpc.Tools` pakiety i, `Google.Protobuf` które zawierają tylko biblioteki i narzędzia klienckie gRPC, są dodawane do aplikacji konsoli.

### <a name="add-file"></a>Dodaj plik

`add-file`Polecenie służy do dodawania plików lokalnych na dysku jako odwołań protobuf. Podane ścieżki plików:

* Może być względna względem bieżącego katalogu lub ścieżek bezwzględnych.
* Mogą zawierać symbole wieloznaczne dla [obsługi symboli wieloznacznych](https://wikipedia.org/wiki/Glob_(programming))plików opartych na wzorcu.

Jeśli jakiekolwiek pliki znajdują się poza katalogiem projektu, `Link` element zostanie dodany w celu wyświetlenia pliku w folderze `Protos` w programie Visual Studio.

### <a name="usage"></a>Użycie

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argumenty

| Argument | Opis |
|-|-|
| files | Odwołuje się do pliku protobuf. Mogą to być ścieżki do globalizowania dla lokalnych plików protobuf. |

#### <a name="options"></a>Opcje

| Opcja krótka | Opcja Long | Opis |
|-|-|-|
| -p | --projekt | Ścieżka do pliku projektu, na którym mają być wykonywane działania. Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.
| -s | --usługi | Typ usług gRPC, które mają zostać wygenerowane. Jeśli `Default` jest określony, `Both` jest używany dla projektów sieci Web i `Client` jest używany dla projektów nieopartych na sieci Web. Akceptowane wartości to,,,, `Both` `Client` `Default` `None` `Server` .
| -i | --dodatkowe-import-katalogów | Dodatkowe katalogi, które mają być używane podczas rozpoznawania importu dla plików protobuf. Jest to rozdzielana średnikami lista ścieżek.
| | --dostęp | Modyfikator dostępu, który ma być używany dla wygenerowanych klas języka C#. Wartość domyślna to `Public`. Akceptowane wartości to `Internal` i `Public` .

### <a name="add-url"></a>Dodaj adres URL

`add-url`Polecenie służy do dodawania pliku zdalnego określonego przez źródłowy adres URL jako odwołanie protobuf. Należy podać ścieżkę pliku, aby określić, gdzie pobrać plik zdalny. Ścieżka pliku może być względna względem bieżącego katalogu lub ścieżki bezwzględnej. Jeśli ścieżka pliku znajduje się poza katalogiem projektu, `Link` element zostanie dodany w celu wyświetlenia pliku w folderze wirtualnym `Protos` w programie Visual Studio.

### <a name="usage"></a>Użycie

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argumenty

| Argument | Opis |
|-|-|
| url | Adres URL pliku protobuf zdalnego. |

#### <a name="options"></a>Opcje

| Opcja krótka | Opcja Long | Opis |
|-|-|-|
| -o | --output | Określa ścieżkę pobierania pliku protobuf zdalnego. Ta opcja jest wymagana.
| -p | --projekt | Ścieżka do pliku projektu, na którym mają być wykonywane działania. Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.
| -s | --usługi | Typ usług gRPC, które mają zostać wygenerowane. Jeśli `Default` jest określony, `Both` jest używany dla projektów sieci Web i `Client` jest używany dla projektów nieopartych na sieci Web. Akceptowane wartości to,,,, `Both` `Client` `Default` `None` `Server` .
| -i | --dodatkowe-import-katalogów | Dodatkowe katalogi, które mają być używane podczas rozpoznawania importu dla plików protobuf. Jest to rozdzielana średnikami lista ścieżek.
| | --dostęp | Modyfikator dostępu, który ma być używany dla wygenerowanych klas języka C#. Wartość domyślna to `Public` . Akceptowane wartości to `Internal` i `Public` .

## <a name="remove"></a>Usuń

`remove`Polecenie służy do usuwania odwołań protobuf z pliku *csproj* . Polecenie akceptuje argumenty ścieżki i źródłowe adresy URL jako argumenty. Narzędzie:

* Usuwa odwołanie protobuf.
* Nie usuwa pliku *. proto* , nawet jeśli został pierwotnie pobrany ze zdalnego adresu URL.

### <a name="usage"></a>Użycie

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argumenty

| Argument | Opis |
|-|-|
| odwołania | Adresy URL lub ścieżki plików odwołań protobuf do usunięcia. |

### <a name="options"></a>Opcje

| Opcja krótka | Opcja Long | Opis |
|-|-|-|
| -p | --projekt | Ścieżka do pliku projektu, na którym mają być wykonywane działania. Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.

## <a name="refresh"></a>Odśwież

`refresh`Polecenie służy do aktualizowania odwołania zdalnego z najnowszą zawartością ze źródłowego adresu URL. Aby określić odwołanie, które ma zostać zaktualizowane, można użyć zarówno ścieżki pliku pobierania, jak i źródłowego adresu URL. Uwaga:

* Skróty zawartości plików są porównywane, aby określić, czy plik lokalny powinien zostać zaktualizowany.
* Nie są porównywane żadne informacje o znaczniku czasu.

Narzędzie zawsze zastępuje plik lokalny plikiem zdalnym, jeśli jest wymagana aktualizacja.

### <a name="usage"></a>Użycie

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argumenty

| Argument | Opis |
|-|-|
| odwołania | Adresy URL lub ścieżki plików do zdalnych odwołań protobuf, które powinny zostać zaktualizowane. Pozostaw ten argument pusty, aby odświeżyć wszystkie odwołania zdalne. |

### <a name="options"></a>Opcje

| Opcja krótka | Opcja Long | Opis |
|-|-|-|
| -p | --projekt | Ścieżka do pliku projektu, na którym mają być wykonywane działania. Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.
| | --osuszyć-Run | Wyświetla listę plików, które zostaną zaktualizowane bez pobierania nowej zawartości.

## <a name="list"></a>Lista

`list`Polecenie jest używane do wyświetlania wszystkich odwołań protobuf w pliku projektu. Jeśli wszystkie wartości w kolumnie są wartościami domyślnymi, kolumna może zostać pominięta.

### <a name="usage"></a>Użycie

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Opcje

| Opcja krótka | Opcja Long | Opis |
|-|-|-|
| -p | --projekt | Ścieżka do pliku projektu, na którym mają być wykonywane działania. Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>

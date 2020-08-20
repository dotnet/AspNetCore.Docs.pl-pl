---
title: Dostawcy plików w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core abstrakcji dostępu do systemu plików przy użyciu dostawców plików.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: 30c28e7bd4cd9c926b157f5a7b9e6688bd5b9b9a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634608"
---
# <a name="file-providers-in-aspnet-core"></a>Dostawcy plików w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core abstrakcję dostępu systemu plików przy użyciu dostawców plików. Dostawcy plików są używani w całym ASP.NET Core Framework. Na przykład:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> udostępnia [katalog główny zawartości](xref:fundamentals/index#content-root) aplikacji i [katalogu głównego sieci Web](xref:fundamentals/index#web-root) jako `IFileProvider` typy.
* [Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.
* [Razor](xref:mvc/views/razor) używa dostawców plików do lokalizowania stron i widoków.
* Narzędzia .NET Core używają dostawców plików i wzorców globalizowania, aby określić, które pliki powinny zostać opublikowane.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfejsy dostawcy plików

Podstawowy interfejs to <xref:Microsoft.Extensions.FileProviders.IFileProvider> . `IFileProvider` udostępnia metody:

* Uzyskaj informacje o pliku ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).
* Uzyskaj informacje o katalogu ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ).
* Skonfiguruj powiadomienia o zmianach (przy użyciu <xref:Microsoft.Extensions.Primitives.IChangeToken> ).

`IFileInfo` zapewnia metody i właściwości do pracy z plikami:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (w bajtach)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> dniu

Można odczytać z pliku za pomocą <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> metody.

Przykładowa aplikacja *FileProviderSample* pokazuje, jak skonfigurować dostawcę plików w programie `Startup.ConfigureServices` do użycia w całej aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementacje dostawcy plików

W poniższej tabeli wymieniono implementacje programu `IFileProvider` .

| Implementacja | Opis |
| -------------- | ----------- |
| [CompositeFileProvider](#compositefileprovider) | Służy do zapewniania połączonego dostępu do plików i katalogów z co najmniej jednego dostawcy. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Służy do uzyskiwania dostępu do plików osadzonych w zestawach. |
| [PhysicalFileProvider](#physicalfileprovider) | Służy do uzyskiwania dostępu do plików fizycznych systemu. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Zapewnia dostęp do fizycznego systemu plików. `PhysicalFileProvider` używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i zakresy wszystkie ścieżki do katalogu i jego elementów podrzędnych. Takie Określanie zakresu uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego elementami podrzędnymi. Najbardziej typowym scenariuszem tworzenia i używania elementu `PhysicalFileProvider` jest zażądanie `IFileProvider` w konstruktorze przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).

W przypadku bezpośredniego tworzenia wystąpienia tego dostawcy wymagana jest bezwzględna ścieżka do katalogu i służy jako ścieżka podstawowa dla wszystkich żądań wysyłanych przy użyciu dostawcy. Wzorce globalizowania nie są obsługiwane w ścieżce katalogu.

Poniższy kod przedstawia sposób korzystania `PhysicalFileProvider` z programu w celu uzyskania zawartości katalogu i informacji o pliku:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Typy w poprzednim przykładzie:

* `provider` jest `IFileProvider` .
* `contents` jest `IDirectoryContents` .
* `fileInfo` jest `IFileInfo` .

Dostawca plików może służyć do iteracji przez katalog określony przez `applicationRoot` lub wywołanie `GetFileInfo` w celu uzyskania informacji o pliku. Nie można przekazywać wzorców globalizowania do `GetFileInfo` metody. Dostawca plików nie ma dostępu poza `applicationRoot` katalogiem.

Przykładowa aplikacja *FileProviderSample* tworzy dostawcę w `Startup.ConfigureServices` metodzie przy użyciu <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Służy <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> do uzyskiwania dostępu do plików osadzonych w zestawach. `ManifestEmbeddedFileProvider`Używa manifestu skompilowanego w zestawie, aby odtworzyć oryginalne ścieżki osadzonych plików.

Aby wygenerować manifest osadzonych plików:

1. Dodaj pakiet NuGet [Microsoft. Extensions. FileProviders. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) do projektu.
1. Ustaw `<GenerateEmbeddedFilesManifest>` Właściwość na wartość `true` . Określ pliki do osadzenia przy użyciu [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Użyj [wzorców globalizowania](#glob-patterns) , aby określić jeden lub więcej plików do osadzenia w zestawie.

Aplikacja Przykładowa *FileProviderSample* tworzy `ManifestEmbeddedFileProvider` i przekazuje aktualnie wykonywany zestaw do jego konstruktora.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Dodatkowe przeciążenia umożliwiają:

* Określ względną ścieżkę pliku.
* Zakres plików do daty ostatniej modyfikacji.
* Nazwij osadzony zasób zawierający manifest pliku osadzonego.

| Występują | Opis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Akceptuje opcjonalny `root` parametr ścieżki względnej. Określ do `root` zakresu wywołania do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> tych zasobów w ramach podanej ścieżki. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akceptuje opcjonalny `root` parametr ścieżki względnej i `lastModified` parametr Date ( <xref:System.DateTimeOffset> ). `lastModified`Data zakresy daty ostatniej modyfikacji dla <xref:Microsoft.Extensions.FileProviders.IFileInfo> wystąpień zwracanych przez <xref:Microsoft.Extensions.FileProviders.IFileProvider> . |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akceptuje opcjonalną `root` ścieżkę względną, `lastModified` datę i `manifestName` parametry. `manifestName`Reprezentuje nazwę zasobu osadzonego zawierającego manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider>Łączy `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami z wielu dostawców. Podczas tworzenia `CompositeFileProvider` , należy przekazać co najmniej jedno `IFileProvider` wystąpienie do jego konstruktora.

W przykładowej aplikacji *FileProviderSample* `PhysicalFileProvider` i `ManifestEmbeddedFileProvider` Podaj pliki do `CompositeFileProvider` zarejestrowanego w kontenerze usługi aplikacji. W metodzie projektu znaleziono następujący kod `Startup.ConfigureServices` :

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Obejrzyj zmiany

<xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>Metoda zawiera scenariusz, aby obejrzeć zmiany w jednym lub kilku plikach lub katalogach. `Watch`Metoda:

* Akceptuje ciąg ścieżki pliku, który może używać [wzorców globalizowania](#glob-patterns) do określenia wielu plików.
* Zwraca wartość <xref:Microsoft.Extensions.Primitives.IChangeToken> .

Otrzymany token zmiany ujawnia:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Właściwość, którą można sprawdzić w celu ustalenia, czy wprowadzono zmianę.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Wywołuje się, gdy zostaną wykryte zmiany do określonego ciągu ścieżki. Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę. Aby włączyć monitorowanie stałe, użyj <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazanego poniżej) lub ponownie Utwórz `IChangeToken` wystąpienia w odpowiedzi na zmiany.

Przykładowa aplikacja *WatchConsole* zapisuje komunikat za każdym razem, gdy plik *txt* w katalogu *TextFiles* zostanie zmodyfikowany:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą niezawodnie wysyłać powiadomienia o zmianach. Ustaw `DOTNET_USE_POLLING_FILE_WATCHER` zmienną środowiskową na `1` lub `true` , aby sondować system plików pod kątem zmian co cztery sekundy (nie można skonfigurować).

### <a name="glob-patterns"></a>Wzorce globalizowania

Ścieżki systemu plików używają wzorców symboli wieloznacznych o nazwie *globalizowania (lub obsługi symboli wieloznacznych)*. Określ grupy plików z tymi wzorcami. Dwa symbole wieloznaczne to `*` `**` :

**`*`**  
Dopasowuje wszystko na bieżącym poziomie folderu, dowolnej nazwie pliku lub dowolnym rozszerzeniu pliku. Dopasowania są kończone przez `/` `.` znaki i w ścieżce pliku.

**`**`**  
Dopasowuje wszystko na wielu poziomach katalogów. Może służyć do rekursywnego dopasowania wielu plików w hierarchii katalogów.

W poniższej tabeli przedstawiono typowe przykłady wzorców globalizowania.

|Wzorce  |Opis  |
|---------|---------|
|`directory/file.txt`|Dopasowuje określony plik w określonym katalogu.|
|`directory/*.txt`|Dopasowuje wszystkie pliki z rozszerzeniem *. txt* w określonym katalogu.|
|`directory/*/appsettings.json`|Dopasowuje wszystkieappsettings.jsplików w katalogach dokładnie * o* jeden poziom poniżej folderu *katalogu* .|
|`directory/**/*.txt`|Dopasowuje wszystkie pliki z rozszerzeniem *. txt* , które znajdują się w dowolnym miejscu w folderze *katalogu* .|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core abstrakcję dostępu systemu plików przy użyciu dostawców plików. Dostawcy plików są używani w całym ASP.NET Core Framework:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> udostępnia [katalog główny zawartości](xref:fundamentals/index#content-root) aplikacji i [katalogu głównego sieci Web](xref:fundamentals/index#web-root) jako `IFileProvider` typy.
* [Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.
* [Razor](xref:mvc/views/razor) używa dostawców plików do lokalizowania stron i widoków.
* Narzędzia .NET Core używają dostawców plików i wzorców globalizowania, aby określić, które pliki powinny zostać opublikowane.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfejsy dostawcy plików

Podstawowy interfejs to <xref:Microsoft.Extensions.FileProviders.IFileProvider> . `IFileProvider` udostępnia metody:

* Uzyskaj informacje o pliku ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).
* Uzyskaj informacje o katalogu ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ).
* Skonfiguruj powiadomienia o zmianach (przy użyciu <xref:Microsoft.Extensions.Primitives.IChangeToken> ).

`IFileInfo` zapewnia metody i właściwości do pracy z plikami:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (w bajtach)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> dniu

Można odczytać z pliku za pomocą metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .

Przykładowa aplikacja pokazuje, jak skonfigurować dostawcę plików w programie `Startup.ConfigureServices` do użycia w całej aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementacje dostawcy plików

Dostępne są trzy implementacje programu `IFileProvider` .

| Implementacja | Opis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Dostawca fizyczny jest używany do uzyskiwania dostępu do plików fizycznych systemu. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Dostawca osadzony manifestu służy do uzyskiwania dostępu do plików osadzonych w zestawach. |
| [CompositeFileProvider](#compositefileprovider) | Dostawca złożony służy do zapewniania połączonego dostępu do plików i katalogów z jednego lub kilku innych dostawców. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Zapewnia dostęp do fizycznego systemu plików. `PhysicalFileProvider` używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i zakresy wszystkie ścieżki do katalogu i jego elementów podrzędnych. Takie Określanie zakresu uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego elementami podrzędnymi. Najbardziej typowym scenariuszem tworzenia i używania elementu `PhysicalFileProvider` jest zażądanie `IFileProvider` w konstruktorze przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).

W przypadku bezpośredniego tworzenia wystąpienia tego dostawcy ścieżka katalogu jest wymagana i służy jako ścieżka podstawowa dla wszystkich żądań wysyłanych przy użyciu dostawcy.

Poniższy kod przedstawia sposób tworzenia `PhysicalFileProvider` i używania go do uzyskiwania informacji o zawartości i pliku katalogu:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typy w poprzednim przykładzie:

* `provider` jest `IFileProvider` .
* `contents` jest `IDirectoryContents` .
* `fileInfo` jest `IFileInfo` .

Dostawca plików może służyć do iteracji przez katalog określony przez `applicationRoot` lub wywołanie `GetFileInfo` w celu uzyskania informacji o pliku. Dostawca plików nie ma dostępu poza `applicationRoot` katalogiem.

Przykładowa aplikacja tworzy dostawcę w `Startup.ConfigureServices` klasie aplikacji za pomocą [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Służy <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> do uzyskiwania dostępu do plików osadzonych w zestawach. `ManifestEmbeddedFileProvider`Używa manifestu skompilowanego w zestawie, aby odtworzyć oryginalne ścieżki osadzonych plików.

Aby wygenerować manifest osadzonych plików, należy ustawić `<GenerateEmbeddedFilesManifest>` Właściwość na `true` . Określ pliki do osadzenia przy użyciu [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Użyj [wzorców globalizowania](#glob-patterns) , aby określić jeden lub więcej plików do osadzenia w zestawie.

Przykładowa aplikacja tworzy `ManifestEmbeddedFileProvider` i przekazuje aktualnie wykonywany zestaw do jego konstruktora.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Dodatkowe przeciążenia umożliwiają:

* Określ względną ścieżkę pliku.
* Zakres plików do daty ostatniej modyfikacji.
* Nazwij osadzony zasób zawierający manifest pliku osadzonego.

| Występują | Opis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Akceptuje opcjonalny `root` parametr ścieżki względnej. Określ do `root` zakresu wywołania do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> tych zasobów w ramach podanej ścieżki. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akceptuje opcjonalny `root` parametr ścieżki względnej i `lastModified` parametr Date ( <xref:System.DateTimeOffset> ). `lastModified`Data zakresy daty ostatniej modyfikacji dla <xref:Microsoft.Extensions.FileProviders.IFileInfo> wystąpień zwracanych przez <xref:Microsoft.Extensions.FileProviders.IFileProvider> . |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akceptuje opcjonalną `root` ścieżkę względną, `lastModified` datę i `manifestName` parametry. `manifestName`Reprezentuje nazwę zasobu osadzonego zawierającego manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider>Łączy `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami z wielu dostawców. Podczas tworzenia `CompositeFileProvider` , należy przekazać co najmniej jedno `IFileProvider` wystąpienie do jego konstruktora.

W przykładowej aplikacji a `PhysicalFileProvider` i `ManifestEmbeddedFileProvider` udostępniaj pliki `CompositeFileProvider` zarejestrowane w kontenerze usługi aplikacji:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Obejrzyj zmiany

Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) zawiera scenariusz, aby obejrzeć zmiany w jednym lub kilku plikach lub katalogach. `Watch` akceptuje ciąg ścieżki, który może używać [wzorców globalizowania](#glob-patterns) do określenia wielu plików. `Watch` Zwraca wartość <xref:Microsoft.Extensions.Primitives.IChangeToken> . Token zmiany ujawnia:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Właściwość, którą można sprawdzić w celu ustalenia, czy wprowadzono zmianę.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Wywołuje się, gdy zostaną wykryte zmiany do określonego ciągu ścieżki. Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę. Aby włączyć monitorowanie stałe, użyj <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazanego poniżej) lub ponownie Utwórz `IChangeToken` wystąpienia w odpowiedzi na zmiany.

W aplikacji przykładowej Aplikacja konsolowa *WatchConsole* jest skonfigurowana do wyświetlania komunikatu za każdym razem, gdy plik tekstowy zostanie zmodyfikowany:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą niezawodnie wysyłać powiadomienia o zmianach. Ustaw `DOTNET_USE_POLLING_FILE_WATCHER` zmienną środowiskową na `1` lub `true` , aby sondować system plików pod kątem zmian co cztery sekundy (nie można skonfigurować).

## <a name="glob-patterns"></a>Wzorce globalizowania

Ścieżki systemu plików używają wzorców symboli wieloznacznych o nazwie *globalizowania (lub obsługi symboli wieloznacznych)*. Określ grupy plików z tymi wzorcami. Dwa symbole wieloznaczne to `*` `**` :

**`*`**  
Dopasowuje wszystko na bieżącym poziomie folderu, dowolnej nazwie pliku lub dowolnym rozszerzeniu pliku. Dopasowania są kończone przez `/` `.` znaki i w ścieżce pliku.

**`**`**  
Dopasowuje wszystko na wielu poziomach katalogów. Może służyć do rekursywnego dopasowania wielu plików w hierarchii katalogów.

**Przykłady wzorców globalizowania**

**`directory/file.txt`**  
Dopasowuje określony plik w określonym katalogu.

**`directory/*.txt`**  
Dopasowuje wszystkie pliki z rozszerzeniem *. txt* w określonym katalogu.

**`directory/*/appsettings.json`**  
Dopasowuje wszystkie `appsettings.json` pliki w katalogach dokładnie o jeden poziom poniżej folderu *katalogu* .

**`directory/**/*.txt`**  
Dopasowuje wszystkie pliki z rozszerzeniem *. txt* , które znajdują się w dowolnym miejscu w folderze *katalogu* .

::: moniker-end

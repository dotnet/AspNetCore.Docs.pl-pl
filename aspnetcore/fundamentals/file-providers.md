---
title: Dostawcy plików w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core abstrakcji dostępu do systemu plików za pomocą dostawców plików.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/file-providers
ms.openlocfilehash: 25607bd534cae05a6c6b11fa6d8902faa3c0684c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751101"
---
# <a name="file-providers-in-aspnet-core"></a>Dostawcy plików w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core abstrakcje dostęp do systemu plików za pomocą dostawców plików. Dostawcy plików są używane w całej ASP.NET core framework. Przykład:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>udostępnia katalog główny [zawartości](xref:fundamentals/index#content-root) aplikacji i `IFileProvider` katalog główny sieci [Web](xref:fundamentals/index#web-root) jako typy.
* [Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.
* [Razor](xref:mvc/views/razor) używa dostawców plików do lokalizowania stron i widoków.
* Narzędzia .NET Core używają dostawców plików i wzorców glob, aby określić, które pliki powinny zostać opublikowane.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfejsy dostawcy plików

Podstawowym interfejsem <xref:Microsoft.Extensions.FileProviders.IFileProvider>jest . `IFileProvider`udostępnia metody na:

* Uzyskiwanie informacji<xref:Microsoft.Extensions.FileProviders.IFileInfo>o pliku ( ).
* Uzyskiwanie informacji<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>o katalogu ( ).
* Konfigurowanie powiadomień o <xref:Microsoft.Extensions.Primitives.IChangeToken>zmianach (przy użyciu ).

`IFileInfo`zapewnia metody i właściwości do pracy z plikami:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(w bajtach)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Data

Można odczytać z pliku <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> przy użyciu metody.

*FileProviderPróbuj* przykładową aplikację pokazuje, jak skonfigurować `Startup.ConfigureServices` dostawcę plików do użytku w całej aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementacje dostawcy plików

W poniższej tabeli `IFileProvider`wymieniono implementacje programu .

| Wdrażanie | Opis |
| -------------- | ----------- |
| [CompositeFileProvider](#compositefileprovider) | Służy do zapewnienia połączonego dostępu do plików i katalogów od jednego lub więcej innych dostawców. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Służy do uzyskiwania dostępu do plików osadzonych w złożeniach. |
| [PhysicalFileProvider](#physicalfileprovider) | Służy do uzyskiwania dostępu do plików fizycznych systemu. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Zapewnia <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dostęp do fizycznego systemu plików. `PhysicalFileProvider`używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i obejmuje wszystkie ścieżki do katalogu i jego wiązki obrażeń. Ten zakres uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego kamiłę kamicy. Najczęstszym scenariuszem tworzenia i `PhysicalFileProvider` używania jest `IFileProvider` żądanie w konstruktorze poprzez [iniekcję zależności.](xref:fundamentals/dependency-injection)

Podczas bezpośredniego tworzenia wystąpienia tego dostawcy wymagana jest bezwzględna ścieżka katalogu i służy jako ścieżka podstawowa dla wszystkich żądań wykonanych przy użyciu dostawcy. Wzorce glob nie są obsługiwane w ścieżce katalogu.

Poniższy kod pokazuje, `PhysicalFileProvider` jak używać do uzyskiwania zawartości katalogu i informacji o plikach:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Typy w poprzednim przykładzie:

* `provider`jest `IFileProvider`.
* `contents`jest `IDirectoryContents`.
* `fileInfo`jest `IFileInfo`.

Dostawca plików może służyć do iteracji za `applicationRoot` pośrednictwem `GetFileInfo` katalogu określonego przez lub wywołać, aby uzyskać informacje o pliku. Wzory glob nie mogą być `GetFileInfo` przekazywane do metody. Dostawca plików nie ma dostępu `applicationRoot` poza katalogiem.

Przykładowa aplikacja *FileProviderSample* tworzy `Startup.ConfigureServices` dostawcę <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>w metodzie przy użyciu:

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Jest <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> używany do uzyskiwania dostępu do plików osadzonych w zestawach. Używa `ManifestEmbeddedFileProvider` manifestu skompilowanego do zestawu w celu odtworzenia oryginalnych ścieżek osadzonych plików.

Aby wygenerować manifest osadzonych plików:

1. Dodaj pakiet [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet do projektu.
1. Ustaw `<GenerateEmbeddedFilesManifest>` właściwość `true`na . Określ pliki, które mają być osadzane za pomocą [ \<>EmbeddedResource: ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Użyj [wzorców glob,](#glob-patterns) aby określić jeden lub więcej plików do osadzenia w złożeniu.

*FileProviderPróbka* przykładowa `ManifestEmbeddedFileProvider` aplikacja tworzy i przekazuje aktualnie wykonywanyzestaw do konstruktora.

*Startup.cs:*

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Dodatkowe przeciążenia pozwalają na:

* Określ względną ścieżkę pliku.
* Zakres plików do ostatniej zmodyfikowanej daty.
* Nazwij osadzony zasób zawierający osadzony manifest pliku.

| Przeciążenie | Opis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Akceptuje opcjonalny `root` parametr ścieżki względnej. Określ `root` do zakresu <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> wywołania do tych zasobów w ramach podanej ścieżki. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akceptuje opcjonalny `root` parametr ścieżki `lastModified` względnej<xref:System.DateTimeOffset>i parametr daty ( ). Data `lastModified` obejmuje datę ostatniej modyfikacji <xref:Microsoft.Extensions.FileProviders.IFileInfo> dla wystąpień <xref:Microsoft.Extensions.FileProviders.IFileProvider>zwróconych przez plik . |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akceptuje opcjonalną `root` ścieżkę `lastModified` względną, datę i `manifestName` parametry. Reprezentuje `manifestName` nazwę osadzonego zasobu zawierającego manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

Łączy <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami od wielu dostawców. Podczas tworzenia `CompositeFileProvider`, przekazać jedno `IFileProvider` lub więcej wystąpień do jego konstruktora.

W *FileProviderSample* przykładowej `PhysicalFileProvider` aplikacji `ManifestEmbeddedFileProvider` a i `CompositeFileProvider` udostępnić pliki do zarejestrowanego w kontenerze usługi aplikacji. W `Startup.ConfigureServices` metodzie projektu znajduje się następujący kod:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Uważaj na zmiany

Metoda <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> zawiera scenariusz, aby obejrzeć jeden lub więcej plików lub katalogów dla zmian. Metoda: `Watch`

* Akceptuje ciąg ścieżki pliku, który może używać [wzorców glob](#glob-patterns) do określania wielu plików.
* Zwraca <xref:Microsoft.Extensions.Primitives.IChangeToken>plik .

Wynikowy token zmiany udostępnia:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Właściwość, która może być kontrolowane, aby ustalić, czy nastąpiła zmiana.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Wywoływane po wykryciu zmian do określonego ciągu ścieżki. Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę. Aby włączyć ciągłe monitorowanie, należy użyć <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazane `IChangeToken` poniżej) lub odtworzyć wystąpień w odpowiedzi na zmiany.

Przykładowa aplikacja *WatchConsole* zapisuje komunikat za każdym razem, gdy plik *txt* w katalogu *TextFiles* zostanie zmodyfikowany:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą nie niezawodnie wysyłać powiadomień o zmianach. Ustaw `DOTNET_USE_POLLING_FILE_WATCHER` zmienną `1` środowiskową na lub `true` sondować system plików pod kątem zmian co cztery sekundy (nie można ich skonfigurować).

### <a name="glob-patterns"></a>Wzory globów

Ścieżki systemu plików używają wzorców symboli wieloznacznych zwanych *wzorami glob (lub globbing).* Określ grupy plików z tymi wzorami. Dwa symbole wieloznaczne są `*` i: `**`

**`*`**  
Dopasowuje wszystko na bieżącym poziomie folderu, dowolną nazwę pliku lub dowolne rozszerzenie pliku. Dopasowania są przerywane przez `/` i `.` znaki w ścieżce pliku.

**`**`**  
Dopasowuje wszystko na wielu poziomach katalogów. Może służyć do cyklicznego dopasowania wielu plików w hierarchii katalogów.

W poniższej tabeli przedstawiono typowe przykłady wzorców glob.

|Wzorce  |Opis  |
|---------|---------|
|`directory/file.txt`|Pasuje do określonego pliku w określonym katalogu.|
|`directory/*.txt`|Dopasowuje wszystkie pliki z rozszerzeniem *txt* w określonym katalogu.|
|`directory/*/appsettings.json`|Dopasowuje wszystkie pliki *appsettings.json* w katalogach dokładnie jeden poziom poniżej folderu *katalogu.*|
|`directory/**/*.txt`|Dopasowuje wszystkie pliki z rozszerzeniem *txt* znalezionym w dowolnym miejscu w folderze *katalogu.*|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core abstrakcje dostęp do systemu plików za pomocą dostawców plików. Dostawcy plików są używane w całej ASP.NET Core framework:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>udostępnia katalog główny [zawartości](xref:fundamentals/index#content-root) aplikacji i `IFileProvider` katalog główny sieci [Web](xref:fundamentals/index#web-root) jako typy.
* [Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.
* [Razor](xref:mvc/views/razor) używa dostawców plików do lokalizowania stron i widoków.
* Narzędzia .NET Core używają dostawców plików i wzorców glob, aby określić, które pliki powinny zostać opublikowane.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfejsy dostawcy plików

Podstawowym interfejsem <xref:Microsoft.Extensions.FileProviders.IFileProvider>jest . `IFileProvider`udostępnia metody na:

* Uzyskiwanie informacji<xref:Microsoft.Extensions.FileProviders.IFileInfo>o pliku ( ).
* Uzyskiwanie informacji<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>o katalogu ( ).
* Konfigurowanie powiadomień o <xref:Microsoft.Extensions.Primitives.IChangeToken>zmianach (przy użyciu ).

`IFileInfo`zapewnia metody i właściwości do pracy z plikami:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(w bajtach)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Data

Można odczytać z pliku przy użyciu [metody IFileInfo.CreateReadStream.](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*)

Przykładowa aplikacja pokazuje, jak skonfigurować `Startup.ConfigureServices` dostawcę plików do użytku w całej aplikacji za pomocą [iniekcji zależności.](xref:fundamentals/dependency-injection)

## <a name="file-provider-implementations"></a>Implementacje dostawcy plików

Dostępne są `IFileProvider` trzy implementacje.

| Wdrażanie | Opis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Dostawca fizyczny jest używany do uzyskiwania dostępu do plików fizycznych systemu. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Manifest osadzony dostawca służy do uzyskiwania dostępu do plików osadzonych w zestawach. |
| [CompositeFileProvider](#compositefileprovider) | Dostawca złożony służy do zapewnienia połączonego dostępu do plików i katalogów od jednego lub więcej innych dostawców. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Zapewnia <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dostęp do fizycznego systemu plików. `PhysicalFileProvider`używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i obejmuje wszystkie ścieżki do katalogu i jego wiązki obrażeń. Ten zakres uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego kamiłę kamicy. Najczęstszym scenariuszem tworzenia i `PhysicalFileProvider` używania jest `IFileProvider` żądanie w konstruktorze poprzez [iniekcję zależności.](xref:fundamentals/dependency-injection)

Podczas tworzenia wystąpienia tego dostawcy bezpośrednio, ścieżka katalogu jest wymagana i służy jako ścieżka podstawowa dla wszystkich żądań wykonanych przy użyciu dostawcy.

Poniższy kod pokazuje, `PhysicalFileProvider` jak utworzyć i używać go do uzyskania zawartości katalogu i informacji o pliku:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typy w poprzednim przykładzie:

* `provider`jest `IFileProvider`.
* `contents`jest `IDirectoryContents`.
* `fileInfo`jest `IFileInfo`.

Dostawca plików może służyć do iteracji za `applicationRoot` pośrednictwem `GetFileInfo` katalogu określonego przez lub wywołać, aby uzyskać informacje o pliku. Dostawca plików nie ma dostępu `applicationRoot` poza katalogiem.

Przykładowa aplikacja tworzy dostawcę w `Startup.ConfigureServices` klasie aplikacji przy użyciu [IHostingEnvironment.ContentRootFileProvider:](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Jest <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> używany do uzyskiwania dostępu do plików osadzonych w zestawach. Używa `ManifestEmbeddedFileProvider` manifestu skompilowanego do zestawu w celu odtworzenia oryginalnych ścieżek osadzonych plików.

Aby wygenerować manifest osadzonych `<GenerateEmbeddedFilesManifest>` plików, `true`ustaw właściwość na . Określ pliki, które mają być osadzane za pomocą [ &lt;programu EmbeddedResource:&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Użyj [wzorców glob,](#glob-patterns) aby określić jeden lub więcej plików do osadzenia w złożeniu.

Przykładowa aplikacja `ManifestEmbeddedFileProvider` tworzy i przekazuje aktualnie wykonywanyzestaw do jego konstruktora.

*Startup.cs:*

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Dodatkowe przeciążenia pozwalają na:

* Określ względną ścieżkę pliku.
* Zakres plików do ostatniej zmodyfikowanej daty.
* Nazwij osadzony zasób zawierający osadzony manifest pliku.

| Przeciążenie | Opis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Akceptuje opcjonalny `root` parametr ścieżki względnej. Określ `root` do zakresu <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> wywołania do tych zasobów w ramach podanej ścieżki. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akceptuje opcjonalny `root` parametr ścieżki `lastModified` względnej<xref:System.DateTimeOffset>i parametr daty ( ). Data `lastModified` obejmuje datę ostatniej modyfikacji <xref:Microsoft.Extensions.FileProviders.IFileInfo> dla wystąpień <xref:Microsoft.Extensions.FileProviders.IFileProvider>zwróconych przez plik . |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akceptuje opcjonalną `root` ścieżkę `lastModified` względną, datę i `manifestName` parametry. Reprezentuje `manifestName` nazwę osadzonego zasobu zawierającego manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

Łączy <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami od wielu dostawców. Podczas tworzenia `CompositeFileProvider`, przekazać jedno `IFileProvider` lub więcej wystąpień do jego konstruktora.

W przykładowej aplikacji `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` i a `CompositeFileProvider` udostępnić pliki do zarejestrowanego w kontenerze usługi aplikacji:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Uważaj na zmiany

[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) Metoda zapewnia scenariusz do oglądania jednego lub więcej plików lub katalogów dla zmian. `Watch`akceptuje ciąg ścieżki, który może używać [wzorców glob](#glob-patterns) do określania wielu plików. `Watch`zwraca <xref:Microsoft.Extensions.Primitives.IChangeToken>plik . Token zmiany udostępnia:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Właściwość, która może być kontrolowane, aby ustalić, czy nastąpiła zmiana.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Wywoływane po wykryciu zmian do określonego ciągu ścieżki. Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę. Aby włączyć ciągłe monitorowanie, należy użyć <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazane `IChangeToken` poniżej) lub odtworzyć wystąpień w odpowiedzi na zmiany.

W przykładowej aplikacji aplikacja konsoli *WatchConsole* jest skonfigurowana do wyświetlania komunikatu za każdym razem, gdy plik tekstowy jest modyfikowany:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą nie niezawodnie wysyłać powiadomień o zmianach. Ustaw `DOTNET_USE_POLLING_FILE_WATCHER` zmienną `1` środowiskową na lub `true` sondować system plików pod kątem zmian co cztery sekundy (nie można ich skonfigurować).

## <a name="glob-patterns"></a>Wzory globów

Ścieżki systemu plików używają wzorców symboli wieloznacznych zwanych *wzorami glob (lub globbing).* Określ grupy plików z tymi wzorami. Dwa symbole wieloznaczne są `*` i: `**`

**`*`**  
Dopasowuje wszystko na bieżącym poziomie folderu, dowolną nazwę pliku lub dowolne rozszerzenie pliku. Dopasowania są przerywane przez `/` i `.` znaki w ścieżce pliku.

**`**`**  
Dopasowuje wszystko na wielu poziomach katalogów. Może służyć do cyklicznego dopasowania wielu plików w hierarchii katalogów.

**Przykłady wzorca Glob**

**`directory/file.txt`**  
Pasuje do określonego pliku w określonym katalogu.

**`directory/*.txt`**  
Dopasowuje wszystkie pliki z rozszerzeniem *txt* w określonym katalogu.

**`directory/*/appsettings.json`**  
Dopasowuje wszystkie `appsettings.json` pliki w katalogach dokładnie jeden poziom poniżej folderu *katalogu.*

**`directory/**/*.txt`**  
Dopasowuje wszystkie pliki z rozszerzeniem *txt* znajdującym się w dowolnym miejscu w folderze *katalogu.*

::: moniker-end

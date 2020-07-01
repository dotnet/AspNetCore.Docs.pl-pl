---
title: Pliki statyczne w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak udostępniać i zabezpieczać pliki statyczne oraz skonfigurować zachowania oprogramowania pośredniczącego do obsługi plików statycznych w aplikacji internetowej ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 39c5c5d4875e1d59abaa6d998a09dbffd723214d
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793417"
---
# <a name="static-files-in-aspnet-core"></a>Pliki statyczne w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)

Pliki statyczne, takie jak HTML, CSS, images i JavaScript, są zasobami, które domyślnie aplikacja ASP.NET Core bezpośrednio obsługuje klientów.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples/3x) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Obsługuj pliki statyczne

Pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu. Domyślnym katalogiem jest `{content root}/wwwroot` , ale można go zmienić za pomocą metody [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) . Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root).

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>Metoda ustawia katalog główny zawartości dla bieżącego katalogu:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Program2.cs?name=snippet_Main)]

Poprzedni kod został utworzony przy użyciu szablonu aplikacji sieci Web.

Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root). Na przykład szablony projektu **aplikacji sieci Web** zawierają kilka folderów w `wwwroot` folderze:

* `wwwroot`
  * `css`
  * `js`
  * `lib`

Rozważ utworzenie folderu *wwwroot/images* i dodanie pliku *wwwroot/images/MyImage.jpg* . Format identyfikatora URI dostępu do pliku w `images` folderze to `https://<hostname>/images/<image_file_name>` . Na przykład: `https://localhost:5001/images/MyImage.jpg`

### <a name="serve-files-in-web-root"></a>Obsługiwanie plików w katalogu głównym sieci Web

Domyślne szablony aplikacji sieci Web wywołują <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> metodę w `Startup.Configure` , co umożliwia obsługę plików statycznych:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Startup.cs?name=snippet&highlight=14)]

`UseStaticFiles`Przeciążenie metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako do zablokowania. Następujące znaczniki odwołują się do *wwwroot/images/MyImage.jpg*:

```html
<img src="~/images/MyImage.jpg"  class="img" alt="My image." />
```

W poprzednim kodzie znak tyldy `~/` wskazuje [element główny sieci Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Obsługiwanie plików poza katalogiem głównym sieci Web

Rozważ hierarchię katalogów, w której pliki statyczne mają być obsługiwane poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

Żądanie może uzyskać dostęp do `red-rose.jpg` pliku przez skonfigurowanie pliku statycznego oprogramowania pośredniczącego w następujący sposób:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupRose.cs?name=snippet&highlight=14-21)]

W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* . Żądanie do `https://<hostname>/StaticFiles/images/red-rose.jpg` obsłużynia pliku *red-rose.jpg* .

Następujące znaczniki odwołują się do *MyStaticFiles/images/red-rose.jpg*:

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose." />
```

### <a name="set-http-response-headers"></a>Ustawianie nagłówków odpowiedzi HTTP

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Obiekt może służyć do ustawiania nagłówków odpowiedzi HTTP. Oprócz konfigurowania pliku statycznego z poziomu [katalogu głównego sieci Web](xref:fundamentals/index#web-root), poniższy kod ustawia `Cache-Control` Nagłówek:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupAddHeader.cs?name=snippet&highlight=14-23)]

Pliki statyczne są publicznie buforowane przez 600 sekund:

![Nagłówki odpowiedzi pokazujące nagłówek Cache-Control został dodany](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autoryzacja pliku statycznego

Oprogramowanie pośredniczące plików statycznych nie zapewnia kontroli autoryzacji. Wszystkie pliki obsługiwane przez ten program, w tym w ramach `wwwroot` programu, są publicznie dostępne. Aby obpracować pliki na podstawie autoryzacji:

* Przechowuj je poza programem `wwwroot` i dowolnym katalogiem dostępnym dla oprogramowania pośredniczącego plików statycznych.
* Można je obsłużyć za pomocą metody akcji, do której zastosowano autoryzację i zwrócić obiekt [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) :

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="directory-browsing"></a>Przeglądanie katalogów

Przeglądanie katalogów umożliwia wyświetlanie listy katalogów w określonych katalogach.

Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [uwagi](#sc).

Włącz przeglądanie katalogów za pomocą:

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>w programie `Startup.ConfigureServices` .
* [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) `Startup.Configure` .

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupBrowse.cs?name=snippet&highlight=4,20-34)]

Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL `https://<hostname>/MyImages` z linkami do każdego pliku i folderu:

![przeglądanie katalogów](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>Obsługuj dokumenty domyślne

Ustawienie strony domyślnej umożliwia odwiedzającym punkt startowy w witrynie. Aby udostępnić stronę domyślną z `wwwroot` bez w pełni kwalifikowanego identyfikatora URI, wywołaj <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> metodę:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty.cs?name=snippet&highlight=14)]

`UseDefaultFiles`musi być wywoływana przed zapisaniem `UseStaticFiles` pliku domyślnego. `UseDefaultFiles`to adres URL służący do ponownego zapisu, który nie obsługuje tego pliku.

W programie `UseDefaultFiles` żądania do folderu w usłudze `wwwroot` Search dla:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI. Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.

Następujący kod zmienia domyślną nazwę pliku na *mydefault.html*:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet2)]

Poniższy kod ilustruje `Startup.Configure` poprzedni kod:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet)]

### <a name="usefileserver-for-default-documents"></a>UseFileServer dla dokumentów domyślnych

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>łączy funkcje `UseStaticFiles` , `UseDefaultFiles` i opcjonalnie `UseDirectoryBrowser` .

Wywołaj, `app.UseFileServer` Aby włączyć obsługę plików statycznych i pliku domyślnego. Przeglądanie katalogów nie jest włączone. Poniższy kod ilustruje `Startup.Configure` `UseFileServer` :

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty2.cs?name=snippet&highlight=14)]

Poniższy kod umożliwia obsługę plików statycznych, plik domyślny i przeglądanie katalogów:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Poniższy kod ilustruje `Startup.Configure` poprzedni kod:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty3.cs?name=snippet&highlight=14-18)]

Weź pod uwagę następującą hierarchię katalogów:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

Poniższy kod umożliwia obsługę plików statycznych, plików domyślnych i przeglądanie katalogów `MyStaticFiles` :

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupUseFileServer.cs?name=snippet&highlight=4,20-30)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>musi być wywoływana, gdy `EnableDirectoryBrowsing` wartość właściwości to `true` .

Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:

| Identyfikator URI            |      Odpowiedź  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/obrazy/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

Jeśli plik o nazwie Default nie istnieje w katalogu *MyStaticFiles* , `https://<hostname>/StaticFiles` zwraca listę katalogów z linkami, które można kliknąć:

![Lista plików statycznych](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> wykonać przekierowanie po stronie klienta z docelowego identyfikatora URI bez końcowej `/` do docelowego identyfikatora URI kończącego się na końcu `/` . Na przykład z `https://<hostname>/StaticFiles` do `https://<hostname>/StaticFiles/` . Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowego ukośnika ( `/` ).

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Klasa [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) zawiera `Mappings` Właściwość, która służy jako mapowanie rozszerzeń plików na typy zawartości MIME. W poniższym przykładzie kilka rozszerzeń plików są mapowane na znane typy MIME. Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* zostaje usunięty:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet2)]

Poniższy kod ilustruje `Startup.Configure` poprzedni kod:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet&highlight=14-42)]

Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Niestandardowe typy zawartości

Oprogramowanie pośredniczące plików statycznych rozpoznaje niemal 400 znanych typów zawartości plików. Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku. Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* . Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.

Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet2)]

Poniższy kod ilustruje `Startup.Configure` poprzedni kod:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet&highlight=14-18)]

W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.

> [!WARNING]
> Włączenie [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) stanowi zagrożenie bezpieczeństwa. Jest on domyślnie wyłączony i nie jest zalecane jego użycie. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.

## <a name="serve-files-from-multiple-locations"></a>Obsługiwanie plików z wielu lokalizacji

`UseStaticFiles`i `UseFileServer` domyślny dla dostawcy plików wskazywanego przez `wwwroot` . Dodatkowe wystąpienia `UseStaticFiles` i `UseFileServer` mogą być dostarczone z innymi dostawcami plików w celu obsłużenia plików z innych lokalizacji. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/15578)w serwisie GitHub.

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>Zagadnienia dotyczące zabezpieczeń plików statycznych

> [!WARNING]
> `UseDirectoryBrowser`i `UseStaticFiles` mogą wyciekać klucze tajne. Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane. Uważnie Przejrzyj katalogi, które są włączone za pośrednictwem `UseStaticFiles` lub `UseDirectoryBrowser` . Cały katalog i jego katalogi podrzędne stają się publicznie dostępne. Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, na przykład `<content_root>/wwwroot` . Oddziel te pliki od widoków MVC, Razor stron, plików konfiguracji itp.

* Adresy URL dla zawartości uwidocznionej `UseDirectoryBrowser` i `UseStaticFiles` podlegają ograniczeniom dotyczącym wielkości liter i znaków w źródłowym systemie plików. Na przykład w systemie Windows nie jest rozróżniana wielkość liter, ale macOS i Linux nie są.

* ASP.NET Core aplikacje hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przesyłania dalej wszystkich żądań do aplikacji, w tym żądań plików statycznych. Procedura obsługi pliku statycznego usług IIS nie jest używana i nie ma możliwości obsługi żądań.

* Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć program obsługi plików statycznych usług IIS na poziomie serwera lub witryny sieci Web:
    1. Przejdź do funkcji **moduły** .
    1. Na liście wybierz pozycję **StaticFileModule** .
    1. Kliknij przycisk **Usuń** na pasku bocznym **Akcje** .

> [!WARNING]
> Jeśli program obsługi plików statycznych usług IIS jest włączony **i** moduł ASP.NET Core jest niepoprawnie skonfigurowany, obsługiwane są pliki statyczne. Dzieje się tak na przykład wtedy, gdy plik *web.config* nie zostanie wdrożony.

* Umieść pliki kodu, w tym *. cs* i *. cshtml*, poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root)projektu aplikacji. W związku z tym tworzone jest podział logiczny między zawartością po stronie klienta a kodem opartym na serwerze. Zapobiega to wyciekowi kodu po stronie serwera.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Wprowadzenie do programu ASP.NET Core](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Scott Addie](https://twitter.com/Scott_Addie)

Pliki statyczne, takie jak HTML, CSS, images i JavaScript, są zasobami, które aplikacja ASP.NET Core może bezpośrednio obsługiwać klientów. Aby umożliwić obsługę tych plików, wymagana jest pewna konfiguracja.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Obsługuj pliki statyczne

Pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu. Domyślnym katalogiem jest *{Content root}/wwwroot*, ale można go zmienić za pomocą metody [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) . Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root) .

Host sieci Web aplikacji musi być świadomy katalogu głównego zawartości.

`WebHost.CreateDefaultBuilder`Metoda ustawia katalog główny zawartości dla bieżącego katalogu:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root). Na przykład szablon projektu **aplikacji sieci Web** zawiera kilka folderów w `wwwroot` folderze:

* `wwwroot`
  * `css`
  * `images`
  * `js`

Format identyfikatora URI służący do uzyskiwania dostępu do pliku w podfolderze *images* to *http:// \<server_address> \<image_file_name> /images/*. Na przykład *http://localhost:9189/images/banner3.svg* .

Jeśli .NET Framework określania wartości docelowej, Dodaj pakiet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu. W przypadku określania platformy .NET Core [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) zawiera ten pakiet.

Skonfiguruj [oprogramowanie pośredniczące](xref:fundamentals/middleware/index), które umożliwia obsługę plików statycznych.

### <a name="serve-files-inside-of-web-root"></a>Obsługuj pliki wewnątrz katalogu głównego sieci Web

Wywołaj metodę [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) w `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

`UseStaticFiles`Przeciążenie metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako do zablokowania. Następujące znaczniki odwołują się do *wwwroot/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

W poprzednim kodzie znak tyldy `~/` wskazuje [element główny sieci Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Obsługiwanie plików poza katalogiem głównym sieci Web

Rozważ hierarchię katalogów, w której pliki statyczne mają być obsługiwane poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

Żądanie może uzyskać dostęp do pliku *banner1. SVG* przez skonfigurowanie pliku statycznego pośredniczącego w następujący sposób:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* . Żądanie *http:// \<server_address> /StaticFiles/images/banner1.SVG* obsługuje plik *banner1. SVG* .

Następujące znaczniki odwołują się do *MyStaticFiles/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Ustawianie nagłówków odpowiedzi HTTP

Obiekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) może służyć do ustawiania nagłówków odpowiedzi HTTP. Oprócz konfigurowania pliku statycznego z poziomu [katalogu głównego sieci Web](xref:fundamentals/index#web-root), poniższy kod ustawia `Cache-Control` Nagłówek:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Metoda [HeaderDictionaryExtensions. Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) istnieje w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

Pliki zostały publicznie przetworzone w pamięci podręcznej przez 10 minut (600 sekund) w środowisku deweloperskim:

![Nagłówki odpowiedzi pokazujące nagłówek Cache-Control został dodany](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autoryzacja pliku statycznego

Oprogramowanie pośredniczące plików statycznych nie zapewnia kontroli autoryzacji. Wszystkie pliki obsługiwane przez ten program, w tym w katalogu *wwwroot*, są publicznie dostępne. Aby obpracować pliki na podstawie autoryzacji:

* Przechowaj je poza katalogiem *wwwroot* i dowolnym katalogu dostępnym dla oprogramowania pośredniczącego plików statycznych.
* Obsłużyć je za pomocą metody akcji, do której zastosowano autoryzację. Zwróć obiekt [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) :

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Włącz przeglądanie katalogów

Przeglądanie katalogów umożliwia użytkownikom aplikacji sieci Web Wyświetlanie listy katalogów i plików w określonym katalogu. Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa (zobacz [uwagi](#sc)). Włącz przeglądanie katalogów, wywołując metodę [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) w `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Dodaj wymagane usługi, wywołując <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> metodę z `Startup.ConfigureServices` :

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http:// \<server_address> /myimages*, z linkami do każdego pliku i folderu:

![przeglądanie katalogów](static-files/_static/dir-browse.png)

Zapoznaj się z [uwagami](#considerations) dotyczącymi zagrożeń bezpieczeństwa podczas włączania przeglądania.

Zwróć uwagę na dwa `UseStaticFiles` wywołania w poniższym przykładzie. Pierwsze wywołanie umożliwia obsługę plików statycznych w folderze *wwwroot* . Drugie wywołanie umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http:// \<server_address> /myimages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Obsługuj dokument domyślny

Ustawienie domyślnej strony głównej zapewnia odwiedzającym logiczny punkt wyjścia podczas odwiedzania witryny. Aby obtworzyć stronę domyślną bez zakwalifikowania identyfikatora URI użytkownika, wywołaj metodę [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) z `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`musi być wywoływana przed zapisaniem `UseStaticFiles` pliku domyślnego. `UseDefaultFiles`to adres URL, który faktycznie nie obsługuje pliku. Włącz oprogramowanie pośredniczące plików statycznych za pośrednictwem programu `UseStaticFiles` w celu obsługi pliku.

W programie `UseDefaultFiles` żądania do folderu wyszukują:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI. Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.

Następujący kod zmienia domyślną nazwę pliku na *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>łączy funkcje `UseStaticFiles` , `UseDefaultFiles` i opcjonalnie `UseDirectoryBrowser` .

Poniższy kod umożliwia obsługę plików statycznych i pliku domyślnego. Przeglądanie katalogów nie jest włączone.

```csharp
app.UseFileServer();
```

Poniższy kod kompiluje na Przeciążenie bez parametrów przez włączenie przeglądania katalogów:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Weź pod uwagę następującą hierarchię katalogów:

* **wwwroot**
  * **kaskad**
  * **rastrow**
  * **JS**
* **MyStaticFiles**
  * **rastrow**
    * *banner1. SVG*
  * *default.html*

Poniższy kod umożliwia włączenie plików statycznych, plików domyślnych i przeglądanie katalogów `MyStaticFiles` :

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser`musi być wywoływana, gdy `EnableDirectoryBrowsing` wartość właściwości to `true` :

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:

| Identyfikator URI            |                             Odpowiedź  |
| ------- | ------|
| *http:// \<server_address> /StaticFiles/images/banner1.SVG*    |      MyStaticFiles/images/banner1. SVG |
| *http:// \<server_address> /StaticFiles*             |     MyStaticFiles/default.html |

Jeśli plik o nazwie Default nie istnieje w katalogu *MyStaticFiles* , *http:// \<server_address> /StaticFiles* zwraca listę katalogów z linkami, które można kliknąć:

![Lista plików statycznych](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> przekierować po stronie klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez końcowego ukośnika) do `http://{SERVER ADDRESS}/StaticFiles/` (z końcowym ukośnikiem). Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowej kreski ułamkowej.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Klasa [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) zawiera `Mappings` Właściwość służącą jako mapowanie rozszerzeń plików na typy zawartości MIME. W poniższym przykładzie kilka rozszerzeń plików są zarejestrowane na znanych typach MIME. Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* jest usuwany.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Niestandardowe typy zawartości

Oprogramowanie pośredniczące plików static rozpoznaje niemal 400 znanych typów zawartości plików. Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku. Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* . Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.

Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.

> [!WARNING]
> Włączenie [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) stanowi zagrożenie bezpieczeństwa. Jest on domyślnie wyłączony i nie jest zalecane jego użycie. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.

## <a name="serve-files-from-multiple-locations"></a>Obsługiwanie plików z wielu lokalizacji

`UseStaticFiles`i `UseFileServer` domyślnym dostawcą plików wskazywanym w lokalizacji *wwwroot*. Można podać dodatkowe wystąpienia `UseStaticFiles` i `UseFileServer` z innymi dostawcami plików do obsługi plików z innych lokalizacji. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/15578)w serwisie GitHub.

### <a name="considerations"></a>Zagadnienia do rozważenia

> [!WARNING]
> `UseDirectoryBrowser`i `UseStaticFiles` mogą wyciekać klucze tajne. Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane. Uważnie Przejrzyj katalogi, które są włączone za pośrednictwem `UseStaticFiles` lub `UseDirectoryBrowser` . Cały katalog i jego katalogi podrzędne stają się publicznie dostępne. Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, takie jak * \<content_root> /wwwroot*. Oddziel te pliki od widoków MVC, Razor stron (tylko 2. x), plików konfiguracji itp.

* Adresy URL dla zawartości uwidocznionej `UseDirectoryBrowser` i `UseStaticFiles` podlegają ograniczeniom dotyczącym wielkości liter i znaków w źródłowym systemie plików. Na przykład w systemie Windows nie jest rozróżniana wielkość liter &mdash; macOS i Linux.

* ASP.NET Core aplikacje hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przesyłania dalej wszystkich żądań do aplikacji, w tym żądań plików statycznych. Procedura obsługi pliku statycznego usług IIS nie jest używana. Nie ma możliwości obsługi żądań, zanim są one obsługiwane przez moduł.

* Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć program obsługi plików statycznych usług IIS na poziomie serwera lub witryny sieci Web:
    1. Przejdź do funkcji **moduły** .
    1. Na liście wybierz pozycję **StaticFileModule** .
    1. Kliknij przycisk **Usuń** na pasku bocznym **Akcje** .

> [!WARNING]
> Jeśli program obsługi plików statycznych usług IIS jest włączony **i** moduł ASP.NET Core jest niepoprawnie skonfigurowany, obsługiwane są pliki statyczne. Dzieje się tak na przykład wtedy, gdy plik *web.config* nie zostanie wdrożony.

* Umieść pliki kodu (w tym *. cs* i *. cshtml*) poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root)projektu aplikacji. W związku z tym tworzone jest podział logiczny między zawartością po stronie klienta a kodem opartym na serwerze. Zapobiega to wyciekowi kodu po stronie serwera.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Wprowadzenie do programu ASP.NET Core](xref:index)

::: moniker-end

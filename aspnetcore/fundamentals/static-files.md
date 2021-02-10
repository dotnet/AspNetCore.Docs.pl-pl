---
title: Pliki statyczne w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak udostępniać i zabezpieczać pliki statyczne oraz skonfigurować zachowania oprogramowania pośredniczącego do obsługi plików statycznych w aplikacji internetowej ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: fad569ab06735600299d8051a258651e329db8ce
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107184"
---
# <a name="static-files-in-aspnet-core"></a>Pliki statyczne w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)

Pliki statyczne, takie jak HTML, CSS, images i JavaScript, są zasobami, które domyślnie aplikacja ASP.NET Core bezpośrednio obsługuje klientów.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Obsługuj pliki statyczne

Pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu. Domyślnym katalogiem jest `{content root}/wwwroot` , ale można go zmienić za pomocą <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody. Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root).

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>Metoda ustawia katalog główny zawartości dla bieżącego katalogu:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

Poprzedni kod został utworzony przy użyciu szablonu aplikacji sieci Web.

Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root). Na przykład szablony projektu **aplikacji sieci Web** zawierają kilka folderów w `wwwroot` folderze:

* `wwwroot`
  * `css`
  * `js`
  * `lib`

Rozważ utworzenie folderu *wwwroot/images* i dodanie pliku *wwwroot/images/MyImage.jpg* . Format identyfikatora URI dostępu do pliku w `images` folderze to `https://<hostname>/images/<image_file_name>` . Na przykład `https://localhost:5001/images/MyImage.jpg`

### <a name="serve-files-in-web-root"></a>Obsługiwanie plików w katalogu głównym sieci Web

Domyślne szablony aplikacji sieci Web wywołują <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodę w `Startup.Configure` , co umożliwia obsługę plików statycznych:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

`UseStaticFiles`Przeciążenie metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako do zablokowania. Następujące znaczniki odwołują się do *wwwroot/images/MyImage.jpg*:

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
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

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* . Żądanie do `https://<hostname>/StaticFiles/images/red-rose.jpg` obsłużynia pliku *red-rose.jpg* .

Następujące znaczniki odwołują się do *MyStaticFiles/images/red-rose.jpg*:

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a>Ustawianie nagłówków odpowiedzi HTTP

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Obiekt może służyć do ustawiania nagłówków odpowiedzi HTTP. Oprócz konfigurowania pliku statycznego z poziomu [katalogu głównego sieci Web](xref:fundamentals/index#web-root), poniższy kod ustawia `Cache-Control` Nagłówek:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

Pliki statyczne są publicznie buforowane przez 600 sekund:

![Dodano nagłówki odpowiedzi pokazujące nagłówek Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autoryzacja pliku statycznego

ASP.NET Core szablonów wywołań <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> przed wywołaniem <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> . Większość aplikacji korzysta z tego wzorca. Gdy oprogramowanie pośredniczące pliku statycznego jest wywoływane przed wyzwoleniem na oprogramowanie pośredniczące:

  * Na plikach statycznych nie są wykonywane żadne testy autoryzacji.
  * Pliki statyczne obsługiwane przez oprogramowanie pośredniczące plików statycznych, takie jak te w programie `wwwroot` , są publicznie dostępne.
  
Aby zapewnić możliwość korzystania z plików statycznych na podstawie autoryzacji:

  * Przechowuj je poza programem `wwwroot` .
  * Wywoływanie `UseStaticFiles` , Określanie ścieżki po wywołaniu `UseAuthorization` .
  * Ustaw [zasady autoryzacji rezerwowej](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  W powyższym kodzie rezerwowe zasady autoryzacji wymagają uwierzytelnienia ***wszystkich*** użytkowników. Punkty końcowe, takie jak kontrolery, Razor strony itp. określają własne wymagania dotyczące autoryzacji, nie używają rezerwowych zasad autoryzacji. Na przykład Razor strony, kontrolery lub metody akcji z `[AllowAnonymous]` lub `[Authorize(PolicyName="MyPolicy")]` używają stosowanego atrybutu autoryzacji, a nie rezerwowych zasad autoryzacji.

  <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> dodaje <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> do bieżącego wystąpienia, które wymusza, że bieżący użytkownik jest uwierzytelniony.

  Zasoby statyczne w obszarze `wwwroot` są publicznie dostępne, ponieważ domyślne oprogramowanie pośredniczące ( `app.UseStaticFiles();` ) jest wywoływane przed `UseAuthentication` . Statyczne zasoby w folderze *MyStaticFiles* wymagają uwierzytelniania. [Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) demonstruje ten sposób.

Alternatywnym podejściem do obsługiwania plików na podstawie autoryzacji jest:

  * Przechowuj je poza programem `wwwroot` i dowolnym katalogiem dostępnym dla oprogramowania pośredniczącego plików statycznych.
  * Można je obsłużyć za pomocą metody akcji, do której zastosowano autoryzację i zwrócić <xref:Microsoft.AspNetCore.Mvc.FileResult> obiekt:

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a>Przeglądanie katalogów

Przeglądanie katalogów umożliwia wyświetlanie listy katalogów w określonych katalogach.

Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [uwagi](#considerations).

Włącz przeglądanie katalogów za pomocą:

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> w programie `Startup.ConfigureServices` .
* <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> w programie `Startup.Configure` .

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL `https://<hostname>/MyImages` z linkami do każdego pliku i folderu:

![przeglądanie katalogów](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>Obsługuj dokumenty domyślne

Ustawienie strony domyślnej umożliwia odwiedzającym punkt startowy w witrynie. Aby udostępnić stronę domyślną z `wwwroot` bez w pełni kwalifikowanego identyfikatora URI, wywołaj <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> metodę:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

`UseDefaultFiles` musi być wywoływana przed zapisaniem `UseStaticFiles` pliku domyślnego. `UseDefaultFiles` to adres URL służący do ponownego zapisu, który nie obsługuje tego pliku.

W programie `UseDefaultFiles` żądania do folderu w usłudze `wwwroot` Search dla:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI. Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.

Następujący kod zmienia domyślną nazwę pliku na *mydefault.html*:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

Poniższy kod ilustruje `Startup.Configure` poprzedni kod:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a>UseFileServer dla dokumentów domyślnych

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> łączy funkcje `UseStaticFiles` , `UseDefaultFiles` i opcjonalnie `UseDirectoryBrowser` .

Wywołaj, `app.UseFileServer` Aby włączyć obsługę plików statycznych i pliku domyślnego. Przeglądanie katalogów nie jest włączone. Poniższy kod ilustruje `Startup.Configure` `UseFileServer` :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

Poniższy kod umożliwia obsługę plików statycznych, plik domyślny i przeglądanie katalogów:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Poniższy kod ilustruje `Startup.Configure` poprzedni kod:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

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

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> musi być wywoływana, gdy `EnableDirectoryBrowsing` wartość właściwości to `true` .

Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:

| URI            |      Reakcja  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/obrazy/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

Jeśli plik o nazwie Default nie istnieje w katalogu *MyStaticFiles* , `https://<hostname>/StaticFiles` zwraca listę katalogów z linkami, które można kliknąć:

![Lista plików statycznych](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> wykonać przekierowanie po stronie klienta z docelowego identyfikatora URI bez końcowej `/`  do docelowego identyfikatora URI kończącego się na końcu `/` . Na przykład z `https://<hostname>/StaticFiles` do `https://<hostname>/StaticFiles/` . Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowego ukośnika ( `/` ).

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Klasa zawiera `Mappings` Właściwość, która służy jako mapowanie rozszerzeń plików na typy zawartości MIME. W poniższym przykładzie kilka rozszerzeń plików są mapowane na znane typy MIME. Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* zostaje usunięty:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

Poniższy kod ilustruje `Startup.Configure` poprzedni kod:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Niestandardowe typy zawartości

Oprogramowanie pośredniczące plików statycznych rozpoznaje niemal 400 znanych typów zawartości plików. Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku. Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* . Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.

Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

Poniższy kod ilustruje `Startup.Configure` poprzedni kod:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.

> [!WARNING]
> Włączenie <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stanowi zagrożenie bezpieczeństwa. Jest on domyślnie wyłączony i nie jest zalecane jego użycie. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.

## <a name="serve-files-from-multiple-locations"></a>Obsługiwanie plików z wielu lokalizacji

`UseStaticFiles` i `UseFileServer` domyślny dla dostawcy plików wskazywanego przez `wwwroot` . Dodatkowe wystąpienia `UseStaticFiles` i `UseFileServer` mogą być dostarczone z innymi dostawcami plików w celu obsłużenia plików z innych lokalizacji. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/15578)w serwisie GitHub.

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>Zagadnienia dotyczące zabezpieczeń plików statycznych

> [!WARNING]
> `UseDirectoryBrowser` i `UseStaticFiles` mogą wyciekać klucze tajne. Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane. Uważnie Przejrzyj katalogi, które są włączone za pośrednictwem `UseStaticFiles` lub `UseDirectoryBrowser` . Cały katalog i jego katalogi podrzędne stają się publicznie dostępne. Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, na przykład `<content_root>/wwwroot` . Oddziel te pliki od widoków MVC, Razor stron, plików konfiguracji itp.

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

Pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu. Domyślnym katalogiem jest *{Content root}/wwwroot*, ale można go zmienić za pomocą <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody. Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root) .

Host sieci Web aplikacji musi być świadomy katalogu głównego zawartości.

`WebHost.CreateDefaultBuilder`Metoda ustawia katalog główny zawartości dla bieżącego katalogu:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root). Na przykład szablon projektu **aplikacji sieci Web** zawiera kilka folderów w `wwwroot` folderze:

* `wwwroot`
  * `css`
  * `images`
  * `js`

Format identyfikatora URI służący do uzyskiwania dostępu do pliku w podfolderze *images* to *http:// \<server_address> \<image_file_name> /images/*. Przykładowy adres URL to *http://localhost:9189/images/banner3.svg*.

Jeśli .NET Framework określania wartości docelowej, Dodaj pakiet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu. W przypadku określania platformy .NET Core [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) zawiera ten pakiet.

Skonfiguruj [oprogramowanie pośredniczące](xref:fundamentals/middleware/index), które umożliwia obsługę plików statycznych.

### <a name="serve-files-inside-of-web-root"></a>Obsługuj pliki wewnątrz katalogu głównego sieci Web

Wywołaj <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodę w `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

`UseStaticFiles`Przeciążenie metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako do zablokowania. Następujące znaczniki odwołują się do *wwwroot/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

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

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* . Żądanie *http:// \<server_address> /StaticFiles/images/banner1.SVG* obsługuje plik *banner1. SVG* .

Następujące znaczniki odwołują się do *MyStaticFiles/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Ustawianie nagłówków odpowiedzi HTTP

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Obiekt może służyć do ustawiania nagłówków odpowiedzi HTTP. Oprócz konfigurowania pliku statycznego z poziomu [katalogu głównego sieci Web](xref:fundamentals/index#web-root), poniższy kod ustawia `Cache-Control` Nagłówek:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>Metoda istnieje w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

Pliki zostały publicznie przetworzone w pamięci podręcznej przez 10 minut (600 sekund) w środowisku deweloperskim:

![Dodano nagłówki odpowiedzi pokazujące nagłówek Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autoryzacja pliku statycznego

Oprogramowanie pośredniczące plików statycznych nie zapewnia kontroli autoryzacji. Wszystkie pliki obsługiwane przez ten program, w tym w katalogu *wwwroot*, są publicznie dostępne. Aby obpracować pliki na podstawie autoryzacji:

* Przechowaj je poza katalogiem *wwwroot* i dowolnym katalogu dostępnym dla oprogramowania pośredniczącego plików statycznych.
* Obsłużyć je za pomocą metody akcji, do której zastosowano autoryzację. Zwróć <xref:Microsoft.AspNetCore.Mvc.FileResult> obiekt:

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Włącz przeglądanie katalogów

Przeglądanie katalogów umożliwia użytkownikom aplikacji sieci Web Wyświetlanie listy katalogów i plików w określonym katalogu. Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa (zobacz [uwagi](#considerations)). Włącz przeglądanie katalogów przez wywołanie <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> metody w `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Dodaj wymagane usługi, wywołując <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> metodę z `Startup.ConfigureServices` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http:// \<server_address> /myimages*, z linkami do każdego pliku i folderu:

![przeglądanie katalogów](static-files/_static/dir-browse.png)

Zapoznaj się z [uwagami](#considerations) dotyczącymi zagrożeń bezpieczeństwa podczas włączania przeglądania.

Zwróć uwagę na dwa `UseStaticFiles` wywołania w poniższym przykładzie. Pierwsze wywołanie umożliwia obsługę plików statycznych w folderze *wwwroot* . Drugie wywołanie umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http:// \<server_address> /myimages*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Obsługuj dokument domyślny

Ustawienie domyślnej strony głównej zapewnia odwiedzającym logiczny punkt wyjścia podczas odwiedzania witryny. Aby obtworzyć stronę domyślną bez zakwalifikowania identyfikatora URI użytkownika, wywołaj <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> metodę z `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` musi być wywoływana przed zapisaniem `UseStaticFiles` pliku domyślnego. `UseDefaultFiles` to adres URL, który faktycznie nie obsługuje pliku. Włącz oprogramowanie pośredniczące plików statycznych za pośrednictwem programu `UseStaticFiles` w celu obsługi pliku.

W programie `UseDefaultFiles` żądania do folderu wyszukują:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI. Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.

Następujący kod zmienia domyślną nazwę pliku na *mydefault.html*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> łączy funkcje `UseStaticFiles` , `UseDefaultFiles` i opcjonalnie `UseDirectoryBrowser` .

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

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` musi być wywoływana, gdy `EnableDirectoryBrowsing` wartość właściwości to `true` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:

| URI            |                             Reakcja  |
| ------- | ------|
| *http:// \<server_address> /StaticFiles/images/banner1.SVG*    |      MyStaticFiles/images/banner1. SVG |
| *http:// \<server_address> /StaticFiles*             |     MyStaticFiles/default.html |

Jeśli plik o nazwie Default nie istnieje w katalogu *MyStaticFiles* , *http:// \<server_address> /StaticFiles* zwraca listę katalogów z linkami, które można kliknąć:

![Lista plików statycznych](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> przekierować po stronie klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez końcowego ukośnika) do `http://{SERVER ADDRESS}/StaticFiles/` (z końcowym ukośnikiem). Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowej kreski ułamkowej.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Klasa zawiera `Mappings` Właściwość służącą jako mapowanie rozszerzeń plików na typy zawartości MIME. W poniższym przykładzie kilka rozszerzeń plików są zarejestrowane na znanych typach MIME. Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* jest usuwany.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

Aby uzyskać informacje na temat używania niestandardowego <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> lub do konfigurowania <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> innych Blazor aplikacji serwerowych, zobacz <xref:blazor/fundamentals/static-files> .

## <a name="non-standard-content-types"></a>Niestandardowe typy zawartości

Oprogramowanie pośredniczące plików static rozpoznaje niemal 400 znanych typów zawartości plików. Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku. Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* . Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.

Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.

> [!WARNING]
> Włączenie <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stanowi zagrożenie bezpieczeństwa. Jest on domyślnie wyłączony i nie jest zalecane jego użycie. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.

## <a name="serve-files-from-multiple-locations"></a>Obsługiwanie plików z wielu lokalizacji

`UseStaticFiles` i `UseFileServer` domyślnym dostawcą plików wskazywanym w lokalizacji *wwwroot*. Można podać dodatkowe wystąpienia `UseStaticFiles` i `UseFileServer` z innymi dostawcami plików do obsługi plików z innych lokalizacji. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/15578)w serwisie GitHub.

### <a name="considerations"></a>Zagadnienia do rozważenia

> [!WARNING]
> `UseDirectoryBrowser` i `UseStaticFiles` mogą wyciekać klucze tajne. Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane. Uważnie Przejrzyj katalogi, które są włączone za pośrednictwem `UseStaticFiles` lub `UseDirectoryBrowser` . Cały katalog i jego katalogi podrzędne stają się publicznie dostępne. Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, takie jak *\<content_root> /wwwroot*. Oddziel te pliki od widoków MVC, Razor stron (tylko 2. x), plików konfiguracji itp.

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

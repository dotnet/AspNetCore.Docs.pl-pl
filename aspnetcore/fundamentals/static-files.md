---
title: Pliki statyczne w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wyświetlać i zabezpieczać pliki statyczne oraz konfigurować zachowania statycznego oprogramowania pośredniczącego hostingu plików w aplikacji sieci web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 95a77defc7e98328e1f4e3615648b1d14485e51e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660126"
---
# <a name="static-files-in-aspnet-core"></a>Pliki statyczne w ASP.NET Core

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Scott [Addie](https://twitter.com/Scott_Addie)

Pliki statyczne, takie jak HTML, CSS, obrazy i JavaScript, to zasoby, które aplikacja ASP.NET Core służy bezpośrednio klientom. Do włączenia obsługi tych plików wymagana jest pewna konfiguracja.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Obsługa plików statycznych

Pliki statyczne są przechowywane w katalogu [głównym](xref:fundamentals/index#web-root) projektu. Katalog domyślny to *{content root}/wwwroot*, ale można go zmienić za pomocą metody [UseWebRoot.](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) Aby uzyskać więcej informacji, zobacz [Katalog główny zawartości](xref:fundamentals/index#content-root) i katalog główny sieci [Web.](xref:fundamentals/index#web-root)

Host internetowy aplikacji musi być poinformowany o katalogu głównym zawartości.

::: moniker range=">= aspnetcore-2.0"

Metoda `WebHost.CreateDefaultBuilder` ustawia katalog główny zawartości do bieżącego katalogu:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Ustaw katalog główny zawartości na bieżący katalog, wywołując [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) wewnątrz `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Pliki statyczne są dostępne za pośrednictwem ścieżki względem [katalogu głównego sieci Web.](xref:fundamentals/index#web-root) Na przykład szablon projektu **aplikacji sieci Web** zawiera kilka folderów w folderze *wwwroot:*

* **wwwroot**
  * **Css**
  * **Obrazów**
  * **Js**

Format URI dostępu do pliku w podfolderze *obrazów* jest *http://\<server_address>/images/\<image_file_name>*. Na przykład *http://localhost:9189/images/banner3.svg*.

::: moniker range=">= aspnetcore-2.1"

Jeśli jest kierowana na program .NET Framework, dodaj do projektu pakiet [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) Jeśli jest kierowana na program .NET Core, [metapakiet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) zawiera ten pakiet.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Jeśli jest kierowana na program .NET Framework, dodaj do projektu pakiet [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) Jeśli jest kierowana na program .NET Core, [metapakiet Microsoft.AspNetCore.All](xref:fundamentals/metapackage) zawiera ten pakiet.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Dodaj pakiet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.

::: moniker-end

Skonfiguruj [oprogramowanie pośredniczące,](xref:fundamentals/middleware/index) które umożliwia obsługę plików statycznych.

### <a name="serve-files-inside-of-web-root"></a>Obsługa plików wewnątrz katalogu głównego sieci Web

Wywołać [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) metody `Startup.Configure`w :

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Przeciążenie `UseStaticFiles` metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako servable. Następujące oznaczenia odwołują się do *wwwroot/images/banner1.svg:*

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

W poprzednim kodzie znak `~/` tyldy wskazuje katalog główny sieci [Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Obsługa plików poza katalogiem głównym sieci Web

Należy wziąć pod uwagę hierarchię katalogów, w której pliki statyczne, które mają być obsługiwane, znajdują się poza [katalogiem głównym sieci Web:](xref:fundamentals/index#web-root)

* **wwwroot**
  * **Css**
  * **Obrazów**
  * **Js**
* **Pliki MyStatic**
  * **Obrazów**
    * *banner1.svg*

Żądanie może uzyskać dostęp do pliku *banner1.svg,* konfigurując oprogramowanie pośredniczące plików statycznych w następujący sposób:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

W poprzednim kodzie hierarchia *katalogów MyStaticFiles* jest publicznie widoczna za pośrednictwem segmentu URI *StaticFiles.* Żądanie *http://\<server_address>/StaticFiles/images/banner1.svg* obsługuje plik *banner1.svg.*

Następujące oznaczenia odwołują się do *mystaticfiles/images/banner1.svg:*

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Ustawianie nagłówków odpowiedzi HTTP

Obiekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) może służyć do ustawiania nagłówków odpowiedzi HTTP. Oprócz konfigurowania pliku statycznego obsługującego z katalogu głównego `Cache-Control` sieci [Web,](xref:fundamentals/index#web-root)następujący kod ustawia nagłówek:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Metoda [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) istnieje w pakiecie [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)

Pliki zostały udostępnione publicznie w pamięci podręcznej przez 10 minut (600 sekund) w środowisku programistycznym:

![Dodano nagłówki odpowiedzi z nagłówkiem Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autoryzacja plików statycznych

Oprogramowanie pośredniczące plików statycznych nie zapewnia sprawdzania autoryzacji. Wszystkie pliki, które są przez nią obsługiwane, w tym te objęte *wwwroot,* są publicznie dostępne. Aby wyświetlać pliki na podstawie autoryzacji:

* Przechowuj je poza *wwwroot* i dowolnego katalogu dostępnego dla oprogramowania pośredniczącego plików statycznych.
* Podawać je za pomocą metody akcji, do której jest stosowana autoryzacja. Zwraca obiekt [FileResult:](/dotnet/api/microsoft.aspnetcore.mvc.fileresult)

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Włączanie przeglądania katalogów

Przeglądanie katalogów umożliwia użytkownikom aplikacji sieci web wyświetlanie listy katalogów i plików w określonym katalogu. Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa (patrz [Zagadnienia).](#considerations) Włącz przeglądanie katalogów, wywołując metodę [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) w `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Dodaj wymagane usługi, wywołując metodę [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) z `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Poprzedni kod umożliwia przeglądanie katalogu folderu *wwwroot/images* przy użyciu adresu URL *http://\<server_address>/MyImages,* z linkami do każdego pliku i folderu:

![przeglądanie katalogów](static-files/_static/dir-browse.png)

Zobacz [zagadnienia dotyczące](#considerations) zagrożeń bezpieczeństwa podczas włączania przeglądania.

Zanotuj dwa `UseStaticFiles` wywołania w poniższym przykładzie. Pierwsze wywołanie umożliwia wyświetlanie plików statycznych w folderze *wwwroot.* Drugie wywołanie umożliwia przeglądanie katalogu folderu *wwwroot/images* przy użyciu adresu URL *http://\<server_address>/MyImages:*

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Obsługa dokumentu domyślnego

Ustawienie domyślnej strony głównej zapewnia odwiedzającym logiczny punkt wyjścia podczas odwiedzania witryny. Aby obsługiwać stronę domyślną bez pełnego kwalifikowania identyfikatora URI przez `Startup.Configure`użytkownika, należy wywołać metodę [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) z:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`należy wywołać `UseStaticFiles` przed, aby służyć plik domyślny. `UseDefaultFiles`to nagrywarka adresów URL, która w rzeczywistości nie obsługuje pliku. Włącz oprogramowanie pośredniczące `UseStaticFiles` plików statycznych za pośrednictwem do obsługi pliku.

W `UseDefaultFiles`przypadku żądań do wyszukiwania folderów:

* *Default.htm*
* *Default.html*
* *Index.htm*
* *Index.html*

Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI. Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.

Poniższy kod zmienia domyślną nazwę pliku na *mydefault.html:*

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>Użyj pliku serwera

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>łączy w sobie `UseStaticFiles`funkcjonalność `UseDefaultFiles`, i `UseDirectoryBrowser`opcjonalnie .

Poniższy kod umożliwia wyświetlanie plików statycznych i pliku domyślnego. Przeglądanie katalogów nie jest włączone.

```csharp
app.UseFileServer();
```

Następujący kod opiera się na przeciążeniu bez parametrów, włączając przeglądanie katalogów:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Należy wziąć pod uwagę następującą hierarchię katalogów:

* **wwwroot**
  * **Css**
  * **Obrazów**
  * **Js**
* **Pliki MyStatic**
  * **Obrazów**
    * *banner1.svg*
  * *Default.html*

Poniższy kod umożliwia przeglądanie plików statycznych, plików `MyStaticFiles`domyślnych i katalogów:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser`musi być wywoływana, `EnableDirectoryBrowsing` `true`gdy wartość właściwości jest:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Korzystając z hierarchii plików i poprzedniego kodu, adresy URL rozwiązują następujące rozwiązania:

| Identyfikator URI            |                             Odpowiedź  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Jeśli w katalogu *MyStaticFiles* nie istnieje plik o domyślnej nazwie, *http://\<server_address>/StaticFiles* zwraca listę katalogów z klikalnymi łączami:

![Lista plików statycznych](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> wykonać przekierowanie po `http://{SERVER ADDRESS}/StaticFiles` stronie klienta z (bez końcowego ukośnika) do `http://{SERVER ADDRESS}/StaticFiles/` (z ukośnikiem). Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowego ukośnika.

## <a name="fileextensioncontenttypeprovider"></a>PlikExtensionContentTypeProvider

[Klasa FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) zawiera `Mappings` właściwość służącą jako mapowanie rozszerzeń plików do typów zawartości MIME. W poniższym przykładzie kilka rozszerzeń plików są rejestrowane do znanych typów MIME. Rozszerzenie *.rtf* zostanie zastąpione, a *plik .mp4* zostanie usunięty.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Niestandardowe typy zawartości

Oprogramowanie pośredniczące plików statycznych rozumie prawie 400 znanych typów zawartości plików. Jeśli użytkownik zażąda pliku o nieznanym typie pliku, oprogramowanie pośredniczące plików statycznych przekazuje żądanie do następnego oprogramowania pośredniczącego w potoku. Jeśli żadne oprogramowanie pośredniczące obsługuje żądanie, zwracana jest odpowiedź *404 Nie znaleziono.* Jeśli przeglądanie katalogów jest włączone, łącze do pliku jest wyświetlane na liście katalogów.

Poniższy kod umożliwia wyświetlanie nieznanych typów i renderuje nieznany plik jako obraz:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

W poprzednim kodzie żądanie pliku o nieznanym typie zawartości jest zwracane jako obraz.

> [!WARNING]
> Włączenie [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) jest zagrożeniem bezpieczeństwa. Jest domyślnie wyłączona, a jego użycie jest odradzane. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejszą alternatywę dla obsługi plików z niestandardowymi rozszerzeniami.

## <a name="serve-files-from-multiple-locations"></a>Obsługa plików z wielu lokalizacji

`UseStaticFiles`i `UseFileServer` domyślnie dostawca plików wskazujący na *wwwroot*. Można podać dodatkowe wystąpienia `UseStaticFiles` `UseFileServer` i z innymi dostawcami plików do obsługi plików z innych lokalizacji. Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Zagadnienia do rozważenia

> [!WARNING]
> `UseDirectoryBrowser`i `UseStaticFiles` może wyciekać tajemnice. Zaleca się wyłączenie przeglądania katalogów w produkcji. Dokładnie sprawdź, które katalogi `UseStaticFiles` `UseDirectoryBrowser`są włączone za pośrednictwem lub . Cały katalog i jego podkatasty stają się publicznie dostępne. Przechowuj pliki odpowiednie do publicznego wyświetlania w dedykowanym katalogu, takie jak * \<content_root>/wwwroot*. Oddziel te pliki od widoków MVC, stron Razor (tylko 2.x), plików konfiguracyjnych itp.

* Adresy URL zawartości `UseDirectoryBrowser` udostępnianej z `UseStaticFiles` i podlegają ograniczeniom dotyczącymi wrażliwości i znaków w podstawowym systemie plików. Na przykład system Windows jest bez&mdash;uwzględniania wielkości liter macOS i Linux nie są.

* aplikacje ASP.NET Core hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przekazywania wszystkich żądań do aplikacji, w tym żądań plików statycznych. Nie jest używany statyczny program obsługi plików IIS. Nie ma szans na obsługę żądań, zanim będą obsługiwane przez moduł.

* Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć statyczny program obsługi plików usług IIS na poziomie serwera lub witryny sieci Web:
    1. Przejdź do funkcji **Moduły.**
    1. Wybierz **staticFileModule** na liście.
    1. Kliknij **pozycję Usuń** na pasku bocznym **Akcje.**

> [!WARNING]
> Jeśli program obsługi plików statycznych IIS jest **włączony, a** ASP.NET modułu rdzenia jest niepoprawnie skonfigurowany, są obsługiwane pliki statyczne. Dzieje się tak na przykład, jeśli plik *web.config* nie jest wdrożony.

* Umieść pliki kodu (w tym *.cs* i *.cshtml)* poza [katalogiem głównym](xref:fundamentals/index#web-root)projektu aplikacji. W związku z tym zostanie utworzona separacja logiczna między zawartością po stronie klienta aplikacji a kodem opartym na serwerze. Zapobiega to wyciekaniu kodu po stronie serwera.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Wprowadzenie do programu ASP.NET Core](xref:index)

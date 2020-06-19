---
title: Interfejs użytkownika wielokrotnego użytku Razor w bibliotekach klas z ASP.NET Core
author: Rick-Anderson
description: Wyjaśnia, jak utworzyć interfejs użytkownika wielokrotnego użytku Razor przy użyciu częściowych widoków w bibliotece klas w ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: adfcc281f285892583ab8a21c014d6fcb120af9c
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102848"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Utwórz interfejs użytkownika wielokrotnego użytku przy użyciu Razor projektu biblioteki klas w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razorwidoki, strony, kontrolery, modele stron, [ Razor składniki](xref:blazor/components/class-libraries), [składniki widoku](xref:mvc/views/view-components)i modele danych mogą być wbudowane w Razor bibliotekę klas (RCL). RCL można spakować i ponownie użyć. Aplikacje mogą zawierać RCL i przesłonić widoki oraz strony, które zawiera. Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Tworzenie biblioteki klas zawierającej Razor interfejs użytkownika

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
* Wybierz pozycję ** Razor Biblioteka klas** > **dalej**.
* Nazwij bibliotekę (na przykład "RazorClassLib"), > **Utwórz**. Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .
* Wybierz **strony i widoki pomocy technicznej** , jeśli chcesz obsługiwać widoki. Domyślnie Razor obsługiwane są tylko strony. Wybierz przycisk **Utwórz**.

RazorDomyślnie szablon biblioteki klas (RCL) jest Razor domyślnym programowaniem składników. Opcja **strony i widoki pomocy technicznej** obsługuje strony i widoki.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

W wierszu polecenia Uruchom polecenie `dotnet new razorclasslib` . Przykład:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

RazorDomyślnie szablon biblioteki klas (RCL) jest Razor domyślnym programowaniem składników. `--support-pages-and-views` `dotnet new razorclasslib --support-pages-and-views` Aby zapewnić obsługę stron i widoków, należy przekazać opcję ().

Aby uzyskać więcej informacji, zobacz [dotnet New](/dotnet/core/tools/dotnet-new). Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .

---

Dodaj Razor pliki do RCL.

Szablony ASP.NET Core założono, że zawartość RCL znajduje się w folderze *obszarów* . Zobacz [układ stron RCL](#rcl-pages-layout) , aby utworzyć RCL, który uwidacznia zawartość `~/Pages` zamiast `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>Odwołanie do zawartości RCL

RCL może odwoływać się do:

* Pakiet NuGet. Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet Dodawanie pakietu](/dotnet/core/tools/dotnet-add-package) oraz [Tworzenie i publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}. csproj*. Zobacz sekcję [dotnet — Dodaj odwołanie](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Zastąp widoki, częściowe widoki i strony

Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web. Na przykład Dodaj *WebApp1/Areas/webfeature/Pages/Strona1. cshtml* do WebApp1, a element Strona1 w WebApp1 będzie miał pierwszeństwo przed STRONA1 w RCL.

W przykładowym pobieraniu Zmień nazwę *WebApp1/obszarów/MyFeature2* na *WebApp1/Areas/webfeature* , aby przetestować priorytet.

Skopiuj widok części *RazorUIClassLib/Areas/Webfeature/Pages/Shared/_Message. cshtml* częściowo do *WebApp1/Areas/Webfeature/Pages/Shared/_Message. cshtml*. Zaktualizuj adiustację, aby wskazać nową lokalizację. Skompiluj i uruchom aplikację, aby sprawdzić, czy jest używana częściowa wersja aplikacji.

### <a name="rcl-pages-layout"></a>Układ stron RCL

Aby odwoływać się do zawartości RCL, tak jakby była częścią folderu *stron* aplikacji sieci Web, Utwórz projekt RCL o następującej strukturze plików:

* *RazorUIClassLib/strony*
* *RazorUIClassLib/strony/udostępnione*

Załóżmy, że *RazorUIClassLib/Pages/Shared* zawiera dwa częściowe pliki: *_Header. cshtml* i *_Footer. cshtml*. `<partial>`Tagi można dodać do pliku *_Layout. cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Tworzenie RCL przy użyciu statycznych zasobów

RCL może wymagać pomocnika zasobów statycznych, do których można odwoływać się za pomocą RCL lub aplikacji zużywanej przez RCL. ASP.NET Core umożliwia tworzenie RCLs zawierających statyczne zasoby, które są dostępne dla aplikacji zużywanej.

Aby dołączyć zasoby towarzyszące jako część RCL, Utwórz folder *wwwroot* w bibliotece klas i Dołącz wszystkie wymagane pliki w tym folderze.

Podczas pakowania RCL wszystkie zasoby towarzyszące w folderze *wwwroot* zostaną automatycznie dołączone do pakietu.

Użyj `dotnet pack` polecenia zamiast wersji NuGet.exe `nuget pack` .

### <a name="exclude-static-assets"></a>Wyklucz zasoby statyczne

Aby wykluczyć statyczne elementy zawartości, Dodaj żądaną ścieżkę wykluczenia do `$(DefaultItemExcludes)` grupy właściwości w pliku projektu. Oddzielaj wpisy średnikami ( `;` ).

W poniższym przykładzie arkusz stylów *lib. css* w folderze *wwwroot* nie jest traktowany jako statyczny zasób i nie jest uwzględniony w opublikowanym RCL:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integracja języka TypeScript

Aby dołączyć pliki TypeScript do RCL:

1. Umieść pliki TypeScript (*. TS*) poza folderem *wwwroot* . Na przykład Umieść pliki w folderze *Client* .

1. Skonfiguruj dane wyjściowe kompilacji TypeScript dla folderu *wwwroot* . Ustaw `TypescriptOutDir` Właściwość wewnątrz elementu `PropertyGroup` w pliku projektu:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Dołącz obiekt docelowy TypeScript jako zależność `ResolveCurrentProjectStaticWebAssets` obiektu docelowego, dodając następujący element docelowy wewnątrz `PropertyGroup` pliku projektu:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Korzystanie z zawartości z RCL, do którego istnieje odwołanie

Pliki znajdujące się w folderze *WWWROOT* RCL są uwidocznione w RCL lub aplikacji zużywanej pod prefiksem `_content/{LIBRARY NAME}/` . Na przykład biblioteka o nazwie * Razor . Klasa. lib* skutkuje ścieżką do zawartości statycznej w lokalizacji `_content/Razor.Class.Lib/` . Podczas tworzenia pakietu NuGet, a nazwa zestawu nie jest taka sama jak identyfikator pakietu, użyj identyfikatora pakietu dla `{LIBRARY NAME}` .

Aplikacja, która zużywa odwołania, odwołuje się do statycznych zasobów udostępnianych przez bibliotekę z `<script>` ,, `<style>` `<img>` i innymi tagami HTML. Aplikacja zużywana musi mieć włączoną [obsługę pliku statycznego](xref:fundamentals/static-files) w programie `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

W przypadku uruchamiania aplikacji zużywającej dane wyjściowe kompilacji ( `dotnet run` ) statyczne zasoby sieci Web są domyślnie włączone w środowisku programistycznym. Aby zapewnić obsługę zasobów w innych środowiskach podczas uruchamiania z danych wyjściowych kompilacji, należy wywołać `UseStaticWebAssets` konstruktora hosta w *program.cs*:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

Wywołanie `UseStaticWebAssets` nie jest wymagane w przypadku uruchamiania aplikacji z opublikowanych danych wyjściowych ( `dotnet publish` ).

### <a name="multi-project-development-flow"></a>Przepływ programistyczny dla wieloprojektowych

Po uruchomieniu aplikacji zużywanej przez aplikację:

* Zasoby w RCL pozostają w swoich oryginalnych folderach. Elementy zawartości nie są przenoszone do aplikacji zużywanej.
* Wszelkie zmiany w folderze *WWWROOT* RCL są odzwierciedlone w aplikacji zużywanej po odtworzeniu RCL i niepomyślnym skompilowaniu aplikacji.

Po skompilowaniu RCL jest tworzony manifest, który opisuje lokalizacje statycznego elementu zawartości sieci Web. Aplikacja, która korzysta z aplikacji, odczytuje manifest w czasie wykonywania, aby wykorzystać zasoby z przywoływanych projektów i pakietów. Po dodaniu nowego elementu zawartości do RCL należy ponownie skompilować RCL, aby zaktualizować jego manifest, zanim aplikacja zużywa dostęp do nowego elementu zawartości.

### <a name="publish"></a>Publikowanie

Po opublikowaniu aplikacji składniki towarzyszące ze wszystkich przywoływanych projektów i pakietów są kopiowane do folderu *wwwroot* opublikowanej aplikacji w obszarze `_content/{LIBRARY NAME}/` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razorwidoki, strony, kontrolery, modele stron, [ Razor składniki](xref:blazor/components/class-libraries), [składniki widoku](xref:mvc/views/view-components)i modele danych mogą być wbudowane w Razor bibliotekę klas (RCL). RCL można spakować i ponownie użyć. Aplikacje mogą zawierać RCL i przesłonić widoki oraz strony, które zawiera. Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Tworzenie biblioteki klas zawierającej Razor interfejs użytkownika

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.
* Wybierz **ASP.NET Core aplikacji sieci Web**.
* Nazwij bibliotekę (na przykład "RazorClassLib") > **OK**. Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .
* Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.
* Wybierz pozycję ** Razor Biblioteka klas** > **OK**.

RCL ma następujący plik projektu:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

W wierszu polecenia Uruchom polecenie `dotnet new razorclasslib` . Przykład:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Aby uzyskać więcej informacji, zobacz [dotnet New](/dotnet/core/tools/dotnet-new). Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .

---

Dodaj Razor pliki do RCL.

Szablony ASP.NET Core założono, że zawartość RCL znajduje się w folderze *obszarów* . Zobacz [układ stron RCL](#rcl-pages-layout) , aby utworzyć RCL, który uwidacznia zawartość `~/Pages` zamiast `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>Odwołanie do zawartości RCL

RCL może odwoływać się do:

* Pakiet NuGet. Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet Dodawanie pakietu](/dotnet/core/tools/dotnet-add-package) oraz [Tworzenie i publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}. csproj*. Zobacz sekcję [dotnet — Dodaj odwołanie](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Przewodnik: Tworzenie projektu RCL i korzystanie z niego z Razor projektu stron

Możesz pobrać [kompletny projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) i przetestować go zamiast go tworzyć. Pobranie próbek zawiera dodatkowy kod i linki, które ułatwiają przetestowanie projektu. Możesz wystawić opinię na temat [tego problemu](https://github.com/dotnet/AspNetCore.Docs/issues/6098) w usłudze GitHub, korzystając z komentarzy dotyczących pobierania próbek, a także instrukcje krok po kroku.

### <a name="test-the-download-app"></a>Testowanie aplikacji do pobrania

Jeśli nie pobrano ukończonej aplikacji i wolisz utworzyć projekt przewodnika, przejdź do [następnej sekcji](#create-an-rcl).

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Otwórz plik *sln* w programie Visual Studio. Uruchom aplikację.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Z wiersza polecenia w katalogu *CLI* Utwórz aplikację RCL i sieć Web.

```dotnetcli
dotnet build
```

Przejdź do katalogu *WebApp1* i uruchom aplikację:

```dotnetcli
dotnet run
```

---

Postępuj zgodnie z instrukcjami w temacie [test WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Utwórz RCL

W tej sekcji jest tworzony RCL. Razorpliki są dodawane do RCL.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Utwórz projekt RCL:

* Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.
* Wybierz **ASP.NET Core aplikacji sieci Web**.
* Nadaj aplikacji nazwę **RazorUIClassLib** > **OK**.
* Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.
* Wybierz pozycję ** Razor Biblioteka klas** > **OK**.
* Dodaj Razor plik widoku częściowego o nazwie *RazorUIClassLib/Areas/webfeature/Pages/Shared/_Message. cshtml*.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

W wierszu polecenia Uruchom następujące polecenie:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Poprzednie polecenia:

* Tworzy `RazorUIClassLib` RCL.
* Tworzy Razor stronę _Message i dodaje ją do RCL. `-np`Parametr tworzy stronę bez `PageModel` .
* Tworzy plik [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) i dodaje go do RCL.

Plik *_ViewStart. cshtml* jest wymagany do używania układu Razor projektu stron (który jest dodawany w następnej sekcji).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Dodawanie Razor plików i folderów do projektu

* Zastąp znaczniki w *RazorUIClassLib/Areas/webfeature/Pages/Shared/_Message. cshtml* następującym kodem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Zastąp znaczniki w *RazorUIClassLib/Areas/webfeature/Pages/Strona1. cshtml* następującym kodem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`jest wymagany do korzystania z widoku częściowego ( `<partial name="_Message" />` ). Zamiast `@addTagHelper` dyrektywy, można dodać plik *_ViewImports. cshtml* . Przykład:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Aby uzyskać więcej informacji na temat *_ViewImports. cshtml*, zobacz [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives)

* Kompiluj bibliotekę klas, aby upewnić się, że nie występują błędy kompilatora:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Dane wyjściowe kompilacji zawierają *RazorUIClassLib.dll* i *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* zawiera skompilowaną Razor zawartość.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Korzystanie z Razor biblioteki interfejsu użytkownika z Razor projektu stron

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Tworzenie Razor aplikacji sieci Web dla stron:

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie > **Dodaj** > **Nowy projekt**.  
* Wybierz **ASP.NET Core aplikacji sieci Web**.
* Nadaj aplikacji nazwę **WebApp1**.
* Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.
* Wybierz pozycję **aplikacja sieci Web** > **OK**.

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **Ustaw jako projekt startowy**.
* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **kompilacja zależności** > **projektu zależności**.
* Sprawdź **RazorUIClassLib** jako zależność **WebApp1**.
* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **Dodaj** > **odwołanie**.
* W oknie dialogowym **Menedżer odwołań** Sprawdź **RazorUIClassLib** > **OK**.

Uruchom aplikację.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Utwórz Razor aplikację internetową stron i plik rozwiązania zawierający Razor aplikację Pages i RCL:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Kompiluj i uruchom aplikację sieci Web:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Test WebApp1

Przejdź do `/MyFeature/Page1` strony, aby sprawdzić, czy Razor Biblioteka klas interfejsu użytkownika jest używana.

## <a name="override-views-partial-views-and-pages"></a>Zastąp widoki, częściowe widoki i strony

Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web. Na przykład Dodaj *WebApp1/Areas/webfeature/Pages/Strona1. cshtml* do WebApp1, a element Strona1 w WebApp1 będzie miał pierwszeństwo przed STRONA1 w RCL.

W przykładowym pobieraniu Zmień nazwę *WebApp1/obszarów/MyFeature2* na *WebApp1/Areas/webfeature* , aby przetestować priorytet.

Skopiuj widok części *RazorUIClassLib/Areas/Webfeature/Pages/Shared/_Message. cshtml* częściowo do *WebApp1/Areas/Webfeature/Pages/Shared/_Message. cshtml*. Zaktualizuj adiustację, aby wskazać nową lokalizację. Skompiluj i uruchom aplikację, aby sprawdzić, czy jest używana częściowa wersja aplikacji.

### <a name="rcl-pages-layout"></a>Układ stron RCL

Aby odwoływać się do zawartości RCL, tak jakby była częścią folderu *stron* aplikacji sieci Web, Utwórz projekt RCL o następującej strukturze plików:

* *RazorUIClassLib/strony*
* *RazorUIClassLib/strony/udostępnione*

Załóżmy, że *RazorUIClassLib/Pages/Shared* zawiera dwa częściowe pliki: *_Header. cshtml* i *_Footer. cshtml*. `<partial>`Tagi można dodać do pliku *_Layout. cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/components/class-libraries>

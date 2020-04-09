---
title: Interfejs Razor wielokrotnego rozrządu wielokrotnego rozrządu w bibliotekach klas z ASP.NET Core
author: Rick-Anderson
description: W tym artykule wyjaśniono, jak utworzyć interfejs użytkownika razor wielokrotnego użycia przy użyciu widoków częściowych w bibliotece klas w ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667567"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Tworzenie interfejsu użytkownika wielokrotnego użycia przy użyciu projektu biblioteki klas Razor w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Widoki maszynki do golenia, strony, kontrolery, modele stron, [komponenty Razor,](xref:blazor/class-libraries) [Wyświetl komponenty](xref:mvc/views/view-components)i modele danych mogą być wbudowane w bibliotekę klas Razor (RCL). RCL mogą być pakowane i ponownie. Aplikacje mogą zawierać rcl i zastąpić widoki i strony, które zawiera. Gdy widok, widok częściowy lub razor Page znajduje się zarówno w aplikacji sieci web i RCL, znaczników Razor (plik*cshtml)* w aplikacji sieci web ma pierwszeństwo.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Tworzenie biblioteki klas zawierającej interfejs użytkownika razor

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z programu Visual Studio wybierz pozycję **Utwórz nowy projekt**.
* Wybierz **bibliotekę** > klas Razor **Next**.
* Nazwij bibliotekę (na przykład "RazorClassLib"), > **Utwórz**. Aby uniknąć kolizji nazwy pliku z wygenerowaną biblioteką widoku, `.Views`upewnij się, że nazwa biblioteki nie kończy się na .
* Wybierz **strony pomocy technicznej i widoki,** jeśli chcesz obsługiwać widoki. Domyślnie obsługiwane są tylko strony Razor. Wybierz **pozycję Utwórz**.

Szablon biblioteki klas Razor (RCL) domyślnie domyślnie jest owy do tworzenia komponentów Razor. **Opcja Obsługa stron i widoków** obsługuje strony i widoki.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Z wiersza polecenia `dotnet new razorclasslib`uruchom polecenie . Przykład:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Szablon biblioteki klas Razor (RCL) domyślnie domyślnie jest owy do tworzenia komponentów Razor. Przekaż `--support-pages-and-views` opcję`dotnet new razorclasslib --support-pages-and-views`( ), aby zapewnić obsługę stron i widoków.

Aby uzyskać więcej informacji, zobacz [dotnet new](/dotnet/core/tools/dotnet-new). Aby uniknąć kolizji nazwy pliku z wygenerowaną biblioteką widoku, `.Views`upewnij się, że nazwa biblioteki nie kończy się na .

---

Dodaj pliki Razor do RCL.

Szablony ASP.NET Core zakładają, że zawartość RCL znajduje się w folderze *Obszary.* Zobacz [układ strony RCL,](#rcl-pages-layout) aby utworzyć umowę `~/Pages` RCL, która udostępnia zawartość, a nie `~/Areas/Pages`.

## <a name="reference-rcl-content"></a>Odwołuje się do zawartości RCL

RCL może się odwoływać:

* Pakiet NuGet. Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet dodać pakiet](/dotnet/core/tools/dotnet-add-package) i tworzenie i [publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Zobacz [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Zastępowanie widoków, widoków częściowych i stron

Gdy widok, widok częściowy lub razor Page znajduje się zarówno w aplikacji sieci web i RCL, znaczników Razor (plik*cshtml)* w aplikacji sieci web ma pierwszeństwo. Na przykład dodaj *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* do aplikacji WebApp1, a strona1 w aplikacji WebApp1 będzie mieć pierwszeństwo przed stroną1 w rcl.

W przykładowym pobieraniu zmień nazwę *webapp1/areas/MyFeature2* na *WebApp1/Areas/MyFeature,* aby przetestować pierwszeństwo.

Skopiuj widok *częściowy RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* do *aplikacji WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Zaktualizuj znaczniki, aby wskazać nową lokalizację. Skompiluj i uruchom aplikację, aby sprawdzić, czy używana jest wersja częściowa aplikacji.

### <a name="rcl-pages-layout"></a>Układ strony RCL

Aby odwoływać się do zawartości RCL tak, jakby była częścią folderu *Pages* aplikacji sieci web, utwórz projekt RCL z następującą strukturą plików:

* *RazorUIClassLib/Strony*
* *RazorUIClassLib/Pages/Udostępnione*

Załóżmy, że *RazorUIClassLib/Pages/Shared* zawiera dwa pliki częściowe: *_Header.cshtml* i *_Footer.cshtml*. Znaczniki `<partial>` można dodać do *pliku _Layout.cshtml:*

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Tworzenie listy RCL z zasobami statycznymi

Lista RCL może wymagać towarzyszących zasobów statycznych, do których można się odwoływać za pomocą listy RCL lub aplikacji zużywającej listy RCL. ASP.NET Core umożliwia tworzenie list RL, które zawierają zasoby statyczne, które są dostępne dla aplikacji zużywającej.

Aby uwzględnić zasoby towarzyszące jako część listy RCL, utwórz folder *wwwroot* w bibliotece klas i dołącz do tego folderu wszystkie wymagane pliki.

Podczas pakowania listy RCL wszystkie zasoby towarzyszące w folderze *wwwroot* są automatycznie uwzględniane w pakiecie.

### <a name="exclude-static-assets"></a>Wykluczanie aktywów statycznych

Aby wykluczyć zasoby statyczne, dodaj żądaną `$(DefaultItemExcludes)` ścieżkę wykluczenia do grupy właściwości w pliku projektu. Oddzielne wpisy ze średnikiem`;`( ).

W poniższym przykładzie arkusz *stylów lib.css* w folderze *wwwroot* nie jest uważany za zasób statyczny i nie jest uwzględniony w opublikowanej liście RCL:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integracja z typemscriptu

Aby uwzględnić pliki TypeScript w rcl:

1. Umieść pliki TypeScript *(ts)* poza folderem *wwwroot.* Na przykład umieść pliki w folderze *klient.*

1. Skonfiguruj dane wyjściowe kompilacji TypeScript dla folderu *wwwroot.* Ustaw `TypescriptOutDir` właściwość wewnątrz `PropertyGroup` pliku projektu:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Uwzględnij cel TypeScript jako zależność `ResolveCurrentProjectStaticWebAssets` obiektu docelowego, dodając `PropertyGroup` w pliku projektu następujący obiekt docelowy:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Korzystanie z zawartości z listy RCL, do którego istnieje odwołanie

Pliki zawarte w folderze *wwwroot* rcl są narażone na rcl lub aplikacji zużywającej `_content/{LIBRARY NAME}/`pod prefiksem . Na przykład biblioteka o nazwie *Razor.Class.Lib* powoduje ścieżkę do zawartości statycznej w pliku `_content/Razor.Class.Lib/`. Podczas tworzenia pakietu NuGet i nazwa zestawu nie jest taka sama jak identyfikator pakietu, należy użyć identyfikatora pakietu dla `{LIBRARY NAME}`.

Aplikacja zużywająca odwołuje się do zasobów statycznych `<script>` `<style>`dostarczanych przez bibliotekę za pomocą `<img>`, i innych tagów HTML. Aplikacja zużywająca musi mieć włączoną `Startup.Configure`obsługę plików [statycznych](xref:fundamentals/static-files) w:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Podczas uruchamiania aplikacji zużywającej`dotnet run`z danych wyjściowych kompilacji ( ), statyczne zasoby sieci web są domyślnie włączone w środowisku programistycznym. Aby obsługiwać zasoby w innych środowiskach podczas `UseStaticWebAssets` uruchamiania z danych wyjściowych kompilacji, wywołaj konstruktora hosta w *Program.cs:*

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

Wywołanie `UseStaticWebAssets` nie jest wymagane podczas uruchamiania aplikacji`dotnet publish`z opublikowanych danych wyjściowych ( ).

### <a name="multi-project-development-flow"></a>Przepływ rozwoju wielu projektów

Po uruchomieniu aplikacji zużywającej:

* Zasoby w liście RCL pozostają w oryginalnych folderach. Zasoby nie są przenoszone do aplikacji zużywającej.
* Wszelkie zmiany w folderze *wwwroot* listy RCL jest odzwierciedlona w aplikacji zużywającej po RCL jest przebudowywany i bez przebudowy aplikacji zużywających.

Po zbudowaniu listy RCL jest produkowany manifest opisujący statyczne lokalizacje zasobów sieci web. Aplikacja zużywająca odczytuje manifest w czasie wykonywania, aby korzystać z zasobów z projektów i pakietów, do których istnieje odwołanie. Po dodaniu nowego zasobu do listy RCL listy RCL musi zostać przebudowany, aby zaktualizować jego manifest, zanim aplikacja zużywająca może uzyskać dostęp do nowego zasobu.

### <a name="publish"></a>Publikowanie

Po opublikowaniu aplikacji zasoby towarzyszące ze wszystkich projektów i pakietów, do których istnieją odwołania, `_content/{LIBRARY NAME}/`są kopiowane do folderu *wwwroot* opublikowanej aplikacji w obszarze .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Widoki maszynki do golenia, strony, kontrolery, modele stron, [komponenty Razor,](xref:blazor/class-libraries) [Wyświetl komponenty](xref:mvc/views/view-components)i modele danych mogą być wbudowane w bibliotekę klas Razor (RCL). RCL mogą być pakowane i ponownie. Aplikacje mogą zawierać rcl i zastąpić widoki i strony, które zawiera. Gdy widok, widok częściowy lub razor Page znajduje się zarówno w aplikacji sieci web i RCL, znaczników Razor (plik*cshtml)* w aplikacji sieci web ma pierwszeństwo.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Tworzenie biblioteki klas zawierającej interfejs użytkownika razor

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.
* Wybierz **ASP.NET podstawową aplikację sieci Web**.
* Nazwij bibliotekę (na przykład "RazorClassLib") > **OK**. Aby uniknąć kolizji nazwy pliku z wygenerowaną biblioteką widoku, `.Views`upewnij się, że nazwa biblioteki nie kończy się na .
* Sprawdź, **czy ASP.NET wybrano core 2.1** lub nowszą.
* Wybierz opcję **Biblioteka** > klas Razor **OK**.

RCL ma następujący plik projektu:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Z wiersza polecenia `dotnet new razorclasslib`uruchom polecenie . Przykład:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Aby uzyskać więcej informacji, zobacz [dotnet new](/dotnet/core/tools/dotnet-new). Aby uniknąć kolizji nazwy pliku z wygenerowaną biblioteką widoku, `.Views`upewnij się, że nazwa biblioteki nie kończy się na .

---

Dodaj pliki Razor do RCL.

Szablony ASP.NET Core zakładają, że zawartość RCL znajduje się w folderze *Obszary.* Zobacz [układ strony RCL,](#rcl-pages-layout) aby utworzyć umowę `~/Pages` RCL, która udostępnia zawartość, a nie `~/Areas/Pages`.

## <a name="reference-rcl-content"></a>Odwołuje się do zawartości RCL

RCL może się odwoływać:

* Pakiet NuGet. Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet dodać pakiet](/dotnet/core/tools/dotnet-add-package) i tworzenie i [publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Zobacz [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Instruktaż: Tworzenie projektu RCL i używanie go w projekcie Razor Pages

Możesz pobrać [kompletny projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) i przetestować go, a nie go utworzyć. Przykładowe pobieranie zawiera dodatkowy kod i łącza, które ułatwiają testowanie projektu. Możesz wystawić opinię w tym problemie z [githubem](https://github.com/dotnet/AspNetCore.Docs/issues/6098) z komentarzami dotyczącymi próbek pobierania w porównaniu z instrukcjami krok po kroku.

### <a name="test-the-download-app"></a>Testowanie aplikacji do pobierania

Jeśli nie pobrałeś ukończonej aplikacji i wolisz utworzyć projekt instruktażowy, przejdź do [następnej sekcji](#create-an-rcl).

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Otwórz plik *.sln* w programie Visual Studio. Uruchom aplikację.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Z wiersza polecenia w katalogu *cli* skompiluj rcl i aplikację internetową.

```dotnetcli
dotnet build
```

Przejdź do katalogu *WebApp1* i uruchom aplikację:

```dotnetcli
dotnet run
```

---

Postępuj zgodnie z instrukcjami w [aplikacji Test WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Tworzenie listy RCL

W tej sekcji tworzony jest rcl. Pliki brzytwy są dodawane do RCL.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Utwórz projekt RCL:

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.
* Wybierz **ASP.NET podstawową aplikację sieci Web**.
* Nazwij aplikację **RazorUIClassLib** > **OK**.
* Sprawdź, **czy ASP.NET wybrano core 2.1** lub nowszą.
* Wybierz opcję **Biblioteka** > klas Razor **OK**.
* Dodaj plik widoku częściowego Razor o nazwie *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

W wierszu polecenia uruchom następujące czynności:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Poprzednie polecenia:

* Tworzy `RazorUIClassLib` rcl.
* Tworzy stronę razor _Message i dodaje ją do listy RCL. Parametr `-np` tworzy stronę bez `PageModel`pliku .
* Tworzy [plik _ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) i dodaje go do listy RCL.

Plik *_ViewStart.cshtml* jest wymagany do użycia układu projektu Razor Pages (który jest dodawany w następnej sekcji).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Dodawanie plików i folderów Razor do projektu

* Zastąp znaczniki w *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* następującym kodem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Zastąp znaczniki w *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* następującym kodem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`jest wymagane do korzystania z`<partial name="_Message" />`widoku częściowego ( ). Zamiast dołączać `@addTagHelper` dyrektywę, można dodać plik *_ViewImports.cshtml.* Przykład:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Aby uzyskać więcej informacji na temat *_ViewImports.cshtml*, zobacz [Importowanie dyrektyw udostępnionych](xref:mvc/views/layout#importing-shared-directives)

* Tworzenie biblioteki klas w celu sprawdzenia, czy nie ma żadnych błędów kompilatora:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Dane wyjściowe kompilacji zawiera *razorUIClassLib.dll* i *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* zawiera skompilowaną zawartość Razor.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Korzystanie z biblioteki razor UI z projektu Razor Pages

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Tworzenie aplikacji internetowej Razor Pages:

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie > **Dodaj** > **nowy projekt**.  
* Wybierz **ASP.NET podstawową aplikację sieci Web**.
* Nazwij aplikację **WebApp1**.
* Sprawdź, **czy ASP.NET wybrano core 2.1** lub nowszą.
* Wybierz **pozycję Aplikacja** > sieci Web **OK**.

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **webapp1** i wybierz pozycję **Ustaw jako projekt startowy**.
* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **webapp1** i wybierz polecenie **Konsylij** > **zależności zależności projektu**.
* Sprawdź **RazorUIClassLib** jako zależność **WebApp1**.
* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **webapp1** i wybierz pozycję **Dodaj** > **odwołanie**.
* W oknie dialogowym **Menedżer odwołań** zaznacz numer **RazorUIClassLib** > **OK**.

Uruchom aplikację.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Utwórz aplikację internetową Razor Pages i plik rozwiązania zawierający aplikację Razor Pages i RCL:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Tworzenie i uruchamianie aplikacji internetowej:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Testowanie webapp1

Przejdź do, aby `/MyFeature/Page1` sprawdzić, czy biblioteka klas razor UI jest w użyciu.

## <a name="override-views-partial-views-and-pages"></a>Zastępowanie widoków, widoków częściowych i stron

Gdy widok, widok częściowy lub razor Page znajduje się zarówno w aplikacji sieci web i RCL, znaczników Razor (plik*cshtml)* w aplikacji sieci web ma pierwszeństwo. Na przykład dodaj *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* do aplikacji WebApp1, a strona1 w aplikacji WebApp1 będzie mieć pierwszeństwo przed stroną1 w rcl.

W przykładowym pobieraniu zmień nazwę *webapp1/areas/MyFeature2* na *WebApp1/Areas/MyFeature,* aby przetestować pierwszeństwo.

Skopiuj widok *częściowy RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* do *aplikacji WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Zaktualizuj znaczniki, aby wskazać nową lokalizację. Skompiluj i uruchom aplikację, aby sprawdzić, czy używana jest wersja częściowa aplikacji.

### <a name="rcl-pages-layout"></a>Układ strony RCL

Aby odwoływać się do zawartości RCL tak, jakby była częścią folderu *Pages* aplikacji sieci web, utwórz projekt RCL z następującą strukturą plików:

* *RazorUIClassLib/Strony*
* *RazorUIClassLib/Pages/Udostępnione*

Załóżmy, że *RazorUIClassLib/Pages/Shared* zawiera dwa pliki częściowe: *_Header.cshtml* i *_Footer.cshtml*. Znaczniki `<partial>` można dodać do *pliku _Layout.cshtml:*

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/class-libraries>

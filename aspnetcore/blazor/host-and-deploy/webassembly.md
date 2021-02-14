---
title: Hostowanie i wdrażanie ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
no-loc:
- appsettings.json
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 04eba2e004e920e9ca799b316781857f0b0b4ca3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279789"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Hostowanie i wdrażanie ASP.NET Core Blazor WebAssembly

Z [ Blazor WebAssembly modelem hostingu](xref:blazor/hosting-models#blazor-webassembly):

* BlazorAplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.
* Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.

Obsługiwane są następujące strategie wdrażania:

* BlazorAplikacja jest obsługiwana przez aplikację ASP.NET Core. Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.
* BlazorAplikacja jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi Blazor aplikacji. Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu Blazor WebAssembly aplikacji jako PODRZĘDNEJ aplikacji usług IIS.

## <a name="compression"></a>Kompresja

Po Blazor WebAssembly opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania. Używane są następujące algorytmy kompresji:

* [Brotli](https://tools.ietf.org/html/rfc7932) (najwyższy poziom)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor korzysta z hosta, aby zapewnić odpowiednie skompresowane pliki. W przypadku korzystania z ASP.NET Core hostowanego projektu host jest w stanie wykonywać negocjacje zawartości i obsługiwać statycznie skompresowane pliki. W przypadku udostępniania Blazor WebAssembly aplikacji autonomicznej może być wymagane dodatkowe działanie, aby zapewnić obsługę plików skompresowanych statycznie:

* Aby uzyskać `web.config` konfigurację kompresji usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) . 
* Podczas hostingu w rozwiązaniach hostingu statycznego, które nie obsługują negocjowanej statycznie negocjacji zawartości plików, na przykład stron usługi GitHub, należy rozważyć skonfigurowanie aplikacji do pobierania i dekodowania skompresowanych plików Brotli:

  * Uzyskaj dekoder JavaScript Brotli z repozytorium usługi [GitHub firmy Google/Brotli](https://github.com/google/brotli). Plik dekodera ma nazwę `decode.js` i znajduje się w [ `js` folderze](https://github.com/google/brotli/tree/master/js)repozytorium.
  
    > [!NOTE]
    > Regresja jest obecna w wersji zminimalizowanego `decode.js` skryptu ( `decode.min.js` ) w [repozytorium usługi GitHub Google/brotli](https://github.com/google/brotli). Zminifikować skrypt na własne (na przykład zobacz [BuildBundlerMinifiering and minifikacja](xref:client-side/bundling-and-minification#configure-bundling-and-minification)) lub Użyj [pakietu npm](https://www.npmjs.com/package/brotli) do momentu rozwiązania problemu [TypeError w decode.min.js (#881 Google/brotli)](https://github.com/google/brotli/issues/881) . Przykładowy kod w tej sekcji używa wersji **unminified** skryptu.

  * Zaktualizuj aplikację, aby użyć dekodera. Zmień adiustację wewnątrz tagu zamykającego `<body>` w `wwwroot/index.html` następujący sposób:
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
Aby wyłączyć kompresję, należy dodać `BlazorEnableCompression` Właściwość programu MSBuild do pliku projektu aplikacji i ustawić wartość na `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

`BlazorEnableCompression`Właściwość można przesłać do [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia z następującą składnią w powłoce poleceń:

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>Ponownie Napisz adresy URL pod kątem prawidłowego routingu

Żądania routingu dla składników strony w Blazor WebAssembly aplikacji nie są tak proste jak żądania routingu w Blazor Server hostowanej aplikacji. Weź pod uwagę Blazor WebAssembly aplikację z dwoma składnikami:

* `Main.razor`: Ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika ( `href="About"` ).
* `About.razor`: `About` składnik.

Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/` ):

1. Przeglądarka wykonuje żądanie.
1. Zostanie zwrócona domyślna strona, która zwykle `index.html` .
1. `index.html` wyładowania aplikacji.
1. Blazorładuje router, a Razor `Main` składnik jest renderowany.

Na stronie głównej wybranie linku do `About` składnika działa na kliencie, ponieważ Blazor router uniemożliwia przeglądarce wykonywanie żądania w Internecie do `www.contoso.com` programu `About` i obsługuje wyrenderowany `About` składnik. Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w Blazor WebAssembly aplikacji* działają w taki sam sposób: żądania nie wyzwalają żądań przeglądarki do zasobów hostowanych przez serwer w Internecie. Router obsługuje wewnętrznie żądania.

Żądanie kończy się niepowodzeniem, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki `www.contoso.com/About` . Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .

Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze na `index.html` stronie. Gdy `index.html` jest zwracany, Blazor router aplikacji przejmuje i reaguje na prawidłowy zasób.

Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym `web.config` plikiem aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Hostowane wdrożenie z ASP.NET Core

*Wdrożenie hostowane* Blazor WebAssembly umożliwia aplikacji przeglądarki z poziomu [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.

Aplikacja kliencka Blazor WebAssembly jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz z wszelkimi innymi statycznymi zasobami sieci Web aplikacji serwera. Te dwie aplikacje są wdrażane razem. Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core. W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu **Blazor WebAssembly aplikacji** ( `blazorwasm` szablon przy użyciu [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia) z **`Hosted`** wybraną opcją ( `-ho|--hosted` przy użyciu `dotnet new` polecenia).

Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji, zobacz <xref:host-and-deploy/index> .

Aby uzyskać informacje na temat wdrażania do Azure App Service, zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> .

## <a name="hosted-deployment-with-multiple-blazor-webassembly-apps"></a>Hostowane wdrożenie z wieloma Blazor WebAssembly aplikacjami

### <a name="app-configuration"></a>Konfiguracja aplikacji

Rozwiązania hostowane Blazor mogą obsługiwać wiele Blazor WebAssembly aplikacji.

> [!NOTE]
> W przykładzie w tej sekcji odwołuje się do korzystania z *rozwiązania* Visual Studio, ale użycie programu Visual Studio i rozwiązania Visual Studio nie jest wymagane, aby wiele aplikacji klienckich działało w Blazor WebAssembly scenariuszu hostowanych aplikacji. Jeśli nie używasz programu Visual Studio, zignoruj `{SOLUTION NAME}.sln` plik i wszystkie inne pliki utworzone dla programu Visual Studio.

W poniższym przykładzie:

* Początkowa (pierwsza) aplikacja kliencka jest domyślnym projektem klienta rozwiązania utworzonego na podstawie Blazor WebAssembly szablonu projektu. Pierwsza aplikacja kliencka jest dostępna w przeglądarce z adresu URL `/FirstApp` na porcie 5001 lub na hoście `firstapp.com` .
* Druga aplikacja kliencka jest dodawana do rozwiązania `SecondBlazorApp.Client` . Druga aplikacja kliencka jest dostępna w przeglądarce z adresu URL `/SecondApp` na porcie 5002 lub na hoście `secondapp.com` .

Użyj istniejącego rozwiązania hostowanego Blazor lub Utwórz nowe rozwiązanie z Blazor szablonu hostowanego projektu:

* W pliku projektu aplikacji klienckiej Dodaj `<StaticWebAssetBasePath>` Właściwość do `<PropertyGroup>` wartości, `FirstApp` Aby ustawić ścieżkę bazową dla statycznych zasobów projektu:

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* Dodaj drugą aplikację kliencką do rozwiązania:

  * Dodaj folder o nazwie `SecondClient` do folderu rozwiązania. Folder rozwiązania utworzony na podstawie szablonu projektu zawiera następujący plik rozwiązania i foldery po `SecondClient` dodaniu folderu:
  
    * `Client` system32\drivers\etc
    * `SecondClient` system32\drivers\etc
    * `Server` system32\drivers\etc
    * `Shared` system32\drivers\etc
    * `{SOLUTION NAME}.sln` rozszerzeniem
    
    Symbol zastępczy `{SOLUTION NAME}` jest nazwą rozwiązania.

  * Utwórz Blazor WebAssembly aplikację o nazwie `SecondBlazorApp.Client` w `SecondClient` folderze z Blazor WebAssembly szablonu projektu.

  * W `SecondBlazorApp.Client` pliku projektu aplikacji:

    * Dodaj `<StaticWebAssetBasePath>` Właściwość do `<PropertyGroup>` wartości z `SecondApp` :

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * Dodaj odwołanie do projektu do `Shared` projektu:

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      Symbol zastępczy `{SOLUTION NAME}` jest nazwą rozwiązania.

* W pliku projektu aplikacji serwera Utwórz odwołanie do projektu dla dodanej `SecondBlazorApp.Client` aplikacji klienckiej:

  ```xml
  <ItemGroup>
    <ProjectReference Include="..\Client\{SOLUTION NAME}.Client.csproj" />
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
    <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
  </ItemGroup>
  ```
  
  Symbol zastępczy `{SOLUTION NAME}` jest nazwą rozwiązania.

* W pliku aplikacji serwera `Properties/launchSettings.json` Skonfiguruj `applicationUrl` profil Kestrel ( `{SOLUTION NAME}.Server` ), aby uzyskać dostęp do aplikacji klienckich na portach 5001 i 5002:

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* W metodzie aplikacji serwera `Startup.Configure` ( `Startup.cs` ) Usuń następujące wiersze, które pojawią się po wywołaniu <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  Dodaj oprogramowanie pośredniczące, które mapuje żądania do aplikacji klienckich. Poniższy przykład konfiguruje oprogramowanie pośredniczące do uruchomienia, gdy:

  * Port żądania ma wartość 5001 dla oryginalnej aplikacji klienckiej lub 5002 dla dodanej aplikacji klienckiej.
  * Host żądania jest `firstapp.com` przeznaczony dla oryginalnej aplikacji klienckiej lub `secondapp.com` dla dodanej aplikacji klienckiej.

    > [!NOTE]
    > Przykład przedstawiony w tej sekcji wymaga dodatkowej konfiguracji dla:
    >
    > * Uzyskiwanie dostępu do aplikacji w przykładowych domenach hostów `firstapp.com` i `secondapp.com` .
    > * Certyfikaty dla aplikacji klienckich, aby włączyć zabezpieczenia protokołu TLS (HTTPS).
    >
    > Wymagana konfiguracja wykracza poza zakres tego artykułu i zależy od tego, w jaki sposób jest hostowane rozwiązanie. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [hosta i wdrażania](xref:host-and-deploy/index).

  Umieść następujący kod, w którym wiersze zostały wcześniej usunięte:

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* W kontrolerze prognozowania pogody aplikacji serwera `Controllers/WeatherForecastController.cs` Zastąp istniejącą trasę ( `[Route("[controller]")]` ) do `WeatherForecastController` następującej trasy:

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  Oprogramowanie pośredniczące dodane do metody aplikacji serwerowej `Startup.Configure` wcześniej modyfikuje przychodzące żądania do `/WeatherForecast` albo `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` w zależności od portu (5001/5002) lub domeny ( `firstapp.com` / `secondapp.com` ). Powyższe trasy kontrolera są wymagane w celu zwrócenia danych pogody z aplikacji serwerowej do aplikacji klienckich.

### <a name="static-assets-and-class-libraries"></a>Statyczne zasoby i biblioteki klas

W przypadku zasobów statycznych należy stosować następujące podejścia:

* Gdy element zawartości znajduje się w folderze aplikacji klienta `wwwroot` , podaj ścieżki zwykle:

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* Gdy element zawartości znajduje się w `wwwroot` folderze [ Razor biblioteki klas (RCL)](xref:blazor/components/class-libraries), odwołuje się do zasobu statycznego w aplikacji klienckiej zgodnie ze wskazówkami w [artykule RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

Składniki udostępniane aplikacji klienckiej przez bibliotekę klas są zwykle przywoływane. Jeśli wszystkie składniki wymagają plików stylów lub JavaScript, plik aplikacji klienta `wwwroot/index.html` musi zawierać poprawne linki do zasobów statycznych. Te podejścia przedstawiono w poniższych przykładach.

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

Dodaj następujący `Jeep` składnik do jednej z aplikacji klienckich. `Jeep`Składnik używa:

* Obraz z folderu aplikacji klienta `wwwroot` ( `jeep-cj.png` ).
* Obraz z [dodanego folderu Razor biblioteki składników](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` `jeep-yj.png` .
* Przykładowy składnik ( `Component1` ) jest tworzony automatycznie przez szablon projektu RCL, gdy `JeepImage` biblioteka zostanie dodana do rozwiązania.

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> Nie Publikuj obrazów pojazdów publicznie, chyba że są **one** właścicielami. W przeciwnym razie ryzyko naruszenia praw autorskich.

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

`jeep-yj.png`Obraz biblioteki można również dodać do `Component1` składnika biblioteki ( `Component1.razor` ):

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

Plik aplikacji klienta `wwwroot/index.html` żąda arkusza stylów biblioteki z następującym dodany `<link>` tag:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

Dodaj nawigację do `Jeep` składnika w składniku aplikacji klienckiej `NavMenu` ( `Shared/NavMenu.razor` ):

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

Aby uzyskać więcej informacji na temat RCLs, zobacz:

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>Wdrożenie autonomiczne

*Wdrożenie samodzielne* służy Blazor WebAssembly jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów. Każdy statyczny serwer plików jest w stanie obsłużyć Blazor aplikację.

Zasoby wdrażania autonomicznego są publikowane w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze.

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssembly Aplikacje można wdrażać w usłudze Azure App Services w systemie Windows, który jest hostem aplikacji w [usługach IIS](#iis).

Wdrażanie autonomicznej Blazor WebAssembly aplikacji do Azure App Service dla systemu Linux nie jest obecnie obsługiwane. Obraz serwera z systemem Linux do hostowania aplikacji nie jest obecnie dostępny. Trwa wykonywanie pracy, aby włączyć ten scenariusz.

### <a name="iis"></a>IIS

Program IIS jest obsługującym statycznego serwera plików dla Blazor aplikacji. Aby skonfigurować usługi IIS do hostowania Blazor , zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Opublikowane zasoby są tworzone w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze. Hostowanie zawartości `publish` folderu na serwerze sieci Web lub w usłudze hostingu.

#### <a name="webconfig"></a>web.config

Po Blazor opublikowaniu projektu `web.config` tworzony jest plik z następującą konfiguracją usług IIS:

* Typy MIME są ustawiane dla następujących rozszerzeń plików:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* Kompresja HTTP jest włączona dla następujących typów MIME:
  * `application/octet-stream`
  * `application/wasm`
* Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:
  * Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji ( `wwwroot/{PATH REQUESTED}` ).
  * Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych ( `wwwroot/index.html` ).
  
#### <a name="use-a-custom-webconfig"></a>Użyj niestandardowego web.config

Aby użyć pliku niestandardowego `web.config` , umieść `web.config` plik niestandardowy w folderze głównym folderu projektu. Skonfiguruj projekt do publikowania zasobów specyficznych dla usług IIS przy użyciu `PublishIISAssets` w pliku projektu aplikacji i Opublikuj projekt:

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a>Zainstaluj moduł ponownego zapisywania adresów URL

[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL. Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS). Moduł musi zostać pobrany z witryny sieci Web usług IIS. Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:

1. Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI. W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.
1. Skopiuj Instalatora na serwer. Uruchom instalatora. Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne. Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.

#### <a name="configure-the-website"></a>Skonfiguruj witrynę sieci Web

Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji. Folder zawiera:

* Plik, za `web.config` pomocą którego usługi IIS konfiguruje witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.
* Folder elementu zawartości statycznej aplikacji.

#### <a name="host-as-an-iis-sub-app"></a>Host jako podrzędną aplikację usług IIS

Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:

* Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.

  Usuń program obsługi w Blazor opublikowanym pliku aplikacji `web.config` , dodając `<handlers>` sekcję do pliku:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Wyłącz dziedziczenie sekcji głównej (nadrzędnej) aplikacji `<system.webServer>` przy użyciu `<location>` elementu z `inheritInChildApplications` ustawioną opcją `false` :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:blazor/host-and-deploy/index#app-base-path). Ustaw ścieżkę bazową aplikacji w `index.html` pliku aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.

#### <a name="brotli-and-gzip-compression"></a>Brotli i Kompresja gzip

*Ta sekcja dotyczy tylko aplikacji autonomicznych Blazor WebAssembly . aplikacje hostowane Blazor używają domyślnego pliku aplikacji ASP.NET Core `web.config` , a nie pliku połączonego z tą sekcją.*

Program IIS można skonfigurować `web.config` w taki sposób, aby obsługiwał skompresowane zasoby Brotli lub gzip Blazor dla aplikacji autonomicznych Blazor WebAssembly . Przykładowy plik konfiguracji można znaleźć w temacie [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .

Dodatkowa konfiguracja przykładowego `web.config` pliku może być wymagana w następujących scenariuszach:

* Specyfikacja aplikacji wywołuje jedną z następujących czynności:
  * Obsługa skompresowanych plików, które nie są konfigurowane przez przykładowy `web.config` plik.
  * Obsługa skompresowanych plików skonfigurowanych przez przykładowy `web.config` plik w nieskompresowanym formacie.
* Konfiguracja usług IIS na serwerze (na przykład `applicationHost.config` ) zapewnia ustawienia domyślne usług IIS na poziomie serwera. W zależności od konfiguracji na poziomie serwera aplikacja może wymagać innej konfiguracji usług IIS niż zawartość pliku przykładowego `web.config` .

#### <a name="troubleshooting"></a>Rozwiązywanie problemów

W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL. Gdy moduł nie jest zainstalowany, `web.config` nie można przeanalizować pliku przez usługi IIS. Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web na podstawie Blazor plików statycznych.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z wdrożeniami w usługach IIS, zobacz <xref:test/troubleshoot-azure-iis> .

### <a name="azure-storage"></a>Azure Storage

Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia Blazor hosting aplikacji bezserwerowych. Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.

Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:

* Ustaw **nazwę dokumentu indeksu** na `index.html` .
* Ustaw **ścieżkę do dokumentu błędu** `index.html` . Razor składniki i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB. Po otrzymaniu żądania dla jednego z tych zasobów, który Blazor powinien zostać obsłużony przez router, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**. `index.html`Obiekt BLOB jest zwracany, a Blazor router ładuje i przetwarza ścieżkę.

Jeśli pliki nie są ładowane w czasie wykonywania ze względu na nieodpowiednie typy MIME w `Content-Type` nagłówkach plików, wykonaj jedną z następujących czynności:

* Skonfiguruj narzędzia do ustawiania prawidłowych typów MIME ( `Content-Type` nagłówkami) podczas wdrażania plików.
* Zmień typy MIME ( `Content-Type` nagłówki) dla plików po wdrożeniu aplikacji.

  W Eksplorator usługi Storage (Azure Portal) dla każdego pliku:
  
  1. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości**.
  1. Ustaw wartość **ContentType** i wybierz przycisk **Zapisz** .

Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Następujący `nginx.conf` plik jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania `index.html` pliku za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

W przypadku ustawienia [limitu szybkości serii Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) w [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) programie Blazor WebAssembly aplikacje mogą wymagać dużej `burst` wartości parametru, aby pomieścić stosunkowo dużą liczbę żądań wykonywanych przez aplikację. Początkowo ustaw wartość na co najmniej 60:

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Zwiększ wartość, jeśli narzędzia deweloperskie przeglądarki lub ruch sieciowy wskazuje, że żądania odbierają kod stanu *niedostępny dla usługi 503* .

Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="apache"></a>Apache

Aby wdrożyć Blazor WebAssembly aplikację w programie CentOS 7 lub nowszym:

1. Utwórz plik konfiguracji Apache. Poniższy przykład to uproszczony plik konfiguracji ( `blazorapp.config` ):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.

1. Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).

1. Uruchom ponownie usługę Apache.

Aby uzyskać więcej informacji, zobacz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .

### <a name="github-pages"></a>Usługa GitHub Pages

Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj `wwwroot/404.html` plik ze skryptem, który obsługuje przekierowywanie żądania do `index.html` strony. Aby zapoznać się z przykładem, zobacz [ Blazor repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [Aktywna witryna](https://stevesandersonms.github.io/BlazorOnGitHubPages/))

W przypadku korzystania z witryny projektu zamiast witryny organizacji zaktualizuj `<base>` tag w temacie `wwwroot/index.html` . Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `/my-repository/` ). W [ Blazor repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)baza `href` jest aktualizowana przy publikowaniu przez [ `.github/workflows/main.yml` plik konfiguracji](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).

> [!NOTE]
> [ Blazor Repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) nie należy do ani nie jest obsługiwane przez platformę .NET Foundation ani firmę Microsoft.

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

[ Blazor WebAssembly aplikacje](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.

### <a name="content-root"></a>Katalog główny zawartości

`--contentroot`Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)). W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** . To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia z `dotnet run` .

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Baza ścieżki

`--pathbase`Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji). W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji. Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> W przeciwieństwie do ścieżki przekazanej do `href` `<base>` tagu, nie dodawaj końcowego ukośnika ( `/` ) podczas przekazywania `--pathbase` wartości argumentu. Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** . To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Adresy URL

`--urls`Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** . To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>Konfigurowanie elementu przycinającego

Blazor wykonuje przycinanie języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych. Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-trimmer>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>Konfigurowanie konsolidatora

Blazor wykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych. Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-linker>.

::: moniker-end

## <a name="custom-boot-resource-loading"></a>Ładowanie niestandardowego zasobu rozruchowego

Blazor WebAssemblyAplikacja może zostać zainicjowana przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego. Należy używać `loadBootResource` w następujących scenariuszach:

* Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane dotyczące strefy czasowej lub `dotnet.wasm` z sieci CDN.
* Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.
* Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.

`loadBootResource` Parametry znajdują się w poniższej tabeli.

| Parametr    | Opis |
| ------------ | ----------- |
| `type`       | Typ zasobu. Typy Permissable: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata` |
| `name`       | Nazwa zasobu. |
| `defaultUri` | Względny lub bezwzględny identyfikator URI zasobu. |
| `integrity`  | Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi. |

`loadBootResource` zwraca jedną z następujących wartości, aby zastąpić proces ładowania:

* Ciąg identyfikatora URI. W poniższym przykładzie ( `wwwroot/index.html` ) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/` :

  * `dotnet.*.js`
  * `dotnet.wasm`
  * Dane strefy czasowej

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.

  Poniższy przykład ( `wwwroot/index.html` ) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.

Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami. Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.

Wystarczy określić typy zachowań niestandardowych. Typy, które nie zostały określone do, `loadBootResource` są ładowane przez platformę na ich domyślne zachowania ładowania.

## <a name="change-the-filename-extension-of-dll-files"></a>Zmień rozszerzenie nazwy pliku DLL

Jeśli potrzebujesz zmienić rozszerzenia nazw plików opublikowanych w aplikacji `.dll` , postępuj zgodnie ze wskazówkami w tej sekcji.

Po opublikowaniu aplikacji Użyj skryptu powłoki lub potoku kompilacji DevOps, aby zmienić nazwy `.dll` plików, aby użyć innego rozszerzenia pliku. Docelowo `.dll` pliki w `wwwroot` katalogu opublikowanych danych wyjściowych aplikacji (na przykład `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).

W poniższych przykładach `.dll` nazwy plików są zmieniane, aby użyć `.bin` rozszerzenia pliku.

W systemie Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

W systemie Linux lub macOS:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Aby użyć innego rozszerzenia pliku niż `.bin` , Zastąp `.bin` w poprzednich poleceniach.

Aby rozwiązać skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki, należy zastosować jedną z następujących metod:

* Usuń skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki. Kompresja jest wyłączona w tym podejściu.
* Rekompresuj zaktualizowany `blazor.boot.json` plik.

Powyższe wskazówki stosuje się również w przypadku używania zasobów roboczych usługi. Usuń lub przekompresuj `wwwroot/service-worker-assets.js.br` i `wwwroot/service-worker-assets.js.gz` . W przeciwnym razie sprawdzanie integralności plików nie powiedzie się w przeglądarce.

Poniższy przykład systemu Windows używa skryptu programu PowerShell znajdującego się w katalogu głównym projektu.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

W pliku projektu skrypt jest uruchamiany po opublikowaniu aplikacji:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> Podczas zmiany nazwy i opóźnionego ładowania tych samych zestawów zobacz wskazówki w temacie <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .

## <a name="resolve-integrity-check-failures"></a>Rozwiązywanie błędów sprawdzania integralności

Po Blazor WebAssembly pobraniu plików startowych aplikacji nakazuje ona przeprowadzenie kontroli integralności odpowiedzi w przeglądarce. Używa informacji w pliku, `blazor.boot.json` Aby określić oczekiwane wartości skrótu SHA-256 dla `.dll` , `.wasm` i innych plików. Jest to korzystne z następujących powodów:

* Gwarantuje to, że nie ma ryzyka ładowania niespójnego zestawu plików, na przykład jeśli nowe wdrożenie jest stosowane do serwera sieci Web, podczas gdy użytkownik jest w trakcie procesu pobierania plików aplikacji. Niespójne pliki mogą prowadzić do niezdefiniowanego zachowania.
* Gwarantuje to, że przeglądarka użytkownika nigdy nie buforuje niespójnych lub nieprawidłowych odpowiedzi, co może uniemożliwić ich uruchomienie aplikacji nawet w przypadku ręcznego odświeżenia strony.
* Pozwala ona bezpiecznie na buforowanie odpowiedzi i nie sprawdza, czy zmiany po stronie serwera są zmieniane, dopóki nie zmienią się oczekiwanych skrótów SHA-256, więc kolejne obciążenia strony obejmują mniejszą liczbę żądań i są znacznie szybsze.

Jeśli serwer sieci Web zwróci odpowiedzi, które nie pasują do oczekiwanych skrótów SHA-256, zobaczysz błąd podobny do poniższego, pojawia się w konsoli dewelopera przeglądarki:

> Nie można znaleźć prawidłowego skrótu w atrybucie "Integrity" dla zasobu " https://myapp.example.com/\_framework/My BlazorApp.dll" z obliczoną integralnością SHA-256 "IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY =". Zasób został zablokowany.

W większości przypadków *nie* jest to problem ze sprawdzaniem integralności. Zamiast tego oznacza to, że występuje jakiś inny problem, a Kontrola integralności ostrzega o tym, że ten problem wystąpi.

### <a name="diagnosing-integrity-problems"></a>Diagnozowanie problemów z integralnością

Po skompilowaniu aplikacji wygenerowany `blazor.boot.json` manifest opisuje skróty SHA-256 zasobów rozruchowych (na przykład, `.dll` `.wasm` i innych plików) w momencie wygenerowania danych wyjściowych kompilacji. Sprawdzanie integralności przebiega tak długo, jak skróty SHA-256 `blazor.boot.json` są zgodne z plikami dostarczonymi do przeglądarki.

Najczęstsze przyczyny tego niepowodzenia to:

 * Odpowiedź serwera sieci Web jest błędem (na przykład *404 — nie można znaleźć* lub *500 — wewnętrzny błąd serwera*) zamiast pliku, którego żądała przeglądarka. Jest on raportowany przez przeglądarkę jako błąd sprawdzania integralności, a nie jako błąd odpowiedzi.
 * Coś zmieniło zawartość plików między kompilacją a dostarczeniem plików do przeglądarki. Może się tak zdarzyć:
   * Jeśli narzędzia kompilacji lub kompilacje ręcznie modyfikują dane wyjściowe kompilacji.
   * Jeśli jakiś aspekt procesu wdrażania zmodyfikował pliki. Na przykład jeśli korzystasz z mechanizmu wdrożenia opartego na usłudze git, weź pod uwagę, że git w sposób przezroczysty konwertuje końce wierszy w stylu systemu Windows do końca wiersza w stylu Unix, jeśli zatwierdzisz pliki w systemie Windows i wyewidencjonujesz je w systemie Linux. Zmiana końców wierszy plików zmienia wartości skrótów SHA-256. Aby uniknąć tego problemu, rozważ [użycie `.gitattributes` programu do traktowania artefaktów kompilacji jako `binary` plików](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).
   * Serwer sieci Web modyfikuje zawartość pliku w ramach ich obsługi. Na przykład niektóre sieci dystrybucji zawartości (sieci CDN) automatycznie próbują [zminifikować](xref:client-side/bundling-and-minification#minification) kod HTML, modyfikując go. Może być konieczne wyłączenie takich funkcji.

Aby zdiagnozować, które z nich mają zastosowanie w Twoim przypadku:

 1. Zwróć uwagę, który plik wyzwala błąd, odczytując komunikat o błędzie.
 1. Otwórz narzędzia deweloperskie w przeglądarce i sprawdź kartę *Sieć* . W razie potrzeby Załaduj ponownie stronę, aby zobaczyć listę żądań i odpowiedzi. Znajdź plik wyzwalający błąd na tej liście.
 1. Sprawdź kod stanu HTTP w odpowiedzi. Jeśli serwer zwróci coś innego niż *200-OK* (lub inny kod stanu 2xx), wystąpił problem po stronie serwera do zdiagnozowania. Na przykład kod stanu 403 oznacza problem z autoryzacją, natomiast kod stanu 500 oznacza, że serwer kończy się nieokreślonym sposobem. Zapoznaj się z dziennikami po stronie serwera, aby zdiagnozować i naprawić aplikację.
 1. Jeśli kod stanu to *200-OK* dla zasobu, zapoznaj się z zawartością odpowiedzi w narzędziach deweloperskich przeglądarki i sprawdź, czy zawartość jest zgodna z oczekiwanymi danymi. Typowym problemem jest na przykład Nieskonfigurowanie routingu w taki sposób, aby żądania zwracały `index.html` dane nawet dla innych plików. Upewnij się, że odpowiedzi na `.wasm` żądania to pliki binarne webassembly, a odpowiedzi na `.dll` żądania to pliki binarne zestawu platformy .NET. W przeciwnym razie masz problem z Routing po stronie serwera do zdiagnozowania.
 1. Sprawdź poprawność opublikowanych i wdrożonych danych wyjściowych aplikacji za pomocą [skryptu Rozwiązywanie problemów ze integralnością programu PowerShell](#troubleshoot-integrity-powershell-script).

Jeśli potwierdzasz, że serwer zwraca poprawne dane plausibly, konieczne może być zmodyfikowanie zawartości między kompilacją i dostarczeniem pliku. Aby zbadać to:

 * Zapoznaj się z mechanizmem kompilowania łańcucha narzędzi i wdrażania na wypadek modyfikacji plików po skompilowaniu plików. Przykładem takiej sytuacji jest to, że program git przekształca końce wierszy plików zgodnie z wcześniejszym opisem.
 * Zapoznaj się z konfiguracją serwer sieci Web lub sieć CDN w przypadku, gdy są skonfigurowane do dynamicznego modyfikowania odpowiedzi (na przykład próba zminifikować HTML). Serwer sieci Web może zaimplementować kompresję HTTP (na przykład zwracając `content-encoding: br` lub `content-encoding: gzip` ), ponieważ nie ma to wpływu na wynik po dekompresji. *Nie* jest jednak konieczne, aby serwer sieci Web modyfikował nieskompresowane dane.

### <a name="troubleshoot-integrity-powershell-script"></a>Rozwiązywanie problemów z integralnością skryptu programu PowerShell

Użyj [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) skryptu programu PowerShell, aby zweryfikować opublikowaną i wdrożoną Blazor aplikację. Skrypt jest dostarczany jako punkt wyjścia, gdy aplikacja ma problemy ze integralnością, których Blazor nie można zidentyfikować w strukturze. Dla Twoich aplikacji może być wymagane dostosowanie skryptu.

Skrypt sprawdza pliki w `publish` folderze i pobrane ze wdrożonej aplikacji w celu wykrywania problemów w różnych manifestach zawierających skróty integralności. Te testy powinny wykrywać najczęstsze problemy:

* Plik został zmodyfikowany w opublikowanym danych wyjściowych bez jego realizacji.
* Aplikacja nie została poprawnie wdrożona w miejscu docelowym wdrożenia lub coś uległo zmianie w środowisku docelowym wdrożenia.
* Istnieją różnice między wdrożoną aplikacją a danymi wyjściowymi publikowania aplikacji.

Wywołaj skrypt za pomocą następującego polecenia w powłoce poleceń programu PowerShell:

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

Symbole zastępcze

* `{BASE URL}`: Adres URL wdrożonej aplikacji.
* `{PUBLISH OUTPUT FOLDER}`: Ścieżka do `publish` folderu lub lokalizacji aplikacji, w której aplikacja została opublikowana do wdrożenia.

> [!NOTE]
> Aby sklonować `dotnet/AspNetCore.Docs` repozytorium GitHub do systemu, który używa skanera antywirusowego [BitDefender](https://www.bitdefender.com) , Dodaj wyjątek do BitDefender dla `integrity.ps1` skryptu. Dodaj wyjątek do BitDefender przed klonem repozytorium, aby uniknąć posiadania skryptu poddanego kwarantannie przez skaner wirusów. Poniższy przykład jest typową ścieżką do skryptu dla sklonowanego repozytorium w systemie Windows. Dostosuj ścieżkę w razie konieczności. Symbol zastępczy `{USER}` jest segmentem ścieżki użytkownika.
>
> ```
> C:\Users\{USER}\Documents\GitHub\AspNetCore.Docs\aspnetcore\blazor\host-and-deploy\webassembly\_samples\integrity.ps1
> ```

### <a name="disable-integrity-checking-for-non-pwa-apps"></a>Wyłącz sprawdzanie integralności dla aplikacji innych niż PWA

W większości przypadków nie należy wyłączać sprawdzania integralności. Wyłączenie sprawdzania integralności nie rozwiązuje podstawowego problemu, który spowodował nieoczekiwane odpowiedzi i spowoduje utratę wymienionych wcześniej korzyści.

Mogą jednak wystąpić sytuacje, w których serwer sieci Web nie może być oparty na zwracaniu spójnych odpowiedzi i nie ma możliwości wyboru, ale w celu wyłączenia kontroli integralności. Aby wyłączyć sprawdzanie integralności, Dodaj następujący element do grupy właściwości w Blazor WebAssembly `.csproj` pliku projektu:

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

`BlazorCacheBootResources` wyłącza również Blazor domyślne zachowanie buforowania `.dll` , `.wasm` i innych plików na podstawie skrótów SHA-256, ponieważ właściwość wskazuje, że nie można opierać się na wartości skrótów SHA-256 w celu poprawnego działania. Nawet w przypadku tego ustawienia Normalna pamięć podręczna w przeglądarce może nadal buforować te pliki, ale bez względu na to, czy ma to miejsce, zależy od konfiguracji serwera sieci Web i `cache-control` nagłówków, które obsługuje.

> [!NOTE]
> `BlazorCacheBootResources`Właściwość nie wyłącza sprawdzania integralności w przypadku [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app). Wskazówki dotyczące PWAs można znaleźć w sekcji [Wyłączanie sprawdzania integralności dla PWAs](#disable-integrity-checking-for-pwas) .

### <a name="disable-integrity-checking-for-pwas"></a>Wyłącz sprawdzanie integralności dla PWAs

Blazorszablon aplikacji sieci Web progresywnej (PWA) zawiera sugerowany `service-worker.published.js` plik, który jest odpowiedzialny za pobieranie i przechowywanie plików aplikacji do użytku w trybie offline. Jest to oddzielny proces od normalnego mechanizmu uruchamiania aplikacji i ma własną logikę sprawdzania integralności.

W `service-worker.published.js` pliku znajduje się następujący wiersz:

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

Aby wyłączyć sprawdzanie integralności, Usuń `integrity` parametr, zmieniając wiersz na następujący:

```javascript
.map(asset => new Request(asset.url));
```

Po ponownym uruchomieniu kontroli integralności oznacza to, że utracisz gwarancje bezpieczeństwa oferowane przez sprawdzanie integralności. Na przykład istnieje ryzyko, że jeśli przeglądarka użytkownika będzie buforować aplikację w tym samym czasie, gdy zostanie wdrożona nowa wersja, może to spowodować przełączenie niektórych plików ze starego wdrożenia, a niektóre z nowego wdrożenia. W takim przypadku aplikacja zostanie zablokowana w stanie przerwania, dopóki nie zostanie wdrożona dodatkowa aktualizacja.

---
title: Oprogramowanie pośredniczące do przepisywania adresów URL w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o przepisywaniu i przekierowywaniu adresów URL za pomocą oprogramowania pośredniczącego do przepisywania adresów URL w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
uid: fundamentals/url-rewriting
ms.openlocfilehash: 7d63cf381f1d8a19ed4fb789348e36f94304ad63
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666468"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>Oprogramowanie pośredniczące do przepisywania adresów URL w ASP.NET Core

Przez [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range=">= aspnetcore-3.0"

W tym dokumencie wprowadzono przepisywanie adresów URL z instrukcjami dotyczącymi używania oprogramowania pośredniczącego do przepisywania adresów URL w aplikacjach ASP.NET Core.

Przepisywanie adresów URL to czynność modyfikowania adresów URL żądań na podstawie co najmniej jednej wstępnie zdefiniowanej reguły. Przepisywanie adresów URL tworzy abstrakcję między lokalizacjami zasobów a ich adresami, dzięki czemu lokalizacje i adresy nie są ściśle powiązane. Przepisywanie adresów URL jest cenne w kilku scenariuszach, aby:

* Przenieś lub zastąp zasoby serwera tymczasowo lub na stałe i zachowaj stabilne lokalizatory dla tych zasobów.
* Przetwarzanie żądań podzielonych na różne aplikacje lub w różnych obszarach jednej aplikacji.
* Usuwanie, dodawanie lub reorganizowanie segmentów adresów URL w żądaniach przychodzących.
* Optymalizacja publicznych adresów URL pod kątem optymalizacji pod kątem wyszukiwarek (SEO).
* Zezwalaj na używanie przyjaznych publicznych adresów URL, aby ułatwić odwiedzającym przewidywanie zwracanej zawartości przez żądanie zasobu.
* Przekieruj niezabezpieczone żądania do bezpiecznych punktów końcowych.
* Zapobiegaj hotlinkingowi, gdy witryna zewnętrzna używa hostowanego zasobu statycznego w innej witrynie, łącząc go z własną zawartością.

> [!NOTE]
> Przepisywanie adresów URL może zmniejszyć wydajność aplikacji. Tam, gdzie jest to możliwe, ogranicz liczbę i złożoność przepisów.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Przekierowanie adresu URL i przepisanie adresu URL

Różnica w sformułowaniu między *przekierowaniem adresu URL* a *przepisaniem adresu URL jest subtelna,* ale ma istotne implikacje dla dostarczania zasobów klientom. ASP.NET Core url przepisywanie oprogramowania pośredniczącego jest w stanie zasypać potrzebę obu.

*Przekierowanie adresu URL* obejmuje operację po stronie klienta, gdzie klient jest poinstruowany, aby uzyskać dostęp do zasobu pod innym adresem niż klient pierwotnie żądany. Wymaga to podróży w obie strony do serwera. Adres URL przekierowania zwrócony do klienta pojawia się na pasku adresu przeglądarki, gdy klient wysunie nowe żądanie dla zasobu.

Jeśli `/resource` zostanie *przekierowany* do `/different-resource`programu, serwer odpowiada, że `/different-resource` klient powinien uzyskać zasób z kodem stanu wskazującym, że przekierowanie jest tymczasowe lub stałe.

![Punkt końcowy usługi WebAPI został tymczasowo zmieniony z wersji 1 (wersja 1) na wersję 2 (wersja 2) na serwerze. Klient sprawia, że żądanie do usługi w wersji 1 ścieżka /v1/api. Serwer wysyła z powrotem odpowiedź 302 (Found) z nową, tymczasową ścieżką dla usługi w wersji 2 /v2/api. Klient wysunie drugie żądanie do usługi pod adresem URL przekierowania. Serwer odpowiada kodem stanu 200 (OK).](url-rewriting/_static/url_redirect.png)

Podczas przekierowywania żądań do innego adresu URL, należy wskazać, czy przekierowanie jest stałe czy tymczasowe, określając kod stanu z odpowiedzią:

* *301 - Przeniesiony kod* stanu trwale jest używany, gdy zasób ma nowy, stały adres URL i chcesz poinstruować klienta, że wszystkie przyszłe żądania dla zasobu powinny używać nowego adresu URL. *Klient może buforować i ponownie używać odpowiedzi po odebraniu kodu stanu 301.*

* Kod stanu *302 - Znaleziono* jest używany, gdy przekierowanie jest tymczasowe lub zazwyczaj może ulec zmianie. Kod stanu 302 oznacza, że klient nie przechowuje adresu URL i nie używa go w przyszłości.

Aby uzyskać więcej informacji na temat kodów stanu, zobacz [RFC 2616: Definicje kodu stanu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

*Przepisanie adresu URL* jest operacją po stronie serwera, która zapewnia zasób z adresu innego zasobu niż żądany klient. Przepisywanie adresu URL nie wymaga podróży w obie strony do serwera. Przepisany adres URL nie jest zwracany do klienta i nie jest wyświetlany na pasku adresu przeglądarki.

Jeśli `/resource` jest *przepisany* do `/different-resource`, serwer *wewnętrznie* pobiera i `/different-resource`zwraca zasób w .

Mimo że klient może być w stanie pobrać zasób przy przepisanych adresów URL, klient nie jest informowany, że zasób istnieje w przepisany adres URL, gdy sprawia, że jego żądanie i odbiera odpowiedź.

![Punkt końcowy usługi WebAPI został zmieniony z wersji 1 (wersja 1) na wersję 2 (wersja 2) na serwerze. Klient sprawia, że żądanie do usługi w wersji 1 ścieżka /v1/api. Adres URL żądania jest przepisany, aby uzyskać dostęp do usługi w ścieżce w wersji 2 /v2/api. Usługa odpowiada klientowi za pomocą kodu stanu 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Przykładowa aplikacja do przepisywania adresów URL

Za pomocą [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)można zapoznać się z funkcjami oprogramowania pośredniczącego przepisywania adresów URL. Aplikacja stosuje reguły przekierowania i przepisywania i pokazuje przekierowany lub przepisany adres URL dla kilku scenariuszy.

## <a name="when-to-use-url-rewriting-middleware"></a>Kiedy używać oprogramowania pośredniczącego do przepisywania adresów URL

Oprogramowanie pośredniczące do przepisywania adresów URL używa się następujących metod:

* [Moduł przepisywania adresów URL z usługami IIS w systemie Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Moduł mod_rewrite Apache na serwerze Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Przepisywanie adresów URL w Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Ponadto należy użyć oprogramowania pośredniczącego, gdy aplikacja jest hostowana na [serwerze HTTP.sys](xref:fundamentals/servers/httpsys) (dawniej nazywanym WebListener).

Główne powody, dla których warto używać opartych na serwerze technologii przepisywania adresów URL w usługach IIS, Apache i Nginx to:

* Oprogramowanie pośredniczące nie obsługuje pełnych funkcji tych modułów.

  Niektóre funkcje modułów serwera nie działają z ASP.NET projektów podstawowych, `IsFile` takich `IsDirectory` jak i ograniczenia modułu IIS Rewrite. W tych scenariuszach zamiast tego należy użyć oprogramowania pośredniczącego.
* Wydajność oprogramowania pośredniczącego prawdopodobnie nie pasuje do wydajności modułów.

  Analiza porównawcza jest jedynym sposobem, aby wiedzieć na pewno, które podejście obniża wydajność najbardziej lub jeśli obniżona wydajność jest znikoma.

## <a name="package"></a>Pakiet

Oprogramowanie pośredniczące do przepisywania adresów URL jest dostarczane przez pakiet [Microsoft.AspNetCore.Rewrite,](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) który jest niejawnie zawarty w aplikacjach ASP.NET Core.

## <a name="extension-and-options"></a>Rozszerzenie i opcje

Uspokaj reguły przepisywania i przekierowywania adresów URL, tworząc wystąpienie klasy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) z metodami rozszerzenia dla każdej reguły przepisywania. Łańcuch wielu reguł w kolejności, w której chcesz je przetworzyć. Są `RewriteOptions` przekazywane do adresu URL przepisywanie oprogramowania pośredniczącego, <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>jak jest dodawany do potoku żądania z:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Przekieruj inne niż www na www

Trzy opcje pozwalają aplikacji przekierować`www` `www`nie-żądania do:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; Trwale przekieruj żądanie do `www` poddomeny, jeśli żądanie nie jest-`www`. Przekierowuje z kodem stanu [Status308PermanentRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect)

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Przekieruj `www` żądanie do poddomeny,`www`jeśli żądanie przychodzące nie jest - . Przekierowuje z kodem stanu [Status307TemporaryRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) Przeciążenie umożliwia podanie kodu stanu odpowiedzi. Użyj pola <xref:Microsoft.AspNetCore.Http.StatusCodes> klasy dla przypisania kodu stanu.

### <a name="url-redirect"></a>Przekierowywanie adresów URL

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> do przekierowywania żądań. Pierwszy parametr zawiera wyrażenie regularne do dopasowywania na ścieżce przychodzącego adresu URL. Drugi parametr to ciąg zastępczy. Trzeci parametr, jeśli jest obecny, określa kod stanu. Jeśli nie określisz kodu stanu, domyślnie kod stanu *302 - Znaleziono*, co oznacza, że zasób jest tymczasowo przeniesiony lub zastąpiony.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

W przeglądarce z włączonymi narzędziami deweloperskimi złożyć `/redirect-rule/1234/5678`żądanie do przykładowej aplikacji ze ścieżką . Wyrażenie regularne jest zgodne `redirect-rule/(.*)`ze ścieżką żądania, `/redirected/1234/5678`a ścieżka jest zastępowana . Adres URL przekierowania jest wysyłany z powrotem do klienta z kodem stanu *302 — znaleziono.* Przeglądarka tworzy nowe żądanie pod adresem URL przekierowania, który pojawia się na pasku adresu przeglądarki. Ponieważ żadne reguły w przykładowej aplikacji nie są zgodne z adresem URL przekierowania:

* Drugie żądanie otrzymuje *odpowiedź 200 — OK* z aplikacji.
* Treść odpowiedzi pokazuje adres URL przekierowania.

Po *przekierowaniu*adresu URL adres URL na serwer odbywa się podróż w obie strony.

> [!WARNING]
> Należy zachować ostrożność podczas ustanawiania reguł przekierowania. Reguły przekierowania są oceniane przy każdym żądaniu do aplikacji, w tym po przekierowaniu. Łatwo jest przypadkowo utworzyć *pętlę nieskończonych przekierowań*.

Oryginalna prośba:`/redirect-rule/1234/5678`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi](url-rewriting/_static/add_redirect.png)

Część wyrażenia zawarta w nawiasach jest nazywana *grupą przechwytywania*. Kropka (`.`) wyrażenia oznacza *dopasowanie dowolnego znaku*. Gwiazdka (`*`) oznacza *dopasowanie poprzedniego znaku zero lub więcej razy*. W związku z tym dwa ostatnie segmenty ścieżki adresu `1234/5678` `(.*)`URL, są przechwytywane przez grupę przechwytywania . Każda wartość podana w `redirect-rule/` adresie URL żądania po jest przechwytywany przez tę pojedynczą grupę przechwytywania.

W ciągu zastępczym przechwycone grupy są wtryskiwane`$`do ciągu ze znakiem dolara ( ), po którym następuje numer sekwencyjny przechwytywania. Pierwsza wartość grupy przechwytywania `$1`jest uzyskiwana za pomocą , drugi z `$2`, i są one kontynuowane w kolejności dla grup przechwytywania w wyrażeniach. Istnieje tylko jedna przechwycona grupa w resecie przekierowania regex w przykładowej aplikacji, więc `$1`jest tylko jedna wstrzyknięta grupa w ciągu zastępczym, który jest . Po zastosowaniu reguły adres `/redirected/1234/5678`URL staje się .

### <a name="url-redirect-to-a-secure-endpoint"></a>Przekierowanie adresu URL do bezpiecznego punktu końcowego

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> do przekierowywania żądań HTTP do tego samego hosta i ścieżki przy użyciu protokołu HTTPS. Jeśli kod stanu nie jest podany, oprogramowanie pośredniczące domyślnie *302 - Znaleziono*. Jeśli port nie jest podany:

* Oprogramowanie pośredniczące `null`domyślnie ma wartość .
* Schemat zmienia `https` się na (protokół HTTPS), a klient uzyskuje dostęp do zasobu na porcie 443.

W poniższym przykładzie pokazano, jak ustawić kod stanu na *301 — przeniesiony na stałe* i zmienić port na 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> do przekierowywania niezabezpieczonych żądań do tego samego hosta i ścieżki z bezpiecznym protokołem HTTPS na porcie 443. Oprogramowanie pośredniczące ustawia kod stanu na *301 - Przeniesiony na stałe*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Podczas przekierowywania do bezpiecznego punktu końcowego bez wymogu dodatkowych reguł przekierowania, zaleca się użycie oprogramowania pośredniczącego przekierowania HTTPS. Aby uzyskać więcej informacji, zobacz temat [Wymuszanie protokołu HTTPS.](xref:security/enforcing-ssl#require-https)

Przykładowa aplikacja jest w stanie `AddRedirectToHttps` wykazać, jak używać lub `AddRedirectToHttpsPermanent`. Dodaj metodę rozszerzenia `RewriteOptions`do pliku . Złożyć niezabezpieczone żądanie do aplikacji w dowolnym adresie URL. Odrzuć ostrzeżenie zabezpieczeń przeglądarki, że certyfikat z podpisem własnym jest niezaufany lub utwórz wyjątek, aby ufać certyfikatowi.

Oryginalne żądanie `AddRedirectToHttps(301, 5001)`za pomocą:`http://localhost:5000/secure`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi](url-rewriting/_static/add_redirect_to_https.png)

Oryginalne żądanie `AddRedirectToHttpsPermanent`za pomocą:`http://localhost:5000/secure`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Regenerowanie adresów URL

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> do tworzenia reguły przepisywania adresów URL. Pierwszy parametr zawiera wyrażenie regularne do dopasowywania na przychodzącej ścieżce adresu URL. Drugi parametr to ciąg zastępczy. Trzeci parametr `skipRemainingRules: {true|false}`, wskazuje oprogramowaniu pośredniczącemu, czy pominąć dodatkowe reguły ponownego zapisu, jeśli stosowana jest bieżąca reguła.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Oryginalna prośba:`/rewrite-rule/1234/5678`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądanie i odpowiedź](url-rewriting/_static/add_rewrite.png)

Karat (`^`) na początku wyrażenia oznacza, że dopasowywanie rozpoczyna się na początku ścieżki adresu URL.

We wcześniejszym przykładzie z `redirect-rule/(.*)`regułą przekierowania ,`^`nie ma karatów ( ) na początku wyrażenia regularnego. W związku z tym `redirect-rule/` wszystkie znaki mogą poprzedzać w ścieżce dla pomyślnego dopasowania.

| Ścieżka                               | Dopasowanie |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Tak   |
| `/my-cool-redirect-rule/1234/5678` | Tak   |
| `/anotherredirect-rule/1234/5678`  | Tak   |

Reguła przepisywania, `^rewrite-rule/(\d+)/(\d+)`, dopasowuje ścieżki `rewrite-rule/`tylko wtedy, gdy zaczynają się od . W poniższej tabeli należy zwrócić uwagę na różnicę w dopasowywaniu.

| Ścieżka                              | Dopasowanie |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Tak   |
| `/my-cool-rewrite-rule/1234/5678` | Nie    |
| `/anotherrewrite-rule/1234/5678`  | Nie    |

Po `^rewrite-rule/` części wyrażenia istnieją dwie grupy przechwytywania, `(\d+)/(\d+)`. Oznacza `\d` to *dopasowanie cyfry (liczby)*. Znak plus`+`( ) oznacza *dopasowanie jednego lub więcej z poprzedniego znaku*. W związku z tym adres URL musi zawierać liczbę, po której następuje ukośnik do przodu, po którym następuje inny numer. Te grupy przechwytywania są wstrzykiwane `$1` do `$2`przepisanych adresów URL jako i . Ciąg zastępowania reguły ponownego przepisywania umieszcza przechwycone grupy w ciągu zapytania. Żądana ścieżka `/rewrite-rule/1234/5678` jest przepisana w celu `/rewritten?var1=1234&var2=5678`uzyskania zasobu w pliku . Jeśli ciąg zapytania jest obecny na oryginalnym żądaniu, jest zachowywany, gdy adres URL jest przepisany.

Nie ma żadnej podróży w obie strony do serwera, aby uzyskać zasób. Jeśli zasób istnieje, jest pobierany i zwracany do klienta z kodem stanu *200 - OK.* Ponieważ klient nie jest przekierowywał, adres URL na pasku adresu przeglądarki nie zmienia się. Klienci nie mogą wykryć, że na serwerze wystąpiła operacja ponownego zmiany adresu URL.

> [!NOTE]
> Używaj, `skipRemainingRules: true` gdy jest to możliwe, ponieważ reguły dopasowywania są kosztowne pod względem obliczeniowym i wydłuża czas reakcji aplikacji. Aby uzyskać najszybszą odpowiedź aplikacji:
>
> * Kolejność przepisywania reguł z najczęściej dopasowywane reguły do reguły najrzadziej dopasowane.
> * Pomiń przetwarzanie pozostałych reguł, gdy wystąpi dopasowanie i nie jest wymagane żadne dodatkowe przetwarzanie reguł.

### <a name="apache-mod_rewrite"></a>mod_rewrite Apache

Zastosuj apache mod_rewrite reguły <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>z . Upewnij się, że plik reguł jest wdrożony z aplikacją. Aby uzyskać więcej informacji i przykłady reguł mod_rewrite, zobacz [mod_rewrite Apache](https://httpd.apache.org/docs/2.4/rewrite/).

A <xref:System.IO.StreamReader> służy do odczytywania reguł z pliku reguł *ApacheModRewrite.txt:*

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Przykładowa aplikacja przekierowuje żądania z `/apache-mod-rules-redirect/(.\*)` . `/redirected?id=$1` Kod stanu odpowiedzi to *302 - Znaleziono*.

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

Oryginalna prośba:`/apache-mod-rules-redirect/1234`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi](url-rewriting/_static/add_apache_mod_redirect.png)

Oprogramowanie pośredniczące obsługuje następujące zmienne serwera mod_rewrite Apache:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* Protokół IPV6
* Query_string
* REMOTE_ADDR
* REMOTE_PORT
* Request_filename
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* CZAS
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Reguły modułu przepisywania adresów URL w uirozmywanych adresach URL w uirozie.

Aby użyć tego samego zestawu reguł, który ma zastosowanie do <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>modułu przepisywania adresu URL iIS, należy użyć programu . Upewnij się, że plik reguł jest wdrożony z aplikacją. Nie kieruj oprogramowania pośredniczącego do używania pliku *web.config* aplikacji podczas uruchamiania w systemie Windows Server IIS. Dzięki usługom IIS te reguły powinny być przechowywane poza *plikiem web.config* aplikacji, aby uniknąć konfliktów z modułem Przepisywanie usług IIS. Aby uzyskać więcej informacji i przykłady reguł modułu zapisywania adresów URL usług IIS, zobacz [Korzystanie z modułu 2.0 do przepisywania adresów URL](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) i odwołanie do konfiguracji [modułu ponownego zapisu adresu URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

A <xref:System.IO.StreamReader> służy do odczytywania reguł z pliku reguł *IISUrlRewrite.xml:*

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Przykładowa aplikacja przepisuje `/iis-rules-rewrite/(.*)` `/rewritten?id=$1`żądania z do . Odpowiedź jest wysyłana do klienta z kodem stanu *200 - OK.*

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

Oryginalna prośba:`/iis-rules-rewrite/1234`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądanie i odpowiedź](url-rewriting/_static/add_iis_url_rewrite.png)

Jeśli masz aktywny moduł przepisywania usług IIS z skonfigurowaną regułą na poziomie serwera, która miałaby wpływ na aplikację w niepożądany sposób, możesz wyłączyć moduł przepisywania usług IIS dla aplikacji. Aby uzyskać więcej informacji, zobacz [Wyłączanie modułów IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nieobsługiwały się funkcje

Oprogramowanie pośredniczące wydane z ASP.NET Core 2.x nie obsługuje następujących funkcji modułu zapisywania adresów URL IIS:

* Reguły ruchu wychodzącego
* Niestandardowe zmienne serwera
* Symbole wieloznaczne
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Obsługiwane zmienne serwera

Oprogramowanie pośredniczące obsługuje następujące zmienne serwera serwera iis url rewrite module:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* Query_string
* REMOTE_ADDR
* REMOTE_PORT
* Request_filename
* REQUEST_URI

> [!NOTE]
> Można również uzyskać <xref:Microsoft.Extensions.FileProviders.IFileProvider> za <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>pośrednictwem . Takie podejście może zapewnić większą elastyczność lokalizacji plików reguł ponownego zapisu. Upewnij się, że pliki reguł ponownego zapisu są wdrażane na serwerze w ścieżce, którą podasz.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Reguła oparta na metodzie

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> do implementowania własnej logiki reguły w metodzie. `Add`udostępnia <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, który udostępnia <xref:Microsoft.AspNetCore.Http.HttpContext> do wykorzystania w metodzie. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) określa sposób obsługi dodatkowego przetwarzania potoku. Ustaw wartość jednego z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> pól opisanych w poniższej tabeli.

| `RewriteContext.Result`              | Akcja                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules`(domyślnie) | Kontynuuj stosowanie reguł.                                         |
| `RuleResult.EndResponse`             | Przestań stosować reguły i wyślij odpowiedź.                       |
| `RuleResult.SkipRemainingRules`      | Przestań stosować reguły i wyślij kontekst do następnego oprogramowania pośredniczącego. |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Przykładowa aplikacja pokazuje metodę, która przekierowuje żądania ścieżek, które kończą się na *.xml*. Jeśli zostanie złożony `/file.xml`wniosek, żądanie zostanie `/xmlfiles/file.xml`przekierowane do . Kod stanu jest ustawiony na *301 - Przeniesiony na stałe*. Gdy przeglądarka wysunie nowe żądanie dla */xmlfiles/file.xml*, Oprogramowanie pośredniczące plików statycznych służy do klienta z folderu *wwwroot/xmlfiles.* W przypadku przekierowania jawnie ustaw kod stanu odpowiedzi. W przeciwnym razie zwracany jest kod stanu *200 - OK,* a przekierowanie nie występuje na kliencie.

*RewriteRules.cs:*

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Takie podejście można również przepisać żądania. Przykładowa aplikacja demonstruje przepisywanie ścieżki dla dowolnego żądania pliku tekstowego do obsługi pliku tekstowego *file.txt* z folderu *wwwroot.* Oprogramowanie pośredniczące plików statycznych obsługuje plik na podstawie zaktualizowanej ścieżki żądania:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs:*

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Reguła oparta na zasadach irule

Użyj <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> logiki reguły w klasie, <xref:Microsoft.AspNetCore.Rewrite.IRule> która implementuje interfejs. `IRule`zapewnia większą elastyczność w stosowania metody opartej na metodzie. Klasa implementacji może zawierać konstruktora, który <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> umożliwia przekazywanie parametrów dla metody.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Wartości parametrów w przykładowej aplikacji `extension` dla `newPath` i są sprawdzane, aby spełnić kilka warunków. Musi `extension` zawierać wartość, a wartość musi być *.png*, *.jpg*lub *gif*. Jeśli `newPath` nie jest prawidłowy, jest wyrzucany. <xref:System.ArgumentException> Jeśli zostanie złożony wniosek o *image.png,* żądanie `/png-images/image.png`zostanie przekierowane do . Jeśli zostanie złożony wniosek o *image.jpg*, `/jpg-images/image.jpg`żądanie zostanie przekierowane do . Kod stanu jest ustawiony na *301 -* `context.Result` Przeniesiony na stałe , a jest ustawiony, aby zatrzymać reguły przetwarzania i wysłać odpowiedź.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Oryginalna prośba:`/image.png`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi dla pliku image.png](url-rewriting/_static/add_redirect_png_requests.png)

Oryginalna prośba:`/image.jpg`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi dla pliku image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Przykłady regex

| Cel | & ciągu regex<br>Przykład dopasowania | & ciągu zastępczego<br>Przykład wyjścia |
| ---- | ------------------------------- | -------------------------------------- |
| Przepisanie ścieżki do sznurka zapytania | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Ukośnik na końcu taśmy | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Wymuszanie końcowego ukośnika | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Unikaj przepisywania określonych żądań | `^(.*)(?<!\.axd)$` lub `^(?!.*\.axd$)(.*)$`<br>Tak:`/resource.htm`<br>№:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Zmienianie rozmieszczenia segmentów adresów URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Zastępowanie segmentu adresu URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym dokumencie wprowadzono przepisywanie adresów URL z instrukcjami dotyczącymi używania oprogramowania pośredniczącego do przepisywania adresów URL w aplikacjach ASP.NET Core.

Przepisywanie adresów URL to czynność modyfikowania adresów URL żądań na podstawie co najmniej jednej wstępnie zdefiniowanej reguły. Przepisywanie adresów URL tworzy abstrakcję między lokalizacjami zasobów a ich adresami, dzięki czemu lokalizacje i adresy nie są ściśle powiązane. Przepisywanie adresów URL jest cenne w kilku scenariuszach, aby:

* Przenieś lub zastąp zasoby serwera tymczasowo lub na stałe i zachowaj stabilne lokalizatory dla tych zasobów.
* Przetwarzanie żądań podzielonych na różne aplikacje lub w różnych obszarach jednej aplikacji.
* Usuwanie, dodawanie lub reorganizowanie segmentów adresów URL w żądaniach przychodzących.
* Optymalizacja publicznych adresów URL pod kątem optymalizacji pod kątem wyszukiwarek (SEO).
* Zezwalaj na używanie przyjaznych publicznych adresów URL, aby ułatwić odwiedzającym przewidywanie zwracanej zawartości przez żądanie zasobu.
* Przekieruj niezabezpieczone żądania do bezpiecznych punktów końcowych.
* Zapobiegaj hotlinkingowi, gdy witryna zewnętrzna używa hostowanego zasobu statycznego w innej witrynie, łącząc go z własną zawartością.

> [!NOTE]
> Przepisywanie adresów URL może zmniejszyć wydajność aplikacji. Tam, gdzie jest to możliwe, ogranicz liczbę i złożoność przepisów.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Przekierowanie adresu URL i przepisanie adresu URL

Różnica w sformułowaniu między *przekierowaniem adresu URL* a *przepisaniem adresu URL jest subtelna,* ale ma istotne implikacje dla dostarczania zasobów klientom. ASP.NET Core url przepisywanie oprogramowania pośredniczącego jest w stanie zasypać potrzebę obu.

*Przekierowanie adresu URL* obejmuje operację po stronie klienta, gdzie klient jest poinstruowany, aby uzyskać dostęp do zasobu pod innym adresem niż klient pierwotnie żądany. Wymaga to podróży w obie strony do serwera. Adres URL przekierowania zwrócony do klienta pojawia się na pasku adresu przeglądarki, gdy klient wysunie nowe żądanie dla zasobu.

Jeśli `/resource` zostanie *przekierowany* do `/different-resource`programu, serwer odpowiada, że `/different-resource` klient powinien uzyskać zasób z kodem stanu wskazującym, że przekierowanie jest tymczasowe lub stałe.

![Punkt końcowy usługi WebAPI został tymczasowo zmieniony z wersji 1 (wersja 1) na wersję 2 (wersja 2) na serwerze. Klient sprawia, że żądanie do usługi w wersji 1 ścieżka /v1/api. Serwer wysyła z powrotem odpowiedź 302 (Found) z nową, tymczasową ścieżką dla usługi w wersji 2 /v2/api. Klient wysunie drugie żądanie do usługi pod adresem URL przekierowania. Serwer odpowiada kodem stanu 200 (OK).](url-rewriting/_static/url_redirect.png)

Podczas przekierowywania żądań do innego adresu URL, należy wskazać, czy przekierowanie jest stałe czy tymczasowe, określając kod stanu z odpowiedzią:

* *301 - Przeniesiony kod* stanu trwale jest używany, gdy zasób ma nowy, stały adres URL i chcesz poinstruować klienta, że wszystkie przyszłe żądania dla zasobu powinny używać nowego adresu URL. *Klient może buforować i ponownie używać odpowiedzi po odebraniu kodu stanu 301.*

* Kod stanu *302 - Znaleziono* jest używany, gdy przekierowanie jest tymczasowe lub zazwyczaj może ulec zmianie. Kod stanu 302 oznacza, że klient nie przechowuje adresu URL i nie używa go w przyszłości.

Aby uzyskać więcej informacji na temat kodów stanu, zobacz [RFC 2616: Definicje kodu stanu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

*Przepisanie adresu URL* jest operacją po stronie serwera, która zapewnia zasób z adresu innego zasobu niż żądany klient. Przepisywanie adresu URL nie wymaga podróży w obie strony do serwera. Przepisany adres URL nie jest zwracany do klienta i nie jest wyświetlany na pasku adresu przeglądarki.

Jeśli `/resource` jest *przepisany* do `/different-resource`, serwer *wewnętrznie* pobiera i `/different-resource`zwraca zasób w .

Mimo że klient może być w stanie pobrać zasób przy przepisanych adresów URL, klient nie jest informowany, że zasób istnieje w przepisany adres URL, gdy sprawia, że jego żądanie i odbiera odpowiedź.

![Punkt końcowy usługi WebAPI został zmieniony z wersji 1 (wersja 1) na wersję 2 (wersja 2) na serwerze. Klient sprawia, że żądanie do usługi w wersji 1 ścieżka /v1/api. Adres URL żądania jest przepisany, aby uzyskać dostęp do usługi w ścieżce w wersji 2 /v2/api. Usługa odpowiada klientowi za pomocą kodu stanu 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Przykładowa aplikacja do przepisywania adresów URL

Za pomocą [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)można zapoznać się z funkcjami oprogramowania pośredniczącego przepisywania adresów URL. Aplikacja stosuje reguły przekierowania i przepisywania i pokazuje przekierowany lub przepisany adres URL dla kilku scenariuszy.

## <a name="when-to-use-url-rewriting-middleware"></a>Kiedy używać oprogramowania pośredniczącego do przepisywania adresów URL

Oprogramowanie pośredniczące do przepisywania adresów URL używa się następujących metod:

* [Moduł przepisywania adresów URL z usługami IIS w systemie Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Moduł mod_rewrite Apache na serwerze Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Przepisywanie adresów URL w Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Ponadto należy użyć oprogramowania pośredniczącego, gdy aplikacja jest hostowana na [serwerze HTTP.sys](xref:fundamentals/servers/httpsys) (dawniej nazywanym WebListener).

Główne powody, dla których warto używać opartych na serwerze technologii przepisywania adresów URL w usługach IIS, Apache i Nginx to:

* Oprogramowanie pośredniczące nie obsługuje pełnych funkcji tych modułów.

  Niektóre funkcje modułów serwera nie działają z ASP.NET projektów podstawowych, `IsFile` takich `IsDirectory` jak i ograniczenia modułu IIS Rewrite. W tych scenariuszach zamiast tego należy użyć oprogramowania pośredniczącego.
* Wydajność oprogramowania pośredniczącego prawdopodobnie nie pasuje do wydajności modułów.

  Analiza porównawcza jest jedynym sposobem, aby wiedzieć na pewno, które podejście obniża wydajność najbardziej lub jeśli obniżona wydajność jest znikoma.

## <a name="package"></a>Pakiet

Aby uwzględnić oprogramowanie pośredniczące w projekcie, dodaj odwołanie do pakietu [Microsoft.AspNetCore.App metapakiet](xref:fundamentals/metapackage-app) w pliku projektu, który zawiera pakiet [Microsoft.AspNetCore.Rewrite.](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite)

Jeśli nie `Microsoft.AspNetCore.App` jest używany metapackage, dodaj `Microsoft.AspNetCore.Rewrite` odwołanie do projektu do pakietu.

## <a name="extension-and-options"></a>Rozszerzenie i opcje

Uspokaj reguły przepisywania i przekierowywania adresów URL, tworząc wystąpienie klasy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) z metodami rozszerzenia dla każdej reguły przepisywania. Łańcuch wielu reguł w kolejności, w której chcesz je przetworzyć. Są `RewriteOptions` przekazywane do adresu URL przepisywanie oprogramowania pośredniczącego, <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>jak jest dodawany do potoku żądania z:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Przekieruj inne niż www na www

Trzy opcje pozwalają aplikacji przekierować`www` `www`nie-żądania do:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; Trwale przekieruj żądanie do `www` poddomeny, jeśli żądanie nie jest-`www`. Przekierowuje z kodem stanu [Status308PermanentRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect)

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Przekieruj `www` żądanie do poddomeny,`www`jeśli żądanie przychodzące nie jest - . Przekierowuje z kodem stanu [Status307TemporaryRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) Przeciążenie umożliwia podanie kodu stanu odpowiedzi. Użyj pola <xref:Microsoft.AspNetCore.Http.StatusCodes> klasy dla przypisania kodu stanu.

### <a name="url-redirect"></a>Przekierowywanie adresów URL

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> do przekierowywania żądań. Pierwszy parametr zawiera wyrażenie regularne do dopasowywania na ścieżce przychodzącego adresu URL. Drugi parametr to ciąg zastępczy. Trzeci parametr, jeśli jest obecny, określa kod stanu. Jeśli nie określisz kodu stanu, domyślnie kod stanu *302 - Znaleziono*, co oznacza, że zasób jest tymczasowo przeniesiony lub zastąpiony.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

W przeglądarce z włączonymi narzędziami deweloperskimi złożyć `/redirect-rule/1234/5678`żądanie do przykładowej aplikacji ze ścieżką . Wyrażenie regularne jest zgodne `redirect-rule/(.*)`ze ścieżką żądania, `/redirected/1234/5678`a ścieżka jest zastępowana . Adres URL przekierowania jest wysyłany z powrotem do klienta z kodem stanu *302 — znaleziono.* Przeglądarka tworzy nowe żądanie pod adresem URL przekierowania, który pojawia się na pasku adresu przeglądarki. Ponieważ żadne reguły w przykładowej aplikacji nie są zgodne z adresem URL przekierowania:

* Drugie żądanie otrzymuje *odpowiedź 200 — OK* z aplikacji.
* Treść odpowiedzi pokazuje adres URL przekierowania.

Po *przekierowaniu*adresu URL adres URL na serwer odbywa się podróż w obie strony.

> [!WARNING]
> Należy zachować ostrożność podczas ustanawiania reguł przekierowania. Reguły przekierowania są oceniane przy każdym żądaniu do aplikacji, w tym po przekierowaniu. Łatwo jest przypadkowo utworzyć *pętlę nieskończonych przekierowań*.

Oryginalna prośba:`/redirect-rule/1234/5678`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi](url-rewriting/_static/add_redirect.png)

Część wyrażenia zawarta w nawiasach jest nazywana *grupą przechwytywania*. Kropka (`.`) wyrażenia oznacza *dopasowanie dowolnego znaku*. Gwiazdka (`*`) oznacza *dopasowanie poprzedniego znaku zero lub więcej razy*. W związku z tym dwa ostatnie segmenty ścieżki adresu `1234/5678` `(.*)`URL, są przechwytywane przez grupę przechwytywania . Każda wartość podana w `redirect-rule/` adresie URL żądania po jest przechwytywany przez tę pojedynczą grupę przechwytywania.

W ciągu zastępczym przechwycone grupy są wtryskiwane`$`do ciągu ze znakiem dolara ( ), po którym następuje numer sekwencyjny przechwytywania. Pierwsza wartość grupy przechwytywania `$1`jest uzyskiwana za pomocą , drugi z `$2`, i są one kontynuowane w kolejności dla grup przechwytywania w wyrażeniach. Istnieje tylko jedna przechwycona grupa w resecie przekierowania regex w przykładowej aplikacji, więc `$1`jest tylko jedna wstrzyknięta grupa w ciągu zastępczym, który jest . Po zastosowaniu reguły adres `/redirected/1234/5678`URL staje się .

### <a name="url-redirect-to-a-secure-endpoint"></a>Przekierowanie adresu URL do bezpiecznego punktu końcowego

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> do przekierowywania żądań HTTP do tego samego hosta i ścieżki przy użyciu protokołu HTTPS. Jeśli kod stanu nie jest podany, oprogramowanie pośredniczące domyślnie *302 - Znaleziono*. Jeśli port nie jest podany:

* Oprogramowanie pośredniczące `null`domyślnie ma wartość .
* Schemat zmienia `https` się na (protokół HTTPS), a klient uzyskuje dostęp do zasobu na porcie 443.

W poniższym przykładzie pokazano, jak ustawić kod stanu na *301 — przeniesiony na stałe* i zmienić port na 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> do przekierowywania niezabezpieczonych żądań do tego samego hosta i ścieżki z bezpiecznym protokołem HTTPS na porcie 443. Oprogramowanie pośredniczące ustawia kod stanu na *301 - Przeniesiony na stałe*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Podczas przekierowywania do bezpiecznego punktu końcowego bez wymogu dodatkowych reguł przekierowania, zaleca się użycie oprogramowania pośredniczącego przekierowania HTTPS. Aby uzyskać więcej informacji, zobacz temat [Wymuszanie protokołu HTTPS.](xref:security/enforcing-ssl#require-https)

Przykładowa aplikacja jest w stanie `AddRedirectToHttps` wykazać, jak używać lub `AddRedirectToHttpsPermanent`. Dodaj metodę rozszerzenia `RewriteOptions`do pliku . Złożyć niezabezpieczone żądanie do aplikacji w dowolnym adresie URL. Odrzuć ostrzeżenie zabezpieczeń przeglądarki, że certyfikat z podpisem własnym jest niezaufany lub utwórz wyjątek, aby ufać certyfikatowi.

Oryginalne żądanie `AddRedirectToHttps(301, 5001)`za pomocą:`http://localhost:5000/secure`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi](url-rewriting/_static/add_redirect_to_https.png)

Oryginalne żądanie `AddRedirectToHttpsPermanent`za pomocą:`http://localhost:5000/secure`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Regenerowanie adresów URL

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> do tworzenia reguły przepisywania adresów URL. Pierwszy parametr zawiera wyrażenie regularne do dopasowywania na przychodzącej ścieżce adresu URL. Drugi parametr to ciąg zastępczy. Trzeci parametr `skipRemainingRules: {true|false}`, wskazuje oprogramowaniu pośredniczącemu, czy pominąć dodatkowe reguły ponownego zapisu, jeśli stosowana jest bieżąca reguła.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Oryginalna prośba:`/rewrite-rule/1234/5678`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądanie i odpowiedź](url-rewriting/_static/add_rewrite.png)

Karat (`^`) na początku wyrażenia oznacza, że dopasowywanie rozpoczyna się na początku ścieżki adresu URL.

We wcześniejszym przykładzie z `redirect-rule/(.*)`regułą przekierowania ,`^`nie ma karatów ( ) na początku wyrażenia regularnego. W związku z tym `redirect-rule/` wszystkie znaki mogą poprzedzać w ścieżce dla pomyślnego dopasowania.

| Ścieżka                               | Dopasowanie |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Tak   |
| `/my-cool-redirect-rule/1234/5678` | Tak   |
| `/anotherredirect-rule/1234/5678`  | Tak   |

Reguła przepisywania, `^rewrite-rule/(\d+)/(\d+)`, dopasowuje ścieżki `rewrite-rule/`tylko wtedy, gdy zaczynają się od . W poniższej tabeli należy zwrócić uwagę na różnicę w dopasowywaniu.

| Ścieżka                              | Dopasowanie |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Tak   |
| `/my-cool-rewrite-rule/1234/5678` | Nie    |
| `/anotherrewrite-rule/1234/5678`  | Nie    |

Po `^rewrite-rule/` części wyrażenia istnieją dwie grupy przechwytywania, `(\d+)/(\d+)`. Oznacza `\d` to *dopasowanie cyfry (liczby)*. Znak plus`+`( ) oznacza *dopasowanie jednego lub więcej z poprzedniego znaku*. W związku z tym adres URL musi zawierać liczbę, po której następuje ukośnik do przodu, po którym następuje inny numer. Te grupy przechwytywania są wstrzykiwane `$1` do `$2`przepisanych adresów URL jako i . Ciąg zastępowania reguły ponownego przepisywania umieszcza przechwycone grupy w ciągu zapytania. Żądana ścieżka `/rewrite-rule/1234/5678` jest przepisana w celu `/rewritten?var1=1234&var2=5678`uzyskania zasobu w pliku . Jeśli ciąg zapytania jest obecny na oryginalnym żądaniu, jest zachowywany, gdy adres URL jest przepisany.

Nie ma żadnej podróży w obie strony do serwera, aby uzyskać zasób. Jeśli zasób istnieje, jest pobierany i zwracany do klienta z kodem stanu *200 - OK.* Ponieważ klient nie jest przekierowywał, adres URL na pasku adresu przeglądarki nie zmienia się. Klienci nie mogą wykryć, że na serwerze wystąpiła operacja ponownego zmiany adresu URL.

> [!NOTE]
> Używaj, `skipRemainingRules: true` gdy jest to możliwe, ponieważ reguły dopasowywania są kosztowne pod względem obliczeniowym i wydłuża czas reakcji aplikacji. Aby uzyskać najszybszą odpowiedź aplikacji:
>
> * Kolejność przepisywania reguł z najczęściej dopasowywane reguły do reguły najrzadziej dopasowane.
> * Pomiń przetwarzanie pozostałych reguł, gdy wystąpi dopasowanie i nie jest wymagane żadne dodatkowe przetwarzanie reguł.

### <a name="apache-mod_rewrite"></a>mod_rewrite Apache

Zastosuj apache mod_rewrite reguły <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>z . Upewnij się, że plik reguł jest wdrożony z aplikacją. Aby uzyskać więcej informacji i przykłady reguł mod_rewrite, zobacz [mod_rewrite Apache](https://httpd.apache.org/docs/2.4/rewrite/).

A <xref:System.IO.StreamReader> służy do odczytywania reguł z pliku reguł *ApacheModRewrite.txt:*

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Przykładowa aplikacja przekierowuje żądania z `/apache-mod-rules-redirect/(.\*)` . `/redirected?id=$1` Kod stanu odpowiedzi to *302 - Znaleziono*.

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

Oryginalna prośba:`/apache-mod-rules-redirect/1234`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi](url-rewriting/_static/add_apache_mod_redirect.png)

Oprogramowanie pośredniczące obsługuje następujące zmienne serwera mod_rewrite Apache:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* Protokół IPV6
* Query_string
* REMOTE_ADDR
* REMOTE_PORT
* Request_filename
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* CZAS
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Reguły modułu przepisywania adresów URL w uirozmywanych adresach URL w uirozie.

Aby użyć tego samego zestawu reguł, który ma zastosowanie do <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>modułu przepisywania adresu URL iIS, należy użyć programu . Upewnij się, że plik reguł jest wdrożony z aplikacją. Nie kieruj oprogramowania pośredniczącego do używania pliku *web.config* aplikacji podczas uruchamiania w systemie Windows Server IIS. Dzięki usługom IIS te reguły powinny być przechowywane poza *plikiem web.config* aplikacji, aby uniknąć konfliktów z modułem Przepisywanie usług IIS. Aby uzyskać więcej informacji i przykłady reguł modułu zapisywania adresów URL usług IIS, zobacz [Korzystanie z modułu 2.0 do przepisywania adresów URL](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) i odwołanie do konfiguracji [modułu ponownego zapisu adresu URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

A <xref:System.IO.StreamReader> służy do odczytywania reguł z pliku reguł *IISUrlRewrite.xml:*

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Przykładowa aplikacja przepisuje `/iis-rules-rewrite/(.*)` `/rewritten?id=$1`żądania z do . Odpowiedź jest wysyłana do klienta z kodem stanu *200 - OK.*

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

Oryginalna prośba:`/iis-rules-rewrite/1234`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądanie i odpowiedź](url-rewriting/_static/add_iis_url_rewrite.png)

Jeśli masz aktywny moduł przepisywania usług IIS z skonfigurowaną regułą na poziomie serwera, która miałaby wpływ na aplikację w niepożądany sposób, możesz wyłączyć moduł przepisywania usług IIS dla aplikacji. Aby uzyskać więcej informacji, zobacz [Wyłączanie modułów IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nieobsługiwały się funkcje

Oprogramowanie pośredniczące wydane z ASP.NET Core 2.x nie obsługuje następujących funkcji modułu zapisywania adresów URL IIS:

* Reguły ruchu wychodzącego
* Niestandardowe zmienne serwera
* Symbole wieloznaczne
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Obsługiwane zmienne serwera

Oprogramowanie pośredniczące obsługuje następujące zmienne serwera serwera iis url rewrite module:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* Query_string
* REMOTE_ADDR
* REMOTE_PORT
* Request_filename
* REQUEST_URI

> [!NOTE]
> Można również uzyskać <xref:Microsoft.Extensions.FileProviders.IFileProvider> za <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>pośrednictwem . Takie podejście może zapewnić większą elastyczność lokalizacji plików reguł ponownego zapisu. Upewnij się, że pliki reguł ponownego zapisu są wdrażane na serwerze w ścieżce, którą podasz.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Reguła oparta na metodzie

Służy <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> do implementowania własnej logiki reguły w metodzie. `Add`udostępnia <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, który udostępnia <xref:Microsoft.AspNetCore.Http.HttpContext> do wykorzystania w metodzie. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) określa sposób obsługi dodatkowego przetwarzania potoku. Ustaw wartość jednego z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> pól opisanych w poniższej tabeli.

| `RewriteContext.Result`              | Akcja                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules`(domyślnie) | Kontynuuj stosowanie reguł.                                         |
| `RuleResult.EndResponse`             | Przestań stosować reguły i wyślij odpowiedź.                       |
| `RuleResult.SkipRemainingRules`      | Przestań stosować reguły i wyślij kontekst do następnego oprogramowania pośredniczącego. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Przykładowa aplikacja pokazuje metodę, która przekierowuje żądania ścieżek, które kończą się na *.xml*. Jeśli zostanie złożony `/file.xml`wniosek, żądanie zostanie `/xmlfiles/file.xml`przekierowane do . Kod stanu jest ustawiony na *301 - Przeniesiony na stałe*. Gdy przeglądarka wysunie nowe żądanie dla */xmlfiles/file.xml*, Oprogramowanie pośredniczące plików statycznych służy do klienta z folderu *wwwroot/xmlfiles.* W przypadku przekierowania jawnie ustaw kod stanu odpowiedzi. W przeciwnym razie zwracany jest kod stanu *200 - OK,* a przekierowanie nie występuje na kliencie.

*RewriteRules.cs:*

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Takie podejście można również przepisać żądania. Przykładowa aplikacja demonstruje przepisywanie ścieżki dla dowolnego żądania pliku tekstowego do obsługi pliku tekstowego *file.txt* z folderu *wwwroot.* Oprogramowanie pośredniczące plików statycznych obsługuje plik na podstawie zaktualizowanej ścieżki żądania:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs:*

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Reguła oparta na zasadach irule

Użyj <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> logiki reguły w klasie, <xref:Microsoft.AspNetCore.Rewrite.IRule> która implementuje interfejs. `IRule`zapewnia większą elastyczność w stosowania metody opartej na metodzie. Klasa implementacji może zawierać konstruktora, który <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> umożliwia przekazywanie parametrów dla metody.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Wartości parametrów w przykładowej aplikacji `extension` dla `newPath` i są sprawdzane, aby spełnić kilka warunków. Musi `extension` zawierać wartość, a wartość musi być *.png*, *.jpg*lub *gif*. Jeśli `newPath` nie jest prawidłowy, jest wyrzucany. <xref:System.ArgumentException> Jeśli zostanie złożony wniosek o *image.png,* żądanie `/png-images/image.png`zostanie przekierowane do . Jeśli zostanie złożony wniosek o *image.jpg*, `/jpg-images/image.jpg`żądanie zostanie przekierowane do . Kod stanu jest ustawiony na *301 -* `context.Result` Przeniesiony na stałe , a jest ustawiony, aby zatrzymać reguły przetwarzania i wysłać odpowiedź.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Oryginalna prośba:`/image.png`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi dla pliku image.png](url-rewriting/_static/add_redirect_png_requests.png)

Oryginalna prośba:`/image.jpg`

![Okno przeglądarki z narzędziami deweloperskimi śledzącymi żądania i odpowiedzi dla pliku image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Przykłady regex

| Cel | & ciągu regex<br>Przykład dopasowania | & ciągu zastępczego<br>Przykład wyjścia |
| ---- | ------------------------------- | -------------------------------------- |
| Przepisanie ścieżki do sznurka zapytania | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Ukośnik na końcu taśmy | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Wymuszanie końcowego ukośnika | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Unikaj przepisywania określonych żądań | `^(.*)(?<!\.axd)$` lub `^(?!.*\.axd$)(.*)$`<br>Tak:`/resource.htm`<br>№:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Zmienianie rozmieszczenia segmentów adresów URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Zastępowanie segmentu adresu URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Wyrażenia regularne w programie .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Język wyrażenia regularnego - podręczny punkt odniesienia](/dotnet/articles/standard/base-types/quick-ref)
* [mod_rewrite Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Korzystanie z modułu 2.0 (dla iis)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [Odwołanie do konfiguracji modułu ponownego zapisu adresu URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [Forum modułu zapisywania adresów URL programu IIS](https://forums.iis.net/1152.aspx)
* [Zachowaj prostą strukturę adresów URL](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 porad i wskazówek dotyczących przepisywania adresów URL](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [Aby ciąć lub nie ciąć](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)

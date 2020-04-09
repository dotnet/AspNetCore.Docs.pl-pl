---
title: Moduły IIS z ASP.NET Core
author: rick-anderson
description: Odkryj aktywne i nieaktywne moduły usług IIS dla aplikacji ASP.NET Core oraz jak zarządzać modułami usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 0f13ef3eb1da03960ef1fa54d33532b6ebbdc128
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657907"
---
# <a name="iis-modules-with-aspnet-core"></a>Moduły IIS z ASP.NET Core

Niektóre natywne moduły usług IIS i wszystkie moduły zarządzane usług IIS nie są w stanie przetwarzać żądań dla aplikacji ASP.NET Core. W wielu przypadkach ASP.NET Core oferuje alternatywę dla scenariuszy adresowanych przez natywne i zarządzane moduły usług IIS.

## <a name="native-modules"></a>Moduły natywne

Tabela wskazuje natywne moduły usług IIS, które działają z aplikacjami ASP.NET Core i ASP.NET Core Module.

| Moduł | Funkcjonalne aplikacje ASP.NET Core | opcja ASP.NET Core |
| --- | :---: | --- |
| **Uwierzytelnianie anonimowe**<br>`AnonymousAuthenticationModule`                                  | Tak | |
| **Uwierzytelnianie podstawowe**<br>`BasicAuthenticationModule`                                          | Tak | |
| **Uwierzytelnianie mapowania certyfikacji klienta**<br>`CertificateMappingAuthenticationModule`      | Tak | |
| **CGI**<br>`CgiModule`                                                                           | Nie  | |
| **Sprawdzanie poprawności konfiguracji**<br>`ConfigurationValidationModule`                                  | Tak | |
| **Błędy HTTP**<br>`CustomErrorModule`                                                           | Nie  | [Oprogramowanie pośredniczące stron kodowych stanu](xref:fundamentals/error-handling#usestatuscodepages) |
| **Rejestrowanie niestandardowe**<br>`CustomLoggingModule`                                                      | Tak | |
| **Dokument domyślny**<br>`DefaultDocumentModule`                                                  | Nie  | [Oprogramowanie pośredniczące plików domyślnych](xref:fundamentals/static-files#serve-a-default-document) |
| **Uwierzytelnianie szyfrowane**<br>`DigestAuthenticationModule`                                        | Tak | |
| **Przeglądanie katalogów**<br>`DirectoryListingModule`                                               | Nie  | [Oprogramowanie pośredniczące przeglądania katalogów](xref:fundamentals/static-files#enable-directory-browsing) |
| **Kompresja dynamiczna**<br>`DynamicCompressionModule`                                            | Tak | [Oprogramowanie pośredniczące kompresji odpowiedzi](xref:performance/response-compression) |
| **Śledzenie żądań nie powiodło się**<br>`FailedRequestsTracingModule`                                     | Tak | [Rejestrowanie rdzenia ASP.NET](xref:fundamentals/logging/index#tracesource-provider) |
| **Buforowanie plików**<br>`FileCacheModule`                                                            | Nie  | [Oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) |
| **Buforowanie HTTP**<br>`HttpCacheModule`                                                            | Nie  | [Oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) |
| **Rejestrowanie HTTP**<br>`HttpLoggingModule`                                                          | Tak | [Rejestrowanie rdzenia ASP.NET](xref:fundamentals/logging/index) |
| **Przekierowywanie ruchu HTTP**<br>`HttpRedirectionModule`                                                  | Tak | [Oprogramowanie pośredniczące ponownego zapisywania adresów URL](xref:fundamentals/url-rewriting) |
| **Śledzenie HTTP**<br>`TracingModule`                                                              | Tak | |
| **Uwierzytelnianie mapowania certyfikatów klienta usługi IIS**<br>`IISCertificateMappingAuthenticationModule` | Tak | |
| **Ograniczenia adresów IP i domen**<br>`IpRestrictionModule`                                          | Tak | |
| **Filtry ISAPI**<br>`IsapiFilterModule`                                                         | Tak | [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) |
| **Isapi**<br>`IsapiModule`                                                                       | Tak | [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) |
| **Obsługa protokołu**<br>`ProtocolSupportModule`                                                  | Tak | |
| **Filtrowanie żądań**<br>`RequestFilteringModule`                                                | Tak | [Oprogramowanie pośredniczące przepisywania adresów URL`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Monitor żądań**<br>`RequestMonitorModule`                                                    | Tak | |
| &#8224; **przepisywania adresów URL**<br>`RewriteModule`                                                      | Tak | [Oprogramowanie pośredniczące ponownego zapisywania adresów URL](xref:fundamentals/url-rewriting) |
| **Zawiera po stronie serwera**<br>`ServerSideIncludeModule`                                            | Nie  | |
| **Kompresja statyczna**<br>`StaticCompressionModule`                                              | Nie  | [Oprogramowanie pośredniczące kompresji odpowiedzi](xref:performance/response-compression) |
| **Zawartość statyczna**<br>`StaticFileModule`                                                         | Nie  | [Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) |
| **Buforowanie tokenów**<br>`TokenCacheModule`                                                          | Tak | |
| **Buforowanie URI**<br>`UriCacheModule`                                                              | Tak | |
| **Autoryzacja adresów URL**<br>`UrlAuthorizationModule`                                                | Tak | [ASP.NET tożsamość podstawowa](xref:security/authentication/identity) |
| **Uwierzytelnianie systemu Windows**<br>`WindowsAuthenticationModule`                                      | Tak | |

&#8224;Typy modułu `isFile` i `isDirectory` dopasowania modułu i dopasowania adresu URL nie działają z aplikacjami ASP.NET Core ze względu na zmiany w strukturze [katalogów.](xref:host-and-deploy/directory-structure)

## <a name="managed-modules"></a>Moduły zarządzane

Moduły zarządzane *nie* działają w aplikacjach hostowanych ASP.NET Core, gdy wersja .NET CLR puli aplikacji jest ustawiona na **Brak kodu zarządzanego.** ASP.NET Core oferuje alternatywy oprogramowania pośredniczącego w kilku przypadkach.

| Moduł                  | opcja ASP.NET Core |
| ----------------------- | ------------------- |
| Anonimowa identyfikacja | |
| Domyślnauthentication   | |
| Autoryzacja pliku       | |
| Formsauthentication     | [Oprogramowanie pośredniczące uwierzytelniania plików cookie](xref:security/authentication/cookie) |
| Outputcache             | [Oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) |
| Profil                 | |
| Menedżer roli             | |
| Moduł skryptów-4.0        | |
| Sesja                 | [Oprogramowanie pośredniczące sesji](xref:fundamentals/app-state) |
| Autoryzacja url        | |
| UrlMappingsModule       | [Oprogramowanie pośredniczące ponownego zapisywania adresów URL](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4.0    | [ASP.NET tożsamość podstawowa](xref:security/authentication/identity) |
| Windowsauthentication   | |

## <a name="iis-manager-application-changes"></a>Zmiany aplikacji Menedżera usług IIS

Podczas konfigurowania ustawień za pomocą Menedżera usług IIS plik *web.config* aplikacji zostanie zmieniony. W przypadku wdrażania aplikacji i dołączania *pliku web.config*wszelkie zmiany wprowadzone w menedżerze usług IIS są zastępowane przez wdrożony plik *web.config.* Jeśli zostaną wprowadzone zmiany w pliku *web.config* serwera, należy natychmiast skopiować zaktualizowany plik *web.config* na serwerze do projektu lokalnego.

## <a name="disabling-iis-modules"></a>Wyłączanie modułów IIS

Jeśli moduł usług IIS jest skonfigurowany na poziomie serwera, który musi być wyłączony dla aplikacji, dodatek do pliku *web.config* aplikacji można wyłączyć moduł. Pozostaw moduł na swoim miejscu i zdezaktywuj go za pomocą ustawienia konfiguracji (jeśli jest dostępny) lub usuń moduł z aplikacji.

### <a name="module-deactivation"></a>Dezaktywacja modułu

Wiele modułów oferuje ustawienie konfiguracji, które umożliwia ich wyłączenie bez usuwania modułu z aplikacji. Jest to najprostszy i najszybszy sposób dezaktywacji modułu. Na przykład moduł przekierowania HTTP można `<httpRedirect>` wyłączyć za pomocą elementu w *web.config:*

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Aby uzyskać więcej informacji na temat wyłączania modułów z ustawieniami konfiguracji, skorzystaj z łączy w sekcji *Elementy podrzędne* [usługi IIS \<system.webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Usuwanie modułów

Jeśli zdecydujesz się usunąć moduł z ustawieniem w *web.config,* odblokuj moduł i najpierw odblokuj sekcję `<modules>` *web.config:*

1. Odblokuj moduł na poziomie serwera. Wybierz serwer usług IIS na pasku bocznym **Połączenia** menedżera usług IIS. Otwórz **moduły** w obszarze **IIS.** Wybierz moduł na liście. Na pasku bocznym **Akcje** po prawej stronie wybierz pozycję **Odblokuj**. Jeśli wpis akcji dla modułu pojawia się jako **Blokada,** moduł jest już odblokowany i nie jest wymagana żadna akcja. Odblokuj tyle modułów, ile planujesz usunąć z *web.config* później.

2. Wdrażanie aplikacji `<modules>` bez sekcji w *witrynie web.config*. Jeśli aplikacja jest wdrażana z *web.config* zawierający sekcję `<modules>` bez odblokowania sekcji po raz pierwszy w Menedżerze usług IIS, menedżer konfiguracji zgłasza wyjątek podczas próby odblokowania sekcji. W związku z tym `<modules>` wdrożyć aplikację bez sekcji.

3. Odblokuj sekcję `<modules>` *web.config*. Na pasku bocznym **Połączenia** wybierz witrynę sieci Web w **obszarze Witryny**. W obszarze **Zarządzanie** otwórz **Edytor konfiguracji**. Użyj kontrolek nawigacji, `system.webServer/modules` aby wybrać sekcję. Na pasku bocznym **Akcje** po prawej stronie wybierz pozycję **Odblokuj** sekcję. Jeśli wpis akcji dla sekcji modułu jest wyświetlany jako **Sekcja blokady,** sekcja modułu jest już odblokowana i nie jest wymagana żadna akcja.

4. Dodaj `<modules>` sekcję do lokalnego pliku *web.config* aplikacji z elementem, `<remove>` aby usunąć moduł z aplikacji. Dodaj `<remove>` wiele elementów, aby usunąć wiele modułów. Jeśli na serwerze zostaną wprowadzone zmiany *web.config,* natychmiast wprowadzać te same zmiany w pliku *web.config* projektu lokalnie. Usunięcie modułu przy użyciu tej metody nie wpływa na użycie modułu z innymi aplikacjami na serwerze.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Aby dodać lub usunąć moduły dla usług IIS Express za pomocą *web.config,* zmodyfikuj *applicationHost.config,* aby odblokować sekcję: `<modules>`

1. Otwórz *{APPLICATION\\ROOT} .vs\config\applicationhost.config*.

1. Zlokalizuj `<section>` element dla modułów IIS i zmień `overrideModeDefault` z: `Deny` `Allow`

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. Znajdź sekcję `<location path="" overrideMode="Allow"><system.webServer><modules>`. W przypadku modułów, które chcesz `lockItem` `true` usunąć, ustaw z . `false` W poniższym przykładzie moduł CGI jest odblokowany:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. Po `<modules>` odblokowaniu sekcji i poszczególnych modułów możesz dodawać lub usuwać moduły usług IIS przy użyciu pliku *web.config* aplikacji do uruchamiania aplikacji w u usług IIS Express.

Moduł IIS można również usunąć za pomocą *pliku Appcmd.exe*. `MODULE_NAME` Podaj `APPLICATION_NAME` i w poleceniu:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Na przykład usuń `DynamicCompressionModule` z domyślnej witryny sieci Web:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Minimalna konfiguracja modułu

Jedynymi modułami wymaganymi do uruchomienia aplikacji ASP.NET Core są moduł uwierzytelniania anonimowego i moduł ASP.NET Core.

Moduł buforowania identyfikatorów`UriCacheModule`URI ( ) umożliwia usługom IIS buforowanie konfiguracji witryny sieci Web na poziomie adresu URL. Bez tego modułu usługi IIS muszą odczytywać i analizować konfigurację na każde żądanie, nawet jeśli ten sam adres URL jest wielokrotnie wymagany. Analizowanie konfiguracji każde żądanie powoduje znaczne kary wydajności. *Chociaż moduł buforowania identyfikatorów URI nie jest ściśle wymagane dla hostowanego ASP.NET core aplikacji do uruchomienia, zaleca się, że moduł buforowania identyfikatorów URI być włączone dla wszystkich ASP.NET wdrożeń Core.*

Moduł buforowania HTTP`HttpCacheModule`( ) implementuje pamięć podręczną danych wyjściowych IIS, a także logikę buforowania elementów w pamięci podręcznej HTTP.sys. Bez tego modułu zawartość nie jest już buforowana w trybie jądra, a profile pamięci podręcznej są ignorowane. Usunięcie modułu buforowania HTTP zwykle ma niekorzystny wpływ na wydajność i użycie zasobów. *Mimo że moduł buforowania HTTP nie jest ściśle wymagane dla hostowanego ASP.NET Core aplikacji do uruchomienia, zaleca się, że moduł buforowania HTTP być włączone dla wszystkich ASP.NET wdrożeń Core.*

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wprowadzenie do architektur IIS: moduły w ujm iis](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Omówienie modułów IIS](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Dostosowywanie ról i modułów usługi IIS 7.0](https://technet.microsoft.com/library/cc627313.aspx)
* [>systemu \<IIS.webServer](/iis/configuration/system.webServer/)

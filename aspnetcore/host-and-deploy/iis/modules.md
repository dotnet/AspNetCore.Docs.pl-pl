---
title: Moduły usług IIS z ASP.NET Core
author: rick-anderson
description: Odnajdywanie aktywnych i nieaktywnych modułów usług IIS dla aplikacji ASP.NET Core i sposobu zarządzania modułami usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 47ba04f199f9b77cf6032de9f80f2410f5c69424
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057404"
---
# <a name="iis-modules-with-aspnet-core"></a>Moduły usług IIS z ASP.NET Core

Niektóre z natywnych modułów usług IIS i wszystkich modułów zarządzanych przez usługi IIS nie mogą przetwarzać żądań dla ASP.NET Core aplikacji. W wielu przypadkach ASP.NET Core oferuje alternatywę dla scenariuszy rozłożonych przez moduły natywne i zarządzane usług IIS.

## <a name="native-modules"></a>Moduły macierzyste

Tabela wskazuje natywne moduły usług IIS, które działają w aplikacjach ASP.NET Core i ASP.NET Core Module.

| Moduł | Funkcjonalne z ASP.NET Core aplikacjami | Opcja ASP.NET Core |
| --- | :---: | --- |
| **Uwierzytelnianie anonimowe**<br>`AnonymousAuthenticationModule`                                  | Yes | |
| **Uwierzytelnianie podstawowe**<br>`BasicAuthenticationModule`                                          | Yes | |
| **Uwierzytelnianie mapowań certyfikatów klientów**<br>`CertificateMappingAuthenticationModule`      | Yes | |
| **CGI**<br>`CgiModule`                                                                           | Nie  | |
| **Weryfikacja konfiguracji**<br>`ConfigurationValidationModule`                                  | Yes | |
| **Błędy HTTP**<br>`CustomErrorModule`                                                           | Nie  | [Oprogramowanie pośredniczące stron kodu stanu](xref:fundamentals/error-handling#usestatuscodepages) |
| **Rejestrowanie niestandardowe**<br>`CustomLoggingModule`                                                      | Yes | |
| **Dokument domyślny**<br>`DefaultDocumentModule`                                                  | Nie  | [Pliki domyślne oprogramowania pośredniczącego](xref:fundamentals/static-files#serve-a-default-document) |
| **Uwierzytelnianie szyfrowane**<br>`DigestAuthenticationModule`                                        | Yes | |
| **Przeglądanie katalogów**<br>`DirectoryListingModule`                                               | Nie  | [Oprogramowanie pośredniczące przeglądania katalogów](xref:fundamentals/static-files#enable-directory-browsing) |
| **Kompresja dynamiczna**<br>`DynamicCompressionModule`                                            | Yes | [Oprogramowanie pośredniczące kompresji odpowiedzi](xref:performance/response-compression) |
| **Śledzenie nieudanych żądań**<br>`FailedRequestsTracingModule`                                     | Yes | [Rejestrowanie ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Buforowanie plików**<br>`FileCacheModule`                                                            | Nie  | [Oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) |
| **Buforowanie HTTP**<br>`HttpCacheModule`                                                            | Nie  | [Oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) |
| **Rejestrowanie HTTP**<br>`HttpLoggingModule`                                                          | Yes | [Rejestrowanie ASP.NET Core](xref:fundamentals/logging/index) |
| **Przekierowywanie ruchu HTTP**<br>`HttpRedirectionModule`                                                  | Yes | [Oprogramowanie pośredniczące ponownego zapisywania adresów URL](xref:fundamentals/url-rewriting) |
| **Śledzenie HTTP**<br>`TracingModule`                                                              | Yes | |
| **Uwierzytelnianie mapowania certyfikatu klienta usług IIS**<br>`IISCertificateMappingAuthenticationModule` | Yes | |
| **Ograniczenia adresów IP i domen**<br>`IpRestrictionModule`                                          | Yes | |
| **Filtry ISAPI**<br>`IsapiFilterModule`                                                         | Yes | [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) |
| **INTERCEPTOR**<br>`IsapiModule`                                                                       | Yes | [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) |
| **Obsługa protokołu**<br>`ProtocolSupportModule`                                                  | Yes | |
| **Filtrowanie żądań**<br>`RequestFilteringModule`                                                | Yes | [Ponowne zapisywanie przez adres URL oprogramowania pośredniczącego `IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Monitor żądań**<br>`RequestMonitorModule`                                                    | Yes | |
| Ponowne **Zapisywanie adresów URL**&#8224;<br>`RewriteModule`                                                      | Yes | [Oprogramowanie pośredniczące ponownego zapisywania adresów URL](xref:fundamentals/url-rewriting) |
| **Zawiera po stronie serwera**<br>`ServerSideIncludeModule`                                            | Nie  | |
| **Kompresja statyczna**<br>`StaticCompressionModule`                                              | Nie  | [Oprogramowanie pośredniczące kompresji odpowiedzi](xref:performance/response-compression) |
| **Zawartość statyczna**<br>`StaticFileModule`                                                         | Nie  | [Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) |
| **Buforowanie tokenów**<br>`TokenCacheModule`                                                          | Yes | |
| **Buforowanie URI**<br>`UriCacheModule`                                                              | Yes | |
| **Autoryzacja adresów URL**<br>`UrlAuthorizationModule`                                                | Yes | [ASP.NET Core Identity](xref:security/authentication/identity) |
| **Uwierzytelnianie systemu Windows**<br>`WindowsAuthenticationModule`                                      | Yes | |

&#8224;typy i dopasowania modułu ponownego zapisywania adresu URL `isFile` `isDirectory` nie działają z aplikacjami ASP.NET Core ze względu na zmiany [struktury katalogów](xref:host-and-deploy/directory-structure).

## <a name="managed-modules"></a>Moduły zarządzane

Moduły zarządzane *nie* działają w przypadku hostowanych aplikacji ASP.NET Core, gdy wersja środowiska .NET CLR puli aplikacji jest ustawiona na **Brak kodu zarządzanego**. ASP.NET Core oferuje alternatywy dla oprogramowania pośredniczącego w kilku przypadkach.

| Moduł                  | Opcja ASP.NET Core |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| FormsAuthentication     | [Cookie Oprogramowanie pośredniczące uwierzytelniania](xref:security/authentication/cookie) |
| OutputCache             | [Oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) |
| Profil                 | |
| RoleManager             | |
| ScriptModule-4,0        | |
| Sesja                 | [Oprogramowanie pośredniczące sesji](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [Oprogramowanie pośredniczące ponownego zapisywania adresów URL](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4,0    | [ASP.NET Core Identity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>Zmiany aplikacji Menedżera usług IIS

W przypadku konfigurowania ustawień przy użyciu Menedżera usług IIS zostanie zmieniony plik *web.config* aplikacji. W przypadku wdrażania aplikacji i w tym *web.config* wszelkie zmiany wprowadzone za pomocą Menedżera usług IIS są zastępowane przez wdrożony plik *web.config* . Jeśli zmiany są wprowadzane do pliku *web.config* serwera, należy natychmiast skopiować zaktualizowany plik *web.config* do lokalnego projektu.

## <a name="disabling-iis-modules"></a>Wyłączanie modułów usług IIS

Jeśli moduł IIS jest skonfigurowany na poziomie serwera, który musi być wyłączony dla aplikacji, dodanie do pliku *web.config* aplikacji może spowodować wyłączenie modułu. Pozostaw moduł w miejscu i Dezaktywuj go przy użyciu ustawienia konfiguracji (jeśli jest dostępne) lub usuń moduł z aplikacji.

### <a name="module-deactivation"></a>Dezaktywacja modułu

Wiele modułów oferuje ustawienie konfiguracji umożliwiające wyłączenie go bez usuwania modułu z aplikacji. Jest to najprostszy i najszybszy sposób dezaktywowania modułu. Na przykład moduł przekierowania HTTP można wyłączyć za pomocą `<httpRedirect>` elementu w *web.config*:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Aby uzyskać więcej informacji na temat wyłączania modułów z ustawieniami konfiguracji, postępuj zgodnie z linkami w sekcji *elementy podrzędne* [usług IIS \<system.webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Usuwanie modułu

Jeśli nie chcesz usunąć modułu z ustawieniem w *web.config*, Odblokuj moduł i Odblokuj `<modules>` sekcję *web.config* najpierw:

1. Odblokuj moduł na poziomie serwera. Na pasku bocznym **połączenia** Menedżera usług IIS wybierz serwer IIS. Otwórz **moduły** w obszarze **IIS** . Wybierz moduł z listy. Na pasku bocznym **Akcje** po prawej stronie wybierz pozycję **Odblokuj**. Jeśli wpis akcji dla modułu pojawia się jako **Blokada**, moduł jest już odblokowany i nie jest wymagana żadna akcja. Odblokuj jako wiele modułów, które zamierzasz usunąć z *web.config* później.

2. Wdróż aplikację bez `<modules>` sekcji w *web.config*. Jeśli aplikacja zostanie wdrożona za pomocą *web.config* zawierającej `<modules>` sekcję bez uprzedniego odblokowania sekcji w Menedżerze usług IIS, Configuration Manager zgłasza wyjątek podczas próby odblokowania sekcji. W związku z tym Wdróż aplikację bez `<modules>` sekcji.

3. Odblokuj `<modules>` sekcję *web.config*. Na pasku bocznym **połączenia** wybierz witrynę sieci Web w obszarze **Lokacje**. W obszarze **Zarządzanie** Otwórz **Edytor konfiguracji**. Użyj kontrolek nawigacji, aby wybrać `system.webServer/modules` sekcję. Na pasku bocznym **Akcje** po prawej stronie wybierz opcję **odblokowania** sekcji. Jeśli wpis akcji dla sekcji modułu jest wyświetlany jako **sekcja blokady**, sekcja modułu jest już odblokowana i nie jest wymagana żadna akcja.

4. Dodaj `<modules>` sekcję do lokalnego pliku *web.config* aplikacji z `<remove>` elementem, aby usunąć moduł z aplikacji. Dodaj wiele `<remove>` elementów, aby usunąć wiele modułów. Jeśli na serwerze wprowadzono zmiany *web.config* , natychmiast wprowadzić te same zmiany w pliku *web.config* projektu lokalnie. Usunięcie modułu korzystającego z tego podejścia nie ma wpływu na używanie modułu z innymi aplikacjami na serwerze.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Aby można było dodać lub usunąć moduły IIS Express przy użyciu *web.config*, zmodyfikuj *applicationHost.config* w celu odblokowania `<modules>` sekcji:

1. Otwórz *aplikację {Application root} \\.vs\config\applicationhost.config*.

1. Znajdź `<section>` element dla modułów usług IIS i przejdź `overrideModeDefault` `Deny` do `Allow` :

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. Znajdź sekcję `<location path="" overrideMode="Allow"><system.webServer><modules>`. Dla wszystkich modułów, które chcesz usunąć, ustaw wartość `lockItem` z `true` na `false` . W poniższym przykładzie moduł CGI jest odblokowany:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. Po `<modules>` odblokowaniu sekcji i poszczególnych modułów można dodać lub usunąć moduły usług IIS przy użyciu pliku *web.config* aplikacji do uruchamiania aplikacji na IIS Express.

Moduł IIS można także usunąć za pomocą *Appcmd.exe*. Podaj `MODULE_NAME` i `APPLICATION_NAME` w poleceniu:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Na przykład Usuń `DynamicCompressionModule` z domyślnej witryny sieci Web:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Minimalna konfiguracja modułu

Jedyne moduły wymagane do uruchomienia aplikacji ASP.NET Core są modułem uwierzytelniania anonimowego i modułem ASP.NET Core.

Moduł buforowania URI ( `UriCacheModule` ) umożliwia usługom IIS buforowanie konfiguracji witryny sieci Web na poziomie adresu URL. Bez tego modułu usługi IIS muszą odczytywać i analizować konfigurację dla każdego żądania, nawet jeśli ten sam adres URL jest wielokrotnie żądany. Analizowanie konfiguracji każde żądanie skutkuje znaczącą wydajnością. *Chociaż moduł buforowania URI nie jest ściśle wymagany do uruchomienia hostowanej aplikacji ASP.NET Core, zalecamy włączenie modułu buforowania URI dla wszystkich wdrożeń ASP.NET Core.*

Moduł buforowania HTTP ( `HttpCacheModule` ) implementuje wyjściową pamięć podręczną programu IIS, a także logikę dla elementów buforowania w pamięci podręcznej HTTP.sys. Bez tego modułu zawartość nie jest już buforowana w trybie jądra, a profile pamięci podręcznej są ignorowane. Usunięcie modułu buforowania HTTP zwykle ma niekorzystne skutki dla wydajności i użycia zasobów. *Chociaż moduł buforowania HTTP nie jest ściśle wymagany do uruchomienia hostowanej aplikacji ASP.NET Core, zalecamy włączenie modułu buforowania HTTP we wszystkich wdrożeniach ASP.NET Core.*

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wprowadzenie do architektury usług IIS: moduły w usługach IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Przegląd modułów usług IIS](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Dostosowywanie ról i modułów usług IIS 7,0](/previous-versions/tn-archive/cc627313(v=technet.10))
* [SERWERZE \<system.webServer>](/iis/configuration/system.webServer/)
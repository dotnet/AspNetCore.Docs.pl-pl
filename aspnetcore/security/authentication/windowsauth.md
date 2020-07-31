---
title: Konfigurowanie uwierzytelniania systemu Windows w ASP.NET Core
author: scottaddie
description: Dowiedz się, jak skonfigurować uwierzytelnianie systemu Windows w ASP.NET Core dla usług IIS i HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330690"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Konfigurowanie uwierzytelniania systemu Windows w ASP.NET Core

Przez [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Uwierzytelnianie systemu Windows (znane także jako uwierzytelnianie negocjowane, Kerberos lub NTLM) można skonfigurować dla ASP.NET Core aplikacji hostowanych za pomocą [usług IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)lub [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Uwierzytelnianie systemu Windows (znane także jako uwierzytelnianie Negocjuj, Kerberos lub NTLM) można skonfigurować dla ASP.NET Core aplikacji hostowanych za pomocą [usług IIS](xref:host-and-deploy/iis/index) lub [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

Uwierzytelnianie systemu Windows polega na użyciu systemu operacyjnego w celu uwierzytelniania użytkowników ASP.NET Core aplikacji. Możesz użyć uwierzytelniania systemu Windows, gdy serwer działa w sieci firmowej przy użyciu tożsamości domeny Active Directory lub kont systemu Windows w celu identyfikowania użytkowników. Uwierzytelnianie systemu Windows najlepiej nadaje się w środowiskach intranetowych, w których użytkownicy, aplikacje klienckie i serwery sieci Web należą do tej samej domeny systemu Windows.

> [!NOTE]
> Uwierzytelnianie systemu Windows nie jest obsługiwane w przypadku protokołu HTTP/2. Problemy z uwierzytelnianiem można wysyłać w odpowiedzi HTTP/2, ale klient programu musi obniżyć poziom protokołu HTTP/1.1 Przed uwierzytelnieniem.

## <a name="proxy-and-load-balancer-scenarios"></a>Scenariusze dotyczące serwerów proxy i równoważenia obciążenia

Uwierzytelnianie systemu Windows jest scenariuszem stanowym głównie używanym w intranecie, gdzie serwer proxy lub moduł równoważenia obciążenia zazwyczaj nie obsługuje ruchu między klientami i serwerami. Jeśli używany jest serwer proxy lub moduł równoważenia obciążenia, uwierzytelnianie systemu Windows działa tylko wtedy, gdy serwer proxy lub moduł równoważenia obciążenia:

* Obsługuje uwierzytelnianie.
* Przekazuje informacje o uwierzytelnianiu użytkownika do aplikacji (na przykład w nagłówku żądania), która działa na informacje o uwierzytelnianiu.

Alternatywą dla uwierzytelniania systemu Windows w środowiskach, w których są używane serwery proxy i moduły równoważenia obciążenia, Active Directory usług federacyjnych (AD FS) za pomocą OpenID Connect Connect (OIDC).

## <a name="iisiis-express"></a>Usługi IIS/IIS Express

Dodaj usługi uwierzytelniania, wywołując <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> przestrzeń nazw) w `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Ustawienia uruchamiania (debuger)

Konfiguracja ustawień uruchamiania ma wpływ tylko na *Właściwości/launchSettings.jsw* pliku dla IIS Express i nie KONFIGURUJE usług IIS do uwierzytelniania systemu Windows. Konfiguracja serwera została omówiona w sekcji [usług IIS](#iis) .

Szablon **aplikacji sieci Web** dostępny za pośrednictwem programu Visual Studio lub interfejs wiersza polecenia platformy .NET Core można skonfigurować do obsługi uwierzytelniania systemu Windows, który automatycznie aktualizuje *Właściwości/launchSettings.jsna* pliku.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

**Nowy projekt**

1. Tworzenie nowego projektu.
1. Wybierz **ASP.NET Core aplikacji sieci Web**. Wybierz pozycję **Dalej**.
1. Podaj nazwę w polu **Nazwa projektu** . Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz pozycję **Utwórz**.
1. Wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.
1. W oknie **Zmienianie uwierzytelniania** wybierz pozycję **uwierzytelnianie systemu Windows**. Wybierz przycisk **OK**.
1. Wybierz pozycję **aplikacja sieci Web**.
1. Wybierz pozycję **Utwórz**.

Uruchom aplikację. Nazwa użytkownika jest wyświetlana w interfejsie użytkownika renderowanej aplikacji.

**Istniejący projekt**

Właściwości projektu umożliwiają uwierzytelnianie systemu Windows i Wyłącz uwierzytelnianie anonimowe:

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Właściwości**.
1. Wybierz kartę **debugowanie** .
1. Usuń zaznaczenie pola wyboru **Włącz uwierzytelnianie anonimowe**.
1. Zaznacz pole wyboru **Włącz uwierzytelnianie systemu Windows**.
1. Zapisz i zamknij stronę właściwości.

Alternatywnie można skonfigurować właściwości w `iisSettings` węźle *launchSettings.jsw* pliku:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

**Nowy projekt**

Wykonaj polecenie [dotnet New](/dotnet/core/tools/dotnet-new) przy użyciu `webapp` argumentu (ASP.NET Core aplikacji sieci Web) i `--auth Windows` Przełącz:

```dotnetcli
dotnet new webapp --auth Windows
```

**Istniejący projekt**

Aktualizuj `iisSettings` węzeł *launchSettings.jsw* pliku:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Podczas modyfikowania istniejącego projektu upewnij się, że plik projektu zawiera odwołanie do pakietu dla pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , **lub** pakiet NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .

### <a name="iis"></a>IIS

Usługi IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do hostowania aplikacji ASP.NET Core. Uwierzytelnianie systemu Windows jest konfigurowane dla usług IIS za pośrednictwem pliku *web.config* . W poniższych sekcjach pokazano, jak:

* Podaj plik *web.config* lokalnego, który aktywuje uwierzytelnianie systemu Windows na serwerze podczas wdrażania aplikacji.
* Za pomocą Menedżera usług IIS Skonfiguruj plik *web.config* aplikacji ASP.NET Core, która została już wdrożona na serwerze.

Jeśli jeszcze tego nie zrobiono, Włącz usługi IIS do hostowania aplikacji ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/index>.

Włącz usługę roli IIS na potrzeby uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania systemu Windows w usługach ról usług IIS (zobacz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).

[Oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) jest skonfigurowane do automatycznego uwierzytelniania żądań domyślnie. Aby uzyskać więcej informacji, zobacz [hostowanie ASP.NET Core w systemie Windows za pomocą usług IIS: opcje usług IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

Moduł ASP.NET Core jest skonfigurowany do domyślnego przesyłania dalej tokenu uwierzytelniania systemu Windows do aplikacji. Aby uzyskać więcej informacji, zobacz [ASP.NET Core informacje o konfiguracji modułu: atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Użyj **jednej** z następujących metod:

* **Przed opublikowaniem i wdrożeniem projektu** Dodaj następujący plik *web.config* do katalogu głównego projektu:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Gdy projekt jest publikowany przez zestaw .NET Core SDK (bez `<IsTransformWebConfigDisabled>` Właściwości ustawionej `true` w pliku projektu), publikowany plik *web.config* zawiera `<location><system.webServer><security><authentication>` sekcję. Aby uzyskać więcej informacji na temat `<IsTransformWebConfigDisabled>` właściwości, zobacz <xref:host-and-deploy/iis/index#webconfig-file> .

* **Po opublikowaniu i wdrożeniu projektu** wykonaj konfigurację po stronie serwera za pomocą Menedżera usług IIS:

  1. W Menedżerze usług IIS wybierz lokację usług IIS w węźle **Lokacje** na pasku bocznym **połączenia** .
  1. Kliknij dwukrotnie pozycję **uwierzytelnianie** w obszarze **usługi IIS** .
  1. Wybierz opcję **uwierzytelnianie anonimowe**. Na pasku bocznym **Akcje** wybierz pozycję **Wyłącz** .
  1. Wybierz pozycję **uwierzytelnianie systemu Windows**. Na pasku bocznym **Akcje** wybierz pozycję **Włącz** .

  Po wykonaniu tych działań Menedżer usług IIS modyfikuje plik *web.config* aplikacji. `<system.webServer><security><authentication>`Dodano węzeł z zaktualizowanymi ustawieniami dla `anonymousAuthentication` i `windowsAuthentication` :

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>`Sekcja dodana do pliku *web.config* przez Menedżera usług IIS znajduje się poza `<location>` sekcją aplikacji dodanej przez zestaw .NET Core SDK po opublikowaniu aplikacji. Ponieważ sekcja została dodana poza `<location>` węzłem, ustawienia są dziedziczone przez wszystkie [aplikacje podrzędne](xref:host-and-deploy/iis/index#sub-applications) do bieżącej aplikacji. Aby zapobiec dziedziczeniu, Przenieś dodaną `<security>` sekcję wewnątrz `<location><system.webServer>` sekcji dostarczonej przez zestaw .NET Core SDK.

  Gdy Menedżer usług IIS jest używany do dodawania konfiguracji usług IIS, ma wpływ tylko na plik *web.config* aplikacji na serwerze. Kolejne wdrożenie aplikacji może spowodować zastąpienie ustawień na serwerze, jeśli kopia *web.config* serwera jest zastępowana przez plik *web.config* projektu. Użyj **jednego** z poniższych metod, aby zarządzać ustawieniami:

  * Za pomocą Menedżera usług IIS Zresetuj ustawienia w pliku *web.config* po zastępowaniu pliku we wdrożeniu.
  * Dodaj *plikweb.config* do aplikacji lokalnie przy użyciu ustawień.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

Pakiet NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) może być używany z [Kestrel](xref:fundamentals/servers/kestrel) do obsługi uwierzytelniania systemu Windows przy użyciu protokołu Negotiate i Kerberos w systemach Windows, Linux i macOS.

> [!WARNING]
> Poświadczenia mogą być utrwalane między żądaniami w ramach połączenia. *Uwierzytelnianie negocjowane nie może być używane z serwerami proxy, chyba że serwer proxy utrzymuje koligację połączenia 1:1 (połączenie trwałe) z Kestrel.*

> [!NOTE]
> Procedura obsługi negocjuje wykrywa, czy podstawowy serwer obsługuje uwierzytelnianie systemu Windows w sposób natywny, a jeśli jest włączony. Jeśli serwer obsługuje uwierzytelnianie systemu Windows, ale jest wyłączony, zostanie zgłoszony błąd z pytaniem, czy należy włączyć implementację serwera. Gdy na serwerze jest włączone uwierzytelnianie systemu Windows, program obsługi negocjuje w sposób niewidoczny do przodu.

Dodawanie usług uwierzytelniania przez wywoływanie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> i <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> w `Startup.ConfigureServices` :

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Dodaj oprogramowanie pośredniczące uwierzytelniania przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> w `Startup.Configure` :

 ```csharp
app.UseAuthentication();
```

Aby uzyskać więcej informacji na temat oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .

Dozwolone są żądania anonimowe. Użyj [autoryzacji ASP.NET Core](xref:security/authorization/introduction) , aby zażądać anonimowych żądań uwierzytelniania.

### <a name="windows-environment-configuration"></a>Konfiguracja środowiska systemu Windows

Składnik [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) wykonuje uwierzytelnianie w trybie użytkownika. Nazwy główne usług (SPN) należy dodać do konta użytkownika, na którym działa usługa, a nie konta komputera. Wykonaj `setspn -S HTTP/myservername.mydomain.com myuser` w powłoce poleceń administracyjnych.

### <a name="linux-and-macos-environment-configuration"></a>Konfiguracja środowiska Linux i macOS

Instrukcje dotyczące przyłączania maszyny z systemem Linux lub macOS do domeny systemu Windows są dostępne w [Azure Data Studio połączenia SQL Server przy użyciu uwierzytelniania systemu Windows — artykuł Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) . Instrukcje tworzą konto komputera dla komputera z systemem Linux w domenie. Nazwy SPN należy dodać do tego konta komputera.

> [!NOTE]
> Po [zastosowaniu wskazówek w Azure Data Studio połącz SQL Server przy użyciu uwierzytelniania systemu Windows — artykuł Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , Zastąp `python-software-properties` w `python3-software-properties` razie potrzeby.

Po przyłączeniu maszyny z systemem Linux lub macOS do domeny należy wykonać dodatkowe czynności, aby dostarczyć [plik plik KEYTAB](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) z nazwami SPN:

* Na kontrolerze domeny Dodaj nowe nazwy SPN usługi sieci Web do konta komputera:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* Użyj [ktpass](/windows-server/administration/windows-commands/ktpass) , aby wygenerować plik plik KEYTAB:
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Niektóre pola muszą być określone wielkimi literami, jak wskazano.
* Skopiuj plik plik KEYTAB na maszynę z systemem Linux lub macOS.
* Wybierz plik plik KEYTAB za pomocą zmiennej środowiskowej:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* Wywołaj `klist` , aby wyświetlić nazwy SPN obecnie dostępne do użycia.

> [!NOTE]
> Plik plik KEYTAB zawiera poświadczenia dostępu do domeny i musi być odpowiednio chroniony.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) obsługuje uwierzytelnianie systemu Windows trybu jądra przy użyciu protokołu negocjowania, NTLM lub uwierzytelniania podstawowego.

Dodaj usługi uwierzytelniania, wywołując <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> przestrzeń nazw) w `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Skonfiguruj hosta sieci Web aplikacji, aby używał HTTP.sys z uwierzytelnianiem systemu Windows (*program.cs*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>znajduje się w <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> przestrzeni nazw.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys delegatów do uwierzytelniania w trybie jądra przy użyciu protokołu uwierzytelniania Kerberos. Uwierzytelnianie w trybie użytkownika nie jest obsługiwane w przypadku używania protokołów Kerberos i HTTP.sys. Konto komputera musi służyć do odszyfrowywania tokenu lub biletu Kerberos uzyskanych z Active Directory i przesłanych przez klienta na serwer w celu uwierzytelnienia użytkownika. Zarejestruj główną nazwę usługi (SPN) dla hosta, a nie użytkownika aplikacji.

> [!NOTE]
> HTTP.sys nie jest obsługiwana w systemie nano Server w wersji 1709 lub nowszej. Aby użyć uwierzytelniania systemu Windows i HTTP.sys z serwerem nano Server, użyj [kontenera Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Aby uzyskać więcej informacji na temat serwera Server Core, zobacz [co to jest opcja instalacji Server Core w systemie Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autoryzuj użytkowników

Stan konfiguracji dostępu anonimowego określa sposób, w jaki `[Authorize]` `[AllowAnonymous]` atrybuty i są używane w aplikacji. W poniższych dwóch sekcjach opisano sposób obsługi niedozwolonych i dozwolonych Stanów konfiguracji dostępu anonimowego.

### <a name="disallow-anonymous-access"></a>Nie Zezwalaj na dostęp anonimowy

Gdy uwierzytelnianie systemu Windows jest włączone i dostęp anonimowy jest wyłączony, `[Authorize]` `[AllowAnonymous]` atrybuty i nie mają żadnego wpływu. Jeśli witryna IIS jest skonfigurowana tak, aby nie zezwalać na dostęp anonimowy, żądanie nigdy nie dociera do aplikacji. Z tego powodu `[AllowAnonymous]` atrybut nie ma zastosowania.

### <a name="allow-anonymous-access"></a>Zezwalaj na dostęp anonimowy

W przypadku włączenia uwierzytelniania systemu Windows i dostępu anonimowego należy użyć `[Authorize]` `[AllowAnonymous]` atrybutów i. Ten `[Authorize]` atrybut umożliwia zabezpieczenie punktów końcowych aplikacji, które wymagają uwierzytelniania. Ten `[AllowAnonymous]` atrybut zastępuje `[Authorize]` atrybut w aplikacjach, które zezwalają na dostęp anonimowy. Aby uzyskać szczegółowe informacje dotyczące użycia atrybutów, zobacz <xref:security/authorization/simple> .

> [!NOTE]
> Domyślnie użytkownicy, którzy nie posiadają autoryzacji dostępu do strony, są wyświetlani z pustą odpowiedzią HTTP 403. [Oprogramowanie pośredniczące StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) można skonfigurować w celu zapewnienia użytkownikom lepszego środowiska "odmowa dostępu".

## <a name="impersonation"></a>Personifikacja

ASP.NET Core nie implementuje personifikacji. Aplikacje są uruchamiane z tożsamością aplikacji dla wszystkich żądań, przy użyciu puli aplikacji lub tożsamości procesu. Jeśli aplikacja powinna wykonać akcję w imieniu użytkownika, należy użyć [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) w ramach [wbudowanego oprogramowania terminala](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) w programie `Startup.Configure` . Uruchom pojedynczą akcję w tym kontekście, a następnie zamknij kontekst.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`nie obsługuje operacji asynchronicznych i nie powinna być używana w scenariuszach złożonych. Na przykład Zawijanie całych żądań lub łańcuchów oprogramowania pośredniczącego nie jest obsługiwane ani zalecane.

::: moniker range=">= aspnetcore-3.0"

Podczas gdy pakiet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) umożliwia uwierzytelnianie w systemach Windows, Linux i macOS, personifikacja jest obsługiwana tylko w systemie Windows.

::: moniker-end

## <a name="claims-transformations"></a>Przekształcenia oświadczeń

::: moniker range=">= aspnetcore-3.0"

W przypadku hostowania za pomocą usług IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika. W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana. Aby uzyskać więcej informacji i przykład kodu, który aktywuje przekształcenia oświadczeń, zobacz <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W przypadku hostowania z programem IIS w trybie przetwarzania <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika. W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana. Aby uzyskać więcej informacji i przykład kodu, który aktywuje przekształcenia oświadczeń podczas hostingu w procesie, zobacz <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

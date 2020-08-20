---
title: Uwierzytelnianie użytkowników za pomocą usługi WS-Federation w ASP.NET Core
author: chlowell
description: W tym samouczku pokazano, jak używać usługi WS-Federation w aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
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
uid: security/authentication/ws-federation
ms.openlocfilehash: 8a593efd799e900483d0337a06e02c3558b63bfb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634088"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Uwierzytelnianie użytkowników za pomocą usługi WS-Federation w ASP.NET Core

W tym samouczku pokazano, jak umożliwić użytkownikom logowanie się przy użyciu dostawcy uwierzytelniania WS-Federation, takiego jak Active Directory Federation Services (ADFS) lub [Azure Active Directory](/azure/active-directory/) (AAD). Używa ona przykładowej aplikacji ASP.NET Core opisanej w temacie [uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).

W przypadku aplikacji ASP.NET Core obsługa protokołu WS-Federation jest zapewniana przez [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Ten składnik jest przewoźny z [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) i udostępnia wiele elementów tego Mechanics. Składniki te są jednak różne na kilka ważnych sposobów.

Domyślnie nowe oprogramowanie pośredniczące:

* Nie zezwala na nieżądane nazwy logowania. Ta funkcja protokołu WS-Federation jest narażona na ataki XSRF. Można go jednak włączyć przy użyciu `AllowUnsolicitedLogins` opcji.
* Nie sprawdza każdego wpisu w formularzu dla wiadomości logowania. Tylko żądania do programu `CallbackPath` są sprawdzane pod kątem logowania. `CallbackPath` wartość domyślna to, `/signin-wsfed` ale można ją zmienić za pomocą dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) . Ta ścieżka może być współużytkowana z innymi dostawcami uwierzytelniania przez włączenie opcji [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .

## <a name="register-the-app-with-active-directory"></a>Zarejestruj aplikację w Active Directory

### <a name="active-directory-federation-services"></a>Usługi Active Directory Federation Services

* Otwórz **Kreatora dodawania zaufania jednostki uzależnionej** serwera z konsoli zarządzania usług AD FS:

![Kreator dodawania zaufania jednostki uzależnionej: Witamy](ws-federation/_static/AdfsAddTrust.png)

* Wybierz, aby ręcznie wprowadzić dane:

![Kreator dodawania zaufania jednostki uzależnionej: Wybieranie źródła danych](ws-federation/_static/AdfsSelectDataSource.png)

* Wprowadź nazwę wyświetlaną jednostki uzależnionej. Nazwa nie jest ważna dla aplikacji ASP.NET Core.

* [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) nie obsługuje szyfrowania tokenów, dlatego nie należy konfigurować certyfikatu szyfrowania tokenu:

![Kreator dodawania zaufania jednostki uzależnionej: Konfigurowanie certyfikatu](ws-federation/_static/AdfsConfigureCert.png)

* Włącz obsługę protokołu pasywnego usługi WS-Federation przy użyciu adresu URL aplikacji. Sprawdź, czy port jest prawidłowy dla aplikacji:

![Kreator dodawania zaufania jednostki uzależnionej: Konfigurowanie adresu URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Musi to być adres URL HTTPS. IIS Express może podać certyfikat z podpisem własnym podczas udostępniania aplikacji podczas opracowywania. Kestrel wymaga ręcznej konfiguracji certyfikatu. Aby uzyskać więcej informacji, zobacz [dokumentację Kestrel](xref:fundamentals/servers/kestrel) .

* Kliknij przycisk **dalej** w pozostałej części kreatora i **Zamknij** na końcu.

* ASP.NET Core Identity wymaga podania **identyfikatora nazwy** . Dodaj jedną z okna dialogowego **Edytowanie reguł dotyczących roszczeń** :

![Edytuj reguły dotyczące roszczeń](ws-federation/_static/EditClaimRules.png)

* W **Kreatorze dodawania reguły przekształcania oświadczeń**pozostaw zaznaczone pole wyboru domyślne **Wysyłaj atrybuty LDAP as** , a następnie kliknij przycisk **dalej**. Dodaj mapowanie reguły atrybut LDAP **nazwa konta sam** do żądania wychodzącego **Identyfikator nazwy** :

![Kreator dodawania reguły przekształcania roszczeń: Konfigurowanie reguły dotyczącej roszczeń](ws-federation/_static/AddTransformClaimRule.png)

* Kliknij przycisk **Zakończ**  >  **OK** w oknie **Edytowanie reguł roszczeń** .

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

* Przejdź do bloku rejestracje aplikacji dzierżawy usługi AAD. Kliknij pozycję **rejestracja nowej aplikacji**:

![Azure Active Directory: Rejestracje aplikacji](ws-federation/_static/AadNewAppRegistration.png)

* Wprowadź nazwę rejestracji aplikacji. Nie ma to znaczenia dla aplikacji ASP.NET Core.
* Wprowadź adres URL, na który aplikacja nasłuchuje jako **adres URL logowania**:

![Azure Active Directory: Utwórz rejestrację aplikacji](ws-federation/_static/AadCreateAppRegistration.png)

* Kliknij pozycję **punkty końcowe** i Zanotuj adres URL **dokumentu metadanych Federacji** . To jest oprogramowanie pośredniczące WS-Federation `MetadataAddress` :

![Azure Active Directory: punkty końcowe](ws-federation/_static/AadFederationMetadataDocument.png)

* Przejdź do rejestracji nowej aplikacji. Kliknij pozycję **uwidaczniaj interfejs API**. Kliknij przycisk identyfikatora aplikacji identyfikator URI **Ustawienia**  >  **Zapisz**. Zanotuj  **Identyfikator URI aplikacji**. To jest oprogramowanie pośredniczące WS-Federation `Wtrealm` :

![Azure Active Directory: właściwości rejestracji aplikacji](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-no-locaspnet-core-identity"></a>Korzystanie z protokołu WS-Federation bez ASP.NET Core Identity

Oprogramowanie pośredniczące WS-Federation może być używane bez programu Identity . Na przykład:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-no-locaspnet-core-identity"></a>Dodaj usługę WS-Federation jako zewnętrznego dostawcę logowania dla ASP.NET Core Identity

* Dodaj zależność od elementu [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) do projektu.
* Dodaj usługę WS-Federation do `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Logowanie za pomocą usługi WS-Federation

Przejdź do aplikacji, a następnie kliknij link **Zaloguj** w nagłówku nawigacji. Istnieje możliwość zalogowania się za pomocą WsFederation: ![ log na stronie](ws-federation/_static/WsFederationButton.png)

Za pomocą usług ADFS jako dostawca przycisk przekierowuje do strony logowania ADFS: ![ Strona logowania ADFS](ws-federation/_static/AdfsLoginPage.png)

Za pomocą Azure Active Directory jako dostawca przycisk przekierowuje do strony logowania do usługi AAD: ![ Strona logowania usługi AAD](ws-federation/_static/AadSignIn.png)

Pomyślne logowanie do nowego użytkownika przekierowuje do strony rejestracji użytkownika aplikacji: ![ Strona rejestr](ws-federation/_static/Register.png)

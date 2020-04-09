---
title: Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core
author: rick-anderson
description: Odnajdowanie obsługi debugowania aplikacji ASP.NET Core podczas pracy z usługami IIS w systemie Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: f87a1d8cf41248f14932908c0633f98a7198853f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664046"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core

Przez [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) do debugowania aplikacji ASP.NET Core uruchomionych z usługami IIS w systemie Windows Server. W tym temacie ominia się włączanie tego scenariusza i konfigurowanie projektu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio dla systemu Windows](https://visualstudio.microsoft.com/downloads/)
* **obciążenie ASP.NET i tworzeniem stron internetowych**
* **Wieloplatformowe obciążenie programistyczne .NET Core**
* Certyfikat zabezpieczeń X.509 (dla obsługi protokołu HTTPS)

## <a name="enable-iis"></a>Włączanie iis

1. W systemie Windows przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączaj lub wyłączaj funkcje systemu Windows** (po lewej stronie ekranu).
1. Zaznacz pole wyboru **Internetowe Usługi informacyjne.** Kliknij przycisk **OK**.

Instalacja usług IIS może wymagać ponownego uruchomienia systemu.

## <a name="configure-iis"></a>Konfigurowanie usług IIS

Serwis IIS musi mieć skonfigurowaną witrynę sieci Web z następującymi potrzebami:

* **Nazwa** &ndash; hosta Zazwyczaj **domyślna witryna sieci Web** jest używana z nazwą **hosta** `localhost`. Jednak każda prawidłowa witryna sieci Web iis z unikatową nazwą hosta działa.
* **Powiązanie witryny**
  * W przypadku aplikacji, które wymagają protokołu HTTPS, utwórz powiązanie z portem 443 z certyfikatem. Zazwyczaj używany jest **certyfikat dewelopera IIS Express,** ale każdy prawidłowy certyfikat działa.
  * W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie, aby opublikować 80 lub utworzyć powiązanie z portem 80 dla nowej witryny.
  * Użyj jednego powiązania dla http lub https. **Powiązanie z portami HTTP i HTTPS jednocześnie nie jest obsługiwane.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Włączanie obsługi iis w czasie programowania w programie Visual Studio

1. Uruchom instalator programu Visual Studio.
1. Wybierz **pozycję Modyfikuj** dla instalacji programu Visual Studio, która ma być używana dla obsługi czasu programowania usług IIS.
1. W przypadku obciążenia **ASP.NET i tworzenia sieci Web** zlokalizuj i zainstaluj składnik obsługi usług **IIS czasu programowania.**

   Składnik jest wymieniony w sekcji **Opcjonalne** w obszarze **Obsługa usług IIS czasu osiągnięć** w panelu **Szczegóły instalacji** po prawej stronie obciążeń. Składnik instaluje [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core z usługami IIS.

## <a name="configure-the-project"></a>Konfigurowanie projektu

### <a name="https-redirection"></a>Przekierowanie HTTPS

W przypadku nowego projektu wymagającego protokołu HTTPS zaznacz pole wyboru **Konfigurowanie protokołu HTTPS** w oknie **Utwórz nową ASP.NET podstawową aplikację sieci Web.** Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania HTTPS i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.

W przypadku istniejącego projektu wymagającego protokołu HTTPS należy użyć `Startup.Configure`programu HTTPS Redirection i oprogramowania pośredniczącego HSTS w programie . Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.

W przypadku projektu korzystającego z protokołu HTTP [przekierowanie HTTPS i oprogramowanie pośredniczące HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji. Nie jest wymagana żadna konfiguracja aplikacji.

### <a name="iis-launch-profile"></a>Profil uruchamiania usługi IIS

Utwórz nowy profil uruchamiania, aby dodać obsługę usługi IIS w czasie rozwoju:

1. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**. Wybierz **pozycję Właściwości**. Otwórz kartę **Debugowanie.**
1. W obszarze **Profil**wybierz przycisk **Nowy.** Nazwij profil "IIS" w oknie podręcznym. Wybierz **przycisk OK,** aby utworzyć profil.
1. W przypadku ustawienia **Uruchom** wybierz pozycję **IIS** z listy.
1. Zaznacz pole wyboru **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.

   Gdy aplikacja wymaga protokołu HTTPS, użyj`https://`punktu końcowego HTTPS ( ). W przypadku protokołu HTTP`http://`należy użyć punktu końcowego HTTP ( ).

   Podaj tę samą nazwę hosta i port, co `localhost` [konfiguracja usług IIS określona wcześniej używa](#configure-iis), zazwyczaj .

   Podaj nazwę aplikacji na końcu adresu URL.

   Na przykład `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` lub (HTTP) są prawidłowymi adresami URL punktów końcowych.
1. W sekcji **Zmienne środowiska** wybierz przycisk **Dodaj.** Podaj zmienną środowiskową **Value** o `Development` **nazwie** `ASPNETCORE_ENVIRONMENT` i wartości .
1. W obszarze **Ustawienia serwera sieci Web** ustaw adres URL **aplikacji** na tę samą wartość, która jest używana dla adresu URL punktu końcowego **przeglądarki Uruchamianie.**
1. Dla ustawienia **modelu hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **Domyślny,** aby użyć modelu hostingu używanego przez projekt. Jeśli projekt ustawia `<AspNetCoreHostingModel>` właściwość w pliku projektu, używana`InProcess` `OutOfProcess`jest wartość właściwości ( lub ). Jeśli właściwość nie jest obecny, domyślny model hostingu aplikacji jest używany, który jest w toku. Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu `In Process` `Out Of Process` aplikacji, ustaw **model hostingu** na jeden lub w razie potrzeby.
1. Zapisz profil.

Jeśli nie jest używany program Visual Studio, ręcznie dodać profil uruchamiania do pliku [launchSettings.json](https://json.schemastore.org/launchsettings) w folderze *Właściwości.* Poniższy przykład konfiguruje profil do używania protokołu HTTPS:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Upewnij się, że `applicationUrl` i `launchUrl` punktów końcowych zgodne i używać tego samego protokołu jako konfiguracji powiązania usług IIS, HTTP lub HTTPS.

## <a name="run-the-project"></a>Uruchamianie projektu

Uruchom program Visual Studio jako administrator:

* Upewnij się, że lista rozwijana konfiguracji kompilacji jest ustawiona na **Debugowanie**.
* Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profilu **IIS** i wybierz przycisk, aby uruchomić aplikację.

Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator. Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.

Jeśli używany jest niezaufany certyfikat dewelopera, przeglądarka może wymagać utworzenia wyjątku dla niezaufanego certyfikatu.

> [!NOTE]
> Debugowanie konfiguracji kompilacji wydania za pomocą [tylko mój kod](/visualstudio/debugger/just-my-code) i optymalizacje kompilatora powoduje pogorszenie środowiska. Na przykład break pointy nie są trafione.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wprowadzenie do menedżera usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) do debugowania aplikacji ASP.NET Core uruchomionych z usługami IIS w systemie Windows Server. W tym temacie ominia się włączanie tego scenariusza i konfigurowanie projektu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio dla systemu Windows](https://visualstudio.microsoft.com/downloads/)
* **obciążenie ASP.NET i tworzeniem stron internetowych**
* **Wieloplatformowe obciążenie programistyczne .NET Core**
* Certyfikat zabezpieczeń X.509 (dla obsługi protokołu HTTPS)

## <a name="enable-iis"></a>Włączanie iis

1. W systemie Windows przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączaj lub wyłączaj funkcje systemu Windows** (po lewej stronie ekranu).
1. Zaznacz pole wyboru **Internetowe Usługi informacyjne.** Kliknij przycisk **OK**.

Instalacja usług IIS może wymagać ponownego uruchomienia systemu.

## <a name="configure-iis"></a>Konfigurowanie usług IIS

Serwis IIS musi mieć skonfigurowaną witrynę sieci Web z następującymi potrzebami:

* **Nazwa** &ndash; hosta Zazwyczaj **domyślna witryna sieci Web** jest używana z nazwą **hosta** `localhost`. Jednak każda prawidłowa witryna sieci Web iis z unikatową nazwą hosta działa.
* **Powiązanie witryny**
  * W przypadku aplikacji, które wymagają protokołu HTTPS, utwórz powiązanie z portem 443 z certyfikatem. Zazwyczaj używany jest **certyfikat dewelopera IIS Express,** ale każdy prawidłowy certyfikat działa.
  * W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie, aby opublikować 80 lub utworzyć powiązanie z portem 80 dla nowej witryny.
  * Użyj jednego powiązania dla http lub https. **Powiązanie z portami HTTP i HTTPS jednocześnie nie jest obsługiwane.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Włączanie obsługi iis w czasie programowania w programie Visual Studio

1. Uruchom instalator programu Visual Studio.
1. Wybierz **pozycję Modyfikuj** dla instalacji programu Visual Studio, która ma być używana dla obsługi czasu programowania usług IIS.
1. W przypadku obciążenia **ASP.NET i tworzenia sieci Web** zlokalizuj i zainstaluj składnik obsługi usług **IIS czasu programowania.**

   Składnik jest wymieniony w sekcji **Opcjonalne** w obszarze **Obsługa usług IIS czasu osiągnięć** w panelu **Szczegóły instalacji** po prawej stronie obciążeń. Składnik instaluje [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core z usługami IIS.

## <a name="configure-the-project"></a>Konfigurowanie projektu

### <a name="https-redirection"></a>Przekierowanie HTTPS

W przypadku nowego projektu wymagającego protokołu HTTPS zaznacz pole wyboru **Konfigurowanie protokołu HTTPS** w oknie **Utwórz nową ASP.NET podstawową aplikację sieci Web.** Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania HTTPS i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.

W przypadku istniejącego projektu wymagającego protokołu HTTPS należy użyć `Startup.Configure`programu HTTPS Redirection i oprogramowania pośredniczącego HSTS w programie . Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.

W przypadku projektu korzystającego z protokołu HTTP [przekierowanie HTTPS i oprogramowanie pośredniczące HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji. Nie jest wymagana żadna konfiguracja aplikacji.

### <a name="iis-launch-profile"></a>Profil uruchamiania usługi IIS

Utwórz nowy profil uruchamiania, aby dodać obsługę usługi IIS w czasie rozwoju:

1. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**. Wybierz **pozycję Właściwości**. Otwórz kartę **Debugowanie.**
1. W obszarze **Profil**wybierz przycisk **Nowy.** Nazwij profil "IIS" w oknie podręcznym. Wybierz **przycisk OK,** aby utworzyć profil.
1. W przypadku ustawienia **Uruchom** wybierz pozycję **IIS** z listy.
1. Zaznacz pole wyboru **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.

   Gdy aplikacja wymaga protokołu HTTPS, użyj`https://`punktu końcowego HTTPS ( ). W przypadku protokołu HTTP`http://`należy użyć punktu końcowego HTTP ( ).

   Podaj tę samą nazwę hosta i port, co `localhost` [konfiguracja usług IIS określona wcześniej używa](#configure-iis), zazwyczaj .

   Podaj nazwę aplikacji na końcu adresu URL.

   Na przykład `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` lub (HTTP) są prawidłowymi adresami URL punktów końcowych.
1. W sekcji **Zmienne środowiska** wybierz przycisk **Dodaj.** Podaj zmienną środowiskową **Value** o `Development` **nazwie** `ASPNETCORE_ENVIRONMENT` i wartości .
1. W obszarze **Ustawienia serwera sieci Web** ustaw adres URL **aplikacji** na tę samą wartość, która jest używana dla adresu URL punktu końcowego **przeglądarki Uruchamianie.**
1. Dla ustawienia **modelu hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **Domyślny,** aby użyć modelu hostingu używanego przez projekt. Jeśli projekt ustawia `<AspNetCoreHostingModel>` właściwość w pliku projektu, używana`InProcess` `OutOfProcess`jest wartość właściwości ( lub ). Jeśli właściwość nie jest obecny, domyślny model hostingu aplikacji jest używany, który jest poza procesem. Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu `In Process` `Out Of Process` aplikacji, ustaw **model hostingu** na jeden lub w razie potrzeby.
1. Zapisz profil.

Jeśli nie jest używany program Visual Studio, ręcznie dodać profil uruchamiania do pliku [launchSettings.json](https://json.schemastore.org/launchsettings) w folderze *Właściwości.* Poniższy przykład konfiguruje profil do używania protokołu HTTPS:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Upewnij się, że `applicationUrl` i `launchUrl` punktów końcowych zgodne i używać tego samego protokołu jako konfiguracji powiązania usług IIS, HTTP lub HTTPS.

## <a name="run-the-project"></a>Uruchamianie projektu

Uruchom program Visual Studio jako administrator:

* Upewnij się, że lista rozwijana konfiguracji kompilacji jest ustawiona na **Debugowanie**.
* Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profilu **IIS** i wybierz przycisk, aby uruchomić aplikację.

Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator. Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.

Jeśli używany jest niezaufany certyfikat dewelopera, przeglądarka może wymagać utworzenia wyjątku dla niezaufanego certyfikatu.

> [!NOTE]
> Debugowanie konfiguracji kompilacji wydania za pomocą [tylko mój kod](/visualstudio/debugger/just-my-code) i optymalizacje kompilatora powoduje pogorszenie środowiska. Na przykład break pointy nie są trafione.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wprowadzenie do menedżera usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

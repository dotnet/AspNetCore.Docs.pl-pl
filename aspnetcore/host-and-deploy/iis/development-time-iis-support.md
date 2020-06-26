---
title: Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core
author: rick-anderson
description: Odkryj obsługę debugowania aplikacji ASP.NET Core podczas uruchamiania programu z usługami IIS w systemie Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: d922b2eb4d00a252b3e7da2f00de8b4571359b20
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406436"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core

Autor [sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) na potrzeby debugowania ASP.NET Core aplikacje działające z usługami IIS w systemie Windows Server. Ten temat zawiera instrukcje dotyczące włączania tego scenariusza i konfigurowania projektu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio dla systemu Windows](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET i programowanie aplikacji sieci Web**
* **Tworzenie aplikacji dla wielu platform w środowisku .NET Core**
* Certyfikat zabezpieczeń X. 509 (dla obsługi protokołu HTTPS)

## <a name="enable-iis"></a>Włącz usługi IIS

1. W systemie Windows przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).
1. Zaznacz pole wyboru **Internet Information Services** . Wybierz przycisk **OK**.

Instalacja usług IIS może wymagać ponownego uruchomienia systemu.

## <a name="configure-iis"></a>Konfigurowanie usług IIS

Usługi IIS muszą mieć skonfigurowaną witrynę sieci Web z następującymi:

* **Nazwa hosta**: zazwyczaj **Domyślna witryna sieci Web** jest używana z **nazwą hosta** `localhost` . Jednak każda prawidłowa witryna sieci Web usług IIS z unikatową nazwą hosta działa.
* **Powiązanie witryny**
  * W przypadku aplikacji wymagających protokołu HTTPS Utwórz powiązanie z portem 443 z certyfikatem. Zwykle używany jest **certyfikat deweloperski IIS Express** , ale każdy prawidłowy certyfikat działa.
  * W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie do opublikowania 80 lub utwórz powiązanie z portem 80 dla nowej lokacji.
  * Użyj pojedynczego powiązania dla protokołu HTTP lub HTTPS. **Powiązanie jednocześnie z portami HTTP i HTTPS nie jest obsługiwane.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Włączanie obsługi usług IIS w czasie opracowywania w programie Visual Studio

1. Uruchom Instalatora programu Visual Studio.
1. Wybierz pozycję **Modyfikuj** dla instalacji programu Visual Studio, która ma być używana na potrzeby obsługi czasu projektowania usług IIS.
1. W przypadku obciążeń **ASP.NET i sieci Web** Znajdź i Zainstaluj składnik **Obsługa usług IIS w czasie opracowywania** .

   Składnik jest wymieniony w sekcji **opcjonalne** w obszarze **czas projektowania obsługa usług IIS** w panelu **szczegóły instalacji** po prawej stronie obciążeń. Składnik instaluje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core za pomocą usług IIS.

## <a name="configure-the-project"></a>Konfigurowanie projektu

### <a name="https-redirection"></a>Przekierowanie HTTPS

Dla nowego projektu, który wymaga protokołu HTTPS, zaznacz pole wyboru w celu **skonfigurowania protokołu HTTPS** w oknie **Tworzenie nowej ASP.NET Core aplikacji sieci Web** . Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania https i HSTS oprogramowania](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.

W przypadku istniejącego projektu wymagającego protokołu HTTPS Użyj przekierowania HTTPS i oprogramowania pośredniczącego HSTS w `Startup.Configure` . Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.

W przypadku projektu korzystającego z protokołu HTTP, [przekierowania https i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji. Nie jest wymagana żadna konfiguracja aplikacji.

### <a name="iis-launch-profile"></a>Profil uruchamiania usług IIS

Utwórz nowy profil uruchamiania, aby dodać obsługę usług IIS w czasie projektowania:

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**. Wybierz pozycję **Właściwości**. Otwórz kartę **debugowanie** .
1. W obszarze **profil**wybierz przycisk **Nowy** . Nazwij profil "IIS" w oknie podręcznym. Wybierz **przycisk OK** , aby utworzyć profil.
1. Dla ustawienia **uruchamiania** wybierz z listy pozycję **IIS** .
1. Zaznacz pole wyboru dla opcji **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.

   Gdy aplikacja wymaga protokołu HTTPS, użyj punktu końcowego HTTPS ( `https://` ). W przypadku protokołu HTTP Użyj `http://` punktu końcowego http ().

   Podaj tę samą nazwę hosta i port, co [Konfiguracja usług IIS określona wcześniej](#configure-iis), zazwyczaj `localhost` .

   Podaj nazwę aplikacji na końcu adresu URL.

   Na przykład `https://localhost/WebApplication1` (https) lub `http://localhost/WebApplication1` (http) są prawidłowymi adresami URL punktów końcowych.
1. W sekcji **zmienne środowiskowe** wybierz przycisk **Dodaj** . Podaj zmienną środowiskową o **nazwie** `ASPNETCORE_ENVIRONMENT` i **wartości** `Development` .
1. W obszarze **Ustawienia serwera sieci Web** Ustaw **adres URL aplikacji** na wartość używaną dla adresu URL punktu końcowego **uruchamiania przeglądarki** .
1. W przypadku ustawienia **model hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **domyślne** , aby użyć modelu hostingu używanego przez projekt. Jeśli projekt ustawia `<AspNetCoreHostingModel>` Właściwość w pliku projektu, używana jest wartość właściwości ( `InProcess` lub `OutOfProcess` ). Jeśli właściwość nie jest obecna, używany jest domyślny model hostingu aplikacji, który jest w procesie. Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu aplikacji, należy ustawić **model hostingu** na albo `In Process` `Out Of Process` w razie potrzeby.
1. Zapisz profil.

Gdy nie korzystasz z programu Visual Studio, ręcznie Dodaj profil uruchamiania do [launchSettings.js](https://json.schemastore.org/launchsettings) w pliku w folderze *Właściwości* . Poniższy przykład konfiguruje profil do korzystania z protokołu HTTPS:

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

Upewnij się, `applicationUrl` że `launchUrl` punkty końcowe i używają tego samego protokołu, co Konfiguracja powiązania usług IIS, http lub https.

## <a name="run-the-project"></a>Uruchamianie projektu

Uruchom program Visual Studio jako administrator:

* Upewnij się, że na liście rozwijanej konfiguracja kompilacji jest ustawiona wartość **Debuguj**.
* Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profil **usług IIS** i wybierz przycisk, aby uruchomić aplikację.

Program Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator. Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.

Jeśli jest używany niezaufany certyfikat programistyczny, w przeglądarce może być konieczne utworzenie wyjątku dla certyfikatu niezaufanego.

> [!NOTE]
> Debugowanie konfiguracji kompilacji wydania z optymalizacjami [tylko mój kod](/visualstudio/debugger/just-my-code) i kompilatora skutkuje obniżeniem wydajności. Na przykład punkty przerwania nie trafią.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wprowadzenie z menedżerem usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) na potrzeby debugowania ASP.NET Core aplikacje działające z usługami IIS w systemie Windows Server. Ten temat zawiera instrukcje dotyczące włączania tego scenariusza i konfigurowania projektu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio dla systemu Windows](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET i programowanie aplikacji sieci Web**
* **Tworzenie aplikacji dla wielu platform w środowisku .NET Core**
* Certyfikat zabezpieczeń X. 509 (dla obsługi protokołu HTTPS)

## <a name="enable-iis"></a>Włącz usługi IIS

1. W systemie Windows przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).
1. Zaznacz pole wyboru **Internet Information Services** . Wybierz przycisk **OK**.

Instalacja usług IIS może wymagać ponownego uruchomienia systemu.

## <a name="configure-iis"></a>Konfigurowanie usług IIS

Usługi IIS muszą mieć skonfigurowaną witrynę sieci Web z następującymi:

* **Nazwa hosta**: zazwyczaj **Domyślna witryna sieci Web** jest używana z **nazwą hosta** `localhost` . Jednak każda prawidłowa witryna sieci Web usług IIS z unikatową nazwą hosta działa.
* **Powiązanie witryny**
  * W przypadku aplikacji wymagających protokołu HTTPS Utwórz powiązanie z portem 443 z certyfikatem. Zwykle używany jest **certyfikat deweloperski IIS Express** , ale każdy prawidłowy certyfikat działa.
  * W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie do opublikowania 80 lub utwórz powiązanie z portem 80 dla nowej lokacji.
  * Użyj pojedynczego powiązania dla protokołu HTTP lub HTTPS. **Powiązanie jednocześnie z portami HTTP i HTTPS nie jest obsługiwane.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Włączanie obsługi usług IIS w czasie opracowywania w programie Visual Studio

1. Uruchom Instalatora programu Visual Studio.
1. Wybierz pozycję **Modyfikuj** dla instalacji programu Visual Studio, która ma być używana na potrzeby obsługi czasu projektowania usług IIS.
1. W przypadku obciążeń **ASP.NET i sieci Web** Znajdź i Zainstaluj składnik **Obsługa usług IIS w czasie opracowywania** .

   Składnik jest wymieniony w sekcji **opcjonalne** w obszarze **czas projektowania obsługa usług IIS** w panelu **szczegóły instalacji** po prawej stronie obciążeń. Składnik instaluje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core za pomocą usług IIS.

## <a name="configure-the-project"></a>Konfigurowanie projektu

### <a name="https-redirection"></a>Przekierowanie HTTPS

Dla nowego projektu, który wymaga protokołu HTTPS, zaznacz pole wyboru w celu **skonfigurowania protokołu HTTPS** w oknie **Tworzenie nowej ASP.NET Core aplikacji sieci Web** . Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania https i HSTS oprogramowania](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.

W przypadku istniejącego projektu wymagającego protokołu HTTPS Użyj przekierowania HTTPS i oprogramowania pośredniczącego HSTS w `Startup.Configure` . Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.

W przypadku projektu korzystającego z protokołu HTTP, [przekierowania https i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji. Nie jest wymagana żadna konfiguracja aplikacji.

### <a name="iis-launch-profile"></a>Profil uruchamiania usług IIS

Utwórz nowy profil uruchamiania, aby dodać obsługę usług IIS w czasie projektowania:

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**. Wybierz pozycję **Właściwości**. Otwórz kartę **debugowanie** .
1. W obszarze **profil**wybierz przycisk **Nowy** . Nazwij profil "IIS" w oknie podręcznym. Wybierz **przycisk OK** , aby utworzyć profil.
1. Dla ustawienia **uruchamiania** wybierz z listy pozycję **IIS** .
1. Zaznacz pole wyboru dla opcji **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.

   Gdy aplikacja wymaga protokołu HTTPS, użyj punktu końcowego HTTPS ( `https://` ). W przypadku protokołu HTTP Użyj `http://` punktu końcowego http ().

   Podaj tę samą nazwę hosta i port, co [Konfiguracja usług IIS określona wcześniej](#configure-iis), zazwyczaj `localhost` .

   Podaj nazwę aplikacji na końcu adresu URL.

   Na przykład `https://localhost/WebApplication1` (https) lub `http://localhost/WebApplication1` (http) są prawidłowymi adresami URL punktów końcowych.
1. W sekcji **zmienne środowiskowe** wybierz przycisk **Dodaj** . Podaj zmienną środowiskową o **nazwie** `ASPNETCORE_ENVIRONMENT` i **wartości** `Development` .
1. W obszarze **Ustawienia serwera sieci Web** Ustaw **adres URL aplikacji** na wartość używaną dla adresu URL punktu końcowego **uruchamiania przeglądarki** .
1. W przypadku ustawienia **model hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **domyślne** , aby użyć modelu hostingu używanego przez projekt. Jeśli projekt ustawia `<AspNetCoreHostingModel>` Właściwość w pliku projektu, używana jest wartość właściwości ( `InProcess` lub `OutOfProcess` ). Jeśli właściwość nie jest obecna, używany jest domyślny model hostingu aplikacji, który jest poza procesem. Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu aplikacji, należy ustawić **model hostingu** na albo `In Process` `Out Of Process` w razie potrzeby.
1. Zapisz profil.

Gdy nie korzystasz z programu Visual Studio, ręcznie Dodaj profil uruchamiania do [launchSettings.js](https://json.schemastore.org/launchsettings) w pliku w folderze *Właściwości* . Poniższy przykład konfiguruje profil do korzystania z protokołu HTTPS:

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

Upewnij się, `applicationUrl` że `launchUrl` punkty końcowe i używają tego samego protokołu, co Konfiguracja powiązania usług IIS, http lub https.

## <a name="run-the-project"></a>Uruchamianie projektu

Uruchom program Visual Studio jako administrator:

* Upewnij się, że na liście rozwijanej konfiguracja kompilacji jest ustawiona wartość **Debuguj**.
* Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profil **usług IIS** i wybierz przycisk, aby uruchomić aplikację.

Program Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator. Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.

Jeśli jest używany niezaufany certyfikat programistyczny, w przeglądarce może być konieczne utworzenie wyjątku dla certyfikatu niezaufanego.

> [!NOTE]
> Debugowanie konfiguracji kompilacji wydania z optymalizacjami [tylko mój kod](/visualstudio/debugger/just-my-code) i kompilatora skutkuje obniżeniem wydajności. Na przykład punkty przerwania nie trafią.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wprowadzenie z menedżerem usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

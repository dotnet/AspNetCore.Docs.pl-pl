---
title: Włącz generowanie kodu QR dla aplikacji TOTP Authenticator w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak włączyć generowanie kodu QR dla aplikacji TOTP Authenticator, które działają przy użyciu uwierzytelniania dwuskładnikowego ASP.NET Core.
ms.author: riande
ms.date: 08/14/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 4ccfd83c273f7179ac26b075eb33f138e724b967
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019563"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="a1f96-103">Włącz generowanie kodu QR dla aplikacji TOTP Authenticator w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1f96-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="a1f96-104">Kody QR wymagają ASP.NET Core 2,0 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="a1f96-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="a1f96-105">ASP.NET Core jest dostarczany z obsługą aplikacji uwierzytelniania do indywidualnego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a1f96-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="a1f96-106">Uwierzytelnianie dwuskładnikowe (funkcji 2FA) przy użyciu algorytmu hasła opartego na czasie (TOTP) jest zalecanym rozwiązaniem dla funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="a1f96-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="a1f96-107">FUNKCJI 2FA przy użyciu TOTP jest preferowana dla SMS funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="a1f96-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="a1f96-108">Aplikacja Authenticator zawiera 6-8-cyfrowy kod, który użytkownicy muszą wprowadzić po potwierdzeniu nazwy użytkownika i hasła.</span><span class="sxs-lookup"><span data-stu-id="a1f96-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="a1f96-109">Zazwyczaj aplikacja uwierzytelniania jest instalowana na telefonie inteligentnym.</span><span class="sxs-lookup"><span data-stu-id="a1f96-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="a1f96-110">Szablony aplikacji sieci Web ASP.NET Core obsługują wystawców, ale nie zapewniają wsparcia dla generacji QRCode.</span><span class="sxs-lookup"><span data-stu-id="a1f96-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="a1f96-111">Generatory QRCode ułatwiają konfigurację funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="a1f96-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="a1f96-112">Ten dokument przeprowadzi Cię przez proces dodawania generowania [kodu QR](https://wikipedia.org/wiki/QR_code) do strony konfiguracji funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="a1f96-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="a1f96-113">Uwierzytelnianie dwuskładnikowe nie odbywa się przy użyciu dostawcy uwierzytelniania zewnętrznego, takiego jak [Google](xref:security/authentication/google-logins) lub [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="a1f96-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="a1f96-114">Logowania zewnętrzne są chronione przez każdy mechanizm, który zapewnia zewnętrzny dostawca logowania.</span><span class="sxs-lookup"><span data-stu-id="a1f96-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="a1f96-115">Rozważmy na przykład, że dostawca uwierzytelniania [firmy Microsoft](xref:security/authentication/microsoft-logins) wymaga klucza sprzętowego lub innego podejścia funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="a1f96-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="a1f96-116">Jeśli domyślne szablony wymuszają "Local" funkcji 2FA, użytkownicy będą musieli spełnić dwa podejścia funkcji 2FA, które nie są powszechnie używanym scenariuszem.</span><span class="sxs-lookup"><span data-stu-id="a1f96-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="a1f96-117">Dodawanie kodów QR do strony konfiguracji funkcji 2FA</span><span class="sxs-lookup"><span data-stu-id="a1f96-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="a1f96-118">Te instrukcje używają *qrcode.js* z https://davidshimjs.github.io/qrcodejs/ repozytorium.</span><span class="sxs-lookup"><span data-stu-id="a1f96-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="a1f96-119">Pobierz [qrcode.js bibliotekę JavaScript](https://davidshimjs.github.io/qrcodejs/) do `wwwroot\lib` folderu w projekcie.</span><span class="sxs-lookup"><span data-stu-id="a1f96-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="a1f96-120">Postępuj zgodnie z instrukcjami w obszarze [szkieletowym Identity ](xref:security/authentication/scaffold-identity) , aby wygenerować */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a1f96-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="a1f96-121">W */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml*odszukaj `Scripts` sekcję na końcu pliku:</span><span class="sxs-lookup"><span data-stu-id="a1f96-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="a1f96-122">W obszarze *Pages/Account/Manage/EnableAuthenticator. cshtml* ( Razor Pages) lub *widoków/Manage/EnableAuthenticator. cshtml* (MVC) odszukaj `Scripts` sekcję na końcu pliku:</span><span class="sxs-lookup"><span data-stu-id="a1f96-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="a1f96-123">Zaktualizuj `Scripts` sekcję, aby dodać odwołanie do `qrcodejs` dodanej biblioteki i wywołać metodę generowania kodu QR.</span><span class="sxs-lookup"><span data-stu-id="a1f96-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="a1f96-124">Powinien wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="a1f96-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="a1f96-125">Usuń akapit, który łączy się z tymi instrukcjami.</span><span class="sxs-lookup"><span data-stu-id="a1f96-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="a1f96-126">Uruchom aplikację i upewnij się, że możesz skanować kod QR i sprawdzać kod, który potwierdzi wystawca uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="a1f96-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="a1f96-127">Zmień nazwę lokacji w kodzie QR</span><span class="sxs-lookup"><span data-stu-id="a1f96-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a1f96-128">Nazwa witryny w kodzie QR jest pobierana z nazwy projektu wybranej podczas pierwszego tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="a1f96-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="a1f96-129">Możesz to zmienić, szukając `GenerateQrCodeUri(string email, string unformattedKey)` metody w */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1f96-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a1f96-130">Nazwa witryny w kodzie QR jest pobierana z nazwy projektu wybranej podczas pierwszego tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="a1f96-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="a1f96-131">Można to zmienić, szukając `GenerateQrCodeUri(string email, string unformattedKey)` metody w pliku *Pages/Account/Manage/EnableAuthenticator. cshtml. cs* ( Razor strony) lub w pliku *controllers/ManageController. cs* (MVC).</span><span class="sxs-lookup"><span data-stu-id="a1f96-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="a1f96-132">Kod domyślny z szablonu wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="a1f96-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="a1f96-133">Drugim parametrem w wywołaniu `string.Format` jest nazwa witryny, pobrana z nazwy rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="a1f96-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="a1f96-134">Można go zmienić na dowolną wartość, ale zawsze musi być zakodowany w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="a1f96-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="a1f96-135">Korzystanie z innej biblioteki kodu QR</span><span class="sxs-lookup"><span data-stu-id="a1f96-135">Using a different QR Code library</span></span>

<span data-ttu-id="a1f96-136">Bibliotekę kodu QR można zastąpić za pomocą preferowanej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="a1f96-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="a1f96-137">HTML zawiera element, `qrCode` do którego można umieścić kod QR przez dowolny mechanizm udostępniany przez bibliotekę.</span><span class="sxs-lookup"><span data-stu-id="a1f96-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="a1f96-138">Poprawnie sformatowany adres URL dla kodu QR jest dostępny w:</span><span class="sxs-lookup"><span data-stu-id="a1f96-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="a1f96-139">`AuthenticatorUri`Właściwość modelu.</span><span class="sxs-lookup"><span data-stu-id="a1f96-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="a1f96-140">`data-url`w `qrCodeData` elemencie.</span><span class="sxs-lookup"><span data-stu-id="a1f96-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="a1f96-141">Przechylenie czasu klienta i serwera TOTP</span><span class="sxs-lookup"><span data-stu-id="a1f96-141">TOTP client and server time skew</span></span>

<span data-ttu-id="a1f96-142">TOTP (oparte na czasie hasło jednorazowe) uwierzytelnianie zależy od zarówno urządzenia serwera, jak i uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a1f96-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="a1f96-143">Tokeny są ostatnie przez 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a1f96-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="a1f96-144">Jeśli logowanie TOTP funkcji 2FA kończy się niepowodzeniem, sprawdź, czy czas serwera jest dokładny i najlepiej zsynchronizowany z dokładną usługą NTP.</span><span class="sxs-lookup"><span data-stu-id="a1f96-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end

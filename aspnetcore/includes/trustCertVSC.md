---
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
ms.openlocfilehash: df4a9a7db8097ea765b2d0f404305b771f994ddf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551532"
---
* <span data-ttu-id="f7a3f-101">Aby ufać certyfikatowi programistycznemu HTTPS, należy uruchomić następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f7a3f-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="f7a3f-102">Poprzednie polecenie nie działa w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="f7a3f-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="f7a3f-103">Zapoznaj się z dokumentacją dystrybucji systemu Linux w celu zaufać certyfikatowi.</span><span class="sxs-lookup"><span data-stu-id="f7a3f-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="f7a3f-104">Poprzednie polecenie wyświetla następujące okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="f7a3f-104">The preceding command displays the following dialog:</span></span>

  ![Okno dialogowe ostrzeżenia o zabezpieczeniach](~/getting-started/_static/cert.png)

* <span data-ttu-id="f7a3f-106">Wybierz opcję **tak** , jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="f7a3f-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="f7a3f-107">Aby uzyskać więcej informacji, zobacz artykuł [Ufaj certyfikatowi deweloperskim protokołu HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="f7a3f-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]
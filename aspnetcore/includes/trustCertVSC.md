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
* Aby ufać certyfikatowi programistycznemu HTTPS, należy uruchomić następujące polecenie:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Poprzednie polecenie nie działa w systemie Linux. Zapoznaj się z dokumentacją dystrybucji systemu Linux w celu zaufać certyfikatowi.

  Poprzednie polecenie wyświetla następujące okno dialogowe:

  ![Okno dialogowe ostrzeżenia o zabezpieczeniach](~/getting-started/_static/cert.png)

* Wybierz opcję **tak** , jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.

  Aby uzyskać więcej informacji, zobacz artykuł [Ufaj certyfikatowi deweloperskim protokołu HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]
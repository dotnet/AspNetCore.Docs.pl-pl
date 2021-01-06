* <span data-ttu-id="42861-101">Aby ufać certyfikatowi programistycznemu HTTPS, należy uruchomić następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="42861-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="42861-102">Poprzednie polecenie nie działa w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="42861-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="42861-103">Zapoznaj się z dokumentacją dystrybucji systemu Linux w celu zaufać certyfikatowi.</span><span class="sxs-lookup"><span data-stu-id="42861-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="42861-104">Poprzednie polecenie wyświetla następujące okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="42861-104">The preceding command displays the following dialog:</span></span>

  ![Okno dialogowe ostrzeżenia o zabezpieczeniach](~/getting-started/_static/cert.png)

* <span data-ttu-id="42861-106">Wybierz opcję **tak** , jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="42861-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="42861-107">Aby uzyskać więcej informacji, zobacz artykuł [Ufaj certyfikatowi deweloperskim protokołu HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="42861-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]
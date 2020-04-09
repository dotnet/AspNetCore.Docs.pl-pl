* <span data-ttu-id="25d8b-101">Zaufaj certyfikatowi programistycznego HTTPS, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="25d8b-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="25d8b-102">Poprzednie polecenie nie działa w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="25d8b-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="25d8b-103">Zapoznaj się z dokumentacją dystrybucji systemu Linux, aby uzyskać zaufanie do certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="25d8b-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="25d8b-104">Poprzednie polecenie wyświetla następujące okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="25d8b-104">The preceding command displays the following dialog:</span></span>

  ![Okno dialogowe z ostrzeżeniem o zabezpieczeniach](~/getting-started/_static/cert.png)

* <span data-ttu-id="25d8b-106">Wybierz **opcję Tak,** jeśli zgadzasz się ufać certyfikatowi dewelopera.</span><span class="sxs-lookup"><span data-stu-id="25d8b-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="25d8b-107">Aby uzyskać więcej informacji, zobacz [Ufanie ASP.NET podstawowym certyfikatem https.](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="25d8b-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  

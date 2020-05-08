* Aby ufać certyfikatowi programistycznemu HTTPS, należy uruchomić następujące polecenie:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Poprzednie polecenie nie działa w systemie Linux. Zapoznaj się z dokumentacją dystrybucji systemu Linux w celu zaufać certyfikatowi.

  Poprzednie polecenie wyświetla następujące okno dialogowe:

  ![Okno dialogowe ostrzeżenia o zabezpieczeniach](~/getting-started/_static/cert.png)

* Wybierz opcję **tak** , jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.

  Aby uzyskać więcej informacji, zobacz artykuł [Ufaj certyfikatowi deweloperskim protokołu HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .
  

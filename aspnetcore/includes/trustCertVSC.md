* Zaufaj certyfikatowi programistycznego HTTPS, uruchamiając następujące polecenie:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Poprzednie polecenie nie działa w systemie Linux. Zapoznaj się z dokumentacją dystrybucji systemu Linux, aby uzyskać zaufanie do certyfikatu.

  Poprzednie polecenie wyświetla następujące okno dialogowe:

  ![Okno dialogowe z ostrzeżeniem o zabezpieczeniach](~/getting-started/_static/cert.png)

* Wybierz **opcję Tak,** jeśli zgadzasz się ufać certyfikatowi dewelopera.

  Aby uzyskać więcej informacji, zobacz [Ufanie ASP.NET podstawowym certyfikatem https.](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)
  

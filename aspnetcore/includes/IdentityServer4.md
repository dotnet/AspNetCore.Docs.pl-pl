ASP.NET Core tożsamość dodaje funkcję logowania interfejsu użytkownika do ASP.NET Core aplikacji sieci Web. Aby zabezpieczyć interfejsy API sieci Web i aplikacji jednostronicowych, użyj jednego z następujących elementów:

* [Azure Active Directory](/azure/api-management/api-management-howto-protect-backend-with-aad)
* [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-custom-rest-api-netfw) (Azure AD B2C)
* [Usługi identityserver4](https://identityserver.io)

Usługi identityserver4 to struktura OpenID Connect Connect i OAuth 2,0 dla ASP.NET Core 3,0. Usługi identityserver4 włącza następujące funkcje zabezpieczeń:

* Uwierzytelnianie jako usługa (AaaS)
* Logowanie jednokrotne (SSO) dla wielu typów aplikacji
* Kontrola dostępu do interfejsów API
* Brama federacyjna

Aby uzyskać więcej informacji, zobacz [Welcome to usługi identityserver4](http://docs.identityserver.io/en/latest/index.html).

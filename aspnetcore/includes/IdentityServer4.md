ASP.NET Core Identity dodaje funkcję logowania interfejsu użytkownika (UI) do aplikacji sieci web ASP.NET Core. Aby zabezpieczyć interfejsy API sieci Web i oso, należy użyć jednej z następujących czynności:

* [Azure Active Directory](/azure/api-management/api-management-howto-protect-backend-with-aad)
* [Usługa Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-custom-rest-api-netfw) (Azure AD B2C)]
* [Serwer Tożsamości4](https://identityserver.io)

IdentityServer4 to rama OpenID Connect i OAuth 2.0 dla ASP.NET Core 3.0. IdentityServer4 włącza następujące funkcje zabezpieczeń:

* Uwierzytelnianie jako usługa (AaaS)
* Logowanie jednokrotne (Logowanie jednokrotne) w wielu typach aplikacji
* Kontrola dostępu dla interfejsów API
* Brama federacji

Aby uzyskać więcej informacji, zobacz [Witamy w IdentityServer4](http://docs.identityserver.io/en/latest/index.html).
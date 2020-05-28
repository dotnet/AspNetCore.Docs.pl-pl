# <a name="key-vault-configuration-provider-sample-app"></a>Przykładowa aplikacja dostawcy konfiguracji Key Vault

Ten przykład ilustruje użycie dostawcy konfiguracji Azure Key Vault.

Przykład jest uruchamiany w jednym z dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* . Aby uzyskać instrukcje, zobacz [dyrektywy preprocesora w przykładowym kodzie](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault. Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.
* `Managed`: Pokazuje, jak używać [tożsamość usługi zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) platformy Azure do uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń w kodzie lub konfiguracji aplikacji. Identyfikator klienta i wpis tajny usługi Azure AD nie są wymagane w celu uwierzytelnienia aplikacji za pomocą Azure Key Vault. Ten przykład musi zostać wdrożony w Azure App Service, aby poznać zarządzaną tożsamość scearnio.

Aby uzyskać więcej informacji, zobacz [Azure Key Vault Provider Configuration](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).

<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Wyłącz weryfikację konta domyślnego

W przypadku szablonów domyślnych użytkownik zostanie przekierowany do lokalizacji, w `Account.RegisterConfirmation` której można wybrać link, aby potwierdzić to konto. Wartość domyślna `Account.RegisterConfirmation` jest używana ***tylko*** do testowania, dlatego Automatyczna weryfikacja konta powinna być wyłączona w aplikacji produkcyjnej.

Aby wymagać potwierdzonego konta i uniemożliwić natychmiastowe Logowanie przy rejestracji, ustaw wartość `DisplayConfirmAccountLink = false` w */Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]
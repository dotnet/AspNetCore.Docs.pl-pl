## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>Przekazywanie informacji o żądaniu za pomocą serwera proxy lub modułu równoważenia obciążenia

Jeśli aplikacja jest wdrażana za serwerem proxy lub modułem równoważenia obciążenia, niektóre z oryginalnych informacji o żądaniu mogą być przekazywane do aplikacji w nagłówkach żądań. Te informacje zwykle obejmują schemat`https`bezpiecznego żądania ( ), hosta i adres IP klienta. Aplikacje nie odczytują automatycznie tych nagłówków żądań, aby odnajdować i używać oryginalnych informacji o żądaniu.

Schemat jest używany w generowaniu łączy, który wpływa na przepływ uwierzytelniania z dostawcami zewnętrznymi. Utrata bezpiecznego schematu (`https`) powoduje, że aplikacja generuje niepoprawne niezabezpieczone adresy URL przekierowania.

Użyj oprogramowania pośredniczącego nagłówków przesyłanych dalej, aby udostępnić oryginalną informację o żądaniu aplikacji do przetwarzania żądań.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

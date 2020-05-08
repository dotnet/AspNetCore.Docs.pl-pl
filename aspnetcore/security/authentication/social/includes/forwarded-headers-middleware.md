## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>Przekazywanie informacji o żądaniu za pomocą serwera proxy lub modułu równoważenia obciążenia

Jeśli aplikacja jest wdrażana za serwerem proxy lub modułem równoważenia obciążenia, niektóre z oryginalnych informacji o żądaniu mogą zostać przekazane do aplikacji w nagłówkach żądania. Te informacje zazwyczaj obejmują schemat bezpiecznego żądania (`https`), hosta i adres IP klienta. Aplikacje nie odczytuje automatycznie tych nagłówków żądań w celu odnalezienia i użycia oryginalnych informacji o żądaniu.

Schemat jest używany w generacji linków, który ma wpływ na przepływ uwierzytelniania z dostawcami zewnętrznymi. Utrata bezpiecznego schematu (`https`) powoduje generowanie nieprawidłowych niezabezpieczonych adresów URL przekierowań.

Używaj przekierowanych nagłówków pośredniczących, aby udostępnić oryginalne informacje o żądaniu dla aplikacji na potrzeby przetwarzania żądań.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

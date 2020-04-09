# <a name="blazor-webassembly-sample-app"></a>Przykładowa aplikacja Blazor Webassembly

Ten przykład ilustruje wykorzystanie scenariuszy Blazora opisanych w dokumentacji Blazora.

## <a name="call-web-api-example"></a>Przykład interfejsu API sieci Web

Przykład interfejsu API sieci web wymaga uruchomionego interfejsu API sieci web opartego na przykładowej aplikacji dla <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">tematu Tworzenie interfejsu API sieci web z ASP.NET Core,</a> który domyślnie używa tego samego portu HTTPS (5001) co przykładowa aplikacja Blazor. Aby korzystać z obu aplikacji na tym samym komputerze w tym samym czasie, zmień port internetowego interfejsu API (na przykład użyj portu 10000). Przykładowa aplikacja wysuwa `https://localhost:10000/api/TodoItems`żądania do internetowego interfejsu API pod adresem . Jeśli używany jest inny adres internetowego interfejsu API, zaktualizuj stałą `ServiceEndpoint` wartość w bloku składnika `@code` Razor.</p>

Przykładowa aplikacja tworzy żądanie <a href="https://docs.microsoft.com/aspnet/core/security/cors">udostępniania zasobów między źródłami (CORS)</a> z `http://localhost:5000` lub `https://localhost:5001` do internetowego interfejsu API. Poświadczenia (pliki cookie/nagłówki autoryzacji) są dozwolone. Dodaj następującą konfigurację oprogramowania pośredniczącego `Startup.Configure` CORS do metody internetowego interfejsu API:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Dostosuj domeny i porty w `WithOrigins` razie potrzeby dla aplikacji Blazor.

Internetowy interfejs API jest skonfigurowany dla usługi CORS, aby zezwalać na autoryzację plików cookie/nagłówków i żądań z kodu klienta, ale internetowy interfejs API utworzony przez samouczek w rzeczywistości nie autoryzuje żądań. Zapoznaj się z <a href="https://docs.microsoft.com/aspnet/core/security/">artykułami ASP.NET core security i identity,</a> aby uzyskać wskazówki dotyczące implementacji.

# <a name="blazor-webassembly-sample-app"></a>Przykładowa aplikacja Blazor webassembly

Ten przykład ilustruje użycie scenariuszy Blazor opisanych w dokumentacji Blazor.

## <a name="call-web-api-example"></a>Przykład wywołania internetowego interfejsu API

Przykład internetowego interfejsu API wymaga działającego internetowego interfejsu API opartego na przykładowej aplikacji dotyczącej <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">tworzenia internetowego interfejsu API za pomocą ASP.NET Core</a> , który domyślnie używa tego samego portu HTTPS (5001) jako aplikacji przykładowej Blazor. Aby używać obu aplikacji na tym samym komputerze w tym samym czasie, zmień port internetowego interfejsu API (na przykład użyj portu 10000). Przykładowa aplikacja wysyła żądania do internetowego interfejsu API pod adresem `https://localhost:10000/api/TodoItems` . Jeśli używany jest inny adres internetowego interfejsu API, należy zaktualizować `ServiceEndpoint` wartość stałą w bloku składnika Razor `@code` .</p>

Przykładowa aplikacja wykonuje żądanie <a href="https://docs.microsoft.com/aspnet/core/security/cors">współużytkowania zasobów między źródłami (CORS)</a> z `http://localhost:5000` lub `https://localhost:5001` do internetowego interfejsu API. Poświadczenia (pliki cookie/nagłówki autoryzacji) są dozwolone. Dodaj poniższą konfigurację oprogramowania CORS do metody internetowego interfejsu API `Startup.Configure` :</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Dostosuj domeny i porty `WithOrigins` dla aplikacji Blazor.

Internetowy interfejs API jest skonfigurowany do obsługi mechanizmu CORS, aby zezwalać na pliki cookie/nagłówki i żądania autoryzacji z kodu klienta, ale internetowy interfejs API utworzony przez samouczek nie zezwala na rzeczywiste żądania. Zapoznaj się z <a href="https://docs.microsoft.com/aspnet/core/security/">artykułami dotyczącymi ASP.NET Core Security i Identity</a> , aby uzyskać wskazówki dotyczące implementacji.

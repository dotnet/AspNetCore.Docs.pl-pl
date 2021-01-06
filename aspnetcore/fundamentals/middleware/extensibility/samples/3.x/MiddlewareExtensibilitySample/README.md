# <a name="aspnet-core-middleware-extensibility-sample"></a>Przykład rozszerzalności oprogramowania pośredniczącego ASP.NET Core

Ten przykład pokazuje scenariusze opisane w ramach [aktywacji oprogramowania pośredniczącego opartego na fabryce w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).

Przykładowa aplikacja pokazuje oprogramowanie pośredniczące aktywowane przez:

* Konwencja. Aby uzyskać więcej informacji na temat konwencjonalnej aktywacji oprogramowania pośredniczącego, zobacz temat [oprogramowanie pośredniczące](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) .
* Implementacja [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) . Domyślna Klasa [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) aktywuje oprogramowanie pośredniczące.

Implementacje oprogramowania pośredniczącego są identyczne i zapisują wartość podaną przez parametr ciągu zapytania ( `key` ). Middlewares używają wstrzykniętego kontekstu bazy danych (usługi w zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.

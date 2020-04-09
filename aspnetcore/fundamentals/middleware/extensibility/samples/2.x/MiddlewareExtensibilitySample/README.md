# <a name="aspnet-core-middleware-extensibility-sample"></a>ASP.NET Core Middleware Extensibility Sample

W tym przykładzie przedstawiono scenariusze opisane w [aktywacji oprogramowania pośredniczącego opartego na fabryce w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).

Przykładowa aplikacja pokazuje oprogramowanie pośredniczące aktywowane przez:

* Konwencji. Aby uzyskać więcej informacji na temat aktywacji konwencjonalnego oprogramowania pośredniczącego, zobacz temat [oprogramowania pośredniczącego.](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/)
* Implementacja [iMiddleware.](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) Domyślna klasa [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) aktywuje oprogramowanie pośredniczące.

Implementacje oprogramowania pośredniczącego działają identycznie i rejestrują wartość dostarczoną przez parametr ciągu zapytania (`key`). Oprogramowanie pośredniczące używa kontekstu wstrzykniętej bazy danych (usługi o określonym zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.

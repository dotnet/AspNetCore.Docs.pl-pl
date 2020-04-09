## <a name="debug-diagnostics"></a>Diagnostyka debugowania

Aby uzyskać szczegółowe dane `Logging:LogLevel:Microsoft` wyjściowe diagnostyczne routingu, ustaw wartość `Debug`. Na przykład w środowisku programistycznym ustaw *ustawienia aplikacji. Development.json*:

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
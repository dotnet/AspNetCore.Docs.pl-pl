## <a name="debug-diagnostics"></a>Diagnostyka debugowania

W celu uzyskania szczegółowych danych wyjściowych diagnostycznych routingu ustaw wartość `Logging:LogLevel:Microsoft` `Debug` . W środowisku programistycznym Ustaw poziom rejestrowania w *appsettings.Development.jsna*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

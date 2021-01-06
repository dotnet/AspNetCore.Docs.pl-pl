## <a name="debug-diagnostics"></a><span data-ttu-id="06ce5-101">Diagnostyka debugowania</span><span class="sxs-lookup"><span data-stu-id="06ce5-101">Debug diagnostics</span></span>

<span data-ttu-id="06ce5-102">W celu uzyskania szczegółowych danych wyjściowych diagnostycznych routingu ustaw wartość `Logging:LogLevel:Microsoft` `Debug` .</span><span class="sxs-lookup"><span data-stu-id="06ce5-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="06ce5-103">W środowisku programistycznym Ustaw poziom rejestrowania w *appsettings.Development.jsna*:</span><span class="sxs-lookup"><span data-stu-id="06ce5-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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

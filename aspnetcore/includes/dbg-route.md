## <a name="debug-diagnostics"></a><span data-ttu-id="44eb5-101">Diagnostyka debugowania</span><span class="sxs-lookup"><span data-stu-id="44eb5-101">Debug diagnostics</span></span>

<span data-ttu-id="44eb5-102">Aby uzyskać szczegółowe dane `Logging:LogLevel:Microsoft` wyjściowe diagnostyczne routingu, ustaw wartość `Debug`.</span><span class="sxs-lookup"><span data-stu-id="44eb5-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="44eb5-103">Na przykład w środowisku programistycznym ustaw *ustawienia aplikacji. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="44eb5-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

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
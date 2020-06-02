---
title: Migruj z Microsoft. Extensions. Logging 2,1 do 2,2 lub 3,0 Author: pakrym Description: informacje na temat migrowania aplikacji non-ASP.NET Core, która używa Microsoft. Extensions. rejestrowanie z 2,1 do 2,2 lub 3,0.
MS. Author: pakrym MS. Custom: MVC MS. Date: 01/04/2019 No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- " SignalR UID: Migration/Logging-nonaspnetcore

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrowanie z Microsoft. Extensions. Logging 2,1 do 2,2 lub 3,0

W tym artykule opisano typowe kroki migrowania aplikacji non-ASP.NET Core, która używa `Microsoft.Extensions.Logging` od 2,1 do 2,2 lub 3,0.

## <a name="21-to-22"></a>Z wersji 2.1 do 2.2

Ręcznie Utwórz `ServiceCollection` i Wywołaj `AddLogging` .

2,1 przykład:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2,2 przykład:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2,1 do 3,0

W 3,0 użyj `LoggingFactory.Create` .

2,1 przykład:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3,0 przykład:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Microsoft. Extensions. Logging. Console pakiet NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>

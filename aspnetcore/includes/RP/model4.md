<a name="codegenerator"></a>W poniższej tabeli opisano parametry generatora kodu ASP.NET Core:

| Parametr               | Opis|
| ----------------- | ------------ |
| -m  | Nazwa modelu. |
| -dc  | Klasa `DbContext` do użycia. |
| -udl | Użyj układu domyślnego. |
| -outDir | Ścieżka folderu danych wyjściowych w celu utworzenia widoków. |
| --referenceScriptBrabraries | Dodaje `_ValidationScriptsPartial` do stron Edycja i Tworzenie |

Użyj `h` przełącznika, aby `aspnet-codegenerator razorpage` uzyskać pomoc dotyczącą polecenia:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Aby uzyskać więcej informacji, zobacz [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Tworzenie nowego projektu.
1. Wybierz **pozycję Usługa pracownika**. Wybierz **pozycję Dalej**.
1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj domyślną nazwę projektu. Wybierz **pozycję Utwórz**.
1. W oknie **dialogowym Tworzenie nowej usługi pracownik** wybierz pozycję **Utwórz**.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Tworzenie nowego projektu.
1. Wybierz **pozycję Aplikacja** w obszarze **.NET Core** na pasku bocznym.
1. Wybierz **pozycję Pracownik** w obszarze ASP.NET **rdzeń**. Wybierz **pozycję Dalej**.
1. Wybierz **.NET Core 3.0** lub nowszą dla **struktury docelowej**. Wybierz **pozycję Dalej**.
1. Podaj nazwę w polu **Nazwa projektu.** Wybierz **pozycję Utwórz**.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Użyj szablonu`worker`Usługa robocza ( ) z nowym poleceniem [dotnet](/dotnet/core/tools/dotnet-new) z powłoki poleceń. W poniższym przykładzie zostanie utworzona `ContosoWorker`aplikacja Usługi procesu roboczego o nazwie . Folder `ContosoWorker` aplikacji jest tworzony automatycznie po wykonaniu polecenia.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---

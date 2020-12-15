Składniki zagnieżdżone zazwyczaj wiążą dane przy użyciu *powiązania łańcuchowego* , zgodnie z opisem w temacie <xref:blazor/components/data-binding> . Zagnieżdżone i niezagnieżdżone składniki mogą udostępniać dostęp do danych przy użyciu zarejestrowanego kontenera stanu w pamięci. Klasy kontenerów Stanów niestandardowych można użyć do przypisania, <xref:System.Action> aby powiadamiać składniki w różnych częściach aplikacji o zmianach stanu. W poniższym przykładzie:

* Para składników używa kontenera stanu do śledzenia właściwości.
* Składniki tego przykładu są zagnieżdżone, ale zagnieżdżanie nie jest wymagane do pracy.

`StateContainer.cs`:

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

W programie `Program.Main` (Blazor webassembly):

```csharp
builder.Services.AddSingleton<StateContainer>();
```

W programie `Startup.ConfigureServices` (Blazor Server):

```csharp
services.AddSingleton<StateContainer>();
```

`Pages/Component1.razor`:

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

`Shared/Component2.razor`:

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

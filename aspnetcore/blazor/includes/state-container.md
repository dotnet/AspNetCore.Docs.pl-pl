<span data-ttu-id="c6afe-101">Składniki zagnieżdżone zazwyczaj wiążą dane przy użyciu *powiązania łańcuchowego* , zgodnie z opisem w temacie <xref:blazor/components/data-binding> .</span><span class="sxs-lookup"><span data-stu-id="c6afe-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="c6afe-102">Zagnieżdżone i niezagnieżdżone składniki mogą udostępniać dostęp do danych przy użyciu zarejestrowanego kontenera stanu w pamięci.</span><span class="sxs-lookup"><span data-stu-id="c6afe-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="c6afe-103">Klasy kontenerów Stanów niestandardowych można użyć do przypisania, <xref:System.Action> aby powiadamiać składniki w różnych częściach aplikacji o zmianach stanu.</span><span class="sxs-lookup"><span data-stu-id="c6afe-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="c6afe-104">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="c6afe-104">In the following example:</span></span>

* <span data-ttu-id="c6afe-105">Para składników używa kontenera stanu do śledzenia właściwości.</span><span class="sxs-lookup"><span data-stu-id="c6afe-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="c6afe-106">Składniki tego przykładu są zagnieżdżone, ale zagnieżdżanie nie jest wymagane do pracy.</span><span class="sxs-lookup"><span data-stu-id="c6afe-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="c6afe-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="c6afe-107">`StateContainer.cs`:</span></span>

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

<span data-ttu-id="c6afe-108">W programie `Program.Main` (Blazor webassembly):</span><span class="sxs-lookup"><span data-stu-id="c6afe-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="c6afe-109">W programie `Startup.ConfigureServices` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="c6afe-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="c6afe-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="c6afe-110">`Pages/Component1.razor`:</span></span>

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

<span data-ttu-id="c6afe-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="c6afe-111">`Shared/Component2.razor`:</span></span>

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

<span data-ttu-id="a4276-101">Los componentes anidados normalmente enlazan datos mediante un *enlace encadenado*, tal y como se describe en <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="a4276-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="a4276-102">Los componentes anidados y no anidados pueden compartir el acceso a los datos mediante un contenedor registrado de estado en memoria.</span><span class="sxs-lookup"><span data-stu-id="a4276-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="a4276-103">Una clase de contenedor de estado personalizado puede usar un elemento <xref:System.Action> asignable para notificar cambios de estado a los componentes en diferentes partes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4276-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="a4276-104">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a4276-104">In the following example:</span></span>

* <span data-ttu-id="a4276-105">Un par de componentes usa un contenedor de estado para realizar el seguimiento de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="a4276-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="a4276-106">Los componentes del ejemplo están anidados, pero el anidamiento no es necesario para que funcione este enfoque.</span><span class="sxs-lookup"><span data-stu-id="a4276-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="a4276-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="a4276-107">`StateContainer.cs`:</span></span>

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

<span data-ttu-id="a4276-108">En `Program.Main` (WebAssembly de Blazor):</span><span class="sxs-lookup"><span data-stu-id="a4276-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="a4276-109">En `Startup.ConfigureServices` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="a4276-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="a4276-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="a4276-110">`Pages/Component1.razor`:</span></span>

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

<span data-ttu-id="a4276-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="a4276-111">`Shared/Component2.razor`:</span></span>

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

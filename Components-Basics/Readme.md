# DailUpDev Pet Store — Blazor Components with .NET 10

A beginner-friendly Blazor project that demonstrates component communication using a simple pet store example.

The application contains reusable pet components for a cat and a dog. Each pet has a happiness score that increases when the user feeds it.

## Features

- Reusable Blazor components
- Parent-to-child communication with `[Parameter]`
- Required parameters with `[EditorRequired]`
- Child-to-parent communication with `EventCallback<T>`
- Local component state
- Button event handling with `@onclick`
- Component references using `@ref`
- Parent calling public child-component methods
- Resetting multiple child components
- Interactive Server rendering

## Technologies

- .NET 10
- ASP.NET Core Blazor
- C#
- Razor Components
- Interactive Server rendering

## How It Works

The application displays two reusable pet components:

- Dog
- Cat

Each component includes:

- Pet name
- Happiness score
- Feed button

When a pet is fed:

1. Its happiness increases by 10.
2. The child component sends the updated value to the parent.
3. The parent updates the feeding dashboard.

The parent can also reset both pets by calling their public methods through `@ref`.

## Component Communication

```text
Parent → Child
[Parameter]

Child → Parent
EventCallback<T>

Parent → Child method
@ref
```

## Child Component

Create `PetFed.razor`.

```razor
<div style="background-color:lightblue;
            padding:20px;
            border-radius:10px;
            width:220px;">

    <h1>🐾 @PetName</h1>

    <p>
        Happiness:
        <span style="color:purple">
            @Happiness
        </span>
    </p>

    <button @onclick="FeedMyPet">
        🍎 Feed Me!
    </button>
</div>

@code {
    [Parameter, EditorRequired]
    public string PetName { get; set; } = string.Empty;

    [Parameter]
    public EventCallback<PetFeedResult> OnPetFed { get; set; }

    private int Happiness { get; set; }

    private async Task FeedMyPet()
    {
        Happiness += 10;

        var result = new PetFeedResult
        {
            PetName = PetName,
            Happiness = Happiness
        };

        await OnPetFed.InvokeAsync(result);
    }

    public async Task ResetHappiness()
    {
        Happiness = 0;
        await InvokeAsync(StateHasChanged);
    }
}
```

## Event Model

Create `PetFeedResult.cs`.

```csharp
namespace DailUpDevPetStore.Models;

public class PetFeedResult
{
    public string PetName { get; set; } = string.Empty;

    public int Happiness { get; set; }
}
```

Import the namespace in `_Imports.razor`:

```razor
@using DailUpDevPetStore.Models
```

## Parent Page

Create or update `Home.razor`.

```razor
@page "/"
@rendermode InteractiveServer

<PageTitle>DailUpDev Pet Store</PageTitle>

<h1>🐶 😺 DailUpDev Pet Store</h1>

<div style="display:flex; gap:10px;">
    <PetFed @ref="dogComponentRef"
            PetName="Dog"
            OnPetFed="HandlePetFed" />

    <PetFed @ref="catComponentRef"
            PetName="Cat"
            OnPetFed="HandlePetFed" />
</div>

<hr />

<button @onclick="Reset">
    🔄 Reset
</button>

<h2>Feeding Dashboard</h2>

<p>Last pet fed: @LastPetFed</p>
<p>Current happiness: @CurrentHappiness</p>
<p>Total feedings: @TotalFeedings</p>

@code {
    private string LastPetFed { get; set; } = "None";
    private int CurrentHappiness { get; set; }
    private int TotalFeedings { get; set; }

    private PetFed? catComponentRef;
    private PetFed? dogComponentRef;

    private void HandlePetFed(PetFeedResult petResult)
    {
        LastPetFed = petResult.PetName;
        CurrentHappiness = petResult.Happiness;
        TotalFeedings++;
    }

    private async Task Reset()
    {
        if (catComponentRef is not null)
        {
            await catComponentRef.ResetHappiness();
        }

        if (dogComponentRef is not null)
        {
            await dogComponentRef.ResetHappiness();
        }

        LastPetFed = "None";
        CurrentHappiness = 0;
        TotalFeedings = 0;
    }
}
```

## Important Detail

Blazor event handlers must use `@onclick`.

Correct:

```razor
<button @onclick="Reset">
    Reset
</button>
```

Incorrect:

```razor
<button onclick="@Reset">
    Reset
</button>
```

The second example uses the normal HTML `onclick` attribute instead of Blazor event handling.

## Main Concepts

### `[Parameter]`

Allows the parent component to send data to the child.

```csharp
[Parameter]
public string PetName { get; set; } = string.Empty;
```

### `[EditorRequired]`

Indicates that the parent should supply the parameter.

```csharp
[Parameter, EditorRequired]
public string PetName { get; set; } = string.Empty;
```

A missing value may produce an editor or build warning. It is not runtime validation.

### `EventCallback<T>`

Allows a child component to send information to its parent.

```csharp
[Parameter]
public EventCallback<PetFeedResult> OnPetFed { get; set; }
```

The child invokes the callback:

```csharp
await OnPetFed.InvokeAsync(result);
```

### `@ref`

Gives the parent a reference to the child component.

```razor
<PetFed @ref="dogComponentRef"
        PetName="Dog" />
```

The parent can then call a public child method:

```csharp
await dogComponentRef.ResetHappiness();
```

Use `@ref` selectively because it creates tighter coupling between components.

## Suggested Project Structure

```text
DailUpDevPetStore/
│
├── Components/
│   ├── Pages/
│   │   └── Home.razor
│   └── PetFed.razor
│
├── Models/
│   └── PetFeedResult.cs
│
├── wwwroot/
├── _Imports.razor
├── Program.cs
└── DailUpDevPetStore.csproj
```

## Run the Project

```bash
git clone https://github.com/your-username/dailupdev-pet-store.git
cd dailupdev-pet-store
dotnet restore
dotnet run
```

Open the local address shown in the terminal.

## Learning Goals

After completing this project, you should understand:

- How to create reusable Razor components
- How to pass data into components
- How components maintain local state
- How child components notify their parent
- How a parent can call a child method
- How Blazor updates the UI after state changes

## Possible Improvements

- Add pet images
- Add a maximum happiness value
- Add a hunger score
- Add pet food choices
- Store pet data in a database
- Add CSS isolation
- Add animations
- Add component tests

## YouTube Series

This project is part of:

**DailUpDev Pet Store — Blazor with .NET 10**

Topics covered:

- Blazor components
- Parameters
- `EditorRequired`
- `EventCallback`
- Component state
- Parent-child communication
- Component references with `@ref`


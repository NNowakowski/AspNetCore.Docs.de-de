---
title: Blazor-Formulare und -Validierung in ASP.NET Core
author: guardrex
description: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarios in Blazor verwendet werden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 3e719261315ed3a17833da7751d801d79a11ee6c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014477"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="cb980-103">Blazor-Formulare und -Validierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb980-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="cb980-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cb980-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cb980-105">Formulare und Validierungen werden in Blazor mithilfe von [Datenanmerkungen](xref:mvc/models/validation) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cb980-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="cb980-106">Der folgende `ExampleModel`-Typ definiert die Validierungslogik mithilfe von Datenanmerkungen:</span><span class="sxs-lookup"><span data-stu-id="cb980-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="cb980-107">Ein Formular wird mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.EditForm>-Komponente definiert.</span><span class="sxs-lookup"><span data-stu-id="cb980-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="cb980-108">Im folgenden Formular sehen Sie typische Elemente, Komponenten und typischen Razor-Code:</span><span class="sxs-lookup"><span data-stu-id="cb980-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="cb980-109">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cb980-109">In the preceding example:</span></span>

* <span data-ttu-id="cb980-110">Das Formular validiert Benutzereingaben im `name`-Feld mithilfe der im `ExampleModel`-Typ definierten Validierung.</span><span class="sxs-lookup"><span data-stu-id="cb980-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="cb980-111">Das Modell wird im `@code`-Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cb980-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="cb980-112">Das Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="cb980-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="cb980-113">Der `@bind-Value` der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente bindet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cb980-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="cb980-114">Die Modelleigenschaft (`exampleModel.Name`) an die `Value`-Eigenschaft der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente.</span><span class="sxs-lookup"><span data-stu-id="cb980-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="cb980-115">Weitere Informationen zur Eigenschaftenbindung finden Sie unter <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span><span class="sxs-lookup"><span data-stu-id="cb980-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="cb980-116">Den Delegat eines Änderungsereignisses an die `ValueChanged`-Eigenschaft der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente.</span><span class="sxs-lookup"><span data-stu-id="cb980-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="cb980-117">Die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb980-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="cb980-118">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente fasst Validierungsnachrichten zusammen.</span><span class="sxs-lookup"><span data-stu-id="cb980-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="cb980-119">`HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich gesendet wird (die Validierung erfolgreich besteht).</span><span class="sxs-lookup"><span data-stu-id="cb980-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="cb980-120">Integrierte Formularkomponenten</span><span class="sxs-lookup"><span data-stu-id="cb980-120">Built-in forms components</span></span>

<span data-ttu-id="cb980-121">Einige integrierte Eingabekomponenten sind verfügbar, um Benutzereingaben zu empfangen und zu validieren.</span><span class="sxs-lookup"><span data-stu-id="cb980-121">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="cb980-122">Eingaben werden validiert, wenn sie geändert werden und wenn ein Formular gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="cb980-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="cb980-123">In der folgenden Tabelle finden Sie verfügbare Eingabekomponenten.</span><span class="sxs-lookup"><span data-stu-id="cb980-123">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="cb980-124">Eingabekomponenten</span><span class="sxs-lookup"><span data-stu-id="cb980-124">Input component</span></span> | <span data-ttu-id="cb980-125">Wird gerendert als&hellip;</span><span class="sxs-lookup"><span data-stu-id="cb980-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="cb980-126">Alle Eingabekomponenten einschließlich <xref:Microsoft.AspNetCore.Components.Forms.EditForm> unterstützen arbiträre Attribute.</span><span class="sxs-lookup"><span data-stu-id="cb980-126">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="cb980-127">Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten HTML-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cb980-127">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="cb980-128">Eingabekomponenten stellen Standardverhalten für das Validieren bei Bearbeitung zur Verfügung und ändern ihre CSS-Klasse, um den Feldzustand darzustellen.</span><span class="sxs-lookup"><span data-stu-id="cb980-128">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="cb980-129">Einige Komponenten beinhalten hilfreiche Parsinglogik.</span><span class="sxs-lookup"><span data-stu-id="cb980-129">Some components include useful parsing logic.</span></span> <span data-ttu-id="cb980-130"><xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> und <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> verarbeiten beispielsweise nicht parsbare Werte ordnungsgemäß.</span><span class="sxs-lookup"><span data-stu-id="cb980-130">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="cb980-131">Typen, die NULL-Werte akzeptieren, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. B. `int?`).</span><span class="sxs-lookup"><span data-stu-id="cb980-131">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="cb980-132">Der folgende `Starship`-Typ definiert eine Validierungslogik mithilfe einer größeren Anzahl Eigenschaften und Datenanmerkungen wie die frühere `ExampleModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="cb980-132">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="cb980-133">Im obigen Beispiel ist `Description` optional, da keine Datenanmerkungen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="cb980-133">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="cb980-134">Das folgende Formular validiert Benutzereingaben mithilfe der im `Starship`-Modell definierten Validierung:</span><span class="sxs-lookup"><span data-stu-id="cb980-134">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="cb980-135"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> erstellt <xref:Microsoft.AspNetCore.Components.Forms.EditContext> als [kaskadierenden Wert](xref:blazor/components/cascading-values-and-parameters), der Metadaten zum Bearbeitungsprozess erfasst, einschließlich der Felder, die geändert wurden und den aktuellen Validierungsnachrichten.</span><span class="sxs-lookup"><span data-stu-id="cb980-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="cb980-136"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> stellt auch für gültige und ungültige Sendevorgänge (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>) geeignete Ereignisse zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="cb980-136">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="cb980-137">Alternativ können Sie <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> verwenden, um die Validierung auszulösen und Feldwerte mit benutzerdefiniertem Validierungscode zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="cb980-137">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="cb980-138">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cb980-138">In the following example:</span></span>

* <span data-ttu-id="cb980-139">Die `HandleSubmit`-Methode wird ausgeführt, wenn die Schaltfläche **`Submit`** ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="cb980-139">The `HandleSubmit` method runs when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="cb980-140">Das Formular wird mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse des Formulars validiert.</span><span class="sxs-lookup"><span data-stu-id="cb980-140">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="cb980-141">Das Formular wird dann weiter validiert, indem <xref:Microsoft.AspNetCore.Components.Forms.EditContext> an die `ServerValidate`-Methode übergeben wird, die einen Web-API-Endpunkt auf dem Server aufruft (*wird hier nicht gezeigt*).</span><span class="sxs-lookup"><span data-stu-id="cb980-141">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="cb980-142">Je nach Ergebnis der client- und serverseitigen Validierung, indem `isValid` überprüft wird, wird weiterer Code ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="cb980-142">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="cb980-143">InputText basierend auf dem Eingabeereignis</span><span class="sxs-lookup"><span data-stu-id="cb980-143">InputText based on the input event</span></span>

<span data-ttu-id="cb980-144">Verwenden Sie die <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das `input`-Ereignis anstelle des `change`-Ereignisses verwendet.</span><span class="sxs-lookup"><span data-stu-id="cb980-144">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="cb980-145">Im folgenden Beispiel erbt die `CustomInputText`-Komponente die `InputText`-Komponente des Frameworks und legt die Ereignisbindung (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) auf das `oninput`-Ereignis fest.</span><span class="sxs-lookup"><span data-stu-id="cb980-145">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="cb980-146">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="cb980-146">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="cb980-147">Die `CustomInputText`-Komponente kann überall dort verwendet werden, wo <xref:Microsoft.AspNetCore.Components.Forms.InputText> verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="cb980-147">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="cb980-148">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="cb980-148">`Pages/TestForm.razor`:</span></span>

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="cb980-149">Optionsfelder</span><span class="sxs-lookup"><span data-stu-id="cb980-149">Radio buttons</span></span>

<span data-ttu-id="cb980-150">Beim Verwenden von Optionsfeldern in einem Formular werden Datenbindungen nicht wie andere Elemente verarbeitet, da Optionsfelder als Gruppe ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="cb980-150">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="cb980-151">Der Wert der einzelnen Optionsfelder ist fest. Der Wert der Optionsfeldgruppe ist jedoch der Wert des ausgewählten Optionsfelds.</span><span class="sxs-lookup"><span data-stu-id="cb980-151">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="cb980-152">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="cb980-152">The following example shows how to:</span></span>

* <span data-ttu-id="cb980-153">Verarbeiten von Datenbindungen für eine Optionsfeldgruppe</span><span class="sxs-lookup"><span data-stu-id="cb980-153">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="cb980-154">Unterstützen der Validierung mithilfe einer benutzerdefinierten `InputRadio`-Komponente</span><span class="sxs-lookup"><span data-stu-id="cb980-154">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="cb980-155">Die folgende <xref:Microsoft.AspNetCore.Components.Forms.EditForm>-Klasse verwendet die vorangehende `InputRadio`-Komponente, um eine Bewertung vom Benutzer zu erhalten und sie zu bewerten:</span><span class="sxs-lookup"><span data-stu-id="cb980-155">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="cb980-156">Binden von `<select>`-Elementoptionen an `null`-Werte von C#-Objekten</span><span class="sxs-lookup"><span data-stu-id="cb980-156">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="cb980-157">Es gibt aus folgenden Gründen keine vernünftige Möglichkeit, den Optionswert eines `<select>`-Elements als `null`-Wert eines C#-Objekts darzustellen:</span><span class="sxs-lookup"><span data-stu-id="cb980-157">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="cb980-158">HTML-Attribute können keine `null`-Werte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="cb980-158">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="cb980-159">`null` entspricht in HTML am ehesten dem Nichtvorhandensein des `value`-HTML-Attributs im `<option>`-Element.</span><span class="sxs-lookup"><span data-stu-id="cb980-159">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="cb980-160">Wenn Sie ein `<option>`-Element ohne `value`-Attribut auswählen, behandelt der Browser den Wert als *Textinhalt* dieses `<option>`-Elements.</span><span class="sxs-lookup"><span data-stu-id="cb980-160">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="cb980-161">Das Blazor-Framework versucht nicht, das Standardverhalten zu unterdrücken, weil dies Folgendes beinhalten würde:</span><span class="sxs-lookup"><span data-stu-id="cb980-161">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="cb980-162">Erstellen einer Kette von Problemumgehungen für Sonderfälle im Framework.</span><span class="sxs-lookup"><span data-stu-id="cb980-162">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="cb980-163">Breaking Changes am aktuellen Frameworkverhalten.</span><span class="sxs-lookup"><span data-stu-id="cb980-163">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cb980-164">Das plausibelste Äquivalent zu `null` in HTML ist eine `value` mit einer *leeren Zeichenfolge*.</span><span class="sxs-lookup"><span data-stu-id="cb980-164">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="cb980-165">Das Blazor-Framework verarbeitet `null` als Konvertierung einer leeren Zeichenfolge für eine bidirektionale Bindung an einen `<select>`-Wert.</span><span class="sxs-lookup"><span data-stu-id="cb980-165">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cb980-166">Beim Versuch einer bidirektionalen Bindung an einen `<select>`-Wert verarbeitet das Blazor-Framework `null` nicht automatisch als Konvertierung einer leeren Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="cb980-166">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="cb980-167">Weitere Informationen finden Sie unter [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221) (Korrigieren der Bindung von <select> an einen NULL-Wert [dotnet/aspnetcore #23221]).</span><span class="sxs-lookup"><span data-stu-id="cb980-167">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="cb980-168">Validierungsunterstützung</span><span class="sxs-lookup"><span data-stu-id="cb980-168">Validation support</span></span>

<span data-ttu-id="cb980-169">Die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen an die kaskadierte <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse an.</span><span class="sxs-lookup"><span data-stu-id="cb980-169">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="cb980-170">Für das Aktivieren der Validierungsunterstützung mithilfe von Datenanmerkungen ist eine explizite Geste erforderlich.</span><span class="sxs-lookup"><span data-stu-id="cb980-170">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="cb980-171">Wenn Sie ein anderes Validierungssystem als Datenanmerkungen verwenden möchten, ersetzen Sie <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="cb980-171">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="cb980-172">Die ASP.NET Core-Implementierung können Sie sich in der Verweisquelle ansehen: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="cb980-172">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="cb980-173">Die vorangehenden Links zur Verweisquelle stellen Code aus dem `master`-Branch des Repositorys bereit, der die aktuelle Entwicklung der Produkteinheit für das nächste Release von ASP.net Core darstellt.</span><span class="sxs-lookup"><span data-stu-id="cb980-173">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="cb980-174">Um den Branch für ein anderes Release auszuwählen, verwenden Sie die GitHub-Branchauswahl (beispielsweise `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="cb980-174">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="cb980-175">Blazor führt zwei Validierungstypen aus:</span><span class="sxs-lookup"><span data-stu-id="cb980-175">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="cb980-176">Die *Feldvalidierung* wird ausgeführt, wenn der Benutzer auf eine Stelle außerhalb des Felds tippt.</span><span class="sxs-lookup"><span data-stu-id="cb980-176">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="cb980-177">Während der Feldvalidierung ordnet die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente alle gemeldeten Validierungsergebnisse dem Feld zu.</span><span class="sxs-lookup"><span data-stu-id="cb980-177">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="cb980-178">*Modellvalidierung* wird ausgeführt, wenn der Benutzer ein Formular sendet.</span><span class="sxs-lookup"><span data-stu-id="cb980-178">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="cb980-179">Während der Modellvalidierung versucht die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente, das Feld basierend auf dem Namen des Members zu ermitteln, das vom Validierungsergebnis gemeldet wird.</span><span class="sxs-lookup"><span data-stu-id="cb980-179">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="cb980-180">Validierungsergebnisse, die keinem einzelnen Member zugeordnet werden, werden dem Modell und keinem Feld zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cb980-180">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="cb980-181">Komponenten der Validierungszusammenfassung und der Validierungsnachricht</span><span class="sxs-lookup"><span data-stu-id="cb980-181">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="cb980-182">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente fasst alle Validierungsnachrichten zusammen. Das ähnelt dem [Taghilfsprogramm für Validierungszusammenfassungen](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="cb980-182">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="cb980-183">Validierungsmeldungen für ein bestimmtes Modell werden mit dem `Model`-Parameter ausgegeben:</span><span class="sxs-lookup"><span data-stu-id="cb980-183">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="cb980-184">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>-Komponente zeigt Validierungsnachrichten für ein bestimmtes Feld an. Das ähnelt dem [Taghilfsprogramm für Validierungsmeldungen](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="cb980-184">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="cb980-185">Das Validierungsfeld wird mit dem `For`-Attribut und einem Lambdaausdruck angegeben, der die Modelleigenschaft benennt:</span><span class="sxs-lookup"><span data-stu-id="cb980-185">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="cb980-186">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>- und <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponenten unterstützen arbiträre Attribute.</span><span class="sxs-lookup"><span data-stu-id="cb980-186">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="cb980-187">Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten `<div>`- oder `<ul>`-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cb980-187">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="cb980-188">Steuern Sie den Stil von Validierungsmeldungen im Stylesheet (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) der App.</span><span class="sxs-lookup"><span data-stu-id="cb980-188">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="cb980-189">Die `validation-message`-Standardklasse legt die Textfarbe von Validierungsmeldungen auf Rot fest:</span><span class="sxs-lookup"><span data-stu-id="cb980-189">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="cb980-190">Benutzerdefinierte Validierungsattribute</span><span class="sxs-lookup"><span data-stu-id="cb980-190">Custom validation attributes</span></span>

<span data-ttu-id="cb980-191">Übergeben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>-Eigenschaft des Validierungskontexts bei der Erstellung der <xref:System.ComponentModel.DataAnnotations.ValidationResult>-Klasse, um sicherzustellen, dass ein Validierungsergebnis korrekt einem Feld zugeordnet wird, wenn ein [benutzerdefiniertes Validierungsattribut](xref:mvc/models/validation#custom-attributes) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="cb980-191">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="cb980-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> ist `null`.</span><span class="sxs-lookup"><span data-stu-id="cb980-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="cb980-193">Das Einfügen von Diensten zur Validierung in der `IsValid`-Methode wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cb980-193">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="cb980-194">Validierungspakete für Datenanmerkungen in Blazor</span><span class="sxs-lookup"><span data-stu-id="cb980-194">Blazor data annotations validation package</span></span>

<span data-ttu-id="cb980-195">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ist ein Paket, das Lücken bei der Validierung mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente schließt.</span><span class="sxs-lookup"><span data-stu-id="cb980-195">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="cb980-196">Das Paket ist aktuell *experimentell*.</span><span class="sxs-lookup"><span data-stu-id="cb980-196">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="cb980-197">[CompareProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="cb980-197">[CompareProperty] attribute</span></span>

<span data-ttu-id="cb980-198"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente, da hier das Validierungsergebnis nicht einem bestimmten Member zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="cb980-198">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="cb980-199">Das führt zu einem nicht konsistenten Verhalten zwischen Validierung auf Feldebene und der Validierung des gesamten Modells bei Sendevorgängen.</span><span class="sxs-lookup"><span data-stu-id="cb980-199">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="cb980-200">Mit dem *experimentellen* Paket [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) wird das zusätzliche Validierungsattribut `ComparePropertyAttribute` eingeführt, das diese Einschränkungen umgeht.</span><span class="sxs-lookup"><span data-stu-id="cb980-200">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="cb980-201">In einer Blazor-App handelt es sich bei `[CompareProperty]` um eine direkte Ersetzung des [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute)-Attributs.</span><span class="sxs-lookup"><span data-stu-id="cb980-201">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="cb980-202">Verschachtelte Modelle, Sammlungstypen und komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="cb980-202">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="cb980-203">Blazor bietet Unterstützung für die Validierung von Formulareingaben mithilfe von Datenanmerkungen mit dem integrierten <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="cb980-203">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="cb980-204"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> validiert jedoch nur Eigenschaften des Modells auf oberster Ebene, die an das Formular gebunden sind, bei denen es sich aber um keine Sammlungs- oder komplexen Eigenschaften handelt.</span><span class="sxs-lookup"><span data-stu-id="cb980-204">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="cb980-205">Verwenden Sie den `ObjectGraphDataAnnotationsValidator`, der im *experimentellen* Paket [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) bereitgestellt wird, um den gesamten Objektgraph des gebundenen Modells zu validieren, einschließlich Sammlungseigenschaften und komplexer Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="cb980-205">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="cb980-206">Fügen Sie Modelleigenschaften Anmerkungen mit `[ValidateComplexType]` hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb980-206">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="cb980-207">In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Datenanmerkungen für die Validierung, wenn das Modell an das Formular gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="cb980-207">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="cb980-208">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="cb980-208">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="cb980-209">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="cb980-209">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="cb980-210">Aktivieren der Schaltfläche zum Senden basierend auf der Formularvalidierung</span><span class="sxs-lookup"><span data-stu-id="cb980-210">Enable the submit button based on form validation</span></span>

<span data-ttu-id="cb980-211">So aktivieren oder deaktivieren Sie die Schaltfläche zum Senden basierend auf der Formularvalidierung:</span><span class="sxs-lookup"><span data-stu-id="cb980-211">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="cb980-212">Verwenden Sie die <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse des Formulars, um das Modell zuzuweisen, wenn die Komponente initialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="cb980-212">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="cb980-213">Validieren Sie das Formular im <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>-Rückruf des Kontexts, um die Schaltfläche zum Senden zu aktivieren und zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="cb980-213">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="cb980-214">Heben Sie die Einbindung des Ereignishandlers in der `Dispose`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="cb980-214">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="cb980-215">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="cb980-215">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="cb980-216">Im vorherigen Beispiel ist `formInvalid` in folgenden Fällen auf `false` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="cb980-216">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="cb980-217">Das Formular ist bereits mit gültigen Standardwerten ausgefüllt.</span><span class="sxs-lookup"><span data-stu-id="cb980-217">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="cb980-218">Sie möchten die Schaltfläche zum Senden aktivieren, wenn das Formular geladen wird.</span><span class="sxs-lookup"><span data-stu-id="cb980-218">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="cb980-219">Eine Nebenwirkung des vorangehenden Ansatzes ist, dass eine <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente mit ungültigen Feldern aufgefüllt wird, nachdem der Benutzer mit einem beliebigen Feld interagiert hat.</span><span class="sxs-lookup"><span data-stu-id="cb980-219">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="cb980-220">Dieses Szenario lässt sich folgendermaßen auflösen:</span><span class="sxs-lookup"><span data-stu-id="cb980-220">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="cb980-221">Verwenden Sie keine <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente des Formulars.</span><span class="sxs-lookup"><span data-stu-id="cb980-221">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="cb980-222">Lassen Sie die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente einblenden, wenn auf die Schaltfläche zum Senden geklickt wird, z. B. in einer `HandleValidSubmit`-Methode.</span><span class="sxs-lookup"><span data-stu-id="cb980-222">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="cb980-223">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="cb980-223">Troubleshoot</span></span>

> <span data-ttu-id="cb980-224">InvalidOperationException: EditForm erfordert einen Model-Parameter oder einen EditContext-Parameter, aber nicht beides.</span><span class="sxs-lookup"><span data-stu-id="cb980-224">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="cb980-225">Vergewissern Sie sich, dass <xref:Microsoft.AspNetCore.Components.Forms.EditForm> über ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>- oder <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Element verfügt.</span><span class="sxs-lookup"><span data-stu-id="cb980-225">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="cb980-226">Wenn Sie dem Formular ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>-Element zuweisen, vergewissern Sie sich, dass der Modelltyp instanziiert wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="cb980-226">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

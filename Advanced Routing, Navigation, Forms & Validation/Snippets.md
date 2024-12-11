# Snippets for Day 2 Demo: Advanced Routing, Navigation, Forms & Validation

## 1. Attribute Routing Example

```csharp
[Route("shipments/track/{trackingNumber}")]
public IActionResult Track(string trackingNumber) {
    // Logic to fetch and display shipment details
    return View();
}
```

## 2. Conventional Routing

```csharp
app.UseEndpoints(endpoints => {
    endpoints.MapControllerRoute(
        name: "shipment-management",
        pattern: "{controller=Shipments}/{action=Index}/{id?}"
    );
});
```

## 3. Navigation Menu in _Layout.cshtml

```html
<ul>
    <li><a asp-controller="Home" asp-action="Index">Dashboard</a></li>
    <li><a asp-controller="Shipments" asp-action="List">Shipment List</a></li>
    <li><a asp-controller="Shipments" asp-action="Add">Add Shipment</a></li>
    <li><a asp-controller="Shipments" asp-action="Track">Track Shipment</a></li>
</ul>
```

## 4. Shipment Model

```csharp
public class Shipment {
    [Required(ErrorMessage = "Tracking number is required")]
    [StringLength(50, ErrorMessage = "Tracking number cannot exceed 50 characters")]
    public string TrackingNumber { get; set; }

    [Required(ErrorMessage = "Delivery date is required")]
    public DateTime DeliveryDate { get; set; }

    [MaxLength(500, ErrorMessage = "Notes cannot exceed 500 characters")]
    public string Notes { get; set; }
}
```

## 5. Razor Form for Adding a Shipment

```html
<form asp-controller="Shipments" asp-action="Create" method="post">
    <label for="trackingNumber">Tracking Number:</label>
    <input type="text" id="trackingNumber" name="TrackingNumber" class="form-control">

    <label for="deliveryDate">Delivery Date:</label>
    <input type="date" id="deliveryDate" name="DeliveryDate" class="form-control">

    <label for="notes">Notes:</label>
    <textarea id="notes" name="Notes" class="form-control"></textarea>

    <button type="submit">Submit</button>
</form>
```

## 6. Controller Action for Form Submission

```csharp
[HttpPost]
public IActionResult Create(Shipment shipment) {
    if (ModelState.IsValid) {
        // Logic to save shipment details
        return RedirectToAction("List");
    }
    return View(shipment);
}
```

## 7. Validation Scripts in _Layout.cshtml

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/jquery-validation@1.19.3/dist/jquery.validate.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/jquery-validation-unobtrusive@3.2.12/dist/jquery.validate.unobtrusive.min.js"></script>
```

## 8. Validation Messages in Razor Form

```html
<span asp-validation-for="TrackingNumber" class="text-danger"></span>
<span asp-validation-for="DeliveryDate" class="text-danger"></span>
<span asp-validation-for="Notes" class="text-danger"></span>
```

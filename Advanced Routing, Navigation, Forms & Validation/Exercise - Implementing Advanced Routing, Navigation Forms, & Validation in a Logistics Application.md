# Exercise: Implementing Advanced Routing, Navigation, Forms & Validation in a Logistics Application

## Scenario

You are developing a logistics management system. One feature is to allow employees to manage shipments by creating, viewing, and editing shipment details. This exercise will test your understanding of advanced routing, navigation menus, form handling, and validation.

---

## User Stories and Use Cases

1. **User Stories**:
   - As an employee, I want to track shipments using a tracking number so that I can view the status and delivery details.
   - As an employee, I want to add new shipment records with validation to ensure that all required fields are filled correctly.
   - As an employee, I want a navigation menu that lets me easily access different parts of the logistics system.

2. **Use Cases**:
   - **Track Shipment**: Enter a tracking number and retrieve details about the shipment.
   - **Add Shipment**: Submit shipment details through a form with validation.
   - **Navigate**: Use a menu to access Dashboard, Shipment List, Add Shipment, and Track Shipment pages.

---

## Task Overview

1. **Routing Implementation**
   - Use attribute routing for a shipment tracking feature.
   - Use conventional routing for managing shipment records.

2. **Navigation Menu Creation**
   - Create a dynamic navigation menu using Razor tag helpers.

3. **Form Development**
   - Create a form to add shipment details.
   - Implement model binding to handle form submissions.

4. **Validation**
   - Add validation rules to ensure data integrity.
   - Display validation errors in the Razor view.

5. **Outcome**
   - A functional logistics management system that includes:
     - Navigation to all major features.
     - A working shipment tracking page.
     - A form for adding new shipments with validation.
     - Error handling and user feedback on the form.

---

## Detailed Instructions

### 1. Routing

1. **Attribute Routing**:
   - Define a route for tracking a shipment using the tracking number.
   - Example:

     ```csharp
     [Route("shipments/track/{trackingNumber}")]
     public IActionResult Track(string trackingNumber) {
         // Logic to fetch and display shipment details
     }
     ```

2. **Conventional Routing**:
   - Define a route in the `Startup.cs` or `Program.cs` for managing shipments:

     ```csharp
     app.UseEndpoints(endpoints => {
         endpoints.MapControllerRoute(
             name: "shipment-management",
             pattern: "{controller=Shipments}/{action=Index}/{id?}");
     });
     ```

---

### 2. Navigation Menu

1. Add a navigation menu in the shared `_Layout.cshtml`:
   - Include links for:
     - Dashboard
     - Shipment List
     - Add Shipment
     - Track Shipment

   Example:

   ```html
   <ul>
       <li><a asp-controller="Home" asp-action="Index">Dashboard</a></li>
       <li><a asp-controller="Shipments" asp-action="List">Shipment List</a></li>
       <li><a asp-controller="Shipments" asp-action="Add">Add Shipment</a></li>
       <li><a asp-controller="Shipments" asp-action="Track">Track Shipment</a></li>
   </ul>
   ```

---

### 3. Form Creation

1. **Model**:
   - Create a `Shipment` model with the following properties:

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

2. **Form**:
   - Create a Razor form to add a shipment:

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

3. **Controller**:
   - Implement the `Create` action in the `Shipments` controller:

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

---

### 4. Validation

1. Enable client-side validation:
   - Add the required scripts in `_Layout.cshtml`:

     ```html
     <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
     <script src="https://cdn.jsdelivr.net/npm/jquery-validation@1.19.3/dist/jquery.validate.min.js"></script>
     <script src="https://cdn.jsdelivr.net/npm/jquery-validation-unobtrusive@3.2.12/dist/jquery.validate.unobtrusive.min.js"></script>
     ```

2. Display validation messages in the Razor form:

   ```html
   <span asp-validation-for="TrackingNumber" class="text-danger"></span>
   <span asp-validation-for="DeliveryDate" class="text-danger"></span>
   <span asp-validation-for="Notes" class="text-danger"></span>
   ```

---

## Submission

1. Submit the following:
   - Razor views (`List`, `Add`, `Track` pages).
   - `ShipmentsController` code.
   - `Shipment` model.

2. Ensure all features work as expected:
   - Routing for shipment management and tracking.
   - Navigation menu links.
   - Form submission and validation.

---

## Evaluation Criteria

1. Correct implementation of attribute and conventional routing.
2. Proper use of Razor tag helpers in navigation.
3. Functional form with validation.
4. Clean and readable code with appropriate comments.
5. Full completion of user stories and use cases.

Good luck! Feel free to ask questions during the exercise.

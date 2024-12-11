# Exercise: Build a Simple Logistics Management App

This exercise involves creating a **Logistics Management App** to manage shipments and vehicles. Students will use **Entity Framework** to implement CRUD operations, apply the Repository Pattern, and follow clean architecture principles.

---

## **Scenario Overview**

**As a Business Analyst**, you're providing user stories and use cases for a **Logistics Management System**. The app should allow users to manage shipments, assign vehicles, and track shipment statuses.

---

## **Requirements**

### **Entities**

1. **Shipment**
   - Represents a package to be delivered.
   - Attributes: `Id`, `Name`, `Origin`, `Destination`, `Status`, `VehicleId`, `DateShipped`, `DateDelivered`.

2. **Vehicle**
   - Represents a vehicle used for shipment.
   - Attributes: `Id`, `LicensePlate`, `DriverName`, `Capacity`.

3. **Relationships**
   - A `Shipment` is assigned to a `Vehicle` (one-to-many).

---

### **User Stories**

1. **Manage Vehicles**
   - As a logistics manager, I need to create, update, view, and delete vehicles so that I can manage the fleet effectively.
2. **Create Shipments**
   - As a logistics manager, I need to create new shipments so that I can track deliveries.
3. **Assign Vehicles**
   - As a logistics manager, I need to assign a vehicle to a shipment so that it can be delivered.
4. **Track Shipment Status**
   - As a logistics manager, I need to update the status of shipments (e.g., Pending, In Transit, Delivered) to reflect delivery progress.
5. **View Shipments**
   - As a logistics manager, I need to view a list of all shipments and their associated vehicles to monitor operations.

---

### **Exercise Instructions**

1. **Database Setup**
   - Create two tables: `Shipments` and `Vehicles`.
   - Use Entity Framework to define these entities and their relationships.

2. **CRUD Operations**
   - Implement CRUD operations for `Vehicle` and `Shipment`.

3. **Assign a Vehicle to a Shipment**
   - Include a dropdown in the `Shipment` form to assign a vehicle to a shipment.

4. **Track Shipment Status**
   - Allow updating the status of a shipment.

5. **Follow the Folder Structure**
   - Use a clean architecture approach with separate folders for Controllers, Models, Repositories, Services, and Views.

---

### **Use Cases**

#### **Use Case 1: Create a Shipment**

**Actors:** Logistics Manager  
**Precondition:** Vehicles must exist in the system.  
**Steps:**

1. Navigate to the “Create Shipment” page.
2. Fill in the shipment details: `Name`, `Origin`, `Destination`, `DateShipped`.
3. Select a `Vehicle` from a dropdown.
4. Save the shipment.

**Postcondition:** The shipment is created and saved in the database, linked to the selected vehicle.

---

#### **Use Case 2: Update Shipment Status**

**Actors:** Logistics Manager  
**Precondition:** Shipments must exist in the system.  
**Steps:**

1. Navigate to the “Shipments” page.
2. Select a shipment to update.
3. Change the `Status` (e.g., Pending, In Transit, Delivered).
4. Save the changes.

**Postcondition:** The shipment status is updated in the database.

---

#### **Use Case 3: Manage Vehicles**

**Actors:** Logistics Manager  
**Steps:**

1. Navigate to the “Vehicles” page.
2. Create, update, or delete vehicles as needed.

**Postcondition:** Vehicle data is managed in the system.

---

### **Hints for Students**

1. **Entities and Relationships**
   - Define `Shipment` and `Vehicle` entities in the data layer.
   - Use `VehicleId` as a foreign key in the `Shipment` table.

2. **Repository Pattern**
   - Create base and specific repositories for `Shipment` and `Vehicle`.

3. **UI Features**
   - Use dropdowns for assigning vehicles to shipments.
   - Display shipment statuses with buttons for updates (e.g., Pending, In Transit, Delivered).

4. **Validation**
   - Ensure required fields (e.g., `Name`, `Origin`, `Destination`, `DateShipped`) are validated.
   - Prevent deletion of a `Vehicle` if it is assigned to a shipment.

5. **Views**
   - Create views for listing, creating, editing, and deleting shipments and vehicles.

---

### **Sample Class Definitions**

#### **Shipment.cs**

```csharp
public class Shipment : BaseEntity
{
    public string Name { get; set; }
    public string Origin { get; set; }
    public string Destination { get; set; }
    public string Status { get; set; } // E.g., Pending, In Transit, Delivered
    public int VehicleId { get; set; }
    public virtual Vehicle Vehicle { get; set; }
    public DateTime DateShipped { get; set; }
    public DateTime? DateDelivered { get; set; }
}
```

#### **Vehicle.cs**

```csharp
public class Vehicle : BaseEntity
{
    public string LicensePlate { get; set; }
    public string DriverName { get; set; }
    public int Capacity { get; set; } // Maximum number of shipments
    public virtual ICollection<Shipment> Shipments { get; set; } = new List<Shipment>();
}
```

---

### **Deliverables**

1. **Code Submission**
   - A fully functional logistics app with CRUD operations for shipments and vehicles.

2. **Documentation**
   - Explain your architecture and how you implemented the requirements.

3. **Deployment**
   - If possible, deploy the app locally or to a test server for review.

---

Let me know if you need further refinements or more details!

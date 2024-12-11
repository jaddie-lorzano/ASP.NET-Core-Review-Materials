# Clean and Scalable ASP.NET Project for Logistics Management

This guide outlines a clean, scalable, and beginner-friendly architecture for a logistics management web application. It uses Domain-Driven Design (DDD) principles, Blazor for the front end, and ASP.NET for the back end. This setup balances simplicity and scalability, allowing junior developers to create maintainable and testable applications.

---

## **Overview**

### **High-Level Architecture**

The project is structured into four layers:

1. **Domain**: Core business logic and rules.
2. **Application**: Coordinates business logic into use cases.
3. **Infrastructure**: Handles technical details (e.g., database, external services).
4. **Presentation**: The user interface, implemented using Blazor.

Each layer has distinct responsibilities and dependencies, adhering to **Separation of Concerns** principles.

---

## **Project Structure**

### **Folder Structure**

Here’s a suggested folder structure for your project:

```
SolutionRoot/
├── ProjectName.Domain/
│   ├── Entities/
│   │   └── Shipment.cs
│   ├── ValueObjects/
│   │   └── Address.cs
│   ├── Enums/
│   │   └── ShipmentStatus.cs
│   └── Interfaces/
│       └── IDomainService.cs
├── ProjectName.Application/
│   ├── Interfaces/
│   │   └── IShipmentRepository.cs
│   ├── UseCases/
│   │   ├── CreateShipmentHandler.cs
│   │   └── GetAllShipmentsHandler.cs
│   ├── Commands/
│   │   └── CreateShipmentCommand.cs
│   └── DTOs/
│       └── ShipmentDto.cs
├── ProjectName.Infrastructure/
│   ├── Persistence/
│   │   ├── LogisticsDbContext.cs
│   │   └── ShipmentRepository.cs
│   ├── Configurations/
│   │   └── EntityConfigurations.cs
│   └── Services/
│       └── ExternalServiceClient.cs
├── ProjectName.BlazorApp/
│   ├── Pages/
│   │   └── Shipments.razor
│   ├── Components/
│   └── Services/
│       └── UIService.cs
└── Tests/
    ├── DomainTests/
    ├── ApplicationTests/
    └── InfrastructureTests/
```

This structure organizes the code into logical modules, enhancing maintainability and scalability.

### **Domain Layer (ProjectName.Domain)**

This layer defines the core concepts and rules of the business.

#### **Entities**

Represent core business objects with identity and state.

```csharp
public class Shipment
{
    public Guid Id { get; private set; }
    public string TrackingNumber { get; private set; }
    public Address Origin { get; private set; }
    public Address Destination { get; private set; }
    public ShipmentStatus Status { get; private set; }

    public Shipment(string trackingNumber, Address origin, Address destination)
    {
        Id = Guid.NewGuid();
        TrackingNumber = trackingNumber;
        Origin = origin;
        Destination = destination;
        Status = ShipmentStatus.Created;
    }

    public void MarkInTransit()
    {
        if (Status != ShipmentStatus.Created)
            throw new InvalidOperationException("Can only mark as in transit from created state.");

        Status = ShipmentStatus.InTransit;
    }

    public void MarkDelivered()
    {
        if (Status != ShipmentStatus.InTransit)
            throw new InvalidOperationException("Shipment must be in transit before it can be delivered.");

        Status = ShipmentStatus.Delivered;
    }
}
```

#### **Value Objects**

Contain data and logic but no identity.

```csharp
public class Address
{
    public string Street { get; }
    public string City { get; }
    public string State { get; }
    public string Country { get; }

    public Address(string street, string city, string state, string country)
    {
        Street = street;
        City = city;
        State = state;
        Country = country;
    }
}
```

#### **Enums**

Define restricted sets of possible states.

```csharp
public enum ShipmentStatus
{
    Created,
    InTransit,
    Delivered
}
```

**Key Principles:**

- No dependencies on other layers.
- Purely business-focused.

---

### **Application Layer (ProjectName.Application)**

This layer contains the application’s orchestration logic.

#### **Interfaces**

Define contracts for data access or external services.

```csharp
public interface IShipmentRepository
{
    Task AddAsync(Shipment shipment);
    Task<Shipment> GetByIdAsync(Guid id);
    Task UpdateAsync(Shipment shipment);
    Task<IEnumerable<Shipment>> GetAllAsync();
}
```

#### **Use Cases (Handlers)**

Encapsulate business processes.

**Example: CreateShipmentHandler**

```csharp
public class CreateShipmentHandler
{
    private readonly IShipmentRepository _shipmentRepository;

    public CreateShipmentHandler(IShipmentRepository shipmentRepository)
    {
        _shipmentRepository = shipmentRepository;
    }

    public async Task<Guid> HandleAsync(CreateShipmentCommand command)
    {
        var origin = new Address(command.OriginStreet, command.OriginCity, command.OriginState, command.OriginCountry);
        var destination = new Address(command.DestinationStreet, command.DestinationCity, command.DestinationState, command.DestinationCountry);

        var shipment = new Shipment(command.TrackingNumber, origin, destination);
        await _shipmentRepository.AddAsync(shipment);

        return shipment.Id;
    }
}
```

#### **Commands and DTOs**

Represent input/output data for use cases.

```csharp
public class CreateShipmentCommand
{
    public string TrackingNumber { get; set; }
    public string OriginStreet { get; set; }
    public string OriginCity { get; set; }
    public string OriginState { get; set; }
    public string OriginCountry { get; set; }
    public string DestinationStreet { get; set; }
    public string DestinationCity { get; set; }
    public string DestinationState { get; set; }
    public string DestinationCountry { get; set; }
}

public class ShipmentDto
{
    public Guid Id { get; set; }
    public string TrackingNumber { get; set; }
    public string Status { get; set; }
    public string Origin { get; set; }
    public string Destination { get; set; }
}
```

**Key Principles:**

- Uses Domain entities.
- Defines clear, testable use cases.
- Communicates with Infrastructure via interfaces.

---

### **Infrastructure Layer (ProjectName.Infrastructure)**

Handles technical implementations like data persistence.

#### **DbContext**

Defines the database schema.

```csharp
public class LogisticsDbContext : DbContext
{
    public DbSet<Shipment> Shipments { get; set; }

    public LogisticsDbContext(DbContextOptions<LogisticsDbContext> options) : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Shipment>().OwnsOne(s => s.Origin);
        modelBuilder.Entity<Shipment>().OwnsOne(s => s.Destination);
    }
}
```

#### **Repository Implementation**

Implements the `IShipmentRepository` interface.

```csharp
public class ShipmentRepository : IShipmentRepository
{
    private readonly LogisticsDbContext _context;

    public ShipmentRepository(LogisticsDbContext context)
    {
        _context = context;
    }

    public async Task AddAsync(Shipment shipment)
    {
        await _context.Shipments.AddAsync(shipment);
        await _context.SaveChangesAsync();
    }

    public async Task<Shipment> GetByIdAsync(Guid id)
    {
        return await _context.Shipments.FindAsync(id);
    }

    public async Task UpdateAsync(Shipment shipment)
    {
        _context.Shipments.Update(shipment);
        await _context.SaveChangesAsync();
    }

    public async Task<IEnumerable<Shipment>> GetAllAsync()
    {
        return await _context.Shipments.ToListAsync();
    }
}
```

**Key Principles:**

- Implements Application-defined contracts.
- Uses Entity Framework Core for persistence.

---

### **Presentation Layer (ProjectName.BlazorApp)**

Implements the user interface using Blazor.

#### **Startup Configuration**

Wire up dependencies.

```csharp
builder.Services.AddDbContext<LogisticsDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("LogisticsDb")));

builder.Services.AddScoped<IShipmentRepository, ShipmentRepository>();
builder.Services.AddScoped<CreateShipmentHandler>();
```

#### **Blazor Page**

Displays shipment data and handles user interactions.

```razor
@page "/shipments"
@inject CreateShipmentHandler CreateHandler
@inject GetAllShipmentsHandler GetAllHandler

<h3>Shipments</h3>

<EditForm Model="@newShipmentCommand" OnValidSubmit="CreateShipment">
    <InputText @bind-Value="newShipmentCommand.TrackingNumber" placeholder="Tracking Number" />
    <!-- Other inputs for origin and destination -->
    <button type="submit">Create Shipment</button>
</EditForm>

<ul>
    @if (shipments is not null)
    {
        @foreach (var shipment in shipments)
        {
            <li>@shipment.TrackingNumber - @shipment.Status</li>
        }
    }
</ul>

@code {
    private CreateShipmentCommand newShipmentCommand = new();
    private IEnumerable<ShipmentDto> shipments;

    protected override async Task OnInitializedAsync()
    {
        shipments = await GetAllHandler.HandleAsync();
    }

    private async Task CreateShipment()
    {
        await CreateHandler.HandleAsync(newShipmentCommand);
        shipments = await GetAllHandler.HandleAsync();
        newShipmentCommand = new CreateShipmentCommand(); // Reset form
    }
}
```

**Key Principles:**

- UI calls Application use cases.
- No business logic in the UI.

---

## **Best Practices**

1. **Separation of Concerns**: Keep business logic in the Domain and Application layers. The Presentation and Infrastructure layers should focus on their respective responsibilities.
2. **Dependency Inversion**: Use interfaces to define contracts. The Application layer depends on abstractions, not implementations.
3. **Testability**: Write unit tests for Domain and Application layers. They don’t require a database or UI to test.
4. **Start Simple**: Don’t over-engineer. Begin with essential features and evolve the architecture as needed.
5. **Scalability**: This structure allows adding new features (e.g., domain events, caching) without major rewrites.

---

By following this guide, you’ll create a clean, scalable logistics application with maintainable code, testable components, and a clear separation of concerns.

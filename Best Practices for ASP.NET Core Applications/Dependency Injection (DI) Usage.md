# Best Practices for ASP.NET Core Application Development

## **Dependency Injection (DI) Usage**

### **1. Introduction to Dependency Injection (DI)**

Dependency Injection (DI) is a cornerstone of modern application development, enabling better separation of concerns, testability, and flexibility in software systems. ASP.NET Core comes with a built-in DI container that makes integrating this pattern straightforward. DI helps manage dependencies effectively, replacing tight coupling with loosely coupled, testable components. In the logistics domain, where systems often interact with APIs, databases, and external services, DI ensures efficient and maintainable architecture.

---

### **2. Principles of Dependency Injection**

- **Inversion of Control (IoC):** The control of dependency creation is inverted, moving responsibility from the dependent class to an external container.
- **Loose Coupling:** Classes depend on abstractions rather than concrete implementations, promoting flexibility.
- **Lifecycle Management:** DI containers handle the lifecycle of dependencies, optimizing resource usage.
- **Testability:** With DI, classes can be tested independently using mock implementations of dependencies.

---

### **3. Implementing DI in ASP.NET Core**

DI in ASP.NET Core revolves around three main service lifetimes:

- **Transient:** Created each time they are requested. Ideal for lightweight, stateless services.
- **Scoped:** Created once per request. Commonly used for database contexts.
- **Singleton:** Created once and shared throughout the application’s lifecycle. Suitable for shared state or configuration objects.

#### **Example: Configuring DI in a Logistics Application**

1. **Registering Services in Program.cs**

   ```csharp
   builder.Services.AddScoped<IShipmentService, ShipmentService>();
   builder.Services.AddTransient<ILogisticsApiClient, LogisticsApiClient>();
   builder.Services.AddSingleton<IConfiguration>(provider =>
       provider.GetRequiredService<IConfiguration>());
   ```

2. **Injecting Services into a Controller**

   ```csharp
   public class ShipmentsController : ControllerBase
   {
       private readonly IShipmentService _shipmentService;

       public ShipmentsController(IShipmentService shipmentService)
       {
           _shipmentService = shipmentService;
       }

       [HttpGet("{id}")]
       public async Task<IActionResult> GetShipmentDetails(int id)
       {
           var shipment = await _shipmentService.GetShipmentDetailsAsync(id);
           return Ok(shipment);
       }
   }
   ```

---

### **4. Best Practices for DI Usage**

#### **4.1 Registering Services**

- Register abstractions with their concrete implementations.
- Use lifetimes appropriately: scoped for database contexts, transient for lightweight services, and singleton for configurations.

#### **4.2 Avoiding Service Locator Pattern**

- Avoid directly resolving services from the DI container (e.g., `provider.GetService<T>()`), as it introduces hidden dependencies and reduces testability.

#### **4.3 Constructor Injection**

- Use constructor injection to declare dependencies explicitly.
- Avoid injecting too many dependencies in a single class (more than 4-5 suggests a violation of the Single Responsibility Principle).

#### **4.4 Scoped Services in Background Tasks**

- Avoid using scoped services in singleton components. Use a scoped factory pattern to resolve scoped services in background tasks.

#### **4.5 Modular Registration**

- Use extension methods to group related service registrations, improving maintainability.

   ```csharp
   public static class ServiceCollectionExtensions
   {
       public static IServiceCollection AddLogisticsServices(this IServiceCollection services)
       {
           services.AddScoped<IShipmentService, ShipmentService>();
           services.AddTransient<ILogisticsApiClient, LogisticsApiClient>();
           return services;
       }
   }
   ```

   ```csharp
   builder.Services.AddLogisticsServices();
   ```

---

### **5. Use Cases in Logistics Applications**

#### **5.1 Shipment Tracking System**

A shipment tracking system needs to fetch data from multiple carriers’ APIs and store it in a database. DI simplifies managing these dependencies.

- **Interfaces and Implementations**

   ```csharp
   public interface ICarrierApiClient
   {
       Task<ShipmentDetails> GetShipmentDetailsAsync(string trackingNumber);
   }

   public class CarrierApiClient : ICarrierApiClient
   {
       private readonly HttpClient _httpClient;

       public CarrierApiClient(HttpClient httpClient)
       {
           _httpClient = httpClient;
       }

       public async Task<ShipmentDetails> GetShipmentDetailsAsync(string trackingNumber)
       {
           // Call external carrier API
           var response = await _httpClient.GetAsync($"/track/{trackingNumber}");
           response.EnsureSuccessStatusCode();
           return await response.Content.ReadAsAsync<ShipmentDetails>();
       }
   }
   ```

- **DI Registration**

   ```csharp
   builder.Services.AddHttpClient<ICarrierApiClient, CarrierApiClient>(client =>
   {
       client.BaseAddress = new Uri("https://api.carrier.com");
   });
   ```

- **Controller Usage**

   ```csharp
   public class ShipmentsController : ControllerBase
   {
       private readonly ICarrierApiClient _carrierApiClient;

       public ShipmentsController(ICarrierApiClient carrierApiClient)
       {
           _carrierApiClient = carrierApiClient;
       }

       [HttpGet("track/{trackingNumber}")]
       public async Task<IActionResult> TrackShipment(string trackingNumber)
       {
           var shipment = await _carrierApiClient.GetShipmentDetailsAsync(trackingNumber);
           return Ok(shipment);
       }
   }
   ```

#### **5.2 Order Processing System**

In an order processing system, services for managing orders, inventory, and payments depend on each other. DI simplifies wiring these dependencies.

- **Interface for Order Service**

   ```csharp
   public interface IOrderService
   {
       Task ProcessOrderAsync(int orderId);
   }
   ```

- **Implementation of Order Service**

   ```csharp
   public class OrderService : IOrderService
   {
       private readonly IInventoryService _inventoryService;
       private readonly IPaymentGateway _paymentGateway;

       public OrderService(IInventoryService inventoryService, IPaymentGateway paymentGateway)
       {
           _inventoryService = inventoryService;
           _paymentGateway = paymentGateway;
       }

       public async Task ProcessOrderAsync(int orderId)
       {
           // Validate inventory
           await _inventoryService.ReserveInventoryAsync(orderId);

           // Process payment
           await _paymentGateway.ProcessPaymentAsync(orderId);

           // Additional order processing logic
       }
   }
   ```

- **DI Registration**

   ```csharp
   builder.Services.AddScoped<IOrderService, OrderService>();
   builder.Services.AddScoped<IInventoryService, InventoryService>();
   builder.Services.AddTransient<IPaymentGateway, PaymentGateway>();
   ```

---

### **6. Conclusion**

Dependency Injection is a vital practice for building scalable, maintainable, and testable ASP.NET Core applications. By leveraging DI effectively, logistics systems can achieve modularity and flexibility, ensuring seamless integration between services like shipment tracking, order management, and carrier APIs. Following best practices for DI usage results in applications that are robust, extensible, and prepared for future challenges.

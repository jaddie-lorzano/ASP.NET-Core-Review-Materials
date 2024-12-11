# Handout: Best Practices for ASP.NET Core Application Development

## **Logging and Monitoring**

### **1. Introduction to Logging and Monitoring**

Logging and monitoring are essential components of any modern web application, ensuring visibility into application performance, identifying potential issues, and aiding in troubleshooting. In a logistics application, where real-time operations such as shipment tracking and order management are critical, effective logging and monitoring practices can significantly enhance system reliability and customer satisfaction.

ASP.NET Core provides built-in support for logging and integrates seamlessly with third-party monitoring tools, allowing developers to capture, analyze, and act on key application metrics.

---

### **2. Key Principles of Logging**

#### **2.1 Use Structured Logging**

Structured logging captures log data in a format that can be easily queried and analyzed. Instead of plain text, use structured formats like JSON to ensure compatibility with monitoring tools such as Elasticsearch or Azure Monitor.

#### **2.2 Log Levels**

Leverage log levels to categorize log messages based on their severity:

- **Trace**: Detailed diagnostic information.
- **Debug**: Information for debugging purposes.
- **Information**: General application flow.
- **Warning**: Potential issues that might require attention.
- **Error**: Recoverable errors.
- **Critical**: Application crashes or severe issues.

#### **2.3 Avoid Over-Logging**

Log meaningful information without flooding the logs. Over-logging can lead to higher storage costs and make it difficult to identify critical issues.

#### **2.4 Sensitive Data Handling**

Ensure no sensitive information, such as user passwords or payment details, is logged. Use redaction mechanisms or avoid logging such fields entirely.

---

### **3. ASP.NET Core Logging Framework**

#### **3.1 Built-In Logging Providers**

ASP.NET Core supports various logging providers:

- **Console**: Outputs logs to the console during development.
- **Debug**: Writes logs to the debugging output.
- **EventLog**: Logs to the Windows Event Log.
- **Azure Application Insights**: Integrates with Azure for application monitoring.

#### **3.2 Configuring Logging in `appsettings.json`**

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "System": "Warning"
    }
  }
}
```

This configuration defines log levels for the application and framework components.

#### **3.3 Adding Logging to Controllers**

```csharp
public class ShipmentsController : ControllerBase
{
    private readonly ILogger<ShipmentsController> _logger;

    public ShipmentsController(ILogger<ShipmentsController> logger)
    {
        _logger = logger;
    }

    [HttpGet("{id}")]
    public IActionResult GetShipment(int id)
    {
        _logger.LogInformation("Fetching shipment details for ID: {Id}", id);

        // Business logic...

        return Ok();
    }
}
```

This example demonstrates structured logging for a shipment tracking API endpoint.

---

### **4. Monitoring and Observability**

#### **4.1 Metrics and Alerts**

Metrics provide insights into application performance, while alerts notify developers of potential issues.

In a logistics application:

- Track shipment processing times.
- Monitor order fulfillment rates.
- Alert on high API response times or failed database queries.

#### **4.2 Distributed Tracing**

Distributed tracing tracks requests across multiple services, providing visibility into workflows and identifying bottlenecks.

**Example Tool:**

- **Jaeger** or **Zipkin** for open-source tracing.
- **Azure Monitor** for integrated Azure tracing.

#### **4.3 Centralized Log Management**

Centralized logging aggregates logs from multiple services into a single location for analysis.

**Example Tools:**

- **Elasticsearch, Logstash, Kibana (ELK)**
- **Azure Log Analytics**
- **Datadog**

---

### **5. Use Cases in Logistics Applications**

#### **5.1 Shipment Tracking**

- **Scenario:** Logs each shipment update (e.g., "Shipment #12345 updated to 'In Transit'").
- **Implementation:** Use structured logging with an external provider to monitor shipment status changes.
- **Benefit:** Improves visibility and allows proactive issue resolution if delays occur.

#### **5.2 Order Management**

- **Scenario:** Logs order creation, updates, and errors during processing.
- **Implementation:** Include context information like Order ID, timestamps, and user ID in logs.
- **Benefit:** Facilitates troubleshooting and ensures accurate order fulfillment.

#### **5.3 API Performance Monitoring**

- **Scenario:** Monitor API response times and error rates.
- **Implementation:** Configure alerts for latency exceeding thresholds.
- **Benefit:** Enhances customer experience by maintaining responsive services.

---

### **6. Advanced Logging Techniques**

#### **6.1 Custom Logging Providers**

Create custom providers for specialized logging requirements.

```csharp
public class CustomLogProvider : ILoggerProvider
{
    public ILogger CreateLogger(string categoryName)
    {
        return new CustomLogger();
    }

    public void Dispose() { }
}

public class CustomLogger : ILogger
{
    public IDisposable BeginScope<TState>(TState state) => null;
    public bool IsEnabled(LogLevel logLevel) => true;

    public void Log<TState>(LogLevel logLevel, EventId eventId, TState state, Exception exception, Func<TState, Exception, string> formatter)
    {
        // Custom logic here
    }
}
```

#### **6.2 Correlation IDs**

Track individual requests using a unique Correlation ID.

```csharp
public class CorrelationIdMiddleware
{
    private readonly RequestDelegate _next;

    public CorrelationIdMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        context.Response.Headers.Add("X-Correlation-ID", Guid.NewGuid().ToString());
        await _next(context);
    }
}
```

---

### **7. Best Practices**

1. **Integrate Third-Party Tools**: Leverage tools like Azure Monitor or Datadog for comprehensive monitoring.
2. **Log Contextual Data**: Include relevant details (e.g., user IDs, request URLs) for troubleshooting.
3. **Monitor in Real-Time**: Use dashboards to observe application health in real-time.
4. **Automate Alerts**: Set up alerts for critical metrics to ensure rapid response.
5. **Optimize Storage**: Retain logs for the required duration to balance cost and compliance.

---

### **8. Conclusion**

Logging and monitoring are critical to ensuring the reliability and performance of ASP.NET Core applications, especially in dynamic domains like logistics. By adopting structured logging, implementing effective monitoring strategies, and using advanced techniques, organizations can maintain robust systems, minimize downtime, and deliver exceptional user experiences.

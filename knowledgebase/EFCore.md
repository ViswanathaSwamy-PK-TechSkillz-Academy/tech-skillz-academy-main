# EF Core

## 03-Jun

The error you encountered is due to the fact that `SupportTicketDbContext` is registered with a scoped lifetime, but `Worker` is registered with a singleton lifetime. Since scoped services cannot be consumed by singletons, this causes a conflict.

To resolve this issue, you can create a scope within the `Worker` class each time you need to use the `SupportTicketDbContext`. This can be done by using the `IServiceProvider` to create a scope and resolve the `SupportTicketDbContext` within that scope.

Here's how you can adjust your `Worker` class:

### Step 1: Modify `Program.cs`

Make sure `SupportTicketDbContext` is registered as a scoped service, and add `IServiceProvider` to your worker class.

```csharp
using Microsoft.EntityFrameworkCore;
using TicketsStorage.Worker;
using TicketsStorage.Worker.Data;

var builder = Host.CreateApplicationBuilder(args);

builder.AddServiceDefaults();
builder.AddAzureQueueClient("QueueConnectionName");

// Add services to the container.
builder.Services.AddDbContext<SupportTicketDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("sqldata")));

builder.Services.AddHostedService<Worker>();

var host = builder.Build();
host.Run();
```

### Step 2: Modify the `Worker` class to create scopes

Update your `Worker` class to use `IServiceProvider` for creating scopes to resolve `SupportTicketDbContext`.

```csharp
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using System.Text;
using System.Text.Json;
using TicketsStorage.Worker.Data;
using TicketsStorage.Worker.Data.Dtos;

namespace TicketsStorage.Worker
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private readonly QueueServiceClient _client;
        private readonly IServiceProvider _serviceProvider;

        public Worker(ILogger<Worker> logger, QueueServiceClient client, IServiceProvider serviceProvider)
        {
            _logger = logger ?? throw new ArgumentNullException(nameof(logger));
            _client = client ?? throw new ArgumentNullException(nameof(client));
            _serviceProvider = serviceProvider ?? throw new ArgumentNullException(nameof(serviceProvider));
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            _logger.BeginScope("Worker is starting.");

            var queueClient = _client.GetQueueClient("tickets");
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(
                    maxMessages: 25, cancellationToken: stoppingToken);

                foreach (var message in messages)
                {
                    _logger.LogInformation("Message from queue: {Message}", message.MessageText);

                    // Decode the base64-encoded message
                    string decodedMessage = Encoding.UTF8.GetString(Convert.FromBase64String(message.MessageText));

                    // Deserialize the JSON message into a SupportTicketDto object
                    SupportTicketDto supportTicketDto = JsonSerializer.Deserialize<SupportTicketDto>(decodedMessage)!;

                    // Now you can use the supportTicket object as needed
                    _logger.LogInformation("Received support ticket with title: {Title}", supportTicketDto.Title);

                    // Create a new scope
                    using (var scope = _serviceProvider.CreateScope())
                    {
                        var dbContext = scope.ServiceProvider.GetRequiredService<SupportTicketDbContext>();

                        // Update the support ticket in the database
                        var supportTicket = await dbContext.Tickets.FindAsync(supportTicketDto.Id);
                        if (supportTicket != null)
                        {
                            supportTicket.AssignedToName = $"Name-{Guid.NewGuid():X}";
                            supportTicket.AssignedAt = DateTime.Now;
                            dbContext.Tickets.Update(supportTicket);
                            await dbContext.SaveChangesAsync();
                        }
                    }

                    await queueClient.DeleteMessageAsync(
                        message.MessageId,
                        message.PopReceipt,
                        cancellationToken: stoppingToken);
                }

                _logger.LogInformation("Worker is waiting for more messages.");
                await Task.Delay(TimeSpan.FromSeconds(15), stoppingToken);
            }
        }
    }
}
```

### Explanation:

1. **Program.cs**: The `SupportTicketDbContext` is registered as a scoped service. The `Worker` service is added as a hosted service.
2. **Worker.cs**: The `Worker` class accepts `IServiceProvider` as a parameter in the constructor. Within the `ExecuteAsync` method, a scope is created using `_serviceProvider.CreateScope()` to resolve the `SupportTicketDbContext`. This ensures that each time the database context is used, it is created within a proper scope, avoiding the singleton consumption issue.

With these changes, your `Worker` service should be able to properly resolve and use `SupportTicketDbContext` within its processing loop.

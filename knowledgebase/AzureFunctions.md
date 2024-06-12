# Azure Functions

## 12-Jun

Yes, you can use strongly typed configuration in Azure Functions to simplify and centralize your configuration settings. This approach uses a configuration class to map the settings from environment variables or other configuration sources.

Here’s how you can achieve this:

### 1. Define a Configuration Class

Create a class to represent your configuration settings.

```csharp
public class FunctionSettings
{
    public string BlobConnectionString { get; set; }
    public string CosmosDbEndpoint { get; set; }
    public string CosmosDbKey { get; set; }
    public string CosmosDbDatabaseId { get; set; }
    public string CosmosDbContainerId { get; set; }
    public string FormRecognizerEndpoint { get; set; }
    public string FormRecognizerKey { get; set; }
}
```

### 2. Add Configuration to Your Azure Function

Configure the Azure Function to bind configuration settings to the `FunctionSettings` class.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure;
using Azure.AI.FormRecognizer.DocumentAnalysis;
using Azure.Cosmos;
using Azure.Storage.Blobs;
using Microsoft.Azure.ServiceBus;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Options;

public class QueueTriggerFunction
{
    private readonly FunctionSettings _settings;
    private readonly BlobServiceClient _blobServiceClient;
    private readonly CosmosClient _cosmosClient;
    private readonly DocumentAnalysisClient _formRecognizerClient;

    public QueueTriggerFunction(IOptions<FunctionSettings> settings)
    {
        _settings = settings.Value;
        _blobServiceClient = new BlobServiceClient(_settings.BlobConnectionString);
        _cosmosClient = new CosmosClient(_settings.CosmosDbEndpoint, _settings.CosmosDbKey);
        _formRecognizerClient = new DocumentAnalysisClient(new Uri(_settings.FormRecognizerEndpoint), new AzureKeyCredential(_settings.FormRecognizerKey));
    }

    [FunctionName("QueueTriggerFunction")]
    public async Task Run([ServiceBusTrigger("image-processing-queue", Connection = "ServiceBusConnection")]Message message, ILogger log)
    {
        try
        {
            // Extract message content
            string messageBody = System.Text.Encoding.UTF8.GetString(message.Body);
            dynamic data = Newtonsoft.Json.JsonConvert.DeserializeObject(messageBody);
            string imageName = data.imageName;
            string language = data.language;

            // Fetch the image from Blob Storage
            BlobContainerClient containerClient = _blobServiceClient.GetBlobContainerClient("incoming-images");
            BlobClient blobClient = containerClient.GetBlobClient(imageName);
            var blobStream = await blobClient.OpenReadAsync();

            // Perform OCR using Form Recognizer
            AnalyzeDocumentOperation operation = await _formRecognizerClient.StartAnalyzeDocumentAsync("prebuilt-read", blobStream);
            await operation.WaitForCompletionAsync();
            AnalyzeResult result = operation.Value;

            // Extract text and metadata
            string extractedText = string.Join(" ", result.Pages.SelectMany(page => page.Lines).Select(line => line.Content));
            var metadata = new { imageName, language, extractedText, timestamp = DateTime.UtcNow };

            // Store the results in Cosmos DB
            Container container = _cosmosClient.GetContainer(_settings.CosmosDbDatabaseId, _settings.CosmosDbContainerId);
            await container.CreateItemAsync(metadata, new PartitionKey(imageName));

            // Move the image to the processed container
            BlobContainerClient processedContainerClient = _blobServiceClient.GetBlobContainerClient("processed-images");
            BlobClient processedBlobClient = processedContainerClient.GetBlobClient(imageName);
            await processedBlobClient.StartCopyFromUriAsync(blobClient.Uri);
            await blobClient.DeleteAsync();

            log.LogInformation($"Processed image: {imageName}");
        }
        catch (Exception ex)
        {
            log.LogError($"Error processing image: {ex.Message}");
            throw; // This will trigger a retry and eventually move to the dead-letter queue if configured
        }
    }
}
```

### 3. Bind Configuration Settings in `Startup.cs`

If you are using a startup class for configuring your function, you can bind the settings there.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(YourNamespace.Startup))]

namespace YourNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            var config = new ConfigurationBuilder()
                .SetBasePath(Environment.CurrentDirectory)
                .AddJsonFile("local.settings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build();

            builder.Services.Configure<FunctionSettings>(config.GetSection("FunctionSettings"));
        }
    }
}
```

### 4. `local.settings.json` for Local Development

For local development, you can define your settings in the `local.settings.json` file.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet",
    "ServiceBusConnection": "<your-service-bus-connection-string>"
  },
  "FunctionSettings": {
    "BlobConnectionString": "<your-blob-connection-string>",
    "CosmosDbEndpoint": "<your-cosmos-db-endpoint>",
    "CosmosDbKey": "<your-cosmos-db-key>",
    "CosmosDbDatabaseId": "<your-cosmos-db-database-id>",
    "CosmosDbContainerId": "<your-cosmos-db-container-id>",
    "FormRecognizerEndpoint": "<your-form-recognizer-endpoint>",
    "FormRecognizerKey": "<your-form-recognizer-key>"
  }
}
```

### 5. Configuration in Azure

When deploying to Azure, you can set these configuration values in the Azure portal under the Application Settings for your Function App. These settings will override the local configuration.

By using strongly typed configuration, you make your configuration more manageable and less error-prone. This approach centralizes the configuration settings, making them easier to maintain and access within your functions.

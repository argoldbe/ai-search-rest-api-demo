# ai-search-rest-api-demo
Azure AI Search REST API Demo

## Overview

Sample Bruno collection for managing [Azure AI Search](https://learn.microsoft.com/en-us/azure/search/search-what-is-azure-search) indexes using the [pull model](https://learn.microsoft.com/en-us/azure/search/search-indexer-overview) via REST API including create, delete, search and list operations.

## Dependencies

### Required Azure Services

Before using this Bruno collection, you'll need the following Azure services:

1. **Azure AI Search Service**
   - A provisioned Azure AI Search service
   - Admin API key access

2. **Azure Storage Account**
   - A storage account with a blob container (`data` is the default )
   - Sample documents in supported formats for indexing
   - Storage account key or managed identity access

3. **Azure OpenAI Service** (Optional - for vector search capabilities)
   - Azure OpenAI service with text embedding model deployment
   - Recommended model: `text-embedding-ada-002`

### Sample Data Requirements

Your storage account should contain sample documents in any / all of the following supported formats:
- **PDF files** (`.pdf`)
- **Word documents** (`.docx`)
- **Email messages** (`.eml`, `.msg`)
- **Excel spreadsheets** (`.xlsx`)

These documents will be used as the data source for creating the search index. There are two indexers -

- The first indexer, [Create indexer](/bruno/Create/Create%20indexer.bru), indexes pdf, docx, eml and msg files and uses the defined [skillset](/bruno/Create/Create%20skillset.bru) which does chunking and creates embedding using AI Search [integrated vectorization](https://learn.microsoft.com/en-us/azure/search/vector-search-integrated-vectorization).
- The second indexer, [Create indexer sheet](/bruno/Create/Create%20indexer%20sheet.bru), indexes xlsx and xlsm files and does not use the skillset.

## Setup

### Environment Configuration

1. Copy the `bruno/.env.template` file to create your local envfironment file:

```bash
cp .env.template .env
```

2. Update the following variables in your `.env` file:

```properties
SUBSCRIPTION_ID="<Your Azure Subscription ID>"
AI_SEARCH_ENDPOINT="<Your AI Search Service Endpoint>"
AI_SEARCH_API_KEY="<Your AI Search API Key>"
AZURE_OPEN_AI_ENDPOINT="<Your Azure OpenAI Endpoint>"
AZURE_OPEN_AI_API_KEY="<Your Azure OpenAI API Key>"
```

#### Getting Your Azure Values:

- **SUBSCRIPTION_ID**: Your Azure subscription ID (found in the Azure portal)
- **AI_SEARCH_ENDPOINT**: Your Azure AI Search service endpoint (e.g., `https://your-search-service.search.windows.net`)
- **AI_SEARCH_API_KEY**: Admin key for your Azure AI Search service (found in the Keys section of your search service)
- **AZURE_OPEN_AI_ENDPOINT**: Your Azure OpenAI service endpoint (e.g., `https://your-openai-service.openai.azure.com`)
- **AZURE_OPEN_AI_API_KEY**: API key for your Azure OpenAI service (found in the Keys section of your OpenAI service)

### Bruno Environment Configuration

3. Update the Bruno environment file `environments/Dev.bru` with your Azure resource names. This can be done in an editor or in the Bruno UI.

```plaintext
vars {
  apiVersion: 2024-05-01-preview
  endpoint: {{process.env.AI_SEARCH_ENDPOINT}}
  indexName: package
  resourceGroupName: your-resource-group-name
  storageAccountName: your-storage-account-name
  containerName: your-storage-container-name
  subscriptionId: {{process.env.SUBSCRIPTION_ID}}
  vectorizerApiKey: {{process.env.AZURE_OPEN_AI_API_KEY}}
  vectorizerDeploymentId: text-embedding-ada-002
  vectorizerResourceUri: {{process.env.AZURE_OPEN_AI_ENDPOINT}}
}
```

Update the following values:
- **resourceGroupName**: The name of your Azure resource group containing your AI Search service
- **storageAccountName**: The name of your Azure Storage account that will be used as the data source for indexing

#### Finding Your Azure Resource Names:

- **Resource Group Name**: Found in the Azure portal under "Resource groups" or in the overview section of your AI Search service
- **Storage Account Name**: Found in the Azure portal under "Storage accounts" or create a new one if needed for your data source
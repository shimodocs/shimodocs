# AI Configuration

[← ShimoDocs Suite Deployment Documentation](../../README.md)

AI configuration is used to connect ShimoDocs Suite with base models, image models, online search, and embedding services. After completing the configuration, features in ShimoDocs Suite such as AI chat, content generation, image processing, and knowledge retrieval can access the corresponding services.

## 1. Understand the Four Types of Capabilities Before Configuration

The purpose of these four configuration types is different, and you do not necessarily need to configure all of them. Please configure according to the features you plan to enable in ShimoDocs Suite.

| Configuration Type | Purpose | Required Configuration |
| --- | --- | --- |
| Base Model | Handles chat, writing, summarization, rewriting, Q&A, and other text or multi-modal tasks | Usually required when using AI features |
| Image Model | Generate or edit images | Only required when using image generation or editing features |
| Online Search | Obtain information from external search services to supplement model references | Only required when using online retrieval features |
| Embeddings | Convert text into vectors for knowledge base retrieval, semantic search, and similar functions | Only required when using knowledge retrieval or vector search features |

> [!TIP]
>
> Online search is usually a standalone search service, different from the built-in online capabilities of the base model.

## 2. Access AI Configuration

1. Log in to the **MDP Operations Platform**.
2. At the top, select **ShimoDocs Suite**.
3. In the left sidebar, select **Tenant Management**.
4. Locate the **AI Configuration** card.
5. Click the card to go to the “AI Model and Search Configuration” page.

## 3. First, select the model service provider to connect

Please first confirm the model service you plan to use, and then go to the corresponding configuration section.

| Model Type | Service Provider |
| --- | --- |
| Base Model | Providers compatible with the OpenAI Responses protocol |
| Image Model | Providers compatible with the OpenAI Image protocol |
| Internet Search Engine Model | Currently only supports Volcano Engine |
| Embedding Model | Providers compatible with the OpenAI Embedding protocol |

## 4. AI Model Configuration

This section is for configuring GPT-related services. Please have the engineering team confirm whether to use OpenAI official services, Azure OpenAI, proxy services, or other compatible interfaces, as the request address and model ID may vary depending on the connection method.

### 4.1 Base Model

Base models are used for conversation, content generation, summarization, rewriting, and multimodal understanding functions.

#### Provider Configuration

| Configuration Item | Example Value | Description |
| --- | --- | --- |
| Provider | Select OpenAI (or compatible with OpenAI Responses protocol) | Select OpenAI (or compatible with OpenAI Responses protocol) |
| Request URL / Base URL | https://myai.com/v1 | Choose your own AI gateway address compatible with the OpenAI Responses protocol |
| API Key | sk-I••••haTO | The API key assigned to you by the AI gateway |
| Default Model | gpt-5.5 | Model compatible with the OpenAI Responses protocol |

> [!TIP]
> The model providers configured here need to support streaming mode. As the client AI, ShimoDocs will always send `stream: true` when requesting the model provider. If the model provider does not support streaming mode, the request will fail.

#### Model Configuration

| Configuration Item | Example Value | Development Notes |
| --- | --- | --- |
| Status | Enabled | Needs to be enabled |
| Model ID | gpt-5.5 | Valid model ID |
| Model Name | gpt-5.5 | Should match the model ID |
| Context Window | 1024000 | Fill in according to actual situation |
| Text Input | Enabled | Needs to be enabled |
| Image Input | Enabled | Needs to be enabled |

### 4.2 Image Model

Image models are used for image generation or image editing. Please fill in the models and features currently supported by the current version.

| Configuration | Example Value | Engineering Notes |
| --- | --- | --- |
| Status | Enabled | Needs to be enabled |
| Provider | OpenAI (or OpenAI-compatible image protocol) | OpenAI (or OpenAI-compatible image protocol) |
| Model Name | gpt-image-2 | Needs to be compatible with OpenAI image protocol |
| Request URL / Base URL | https://myai.com/v1 | Choose a custom AI gateway address compatible with the OpenAI Responses protocol |
| API Key | sk-I••••haTO | This API key is assigned to you by the AI gateway |
| Function | Image generation, Image editing | Keep default image generation, image editing |

> [!TIP]
>
> Currently, only the OpenAI Image API protocol is supported

### 4.3 Web Search Model

Web search currently only supports configuring the Volcano Engine.

| Configuration Item | Example Value | Engineering Notes |
| --- | --- | --- |
| Status | Enabled | Enable according to actual needs. If enabled, all other items in the current configuration group need to be completed |
| Service Provider | Volcano Engine | Currently only Volcano Engine is supported |
| API Endpoint | https://open.feedcoopapi.com/search_api/web_search | Default web search address for Volcano Engine |
| API Key | mCmh•••••••• | Obtain from the web search service provider |
| Timeout Setting | 120 seconds | If a single network search request exceeds this time, it will fail. It is recommended to keep it at 120 seconds. |

### 4.4 Embedding Model

Embedding models are used for knowledge base retrieval and semantic search. The model ID and dimensions must match the actual vector output.

| Configuration Item | Example Value | Development Notes |
| --- | --- | --- |
| Service Provider | OpenAI (or OpenAI-compatible embedding models) | OpenAI (or OpenAI-compatible embedding models) |
| Base URL | https://myai.com/v1 | Choose a custom AI gateway address compatible with the OpenAI Responses protocol |
| API Key | ak-•••••••• | Obtain from the embedding model provider |
| Embedding Model | qwen3-embedding:4b | Model ID |
| Dimension | Integer value | Dimension is related to the embedding model; you can consult the provider for dimension parameters |

| Development Confirmation Items | Content |
| --- | --- |
| Supported Embedding Models | OpenAI (or OpenAI compatible embedding models) |
| Recommended Vector Dimension | Related to the embedding model |
| Is it necessary to reconstruct vector data for different dimensions | Yes |

> [!TIP]
>
> Currently, only OpenAI Embedding API protocol is supported

### 4.5 GPT Configuration Completion Check

| Verification Item | Expected Result | Actual Result |
| --- | --- | --- |
| Basic Model Conversation | Enter a simple question in the AI session | The model returns the corresponding result |
| Long Text Processing | Output long text | The model returns corresponding results based on the content of the long text |
| Image Input or Image Processing | Input images for recognition | Can return the recognized content |
| Internet Search | Prompt to search for flight or train ticket information | Can return flight or train ticket results |
| Embedding Vectorization | Use keywords for in-site AI search | Can return the expected matching content |

## 5. Business Significance of Each Configuration Item

This section provides a unified explanation of the purpose of each configuration item on the page. During the initial setup, you can fill in according to the vendor templates mentioned earlier, and then return to this section to confirm whether each field meets the actual business requirements.

### 5.1 Basic Model Vendor Configuration Items

| Configuration Item | Business Significance | Common Effects of Incorrect Input | Required | 
| --- | --- | --- | --- |
| Provider | Indicates which model adaptation method the system should use. Even if two service interfaces are similarly compatible, the provider option may determine the request format, authentication method, and result parsing method. | May fail to save, request format mismatch, or response cannot be parsed. | Yes |
| Request URL / Base URL | The entry address that ShimoDocs Suite accesses when sending requests to the model service. | If the address is incorrect, the model cannot be reached; if the path level is wrong, the interface may show as nonexistent. | Yes |
| API Key | Credential used by the model service to identify the caller and verify permissions. | Usually prompts authentication failure when the credential is incorrect, expired, or has insufficient permissions. | Yes |
| Default Model | The model that the system prioritizes calling when the business function does not explicitly specify a model. | If not set or set to an unavailable model, some AI features may be unusable. | Yes |

### 5.2 Basic Model Configuration Items

| Configuration Item | Business Significance | Common Consequences of Incorrect Input | Required |
| --- | --- | --- | --- |
| Status | Controls whether the model is allowed to be called. After ShimoDocs Suite is closed, the configuration can be retained, but business operations usually cannot continue to use the model. | Even if the model is configured correctly, if the status is off, the business may still show it as unavailable. | Yes |
| Model ID | The name or unique identifier of the model recognized by the model service interface. | If it does not match the server name, it usually prompts that the model does not exist. | Yes |
| Model Name | The name displayed to administrators on the operations platform, used to distinguish different models. | If the name is duplicated or unclear, it is easy to select the wrong model; whether it participates in actual requests is confirmed by engineering. | Yes |
| Context Window | The total amount of information the model can process in a single request, which usually affects the length of input text, conversation history, and output space. | Setting it larger than the model's actual capacity may cause request failures; setting it too small may result in content being truncated or unable to be submitted. | Yes |
| Text Input | Indicates whether the model can accept text content. | If incorrectly set to off, text-related functions may not be selectable or callable for the model. | Yes |
| Image Input | Indicates whether the base model can understand images uploaded by users; this is a multimodal input feature and is different from generating images. | Enabling this feature for models that do not support images may lead to request failures; if turned off, the image understanding function will not be available. | Yes |

### 5.3 Image Model Configuration Options

| Configuration Item | Business Significance | Common Effects of Incorrect Settings | Required |
| --- | --- | --- | --- |
| Status | Controls whether the image model can be called by image generation or editing functions. | If the status is off, the related image functions cannot use this model. | Yes |
| Service Provider | Determines the interface adaptation method used for image requests. | Improper selection may cause request parameters or return formats to be incompatible. | Yes |
| Model Name / Model ID | Specifies the image model to actually be called. Whether this field is a display name or request ID should be confirmed by engineering. | Inconsistency between the name and the server may indicate that the model does not exist. | Yes |
| Base URL | The service address where image generation or editing requests are sent. | If the address or path is incorrect, the image service cannot be called. | Yes |
| API Key | The authentication credential used when calling the image service. | Incorrect, expired, or insufficient permissions will result in authentication failure. | Yes |
| Feature | Declares the image features supported by the model, such as image generation, image editing, etc. | If a feature unsupported by the model is configured, the business entry may be visible, but the call will fail. | Yes |

Note: Currently, only the OpenAI image API protocol is supported

### 5.4 Internet Search Configuration

| Configuration Item | Business Significance | Common Impact (if incorrect) | Required |
| --- | --- | --- | --- |
| Status | Controls whether ShimoDocs Suite can call the current search service. | When the status is off, the model may still be available, but internet search results cannot be obtained. | No |
| Service Provider | Specifies the type of search service used and its interface adaptation method. | If chosen incorrectly, requests and result parsing may be incompatible. | No |
| Interface Address | The server endpoint accessed when initiating a search request. | If the address is incorrect, internet functionality may time out or fail to connect. | No |
| API Key | The authentication credential used by the search service. | If incorrect or insufficient in permissions, search requests will be denied. | No |
| Timeout Settings | The maximum waiting time for a single search; if exceeded, the system will stop waiting and treat it as a failure or no result. | Setting too short will cause frequent timeouts; setting too long will increase user waiting time. | No |

### 5.5 Embedding Configuration

Embedding models do not necessarily need to be enabled, but if not enabled, document content cannot be vectorized, and the system will be unable to handle questions related to the user knowledge base.

| Configuration Item | Business Significance | Common Consequences of Incorrect Input | Required |
| --- | --- | --- | --- |
| Base URL | The service address used for text vectorization requests. | If the address is incorrect, vector data cannot be generated or updated. | No |
| API Key | The authentication credential used by the embedding service. | An incorrect, expired, or unauthorized key causes vectorization to fail. | No |
| Embedding Model | The model ID used to convert text into vectors. | If the model does not exist or does not match the configured service, vectors cannot be generated. | No |
| Dimension | The vector length generated for each text input; it must match the model output and vector-storage configuration. | If the dimension is inconsistent, writes or retrievals can fail. Changing it may require existing vectors to be regenerated. | No |

Note: Currently, only the OpenAI Embedding API protocol is supported.

## 6. Recommended Configuration Order

To reduce repetition, it is recommended to configure in the following order:

1. First, confirm which AI features in ShimoDocs Suite need to be enabled.
2. Select a base model that meets the protocol requirements.
3. Configure the provider and add at least one base model.
4. Set the verified available model as the default model.
5. Configure the image model according to business needs.
6. Configure web search according to business needs.
7. If using a knowledge base or semantic search, configure embeddings.
8. After saving, verify each function separately; do not determine whether the configuration is successful solely based on the "enabled" status on the page.

## 7. Effective Configuration Rules
| Issues Requiring Engineering Confirmation | Details |
| --- | --- |
| Does the configuration take effect immediately after saving | It does not take effect immediately; you need to wait 1-2 minutes |
| Do you need to restart the service | No need to restart the service |
| Does the new configuration take effect on already opened pages | You need to refresh the current page |
| Priority selection among multiple models | Not supported |
| Will it automatically switch if the default model is unavailable? | Not supported |

## 8. Common Troubleshooting 

| Phenomenon | Common Causes | Troubleshooting Methods |
| --- | --- | --- |
| Failed to connect to model service | Request address, network, certificate, or port configuration issues | Check the service address, DNS port, certificate, and firewall policies | 
| Authentication failed | API Key is incorrect, expired, or lacks permission | Verify that the API Key is correct and has access to the target model or service | 
| Model not found | Model ID does not match the server-side name | Confirm the complete model ID and check case sensitivity and version suffix | 
| Text works but images do not | Model does not support image input, or image input switch is not enabled | Check model capability and input switch | 
| Image feature entry exists but call fails | Feature inconsistent with actual model capability | Verify the generation and editing capabilities supported by the image model |
| Frequent online search timeouts | Search service is slow, network is unstable, or timeout settings are too short | Check network latency, service performance, and timeout settings | 
| Embedding write failure | Output dimensions do not match vector storage configuration | Verify the actual output dimensions of the model and storage configuration | 

## Q&A

1. How to verify if the configuration is effective?

After completing the configuration, you can go to the editor sidebar to start an AI session to verify if the functionality works properly:

- Messages should be able to reply normally
- If an image model is configured, you can send a command like "Generate a Xxx image" and observe whether the command executes correctly
- If online search is configured, you can send a command like "Search today's Beijing weather" and check if the results meet expectations

2. Does it support the /chat/completions endpoint?

Currently not supported. At present, only the OpenAI Responses API protocol is supported. Known official APIs such as Deepseek / Xiaomi Mimo provide support for this protocol. Local deployment solutions like vLLM and Ollama also support the Responses protocol.

# API Testing Hub Integration Example

This example demonstrates how to integrate the API Testing Hub into an e-commerce scenario that fetches product data from an external API.

## Scenario Overview

In this example, we have an e-commerce integration that:
1. Fetches product data from an external API
2. Processes the data
3. Updates inventory in a separate system

We'll modify this scenario to log all API calls through our API Testing Hub.

## Original Scenario

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ 1. Scheduler│────▶│ 2. HTTP Get │────▶│ 3. Iterator │
└─────────────┘     │ Products API│     └─────────────┘
                    └─────────────┘            │
                                               ▼
                                       ┌─────────────┐
                                       │ 4. HTTP Put │
                                       │ Update      │
                                       │ Inventory   │
                                       └─────────────┘
```

## Modified Scenario with API Testing Hub Integration

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ 1. Scheduler│────▶│ 2. API Hub  │────▶│ 3. HTTP Get │────▶│ 4. Iterator │
└─────────────┘     │ Request     │     │ Products API│     └─────────────┘
                    └─────────────┘     └─────────────┘            │
                                                                   ▼
                    ┌─────────────┐                        ┌─────────────┐
                    │ 6. API Hub  │◀───────────────────────│ 5. API Hub  │
                    │ Response    │                        │ Request     │
                    └─────────────┘                        └─────────────┘
                          │                                       │
                          ▼                                       ▼
                    ┌─────────────┐                        ┌─────────────┐
                    │ 7. Router   │                        │ 8. HTTP Put │
                    │ Check Status│                        │ Update      │
                    └─────────────┘                        │ Inventory   │
                                                           └─────────────┘
```

## Implementation Details

### 1. Before the HTTP Get Products API

Add an "HTTP" module to call the API Testing Hub:

```
Method: POST
URL: [Your API Testing Hub Webhook URL]
Headers:
  Content-Type: application/json
Body:
{
  "url": "https://api.example.com/products",
  "method": "GET",
  "headers": "{{toJSON({\"Authorization\": \"Bearer \" + access_token})}}",
  "queryParams": "{{toJSON({\"page\": 1, \"per_page\": 100})}}",
  "body": "",
  "sourceScenario": "E-Commerce Integration",
  "environment": "production",
  "notifyOnError": true
}
```

### 2. Before the HTTP Put Update Inventory

Add another "HTTP" module to call the API Testing Hub:

```
Method: POST
URL: [Your API Testing Hub Webhook URL]
Headers:
  Content-Type: application/json
Body:
{
  "url": "https://inventory.example.com/update/{{currentProduct.id}}",
  "method": "PUT",
  "headers": "{{toJSON({\"Authorization\": \"Bearer \" + inventory_token})}}",
  "queryParams": "{{toJSON({})}}",
  "body": "{{toJSON({\"quantity\": currentProduct.available_quantity, \"status\": currentProduct.status})}}",
  "sourceScenario": "E-Commerce Integration",
  "environment": "production",
  "notifyOnError": true
}
```

### 3. After the HTTP Put (Optional: Log Response)

You can also log the response from the HTTP Put operation:

```
Method: POST
URL: [Your API Testing Hub Webhook URL]
Headers:
  Content-Type: application/json
Body:
{
  "url": "https://api.example.com/response-log",
  "method": "POST",
  "headers": "{{toJSON({\"Content-Type\": \"application/json\"})}}",
  "body": "{{toJSON({\"operation\": \"inventory-update\", \"productId\": currentProduct.id, \"status\": lastModule.statusCode, \"response\": lastModule.body})}}",
  "sourceScenario": "E-Commerce Integration - Response Log",
  "environment": "production",
  "notifyOnError": false
}
```

## Benefits of This Integration

1. **Centralized Logging**: All API calls are logged in a single Google Sheet
2. **Error Monitoring**: Automated notifications when APIs return errors
3. **Performance Tracking**: Response times are tracked over time
4. **Debugging**: Complete request and response details are saved for troubleshooting
5. **Historical Data**: Helps identify patterns in API usage and performance over time

## Advanced Integration Tips

### Conditional Logging

You might want to log only certain API calls based on conditions:

```
Router
├── Route 1: Production Environment
│   Condition: {{environment}} equal to "production"
│   → API Testing Hub Request
├── Route 2: Development Environment with Errors
│   Condition: {{environment}} equal to "development" AND {{lastModule.statusCode}} greater than or equal to 400
│   → API Testing Hub Request
└── Route 3: Default
    No logging
```

### Transforming Data

If your API request or response contains sensitive information, you can transform it before logging:

```
{
  "url": "https://api.example.com/user/{{userId}}",
  "method": "GET",
  "headers": "{{toJSON({\"Authorization\": \"Bearer [REDACTED]\"})}}",
  "queryParams": "{{toJSON({})}}",
  "body": "",
  "sourceScenario": "User Data Integration",
  "environment": "production",
  "notifyOnError": true
}
```

### Error Handling Workflow

You can extend the API Testing Hub integration to create a more robust error handling workflow:

```
API Testing Hub Request
↓
HTTP API Call
↓
API Testing Hub Response Log
↓
Router: Check API Status
├── Route 1: Success (Status Code < 400)
│   → Continue normal flow
└── Route 2: Error (Status Code >= 400)
    → Error Handler Module
    → Retry Logic
    → Fallback Process
    → Alert Notification
```

## Example: Complete Scenario JSON

For a complete example of the modified scenario JSON, see the `examples/ecommerce-integration.json` file in this repository.

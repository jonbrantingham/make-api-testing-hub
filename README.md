# API Testing Hub for Make.com

A reusable API testing and monitoring solution that logs all API requests across your Make.com scenarios.

## Features

- **Centralized Testing**: Test all your APIs from a single place
- **Comprehensive Logging**: Track all API requests, responses, and performance metrics
- **Smart Error Handling**: Proper error handling without failing scenarios
- **Reusable Component**: Use as a standalone tool or integrate into other scenarios
- **Performance Monitoring**: Track response times and error rates
- **Input Validation**: Prevents errors with proper validation before making API calls
- **Flexible Configuration**: Customize headers, methods, and error notification

## Setup Instructions

### 1. Create the Scenario

1. In your Make.com dashboard, create a new scenario
2. Delete any placeholder modules
3. Name your scenario "API Testing Hub"
4. In scenario settings, enable "Make this scenario shareable"
5. Set to run "Instantly" when the webhook is called

### 2. Implement the Scenario

Follow these steps to implement the API Testing Hub:

#### Webhook Entry Point

1. Add a "Custom Webhook" module as the trigger
2. Configure it as an "Instant Webhook"
3. Enable "Determine data structure" and add the provided sample data

```json
{
  "url": "https://api.example.com/endpoint",
  "method": "GET",
  "headers": "{\"Authorization\":\"Bearer token123\"}",
  "body": "",
  "queryParams": "{\"param1\":\"value1\"}",
  "sourceScenario": "Manual Test",
  "notifyOnError": true
}
```

#### Input Validation

See the `scenario-blueprint.json` file in this repository for the detailed configuration of all modules.

#### Google Sheets Setup

1. Create a Google Sheet named "API Test Logs" with these columns:
   - Timestamp
   - Source
   - URL
   - Method
   - Request Headers
   - Request Body
   - Status Code
   - Response Time (ms)
   - Response Body
   - Error Details
   - Environment

2. Connect the Google Sheets modules in the scenario to this sheet.

### 3. Using as a Component

To use the API Testing Hub in another scenario:

1. Add a "Make an API Call" module
2. Set Method to "POST"
3. URL should be the webhook URL from your API Testing Hub scenario
4. In the Headers, add `Content-Type: application/json` 
5. In Body, format like this:

```json
{
  "url": "YOUR_TARGET_API_URL",
  "method": "GET", 
  "headers": "{\"Authorization\": \"Bearer YOUR_TOKEN\"}",
  "queryParams": "{\"param1\": \"value1\"}",
  "body": "YOUR_REQUEST_BODY",
  "sourceScenario": "Name of calling scenario",
  "notifyOnError": true
}
```

## Scenario Blueprint

This repository contains a JSON blueprint for the complete API Testing Hub scenario. You can import this blueprint into Make.com to quickly set up the entire scenario.

The blueprint is available in the `scenario-blueprint.json` file.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

# API Testing Hub Usage Guide

This guide provides detailed instructions for setting up and using the API Testing Hub in different scenarios.

## Table of Contents

1. [Setting Up the API Testing Hub](#setting-up-the-api-testing-hub)
2. [Using as a Standalone Testing Tool](#using-as-a-standalone-testing-tool)
3. [Integrating with Other Scenarios](#integrating-with-other-scenarios)
4. [Advanced Configuration](#advanced-configuration)
5. [Troubleshooting](#troubleshooting)

## Setting Up the API Testing Hub

### Importing from Blueprint

The easiest way to set up the API Testing Hub is to import the provided blueprint:

1. Download the `scenario-blueprint.json` file from this repository
2. In Make.com, go to "Create a new scenario"
3. Click on the three-dot menu and select "Import Blueprint"
4. Upload the `scenario-blueprint.json` file
5. Review and configure the scenario to match your needs

### Manual Setup

If you prefer to set up the scenario manually, follow these steps:

1. In your Make.com dashboard, create a new scenario
2. Delete any placeholder modules
3. Name your scenario "API Testing Hub"
4. Add modules as described in the [README.md](README.md) file

## Using as a Standalone Testing Tool

Once your API Testing Hub is set up, you can use it as a standalone testing tool:

1. Activate the scenario in Make.com
2. Copy the webhook URL provided by Make.com
3. Use the webhook URL to make API requests with tools like Postman, cURL, or directly from your browser

### Example Request

```bash
curl -X POST \
  [YOUR_WEBHOOK_URL] \
  -H 'Content-Type: application/json' \
  -d '{
    "url": "https://api.example.com/users",
    "method": "GET",
    "headers": "{\"Authorization\":\"Bearer YOUR_TOKEN\"}",
    "queryParams": "{\"page\":\"1\",\"per_page\":\"10\"}",
    "body": "",
    "sourceScenario": "Manual Test",
    "notifyOnError": true
  }'
```

### Response Format

Successful responses will have the following structure:

```json
{
  "success": true,
  "statusCode": 200,
  "responseTimeMs": 345,
  "responseBody": {
    // Parsed JSON response from the API
  },
  "timestamp": "2025-03-27T22:34:40Z",
  "logId": "12345"
}
```

Error responses will include error details:

```json
{
  "success": false,
  "statusCode": 404,
  "responseTimeMs": 254,
  "responseBody": {
    "error": "Resource not found"
  },
  "error": "HTTP 404 Error",
  "timestamp": "2025-03-27T22:35:40Z",
  "logId": "12346"
}
```

Validation errors will be reported as:

```json
{
  "success": false,
  "errors": ["URL is required", "Invalid headers JSON format"],
  "timestamp": "2025-03-27T22:36:40Z"
}
```

## Integrating with Other Scenarios

You can integrate the API Testing Hub into your other Make.com scenarios to log all API calls:

### Method 1: Using Make an API Call

1. In your scenario, add a "Make an API Call" module
2. Set the method to POST
3. Enter your API Testing Hub webhook URL
4. Configure the request body to include your API call details

### Method 2: Using HTTP Module

1. In your scenario, add an "HTTP" module
2. Set the method to POST
3. Enter your API Testing Hub webhook URL
4. Configure the request body to include your API call details

### Example Configuration

```json
{
  "url": "{{your_actual_api_url}}",
  "method": "{{your_method}}",
  "headers": "{{toJSON(your_headers_object)}}",
  "queryParams": "{{toJSON(your_query_params)}}",
  "body": "{{your_request_body}}",
  "sourceScenario": "My E-commerce Integration",
  "environment": "production"
}
```

## Advanced Configuration

### Custom Error Notifications

To receive notifications for API errors:

1. Set `notifyOnError` to `true` in your request
2. Add one of the following modules after the "Prepare error notification" module:
   - Email module
   - Slack module
   - Discord module
   - Microsoft Teams module

Configure the notification module with a condition like:
```
{{1.notifyOnError}} equal to true
```

### Environment Tracking

Track which environment your API calls are made from by adding an `environment` parameter to your requests:

```json
{
  "url": "https://api.example.com/products",
  "method": "GET",
  "environment": "development"
}
```

Common values:
- development
- testing
- staging
- production

### Logging to Different Sheets

To log different types of API calls to different sheets:

1. Modify the Google Sheets modules
2. Add a router before the Google Sheets modules
3. Create routes based on API domains or other criteria
4. Configure each route to log to a different sheet

## Troubleshooting

### Common Issues

**Issue: Invalid JSON format errors**

Make sure your `headers` and `queryParams` are properly formatted as JSON strings. You may need to use `toJSON()` function in Make.com to convert objects to JSON strings.

**Issue: Google Sheets authentication errors**

Ensure your Google Sheets connection is properly authenticated and has not expired. Reconnect if necessary.

**Issue: Webhook URL not working**

Verify that your scenario is activated in Make.com. The webhook URL only works when the scenario is active.

**Issue: Large response bodies**

By default, the scenario truncates response bodies larger than 50,000 characters when logging to Google Sheets. You can adjust this limit in the Google Sheets module configuration.

### Getting Help

If you encounter any issues, please:

1. Check the error messages in Make.com's execution history
2. Review your API Testing Hub logs in Google Sheets
3. Open an issue in this GitHub repository for additional help

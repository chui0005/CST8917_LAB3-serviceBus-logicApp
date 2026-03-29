# CST8917 Lab 3 - FleetBook

This project is a small serverless lab for a vehicle booking workflow built with Azure Functions, Azure Service Bus, and Logic Apps.

The main backend logic lives in `function_app.py`. It receives booking requests, checks a mock fleet dataset, picks an available vehicle when possible, calculates pricing, and returns either a confirmed or rejected result. The project also includes a simple frontend in `client.html` that submits booking messages to Service Bus and displays the results as they come back from the workflow.

## Codebase overview

- `function_app.py` - Python Azure Function App with:
  - `check-booking` HTTP endpoint to evaluate booking requests
  - `health` HTTP endpoint for a quick status check
- `client.html` - browser client for submitting booking requests and viewing results
- `test-function.http` - sample HTTP requests for testing the function locally in VS Code REST Client
- `requirements.txt` - Python dependency list
- `host.json` - Azure Functions host configuration
- `local.settings.json` - local runtime settings for Functions and storage emulator
- `AzuriteConfig` - local Azurite storage configuration


## Prerequisites

Install these before running the lab:

- Python 3.10 or newer
- Azure Functions Core Tools
- Azurite
- VS Code REST Client extension (optional, for `test-function.http`)

## Local setup

1. Open the lab folder in a terminal.
2. Create and activate a virtual environment.
3. Install dependencies from `requirements.txt`.
4. Start Azurite.
5. Start the Azure Functions host.

Example commands:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
azurite
func start
```

The function app should start locally at:

```text
http://localhost:7071
```

## Testing the function locally

You can test the function in either of these ways:

1. Use `test-function.http` in VS Code REST Client.
2. Send requests manually to:

```text
GET  http://localhost:7071/api/health
POST http://localhost:7071/api/check-booking
```

The booking endpoint expects JSON with fields such as:

```json
{
  "bookingId": "BK-0001",
  "customerName": "Jane Doe",
  "customerEmail": "jane@example.com",
  "vehicleType": "sedan",
  "pickupLocation": "Ottawa",
  "pickupDate": "2026-04-01",
  "returnDate": "2026-04-05",
  "notes": "GPS, child seat"
}
```

## Frontend client

`client.html` is a simple demo UI for the full lab workflow. It does not call the Azure Function directly. Instead, it:

1. Sends booking messages to a Service Bus queue named `booking-queue`
2. Assumes a Logic App reads the queue message
3. Assumes the Logic App calls the Azure Function
4. Polls a Service Bus topic named `booking-results`
5. Reads results from subscriptions `confirmed-sub` and `rejected-sub`

To use the client, open `client.html` in a browser and enter:

- Service Bus namespace
- SAS policy name
- SAS key

## Important note

This repository contains the Function App and demo client, but the Azure Service Bus and Logic App resources are expected to be created and configured separately in Azure for the full end-to-end workflow.

# Juvoly API Session Creation

This document describes how to create a session using the Juvoly API.

## Endpoint

```
POST https://prod.juvoly.nl/api/v2/rest/session
```

## Required Headers

| Header Name | Description |
|-------------|-------------|
| `X-Juvoly-Client-Id` | Your Juvoly Client ID |
| `X-Juvoly-Api-Key` | Your Juvoly API Key |
| `Accept` | Set to `application/json` |
| `Content-Type` | Set to `application/json` |

## Example Usage (TypeScript)

```typescript
import fetch from 'node-fetch';

async function createJuvolySession(juvolyClientId: string, juvolyApiKey: string): Promise<Response> {
  const response = await fetch(
    'https://prod.juvoly.nl/api/v2/rest/session',
    {
      method: 'POST',
      headers: {
        'X-Juvoly-Client-Id': juvolyClientId,
        'X-Juvoly-Api-Key': juvolyApiKey,
        'Accept': 'application/json',
        'Content-Type': 'application/json',
      },
    }
  );

  return response;
}

// Usage
const clientId = 'your-client-id';
const apiKey = 'your-api-key';

createJuvolySession(clientId, apiKey)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

## Notes

- Ensure that you replace `'your-client-id'` and `'your-api-key'` with your actual Juvoly Client ID and API Key.
- This example uses `node-fetch`, but you can adapt it to use other HTTP clients or the built-in `fetch` in modern environments.
- Always keep your API key secure and never expose it in client-side code.

## Response

The API will respond with a JSON object containing session information. Handle the response accordingly in your application.

---

For more information, please refer to the official Juvoly API documentation.

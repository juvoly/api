Here's a markdown document describing the action with nice formatting, including the required values and an example in TypeScript:

# Juvoly Session Creation

This document describes how to create a new session using the Juvoly API.

## API Endpoint

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
const juvolyClientId = 'your_client_id_here';
const juvolyApiKey = 'your_api_key_here';

async function createJuvolySession(): Promise<JuvolySessionInfoV2> {
  const response = await fetch(
    'https://prod.juvoly.nl/api/v2/rest/session',
    {
      method: 'POST',
      headers: {
        'X-Juvoly-Client-Id': juvolyClientId,
        'X-Juvoly-Api-Key': juvolyApiKey,
        Accept: 'application/json',
        'Content-Type': 'application/json',
      },
    },
  );

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }

  return await response.json();
}
```

## Response

The API returns a `JuvolySessionInfoV2` object with the following structure:

```typescript
export interface JuvolySessionInfoV2 {
  clientId: string;
  sessionId: string;
  sessionCreateTime: Date;
  sessionClaimTime: Date;
}
```

### Response Fields

| Field Name | Type | Description |
|------------|------|-------------|
| `clientId` | string | The client ID associated with the session |
| `sessionId` | string | A unique identifier for the created session |
| `sessionCreateTime` | Date | The timestamp when the session was created |
| `sessionClaimTime` | Date | The timestamp when the session was claimed |

## Notes

- Ensure that you replace `'your_client_id_here'` and `'your_api_key_here'` with your actual Juvoly Client ID and API Key.
- The `sessionCreateTime` and `sessionClaimTime` are returned as Date objects, which can be easily manipulated or formatted as needed in your application.
- Always handle potential errors, such as network issues or invalid responses, in your implementation.

Remember to keep your API key and client ID secure and never expose them in client-side code or public repositories.

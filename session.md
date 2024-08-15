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

## WebSocket Connection

After obtaining a session, you can connect to the Juvoly API using a WebSocket. Here's an example implementation:

```typescript
class JuvolyApiSessionV2 {
  private ws: WebSocket;
  private sessionId: string;

  constructor(clientId: string, sessionId: string, model: 'generic' | 'radiology' | 'multilingual') {
    this.sessionId = sessionId;
    this.ws = new WebSocket(`wss://prod.juvoly.nl/api/v2/socket/${model}`, ['v1.juvoly', clientId, sessionId]);
    
    this.ws.onopen = this.onOpen.bind(this);
    this.ws.onmessage = this.onMessage.bind(this);
    this.ws.onerror = this.onError.bind(this);
    this.ws.onclose = this.onClose.bind(this);
  }

  private onOpen(event: Event) {
    console.log('WebSocket connection opened:', event);
    this.subscribeToInformation();
  }

  private onMessage(event: MessageEvent) {
    const message = JSON.parse(event.data);
    switch (message.type) {
      case 'information':
        this.handleInformation(message);
        break;
      case 'ready':
        this.handleReady(message);
        break;
      case 'transcript':
        this.handleTranscript(message);
        break;
    }
  }

  private onError(event: Event) {
    console.error('WebSocket error:', event);
  }

  private onClose(event: CloseEvent) {
    console.log('WebSocket connection closed:', event);
    if (event.code !== 1000) {
      console.error('Abnormal closure:', event.reason);
    }
  }

  private subscribeToInformation() {
    this.ws.send(JSON.stringify({
      type: 'subscribe',
      subscription: { type: 'information' }
    }));
  }

  private handleInformation(message: JuvolyInformationMessageV2) {
    console.log('Received information:', message);
    // Handle the information message
  }

  private handleReady(message: JuvolyReadyMessageV2) {
    console.log('Ready to receive audio:', message);
    // Handle the ready message
  }

  private handleTranscript(message: JuvolyTranscriptMessageV2) {
    console.log('Received transcript:', message);
    // Handle the transcript message
  }

  public sendAudio(blob: Blob) {
    this.ws.send(blob);
  }

  public disconnect() {
    this.ws.close();
  }
}
```

## Interfaces

Here are the interfaces used in the Juvoly API:

```typescript
interface JuvolySessionInfoV2 {
  clientId: string;
  sessionId: string;
  sessionCreateTime: Date;
  sessionClaimTime: Date;
}

interface JuvolyCodingV2 {
  system: string;
  code: string;
  description: string;
}

interface JuvolyResourceV2 {
  link: string;
  description: string;
  origin: 'Thuisarts' | 'NHG';
}

interface JuvolyInformationMessageV2 {
  type: 'information';
  codings: JuvolyCodingV2[];
  resources: JuvolyResourceV2[];
}

interface JuvolyWordUtteranceV2 {
  word: string;
  start: number;
  end: number;
}

interface JuvolyUtteranceV2 {
  id: string;
  sentence: string;
  words: JuvolyWordUtteranceV2[];
}

interface JuvolyTranscriptMessageV2 {
  type: 'transcript';
  completed: boolean;
  utterance: JuvolyUtteranceV2;
}

interface JuvolyReadyMessageV2 {
  type: 'ready';
}

type JuvolyWebSocketMessageV2 =
  | JuvolyInformationMessageV2
  | JuvolyReadyMessageV2
  | JuvolyTranscriptMessageV2;
```

## Usage Example

Here's how you might use the Juvoly API in your application:

```typescript
const main = async () => {
  try {
    const sessionInfo = await createSession('your-client-id', 'your-api-key');
    const session = new JuvolyApiSessionV2(sessionInfo.clientId, sessionInfo.sessionId, 'generic');

    // Wait for the 'ready' message before sending audio
    // You would typically set up event listeners or use a promise here

    // Send audio data
    const audioBlob = new Blob(['audio data here'], { type: 'audio/wav' });
    session.sendAudio(audioBlob);

    // When you're done
    session.disconnect();
  } catch (error) {
    console.error('Error:', error);
  }
};

main();
```

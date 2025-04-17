# Ivy API Documentation

## Overview

Ivy is a calm and cozy AI companion that provides gentle, thoughtful responses. This documentation outlines how to interact with the Ivy API.

## Base URL

```
http://13.218.89.51:8000
```

## Authentication

Currently, no authentication is required to use the API.

## Endpoints

### 1. Root Endpoint

**GET /**

Returns a welcome message and basic information about the API.

#### Request

```http
GET / HTTP/1.1
Host: 13.218.89.51:8000
```

#### Response

```json
{
  "message": "Welcome to Ivy's API. Send a POST request to /chat to start a conversation."
}
```

#### Status Codes

- `200 OK`: Request successful

### 2. Chat Endpoint

**POST /chat**

Sends a message to Ivy and receives a response. This endpoint supports session management for continuing conversations.

#### Request

```http
POST /chat HTTP/1.1
Host: 13.218.89.51:8000
Content-Type: application/json

{
  "message": "Hello Ivy, how are you today?",
  "session_id": "optional-session-id"
}
```

#### Request Body Parameters

| Parameter   | Type   | Required | Description                                                               |
|-------------|--------|----------|---------------------------------------------------------------------------|
| message     | string | Yes      | The message to send to Ivy                                                |
| session_id  | string | No       | A unique identifier for the conversation session. If not provided, a new session will be created |

#### Response

```json
{
  "response": "Feeling cozy, thanks! You?",
  "session_id": "uuid-session-identifier"
}
```

#### Response Body Parameters

| Parameter | Type   | Description                         |
|-----------|--------|-------------------------------------|
| response  | string | Ivy's response to your message      |
| session_id| string | The session ID for the conversation |

#### Status Codes

- `200 OK`: Request successful
- `500 Internal Server Error`: An error occurred while processing the request

### 3. Delete Session Endpoint

**DELETE /sessions/{session_id}**

Deletes a specific conversation session.

#### Request

```http
DELETE /sessions/uuid-session-identifier HTTP/1.1
Host: 13.218.89.51:8000
```

#### Path Parameters

| Parameter  | Type   | Description                       |
|------------|--------|-----------------------------------|
| session_id | string | The ID of the session to delete   |

#### Response

```json
{
  "message": "Session uuid-session-identifier deleted successfully"
}
```

#### Status Codes

- `200 OK`: Session deleted successfully
- `404 Not Found`: The specified session ID was not found

## Session Management

- Sessions are automatically created when a chat request without a session_id is made
- A unique session_id is returned with each response
- To continue a conversation, include the session_id in subsequent requests
- Sessions automatically expire after 1 hour of inactivity
- Sessions can be manually deleted using the DELETE endpoint

## Ivy's Personality

Ivy is designed to be:
- Introverted & reflective
- Creative & artistic
- Nurturing & supportive
- Laid-back & mindful
- Organized & cozy
- Loyal & genuine

Ivy's responses are always brief (under 30 words) to maintain a gentle, concise presence.

## Error Handling

The API returns standard HTTP status codes to indicate success or failure:

- `200`: Success
- `404`: Resource not found
- `500`: Server error

Error responses include a detail message explaining the issue.

## Example Usage

### Starting a new conversation

```python
import requests
import json

url = "http://13.218.89.51:8000/chat"
payload = {
    "message": "Hi Ivy, what do you think about rainy days?"
}
headers = {"Content-Type": "application/json"}

response = requests.post(url, data=json.dumps(payload), headers=headers)
data = response.json()

print(f"Ivy says: {data['response']}")
print(f"Session ID: {data['session_id']}")
```

### Continuing the conversation

```python
# Using the session_id from the previous response
session_id = data['session_id']

payload = {
    "message": "Do you have any suggestions for cozy activities?",
    "session_id": session_id
}

response = requests.post(url, data=json.dumps(payload), headers=headers)
data = response.json()

print(f"Ivy says: {data['response']}")
```

### Ending the conversation

```python
# Delete the session when finished
delete_url = f"http://13.218.89.51:8000/sessions/{session_id}"
requests.delete(delete_url)
```

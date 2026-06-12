# SafeAlert API Documentation

## Base URL
```
https://api.safealert.app/api/v1
```

## Authentication

All requests require Bearer token in Authorization header:
```
Authorization: Bearer <JWT_TOKEN>
```

### Token Response Format
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
  "expiresIn": 3600,
  "tokenType": "Bearer"
}
```

---

## Authentication Endpoints

### POST /auth/register
Register a new user account.

**Request:**
```json
{
  "email": "user@example.com",
  "phoneNumber": "+1234567890",
  "displayName": "John Doe",
  "password": "securePassword123"
}
```

**Response (201):**
```json
{
  "id": "user_12345",
  "email": "user@example.com",
  "displayName": "John Doe",
  "createdAt": "2026-06-12T10:30:00Z",
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

### POST /auth/login
Authenticate user and receive tokens.

**Request:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response (200):**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
  "expiresIn": 3600
}
```

### POST /auth/refresh
Refresh access token using refresh token.

**Request:**
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

**Response (200):**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "expiresIn": 3600
}
```

### POST /auth/logout
Invalidate refresh token.

**Response (200):**
```json
{
  "message": "Successfully logged out"
}
```

---

## Location Endpoints

### POST /locations
Submit current location.

**Request:**
```json
{
  "latitude": 40.7128,
  "longitude": -74.0060,
  "altitude": 10.5,
  "accuracy": 5.0,
  "speed": 2.5,
  "heading": 45.0,
  "timestamp": "2026-06-12T10:30:00Z",
  "source": "gps"
}
```

**Response (201):**
```json
{
  "id": "loc_12345",
  "userId": "user_12345",
  "latitude": 40.7128,
  "longitude": -74.0060,
  "accuracy": 5.0,
  "timestamp": "2026-06-12T10:30:00Z",
  "source": "gps",
  "createdAt": "2026-06-12T10:30:00Z"
}
```

### GET /locations/me
Get current user's latest location.

**Response (200):**
```json
{
  "id": "loc_12345",
  "userId": "user_12345",
  "latitude": 40.7128,
  "longitude": -74.0060,
  "accuracy": 5.0,
  "timestamp": "2026-06-12T10:30:00Z",
  "source": "gps"
}
```

### GET /locations/history
Get location history.

**Query Parameters:**
- `hours`: Number of hours (default: 24)
- `limit`: Max results (default: 100, max: 1000)
- `offset`: Pagination offset (default: 0)

**Response (200):**
```json
{
  "total": 150,
  "limit": 100,
  "offset": 0,
  "locations": [
    {
      "id": "loc_12345",
      "latitude": 40.7128,
      "longitude": -74.0060,
      "accuracy": 5.0,
      "timestamp": "2026-06-12T10:30:00Z"
    },
    ...
  ]
}
```

### GET /locations/shared
Get locations from users who shared with you.

**Query Parameters:**
- `userId`: Filter by specific user (optional)
- `limit`: Max results (default: 50)

**Response (200):**
```json
{
  "sharedLocations": [
    {
      "userId": "user_67890",
      "displayName": "Jane Doe",
      "latitude": 40.7128,
      "longitude": -74.0060,
      "accuracy": 5.0,
      "timestamp": "2026-06-12T10:30:00Z"
    },
    ...
  ]
}
```

---

## Mesh Network Endpoints

### POST /mesh/nodes
Register a mesh node (device).

**Request:**
```json
{
  "deviceId": "device_abc123",
  "signalStrength": -55,
  "relayCapacity": 10
}
```

**Response (201):**
```json
{
  "id": "node_12345",
  "deviceId": "device_abc123",
  "isActive": true,
  "discoveryTime": "2026-06-12T10:30:00Z"
}
```

### GET /mesh/nodes
Get list of nearby mesh nodes.

**Query Parameters:**
- `maxDistance`: Filter by distance in meters (optional)
- `limit`: Max results (default: 50)

**Response (200):**
```json
{
  "nodes": [
    {
      "id": "node_12345",
      "deviceId": "device_abc123",
      "signalStrength": -55,
      "distance": 50,
      "lastUpdate": "2026-06-12T10:30:00Z",
      "isActive": true
    },
    ...
  ]
}
```

### PUT /mesh/nodes/:id
Update mesh node status.

**Request:**
```json
{
  "signalStrength": -48,
  "isActive": true
}
```

**Response (200):**
```json
{
  "id": "node_12345",
  "signalStrength": -48,
  "lastUpdate": "2026-06-12T10:35:00Z"
}
```

### DELETE /mesh/nodes/:id
Deregister mesh node.

**Response (204):**
Empty response

---

## Sharing Endpoints

### POST /sharing/enable
Enable location sharing with another user.

**Request:**
```json
{
  "targetUserId": "user_67890",
  "expiresIn": 3600
}
```

**Response (201):**
```json
{
  "id": "share_12345",
  "targetUserId": "user_67890",
  "createdAt": "2026-06-12T10:30:00Z",
  "expiresAt": "2026-06-12T11:30:00Z"
}
```

### POST /sharing/disable
Disable location sharing with a user.

**Request:**
```json
{
  "targetUserId": "user_67890"
}
```

**Response (200):**
```json
{
  "message": "Sharing disabled"
}
```

### GET /sharing/list
List all active sharing permissions.

**Response (200):**
```json
{
  "sharedWith": [
    {
      "userId": "user_67890",
      "displayName": "Jane Doe",
      "createdAt": "2026-06-12T10:30:00Z",
      "expiresAt": "2026-06-13T10:30:00Z"
    },
    ...
  ]
}
```

---

## Social Media Endpoints

### POST /social/connect
Connect social media account.

**Request:**
```json
{
  "provider": "facebook",
  "accessToken": "EAABsbCS...",
  "userId": "123456789"
}
```

**Response (201):**
```json
{
  "id": "social_12345",
  "provider": "facebook",
  "connectedAt": "2026-06-12T10:30:00Z"
}
```

### POST /social/share
Share location to social media.

**Request:**
```json
{
  "provider": "facebook",
  "message": "Check where I am!",
  "latitude": 40.7128,
  "longitude": -74.0060,
  "expiresIn": 3600
}
```

**Response (201):**
```json
{
  "id": "share_12345",
  "provider": "facebook",
  "postId": "123456789",
  "expiresAt": "2026-06-12T11:30:00Z",
  "url": "https://facebook.com/posts/123456789"
}
```

### GET /social/accounts
List connected social media accounts.

**Response (200):**
```json
{
  "accounts": [
    {
      "id": "social_12345",
      "provider": "facebook",
      "displayName": "John Doe",
      "connectedAt": "2026-06-12T10:30:00Z"
    },
    ...
  ]
}
```

### DELETE /social/accounts/:id
Disconnect social media account.

**Response (204):**
Empty response

---

## User Profile Endpoints

### GET /users/profile
Get current user's profile.

**Response (200):**
```json
{
  "id": "user_12345",
  "email": "user@example.com",
  "displayName": "John Doe",
  "phoneNumber": "+1234567890",
  "avatar": "https://avatars.example.com/user_12345.jpg",
  "createdAt": "2026-06-12T10:30:00Z"
}
```

### PUT /users/profile
Update user profile.

**Request:**
```json
{
  "displayName": "John M. Doe",
  "phoneNumber": "+1987654321",
  "avatar": "https://avatars.example.com/new_avatar.jpg"
}
```

**Response (200):**
```json
{
  "id": "user_12345",
  "displayName": "John M. Doe",
  "phoneNumber": "+1987654321",
  "updatedAt": "2026-06-12T10:35:00Z"
}
```

### PUT /users/settings
Update user settings.

**Request:**
```json
{
  "privacyLevel": "friends",
  "trackingEnabled": true,
  "sharingEnabled": true,
  "meshEnabled": true,
  "encryptionEnabled": true,
  "dataRetentionDays": 30
}
```

**Response (200):**
```json
{
  "settings": {
    "privacyLevel": "friends",
    "trackingEnabled": true,
    "sharingEnabled": true,
    "meshEnabled": true,
    "encryptionEnabled": true,
    "dataRetentionDays": 30
  },
  "updatedAt": "2026-06-12T10:35:00Z"
}
```

---

## Error Responses

### 400 Bad Request
```json
{
  "error": "INVALID_REQUEST",
  "message": "Missing required field: latitude",
  "details": { "field": "latitude" }
}
```

### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Invalid or expired token"
}
```

### 403 Forbidden
```json
{
  "error": "FORBIDDEN",
  "message": "You do not have permission to access this resource"
}
```

### 404 Not Found
```json
{
  "error": "NOT_FOUND",
  "message": "Resource not found"
}
```

### 429 Too Many Requests
```json
{
  "error": "RATE_LIMITED",
  "message": "Too many requests, please retry after 60 seconds",
  "retryAfter": 60
}
```

### 500 Internal Server Error
```json
{
  "error": "INTERNAL_ERROR",
  "message": "An unexpected error occurred"
}
```

---

## Rate Limiting

- **Location Updates**: 100 requests/minute per user
- **Mesh Node Updates**: 1000 requests/minute per device
- **API Calls**: 10,000 requests/minute per user

Rate limit info in response headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1623596400
```

---

## WebSocket Events

### Client -> Server

```javascript
// Subscribe to location updates
socket.emit('subscribe:location', { userId: 'user_67890' })

// Update own location in real-time
socket.emit('location:update', {
  latitude: 40.7128,
  longitude: -74.0060,
  accuracy: 5.0,
  timestamp: Date.now()
})

// Broadcast mesh message
socket.emit('mesh:broadcast', {
  type: 'location',
  payload: { latitude, longitude },
  ttl: 3
})
```

### Server -> Client

```javascript
// Location update received
socket.on('location:update', (data) => {
  // { userId, latitude, longitude, timestamp }
})

// Mesh device discovered
socket.on('mesh:device:discovered', (device) => {
  // { deviceId, signalStrength, distance }
})

// Location shared with you
socket.on('location:shared', (data) => {
  // { userId, displayName, latitude, longitude }
})
```

---

For more information, visit [SafeAlert Documentation](./README.md)

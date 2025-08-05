# SDKv1 Error Handling

## Login and refresh token

`loginWithUsername(...)`, `refreshToken(...)`, `logout(...)`

- **CallFailedException**
  > The API call failed.
- **ParseResponseException**
  > Error parsing the API response.
- **ExpiredAuthTokenThrowable**
  > The user's auth token has expired.
- **InvalidAuthTokenThrowable**
  > The user's auth token is invalid.
- **NetworkUnavailableException**
  > No internet connection.
- **LoginErrorThrowable**
  > Error trying to authenticate user with the given credentials.
- **UserNotLoginErrorThrowable**
  > No user session, unable to refresh or logout.

## Fetch servers/Pops

`fetchAllSerceversX(...)`, `fetchSercersX(...)`, `fetchAllPopsX(...)`, `fetchPopsX(...)`, `updateX(...)`

- **CallFailedException**
  > The API call failed.
- **ParseResponseException**
  > Error parsing the API response.
- **ExpiredAuthTokenThrowable**
  > The user's auth token has expired.
- **InvalidAuthTokenThrowable**
  > The user's auth token is invalid.
- **NetworkUnavailableException**
  > No internet connection.
- **ServerErrorThrowable**
  > The API rejected the get server list request.

## Connect

`connectX(...)`, `attemptToConnectX(...)`, `disconnect(...)`, `attemptToDisconnect(...)`

- **CallFailedException**
  > The API call failed.
- **ParseResponseException**
  > Error parsing the API response.
- **ExpiredAuthTokenThrowable**
  > The user's auth token has expired.
- **InvalidAuthTokenThrowable**
  > The user's auth token is invalid.
- **NetworkUnavailableException**
  > No internet connection.
- **ExpiredWireGuardAccountException**
  > Unable to fetch WireGuard configuration, the user account has expired.
- **InvalidWireGuardApiResponseException**
  > Unable to fetch WireGuard configuration, the user account is invalid.
- **UnableToConnectToWireGuardApiException**
  > Unable to connect to server with WireGuard.
- **NoServersAvailableException**
  > Load balance didn't found a matching server to connect.
- **CouldNotDisconnectException**
  > Unable to stop the VPN service.
- **CouldNotEstablishConnectionException**
  > Unable to connect to the VPN server.
- **NoPopMatchForServerException**
  > Load balance didn't found a matching pop to connect. 
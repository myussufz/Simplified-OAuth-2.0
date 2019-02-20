## Simplified OAuth 2.0 (Concept)

### Tables Structure
* users

  | Column                  | Data Type
  |----------               |-------------
  | id                      | int
  | name                    | string
  | username                | string
  | password                | string
  <br>

* clients

  | Column                  | Data Type
  |----------               |-------------
  | id                      | int
  | client_id               | string
  | client_secret           | string
  | name                    | string
  | home_page_url           | string
  | logo_url                | string
  | privacy_policy_url      | string
  | user_id                 | string
  | is_live                 | bool
  | redirect_uri            | []string
  <br>

* client_access_tokens

  | Column                  | Data Type
  |----------               |-------------
  | id                      | int
  | client_id               | int
  | user_id                 | int
  | client_refresh_token_id | int
  | access_token            | string
  | grant_type              | string
  | scope                   | string
  | audience                | string
  | expired_at              | datetime
  <br>

* client_refresh_tokens

  | Column                  | Data Type
  |----------               |-------------
  | id                      | int
  | client_id               | int
  | user_id                 | int
  | refresh_token           | string
  | grant_type              | string
  | scope                   | string
  | audience                | string
  | expired_at              | datetime
  <br>

* client_authorization_codes

  | Column                  | Data Type
  |----------               |-------------
  | id                      | int
  | client_id               | int
  | user_id                 | int
  | code                    | string
  | scope                   | []string
  | is_used                 | bool
  | redirect_uri            | string
  | expired_at              | datetime
  <br>


### Grant Types
* client_credentials
* password
* authorization_code
* refresh_token
<br><br><br>

#### Client Credentials Grant
The client needs to authenticate themselves for this request. Typically the service will allow  accept the client ID and secret in the HTTP Basic auth header.

##### Request (e.g)
```javascript
POST /oauth/token
Host: authorization-server.com

// header
Authorization: Basic base64_encode(client_id:client_secret) // required

// body
{
  grant_type: "client_credentials", // required
  scope: "get_user_profile" // optional, but if not passed will give full access of the client user
}
```

##### Response (e.g)
```javascript
// body
{
   tokenType: "Bearer",
   expiresIn: 3600,
   accessToken: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
   refreshToken: "eqweasdasdanvbSifkLImUAtuXITjxKMzkXjZvKGGIcLyFqHIDrVNexjHwXBNRhgicutwNStdasdasdLmMjfggjHrYvzWaIANmUvNOoDtNIOKOFywqedsa",
   scope: "get_user_profile"
}
```
<br><br>
#### Password Grant
If the client was issued a secret, then the client must authenticate this request. Typically the service will allow accept the client ID and secret in the HTTP Basic auth header.

##### Request (e.g)
```javascript
POST /oauth/token
Host: authorization-server.com

// header
Authorization: Basic base64_encode(client_id:client_secret) // optional

// body
{
  grant_type: "password", // required
  username: "yussuf", // required
  password: "123456" // required
  scope: "get_user_profile" // optional, but if not passed will give full access of the user
}
```

##### Response (e.g)
```javascript
// body
{
   tokenType: "Bearer",
   expiresIn: 3600,
   accessToken: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
   refreshToken: "eqweasdasdanvbSifkLImUAtuXITjxKMzkXjZvKGGIcLyFqHIDrVNexjHwXBNRhgicutwNStdasdasdLmMjfggjHrYvzWaIANmUvNOoDtNIOKOFywqedsa",
   scope: "get_user_profile"
}
```
<br><br>
#### Authorization Code Grant
The authorization code is a temporary code that the client will exchange for an access token. The code itself is obtained from the authorization server where the user gets a chance to see what the information the client is requesting, and approve or deny the request.

The first step of the web flow is to request authorization from the user. This is accomplished by creating an authorization request link for the user to click on.

The authorization URL is usually in a format such as:
`https://authorization-server.com/oauth/authorize?client_id=abcdefgh&response_type=code&state=testing&redirect_uri=https%3A%2F%2Frevenuemonster.my%0A&scope=get_user_profile`

After user approve redirect format:
`https://revenuemonster.my?code=abc123&state=testing`


##### Request (e.g)
```javascript
POST /oauth/token
Host: authorization-server.com

// header
Authorization: Basic base64_encode(client_id:client_secret) // required

// body
{
  grant_type: "authorization_code", // required
  code: "abc123", // required get from the redirect url code query string
  redirect_uri: "https://revenuemonster.my" // required
}
```

##### Response (e.g)
```javascript
// body
{
   tokenType: "Bearer",
   expiresIn: 3600,
   accessToken: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
   refreshToken: "eqweasdasdanvbSifkLImUAtuXITjxKMzkXjZvKGGIcLyFqHIDrVNexjHwXBNRhgicutwNStdasdasdLmMjfggjHrYvzWaIANmUvNOoDtNIOKOFywqedsa",
   scope: "get_user_profile"
}
```
<br><br>
#### Refresh Token Grant
If the client was issued a secret, then the client must authenticate this request. Typically the service will allow accept the client ID and secret in the HTTP Basic auth header.

##### Request (e.g)
```javascript
POST /oauth/token
Host: authorization-server.com

// header
Authorization: Basic base64_encode(client_id:client_secret) // optional

// body
{
  grant_type: "refresh_token", // required
  refresh_token: "eqweasdasdanvbSifkLImUAtuXITjxKMzkXjZvKGGIcLyFqHIDrVNexjHwXBNRhgicutwNStdasdasdLmMjfggjHrYvzWaIANmUvNOoDtNIOKOFywqedsa", // required
  scope: "get_user_profile" // optional, the requested scope must not include additional scopes that were not issued in the original access token, can omit some scope from the original access token.
}
```

##### Response (e.g)
```javascript
// body
{
   tokenType: "Bearer",
   expiresIn: 3600,
   accessToken: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5cnewtoken",
   refreshToken: "eqweasdasdanvbSifkLImUAtuXITjxKMzkXjZvKGGIcLyFqHIDrVNexjHwXBNRhgicutwNStdasdasdLmMjfggjHrYvzWaIANmUvNOoDtNIOKOFywqedsanewrefreshtoken",
   scope: "get_user_profile"
}
```

<br><br>
### Reference
* https://www.oauth.com

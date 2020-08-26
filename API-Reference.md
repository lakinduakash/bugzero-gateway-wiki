# API Reference

### Authorization

Every call to API server must have the following authorization header  `{Authorization: Bearer JWT}`. The requester must have the private key, so JWT can be signed. Then the server will check the signature of the JWT using the public key provided when the server is initializing.

JWT must have the following fields.
```js
{
  "alg": "RS256",
  "typ": "JWT"
}

{
  "sub": "org-id",
  "exp": 1516240342,
  "iat": 1516239022
}
```
### API guide

#### Add new user

`POST /api/user/add`

| Name     | Type   | In   | Description                                                                                                                                                                                     |
|----------|--------|------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| username | string | body | VPN identity of the new user. <br>The minimum length is 6. Maximum length is 20<br>only alphanumeric characters allowed and username can only be started with characters.<br>Not case sensitive |
| secret   | string | body | Secret for the user. Minimum length is 8. Maximum length is 20                                                                                                                                  |


### Update user

`POST /api/user/update`

| Name     | Type   | In   | Description                                                                                                                                                                                     |
|----------|--------|------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| username | string | body | VPN identity of the new user. <br>The minimum length is 6. Maximum length is 20<br>only alphanumeric characters allowed and username can only be started with characters.<br>Not case sensitive |
| secret   | string | body | Secret for the user. Minimum length is 8. Maximum length is 20                                                                                                                                  |

If `username` does not exist it will return an error.

### Delete user

`DELETE /api/user/delete/{username}`

| Name     | Type   | In   | Description            |
|----------|--------|------|------------------------|
| username | string | path | Username to be removed |

If `username` does not exist it will return an error.

### List users

`GET /api/user/list`

This will return a list of usernames as an array.

Ex: `['user1','user2','user3']`

### Get Server Status

`GET /api/util/status`

Returns the VPN server status

### Get CA

`GET /api/util/getca`

Returns the CA certificate as a text

### Get certificate

`GET /api/util/getcert`

Returns the client certificate as a text


### Get ubuntu client configuration script

`GET /api/util/getubuntuclientconfig`

Returns the ubuntu client configuration script as text

### Get logs

`GET /api/util/logs`

Returns the VPN server logs




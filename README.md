# Login

Authenticates the user and generates API token.

The user credentials are passed in the request header as HTTP Basic Auth string.

As a result of the method the user data and access token are returned.

The input parameters for this method are sent in form of HTTP Basic Authentication.

As the Authentication parameter is sent in the Request Header, the HTTP method is not important but for consistency only GET and POST are allowed.

In case of successfull login a bearer token will be sent. It can be used instead of the actual user password in all consequtive request

## API Endpoint

**GET** `/api/auth/login`

### Parameters:

| Name | Type | Description |
|---|---|---|
| Auth Username | String | Login name of the user |
| Auth Password | String | User inserted password |


### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| Id | Integer | User internal identificator. |
| user | String | User login. |
| name | String | Screen name of the user. |
| phone | String | Contact phone. |
| email | String | Contact email. |
| role | Integer | User access level. |
| pop | String | Default user instance. |
| token | String | Session access token. Can be used instead of password in all consequtive requests. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/auth/login/ -G \
        -u api_user:api_password 
```

## Example Responce

```json
{
    "result": 200,
    "message": "Success",
    "data": {
        "Id": 13,
        "name": "kalin",
        "phone": "02-969-1540",
        "email": "knikolov@evolink.com",
        "role": 9,
        "pop": "sof",
        "token": "eyJ0eXAiOiJKV1QiLCJerteertJIUzI1NiJ9.eyJ1c2VyaWQiOiIxMyIsImlhdCI6MTY4NzI0MjA1MX0.U79Cb_8e2OZJn_2GUByPk96mR3pUtveGdOMPAWFa1-I"
    }
}
```


# User

## User Statuses 

| Status | Description | User | Admin | Reseller | Support |
| ------ | ----------- | ---- | ----- | -------- | ------- |
| 0 | Not Activated | - | x | - | - |
| 1 | Active | - | x | - | - |
| 2 | Reserved | - | x | - | - |
| 3 | Reserved | - | x | - | - |
| 4 | Reserved | - | x | - | - |
| 5 | Reserved | - | x | - | - |
| 6 | Reserved | - | x | - | - |
| 7 | Reserved | - | x | - | - |
| 8 | Suspended | - | x | - | - |
| 9 | Deleted | - | x | - | - |

**Admins Only** : If status of user is changed to anything else than Active (1), all his applications goes Suspended with status 4(Previously Disabled) or 5 (Previously Enabled). When status of user is changed again to Active (1), all previously suspended applications, goes in their reagular state. 4 -> Disabled, 5 -> Enabled.

Users cannot change their status!

## User Roles

| Role | Description | User | Admin | Reseller | Support |
| ------ | ----------- | ---- | ----- | -------- | ------- |
| 0 | Admin | - | x | - | - |
| 1 | Reserved | - | x | - | - |
| 2 | Reserved | - | x | - | - |
| 3 | Reserved | - | x | - | - |
| 4 | Reserved | - | x | - | - |
| 5 | Reserved | - | x | - | - |
| 6 | Reserved | - | x | - | - |
| 7 | Reserved | - | x | - | - |
| 8 | Reserved | - | x | - | - |
| 9 | User | x | x | - | - |

**Admin Only** : Only Admins can change user role. At this time (04.07.2023), changing role isn't supported, to admin too!

Users cannot change their role!

# Create User

## Endpoint: **POST** `/api/user/`

This endpoint is used to create a new user in the system.

## Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| username | String | The desired username for the new user. Only characters, numbers, underscore and dash are allowed. This is a required field |
| email | String | The email address of the new user. This is a required field |
| password | String | The password for the new user. This is a required field |
| fullname | String | The full name of the user. Only characters, numbers, underscore, dash and space are allowed. This is optional |
| phone | String | The phone number of the user. Only numbers and dash are allowed. This is optional |
| location | String | POP location. This is optional and default is 'sof' |

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| userid | Integer | The internal identifier of the user |
| username | String | The username of the new user |
| activation_code | String | The activation code for the new user |
| valid_until | DateTime | The expiration time for the activation code |
| email | String | The email address of the new user |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The user is created! |
| 400 | Bad request | One of the parameters is missing or incorrect |
| 409 | Conflict | Username or email already exists |
| 500 | Internal error | The user cannot be created due to an error in the server |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/user/ -X POST \
	-d '{"username": "client_username", "email" : "email@example.com" ,"password" : "Secret_password_of_user", "fullname" : "First Last" , "phone" : "+359 555 123 123", "location" : "sof"}'
```
## Example Response

```json
{
    "result": 200,
    "message": "User added successfully.",
    "data": {
        "userid": 34,
        "username": "client_username",
        "activation_code": "246e3c099b6f9157773ce6aa43fb3034e8ce6eb1dc957db4f22626b775ccd386",
        "valid_until": "2023-07-02 11:20:41",
        "email": "email@example.com"
    }
}
```


# Activate User

## Endpoint: **GET** `/api/user/activation/{activation_code}`

This endpoint activates a user account on the site. To activate a user, it is necessary to supply the activation code which was sent to the user's email.

### Note

Only users can activate their accounts using their activation code.

## Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| activation_code | String | Activation code sent to the user's email. This is a required field |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | User activated successfully |
| 400 | Bad request | Invalid activation code or the activation code is already used |
| 500 | Internal error | Error in execution on the server side |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/user/activation/1z2x3c4v5b6n7m89o0p1qw2e3r45ty6yvfgfr43fd45g
```

or

```bash
curl http://dev.k.cdn.bg/api/user/activation/ -G \
-d "activation_code=1z2x3c4v5b6n7m89o0p1qw2e3r45ty6yvfgfr43fd45g"
```

## Example Response

```json
{
    "result": 200,
    "message": "User activated successfully",
    "data": {
        "userid": 38
    }
}
```

## Example Response (Error)

```json
{
    "result": 400,
    "message": "Invalid activation code"
}
```

# Fetching Users List

## Endpoint:  **GET** `/api/user/list`

**Admin Only** This endpoint retrieves a list of users based on specific filters, order, limit, and offset parameters.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | header | Bearer token received after authentication. | Yes | String |
| filter | query | This parameter allows filtering of the user list based on specific fields. The field and its value should be specified in the format `filter[field]=value`. Wildcards are allowed by using the asterisk symbol. For example, `filter[username]=*name*` will return users whose username contains 'iv'. Multiple filters can be specified. | No | String |
| limit | query | This parameter specifies the maximum number of users to return in the response. `Default is 10. Maximum value is 100.`| No | Integer |
| offset | query | This parameter specifies the number of users to skip before starting to collect the set of returned users. `Default is 0`| No | Integer |
| order | query | This parameter allows ordering of the returned users based on a specific field. The order should be specified in the format `order=field.direction`. For example, `order=userid.desc` will return users ordered by user ID in descending order. `Default is userid.asc`| No | String |

### Filter and Order parameters

`userid, username, fullname, email, role, pop, status, phone`

### Order Directions 

`ASC or DESC`

## Responses

| Status | Description |
| ------ | ----------- |
| 200 | Successful operation. Returns list of users. |
| 400 | Bad request. Incorrect parameters. |
| 401 | Unauthorized. No permissions. |
| 500 | Internal server error. |

## Example Request
```bash
curl http://dev.k.cdn.bg/api/user/list -G \
    -H "Authorization: Bearer <your_token>" \
    -d "filter[username]=*iv*" \
    -d "limit=10" \
    -d "offset=0" \
    -d "order=userid.desc"
```

## Example Response

```json
{
    "result": 200,
    "message": "Success",
    "meta": {
        "filter": {
            "username": "*iv*"
        },
        "order": "userid.desc",
        "offset": 0,
        "limit": 10,
        "count": 3,
        "total": 3
    },
    "data": {
        "27": {
            "id": 27,
            "username": "ivi2w",
            "fullname": "",
            "email": "ivsi@gmail.com",
            "phone": "",
            "role": 9,
            "pop": "sof",
            "status": 1
        },
        "26": {
            "id": 26,
            "username": "ivi2",
            "fullname": "",
            "email": "ivi@gmail.com",
            "phone": "",
            "role": 9,
            "pop": "sof",
            "status": 1
        },
        "12": {
            "id": 12,
            "username": "ivi",
            "fullname": "",
            "email": "iivanov@evolink.com",
            "phone": "888-999-999",
            "role": 9,
            "pop": "sof",
            "status": 1
        }
    }
}
```
or
```json
{
    "result": 400,
    "message": "Invalid filter parameter. Please use one or more of the following keys: userid, username, fullname, email, role, pop, status, phone."
}
```
# Fetching User Information

## Endpoint:  **GET** `/api/user/{userid}`

This endpoint retrieves the information of a specific user. The user ID is required in the endpoint as a path parameter.

> **Note:** User Info is not cascading.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | header | Bearer token received after authentication. | Yes | String |
| userid | path | The unique identifier of the user. | Yes | Integer |

## Responses

| Status | Description |
| ------ | ----------- |
| 200 | Successful operation. Returns user's information. |
| 400 | Bad request. User not found. |
| 401 | Unauthorized. No permissions. |
| 500 | Internal server error. |

## Example Request
```bash
curl http://dev.k.cdn.bg/api/user/13 -G \
    -H "Authorization: Bearer <your_token>"
```

## Example Respnses
```json
{
    "result": 200,
    "message": "User Information.",
    "data": {
        "id": 18,
        "username": "test",
        "fullname": "",
        "email": "test@gmail.com",
        "phone": "",
        "status": 1,
        "role": 9,
        "pop": "sof"
    }
}
```

or

```json
{
    "result": 400,
    "message": "User not found."
}
```


# Edit User Details

## Endpoint: **PATCH** `/api/user/{userid}`

This endpoint allows you to update user details. For general users, they can update their own details such as phone number and full name. For admin role, they can update the details of other users including user role and status.

> **Note:** User update is not cascading.

## Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| Authorization Header | String | Bearer token received after authentication |
| userid (for Admin only) | Integer | ID of the user. This is a required field for admin |
| phone | String | User's updated phone number. This is a required field |
| fullname | String | User's updated full name. This is a required field |
| role (for Admin only) | Integer | User's updated role. For admin role, this is a required field. **See table above**|
| status (for Admin only) | Integer | User's updated status. For admin role, this is a required field. **See table above** |

**Admins Only** : If status of user is changed to anything else than Active (1), all his applications goes Suspended with status 4(Previously Disabled) or 5 (Previously Enabled). When status of user is changed again to Active (1), all previously suspended applications, goes in their reagular state. 4 -> Disabled, 5 -> Enabled.

Users cannot change their status!

## Output data 

| Parameter | Type | Description |
| result | Integer | Result code of the operation |
| message |String | Details about the result of the operation |
| data | JSON Object | Contains the updated user data |

## Response codes

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success	| The user details are updated successfully |
| 400 | Bad request	| There were errors during the process or one of the parameters is missing, empty, or invalid |
| 401 | Unauthorized | Incorrect token or no permissions |

## Example Request

**For general users:**

```bash
curl http://dev.k.cdn.bg/api/user/13 -X PATCH \
    -H "Authorization: Bearer <your_token>" \
    -d '{ "phone" : "123-456-789", "fullname": "Name Family"}'
```

**For Admins:**
```bash
curl http://dev.k.cdn.bg/api/user/13 -X PATCH \
    -H "Authorization: Bearer <your_token>" \
    -d '{"userid": 13, "phone" : "888-570-543", "role" : 9, "status" : 1}'
```

## Example Respnses

```json
{
    "result": 200,
    "message": "User details updated successfully.",
    "data": {
        "userid": 13,
        "phone": "123-456-789",
        "fullname": "Name Family",
        "role": 9,
        "status": 1
    }
}
```

or

```json
{
    "result": 400,
    "message": "Invalid parameters. Please check your inputs."
}
```

or

```json
{
    "result": 200,
    "message": "User information updated successfully.",
    "data": {
        "id": 13,
        "username": "user_name",
        "fullname": "Name Family",
        "email": "username@example.com",
        "phone": "123-456-789",
        "status": 1,
        "role": 9,
        "pop": "sof"
    },
    "applications": {
        "fls10070": {
            "result": 200,
            "message": "App Enabled successfully."
        },
        "fls10071": {
            "result": 200,
            "message": "App Enabled successfully."
        },
        "vod10007": {
            "result": 200,
            "message": "App Enabled successfully."
        }
    }
}

```

# Fetching User Roles

## Endpoint:  **GET** `/api/user/roles/`

This endpoint retrieves the roles of users. No parameters are required in this endpoint.

> **Note:** Roles info is not cascading.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | header | Bearer token received after authentication. | Yes | String |

## Responses

| Status | Description |
| ------ | ----------- |
| 200 | Successful operation. Returns roles information. |
| 400 | Bad request. Roles not found. |
| 401 | Unauthorized. No permissions. |
| 500 | Internal server error. |

## Example Request
```bash
curl http://dev.k.cdn.bg/api/user/roles/ -G \
    -H "Authorization: Bearer <your_token>"
```

## Example Response
```json
{
    "result": 200,
    "message": "User Roles Information.",
    "data": {
        "Role_0": {
            "name": "admin",
            "role": 0,
            "description": "Administrator"
        },
        "Role_1": {
            "name": "Reserved",
            "role": 1,
            "description": "Reserved"
        },
        "Role_2": {
            "name": "Reserved",
            "role": 2,
            "description": "Reserved"
        },
        "Role_3": {
            "name": "Reserved",
            "role": 3,
            "description": "Reserved"
        }
    }
}
```

# Fetching User Statuses

## Endpoint:  **GET** `/api/user/statuses/`

This endpoint retrieves the statuses of users. No parameters are required in this endpoint.

> **Note:** User statuses info is not cascading.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | header | Bearer token received after authentication. | Yes | String |

## Responses

| Status | Description |
| ------ | ----------- |
| 200 | Successful operation. Returns user statuses information. |
| 400 | Bad request. User statuses not found. |
| 401 | Unauthorized. No permissions. |
| 500 | Internal server error. |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/user/statuses/ -G \
    -H "Authorization: Bearer <your_token>"
```

## Example Response

```json
{
    "result": 200,
    "message": "User Status Information.",
    "data": {
        "Status_0": {
            "id": 0,
            "name": "Not Activated",
            "status": 0,
            "description": "User need to activate his account trough activation link."
        },
        "Status_1": {
            "id": 1,
            "name": "Active",
            "status": 1,
            "description": "User is Enabled and there is no limit to him."
        },
        "Status_8": {
            "id": 8,
            "name": "Suspended",
            "status": 8,
            "description": "User account is in suspended mode."
        },
        "Status_9": {
            "id": 9,
            "name": "Deleted",
            "status": 9,
            "description": "User and his application are removed from CDN!"
        }
    }
}
```


# Create User Token

## Endpoint: **POST** `/api/auth/token`

This endpoint adds a branch to the site's structure. To create a user bearer token, it is necessary to supply its UserID and its location parameters, respectively.

### Note

Admins can create tokens for other users. Users can create tokens for them only.

## Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| userid | Integer | ID of the user. This is a required field |
| location | String | POP location. This is a required field |

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| Id | Integer | The internal identifier of the directory |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The token is created! |
| 400 | Bad request | One of the parameters is missing or empty |
| 401 | Unauthorized | Incorrect authentication or No permissions |
| 500 | Internal error | The section cannot be created due to an error in the server |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/auth/token/ -X POST \
    -H "Authorization: Bearer <your_token>" \
	-d '{"userid": 13, "location": "sof"}' 
```
## Example Response

```json
{
    "result": 200,
    "message": "Token has been created",
    "data": {
        "userid": "18",
        "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyaWQiOiIxNCIsImlhdCI6MTY4NzI0OTk2OX0.9PBqDM-5yY8uRESS2BGOG5RWCv4Rw3_bTjXYIguWe6I"
    }
}
```

# Get User Token

## Endpoint: **GET** `/api/auth/token`

### Description:
This API endpoint returns a list of tokens for specific userid.

### Note
Admins can get tokens for other users. Users can get only their tokens. 

### Parameters:
| Name | Type | Description |
|---|---|---|
| userid | Integer | ID of the user. This is a required field |
| Location | String | POP location |

### Output data:
| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error |
| message | String | Human-readable result message |
| meta | Object | Meta data object of the result returned |
| data | Hash | A list of Directory objects. The key of the Hash is the ID of the directory |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | No tokens for given user |
| 401 | Unauthorized | Incorrect authenication or No permissions |
| 500 | Internal error | The section cannot be created due to an error in the server |

### Example Request:

To get all directories with parent ID = 3, ordered by path in ascending order, you can use:

```bash
curl http://dev.k.cdn.bg/api/auth/token/ -G \
    -H "Authorization: Bearer <token>"
	-d 'userid=18' \
    -d 'location=sof' 
```

### Example Response:

```json
{
    "result": 200,
    "message": "Tokens retrieved successfully",
    "data": {
        "userid": "18",
        "tokens": [
            {
                "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyaWQiOiIxNCIsIm7hdCI6MTY4NzI0ODU5MX0.piTyeGy6W9hlC55e8DKznHcjsVzMsG3oHG8vknGS8IU",
                "created_time": "2023-06-20 08:09:51"
            },
            {
                "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyaWQiOiIxNCIsIm7hdCI6MTY4NzI0OTk2MX0.bSsqgI78M79-G4ZwIlQnQyHkdJd-GT0u_uHF2xbapbc",
                "created_time": "2023-06-20 08:32:41"
            },
            {
                "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyaWQiOiIxNCIsIm7hdCI6MTY4NzI0OTk2M30.U3EMUT_6V4OCk3yOW2QboWCT_S9A7eQUradGsUFaRl0",
                "created_time": "2023-06-20 08:32:43"
            },
            {
                "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyaWQiOiIxNCIsIm7hdCI6MTY4NzI0OTk2OX0.9PBqDM-5yY8uRESS2BGOG5RWCv4Rw3_bTjXYIguWe6I",
                "created_time": "2023-06-20 08:32:49"
            }
        ]
    }
}
```

# Creating a Live (FLS) Application

**POST** `/api/application/fls/`

This endpoint allows you to create a new live (FLS) application. You will need to provide the user's ID, the name of the application, and the desired location for the application.

## Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| Authorization Header | String | Bearer token received after authentication |
| userid | Integer | ID of the user. This is a required field |
| name | String | Name of the application. This is a required field |
| location | String | The desired location for the application. This is a required field |

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| appid | Integer | The internal identifier of the application |
| userid | Integer | The ID of the user who created the application |
| name | String | The name of the application |
| active | Integer | The status of the application (0 for inactive, 1 for active) |
| security_template | Integer | The security template of the application (default is 1) |
| podData | Object | Information about the pod resources created for the application |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The application is created! |
| 400 | Bad request | One of the parameters is missing or empty |
| 401 | Unauthorized | Incorrect username or password |
| 500 | Internal error | The application cannot be created due to an error in the server |

## Example Request

```bash
curl  http://dev.k.cdn.bg/api/application/fls/ -X POST \
    -H "Authorization: Bearer <your_token>" \
	-d '{"userid": 13, "name": "Real-App-3", "location": "sof"}' 
```
## Example Responce
```json
{
    "result": 200,
    "message": "Application created successfully",
    "data": {
        "appid": 10061,
        "userid": 13,
        "name": "Real-App-3",
        "active": "0",
        "security_tempate": 1
    },
    "podData": {
        "services": "ifls_pod, ofls_pod, ifls_service, ofls_service",
        "status": "success, success, success, success",
        "httpcode": "201, 201, 201, 201"
    }
}
```

# FLS Applications Listing

**GET** /api/application/fls

### Description:
This API endpoint returns a list of FLS Application objects associated with a user, based on the given parameters, or an empty result set.

### Parameters:
| Name | Type | Description |
|---|---|---|
| Authorization Header | String | Bearer token received after authentication |
| filter | Hash | List filter. Mandatory Filters: userid, location. Additional filtering by the following fields is possible: status, name |
| order | String | List order. Ordering by the following fields is possible: appid, userid, name, active, security_template (asc or desc). Example: name.asc or appid.desc |
| offset | Integer | Result-set offset, used in pagination. Default = 0 |
| limit | Integer | Result-set records count. Default = 10 |

### Output data:
| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error |
| message | String | Human-readable result message |
| meta | Object | Meta data object of the result returned, including filter parameters, order, offset, limit, count of results in the set and total results |
| data | Array | A list of FLS Application objects or a message if no application found |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The applications list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Bad request | Invalid request parameters |

### Example Request:

To get all applications associated with user ID 13 and location 'sof', ordered by name in ascending order, you can use:

```bash
curl http://dev.k.cdn.bg/api/application/fls/ -G \
    -H "Authorization: Bearer <your_token>" \
    -d 'filter[userid]=13' \
    -d 'filter[location]=sof' \
    -d 'order=name.asc' 
```

### Example Response:

```json
{
    "result": 200,
    "message": "Success",
    "meta": {
        "filter": {
            "userid": 13,
            "location": "sof"
        },
        "order": "name.asc",
        "offset": 0,
        "limit": 10,
        "count": 6,
        "total": 6
    },
    "data": [
        {
            "appid": 10006,
            "userid": 13,
            "name": "FLS Application 1",
            "active": "1",
            "security_template": 1,
        },
        {
            "appid": 10007,
            "userid": 13,
            "name": "FLS Application 2",
            "active": "0",
            "security_template": 0,
        },
        // additional application objects...
    ]
}
```
# Updating FLS Application Parameters

**`PATCH /api/application/fls/{appid}`**

This endpoint allows you to update parameters of an existing application. You will need to provide the user's ID, the location of the application, and the application ID.

> **Note:** Application update is not cascading.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| userid | Body & URL | ID of the user. | Yes | Integer |
| appid | Body | ID of the application. This is a required field | Yes | String |
| name | Body | Name of the application. This is a required field | Yes | String |
| location | Body | The desired location for the application. | Yes | String |
| active | Body | The active status of the application. For user role, can only be 0 or 1. For admin role, can be any integer from 0 to 9 | Yes | Integer |
| security_template | Body | The security template ID. Must belong to the user and exist in the database | Yes | Integer |


## Active parameter

| Parameter | Type | Description | User | Admin | Reseller | Support |
| --------- | ---- | ----------- | ---- | ----- | -------- | ------- |
| 0 | Integer | Disable | x | x | x | x |
| 1 | Integer | Enable | x | x | x | x |
| 2 | Integer | Prohibited | - | x | - | - |
| 3 | Integer | Reserved | - | x | - | - |
| 4 | Integer | Suspend - Disable | - | x | - | - |
| 5 | Integer | Suspend - Enable | - | x | - | - |
| 6 | Integer | Reserved | - | x | - | - |
| 7 | Integer | Reserved | - | x | - | - |
| 8 | Integer | Reserved | - | x | - | - |
| 9 | Integer | Deleted | - | x | - | - |

**Admins Only** : If status of user is changed to anything else than Active (1), all his applications goes Suspended with status 4(Previously Disabled) or 5 (Previously Enabled). When status of user is changed again to Active (1), all previously suspended applications, goes in their reagular state. 4 -> Disabled, 5 -> Enabled. 
  
## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| result | Integer | Result code of the operation |
| message | String | Details about the result of the operation |
| data | JSON Object | Contains the updated application data |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The application is updated successfully! |
| 400 | Bad request | There were errors during the process or one of the parameters is missing, empty, or invalid |
| 401 | Unauthorized | Incorrect token or no permissions |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/application/fls/10058 -X PATCH \
    -H "Authorization: Bearer <your_token>" \
    -d '{"userid": 13, "location": "sof", "appid": 10066, "name": "New_App_Name", "active": 1, "security_template": 5}'
```
### Example Response:

> **Change status to ENABLE**
```json
{
    "result": 200,
    "message": "Application updated successfully.",
    "data": {
        "appid": 10071,
        "userid": 13,
        "name": "Patch_99",
        "active": "1",
        "security_template": 3
    },
    "pod": {
        "services": "ifls_pod, ofls_pod, ifls_service, ofls_service",
        "status": "success, success, success, success",
        "httpcode": "201, 201, 201, 201"
    }
}
```

or

> **Change status to DISABLE**
```json
{
    "result": 200,
    "message": "Application updated successfully.",
    "data": {
        "appid": 10071,
        "userid": 13,
        "name": "Patch_99",
        "active": "0",
        "security_template": 0
    },
    "pod": {
        "result": 200,
        "message": "App disabled successfully."
    }
}
```

or

> **Status not changed**
```json
{
    "result": 200,
    "message": "Application updated successfully.",
    "data": {
        "appid": 10070,
        "userid": 13,
        "name": "Patch_99",
        "active": "0",
        "security_template": 0
    },
    "pod": "Preserve current status!"
}
```

or

```json
{
    "result": 400,
    "message": "Invalid Active value. Users can not change active value. Administratively prohibited."
}
```

or 

```json
{
    "result": 400,
    "message": "The security template does not belong to the user."
}
```

# Deleting FLS Application

## Endpoint **DELETE** `/api/application/fls/{appid}`**

This endpoint allows you to delete an existing application. You will need to provide the user's ID, the location of the application, and the application ID.

> **Note:** Application deletion is not cascading.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| userid | Body & URL | ID of the user. | Yes | Integer |
| appid | Body | ID of the application. This is a required field | Yes | String |
| location | Body | The desired location for the application. | Yes | String |


## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| result | Integer | Result code of the operation |
| message | String | Details about the result of the operation |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The application is deleted successfully! |
| 400 | Bad request | There were errors during the process or one of the parameters is missing or empty |
| 401 | Unauthorized | Incorrect token or no permissions |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/application/fls/10066 -X DELETE \
    -H "Authorization: Bearer <your_token>" \
    -d '{"userid": 13, "location": "sof","appid": 10066}'
```

### Example Response:

```json
{
    "result": 200,
    "message": "App deleted successfully."
}
```

or

```json
{
    "result": 400,
    "message": "Errors occurred: \nPod: An error occurred while deleting the Pod 'ifls10065'. HTTP Status Code: 404\nPod: An error occurred while deleting the Pod 'ofls10065'. HTTP Status Code: 404\nService: An error occurred while deleting the Service 's-ifls10065'. HTTP Status Code: 404\nService: An error occurred while deleting the Service 's-ofls10065'. HTTP Status Code: 404\n"
}
```

This markdown document provides all the details to use the endpoint correctly, from the necessary parameters to the response structure, as well as example request and response.

# Creating a VoD (VOD) Application

## Endpoint:  **POST** `/api/application/vod/`

This endpoint allows you to create a new VoD (VOD) application. You will need to provide the user's ID, the name of the application, and the desired location for the application. Optional you can specify webhook, where client to receive notification when files are transcoded.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| userid | Body | ID of the user. | Yes | Integer |
| name | Body | Name of the application. This is a required field | Yes | String |
| location | Body | The desired location for the application. | Yes | String |
| webhook | Body | Webhook to receive notification about processing of files.| No | String |

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| appid | Integer | The internal identifier of the application |
| userid | Integer | The ID of the user who created the application |
| name | String | The name of the application |
| active | Integer | The status of the application (0 for inactive, 1 for active) |
| security_template | Integer | The security template of the application (default is 1) |
| podData | Object | Information about the pod resources created for the application |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The application is created! |
| 400 | Bad request | One of the parameters is missing or empty |
| 401 | Unauthorized | Incorrect username or password |
| 500 | Internal error | The application cannot be created due to an error in the server |

## Example Request

```bash
curl  http://dev.k.cdn.bg/api/application/vod/ -X POST \
    -H "Authorization: Bearer <your_token>" \
	-d '{"userid": 36, "name": "News-VoD", "location": "sof", "webhook" : "http://dev.k.cdn.bg/test/webhook/cust36a"}' 
```

## Example Response

```json
{
    "result": 200,
    "message": "Application created successfully",
    "data": {
        "appid": 10014,
        "userid": 36,
        "name": "News-VoD",
        "security_template": 0,
        "active": 1,
        "source_s3_cred_id": 11,
        "dest_s3_cred_id": 12,
        "customer_webhook": "http:\/\/dev.k.cdn.bg\/test\/webhook\/cust36a",
        "S3": "Newly S3 keys added for User.",
        "podData": {
            "services": "vod_pod, vod_service",
            "status": "success, success",
            "httpcode": "201, 201"
        }
    }
}
```

# VOD Applications Listing

## Endpoint: **GET** /api/application/vod

### Description:
This API endpoint returns a list of VOD Application objects associated with a user, based on the given parameters, or an empty result set.

### Parameters:
| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization  | Header | Bearer token received after authentication | Yes | String |
| location | Query | The desired location for the application. | Yes | String |
| filter | Query | List filter. Mandatory Filters: userid, location. Additional filtering by the following fields is possible: status, name | No | Hash |
| order | Query | List order. Ordering by the following fields is possible: appid, userid, name, active, security_template (asc or desc). Example: name.asc or appid.desc | No | String |
| offset | Query | Result-set offset, used in pagination. Default = 0 | No | Integer |
| limit | Query | Result-set records count. Default = 10 | No | Integer |


### Output data:
| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error |
| message | String | Human-readable result message |
| meta | Object | Meta data object of the result returned, including filter parameters, order, offset, limit, count of results in the set and total results |
| data | Array | A list of VOD Application objects or a message if no application found |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The applications list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Bad request | Invalid request parameters |

### Example Request:

To get all applications associated with user ID 13 and location 'sof', ordered by name in ascending order, you can use:

```bash
curl http://dev.k.cdn.bg/api/application/vod/ -G \
    -H "Authorization: Bearer <your_token>" \
    -d 'filter[userid]=13' \
    -d 'filter[location]=sof' \
    -d 'order=name.asc' 
```

### Example Response:

```json
{
    "result": 200,
    "message": "Success",
    "meta": {
        "filter": {
            "userid": 13,
            "location": "sof"
        },
        "order": "appid.desc",
        "offset": 0,
        "limit": 5,
        "count": 2,
        "total": 2
    },
    "data": [
        {
            "appid": 10008,
            "userid": 13,
            "name": "Real-Vod-01",
            "security_template": 0,
            "active": 1,
            "source_s3_cred_id": 1,
            "dest_s3_cred_id": 2,
            "customer_webhook": null
        },
        {
            "appid": 10007,
            "userid": 13,
            "name": "News",
            "security_template": 0,
            "active": 5,
            "source_s3_cred_id": 1,
            "dest_s3_cred_id": 2,
            "customer_webhook": "http:\/\/dev.k.cdn.bg\/test\/webhook\/index.php?cust10007"
        }
    ]
}
```

# Updating VOD Application Parameters

## Endpoint **PATCH** `/api/application/vod/{appid}`

This endpoint allows you to update parameters of an existing application. You will need to provide the user's ID, the location of the application, and the application ID.

> **Note:** Application update is not cascading.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| userid | Body & URL | ID of the user. | Yes | Integer |
| appid | Body | ID of the application. This is a required field | Yes | String |
| name | Body | Name of the application. This is a required field | Yes | String |
| location | Body | The desired location for the application. | Yes | String |
| active | Body | The active status of the application. For user role, can only be 0 or 1. For admin role, can be any integer from 0 to 9 | Yes | Integer |
| security_template | Body | The security template ID. Must belong to the user and exist in the database | Yes | Integer |
| webhook | Body | Webhook to receive notification about processing of files.| No | String |

## Active parameter

| Parameter | Type | Description | User | Admin | Reseller | Support |
| --------- | ---- | ----------- | ---- | ----- | -------- | ------- |
| 0 | Integer | Disable | x | x | x | x |
| 1 | Integer | Enable | x | x | x | x |
| 2 | Integer | Prohibited | - | x | - | - |
| 3 | Integer | Reserved | - | x | - | - |
| 4 | Integer | Suspend - Disable | - | x | - | - |
| 5 | Integer | Suspend - Enable | - | x | - | - |
| 6 | Integer | Reserved | - | x | - | - |
| 7 | Integer | Reserved | - | x | - | - |
| 8 | Integer | Reserved | - | x | - | - |
| 9 | Integer | Deleted | - | x | - | - |

**Admins Only** : If status of user is changed to anything else than Active (1), all his applications goes Suspended with status 4(Previously Disabled) or 5 (Previously Enabled). When status of user is changed again to Active (1), all previously suspended applications, goes in their reagular state. 4 -> Disabled, 5 -> Enabled. 
  
## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| result | Integer | Result code of the operation |
| message | String | Details about the result of the operation |
| data | JSON Object | Contains the updated application data |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The application is updated successfully! |
| 400 | Bad request | There were errors during the process or one of the parameters is missing, empty, or invalid |
| 401 | Unauthorized | Incorrect token or no permissions |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/application/vod/10017 -X PATCH \
    -H "Authorization: Bearer <your_token>" \
            -d '{"userid": 13, "location": "sof", "name": "Movies-VoD", "appid": 10017, "active": 1 , "security_template": 0 , "webhook" : "http://example.com/webhook" , "dest_s3_cred_id" : "2", "source_s3_cred_id" : "1"}' 

```
### Example Response:

> **Change status to ENABLE**
```json
{
    "result": 200,
    "message": "Application updated successfully.",
    "data": {
        "appid": 10017,
        "userid": 13,
        "name": "Movies-VoD",
        "security_template": 0,
        "active": 1,
        "source_s3_cred_id": 1,
        "dest_s3_cred_id": 2,
        "customer_webhook": "http:\/\/example.com\/webhook"
    },
    "pod": {
        "services": "vod_pod, vod_service",
        "status": "success, success",
        "httpcode": "201, 201"
    }
}
```

or

> **Change status to DISABLE**
```json
{
    "result": 200,
    "message": "Application updated successfully.",
    "data": {
        "appid": 10017,
        "userid": 13,
        "name": "Movies-VoD",
        "security_template": 0,
        "active": 0,
        "source_s3_cred_id": 1,
        "dest_s3_cred_id": 2,
        "customer_webhook": "http:\/\/example.com\/webhook"
    },
    "pod": {
        "result": 200,
        "message": "App disabled successfully."
    }
}
```

or

> **Status not changed**
```json
{
    "result": 200,
    "message": "Application updated successfully.",
    "data": {
        "appid": 10017,
        "userid": 13,
        "name": "Movies-VoD",
        "security_template": 0,
        "active": 1,
        "source_s3_cred_id": 1,
        "dest_s3_cred_id": 2,
        "customer_webhook": "http:\/\/example.com\/webhook"
    },
    "pod": "Preserve current status!"
}
```

or

```json
{
    "result": 400,
    "message": "Invalid Active value. Users can not change active value. Administratively prohibited."
}
```

or 

```json
{
    "result": 400,
    "message": "The security template does not belong to the user."
}
```

# Deleting VOD Application

## Endpoint **DELETE** `/api/application/vod/{appid}`

This endpoint allows you to delete an existing application. You will need to provide the user's ID, the location of the application, and the application ID.

> **Note:** Application deletion is not cascading.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| userid | Body & URL | ID of the user. | Yes | Integer |
| appid | Body | ID of the application. This is a required field | Yes | String |
| location | Body | The desired location for the application. | Yes | String |

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| result | Integer | Result code of the operation |
| message | String | Details about the result of the operation |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The application is deleted successfully! |
| 400 | Bad request | There were errors during the process or one of the parameters is missing or empty |
| 401 | Unauthorized | Incorrect token or no permissions |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/application/vod/10066 -X DELETE \
    -H "Authorization: Bearer <your_token>" \
    -d '{"userid": 13, "location": "sof","appid": 10066}'
```

### Example Response:

```json
{
    "result": 200,
    "message": "App deleted successfully."
}
```

or

```json
{
    "result": 400,
    "message": "Errors occurred: \nPod: An error occurred while deleting the Pod 'vod10066'. HTTP Status Code: 404\nService: An error occurred while deleting the Service 's-vod10066'. HTTP Status Code: 404\n"
}
```


# Browse

## List Objects / Browse across dir/file structure functionality

**GET** `/api/vod/browse`

This endpoint returns a combined list of directories and files associated with a given `userid`, `appid`, and `parent`. Directories are prioritized first before files. If the count of directories is less than the set `limit`, files fill up the remaining slots up to the `limit`.

This endpoint follows the same pattern as `/file` and `/directory` endpoints but unifies their functionalities. It provides a clean and intuitive way for users to retrieve a combined list of directories and files.

### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| Authorization Header | String | Bearer token received after authentication |
| filter | Hash | List filter. Mandatory Filters : userid, appid, parent, location. Filtering by the following fields is also possible: status |
| limit | Integer | Maximum number of objects to return. Default is `10` , maximum is `100`.|
| order | Hash | The field and direction to sort the list by, separated by a dot (`.`). For example, `created.asc`. Possible values : name, created, updated  (asc or desc)|

### Output data

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| meta | Object | Metadata about the request, including filter, order, limit, count (number of objects returned), and total (number of objects matching the filter). |
| data | Hash | An array of objects, each represented as an object with properties. Each object is either a directory or a file. |

### Response codes:

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The list was retrieved successfully |
| 204 | No Results | No matching objects were found |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

### Example Request

```bash
curl http://dev.k.cdn.bg/api/vod/browse/ -G \
    -H "Authorization: Bearer <your_token>" \
    -d 'filter[userid]=13' \
    -d 'filter[appid]=10006' \
    -d 'filter[parent]=0' \
    -d 'filter[location]=sof' \
    -d 'limit=10' \
    -d 'offset=20' \
    -d 'order=created.asc'
```

### Example Response

```json
{
    "result": 200,
    "message": "Success",
    "meta": {
        "filter": {
            "userid": 13,
            "appid": 10006,
            "parent": "6489b32d464636a62d0021cd"
        },
        "order": "path.asc",
        "limit": 10,
        "count": 3
    },
    "data": [
        {
            "Id": "6489b37913b2847ee80f4eb7",
            "name": "VeryFunny",
            "type": "directory",
            "parentId": "6489b32d464636a62d0021cd",
            "userid": 13,
            "appid": 10006,
            "created": "2023-06-14T12:32:57+0000",
            "updated": "2023-06-14T12:32:57+0000"
        },
        {
            "Id": "6492c8d6b95119fb7792a881",
            "filename": "Filename1.mp4",
            "type": "file",
            "parentId": "6489b32d464636a62d0021cd",
            "userid": 13,
            "appid": 10006,
            "url": "\/kcdn-13\/vod10006\/upload\/music\/trans\/Comedy\/Filename1.mp4",
            "status": "new",
            "contenttype": null,
            "filesize": null,
            "created": "2023-06-21T09:54:30+0000",
            "updated": "2023-06-21T10:03:20+0000"
        },
        {
            "Id": "6492c8d6b95119fb7792a886",
            "filename": "Filename2.mp4",
            "type": "file",
            "parentId": "6489b32d464636a62d0021cd",
            "userid": 13,
            "appid": 10006,
            "url": "\/kcdn-13\/vod10006\/upload\/music\/trans\/Comedy\/Filename2.mp4",
            "status": "new",
            "contenttype": null,
            "filesize": null,
            "created": "2023-06-21T09:54:30+0000",
            "updated": "2023-06-21T10:03:20+0000"
        }
    ]
}

```

# Create a directory

**POST** `/api/vod/directory`

This endpoint adds a branch to the site's structure. To create a directory, it is necessary to supply its name and its location through `path` and `parentid` parameters, respectively.

## Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| Authorization Header | String | Bearer token received after authentication |
| userid | Integer | ID of the user. This is a required field |
| appid | Integer | ID of the application. This is a required field |
| Location | String | POP location |
| path | String | Path of the section. This is a required field |
| parentid | Integer | The ID of the parent section. If not selected, the default is 0 |

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| Id | Integer | The internal identifier of the directory |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 200 | Success | The section is created! |
| 400 | Bad request | One of the parameters is missing or empty |
| 401 | Unauthorized | Incorrect username or password |
| 500 | Internal error | The section cannot be created due to an error in the server |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/vod/directory/ -X POST \
    -H "Authorization: Bearer <your_token>" \
	-d '{"userid": 13, "appid": 10006, "location": "sof", "path": "Test_section", "parentid":"12"}' 

```
## Example Response

```json
{
    "result": 200,
    "message": "The section is created",
    "data": {
        "Id": "6489b2c7464636a62d0021cc"
    }
}
```

# Directory Listing

**GET** /api/vod/directory

### Description:
This API endpoint returns a list of Directory objects based on the given parameters, or an empty result set.

### Parameters:
| Name | Type | Description |
|---|---|---|
| Authorization Header | String | Bearer token received after authentication |
| filter | Hash | List filter. Mandatory Filters : userid, appid, location. Filtering by the following fields is possible: parent |
| order | Hash | List order. Ordering by the following fields is possible: path, created, updated  (asc or desc) ex. path.desc or created.asc|
| offset | Integer | Result-set offset, used in pagination. Default = 0 |
| limit | Integer | Result-set records count. Default = 100 |
| fields | Array | The list of additional fields to include in the result-set. Available fields: url - the URL of the directory |
| expand | Array | The list of additional objects to include in the result-set. Available expands: children - the list of child directories |

### Output data:
| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error |
| message | String | Human-readable result message |
| meta | Object | Meta data object of the result returned |
| data | Hash | A list of Directory objects. The key of the Hash is the ID of the directory |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |

### Example Request:

To get all directories with parent ID = 3, ordered by path in ascending order, you can use:

```bash
curl http://dev.k.cdn.bg/api/vod/directory/ -G \
    -H "Authorization: Bearer <your_token>" \
	-d 'filter[userid]=13' \
    -d 'filter[appid]=10006' \
    -d 'filter[location]=sof' \
	-d 'order=path.asc' 
```

### Example Response:

```json
{
    "result": 200,
    "message": "Success",
    "meta": {
        "filter": {
            "userid": 13,
            "appid": 10006,
            "location": "sof"
        },
        "order": "path.asc",
        "offset": 0,
        "limit": 100,
        "count": 6,
        "total": 6
    },
    "data": {
        "64898203b0d08b2d16282854": {
            "Id": "64898203b0d08b2d16282854",
            "name": "dnes",
            "parentId": "64896e7cb0d08b2d16282852"
        },
        "648988cab0d08b2d1628285a": {
            "Id": "648988cab0d08b2d1628285a",
            "name": "hiphop",
            "parentId": "64898885b0d08b2d16282856"
        },
        "6489888fb0d08b2d16282858": {
            "Id": "6489888fb0d08b2d16282858",
            "name": "movies",
            "parentId": "0"
        },
        "64898885b0d08b2d16282856": {
            "Id": "64898885b0d08b2d16282856",
            "name": "music",
            "parentId": "0"
        },
        "64896e7cb0d08b2d16282852": {
            "Id": "64896e7cb0d08b2d16282852",
            "name": "sport",
            "parentId": "0"
        },
        "648988d2b0d08b2d1628285c": {
            "Id": "648988d2b0d08b2d1628285c",
            "name": "trans",
            "parentId": "64898885b0d08b2d16282856"
        }
    }
}
```

# Files

# Create/Add File

**POST** `/api/vod/file`

This endpoint processes the newly uploaded or updated files. When file is uploaded to S3 storage POST request need to be send to endpoint to add file in CDN structure. It also performs several tasks such as checking if the file exists in the S3 storage, getting the metadata about the file (such as size and content type), and creating or updating the file record in the CDN database.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| appid | String | ID of the application. | Yes | Integer |
| location | String | The desired location for the application. | Yes | String |
| dirid | String | 24-character hexadecimal string of the destination directory ID. | Yes | String |
| filename | String | Filename of the desired file which will be uploaded. Valid characters are a-z (case-insensitive), 0-9, "-", and "_". Only `.mp4` are accepted at this time. | Yes | String |

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| result | Integer | The HTTP status code of the request |
| message | String | Informational message |
| data | Object | An object containing information about the file. This object includes: 'id' - The internal identifier of the file in MongoDB, 'userid' - The user ID extracted from the filename, 'appid' - The application ID extracted from the filename, 'url' - The filename (i.e., full path to the file on S3), 'filename' - The base name of the file, QueueID - ID of queue process that can be user from client to check what the actual state of file - queue, transcoding or transcoded.  |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 201 | Success | The file was created or updated successfully |
| 404 | Not found | The file was not found either in the S3 storage or the specified directory does not exist |
| 500 | Internal Server Error | There was an error in processing the request, could be due to inability to access S3 or MongoDB operations |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/vod/file/ -X POST \
    -H "Authorization: Bearer <your_token>" \
	-d '{"filename": "file.mp4", "appid": "10006", "dirid": "6489b32d464636a62d0021cD", "location": "sof"}' 
```


## Example Response
```json
[
    {
        "result": 201,
        "message": "File created",
        "data": {
            "id": "6492c8d6b95119fb7792a881",
            "userid": 13,
            "appid": 10006,
            "url": "/kcdn-13/vod10006/upload/music/hiphop/file.mp4",
            "filename": "file.mp4",
            "QueueID": "64ba1eed07b50a72380e5e69"
        }
    }
]
```

# Listing Files 

**GET** `/api/vod/file`

This document provides details on how to use a provided PHP script to list files, stored in a CDN database.

## API Endpoint

The script creates an API endpoint that accepts HTTP GET requests. The endpoint URL depends on where you deploy this script.


### Parameters:

| Name | Type | Description |
|---|---|---|
| Authorization Header | String | Bearer token received after authentication |
| filter | Hash | List filter. Mandatory Filters : userid, appid, location. Filtering by the following fields is possible: parent, `status` |
| order | Hash | The field and direction to sort the list by, separated by a dot (`.`). For example, `path.asc`.Possible values : path, created, updated  (asc or desc)|
| offset | Integer | Number of files to skip before starting to collect the file list. Default is `0`.|
| limit | Integer | Maximum number of files to return. Default is `100`.|
| fields | Array | Comma-separated list of additional fields to include in the response. Can include `url`. |
| expand | Array | Not used in this script. |

### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| meta | Object | Metadata about the request, including filter, order, offset, limit, count (number of files returned), and total (number of files matching the filter). |
| data | Hash | An array of files, each represented as an object with properties including `Id`, `filename`, `status`, `parentId`, `filesize`, `contenttype`, `created`, `updated`, and possibly `url`. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/vod/file/ -G \
        -H "Authorization: Bearer <your_token>" \
        -d 'filter[userid]=13' \
        -d 'filter[appid]=10006' \
        -d 'filter[location]=sof' \
        -d 'order=path.asc' \
        -d 'filter[parent]=0'
```

## Example Responce

```json
{
    "result": 200,
    "message": "Success",
    "meta": {
        "filter": {
            "userid": 13,
            "appid": 10006,
            "location": "sof"
        },
        "order": "path.asc",
        "offset": 0,
        "limit": 100,
        "count": 5,
        "total": 5
    },
    "data": {
        "6492c8d6b95119fb7792a87c": {
            "Id": "6492c8d6b95119fb7792a87c",
            "filename": "Filename1.mp4",
            "status": "new",
            "parentId": "648988d2b0d08b2d1628285c",
            "filesize": null,
            "contenttype": null,
            "created": "2023-06-21T09:54:30+0000",
            "updated": "2023-06-21T09:59:08+0000"
        },
        "6492c8d6b95119fb7792a881": {
            "Id": "6492c8d6b95119fb7792a881",
            "filename": "Filename1.mp4",
            "status": "new",
            "parentId": "6489b32d464636a62d0021cd",
            "filesize": null,
            "contenttype": null,
            "created": "2023-06-21T09:54:30+0000",
            "updated": "2023-06-21T10:03:20+0000"
        },
        "6492c8d6b95119fb7792a888": {
            "Id": "6492c8d6b95119fb7792a888",
            "filename": "sport.mp4",
            "status": "new",
            "parentId": "0",
            "filesize": null,
            "contenttype": null,
            "created": "2023-06-21T09:54:30+0000",
            "updated": "2023-06-21T10:03:20+0000"
        },
    }
}
```
# Live Streams (FLS)

# Create/Add Live Stream

## ENDPOINT : **POST** `/api/fls/`

This endpoint manage creating streams. file record in the CDN database.

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| appid | String | ID of the application. | Yes | Integer |
| location | String | The desired location for the application. | Yes | String |
| name | String | Human based Name of stream. Valid characters are a-z (case-insensitive), 0-9, "-", "_" and "SPACE". | Yes | String |

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| result | Integer | The HTTP status code of the request |
| message | String | Informational message |
| data | Object | An object containing information about the file. This object includes: `id` - The internal identifier of the file in DB, `userid` - The user ID extracted from the filename, `appid` - The application ID, `name` - Human based name of stream, that name is for client purpouse, actual stream name is `streamname`. Authorization for the the stream is `hash` string. |

## Response codes 

| Code | Name | Description |
| ---- | ---- | ----------- |
| 201 | Success | The file was created or updated successfully |
| 404 | Not found | The file was not found either in the S3 storage or the specified directory does not exist |
| 500 | Internal Server Error | There was an error in processing the request, could be due to inability to access S3 or MongoDB operations |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/fls/ -X POST \
    -H "Authorization: Bearer <your_token>" \
	-d '{"name": "StreamName","appid": "10006", "location": "sof"}'
```


## Example Response
```json
{
    "result": 201,
    "message": "Stream created",
    "data": {
        "userid": 13,
        "appid": 10006,
        "name": "StreamName",
        "streamname": 93966450,
        "hash": "A79B583F",
        "created": "2023-07-28T06:12:18+0000",
        "updated": "2023-07-28T06:12:18+0000",
        "id": "64c35c42caa94e4f5404c947"
    }
}
```

# List Live Streams (or Get Full Info)

## ENDPOINT : **GET** `/api/fls/`

This document provides details on how to use a provided PHP script to list live streams, stored in a CDN database. Or get full Info for stream.

## API Endpoint

The script creates an API endpoint that accepts HTTP GET requests. The endpoint URL depends on where you deploy this script. To get full info for stream including hish `HASH`, that is used to authenticate to streaming servers to initilize streams, you need to use as a request for filter filed `streamid`.


### Parameters:

| Name | Type | Description |
|---|---|---|
| Authorization Header | String | Bearer token received after authentication |
| filter | Hash | List filter. Mandatory Filters : `userid`, `appid`, `location` of `streamname` to get full information to that exact stream including `HASH`. Filtering by the following fields is possible: `name`, `streamname`, `status`, `streamid` |
| order | Hash | The field and direction to sort the list by, separated by a dot (`.`). For example, `name.asc`.Possible values : `name`, `streamname`, `status`, `created`, `updated`  (`asc` or `desc`)|
| offset | Integer | Number of live streams to skip before starting to collect the stream list. Default is `0`.|
| limit | Integer | Maximum number of stream to return. Default is `100`.|
| fields | Array | Comma-separated list of additional fields to include in the response. Can include `url`. |
| expand | Array | Not used in this script. |

### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| meta | Object | Metadata about the request, including filter, order, offset, limit, count (number of files returned), and total (number of files matching the filter). |
| data | Hash | An array of files, each represented as an object with properties including `streamid`, `name`, `streamname`, `appid`, `status`, `created`, `updated`. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/vod/file/ -G \
        -H "Authorization: Bearer <your_token>" \
        -d "filter[userid]=13"
        -d "filter[appid]=10006" \
        -d "filter[location]=sof" \
        -d "order=name.asc" \
        -d "limit=3"
```

## Example Response

```json
{
    "result": 201,
    "message": "Success",
    "meta": {
        "filter": {
            "appid": 10006,
            "location": "sof",
            "userid": 13
        },
        "order": "name.asc",
        "offset": 0,
        "limit": 3,
        "count": 3,
        "total": 8
    },
    "data": {
        "64c369e81ecf56440901d79a": {
            "streamid": "64c369e81ecf56440901d79a",
            "name": "aaalf",
            "streamname": 83571675,
            "appid": 10006,
            "status": 1,
            "created": "2023-07-28T07:10:32+0000",
            "updated": "2023-07-28T07:10:32+0000"
        },
        "64c369e5b589c3a74606c665": {
            "streamid": "64c369e5b589c3a74606c665",
            "name": "aalf",
            "streamname": 82812364,
            "appid": 10006,
            "status": 1,
            "created": "2023-07-28T07:10:29+0000",
            "updated": "2023-07-28T07:10:29+0000"
        },
        "64c3698cb3f18a45fe049e41": {
            "streamid": "64c3698cb3f18a45fe049e41",
            "name": "alf",
            "streamname": 29763694,
            "appid": 10006,
            "status": 1,
            "created": "2023-07-28T07:09:00+0000",
            "updated": "2023-07-28T07:09:00+0000"
        }
    }
}
```

or to get full info
## Example Request
```bash
curl http://dev.k.cdn.bg/api/vod/file/ -G \
        -H "Authorization: Bearer <your_token>" \
        -d "filter[userid]=13"
        -d "filter[appid]=10006" \
        -d "filter[location]=sof" \
        -d "filter[streamid]=64c369e81ecf56440901d79a" \ // This is required to get the HASH
        -d "order=name.asc" \
        -d "limit=3"
```
## Example Response
```json
{
    "result": 201,
    "message": "Success",
    "meta": {
        "filter": {
            "appid": 10006,
            "location": "sof",
            "streamid": "64c369e81ecf56440901d79a",
            "userid": 13
        },
        "order": "name.asc",
        "offset": 0,
        "limit": 3,
        "count": 1,
        "total": 1
    },
    "data": {
        "64c369e81ecf56440901d79a": {
            "streamid": "64c369e81ecf56440901d79a",
            "name": "aaalf",
            "streamname": 83571675,
            "appid": 10006,
            "status": 1,
            "created": "2023-07-28T07:10:32+0000",
            "updated": "2023-07-28T07:10:32+0000",
            "hash": "79C45B99" // This is authorization token for the stream, visible explicit only request is made direct to streamid!
        }
    }
}
```



# Edit Live Stream Details

## Endpoint: **PATCH** `/api/fls/{userid}`

This endpoint allows you to update stream details such as `name`, `status`, `stream name` and `hash`. 

## Parameters

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization Header | Header | Bearer token received after authentication | Yes | String |
| appid | Body | Application id of the stream. Should be numeric | Yes | Integer |
| userid | URL | User id of the stream. Should be numeric | Yes | Integer |
| streamid | Body | ID of the stream. This is a required field | Yes | String |
| location | Body | Updated location of the stream. This is a required field | Yes | String |
| status | Body | Updated status of the stream. Should be a single digit | No | Integer |
| streamname | Body | If set to `True`, a new stream name will be generated | No | Boolean |
| hash | Body | If set to `True`, a new hash will be generated | No | Boolean |


> **Note:** The parameters for the `hash` and the `streamname` are Boolean flags that, when set to true, trigger the generation of new unique values for these parameters. 

## Output data 

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| result | Integer | Result code of the operation |
| message | String | Details about the result of the operation |
| data | JSON Object | Contains the updated stream data |

## Response codes

| Code | Name | Description |
| ---- | ---- | ----------- |
| 201 | Success	| The stream details are updated successfully |
| 400 | Bad request	| There were errors during the process or one of the parameters is missing, empty, or invalid |
| 401 | Unauthorized | Incorrect token or no permissions |
| 500 | Internal Server Error | An unexpected error occurred |

## Example Request

```bash
curl -H "Authorization: <your_token>" http://dev.k.cdn.bg/api/fls/{userid} -X PATCH \
-d '{ "streamid" : "64c369e81ecf56440901d79a","status":1, "streamname": "True", "hash": "True" , "location": "sof" , "appid":"10006"}'
```
## Example Response
```json
{
    "result": 201,
    "message": "Stream updated.",
    "data": {
        "streamid": "64c369e81ecf56440901d79a",
        "userid": 13,
        "appid": 10006,
        "name": "alf",
        "streamname": 95241099,
        "hash": "EBB65FDC",
        "status": 1,
        "location": "sof",
        "created": "2023-07-28T07:10:32+0000",
        "updated": "2023-07-28T11:20:21+0000"
    }
}
```
or

```json
{
    "result": 400,
    "message": "Invalid parameters. Please check your inputs."
}
```

# Create Security Template (url)

## ENDPOINT : **POST** `/api/security/template/`

This document provides details on how to use a provided PHP script to create valid security template.

## API Endpoint

The script creates an API endpoint that accepts HTTP POST requests. To create security template that user can assign to specific Application or Object frontend must
send paramteres using POST request and send data in JSON format. The security of content is complex mechanism and in the same time very flexible. The client can select
only these or all security restriction that match his criteria. If there is a value in one of the criteria, this criteria will be applied automaticaly to security template.
Example if client want to secure his content only by Viewer_IP, and User-Agent, client can select only `verify_ip`,`verify_user_agent` with `True`.
If client want to made some complicated security, he can add country restriction, or even time limits when, the content will be displayed using `country_whitelist`,`country_blacklist`  and `valid_after`,`valid_before`.
Any combination betwen : `verify_ip`,`verify_user_agent`,`country_whitelist`,`country_blacklist`,`valid_after`,`valid_before`,`whitelist_net`,`blacklist_net` and `referrer` is possible. One, two or all parameters can be used when client design his own security criteria.


### Parameters:

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization Header | Header | Bearer token received after authentication. | Yes | String |
| location | URL | Location. | Yes | String |
| type | URL | Type of selected stream - `main` , `object` and etc. | Yes | String |
| template_name | URL | Human readable name of template. | Yes | String |
| referrer | URL | Referrer domain or subdomain that stream can be published. | No | Array |
| verify_ip | URL | Secure content using remote client IP address. `True`/`Flase` | No | Bool |
| verify_user_agent | URL | Secure content using remote client User-Agent including OS, Browser, Versions and etc. `True`/`Flase` | No | Bool |
| valid_after | URL | Time after template is gain access to content in format UNIX TimeStamp. | No | Integer |
| valid_before | URL | Time before template is gain access to content in format UNIX TimeStamp. | No | Integer |
| whitelist_net | URL | White List of IP address or networks that has access to content. | No | Array |
| blacklist_net | URL | Black List of IP address or networks that has NO access to content. | No | Array |
| country_whitelist | URL | White list of country/s with they two letters code that has access to content. | No | Array |
| country_blacklist | URL | Black list of country/s with they two letters code that has NO access to content. | No | Array |

### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| data | JsonData | Detailed output of template content. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request

```bash
curl -X POST http://dev.k.cdn.bg/api/security/template/ \
        -H "Authorization: Bearer <your_token>" \
        -H "Content-Type: application/json" \
        -d '{
            "location": "sof",
            "type": "main",
            "template_name": "First_Template_Name",
            "userid": 13,
            "valid_after": 1572504800,
            "valid_before": 1794040800,
            "verify_ip": true,
            "verify_user_agent": false,
            "referrer": ["domain.com", "cdn-k.*", "*.cdn.bg", "*.k.cdn.bg" , "k.cdn.*"],
            "whitelist_net": ["192.168.1.24", "174.34.55.98", "89.10.11.12/27" , "4.4.4.4/32" , "123.45.67.0/24"],
            "blacklist_net": ["10.0.0.0/8"],
            "country_whitelist": ["US", "CA", "BG", "DE", "IT", "UA"],
            "country_blacklist": ["RU", "TR"]
         }'
    
```

## Example Response

```json
{
    "result": 201,
    "message": "Success",
    "data": {
        "template": {
            "created": "2023-09-08T11:03:04+0000",
            "updated": "2023-09-08T11:03:04+0000",
            "location": "sof",
            "userid": 13,
            "name": "First_Template_Name",
            "type": "main",
            "verify_ip": true,
            "verify_user_agent": false
        },
        "timerange": {
            "valid_after": "2019-10-31T06:53:20+0000",
            "valid_before": "2026-11-07T08:40:00+0000"
        },
        "referrer": {
            "domains": [
                "domain.com",
                "cdn-k.*",
                "*.cdn.bg",
                "*.k.cdn.bg",
                "k.cdn.*"
            ]
        },
        "whitelist": {
            "nets": [
                "192.168.1.24",
                "174.34.55.98",
                "89.10.11.12\/27",
                "4.4.4.4\/32",
                "123.45.67.0/24"
            ]
        },
        "blacklist": {
            "nets": [
                "10.0.0.0\/8"
            ]
        },
        "country_whitelist": {
            "codes": [
                "US",
                "CA",
                "BG",
                "DE",
                "IT",
                "UA"
            ]
        },
        "country_blacklist": {
            "codes": [
                "RU",
                "TR"
            ]
        }
    }
}
```

# Edit Security Template (url)

## ENDPOINT : **PATCH** `/api/security/template/{user_id}`

This document provides details on how to use a provided PHP script to edit security template.

## API Endpoint

The script creates an API endpoint that accepts HTTP PATCH requests. To edit security template that user can assign to specific Application or Object frontend must
send paramteres using PATCH request and send data in JSON format. Security Template ID (stid) is mandatory to edit epcific template. The security of content is complex mechanism and in the same time very flexible. The client can select only these or all security restriction that match his criteria. If there is a value in one of the criteria, this criteria will be applied automaticaly to security template. Example if client want to secure his content only by Viewer_IP, and User-Agent, client can select only `verify_ip`,`verify_user_agent` with `True`. If client want to made some complicated security, he can add country restriction, or even time limits when, the content will be displayed using `country_whitelist`,`country_blacklist`  and `valid_after`,`valid_before`.
Any combination betwen : `verify_ip`,`verify_user_agent`,`country_whitelist`,`country_blacklist`,`valid_after`,`valid_before`,`whitelist_net`,`blacklist_net` and `referrer` is possible. One, two or all parameters can be used when client design his own security criteria.

**_NOTE:_** All parameters will be overwriten with new ones!

### Parameters:

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization Header | Header | Bearer token received after authentication. | Yes | String |
| location | URL | Location. | Yes | String |
| stid | Body | Security Template ID | Yes | String |
| type | Body | Type of selected stream - `main` , `object` and etc. | Yes | String |
| template_name | Body | Human readable name of template. | Yes | String |
| referrer | Body | Referrer domain or subdomain that stream can be published. | No | Array |
| verify_ip | Body | Secure content using remote client IP address. `True`/`Flase` | No | Bool |
| verify_user_agent | Body | Secure content using remote client User-Agent including OS, Browser, Versions and etc. `True`/`Flase` | No | Bool |
| valid_after | Body | Time after template is gain access to content in format UNIX TimeStamp. | No | Integer |
| valid_before | Body | Time before template is gain access to content in format UNIX TimeStamp. | No | Integer |
| whitelist_net | Body | White List of IP address or networks that has access to content. | No | Array |
| blacklist_net | Body | Black List of IP address or networks that has NO access to content. | No | Array |
| country_whitelist | Body | White list of country/s with they two letters code that has access to content. | No | Array |
| country_blacklist | Body | Black list of country/s with they two letters code that has NO access to content. | No | Array |

### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| data | JsonData | Detailed output of template content. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request

```bash
curl -X PATCH http://dev.k.cdn.bg/api/security/template/ \
         -H "Authorization: Bearer <your_token>" \
         -H "Content-Type: application/json" \
         -d '{
            "stid": "64faff68f0b3432f030b30c4",
            "location": "sof",
            "type": "main",
            "template_name": "Edit_First_Template_name_9",
            "userid": 13,
            "valid_after": 1572504800,
            "valid_before": 1794040800,
            "verify_ip": true,
            "verify_user_agent": false,
            "referrer": ["ed.domain.com", "ed.cdn-k.*", "*.cdn.bg", "*.k.cdn.bg" , "k.cdn.*"],
            "whitelist_net": ["192.168.99.24", "174.99.55.98", "89.10.11.12/27" , "4.4.4.4/32", "0.0.0.0/0", "0.0.0.0/16"],
            "blacklist_net": ["10.0.0.0/8", "10.0.99.0"],
            "country_whitelist": ["US", "CA", "BG", "DE", "IT", "UA", "RO"],
            "country_blacklist": ["RU", "TR", "NI"]
         }'

    
```

## Example Response

```json
{
    "result": 201,
    "message": "Success",
    "data": {
        "template": {
            "updated": "2023-09-12T10:29:21+0000",
            "location": "sof",
            "userid": 13,
            "name": "Edit_First_Template_name_9",
            "type": "main",
            "verify_ip": true,
            "verify_user_agent": false,
            "stid": "64faff68f0b3432f030b30c4"
        },
        "timerange": {
            "valid_after": "2019-10-31T06:53:20+0000",
            "valid_before": "2026-11-07T08:40:00+0000"
        },
        "referrer": {
            "domains": [
                "ed.domain.com",
                "ed.cdn-k.*",
                "*.cdn.bg",
                "*.k.cdn.bg",
                "k.cdn.*"
            ]
        },
        "whitelist": {
            "nets": [
                "192.168.99.24",
                "174.99.55.98",
                "89.10.11.12\/27",
                "4.4.4.4\/32",
                "0.0.0.0\/0",
                "0.0.0.0\/16"
            ]
        },
        "blacklist": {
            "nets": [
                "10.0.0.0\/8",
                "10.0.99.0"
            ]
        },
        "country_whitelist": {
            "codes": [
                "US",
                "CA",
                "BG",
                "DE",
                "IT",
                "UA",
                "RO"
            ]
        },
        "country_blacklist": {
            "codes": [
                "RU",
                "TR",
                "NI"
            ]
        }
    }
}
```


# List Security Templates (or Get Full Info)

## ENDPOINT : **GET** `/api/security/template/`

This document provides details on how to use a provided PHP script to list live streams, stored in a CDN database. Or get full Info for stream.

## API Endpoint

The script creates an API endpoint that accepts HTTP GET requests. The endpoint URL depends on where you deploy this script. To get full info for stream including hish `HASH`, that is used to authenticate to streaming servers to initilize streams, you need to use as a request for filter filed `streamid`.


### Parameters:

| Name | Type | Description |
|---|---|---|
| Authorization Header | String | Bearer token received after authentication |
| filter | Hash | List filter. Mandatory Filters : `userid`, `location`, `stid` or `name` to get full information is mandatory to use `stid`. Filtering by the following fields is possible: `name`, `streamname`, `status`, `streamid` |
| order | Hash | The field and direction to sort the list by, separated by a dot (`.`). For example, `name.asc`.Possible values : `name`, `stid`, `created`, `updated`  (`asc` or `desc`)|
| offset | Integer | Number of live streams to skip before starting to collect the stream list. Default is `0`.|
| limit | Integer | Maximum number of stream to return. Default is `100`.|
| fields | Array | Comma-separated list of additional fields to include in the response. Can include `url`. |
| expand | Array | Not used in this script. |

### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| meta | Object | Metadata about the request, including filter, order, offset, limit, count (number of files returned), and total (number of files matching the filter). |
| data | Hash | An array of files, each represented as an object with properties including `stid`, `template_name`, `created`, `updated` and all data that is asociated with template. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request (List)

```bash
curl http://dev.k.cdn.bg/api/security/template/ -G \
    -H "Authorization: Bearer <your_token>" \
    -d "filter[location]=sof" \
    -d "order=name.asc" \
    -d "limit=3"
```

## Example Response (List)
```json
{
    "result": 201,
    "message": "Success",
    "meta": {
        "filter": {
            "location": "sof",
            "userid": 13
        },
        "order": "template.name.asc",
        "offset": 0,
        "limit": 3,
        "count": 7,
        "total": 5
    },
    "data": {
        "64faff68f0b3432f030b30c4": {
            "template": {
                "updated": "2023-09-12T10:38:47+0000",
                "location": "sof",
                "userid": 13,
                "name": "Edit_First_Template_name_9",
                "type": "main",
                "verify_ip": true,
                "verify_user_agent": false
            },
            "timerange": {
                "valid_after": "2019-10-31T06:53:20+0000",
                "valid_before": "2026-11-07T08:40:00+0000"
            },
            "referrer": {
                "domains": [
                    "ed.domain.com",
                    "ed.cdn-k.*",
                    "*.cdn.bg",
                    "*.k.cdn.bg",
                    "k.cdn.*"
                ]
            },
            "whitelist": {
                "nets": [
                    "192.168.99.24",
                    "174.99.55.98",
                    "89.10.11.12\/27",
                    "4.4.4.4\/32",
                    "0.0.0.0\/0",
                    "0.0.0.0\/16"
                ]
            },
            "blacklist": {
                "nets": [
                    "10.0.0.0\/8",
                    "10.0.99.0"
                ]
            },
            "country_whitelist": {
                "codes": [
                    "US",
                    "CA",
                    "BG",
                    "DE",
                    "IT",
                    "UA",
                    "RO"
                ]
            },
            "country_blacklist": {
                "codes": [
                    "RU",
                    "TR",
                    "NI"
                ]
            }
        },
        "64fad5f1f30225aeea0b1d68": {
            "template": {
                "created": "2023-09-08T08:06:09+0000",
                "updated": "2023-09-08T08:06:09+0000",
                "location": "sof",
                "userid": 13,
                "name": "First_Template_name_1",
                "type": "main",
                "verify_ip": true,
                "verify_user_agent": true
            },
            "timerange": {
                "valid_after": "2019-10-31T06:53:20+0000",
                "valid_before": "2026-11-07T08:40:00+0000"
            },
            "referrer": {
                "domains": [
                    "domain.com",
                    "cdn.*",
                    "*.cdn.bg",
                    "*.k.cdn.bg",
                    "k.cdn.*"
                ]
            },
            "whitelist": {
                "nets": [
                    "192.168.1.0\/24",
                    "174.34.55.98",
                    "8.8.8.8",
                    "4.4.4.4",
                    "4.0.0.0\/8",
                    "0.0.0.0\/16"
                ]
            },
            "blacklist": {
                "nets": [
                    "10.0.0.0\/8"
                ]
            },
            "country_whitelist": {
                "codes": [
                    "US",
                    "CA",
                    "BG",
                    "DE",
                    "IT",
                    "UA"
                ]
            },
            "country_blacklist": {
                "codes": [
                    "RU",
                    "TR"
                ]
            }
        },
        "64fad9dbf0b3432f030b30c3": {
            "template": {
                "created": "2023-09-08T08:22:51+0000",
                "updated": "2023-09-08T08:22:51+0000",
                "location": "sof",
                "userid": 13,
                "name": "First_Template_name_2",
                "type": "main",
                "verify_ip": true,
                "verify_user_agent": false
            },
            "timerange": {
                "valid_after": "2019-10-31T06:53:20+0000",
                "valid_before": "2026-11-07T08:40:00+0000"
            },
            "referrer": {
                "domains": [
                    "domain.com",
                    "cdn-k.*",
                    "*.cdn.bg",
                    "*.k.cdn.bg",
                    "k.cdn.*"
                ]
            },
            "whitelist": {
                "nets": [
                    "192.168.1.24",
                    "174.34.55.98",
                    "89.10.11.12\/27",
                    "4.4.4.4\/32",
                    "0.0.0.0\/0",
                    "0.0.0.0\/16"
                ]
            },
            "blacklist": {
                "nets": [
                    "10.0.0.0\/8"
                ]
            },
            "country_whitelist": {
                "codes": [
                    "US",
                    "CA",
                    "BG",
                    "DE",
                    "IT",
                    "UA"
                ]
            },
            "country_blacklist": {
                "codes": [
                    "RU",
                    "TR"
                ]
            }
        }
    }
}
```


## Example Request (Get info for specific Security Template ID (stid))

```bash
http://dev.k.cdn.bg/api/security/template/ -G \
    -H "Authorization: Bearer <your_token>" \
    -d "filter[location]=sof" \
    -d "filter[stid]=65000a4e6d21af897f017468"
```

## Example Response (Get info for specific Security Template ID (stid))

```json
{
    "result": 201,
    "message": "Success",
    "meta": {
        "filter": {
            "location": "sof",
            "stid": "65000a4e6d21af897f017468",
            "userid": 13
        },
        "order": "template.name.asc",
        "offset": 0,
        "limit": 3,
        "count": 7,
        "total": 1
    },
    "data": {
        "65000a4e6d21af897f017468": {
            "template": {
                "created": "2023-09-12T06:50:54+0000",
                "updated": "2023-09-12T06:50:54+0000",
                "location": "sof",
                "userid": 13,
                "name": "First_Template_name_STID",
                "type": "main",
                "verify_ip": true,
                "verify_user_agent": false
            },
            "timerange": {
                "valid_after": "2019-10-31T06:53:20+0000",
                "valid_before": "2026-11-07T08:40:00+0000"
            },
            "referrer": {
                "domains": [
                    "domain.com",
                    "cdn-k.*",
                    "*.cdn.bg",
                    "*.k.cdn.bg",
                    "k.cdn.*"
                ]
            },
            "whitelist": {
                "nets": [
                    "192.168.1.24",
                    "174.34.55.98",
                    "89.10.11.12\/27",
                    "4.4.4.4\/32",
                    "0.0.0.0\/0",
                    "0.0.0.0\/16"
                ]
            },
            "blacklist": {
                "nets": [
                    "10.0.0.0\/8"
                ]
            },
            "country_whitelist": {
                "codes": [
                    "US",
                    "CA",
                    "BG",
                    "DE",
                    "IT",
                    "UA"
                ]
            },
            "country_blacklist": {
                "codes": [
                    "RU",
                    "TR"
                ]
            }
        }
    }
}
```
# Create Player Template (url)

## ENDPOINT : **POST** `/api/player/template/`

This document provides details on how to use a provided PHP script to create valid player template.

## API Endpoint

The script creates an API endpoint that accepts HTTP POST requests. To create player template that user can assign to specific Application or Object frontend must
send paramteres using POST request and send data in JSON format. Player Template define outfit/visualisation of html5 video player, that bring content to viewer. In player template not visualisation of video player is modified, but also Advertising model. At the moment only PreRoll Tag is supported, which provide preroll advirtisment.


### Parameters:

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization Header | Header | Bearer token received after authentication. | Yes | String |
| location | URL | Location. | Yes | String |
| userid | URL | User ID. | No | Integer |
| template_name | URL | Human readable name of template. | Yes | String |
| skin | URL | Select player skin from various predefined. Available options are : `shaka`,`pinko`,`chrome`,`flow`,`jwlike`,`mockup`, `nuevo`,`party`,`pinko`,`roundal`,`shaka`,`slategrey` and `treso`. | No | String |
| pre_roll_tar | URL | URL address of preroll tag. Note. Add url whithout schema (http or https).| No | String |

### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| data | JsonData | Detailed output of template content. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request

```bash
curl -X POST http://dev.k.cdn.bg/api/player/template/ \
        -H "Authorization: Bearer <your token>" \
        -H "Content-Type: application/json" \
        -d '{
            "location": "rome",
            "template_name": "Publish_player_template",
            "userid": 201,
            "skin": "jwlike",
            "pre_roll_tag": "pubads.g.doubleclick.net/gampad/live/ads?iu=/157018629/Nova_bg/Live_stream/Preroll_Novanews&description_url=https%3A%2F%2Fnova.bg%2Flive%2Fnews&tfcd=0&npa=0&sz=640x480%7C1280x720&gdfp_req=1&output=vast&unviewed_position_start=1&env=vp&impl=s&correlator="
         }'
    
```

## Example Response

```json
{
    "result": 201,
    "message": "Success",
    "data": {
        "template": {
            "created": "2023-12-11T10:44:25+0000",
            "updated": "2023-12-11T10:44:25+0000",
            "location": "rome",
            "userid": 201,
            "name": "Publish1_player_template",
            "ptid": "6576e809fc89b41ae503913e"
        },
        "skin": "jwlike",
        "pre_roll_tag": "pubads.g.doubleclick.net\/gampad\/live\/ads?iu=\/157018629\/Nova_bg\/Live_stream\/Preroll_Novanews&description_url=https%3A%2F%2Fnova.bg%2Flive%2Fnews&tfcd=0&npa=0&sz=640x480%7C1280x720&gdfp_req=1&output=vast&unviewed_position_start=1&env=vp&impl=s&correlator="
    }
}
```

# Edit Player Template (url)

## ENDPOINT : **PATCH** `/api/player/template/`

This document provides details on how to use a provided PHP script to edit player template.

## API Endpoint

The script creates an API endpoint that accepts HTTP PATCH requests. To edit player template that user can assign to specific Application or Object frontend must
send paramteres using PATCH request and send data in JSON format. Player Template ID (ptid) is mandatory to edit epcific template. In edit section user can change `template_name`, `skin` and `pre_roll_tag`. If Advirtesment campaingn is over, to remove VAST tag, `pre_roll_tag` can be send with `0` or `null` as a value.

**_NOTE:_** All parameters will be overwriten with new ones!

### Parameters:

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization Header | Header | Bearer token received after authentication. | Yes | String |
| location | URL | Location. | Yes | String |
| stid | Body | Security Template ID | Yes | String |
| userid | Body | User ID | No | Integer |
| template_name | URL | Human readable name of template. | Yes | String |
| skin | URL | Select player skin from various predefined. Available options are : `shaka`,`pinko`,`chrome`,`flow`,`jwlike`,`mockup`, `nuevo`,`party`,`pinko`,`roundal`,`shaka`,`slategrey` and `treso`. `null` or `0` to disable/remove value - in this case default skin will be loaded. | No | String |
| pre_roll_tar | URL | URL address of preroll tag. Note. Add url whithout schema (http or https). `null` or `0` to disable/remove Pre Roll Tag| No | String |

### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| data | JsonData | Detailed output of template content. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request

```bash
curl -X PATCH http://dev.k.cdn.bg/api/player/template/edit.php \
        -H "Authorization: Bearer <your token>" \
        -H "Content-Type: application/json" \
        -d '{
            "location": "rome",
            "ptid": "65731c86624ff9051106f9fc",
            "template_name": "Edit_1_player_template",
            "userid": 201,
            "skin": "pinko",
            "pre_roll_tag": "0" // remove VAST tag
         }'

    
```

## Example Response

```json
{
    "result": 201,
    "message": "Success",
    "data": {
        "template": {
            "updated": "2023-12-11T10:51:20+0000",
            "location": "rome",
            "userid": 201,
            "template_name": "Edit_1_player_template",
            "created": "2023-12-08T13:39:18+0000",
            "ptid": "65731c86624ff9051106f9fc"
        },
        "skin": "pinko",
        "pre_roll_tag": null
    }
}
```


# List Player Templates

## ENDPOINT : **GET** `/api/player/template/`

This document provides details on how to use a provided PHP script to list player templates from a CDN database.

## API Endpoint

The script creates an API endpoint that accepts HTTP GET requests. It returns list of all player templates that user is generated based on specific criteria.


### Parameters:

| Name | Type | Description |
|---|---|---|
| Authorization Header | String | Bearer token received after authentication |
| filter | Hash | List filter. Mandatory Filters : `userid`, `location`, `ptid` or `name` to get full information is mandatory to use `ptid`. Filtering by the following fields is possible: `name`, `location`, `ptid`, `userid` |
| order | Hash | The field and direction to sort the list by, separated by a dot (`.`). For example, `name.asc`.Possible values : `name`, `ptid`, `created`, `updated`  (`asc` or `desc`)|
| offset | Integer | Number of live streams to skip before starting to collect the stream list. Default is `0`.|
| limit | Integer | Maximum number of stream to return. Default is `100`.|
| fields | Array | Comma-separated list of additional fields to include in the response. Can include `url`. |
| expand | Array | Not used in this script. |

### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| meta | Object | Metadata about the request, including filter, order, offset, limit, count (number of files returned), and total (number of files matching the filter). |
| data | Hash | An array of files, each represented as an object with properties including `ptid`, `name`, `created`, `updated` and all data that is asociated with template. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request (List)

```bash
curl -H "Authorization: Bearer <your token>" \
     http://dev.k.cdn.bg/api/player/template/ -G \
    -d "filter[location]=rome" \
    -d "order=name.asc" \
    -d "limit=3"


```

## Example Response (List)
```json
{
    "result": 201,
    "message": "Success",
    "meta": {
        "filter": {
            "location": "rome",
            "userid": 201
        },
        "order": "template.name.asc",
        "offset": 0,
        "limit": 3,
        "count": 3,
        "total": 11
    },
    "data": {
        "65731ca2e43323a1d40e4079": {
            "template": {
                "updated": "2023-12-08T14:13:47+0000",
                "location": "rome",
                "userid": 201,
                "template_name": "Publish_111_player_template",
                "created": "2023-12-08T13:39:46+0000"
            }
        },
        "65731c86624ff9051106f9fc": {
            "template": {
                "updated": "2023-12-11T10:51:20+0000",
                "location": "rome",
                "userid": 201,
                "template_name": "Edit_1_player_template",
                "created": "2023-12-08T13:39:18+0000"
            },
            "skin": "pinko",
            "pre_roll_tag": null
        },
        "655624003c31b6397b9e61ac": {
            "template": {
                "updated": "2023-12-06T09:39:17+0000",
                "location": "rome",
                "userid": 201,
                "name": "First Template",
                "created": "2023-12-06T09:39:17+0000"
            },
            "skin": "shaka",
            "pre_roll_tag": null
        }
    }
}
```

# Get/Publish Iframe

## ENDPOINT : **POST** `/api/iframe/{code}`

This document provides details on how to use a provided PHP script to get iframe template that generate html code for video content.

### Publish Iframe into web page

To publish iframe html code into a web page you need to use it like that : 

```html
<iframe width="720" height="405" src="//dev.k.cdn.bg/api/iframe/5TQIPO" scrolling="no" frameborder="0" mozallowfullscreen="true" webkitallowfullscreen="true" allowfullscreen="true"></iframe>
```

# Create Iframe

## ENDPOINT : **POST** `/api/iframe`

This document provides details on how to use a provided PHP script to create iframe template that generate html code for video content.

## API Endpoint

The script describe an API endpoint that accepts HTTP POST requests. To create iframe code to include into your website. Optional parameters are to describe if you want to apply `security_template` or `player_template` to that Iframe, if you not specify them the default ones will be used. Ti create Iframe you need to provide `appid` - valid application ID, `location`, `type` of stream (vod or fls), `streamid` - valid stream ID to which Iframe will be assigned.

### Parameters:

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| appid | String | ID of the application. | Yes | Integer |
| location | String | The desired location for the application. | Yes | String |
| type | String | Type of stream fls or vod. | Yes | String |
| streamid | String | Stream ID in 24 charracters digits/alphabet. | Yes | String |
| security_template | String | Security Template ID in 24 characters digits/alphabet. | No | String |
| player_template | String | Player Template ID in 24 characters digits/alphabet. | No | String |


### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| data | Array | With information about newely created Iframe code. |


### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request

```bash
curl http://dev.k.cdn.bg/api/iframe/ -X POST \
        -H "Authorization: Bearer <your_token>" \
        -H "Content-Type: application/json" \
        -d '{"type": "fls","streamid": "65699bcadfdb22509603bf46", "location": "sof" , "security_template": "65605ae044e3b8dd690712f2", "player_template":  "655624003b31b6397b9e61ac"}'

```

## Example Response

```json
{
    "result": 201,
    "message": "Iframe created",
    "data": {
        "userid": 201,
        "appid": 20005,
        "code": "5TQIPO",
        "streamid": "65699bcadfdb22509603bf46",
        "type": "fls",
        "status": 1,
        "location": "rome",
        "created": "2023-12-01T11:26:14+0000",
        "updated": "2023-12-01T11:26:14+0000",
        "player_template": "655624003c31b6397b9e61ac",
        "security_template": "65605ae044e3b8dd690712f2",
        "iframeid": "6569c2d6624af9051106f9f6"
    }
}

```


# List/Search Iframe 

## ENDPOINT : **GET** `/api/iframe/`

This document provides details on how to use a provided PHP script to list iframes stored in CDN database. Or get full Info about iframe code.

## API Endpoint

The script creates an API endpoint that accepts HTTP GET requests. Using API endpoint you can list iframe codes with their different parameters based on various criteria. You can use `filter` to specify in details exact iframe that will be listed or group of iframes. Available filters are `code`, `streamid`, `iframeid`, `appid`. Also order directive `asc` or `desc` based on `code`, `streamid`, `iframeid`, `appid`, `created` and `updated`. 


### Parameters:

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| filter | Body | List based on filter. Mandatory filter is `location`. Optional are `code`, `appid`, `streamid`, `iframeid` .| Yes | Hash |
| order | Body | The field and direction to sort the list by, separated by a dot (`.`). For example, `name.asc`.Possible values : `code`, `streamid`, `iframeid`, `appid`, `created` and `updated` (`asc` or `desc`) | No | Hash |
| offset | Body | Number of live streams to skip before starting to collect the stream list. Default is `0`. | No | Integer |
| limit | Body | Maximum number of stream to return. Default is `100`. | No | Integer |


### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| meta | Object | Metadata about the request, including filter, order, offset, limit, count (number of files returned), and total (number of files matching the filter). |
| data | Hash | An array of iframe, each represented as an object with its properties and associated data. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request (List)

```bash
curl http://dev.k.cdn.bg/api/iframe/ -G \
    -H "Authorization: Bearer <your_token>" \
    -d "filter[location]=sof" \
    -d "order=name.asc" \
    -d "limit=3"
```

```txt

Filters : 
    filter[location]=String // Required
    filter[code]=String // 6 Character Iframe Code
    filter[appid]=Integer // 5 Characters of Application ID
    filter[streamid]=String // 24 Characters of StreamID
    filter[frameid]=String // 24 Characters of StreamID
    filter[created]=String // Datetime
    filter[updated]=String // Datetime

Oder :
    order={code,streamid,iframeid,appid,created,updated}.{ask,desc}

Offset : 
    offset=Integer 

Limit : 
    limit=Integer // From 0 to 100

```
## Example Response (List)
```json
{
    "result": 201,
    "message": "Success",
    "meta": {
        "filter": {
            "location": "rome",
            "userid": 201
        },
        "order": "code.desc",
        "offset": 0,
        "limit": 3,
        "count": 8,
        "total": 13
    },
    "data": {
        "6569a913bd91dcc2c3046607": {
            "userid": 201,
            "appid": 20005,
            "code": "wecRAN",
            "streamid": "65699bcfdfdb22509603bf46",
            "type": "fls",
            "status": 1,
            "location": "rome",
            "created": "2023-12-01T09:36:19+0000",
            "updated": "2023-12-01T09:36:19+0000",
            "player_template": "655624003c31b6397b9e61ac",
            "security_template": "65605be044e3b8dd690712f2"
        },
        "65699c57fc89b41ae5039134": {
            "userid": 201,
            "appid": 20005,
            "code": "sByLdJ",
            "streamid": "65699bcfdfdb22509603bf46",
            "type": "fls",
            "status": 1,
            "location": "rome",
            "security_template": "0"
        },
        "655621ea3c31b6397b9e61aa": {
            "code": "kalin7",
            "userid": 201,
            "appid": 20005,
            "type": "fls",
            "location": "rome",
            "streamid": "654e1c4d624ff9051106f9f2",
            "created": "2023-11-24T08:16:32+0000",
            "updated": "2023-11-24T08:16:32+0000"
        }
    }
}
```

# Edit Iframe 

## ENDPOINT : **PATCH** `/api/iframe/`

This document provides details on how to use a provided PHP script to edit iframes stored in CDN database.

## API Endpoint

The script creates an API endpoint that accepts HTTP `PATCH` requests. Using API endpoint you can edit iframe codes with their different parameters based on various criteria. You can edit/change `security_template`, `player_template` and `status`. Both templates are used to customize security permisions or player design.


### Parameters:

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| Authorization | Header | Bearer token received after authentication. | Yes | String |
| frameid | Body | Id of edited frame | Yes | String |
| location | Body | Location | Yes | String |
| status | Body | Status of current Iframe code : 1-enabled or 2-disabled | No | Integer |
| security_template | Body | Id of predefined security template or 0 to remove that template | No | Integer |
| player_template | Body | Id of predefined player template or 0 to remove that template | No | Integer |


### Output data:

| Parameter | Type | Description |
|---|---|---|
| result | Integer | Result code. 200 for successful operation or status code for error. |
| message | String | Human-readable result message. |
| data | Hash | An array of iframe, each represented as an object with its properties and associated data. |

### Response codes:
| Code | Name | Description |
|---|---|---|
| 200 | Success | The directory list is retrieved |
| 204 | No results | The applied filters produce an empty result-set |
| 400 | Error | If an error occurs, the API returns a JSON object with `result` set to `400` and `message` set to an error message. |

## Example Request (Edit)

```bash
curl http://dev.k.cdn.bg/api/iframe/ -X PATCH \
    -H "Authorization: Bearer <your_token>" \
    -d '{
        "iframeid": "6569e3f8fc89b41ac5039137",
        "location": "SOF",
        "status": 1,
        "player_template": 0,
        "security_template": 0
        }'

```
## Example Response (Edit)
```json
{
    "result": 200,
    "message": "Success",
    "data": {
        "userid": 201,
        "appid": 20005,
        "code": "MBC2kJ",
        "streamid": "65699bcfdfdb22509603bf46",
        "type": "fls",
        "status": 1,
        "location": "sof",
        "created": "2023-12-01T13:47:36+0000",
        "updated": "2023-12-04T13:44:40+0000",
        "player_template": 0,
        "security_template": 0,
        "iframeid": "6569e3f8fc89b41ac5039137"
    }
}
```

# Fetching Statistics Data (stats)

## Endpoint:  **GET** `/api/stats/`

This endpoint retrieves a data in Json format to be used in statistics. 

## Parameters and Values


| Name | Values | Required | Default |
| ---- | ----- | -------- | ------ |
| total | matrix / vector| No | none |
| range | vector / period | Yes | none |
| graph | general / country / os | Yes | None |
| graph_type | data / viewers | Yes | None |
| start | UnixTimestamp | No | Depends on range |
| end | UnixTimestamp | No | Depends on range |

### Parameter 'total' : 
Used to display Total bytes of generated traffic based on below filters
-	`vector` value return a single array with single value. It can be used when you send to user a widget with a single value in it (ex. 200TB).It can be for a single moment in time or for period.  Example. In 20.Jan.2024 at 13:30 – generated traffic is 10GB or between 20.Jan.2024 at 13:30 and 21.Jan.2024 at 13:30 – generated traffic is 600GB. Received value is a single.
-	`matrix` value return a full size array with incremental values in it. It can be used when you want to draw a graphic how the total generated traffic increase over time. Received data will be a table with increased value for specific timestamp. 
Example between 20.Jan.2024 at 13:30 and 21.Jan.2024 at 13:30 – generated traffic is :
1705928437 – 10132321
1705928497 – 20234779
…
1705932037 – 610132321
-	`not set` will be ignored and return result based on below request. When Total variable isn’t set the API will return values in matrix or vector variant for data or viewers based on other criteria. 
Usage : `total=vector` or `total=matrix`

### Parameter 'range' :
Describe type of time range that will be used to generate graphic bases on below filters
-	`instant` will return single value for specific time (described in TS in variable ‘start’). It’s useful when display single point of data in exact moment of time, it can be data or viewers based on below criteria for just a single timestamp
-	`period` will return an array of values for specific period (Using ‘start’ and ‘end’ variables). It will be used when data in table variant or graph is needed. It returns multiple values for each timestamp between `start` and `end` period.
Usage : `range=instant` or `range=period`

### Parameter 'graph' :
Select what type of data will be returner.
-	`general` – return general data based only on traffic data of viewers counter.
-	`country` –  return result based by Country (Origin of request) or for specific country if filter[‘country’] is described. It can be used when data or number of viewers need to be separate based on country of viewer.
-	`os` – return result based on Operating System or for specific operating system if filter[‘os’] is described. It can be used when data or number of viewers need to be separate based on used operating system of viewer.
Usage: `graph=general` or `graph=country` or `graph=os`

### Parameter 'graph_type' :
Select criteria what type of data is returning : 
-	`data` – returns data in bytes.
-	`viewers` – return number of uniqe viewers. Counter of simultaneous viewers on specific time or period.
Usage : `graph_type=data` or `graph_type=viewers`

### Values for parameters 'start' and 'end'
-   `start` - define begin of the period (Unix Timestamp), or exact time when `range=instant`
| if varible 'start' not set and : | range=period | range=instant |
| -------------------------------- | ------------ | -------------- |
| `start` value will be set to :  | time_now() - 1h | time_now() |

-   `end` - define end of the period (Unix Timestamp), not in use when `range=instant`
| if varible 'end' not set and : | range=period | range=instant |
| -------------------------------- | ------------ | -------------- |
| `start` value will be set to :  | start + 1h | Null |
 
## Filters
-   `location` Specify the location for data retrieval.
-   `type`: Define the type of streaming (fls or vod).
-   `appid`: Application ID in the format (NNNNN).
-   `streamid`: StreamID in the format (24 characters MongoDB _ids).
-   `resolution`: Resolution of the stream (240p, 360p, 480p, 720p, or 1080p).
-   `os`: Operating system (e.g., Windows, Linux).
-   `country`: Country Code in 2 letters UPPERSCALE.
  Usage : `filter[location]=rome`
	`filter[type]=fls`
	`filter[appid]=20005`
	`filter[streamid]= 64c3698cb3f18a45fe049e41`
	`filter[os]=Windows`
	`filter[country]=BG`
    `filter[resolution]=1080p`

## Query
Query is based mainly on Filter parameters. To get specific data based on some criteria parameters you will receive exactly that data that you specified. If data for the whole User is needed only `filter[userid]` is required to be sent as a parameter. If data is needed for specific `type` of application required parameters are : `filter[userid]`, `filter[location]`, `filter[appid]`. Below table explain needed/required parameters to get specific data. As more detailed data needed more parameters need to be send.

| Filter[parameter]| Priority (Level) | location | type | appid | streamid | resoltuion | os | country |
| -- | -------- | -------- | ---- | ----- | -------- | ---------- | -- | ------- |
| userid | 1 |  |  |  |  |  | optional | optional |
| location | 2 | x |  |  |  |  | optional | optional |
| type | 3 | x | x |  |  |  | optional | optional |
| appid | 4 | x | x | x |  |  | optional | optional |
| streamid | 5 | x | x | x | x |  | optional | optional |
| resoltuion | 6 | x | x | x | x | x | optional | optional |

`filter[os]` and `filter[country]` parameter are optional and they work with combination of variable `graph={os or country}`, when specific `graph` variable is send you can specify to which country or os to get data.

When build query result is returned in two section levels – MAIN and SUB. Where in MAIN level receive data for the exact request, where in SUB level received data is for next Level . Example: when you request data for ‘type’ (Level 3), you will receive data for Level 3 in main section and data for Level 4 in sub section.  

Detailed Explanation: When request data for userid only it will return as a main level data for userid, but for sub level will include all data that user is generated in all location. 
If data for location is requested, main level of data contains data for that specific location, but sub levels contains data for all types in that location.
If data for specific type is requested, main level contain data for that type, sub level contain data for all application that belong to this type.
If data for appid is requested, main level contains data for that specific application, but at sub levels it contains all data for all streams that belong to this application.
If data to streamed is requested, main level contain data for that specific stream, but sub levels contain all resolutions.

## Responses

| Status | Description |
| ------ | ----------- |
| 200 | Successful operation. Returns data in json. |
| 401 | Unauthorized. No permissions. |
| 500 | Internal server error. |
## Explain the responce
```json
    "filters": {
        "appid": 20005, // Application ID
        "location": "rome", // Location
        "type": "fls", // Type
        "streamid": "654e1c4d624ff9051106f9f2", // Stream ID
        "resolution": "240p", // Resolution 
        "country": "WL", // Country Code
        "os": "Windows", // Operating system
        "userid": 201, // User Id
        "lowestLevel": 6, // Level of data calculated based on request
        "range": "period", // Range 
        "start": "1704964260", // Start timestamp
        "end": 1705994671, // End timestamp
        "step": "1h", // Step! See below explanation of the step!
        "graph": "country", // Graph
        "graph_type": "viewers", // Graph_type
        "streamname": 58581668 // Stream Name
    },
    "main": { // Main level of data
        "name": "MainLevel", // Name 
        "status": "success", // Status
        "resultType": "matrix", // Type! Matrix (multi value) of Vector (single value)
        "seriesFetched": "1", // Number of series typical = 1
        "executionTimeMsec": 27, // Execution time in ms.
        "data": [ // Data in format [timestamp , "value"]
        ]
    },
    "sub": { // Sub level of data
        "name": "SubLevel", // Name
        "status": "success", // Status
        "resultType": "matrix", // Type! Matrix (multi value) of Vector (single value)
        "seriesFetched": "2", // Number of series. Based on returning results. 
        "executionTimeMsec": 1, // Execution time in ms.
        "data": [ // Data in format [timestamp , "value"]
        ]
    }
```
-   `step` - This is the step of returning result. Data is collected every minute, and for shor periods returning result is for every minute. But when request is for a long period of time, it returns scrubbed data based on below table. Up to 8h returning result is based on every minute. If period id between 8h and 24h it returns data for every 5m, if period is between 24h and 7days data is returned for every 15m, and if period is more than 7 days returning data is with step 1h.

| Range | Step |
| ----- | ---- |
| <8h   | 1m |
| 8h - 24h | 5m |
| 24h - 7d | 15m |
| >7d | 1h |

## Example Request
### Example 1 - Most granular request - Resolution
Return data for uniq 'viewers' (graph_type), for specific 'period' (range), when period begins '1704964260' (start) and period ends '1705994671'(end), graph is based on viewers by country 'country'(graph), for user with ID '201'(filter[userid]), in location 'rome'(filter[location]), type 'fls'(filter[type]), for application '20005'(filter[appid]), stream id '654e1c4d624ff9051106f9f2'(filter[streamid]) and resolution '240p'(filter[resolution]), also exact country is selected 'WL'(filter[country])
Received data is in type of matrix (ts/value), but only for main level request, because there is no granularity below resolution. Resolution is the last possible level of vieweing statistics.

### Request
```bash
curl --insecure -H "Authorization: Bearer [token]" https://api-alpha.cdn.bg/api/stats/ -G \
	-d "range=period" \
	-d "start=1704964260" \
	-d "end=1705994671" \
	-d "graph=country" \
    -d "graph_type=viewers" \
	-d "filter[userid]=201" \
    -d "filter[location]=rome" \
    -d "filter[type]=fls" \
	-d "filter[appid]=20005" \
    -d "filter[streamid]=654e1c4d624ff9051106f9f2" \
    -d "filter[resolution]=240p" \
    -d "filter[country]=WL"

```

### Responce
```json
{
    "filters": {
        "appid": 20005,
        "location": "rome",
        "type": "fls",
        "streamid": "654e1c4d624ff9051106f9f2",
        "resolution": "240p",
        "country": "WL",
        "userid": 201,
        "lowestLevel": 6,
        "range": "period",
        "start": "1704964260",
        "end": 1705994671,
        "step": "1h",
        "graph": "country",
        "graph_type": "viewers",
        "streamname": 58581668
    },
    "main": {
        "name": "MainLevel",
        "status": "success",
        "resultType": "matrix",
        "seriesFetched": "1",
        "executionTimeMsec": 27,
        "data": [
            {
                "metric": {
                    "resolution": "240p"
                },
                "values": [
                    [
                        1704967200,
                        "2"
                    ],
...
                    [
                        1705687200,
                        "1"
                    ],
                    [
                        1705690800,
                        "1"
                    ]
                ]
            }
        ]
    },
    "sub": null
}
```

### Example 2 - Statistics based on Location 
Returning data in josn for bytes 'data'(graph_type) for general purpouse 'general'(graph), for a specific time frame 'period'(range) when period begins '1704964260' (start) and period ends '1705994671'(end) for user '201'(filter[userid]) and locatoin 'rome'(filter[location]).
Received data is in type of matrix (ts/value), and contains data in json format for main level request and for all other available sub levels (fls and vod).

### Request
```bash
curl --insecure -H "Authorization: Bearer [token]" https://api-alpha.cdn.bg/api/stats/ -G \
	-d "graph_type=data" \
    -d "graph=general"
	-d "range=period" \
	-d "start=1704964260" \
	-d "end=1705994671" \
	-d "filter[userid]=201" \
    -d "filter[location]=rome"
```
# Responce
```json
{
    "filters": {
        "location": "rome",
        "userid": 201,
        "lowestLevel": 2,
        "range": "period",
        "start": "1704964260",
        "end": 1705996374,
        "step": "1h",
        "graph": "general",
        "graph_type": "data"
    },
    "main": {
        "name": "MainLevel",
        "status": "success",
        "resultType": "matrix",
        "seriesFetched": "1",
        "executionTimeMsec": 4,
        "data": [
            {
                "metric": {
                    "location": "rome"
                },
                "values": [
                    [
                        1704967200,
                        "5636354"
                    ],
...
                        "648925"
                    ],
                    [
                        1705687200,
                        "3576872"
                    ],
                    [
                        1705690800,
                        "10072906"
                    ]
                ]
            }
        ]
    },
    "sub": {
        "name": "SubLevel",
        "status": "success",
        "resultType": "matrix",
        "seriesFetched": "2",
        "executionTimeMsec": 1,
        "data": [
            {
                "metric": {
                    "type": "fls"
                },
                "values": [
                    [
                        1704967200,
                        "5636354"
                    ],
                    [
                        1704970800,
                        "1599404"
                    ],
...
					[
                        1705687200,
                        "3576872"
                    ],
                    [
                        1705690800,
                        "10072906"
                    ]
                ]
            },
            {
                "metric": {
                    "type": "vod"
                },
                "values": [
                    [
                        1705075200,
                        "2176"
                    ],
                    [
                        1705312800,
                        "947462"
                    ]
                ]
            }
        ]
    }
}
```

### Example 3 - Total query - vector
Returning data in total generated bytes for specific period. The result is of type 'vector'(total) (single value) for the period from 'start' to 'end' for specific application '20005',for user '201', in location 'rome'.
As a main result is generated bytes for Application, where in sub levels is for all streams. This is useful, when you want to display just a single value as a generated traffic.

### Request
```bash
curl --insecure -H "Authorization: Bearer [token]" https://api-alpha.cdn.bg/api/stats/ -G \
    -d "total=vector" \
    -d "filter[appid]=20005" \
    -d "filter[location]=rome" \
    -d "filter[type]=fls" \
    -d "range=period" \
    -d "start=1704964260" \
    -d "graph=general" \
    -d "graph_type=data" 
```

### Responce
```json
{
    "filters": {
        "appid": 20005,
        "location": "rome",
        "type": "fls",
        "userid": 201,
        "lowestLevel": 4,
        "range": "period",
        "start": "1704964260",
        "end": 1704967860,
        "step": "1m",
        "graph": "general",
        "graph_type": "data"
    },
    "main": {
        "name": "MainLevel_Total",
        "status": "success",
        "resultType": "matrix",
        "seriesFetched": "1",
        "executionTimeMsec": 0,
        "data": [
            {
                "metric": {
                    "appid": "20005"
                },
                "values": [
                    [
                        1704967860,
                        "138156126"
                    ]
                ]
            }
        ]
    },
    "sub": {
        "name": "SubLevel_Total",
        "status": "success",
        "resultType": "matrix",
        "seriesFetched": "1",
        "executionTimeMsec": 0,
        "data": [
            {
                "metric": {
                    "stream": "58581668"
                },
                "values": [
                    [
                        1704967860,
                        "138156126"
                    ]
                ]
            }
        ]
    }
}
```

### Example 4 - Total - matrix :
The same request as above, but with total=matrix :
Difference is that returning bytes increse over the time range. The final value is the same in both requests, but when matrix is selected graph can be draw with data increasing over time.

### Request
```bash
curl --insecure -H "Authorization: Bearer [token]" https://api-alpha.cdn.bg/api/stats/ -G \
    -d "total=matrix" \
    -d "filter[appid]=20005" \
    -d "filter[location]=rome" \
    -d "filter[type]=fls" \
    -d "range=period" \
    -d "start=1704964260" \
    -d "graph=general" \
    -d "graph_type=data"
``` 
### Responce
```json
{
    "filters": {
        "appid": 20005,
        "location": "rome",
        "type": "fls",
        "userid": 201,
        "lowestLevel": 4,
        "range": "period",
        "start": "1704964260",
        "end": 1704967860,
        "step": "1m",
        "graph": "general",
        "graph_type": "data"
    },
    "main": {
        "name": "MainLevel_Total",
        "status": "success",
        "resultType": "matrix",
        "seriesFetched": "1",
        "executionTimeMsec": 0,
        "data": [
            {
                "metric": {
                    "appid": "20005"
                },
                "values": [
                    [
                        1704965640,
                        "2404182"
                    ],
                    [
                        1704965700,
                        "4180820"
                    ],
                    [
                        1704965760,
                        "6124778"
                    ],
                    [
                        1704965820,
                        "8827128"
                    ],
.....
                    [
                        1704967800,
                        "130375820"
                    ],
                    [
                        1704967860,
                        "138156126"
                    ]
                ]
            }
        ]
    },
    "sub": {
        "name": "SubLevel_Total",
        "status": "success",
        "resultType": "matrix",
        "seriesFetched": "1",
        "executionTimeMsec": 0,
        "data": [
            {
                "metric": {
                    "stream": "58581668"
                },
                "values": [
                    [
                        1704965640,
                        "2404182"
                    ],
                    [
                        1704965700,
                        "4180820"
                    ],
                    [
                        1704965760,
                        "6124778"
                    ],
                    [
                        1704965820,
                        "8827128"
                    ],
....
                    [
                        1704967800,
                        "130375820"
                    ],
                    [
                        1704967860,
                        "138156126"
                    ]
                ]
            }
        ]
    }
}```

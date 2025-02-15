# API OVERVIEW

API's are built with `REST` standard that allows you to easily send messages to your end users and make your work easy. You can send alerts, reminders, and notifications, or you can send verification messages containing one-time passcodes (OTP) using this feature of ours.

This documentation gives you instructions on how to integrate our api through various _*HTTP*_ API and _*JSON*_ API.

We'll be helping you configure, access and use the API for our services.

To integrate our API, any HTTP recipient in any programming language can be used.

@if(isset($products))

## PRODUCTS/SERVICES

List of product names and product codes

@foreach($products as $key => $name)
@if ($loop->index == 0)
| Product Code | Product Name |
| ---- | ---- |
@endif
| {{ $key }} | {{ $name }} |
@endforeach

@endif

#### API Endpoint

```
{domain}/api/{version}/
```

Note: Few elements in endpoint may change for service to service.

@if(isset($branding) && isset($branding->options) && isset($branding->options['docs.postman']))

## API Collection Postman link

```
https://www.getpostman.com/collections/{collection}
```

@endif

#### Available HTTP methods

Our API uses HTTP verbs to understand if you want to read (`GET`), delete (`DELETE`) or create (`POST`) an object. When your web application cannot do a `POST` or `DELETE`, we provide the ability to set the method through the query parameter `_method`.

## AUTHENTICATION

Each API request will have to contain request headers that include your Bearer token to authenticate the request.

You can generate Bearer Token from existing access tokens generate once from application panel.

Don't have an access token? You will find your access token in the `Developers -> API Keys/Access Tokens` in menu bar.

When your application can't send an Authorization header, you can use the GET parameter `access_token` to provide your access key.

> "Your API keys carry significant privileges. Please ensure to keep them 100% secure and be sure to not share your secret API keys in areas that are publicly accessible like GitHub. See [API Access Key Security](#content-api-access-key-security) for more information."

We do provide incoming request whitelisting on our platform for our REST API. You can whitelist the IP addressess while generating the access token.

## Get Bearer Token

```shell
$ curl {endpoint}auth/token/generate \
-H 'Authorization: Bearer 38e896f55670311982434e929559xxxx' \
-H 'Accept: application/json'
```

If the client exists and the secret is valid, the server will send the following response:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL3BvcnRhbC5tb2J0ZXh0aW5nLmNvL2FwaS92My9hdXRoL3Rva2VuL2dlbmVyYXRlIiwiaWF0IjoxNjcwMjQ0OTI2LCJleHAiOjE2NzAyNDg1MjYsIm5iZiI6MTY3MDI0NDkyNiwianRpIjoiZ0lrQ1F3SWFKdFhSOEV6MCIsInN1YiI6IjIiLCJwcnYiOiIyM2JkNWM4OTQ5ZjYwMGFkYjM5ZTcwMWM0MDA4NzJkYjdhNTk3NmY3In0.jJTZ61sHrT-yDx0rKyN03a7Z_uFHdMV63wieVWwRI-FllaIl_Nm8xxtJujVaXNcBWa6FRg6BNDvjfqpUSo0dPn2mQ6FF2C_gb7QSE66zTRao3dEdJL2AslLgaLejqcqgBxB8JSzIQqlDQxBnjvPJG6rbYWPhuaOFw_clkRjkd_kynyf9kOT59pGcCPJnmDUfNAQrjU77bopKuOkGIXUjid-SmYDsQaihcgZytXkR7xiPBJ5G8qMuTKF_Bt0wTkw2WFR4DaFusXEK7uysGhK7TZERCfRqtACex36kYrVU8zbLXcR7Is-cJ3S_a_QpJmKCyJrKMVmUZ-IZwuuTc_DgitXPQvpzWZiXLUEtCaETJ_wrKiezpVZr1thcwtj1XNADk0--mSSrQzLEMZwxgEaDrHh7KpbtuvtfjTgAoFgweCcnHcRnIMDDGz__y_XlAO33JetZVj1WBFGn34VvYETuw2s_T6Z4ZlSECwNR3HWn_Js89DG8YntDtWTs89E12RlODPSS6Ywa_To459bW97WozdWlhfm3VYr-877oBj9L1CBlKBTAhEBjB_I3DOXTrQaBc_Z8UZwi3nYmQkdXUk0x0alxxxxx.......",
    "token_type": "bearer",
    "expires_in": 3600
}

```

The `access_token` field contains the access token used to authenticate to the actual API, the `expires_in` field contains the number of seconds until the token becomes invalid as an integer.

Do not rely on this value being exactly an hour like in the example above: We may tweak the expiration at any time depending on security observations: It might very well be that this comes down to 10 minutes at some point.

This means that your application will need to keep track of the expiration and perform the above POST call again to obtain a new token in time.

After you’ve got your token, you can perform actual API requests by including it in the Authorization header of your requests, with the authorization type set to Bearer:

```shell
$ curl {endpoint}auth/me \
-H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL3BvcnRhbC5tb2J0ZXh0aW5nLmNvL2FwaS92My9hdXRoL3Rva2VuL2dlbmVyYXRlIiwiaWF0IjoxNjcwMjQ0OTI2LCJleHAiOjE2NzAyNDg1MjYsIm5iZiI6MTY3MDI0NDkyNiwianRpIjoiZ0lrQ1F3SWFKdFhSOEV6MCIsInN1YiI6IjIiLCJwcnYiOiIyM2JkNWM4OTQ5ZjYwMGFkYjM5ZTcwMWM0MDA4NzJkYjdhNTk3NmY3In0.jJTZ61sHrT-yDx0rKyN03a7Z_uFHdMV63wieVWwRI-FllaIl_Nm8xxtJujVaXNcBWa6FRg6BNDvjfqpUSo0dPn2mQ6FF2C_gb7QSE66zTRao3dEdJL2AslLgaLejqcqgBxB8JSzIQqlDQxBnjvPJG6rbYWPhuaOFw_clkRjkd_kynyf9kOT59pGcCPJnmDUfNAQrjU77bopKuOkGIXUjid-SmYDsQaihcgZytXkR7xiPBJ5G8qMuTKF_Bt0wTkw2WFR4DaFusXEK7uysGhK7TZERCfRqtACex36kYrVU8zbLXcR7Is-cJ3S_a_QpJmKCyJrKMVmUZ-IZwuuTc_DgitXPQvpzWZiXLUEtCaETJ_wrKiezpVZr1thcwtj1XNADk0--mSSrQzLEMZwxgEaDrHh7KpbtuvtfjTgAoFgweCcnHcRnIMDDGz__y_XlAO33JetZVj1WBFGn34VvYETuw2s_T6Z4ZlSECwNR3HWn_Js89DG8YntDtWTs89E12RlODPSS6Ywa_To459bW97WozdWlhfm3VYr-877oBj9L1CBlKBTAhEBjB_I3DOXTrQaBc_Z8UZwi3nYmQkdXUk0x0alxxxxx.......' \
-H 'Accept: application/json'
```

## IP ADDRESSES

Our API platform is offered from a globally distributed infrastructure, so you won't be able to whitelist the IP addresses of our platform. Keep in mind that request for delivery reports and inbound messages originate from various IP addresses.

## API ACCESS KEY SECURITY

Given your API access key is your authentication token for using APIs, they need to be appropriately secured. One of the easiest ways to think of this is to treat your API access keys just like you would your passwords, including storing them securely and never sharing them with anyone.

One of the most common mistakes that is made with API keys is to inadvertently check them into public repositories on platforms such as GitHub. From here, fraudsters can find and steal your API access key and then use it to send Spam messages and also drain your account balance. There are numerous techniques to avoid this, however storing your API access key in an environment variable, passing them as command line arguments or using a secrets manager can all help to prevent this from occurring. The main takeaway is don't hard-code your API access key and don't check it into a public code repository.

In a similar manner, sharing code snippets on platforms such as PasteBin, GitHub Gists or StackOverflow can inadvertently leak your API access key so ensure that you and your developers are aware of this risk.

## RATE LIMITS

To keep you in compliance, We maintains the appropriate rate limits:

| API | VALUE                                                                                                                                                                   |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| All | `GET` Requests are rate limited to 1000 requests per minute. Once this limit has been crossed, your requests will be rejected with an HTTP 429 'Too Many Requests' code |

You can contact our support team to increase the limit. Our team will increase the limit case by case.

## ERROR CODES

We uses standard HTTP status codes to indicate success or failure of an API request. Codes in the 2xx range indicate that a request was successfuly processed and codes in the 4xx range indicate that there was an error that resulted from the information sent (e.g. authentication, no balance or a missing or wrong parameter).

In case of an error, the body of the response includes a JSON formatted response that tells you exactly what is wrong.

#### ATTRIBUTES

| Name    | Value                                                                                                                                     |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| status  | This represents the error type. OK or 200 is success and rest everthing is failed.                                                        |
| code    | This represents the http code. This value is optional. Not be avaliable in all responses.                                                 |
| message | A human-readable description of the error. You can provide your users with this information to indicate what they can do about the error. |

## HTTP STATUS CODES

| code | Descriptions                                                                     |
| ---- | -------------------------------------------------------------------------------- |
| 200  | OK - Everything went as planned.                                                 |
| 202  | Accepted - Request accepted.                                                     |
| 400  | Bad Request - Something in your header or request body was malformed.            |
| 401  | Unauthorised - Necessary credentials were either missing or invalid.             |
| 403  | Your credentials are valid, but you don't have access to the requested resource. |
| 404  | The resources cannot be found                                                    |
| 409  | Conflict - You might be trying to update the same resource concurrently.         |
| 422  | Validation error                                                                 |
| 429  | Too Many Requests - You are calling our APIs more frequently than we allow.      |
| 5xx  | Something went wrong on our end. Please try again                                |

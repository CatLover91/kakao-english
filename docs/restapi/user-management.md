# User Management
-----------------
User management is one of the key functions provided by Cacao Platform Services. User management connects your account to the cacao platform in an easy and fast way. With this feature, you can create a secure, more robust, engaging app.
The detailed functions of user management are as follows.
* [Login](/docs/restapi/user-management#로그인)

    Supports quick and easy login via a cacao account.
* [Logout](/docs/restapi/user-management#로그아웃)
    
    Disconnects the session of the logged in user.
* [Connect app Connects](/docs/restapi/user-management#앱-연결)
    
    signed-in users and apps to the cacao platform. [Similar to when a user subscribes / subscribes to an app.](/docs/restapi/user-management#앱-연결)
* [Unlink](/docs/restapi/user-management#앱-연결-해제)

    apps Permanently unlinks apps from users connected to the Cacao platform. [This is similar to when a user requests to leave an app.](/docs/restapi/user-management#앱-연결-해제)
* [User Information Requests](/docs/restapi/user-management#사용자-정보-요청)

    You can get information about users. [You must be logged in and connected to the app to use this feature.](/docs/restapi/user-management#사용자-정보-요청)
* [Save](/docs/restapi/user-management#사용자-정보-저장)

    user information You can save specific information about the user. [You must be logged in and connected to the app to use this feature.](/docs/restapi/user-management#사용자-정보-저장)
* [List of user IDs](/docs/restapi/user-management#사용자-리스트-요청)
    
    You can get a list of app users. [This function must be called with the Admin key.](/docs/restapi/user-management#사용자-리스트-요청)
* [Validating User Tokens and Getting](/docs/restapi/user-management#사용자-토큰-유효성-검사-및-정보-얻기)
    
    Information You can validate or obtain additional information about the user's access token obtained through login.
* [Dynamic Consent A](/docs/restapi/user-management#동적동의)

    dynamic consent process is required if the user requires additional consent when using the API.
> [Here](/buttons#Login_Buttons) are the buttons you need to use the Login API .

## login
You can log in quickly and easily using your cacao account. 

This login feature supports OAuth 2.0. The following is the most common OAuth authentication process provided by the Cacao Platform Service.
1. The user clicks the login button to the cacao account.
2. Recognize users through the Credentials of a cacao account linked to the Kakao Talk app.
3. If the credentials are correct, obtain the consent / permission of the access resource from the Resource Owner.
4. If you successfully completed the above 3, Authorization Code will be issued. The authorization code is delivered to the third app based on the Redirection URI.
5. The third app will request and get a user token (Access Token, Refresh Token) based on the received authentication code.
> The user token is used as an important key in using the login-based functions provided by the Cacao Platform Service. More information about OAuth 2.0 can be found here .
### Get a code
When you click on the login button, you must first obtain the code to get your token via user consent.
> You can download various images of the buttons for logging into your cacao account [here](http://oauth.net/2/).

**[Request]**
```bash
GET /oauth/authorize?client_id={app_key}&redirect_uri={redirect_uri}&response_type=code HTTP/1.1
Host: kauth.kakao.com
```

key | Explanation |	necessary
-----|-----|----
client_id |	The REST API key that was issued when the app was created. | O
redirect_uri | URI to redirect code. _Settings > General > Web > URI_ that has _Settings > General > Web > Redirect Path_ added to each _domain_ set in the _site domain_. | O
response_type |	code Fixed to string value. | O
state |	[Cross-site Request Forgery](http://en.wikipedia.org/wiki/Cross-site_request_forgery) Any unique string to protect against attacks. [The corresponding value is passed when redirecting](http://en.wikipedia.org/wiki/Cross-site_request_forgery). | X
encode_state | Whether state is encoded (boolean). If it is true (y or 1), it is delivered as decoded state when state is transferred. If false (n or 0), the state is transferred without any separate decode. default false. | X

> An example of `redirect_uri` is shown below. For example, if `http://your.website.domain.com` you set the site domain to `/ kakao_oauth` as the Redirect Path, then `redirect_uri` will be `http://your.website.domain.com/kakao_oauthformatted`. The default for Redirect Path is `/ oauth`.

If you have not signed in to your cacao account on that device, you will be redirected to the login window. Once you log in, the cacao account is valid for 6 hours, so if you have logged in within 6 hours, you will be prompted to accept / approve the access resource.

**[Response]**
Clicking Allow will redirect the code to the redirect_uri with the query string. redirect_uri Processes the request to get the code.
```bash
HTTP/1.1 302 Found
Content-Length: 0
Location: {redirect_uri}?code={authorize_code}
```

### Get a user token
After getting the code, you can use it to get a user token (Access Token, Refresh Token) that can actually call the API.
**[Request]**
```bash
POST /oauth/token HTTP/1.1
Host: kauth.kakao.com
```
Request the values ​​of the following parameters via POST.

key | Explanation | necessary
-----|-----|----
grant\_type | Fixed with authorization_code | O
client_id | The REST API key that was issued when the app was created. | O
redirect_uri | The URI whose code was redirected. _Settings > General > Web > URI_ that has _Settings > General > Web > Redirect Path_ added to each domain set in the site domain. | O
code | Authorized code from receiving the code above. | O
client\_secret | Client\_secret code generated by _Setup > General > Client Secret_ must be set to required if ACTIVE state | X

For example, request with POST as follows:
```bash
curl -v -X POST https://kauth.kakao.com/oauth/token \
 -d 'grant_type=authorization_code' \
 -d 'client_id={app_key}' \
 -d 'redirect_uri={redirect_uri}' \
 -d 'code={authorize_code}'
 ```

**[Response]**
The response body is a JSON object that contains the values ​​below.
```bash
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
    "access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "token_type":"bearer",
    "refresh_token":"yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy",
    "expires_in":43199,
    "scope":"Basic_Profile"
}
```

It receives the access\_token used to call the API as a result of the request, the expiration time (in seconds) of the token, and a refresh\_token to update the token.

### Update user token
When the token expires, it updates the token using the refresh_token received in response to the token request or received in response to the token update request.
> The access_token is valid for 12 hours to 24 hours (subject to change) upon receipt. The refresh token is valid for one month, and when a user token renewal request is made at the point where the refresh token expires within one week, the updated access token and the updated refresh token are returned together.
The following request is required to update the token.

**[Request]**
```bash
POST /oauth/token HTTP/1.1
Host: kauth.kakao.com
```

Request the values ​​of the following parameters via POST.

key | Explanation | necessary
-----|-----|----
grant\_type | Fixed with refresh\_token | O
client\_id | The REST API key that was issued when the app was created. | O
refresh\_token | It is used to update the Access Token with the refresh\_token received in response to the token issuance. | O
client\_secret | Client\_secret code generated by Setup > General > Client Secret
Must be set to required if ACTIVE state | X

For example, request with POST as follows:
```bash
curl -v -X POST https://kauth.kakao.com/oauth/token \
 -d 'grant_type=refresh_token' \
 -d 'client_id={app_key}' \
 -d 'refresh_token={refresh_token}'
```

**[Response]**
```bash
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
    "access_token":"wwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwwww",
    "token_type":"bearer",
    "refresh_token":"zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz",  //optional
    "expires_in":43199,
}
```

The new access\_token that is updated as a result of the request, the expiration time of the token, and the updated refresh\_token if the refresh token itself has been updated. Because refresh\_token may or may not be included in the response, it should only be updated if it is included.

## Log out

This function disconnects the session with the cacao account.
> The login function supports multiple devices. If a user logs in and logs out with the same cacao account from multiple devices, the session will be disconnected only from the device that performed the logout.
**[Request]**
```bash
POST /v1/user/logout HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
```

Put the user token in the header and request it at POST. For example,
```bash
curl -v -X POST https://kapi.kakao.com/v1/user/logout \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```
**[Response]**
The response body is a JSON object that contains the values ​​below.

key | Explanation | type
id | Logged-out user ID | signed int64

For example,

```bash
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
    "id":123456789
}
```

## App Link

Connecting an app is similar to connecting a signed-in user and an app to a cacao platform, typically when a user makes an app subscription / enrollment request. In order to use the Cacao Platform Service properly, the application must be connected before login and can be performed only once. If the app is properly linked, it will be given a unique ID for that user.
After login, if user information is called but failed to join, you must open the registration window. If you want to save certain additional information about an individual user at the time of subscribing, you can save the user information along with the subscription. This, after registering the user information stored can be updated through.

**[Request]**
```bash
POST /v1/user/signup HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
Content-Type: application/x-www-form-urlencoded;charset=utf-8
```

Put the user token in the header and request it at POST. If you want to store your user information at the same time as connecting to your app, you can request the values ​​of the parameters below with your user token via POST. User information can be updated after sign-up by saving user information.

key | Explanation | necessary
-----|-----|----
properties | The information of the user you are subscribed to. A key-value of type JSON. | X

For example, if you want to get your age and gender at the time of your subscription,
> age and gender must be added as custom user information columns in the user management settings.
```bash
curl -v -X POST https://kapi.kakao.com/v1/user/signup \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
  --data-urlencode 'properties={"age":"23", "gender":"female"}'
```

**[Response]**
If the app connection request is successful, include the value below as a JSON object in the response body.

key | Explanation | type
-----|-----|----
id | App associated user ID | signed int64

For example,
```bash
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
    "id":123456789
}
```

## Unlink app

Disconnecting an app is similar to when a user requests to leave an app by permanently disconnecting the app from users connected to the cacao platform. Users disconnected from the app are permanently unrecoverable and can no longer use the cacao platform service. However, you can access the cacao platform service with new data via app connection again.
> When you unlink an app, all data for that user managed by the cacao platform will be erased. However, the data stored in Third Apps can not be held by the Cacao Platform Service. This has an obligation to remove from the third app (see our [policies and terms](https://developers.kakao.com/policies/support) for more details). This is the difference between unplugging an app and unlinking an app.

**[Request]**
```bash
POST /v1/user/unlink HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
```

```bash
POST /v1/user/unlink HTTP/1.1
Host: kapi.kakao.com
Authorization: KakaoAK {admin_key}
```

> The Admin Key is a kind of Master Key that should never be leaked, so you must call the API from the Third Server. The Admin key can be found in My Applications on the Cacao website. If you use the admin key within the source code of your iOS or Android application, the application may be vulnerable.
Requests a user token / admin key in the header and POST.
```bash
curl -v -X POST https://kapi.kakao.com/v1/user/unlink \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

```bash
curl -v -X POST https://kapi.kakao.com/v1/user/unlink \
  -H "Authorization: KakaoAK kkkkkkkkkkkkkkkkkkkkkkkkkkkkkk" \
  -d 'target_id_type=user_id&target_id=123456789'
```

**[Response]**
If the app disconnect request is successful, include the value below as a JSON object in the response body.

key | Explanation | type
-----|-----|----
id | App disconnected user ID | signed int64

For example,
```bash
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
    "id":123456789
}
```

## Request user information

User information request is a function to get ID (User ID), Cacao account email (email) and individual details. To use this feature, you need a user token that can be obtained after a successful login. You also need to have an app connection.
> In the case of user ID (ID), this is the unique ID of the user issued by the app connection process. The user can be identified in the app by their ID, and the same user will remain at the same value unless the app is disconnected.
Please note the following when collecting and using your cacao account email:
> * The user's cacao account email provided to the service will also include unauthorized email. It also includes e-mail and e-mail authentication when requesting user information, so you should always check whether the e-mail is authenticated before using it. If you need your email information when you sign up for the service, we recommend that you notify the user that the service has not authenticated your cacao account email and that you receive email directly from the user (eg, you are using an unauthenticated cacao account. Address is required.)
> * If a user declines to provide an email from a cacao account, the user's email will not be provided. If you need your email information when subscribing to the service, we recommend that you receive email directly from the user. 
> * The email in your cacao account may change due to your request. We recommend that you check for changes to your email at the time you use the service.
When an administrator wishes to obtain information for a specific user, he or she can also request it through the Admin key and user ID.
> To use this feature, you need Admin Key, which allows you to manage all users of the app. To prevent the administration key from being hijacked, please call the API from your own app server without calling the API with the admin key directly in the app.

**[Request]**
```bash
GET/POST /v1/user/me HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
Content-type: application/x-www-form-urlencoded;
```

```bash
charset=utf-8
GET/POST /v1/user/me HTTP/1.1
Host: kapi.kakao.com
Authorization: KakaoAK {admin_key}
Content-type: application/x-www-form-urlencoded;charset=utf-8
```
> The Admin Key is a kind of Master Key that should never be leaked, so you must call the API from the Third Server. The Admin key can be found in My Applications on the Cacao website. If you use the admin key within the source code of your iOS or Android application, the application may be vulnerable.
When using the Admin key and user ID (ID) as authentication information, it is requested together with the following parameters.

key | Explanation | necessary
-----|-----|----
target_id_type | The type of user ID. Fixed value for user_id | O
target_id | User ID | O

GET / POST request with user token or admin key and user ID (ID) in header. You can optionally add values ​​for the following parameters along with user authentication information.

key | Explanation | necessary
-----|-----|----
propertyKeys | Key list of user information. JSON Array type. | X
secure_resource | Whether to return the image url as https. true / false | X

For example, if you want to get full user information, ask:
```bash
curl -v -X GET https://kapi.kakao.com/v1/user/me \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

For example, if you want to get only the name and age of user information, ask:

```bash
curl -v -X POST https://kapi.kakao.com/v1/user/me \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
  -d 'propertyKeys=["name","age"]'
```

If you want to get only the name and age of the user with the user ID (ID) 12345 on the server of the app,

```bash
curl -v -X POST https://kapi.kakao.com/v1/user/me \
  -H "Authorization: KakaoAK kkkkkkkkkkkkkkkkkkkkkkkkkkkkkk" \
  -d 'target_id_type=user_id&target_id=12345' \
  -d 'propertyKeys=["name","age"]'
```

**[Response]**
If the user information request is successful, include the following value as a JSON object in the response body:

key | Explanation | type
-----|-----|----
id | User's unique ID | signed int64
kaccount_email | Emails from user cacao accounts | String
kaccount_email_verified | Whether it is an 
authenticated cacao account email. Unauthorized emails are subject to change. | Boolean
properties | Your information. A key-value of type Json. If you specify a user information key, only information about that key is included. | String

The default user properties are nickname, profile\_image, and thumbnail\_image.
* nickname: nickname information for KakaoTalk or Cacao Story
* profile_image: Profile image URL of 640px * 640px Kakao Talk or Cacao Story URL (May be 480px * 480px ~ 1024px * 1024px for subscribers before 2017/08/22)
* thumbnail_image: 110px * 110px thumbnail profile image URL for Kakao Talk or Cacao Story (may have size 160px * 213px for subscribers before 2017/08/22)

```bash
HTTP/1.1 200 OK
{
    "id":123456789,
    "kaccount_email": "xxxxxxx@xxxxx.com",
    "kaccount_email_verified": true,
    "properties":
    {
        "nickname":"홍길동",
        "thumbnail_image":"http://xxx.kakao.co.kr/.../aaa.jpg",
        "profile_image":"http://xxx.kakao.co.kr/.../bbb.jpg",
        "custom_field1":"23",
        "custom_field2":"여"
        ...
    }
}
```

```bash
HTTP/1.1 200 OK
{
    "id":123456789,
    "kaccount_email": "xxxxxxx@xxxxx.com",
    "kaccount_email_verified": true,
    "properties":
    {
        "nickname":"홍길동",
        "custom_field1":"23"
        ...
    }
}
```

If you want to collect additional user's cacao account emails and you encounter errors, please refer to the guide below.

    To collect e-mail accounts of users who have signed up for e-mail items without consent, the user's dynamic consent is required. You can get a user's dynamic consent when email is needed.
    If you require dynamic consent from the user, you will need to request your cacao account email information as follows, and you will need to process the error and get your consent, as instructed by [dynamic consent](/docs/restapi/user-management#동적동의).

```bash
curl -v -X POST https://kapi.kakao.com/v1/user/me \
-H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
-d 'property_keys={"kaccount_email"}'
```

```bash
HTTP/1.1 403 Forbidden
{
  "msg": "insufficient scopes.",
  "code": -402,
  "api_type": "USER_ME",
  "required_scopes": [
    "kaccount_email"
  ],
  "allowed_scopes": [
    "profile"
  ]
}
```
Below is an error response that you might get if you have not set up to use email.

    If you see the error below, you should set it to use email in _Settings > User Management > Consent Item Management_.

    ```bash
    HTTP/1.1 403 Forbidden
    {
        "msg": "[맛있는 앱] App disabled [account_email] scopes for [USER_ME] API on developers.kakao.com. Enable it first.",
        "code": -3
    }
    ```

## Save user information

Saving user information is a function to save specific additional information for individual users. In addition to the basic additional information provided by the Cacao Platform Service, you can also save customized data by declaring customized information for each app via the developer's website. To use this feature, you need a user token that can be obtained after a successful login. You also need to have an app connection.

    User information that can not be modified by user information saving function exists. For example, a user's ID can not be modified.
    If only one of profile_image and thumbnail_image is requested, it is saved as 640px * 640px, 110px * 110px respectively. If both images are requested to be saved together, profile_image is saved as the original size.
**[Request]**
```bash
POST /v1/user/update_profile HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
Content-type: application/x-www-form-urlencoded;charset=utf-8
```
POST requests the values ​​of the parameters below with the user token.

key | Explanation | necessary
-----|-----|----
properties | The information of the user you are subscribed to. Key: value in JSON format. | O

For example, if you want to update nickname and custom information age of basic additional information in user information, request as follows.
    age and gender must be added as custom user information columns in the user management settings.

```bash
curl -v -X POST https://kapi.kakao.com/v1/user/update_profile \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
  --data-urlencode 'properties={"nickname":"홍길동","age":"22"}'
```
**[Response]**
If the user information save request succeeds, the response body will contain the following values ​​as JSON objects:

key | Explanation | type
-----|-----|----
id | User ID | signed int64

For example,
```bash
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
    "id":123456789
}
```

## User List Request

Get a list of user IDs for this app in paging format. Paging is a way to separate the entire list into multiple API requests. First, if you send a request without any parameter value, the list of user IDs 1 ~ 100 is displayed in ascending order of user ID. It also contains the previous page URL and the next page URL, which is the URL of the page that should be before the current page (the page with the smallest user ID less than the smallest user ID on the current page, The URL is the URL of the page that should be following the current page (the 100 user pages with the largest user ID and the largest user ID on the current page). If there is no user to enter the previous or next page, a null value is returned in the URL.

So if you want to get all of the users, you can proceed to the following steps.
1. Sends a request with no parameter value.
2. It processes the user IDs from the received result and requests the next page URL.
3. If you repeat 2 and the URL is returned as null, there are no more users to stop and will stop.

If you want to make a special request, you can use 'from_id', 'limit', 'order' below to create a list of 'limit users'
    To use this feature, you need Admin Key, which allows you to manage all users of the app. To prevent the administration key from being hijacked, please call the API from your own app server without calling the API with the admin key directly in the app.

**[Request]**
```bash
GET /v1/user/ids HTTP/1.1
Host: kapi.kakao.com
Authorization: KakaoAK {admin_key}
Content-type: application/x-www-form-urlencoded;charset=utf-8
```

You can request the following parameter values ​​with the Admin key.

key | Explanation | necessary
-----|-----|----
limit | The maximum number of users to enter the page. (Minimum 1, maximum 100, default 100) Integer type. | X
from_id | The user ID value from which to start paging. Generally, we use the results from Response. If there is no value, it reads from the user with the smallest ID. Long form | X
order | The direction to search for paging. one of asc / desc (default asc) | X

For example, if you want to get the full information of the first 100 users, ask:
```bash
curl -v -X GET https://kapi.kakao.com/v1/user/ids \
  -H "Authorization: KakaoAK kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkk"
```

If you want to get only 3 users with a user ID greater than 12345,
```bash
curl -v -X POST https://kapi.kakao.com/v1/user/ids \
  -H "Authorization: KakaoAK kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkk" \
  -d 'limit=3&order=asc' \
  -d 'from_id=12345'
```
**[Response]**
User Information List If the request is successful, the response body will contain the following value as a JSON object:

key | Explanation | type
-----|-----|----
elements | User ID list. | List
total_count | The total number of users in your app. | Integer
before_url | Previous page URL. Null if there is no previous page. | String
after_url | Next page URL. Null if there is no next page. | String

```bash
HTTP/1.1 200 OK
{
  "elements": [
  1376016924426111111, 1376016924426222222, 1376016924426333333 ]
  , "total_count": 9
  , "before_url": "http://kapi.kakao.com/v1/user/ids?limit=3&order=desc&from_id=1376016924426111111&app_key=12345674ae6e12379d5921f4417b399e7"
  , "after_url": "http://kapi.kakao.com/v1/user/ids?limit=3&order=asc&from_id=1376016924426333333&app_key=12345674ae6e12379d5921f4417b399e7"
}
```

## Validate user tokens and get information
Validate the Access Token obtained by [receiving the user token](/docs/restapi/user-management#토큰-받기) or obtain additional information of the token.

To [update a user token](/docs/restapi/user-management#토큰-갱신), it is necessary to call the current token within a certain period, such as whether the token has expired and how long it is valid.
    User tokens can be obtained through the Native SDK, REST API, or Javascript SDK. Regardless of which user tokens are obtained on which platform, you can use the functionality provided by the user token obtained after login.
**[Request]**
```bash
GET /v1/user/access_token_info HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
Content-type: application/x-www-form-urlencoded;charset=utf-8
```
Put the user token in the request header and request it with the GET Method. No additional parameters are required.

Here is an example of a request for that feature:
```bash
curl -v -X GET https://kapi.kakao.com/v1/user/access_token_info \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```
**[Response]**
If the request succeeds, the response body will contain the following value as a JSON object:

key | Explanation | type
-----|-----|----
id | User ID	signed | int64
expiresInMillis | The remaining validity period (Milli-seconds) of the given token. 0 or positive number | signed int64
appId | Id of the app that issued the given token | signed int64

```bash
HTTP/1.1 200 OK
{
    "id":123456789,
    "expiresInMillis":239036,
    "appId":1234
}
```
    The above response is an example of when a given token is correct. If a given user token is malformed, code-2 may be returned in HTTP Status 400, and code -401 may be returned in HTTP Status 401 if the token has already expired. 

    If the response error code is -401 (usually token expiration), you should [update the user token](/docs/restapi/user-management#토큰-갱신) in the place you normally called. If the response error code is -1, it is recommended that you do not forcibly expire (discard) or [logout](/docs/restapi/user-management#로그아웃) processing the token (temporary error message processing is recommended) , since this is a temporary internal failure state of the cacao platform service . The other error is that the status of the app / user / token is no longer valid, so re-updating the token does not mean much. In this case, [logout](/docs/restapi/user-management#로그아웃) processing is recommended. 

    Refer to the [error code](/docs/restapi/quick-reference#응답-코드-에러-코드) for more information about [errors](/docs/restapi/quick-reference#응답-코드-에러-코드).

## Dynamic consent

Dynamic consent process is required when using API requires user's additional consent. 

For example, a user who does not agree to _send a Kakao Talk message_ will ask you to send it to me.
```bash
curl -v -X POST 'https://kapi.kakao.com/v2/api/talk/memo/default/send' \
-H 'Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx' \
-d 'template_object={...}'
```
**[Response]**
The request fails and the response body contains the following value as a JSON object:

key | Explanation
-----|-----
required_scopes | The current consent required to use the API
allowed_scopes | Consent items the user has agreed to
```bash
HTTP/1.1 403 Forbidden
{
  "msg": "insufficient scopes.",
  "code": -402,
  "api_type": "TALK_MEMO_DEFAULT_SEND",
  "required_scopes": [
    "talk_message"
  ],
  "allowed_scopes": [
    "profile",
    "account_email"
  ]
}
```
    You must request dynamic consent, including the required_scopes of the API response .
**[Request]**
```bash
GET /oauth/authorize?client_id={app_key}&redirect_uri={redirect_uri}&response_type=code&scope={required_scopes.join(',')} HTTP/1.1
Host: kauth.kakao.com
```

key | Explanation | necessary
-----|-----|----
scope | API requests, which are received in response to the required_scopes 
",". | O

    The dynamic acceptance process is the same as the [code receipt](/docs/restapi/user-management#코드-받기) process. Please refer to [Receive Code](/docs/restapi/user-management#코드-받기).

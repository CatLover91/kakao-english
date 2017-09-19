# Getting Started
-----------------
Cacao Platform Services provides APIs related to cacao services such as Kakao Talk, Cacao Story, and Cacao Platform Technology.

This document describes the REST API, which can utilize various functions of the cacao platform service as an HTTP request, and includes a detailed description of each function provided by the cacao platform service.

We recommend that you refer to the descriptions of the advanced features that will be provided in the future, or to the basics below before attempting to perform these functions.

* [Configuring the development environment](/docs/restapi/getting-started#개발환경-구성)
* [Create an app](/docs/restapi/getting-started#앱-생성)
* [User management settings](/docs/restapi/getting-started#사용자-관리-설정)

For a quick reference on the REST API, see below.
* [Easy reference](/docs/restapi/quick-reference)

The detailed features provided in this document are as follows.
* [User Management](/docs/restapi/user-management)
    
    Provides quick login through your Kakao account. [It also includes the ability to easily manage individual information of users.](/docs/restapi/user-management) [Among the functions provided by the Kakao platform service, it must be preceded in order to use functions that require login.](/docs/restapi/user-management)
* [Cacao Story The](/docs/restapi/kakaostory-api)
    
    APIs provided by Cacao Stories are available directly in the app.
* [KakaoTalk](/docs/restapi/kakaotalk-api)
    
    You can use the API provided by KakaoTalk directly in the app.
* [카카오페이](/docs/restapi/kakaopay-api)
    
    Describes the REST API for using the Cacao PayCafe Pay .
* [Push Notifications](/docs/restapi/push-notification)
    
    Describes the basic process and REST API for using push notifications. [You can send push notifications to iOS, Android applications via the REST API.](/docs/restapi/push-notification)
The following documents are based on the following environment, and configurations may differ depending on developer's system environment.
* Apple OS X system 10.9.2
* Curl (7.30.0) tool
* Chrome (33.0.1750.146), Firefox (28.0) browser

## Configuring the development environment
The REST API is available wherever you can send HTTP requests. Here is an example of an environment where you can take advantage of the REST API.
* Using Javascript in Mobile / PC Web Environment
* Utilizing web servers in various environments (Java, Ruby, Python, etc.)
* iOS, Android, etc.
> For iOS, Android, and Javascript, we provide the [Kakao SDK](/docs/sdk) , which makes development easier and more convenient .
The developer website provides a variety of tools for developing and debugging the REST API, and this article provides an example screen using curl.
> If you do not have curl installed, you can install it by [downloading curl](http://curl.haxx.se/download.html).

## Create an app
[Create](/apps/new) an _app_ using [My Apps> Create](https://developers.kakao.com/apps/new) an _app_.
* Specify the app name and app icon. 
![getting started](/assets/images/dashboard/dev_017.png)
* When you click _Create an app, the creation_ of the app is complete and the app key is assigned. When using the REST API, use the _REST API key_.
![getting started](/assets/images/dashboard/dev_018.png)
* _Set_ click to go after the setting. Select _Add Platform > Web_ and enter the _site domain_.
* To use additional [user management](/docs/restapi/user-management) features , you must enter a _Redirect Path_ to redirect the code.
![getting started](/assets/images/dashboard/dev_019.png)

## User management settings
To use the [user management](/docs/restapi/user-management) function, additional setting is required in _Settings > User Management_.
![getting started](/assets/images/dashboard/dev_021.png)
* First, _use_ the _ON_ must
* Second, you can select _App Linkage Settings > Auto Subscribe_.
> The Cacao Platform service provides automatic app connectivity for your convenience. If this setting is enabled, you will not be required to perform a separate app connection process, since the app connection is automatically called when you first sign in.
> If the moment the user first obtains the token by logging in to the service is not the same as the actual service subscription time (for example, if there is a separate subscription procedure within the service, or if there is a separate procedure for accepting the Terms of Service) You must. If you turn off the auto-join option, you must explicitly call the [subscription API](/docs/restapi/user-management#가입API) after your first sign-in to complete your subscription.
> Even if the actual service subscription time is different, turning on the auto subscription option may confuse users. For example, if you leave during the service sign-up process and the cacao sign-in is complete, the app will appear in Manage linked apps. This process is often encountered during the process of logging in to verify that you are unsubscribed after leaving the service.
> You can find information about the apps linked to your cacao account in the linked app management page of your cacao account or a cacao account in the cacao story.
* Third, you can choose to _link the apps > Kakao account_.
> The default user information determines whether to use KakaoTalk service information, Kakao story service information, or which service information should be prioritized for users using both services. User properties added by default include nickname, profile_image, and thumbnail_image.
> You will be synchronized with Kakao Talk or Cacao Story Service for the first time in the app connection process. Even if the user changes the information in the Kakao Talk or Cacao Story, the changed data will not be reflected.
> If you have disabled Synchronization with KakaoTalk or Cacao Story, the additional information is filled with empty values. This basic additional information can be replaced at any time with other data via the [user information storage](/docs/restapi/user-management#사용자-정보-저장) function.
* Fourth, you can add user information you want to add at the time of subscription through _user list and property management_ menu.
> Limit custom user information columns to 5 or fewer, and each custom user information value is recommended to be 160 characters or less. In fact, your properties can be added at [app connection](/docs/restapi/user-management#앱-연결) time. It can also be added through [user information saving](/docs/restapi/user-management#사용자-정보-저장) function.
* Fifth, you must select the consent items required by the API to be used in the service, and set the purpose of calling the API, that is, the purpose of collecting the information.
> If the purpose of the collection purpose you enter is different from the purpose of using the personal information in the actual service, it may be a reason for denying the API service. To use your cacao account email, you must enable email here.
* Sixth, if the place where the service is operated is out of the country, _user management_ > personal information It is necessary to input the information of the country where the personal information is stored in the _overseas transfer_.
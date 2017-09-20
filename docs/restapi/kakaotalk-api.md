# KakaoTalk
KakaoTalk API refers to API provided by KakaoTalk service. It can be used in apps that sign in with a cacao account and is only available for KakaoTalk users connected to a cacao account.

The following KakaoTalk APIs are available.
* Request a profile
* Send to me

## Request a profile

Kakao Talk profile request is a function that can get Kakao Talk profile information only for Kakao Talk users connected to a Kakao account among users. To use this feature, you need a user token that can be obtained after a successful login.

**[Request]**
```
GET /v1/api/talk/profile HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
```
Put a user token in the header and request it with GET. You can optionally add values ​​for the following parameters along with your user token:

key | Explanation | necessary | type
----|-------------|-----------|-------
secure_resource | Whether to return the image URL as https. true / false | X | Boolean

For example,
```
curl -v -X GET  https://kapi.kakao.com/v1/api/talk/profile \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```
**[Response]**

The response body is a JSON object that contains the following values:

key	| Explanation | type
----|-------------|-----
nickName | Cacao Tok Alias. | String
profileImageURL | KakaoTalk profile image URL. 640px * 640px Dimensions | String
thumbnailURL | Kakao Talk Thumbnail Profile Image URL. 110px * 110px Size | String
countryISO |Country code of KakaoTalk | String
For example,
```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
  "nickName":"홍길동",
  "profileImageURL":"http://xxx.kakao.co.kr/.../aaa.jpg",
  "thumbnailURL":"http://xxx.kakao.co.kr/.../bbb.jpg",
  "countryISO":"KR"
}
```
## Send to me
Send to KakaoTalk is a feature that allows you to send messages to chat rooms in KakaoTalk only if you are a KakaoTalk user connected to your Kakao account. To use this feature, you need a user token that can be obtained after a successful login. Send to me supports _Message Template V2_.
> In KakaoTalk Send to Me , you can send messages in much more variety than the existing _V1_ using the new message _template V2_.
> * Send to KakaoTalk allows you to define basic types of message templates that can be used widely , such as _feeds_, _lists_, _locations_, and _scraps_, and provides an interface that allows you to easily send them with parameters.
> * Provides a Message Template builder that allows you to configure message templates optimized for Third Party services. You can easily create your own service-specific message templates by going to the _[My Applications] - App Selection - [Settings] - [Message Template v2]_ menu on the developer's website.
> KakaoTalk To view messages sent to you, you must have KakaoTalk installed on your mobile device that supports this version of the message template. Below are the minimum versions of KakaoTalk by platform that support sending the latest version to me.
> * Android: **6.0.0**
> * iOS: **5.9.8**
* [Introduction to Message Types](/docs/restapi/kakaotalk-api#나에게-보내기-메시지-타입-소개)
* [Send default templates](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-보내기)
* [Send scrap template](/docs/restapi/kakaotalk-api#나에게-보내기-스크랩템플릿-보내기)
* [Sending a custom template](/docs/restapi/kakaotalk-api#나에게-보내기-커스텀-템플릿-보내기)
### Introduction to Message Types
KakaoTalk links send messages using predefined message templates. The types of message templates you can send to a KakaoTalk link are:

**Click on the message you want to learn more about.**

default_feed.png feed.png
default_list.png list.png
default_commerce.png commerce.png
default_location.png default_scrap.png

### Send default templates
In KakaoTalk Send to Me, _you_ can define the _default_ message template as the _default_ template and send the [feed](/docs/restapi/kakaotalk-api#나에게-보내기-피드-템플릿-보내기), [list](/docs/restapi/kakaotalk-api#나에게-보내기-리스트-템플릿-보내기), [location](/docs/restapi/kakaotalk-api#나에게-보내기—위치-템플릿-보내기), and [commerce](/docs/restapi/kakaotalk-api#나에게-보내기-커머스-템플릿-보내기) message template simply by creating parameters without creating any separate template.

**Send feed template**

> 1. Image area: Up to 1 image / 800px * 800px or higher (recommended)
> 2. Title / Description area: Up to 4 lines (title, description, 2 lines each)
> 3. Social area: Show up to 3 (Order: Like> Comment> Share> View> Subscribe)
> 4. Button area: Up to 2 displays, button name 8 characters or less recommended

default_feed_spec.png

A feed template provided as a _default template_ has one [content](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-content오브젝트) and one default [button](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-button오브젝트). You can add [social](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-social오브젝트) information and set up any button.
> You can send your feed template in a larger format, such as multiple images, profile information, etc. , using a [custom template](/docs/restapi/kakaotalk-api#나에게-보내기-커스텀-템플릿-보내기).
A feed template, provided as a default template, can have up to two buttons, one for content and one for social information.

**[Request]**
```
POST /v2/api/talk/memo/default/send HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
```
If you request a user token in the header and a POST in the body with the appropriate parameters for each template, a message filled with parameters will be sent to the default template in your own chat room.

key             | Explanation | necessary | type
----------------|-------------|-----------|-----------------
template_object | feed object | O         | JSON feed object

**feed object**

key          | Explanation                                                                                                   | necessary | type
-------------|---------------------------------------------------------------------------------------------------------------|-----------|------------------------
object_type  | Template type. "feed" fixed value                                                                             | O         | String
content      | About the main content of a message                                                                           | O         | content object
social       | Social information about your content                                                                         | X         | social object
button_title | Set when you want to change the default button title ("Details")                                              | X         | String
buttons      | List of buttons. Button Used when you want to change title and link, when you want to use two buttons (max 2) | X         | Array of button objects

> buttonTitle, buttons If both are present, buttons is used. If both are not given, a button is constructed with the link information in the default title and content.

For example,
```bash
curl -v -X POST "https://kapi.kakao.com/v2/api/talk/memo/default/send" \
-H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
-d 'template_object= {
  "object_type": "feed",
  "content": {
    "title": "디저트 사진",
    "description": "아메리카노, 빵, 케익",
    "image_url": "http://mud-kage.kakao.co.kr/dn/NTmhS/btqfEUdFAUf/FjKzkZsnoeE4o19klTOVI1/openlink_640x640s.jpg",
    "image_width": 640,
    "image_height": 640,
    "link": {
      "web_url": "http://www.daum.net",
      "mobile_web_url": "http://m.daum.net",
      "android_execution_params": "contentId=100",
      "ios_execution_params": "contentId=100"
    }
  },
  "social": {
    "like_count": 100,
    "comment_count": 200,
    "shared_count": 300,
    "view_count": 400,
    "subscriber_count": 500
  },
  "buttons": [
    {
      "title": "웹으로 이동",
      "link": {
        "web_url": "http://www.daum.net",
        "mobile_web_url": "http://m.daum.net"
      }
    },
    {
      "title": "앱으로 이동",
      "link": {
        "android_execution_params": "contentId=100",
        "ios_execution_params": "contentId=100"
      }
    }
  ]
}'
```
**[Response]**

The response body is a JSON object that contains the following values:

key         | Explanation            | type
------------|------------------------|--------
result_code | Transfer successful: 0 | Integer

For example,
```
HTTP/1.1 200 OK
{
  "result_code":0
}
```
**Send list template**
> 1. Header area
> 2. Item list area: Display up to 3 items
> 3. Title / Description area: Display up to 3 lines (2 lines of title, 1 line of description)
> 4. Image area: 400px * 400px (recommended)
> 5. Button area: Up to 2 displays, button name 8 characters or less recommended

default\_list\_spec.png

A list template consists of header titles exposed at the top of the message, a list of [content](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-content오브젝트), and [buttons](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-button오브젝트). You can have a [link](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-link오브젝트) to each header and content . Like the feed template, it has one default button and you can set any button.
> More extensive list templates, including more than 3 [content](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-content오브젝트), can be sent using custom templates.

**[Request]**
```
POST /v2/api/talk/memo/default/send HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
```
If you request a user token in the header and a POST in the body with the appropriate parameters for each template, a message filled with parameters will be sent to the default template in your own chat room.

key             | Explanation | necessary | type
----------------|-------------|-----------|-------------------
template_object | list object | O         | JSON [list object](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-list오브젝트)

**list object**

key          | Explanation                                                                                                   | necessary | type
-------------|---------------------------------------------------------------------------------------------------------------|-----------|------------------------
object_type  | Template type. "list" fixed value                                                                             | O         | String
header_title | The main title exposed at the top of the list. 200 characters max                                             | O         | String
header_link  | Link information corresponding to header title content                                                        | O         | [link object](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-link오브젝트)
contents     | List of content exposed on the list. Two or more required. Up to 3                                            | O         | Array of [content objects](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-content오브젝트)
button_title | Set when you want to change the default button title ("Details")                                              | X         | String
buttons      | List of buttons. Button Used when you want to change title and link, when you want to use two buttons (max 2) | X         | Array of [button objects](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-button오브젝트)

> buttonTitle, buttons If both are present, buttons is used. If both are not given, a button is constructed with the link information in the default title and content.

For example,
```bash
curl -v -X POST "https://kapi.kakao.com/v2/api/talk/memo/default/send" \
-H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
-d 'template_object= {
  "object_type": "list",
  "header_title": "WEEKELY MAGAZINE",
  "header_link": {
    "web_url": "http://www.daum.net",
    "mobile_web_url": "http://m.daum.net",
    "android_execution_params": "main",
    "ios_execution_params": "main"
  },
  "contents": [
    {
      "title": "자전거 라이더를 위한 공간",
      "description": "매거진",
      "image_url": "http://mud-kage.kakao.co.kr/dn/QNvGY/btqfD0SKT9m/k4KUlb1m0dKPHxGV8WbIK1/openlink_640x640s.jpg",
      "image_width": 640,
      "image_height": 640,
      "link": {
        "web_url": "http://www.daum.net/contents/1",
        "mobile_web_url": "http://m.daum.net/contents/1",
        "android_execution_params": "/contents/1",
        "ios_execution_params": "/contents/1"
      }
    },
    {
      "title": "비쥬얼이 끝내주는 오레오 카푸치노",
      "description": "매거진",
      "image_url": "http://mud-kage.kakao.co.kr/dn/boVWEm/btqfFGlOpJB/mKsq9z6U2Xpms3NztZgiD1/openlink_640x640s.jpg",
      "image_width": 640,
      "image_height": 640,
      "link": {
        "web_url": "http://www.daum.net/contents/2",
        "mobile_web_url": "http://m.daum.net/contents/2",
        "android_execution_params": "/contents/2",
        "ios_execution_params": "/contents/2"
      }
    },
    {
      "title": "감성이 가득한 분위기",
      "description": "매거진",
      "image_url": "http://mud-kage.kakao.co.kr/dn/NTmhS/btqfEUdFAUf/FjKzkZsnoeE4o19klTOVI1/openlink_640x640s.jpg",
      "image_width": 640,
      "image_height": 640,
      "link": {
        "web_url": "http://www.daum.net/contents/3",
        "mobile_web_url": "http://m.daum.net/contents/3",
        "android_execution_params": "/contents/3",
        "ios_execution_params": "/contents/3"
      }
    }
  ],
  "buttons": [
    {
      "title": "웹으로 이동",
      "link": {
        "web_url": "http://www.daum.net",
        "mobile_web_url": "http://m.daum.net"
      }
    },
    {
      "title": "앱으로 이동",
      "link": {
        "android_execution_params": "main",
        "ios_execution_params": "main"
      }
    }
  ]
}'
```
**[Response]**
The response body is a JSON object that contains the following values:

key         | Explanation            | type
------------|------------------------|--------
result_code | Transfer successful: 0 | Integer

For example,
```
HTTP/1.1 200 OK
{
  "result_code":0
}
```
**Send location template**

> 1. Image area: Up to 1 image / 800px * 800px or higher (recommended)
> 2. Title / Description area: Up to 4 lines (title, description, 2 lines each)
> 3. Social area: Show up to 3 (Order: Like> Comment> Share> View> Subscribe)
> 4. Button area: Up to 2 displays, button name 8 characters or less recommended

kakao_link_default_location.png

A location template consists of address information used to display the map and content objects that can describe the location. Basic button at the bottom left, "View location" button to show the map at the bottom right is added. If you click the "View location" button, you can see the location of the address by switching directly to the map screen in the Kakao Talk room.

**[Request]**
```
POST /v2/api/talk/memo/default/send HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
```
If you request a user token in the header and a POST in the body with the appropriate parameters for each template, a message filled with parameters will be sent to the default template in your own chat room.

key             | Explanation     | necessary | type
----------------|-----------------|-----------|---------------------
template_object | location object | O         | JSON [location object](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-location오브젝트)

**location object**

key           | Explanation                                                                                                                                   | necessary | type
--------------|-----------------------------------------------------------------------------------------------------------------------------------------------|-----------|------------------------
object_type   | Template type. "location" fixed value                                                                                                         | O         | String
address       | Address of location to share (eg: 235, Bundang-gu, Bundang-gu, Seongnam-si, Gyeonggi-do)                                                      | O         | String
address_title | Title used in map view within KakaoTalk (Ex: Cacao Pangyo Office)                                                                             | X         | String
content       | Content information describing your location                                                                                                  | O         | [content object](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-content오브젝트)
social        | Additional social information                                                                                                                 | X         | [social object](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-social오브젝트)
button_title  | Set when you want to change the default button title ("Details")                                                                              | X         | String
buttons       | List of buttons. Set when you want to change the link besides the title of the default button. (Max 1, right "Show position" button is fixed) | X         | Array of [button objects](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-button오브젝트)

> buttonTitle, buttons If both are present, buttons is used. If both are not given, a button is constructed with the link information in the default title and content.

For example,
```bash
curl -v -X POST "https://kapi.kakao.com/v2/api/talk/memo/default/send" \
-H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
-d 'template_object={
  "object_type": "location",
  "content": {
    "title": "카카오 판교오피스",
    "description": "카카오 판교오피스 위치입니다.",
    "image_url": "https://mud-kage.kakao.com/dn/drTdbB/bWYf06POFPf/owUHIt7K7NoGD0hrzFLeW0/kakaolink40_original.png",
    "image_width": 800,
    "image_height": 800,
    "link": {
      "web_url": "http://dev.kakao.com",
      "mobile_web_url": "http://dev.kakao.com/mobile",
      "android_execution_params": "platform=android",
      "ios_execution_params": "platform=ios"
    }
  },
  "buttons": [
    {
      "title": "웹으로 보기",
      "link": {
        "web_url": "http://dev.kakao.com",
        "mobile_web_url": "http://dev.kakao.com/mobile"
      }
    }
  ],
  "address": "경기 성남시 분당구 판교역로 235 에이치스퀘어 N동 7층",
  "address_title": "카카오 판교오피스"
}'
```
**[Response]**

The response body is a JSON object that contains the following values:

key         | Explanation            | type
------------|------------------------|--------
result_code | Transfer successful: 0 | Integer

For example,
```
HTTP/1.1 200 OK
{
  "result_code":0
}
```
**Send Commerce Template**

> 1. Image area: Up to 1 image / 800px * 800px or higher (recommended)
> 2. Discounted Price Area
> 3. Regular Price Area
> 4. Discount rate area
> 5. Product name area: Display up to 2 lines
> 6. Button area: Up to 2 displays, button name 8 characters or less recommended

default_commerce_spec.png

Commerce templates, which are provided as _default templates_, have one [content](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-content오브젝트), one [commerce content](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-commerce-detail오브젝트) and one default [button](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-button오브젝트).
> You can send your feed template in a larger format, such as multiple images, profile information, etc., using a [custom template](/docs/restapi/kakaotalk-api#나에게-보내기-커스텀-템플릿-보내기).
Commerce templates, which are provided as default templates, can have up to two buttons, one for content and one for commerce.

**[Request]**
```
POST /v2/api/talk/memo/default/send HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
```
If you request a user token in the header and a POST in the body with the appropriate parameters for each template, a message filled with parameters will be sent to the default template in your own chat room.

key             | Explanation     | necessary | type
----------------|-----------------|-----------|-----------------------
template_object | commerce object | O         | JSON [commerce object](/docs/restapi/kakaotalk-api#나에게-보내기-기본템플릿-commerce오브젝트)

**commerce object**

key	Explanation	necessary	type
object_type	Template type. "commerce" fixed value	O	String
content	The main content information of the message. description unavailable	O	content object
commerce	Price information for the item	O	commerce detail object
button_title	Set when you want to change the default button title ("Details")	X	String
buttons	List of buttons. Button Used when you want to change title and link, when you want to use two buttons (max 2)	X	Array of button objects
buttonTitle, buttons If both are present, buttons is used. If both are not given, a button is constructed with the link information in the default title and content.

For example,
curl -v -X POST "https://kapi.kakao.com/v2/api/talk/memo/default/send" \
-H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
-d 'template_object= {
  "object_type": "commerce",
  "content": {
    "title": "Ivory long dress (4 Color)",
    "image_url": "http://mud-kage.kakao.co.kr/dn/RY8ZN/btqgOGzITp3/uCM1x2xu7GNfr7NS9QvEs0/kakaolink40_original.png",
    "image_width": 640,
    "image_height": 640,
    "link": {
      "web_url": "https://style.kakao.com/main/women/contentId=100",
      "mobile_web_url": "https://style.kakao.com/main/women/contentId=100",
      "android_execution_params": "contentId=100",
      "ios_execution_params": "contentId=100"
    }
  },
  "commerce": {
    "regular_price": 208800,
    "discount_price": 146160,
    "discount_rate": 30
  },
  "buttons": [
    {
      "title": "구매하기",
      "link": {
        "web_url": "https://style.kakao.com/main/women/contentId=100/buy",
        "mobile_web_url": "https://style.kakao.com/main/women/contentId=100/buy",
        "android_execution_params": "contentId=100&buy=true",
        "ios_execution_params": "contentId=100&buy=true"
      }
    },
    {
      "title": "공유하기",
      "link": {
        "web_url": "https://style.kakao.com/main/women/contentId=100/share",
        "mobile_web_url": "https://style.kakao.com/main/women/contentId=100/share",
        "android_execution_params": "contentId=100&share=true",
        "ios_execution_params": "contentId=100&share=true"
      }
    }
  ]
}'
[Response]
The response body is a JSON object that contains the following values:
key	Explanation	type
result_code	Transfer successful: 0	Integer
For example,
HTTP/1.1 200 OK
{
  "result_code":0
}
content object
An object that contains the content of the content.
key	Explanation	necessary	type
title	Title of content	O	String
image_url	Image URL of the content	O	String
image_width	Image width of the content in pixels	X	Int
image_height	Image height of content in pixels	X	Int
description	Detailed description of the content. Up to 4 lines combined with title.	X	String
link	About links to be moved on content clicks	O	link object
Message template image requirements
Image URLs that do not comply with RFC2396 , RFC1034 , or RFC1123 will not see the image.
Images must be at least 200 pixels wide by 200 pixels high .
Can not exceed 2MB .
link object
A link information object that is moved in the content area or on a button click in a message. One of the four must be mandatory because it represents the URL that is moved on click. If there is no link information for each platform, the representative information registered in the developer's site is used in the platform KakaoTalk, or if the registered information is not available, the button is not displayed or the button is not clicked.
The link execution priority is xxxExecutionParams> mobileWebURL> webURL.
key	Explanation	necessary	type
web_url	PC version Web link URL used in Kakao Talk. The domain part matches the domain registered on the developer site	Conditional O	String
mobile_web_url	Web link URL used in mobile KakaoTalk. The domain part matches the domain registered on the developer site	Conditional O	String
android_execution_params	A parameter to be used for the app link URL used by Android KakaoTalk. Use mobile_web_url if no value is available	Conditional O	String
ios_execution_params	Parameters to be used for app link URLs used in iOS KakaoTalk. Use mobile_web_url if no value is available	Conditional O	String
social object
An object used to represent social information such as likes, comments, etc.
Only up to three of the five attributes are displayed. The priority is Like > Comment > Shared > View > Subscriber .
key	Explanation	necessary	type
like_count	Likes of content	X	Int
comment_count	Comments on the content	X	Int
shared_count	Shared content
view_count	Views of your content	X	Int
subscriber_count	Number of content subscriptions	X	Int
commerce detail object
An object used to represent pricing information.
key	Explanation	necessary	type
regular_price	Normal price	O	Int
discount_price	Discounted prices	X	Int
discount_rate	Discount rate	X	Int
button object
The button object added at the bottom of the message.
key	Explanation	necessary	type
title	Title of the button	O	String
link	Link information to move when button is clicked	O	link object
Send scrap template
Image area: Up to 1 image / 800px * 800px or higher (recommended)
Title / Description area: Up to 4 lines (title, description, 2 lines each)
Button area: Up to 1 display, button name 8 characters or less recommended
A. og: image / B. video: duration, music: duration / C. og: title, og: description /
default_scrap_spec.png
Using the Open Graph information of the web page , you can easily send the message with the URL without creating a separate template .
The domain of the website you want to scrap must be registered in your application settings. You can register your 
domain from the [My Applications] - Select Applications - [Settings] - [General] menu on the developer's website .
[Request]
POST /v2/api/talk/memo/scrap/send HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
If you fill in the user token in the header, the web URL to be scrapped in the body, and request it as POST, the scrap results will be sent in a pretty speech bubble message in your own chat room.
key	Explanation	necessary	type
request_url	The target URL to be scraped. The domain part matches the domain registered on the developer site	O	String
template_id	In addition to the default scrap template, you can use the [Message Template v2] menu to change it for your own scrap. Message template id	X	Long
template_args	If template_id is specified, it must be given in key: value format if it is necessary. Can not overwrite scrap results	X	JSONObject

For example,
curl -v -X POST "https://kapi.kakao.com/v2/api/talk/memo/scrap/send" \
-H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
-d 'request_url=https://developers.kakao.com'
[Response]
The response body is a JSON object that contains the following values:
key	Explanation	type
result_code	Transfer successful: 0	Integer
For example,
HTTP/1.1 200 OK
{
  "result_code":0
}
If you want to send more specific types of scrap messages to your service, you should use a custom template for scrap.
Custom Template scrap used in the developer's website [My Apps] - Select app - Settings - Message templates v2] can be produced through the menu. By default, creating and sending a template is the same as sending a custom template, but the generated template must have the parameters listed in the table below (Template Arguments). The Web site information that is scraped into the Message Template component that contains the key is automatically populated on the server .
Argument key	Explanation
SCRAP_REQUEST_URL	Scrap request URL
SCRAP_HOST	Host of request URL
SCRAP_TITLE	Title of request URL
SCRAP_DESCRIPTION	Description of request URL
SCRAP_IMAGE	og: image
SCRAP_IMAGE_WIDTH	og: width of image
SCRAP_IMAGE_HEIGHT	og: height of image
SCRAP_DURATION	og: duration
For example, the message using the template builder to create a feed template "$ {SCRAP_IMAGE}" the image , "$ {SCRAP_TITLE}" in the title , "$ {SCRAP_DESCRIPTION}" in the description enter and send this template to a web page The corresponding scrap information is exposed in the image , title , and description location, respectively. At this time, the parameters (Template Arguments) in the above table are inputted from the server, so a separate template_args value is not required.
Sending a custom template
If the template above is not enough, you can freely create your own message templates using the Message Template builder .
FEED TYPE
Image area: Up to 3 images / 800px * 800px or higher (recommended)
Profile: An area that represents profile images and profile names
Title / Description area: Up to 4 lines (title, description, 2 lines each)
Social area: Show up to 3 (Order: Like> Comment> Share> View> Subscribe)
Button area: Up to 2 displays, button name 8 characters or less recommended
Service / origin information area: Service name, area expressing source
feed_spec.png
LIST TYPE
Header area: background image can be set
Item list area: Display up to 5 items
Title / Description area: Display up to 3 lines (2 lines of title, 1 line of description)
Image area: Playback icon / Time display possible / 400px * 400px (Recommended)
Button area: Up to 2 displays, button name 8 characters or less recommended
Service / origin information area: Service name, area expressing source
list_spec.png
COMMERCE TYPE
Image area: Up to 3 images / 800px * 800px or higher (recommended)
Profile: An area that represents profile images and profile names
Discounted Price Area
Regular Price Area
Discount rate area
Product name area: Display up to 2 lines
Button area: Up to 2 displays, button name 8 characters or less recommended
commerce_spec.png
Custom template developer's website [My Apps] - Select app - Settings - Message templates v2] can be produced through the menu. Creating a message template
URLs or text values ​​entered in custom templates can be set dynamically at the time of message transfer . Define your own parameters for the values ​​you want to change, and if you pass the key and value of the parameter at the time of transmission, the specified value is exposed in the message. For example, if you want to change the content description of a feed template dynamically, enter " $ {MY_DESCRIPTION} " in the description and " {" MY_DESCRIPTION ":" the changed description .
[Request]
POST /v2/api/talk/memo/send HTTP/1.1
Host: kapi.kakao.com
Authorization: Bearer {access_token}
If the user token is in the header, the template id and the argument in the corresponding template are filled in the body and the POST request is made, the message will be sent to your own chat room.
key	Explanation	necessary	type
template_id	[Message template v2] The message template id	O	Integer
args	The key: value format of arg defined in the message template. JSONObject.	X	JSON

For example,
curl -v -X POST "https://kapi.kakao.com/v2/api/talk/memo/send" \
-H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
-d 'template_id=123456' \
--data-urlencode 'args={"ARTICLE_ID":123}'
[Response]
The response body is a JSON object that contains the following values:
key	Explanation	type
result_code	Transfer successful: 0	Integer
For example,
HTTP/1.1 200 OK
{
  "result_code":0
}
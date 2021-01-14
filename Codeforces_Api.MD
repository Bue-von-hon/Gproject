# Codeforces Api

# Introduction

### Introduction

With Codeforces API you can get access to some of our data in machine-readable JSON format.

코드포스 api를 이용하면 Json 포맷의 데이터를 얻을 수 있습니다

To access the data you just send a HTTP-request to address https://codeforces.com/api/{methodName} with method-specific parameters.

Each method description has an example URL

코드포스 주소로 HTTP-request를 보내면 메소드에 대한 데이터를 얻을 수 있고, 각각의 메소드들은 설명과 함께 예제가 같이 적혀있습니다

Each method call returns a JSON-object with three possible fields: status, comment and result.

Status is either "OK" or "FAILED".

If status is "FAILED" then comment contains the reason why the request failed. If status is "OK", then there is no comment.

If status is "OK" then result contains method-dependent JSON-element which will be described for each method separately. If status is "FAILED", then there is no result.

각각의 메소드에 대한 호출은 JSON-object를 반환값으로 받는데 이때 객체는 상태, 주석, 결과 이 3가지에 대한 정보를 담고있습니다

상태는 ok, failed 둘 중 하나 입니다

만약 상태가 실패라면 주석에는 왜 이 request가 실패했는지에 대한 이유가 적혀있습니다 그리고 결과란은 공란입니다

만약 상태가 ok라면 주석은 공란입니다 그리고 이때 결과란에는 메소드가 호출한 JSON-element를 담고 있습니다

어떤 요소가 담겨 오는지는 각 메소드의 설명에 적혀있습니다

API may be requested at most 5 times in one second. 

If you send more requests, you will receive a response with "FAILED" status and "Call limit exceeded" comment.

API의 호출은 초당 최대 약 5번 정도가 권장됩니다

만약 이보다 더 빈번하게 호출하게 된다면, request는 failed될 수 있고, 이는 주석란에 Call limit exceeded라고 적혀 있는 것을 통해 알 수 있습니다

Language-depended fields like names or descriptions will be returned in the default language.

Also, you can pass additional parameter lang with values en and ru to select the language of the result.

언어에 따라 달라질 수 있는 내용들은, 이름 설명, 초기 설정 값에 설정된 언어에 맞게 리턴값을 반환합니다

이를 바꾸고 싶으면 추가로 파라미터에 언어에 대한 정보 en, ru를 보내주면 됩니다 (이 옵션은 영어, 러시아 두개가 제공됩니다)

### Authorization
All methods can be requested anonymously. This way only public data will be accessable via API. 

모든 메소드들의 호출은 익명으로 가능합니다. 하지만 이런 경우 공공 데이터에 한해서만 접근이 가능합니다

To access data, private for some user (e.g. hacks during the contest), API key must be generated on https://codeforces.com/settings/api page. 

만약 프라이빗한 정보에(contest중의 hacks 등) 대해 접근하고 싶다면, API key를 생성해야 합니다

Each API key has two parameters: key and secret. To use the key you must add following parameters to your request.

각각의 키는 두가지 파라미터를 갖습니다. (key, secret)

key를 사용하기 위해서는 request를 보낼 때 아래의 파라미터들을 전달해야 합니다

1. apiKey — it must be equal to key

apikey의 값은 key값과 동일하게 전달해야 합니다.   

2. time — current time in unix format (e.g., System.currentTimeMillis()/1000). 

현재 시간을 전달해야합니다.
   
If the difference between server time and time, specified in parameter, will be greater than 5 minutes, request will be denied.

만약 서버의 시간과 전달하는 시간의 차이가 5분 이상이면 request는 거부됩니다.
   
3. apiSig — signature to ensure that you know both key and secret. First six characters of the apiSig parameter can be arbitrary. 

싸인은 key, secret 둘 모두를 알고있다는 것을 보장합니다. 또 apiSig의 첫 6글자는 임의로 선택 가능합니다.
   
We recommend to choose them at random for each request.

코드포스에서는 이를 각각의 request에 대해 랜덤하게 선택하는 것을 권장합니다

Let's denote them as rand. The rest of the parameter is hexadecimal representation of SHA-512 hash-code of the following string: <rand>/<methodName>?param1=value1&param2=value2...&paramN=valueN#<secret> where (param_1, value_1), (param_2, value_2),..., (param_n, value_n) are all the request parameters (including apiKey, time, but excluding apiSig) with corresponding values, sorted lexicographically first by param_i, then by value_i.

우선 apisig의 첫 6글자를 rand라고 가정해봅시다.

apiSig 파라미터의 나머지 부분은 16진수 바탕의 SHA-512 해싱을 적용합니다.

이때 해싱은 <rand>/<methodName>?param1=value1&param2=value2...&paramN=valueN#<secret> 이 부분을 해싱합니다.

paranm1, value1 등등은 request에 포함되는 모든 파라미터들을 표현한 것 입니다.(apikey, time 역시 포함 apiSig는 포함X)

For example:

If your key is xxx, secret is yyy, chosen rand is 123456 and you want to access method contest.

만약 key=xxx, secret=yyy이고 임의로 선택한 rand=123456이라면,

hacks for contest 566, you should compose request like this: https://codeforces.com/api/contest.hacks?contestId=566&apiKey=xxx&time=1610337889&apiSig=123456<hash>, where <hash> is sha512Hex(123456/contest.hacks?apiKey=xxx&contestId=566&time=1610337889#yyy)

566번 contest hacks에 대한 request는 아래와 같습니다.

https://codeforces.com/api/contest.hacks?contestId=566&apiKey=xxx&time=1610337889&apiSig=123456<hash>

이때 hash의 값은 sha512Hex(123456/contest.hacks?apiKey=xxx&contestId=566&time=1610337889#yyy) 입니다.

### JSONP

JSONP is supported. Add jsonp parameter to the query and the result will be returned as JavaScript function call.

JSONNP 역시 제공합니다.

query에 jsonp 파라미터를 추가함으로서 결과를 JS 함수 호출 형태로 반환 받을 수 있습니다. 

For example, if jsonp parameter is parseResponse and method returned object {"status":"OK","response":"..."},

then final result is parseResponse({"status":"OK","response":"..."});.

이를 활용하면,

{"status":"OK","response":"..."} 와 같은 리턴 값이

parseResponse({"status":"OK","response":"..."}); 와 같이 변하게 됩니다.

# Return objects

### User

Represents a Codeforces user.

코드포스 유저에 대한 정보입니다

|Field	|Description|
|------|---|
|handle	|String. Codeforces user handle. 유저의 닉네임|
|email	|String. Shown only if user allowed to share his contact info. 유저가 개인정보 제공에 동의한 경우 email을 보여줍니다|
|vkId	|String. User id for VK social network. Shown only if user allowed to share his contact info. 유저가 개인정보 제공에 동의한 경우 VK SNS 아이디를 보여줍니다|
|openId	|String. Shown only if user allowed to share his contact info.|
|firstName	|String. Localized. Can be absent.|
|country	|String. Localized. Can be absent.|
|city	|String. Localized. Can be absent.|
|organization	|String. Localized. Can be absent. 소속, 공란 가능, 언어 옵션 선택 가능|
|contribution	|Integer. User contribution. 유저의 기여도|
|rank	|String. Localized. 랭크|
|rating	|Integer. 레이팅|
|maxRank	|String. Localized. 최대 랭크|
|maxRating	|Integer. 최고 레이팅|
|lastOnlineTimeSeconds	|Integer. Time, when user was last seen online, in unix format.|
|registrationTimeSeconds	|Integer. Time, when user was registered, in unix format.|
|friendOfCount	|Integer. Amount of users who have this user in friends. 이 유저를 친구로 추가한 유저들의 수|
|avatar	|String. User's avatar URL.|
|titlePhoto	|String. User's t itle photo URL.|

### RatingChange

Represents a participation of user in rated contest.

rated되는 contest에 참가한 유저의 참여정보를 보여줍니다

|Field	|Description|
|----|----|
|contestId|	Integer. contest의 아이디|
|contestName|	String. Localized. contest의 이름|
|handle|	String. Codeforces user handle. 유저의 핸들|
|rank|	Integer. Place of the user in the contest. This field contains user rank on the moment of rating update. If afterwards rank changes (e.g. someone get disqualified), this field will not be update and will contain old rank. 유저가 몇등을 했는지 보여줍니다. 물론 이는 유저가 최근에 또 다른 rated contest에 참가를 했다면, 최근의 랭크와는 차이가 있을 수 있습니다.|
|ratingUpdateTimeSeconds|	Integer. Time, when rating for the contest was update, in unix-format.|
|oldRating|	Integer. User rating before the contest. 이전의 레이팅|
|newRating|	Integer. User rating after the contest. 이후의 레이팅|

### Contest

Represents a contest on Codeforces.

contest에 대한 정보를 보여줍니다.

|Field|	Description|
|---|---|
|id|	Integer.|
|name|	String. Localized.|
|type|	Enum: CF, IOI, ICPC. Scoring system used for the contest.|
|phase|	Enum: BEFORE, CODING, PENDING_SYSTEM_TEST, SYSTEM_TEST, FINISHED.|
|frozen|	Boolean. If true, then the ranklist for the contest is frozen and shows only submissions, created before freeze.|
|durationSeconds|	Integer. Duration of the contest in seconds.|
|startTimeSeconds|	Integer. Can be absent. Contest start time in unix format.|
|relativeTimeSeconds|	Integer. Can be absent. Number of seconds, passed after the start of the contest. Can be negative.|
|preparedBy|	String. Can be absent. Handle of the user, how created the contest.|
|websiteUrl|	String. Can be absent. URL for contest-related website.|
|description|	String. Localized. Can be absent.|
|difficulty|	Integer. Can be absent. From 1 to 5. Larger number means more difficult problems.|
|kind|	String. Localized. Can be absent. Human-readable type of the contest from the following categories: Official ICPC Contest, Official School Contest, Opencup Contest, School/University/City/Region Championship, Training Camp Contest, Official International Personal Contest, Training Contest.|
|icpcRegion|	String. Localized. Can be absent. Name of the Region for official ICPC contests.|
|country|	String. Localized. Can be absent.|
|city|	String. Localized. Can be absent.|
|season|	String. Can be absent.|

### Problem

Represents a problem.

문제에 대한 정보를 보여줍니다.

|Field|	Description|
|---|---|
|contestId|	Integer. Can be absent. Id of the contest, containing the problem.|
|problemsetName|	String. Can be absent. Short name of the problemset the problem belongs to.|
|index|	String. Usually a letter of a letter, followed by a digit, that represent a problem index in a contest.|
|name|	String. Localized.|
|type|	Enum: PROGRAMMING, QUESTION.|
|points|	Floating point number. Can be absent. Maximum ammount of points for the problem. 문제를 풀어 얻을 수 있는 점수|
|rating|	Integer. Can be absent. Problem rating (difficulty). 문제의 난이도|
|tags|	String list. Problem tags.|

### ProblemStatistics

Represents a statistic data about a problem.

문제에 대한 통계를 보여줍니다.

|Field|	Description|
|---|---|
|contestId|	Integer. Can be absent. Id of the contest, containing the problem.|
|index|	String. Usually a letter of a letter, followed by a digit, that represent a problem index in a contest.|
|solvedCount|	Integer. Number of users, who solved the problem. 몇명이 풀었는지 보여줍니다.|

### Submission

Represents a submission.

|Field|	Description|
|---|---|
|id|	Integer.|
|contestId|	Integer. Can be absent.|
|creationTimeSeconds|	Integer. Time, when submission was created, in unix-format.|
|relativeTimeSeconds|	Integer. Number of seconds, passed after the start of the contest (or a virtual start for virtual parties), before the submission.|
|problem|	Problem object.|
|author|	Party object.|
|programmingLanguage|	String.|
|verdict|	Enum: FAILED, OK, PARTIAL, COMPILATION_ERROR, RUNTIME_ERROR, WRONG_ANSWER, PRESENTATION_ERROR, TIME_LIMIT_EXCEEDED, MEMORY_LIMIT_EXCEEDED, IDLENESS_LIMIT_EXCEEDED, SECURITY_VIOLATED, CRASHED, INPUT_PREPARATION_CRASHED, CHALLENGED, SKIPPED, TESTING, REJECTED. Can be absent.|
|testset|	Enum: SAMPLES, PRETESTS, TESTS, CHALLENGES, TESTS1, ..., TESTS10. Testset used for judging the submission.|
|passedTestCount|	Integer. Number of passed tests.|
|timeConsumedMillis|	Integer. Maximum time in milliseconds, consumed by solution for one test.|
|memoryConsumedBytes|	Integer. Maximum memory in bytes, consumed by solution for one test.|
|points|	Floating point number. Can be absent. Number of scored points for IOI-like contests.|

# Methods

### contest.status

Returns submissions for specified contest. Optionally can return submissions of specified user.

특정 contest에 대한 submissions을 반환합니다.
특정 유저의 submissions만을 선택적으로 받을 수 있습니다.

|Parameter|	Description|
|---|---|
|contestId| (Required)	Id of the contest. It is not the round number. It can be seen in contest URL. For example: /contest/566/status|
|handle|	Codeforces user handle.|
|from|	1-based index of the first submission to return.|
|count|	Number of returned submissions.|

Return value: Returns a list of Submission objects, sorted in decreasing order of submission id.

submission 객체를 반환합니다.

Example: https://codeforces.com/api/contest.status?contestId=566&from=1&count=10



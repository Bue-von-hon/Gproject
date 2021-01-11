# Codeforces Api

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

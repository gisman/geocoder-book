# 주소 지오코딩

주소를 지리적 좌표(위도, 경도)로 변환합니다.

## GET 지오코딩

GET /geocode

### 요청 파라미터

파라미터
타입
필수
설명

q
string
예
하나 이상의 주소를 포함하는 문자열. 각 주소는 줄바꿈(\n)으로 구분

token
string
예
API 인증 토큰

최대 3천건까지 지오코딩할 수 있습니다. 더 많은 주소를 처리하려면 POST 방식의 API를 사용하세요.

줄바꿈(\n)은 URL 인코딩 시 %0A로 변환됩니다.

### curl 요청 예제

```
curl 'https://geocode-api.gimi9.com/geocode?q=서울특별시 송파구 송파대로8길 10%0A김제 온천길 37&token=YOUR_API_TOKEN'
```

url encoding을 사용하여 주소에 포함된 공백과 특수 문자를 인코딩해야 합니다. 예를 들어, 공백은 %20으로 인코딩됩니다.

```
curl 'https://geocode-api.gimi9.com/geocode?q=%EC%84%9C%EC%9A%B8%ED%8A%B9%EB%B3%84%EC%8B%9C%20%EC%86%A1%ED%8C%8C%EA%B5%AC%20%EC%86%A1%ED%8C%8C%EB%8C%80%EB%A1%9C8%EA%B8%B8%2010%0A%EA%B9%80%EC%A0%9C%20%EC%98%A8%EC%B2%9C%EA%B8%B8%2037&token=YOUR_API_TOKEN'
```

### python 요청 예제

```python
import requests

params = {
"q": "서울특별시 송파구 송파대로8길 10\n김제 온천길 37", # 줄 바꿈으로 주소를 구분하세요.
"token": "YOUR_TOKEN",  # 여기에 실제 토큰을 입력하세요
}
url = "https://geocode-api.gimi9.com/geocode"
response = requests.get(url, params=params)
print(response.json())
```

### 응답 예제

```
{
"document_id": null,
"document_name": null,
"total_time": 0.0036127567291259766,
"total_count": 2,
"success_count": 2,
"hd_success_count": 2,
"fail_count": 0,
"results": [
{
"inputaddr": "서울특별시 송파구 송파대로8길 10",
"success": true,
"errmsg": "",
"addressCls": "ROAD_ADDRESS",
"x_axis": 127.1291683066244,
"y_axis": 37.47688998121118,
"z": "05813",
"h1_nm": "서울",
"h23_nm": "송파구",
"hd_nm": "장지동",
"rm": "송파대로8길",
"h1_cd": "11",
"h23_cd": "11710",
"hd_cd": "1171064600",
"hd_history": [
{
"hd_cd": "1171064600",
"hd_nm": "장지동",
"hd_en": "Jangji-dong",
"from_yyyymm": "202305",
"to_yyyymm": "202507"
}
],
"kostat_h1_cd": "11",
"kostat_h2_cd": "11240",
"bld_mgt_no": "1171010900102520004000001",
"bm": [
"송파파인13",
"1304동"
]
},
{
"inputaddr": "김제 온천길 37",
"success": true,
"errmsg": "인근 도로명 주소: 37-1",
"addressCls": "ROAD_ADDRESS",
"x_axis": 127.84423028792409,
"y_axis": 37.70551640104829,
"z": "25112",
"h1_nm": "강원",
"h23_nm": "홍천군",
"hd_nm": "북방면",
"rm": "온천길",
"h1_cd": "51",
"h23_cd": "51720",
"hd_cd": "5172038000",
"hd_history": [
{
"hd_cd": "5172038000",
"hd_nm": "북방면",
"hd_en": "Bukbang-myeon",
"from_yyyymm": "202306",
"to_yyyymm": "202507"
},
{
"hd_cd": "4272038000",
"hd_nm": "북방면",
"hd_en": "Bukbang-myeon",
"from_yyyymm": "202305",
"to_yyyymm": "202305"
}
],
"kostat_h1_cd": "32",
"kostat_h2_cd": "32510",
"bld_mgt_no": "",
"bm": []
}
]
}
```

## POST 지오코딩

대량의 주소를 한 번에 지오코딩할 수 있는 POST 방식의 API입니다.

GET 방식과 동일한 기능을 제공하지만, 요청 본문에 JSON 형식으로 주소 목록을 전달합니다. GET 방식에 비해 더 많은 주소를 한 번에 처리할 수 있습니다.

POST /geocode

### 요청 파라미터

파라미터
타입
필수
설명

data
object
예
지오코딩할 주소 목록

token
string
예
API 인증 토큰

### 요청 본문 예제

```
{
"q": [
"서울특별시 송파구 송파대로8길 10",
"김제 온천길 37",
"강원 춘천시 남산면 서천리 산111"
]
}
```

### curl 요청 예제

```
curl 'https://geocode-api.gimi9.com/geocode' \
-H 'Content-Type: application/json' \
-H 'Authorization: YOUR_TOKEN' \
--data-raw $'{  "q": [    "서울특별시 송파구 송파대로8길 10",    "김제 온천길 37",    "강원 춘천시 남산면 서천리 산111"  ]}'
```

### python 요청 예제

```python
import requests
import json

url = "https://geocode-api.gimi9.com/geocode"
headers = {
"Content-Type": "application/json",
"Authorization": "YOUR_API_TOKEN"  # 여기에 실제 토큰을 입력하세요
}
data = {
"q": [
"서울특별시 송파구 송파대로8길 10",
"김제 온천길 37",
"강원 춘천시 남산면 서천리 산111"
]
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

### 응답 형식

GET 지오코딩과 동일한 응답 형식입니다.

### 응답 필드 설명

필드
타입
설명

total_time
float
총 처리 시간 (초)

total_count
integer
총 요청 주소 개수

success_count
integer
성공한 주소 개수

hd_success_count
integer
성공한 행정동 개수

fail_count
integer
실패한 주소 개수

results
array
지오코딩 결과 목록

### results array의 객체 필드

필드
타입
설명

inputaddr
string
입력 주소

success
boolean
지오코딩 성공 여부

errmsg
string
오류 메시지

addressCls
string
주소 유형

x_axis
float
경도 (EPSG:4326) (wgs84)

y_axis
float
위도 (EPSG:4326) (wgs84)

z
string
우편번호

h1_nm
string
광역시도 이름

h23_nm
string
시군구 이름

hd_nm
string
행정동 이름

rm
string
도로명

h1_cd
string
광역시도 코드

h23_cd
string
시군구 코드

hd_history
array
행정동 이력

kostat_h1_cd
string
통계청 광역시도 코드

kostat_h2_cd
string
통계청 시군구 코드

bld_mgt_no
string
건물 관리 번호

bm
array
건물명 목록

# 역 지오코딩

지리적 좌표(위도, 경도)를 주소로 변환합니다.

도로명 주소와 지번 주소를 모두 반환합니다. 도로명 주소가 없는 경우 지번 주소만 반환됩니다.

GET /reverse_geocode

### 요청 파라미터

파라미터
타입
필수
설명

x
float
예
경도 좌표 (EPSG:4326)

y
float
예
위도 좌표 (EPSG:4326)

token
string
예
API 인증 토큰

### 요청 예제

```
GET https://geocode-api.gimi9.com/reverse_geocode?x=127.1146829&y=37.5138498&token=YOUR_API_TOKEN
```

### curl 요청 예제

```
curl 'https://geocode-api.gimi9.com/reverse_geocode?x=127.1146829&y=37.5138498&token=YOUR_API_TOKEN'
```

### python 요청 예제

```python
import requests

params = {
"x": 127.1146829,
"y": 37.5138498,
"token": "YOUR_API_TOKEN"  # 여기에 실제 토큰을 입력하세요
}
url = "https://geocode-api.gimi9.com/reverse_geocode"
response = requests.get(url, params=params)
print(response.json())
```

### 응답 예제

```
{
"road_addr": {
"ADR_MNG_NO": "11710109416935500001000000",
"yyyymm": "MST.46000",
"address": "서울특별시 송파구 송파대로8길 10",
"success": true,
"geom": "POLYGON ((127.127840 37.477157, 생략...))",
"errmsg": ""
},
"jibun_addr": {
"PNU": "1171010900108570000",
"yyyymm": "202404",
"address": "서울특별시 송파구 장지동 857번지",
"success": true,
"geom": "POLYGON ((127.127839 37.477158, 생략...))",
"errmsg": ""
},
"success": true
}
```

### 응답 필드 설명

필드
타입
설명

road_addr
object
도로명 주소 정보(도로명 주소가 있는 경우에만 포함)

jibun_addr
object
지번 주소 정보

success
boolean
전체 요청 성공 여부

#### road_addr / jibun_addr 객체 필드

필드
타입
설명

ADR_MNG_NO
string
건물 관리 번호 - 도로명 주소에만 해당

PNU
string
PNU (지번 주소 관리 번호) - 지번 주소에만 해당

yyyymm
string
데이터 기준 년월 (YYYYMM 형식, 예: 202306)

address
string
전체 주소 (도로명 또는 지번)

success
boolean
리버스 지오코딩 성공 여부

geom
string
WKT 형식의 지오메트리 정보 (예: POINT(X Y))

errmsg
string
오류 메시지

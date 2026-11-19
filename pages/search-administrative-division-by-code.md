# 코드로 행정구역 형상 검색

주어진 코드로 행정구역 영역(광역시도, 시군구, 행정동)을 찾아 지리적 형상 정보를 반환합니다.

특정 년월의 행정구역 정보를 조회할 수 있으며, 행정구역 경계의 WKT(Well-Known Text) 형식의 지오메트리 정보를 제공합니다.

GET /region

### 요청 파라미터

파라미터
타입
필수
설명

type
string
예
영역 유형. 기본값은 "hd" (행정동). 가능한 값: "h1" (광역시도), "h23" (시군구), "hd" (행정동)

code
string
예
행정구역 코드. 콤마로 구분하여 여러 개 입력 가능. 테스트용 기본값은 "1114055000" (명동)

yyyymm
string
아니오
특정 년월(YYYYMM)의 행정구역을 조회. 생략시 현재 기준 데이터를 조회합니다.

token
string
예
API 인증 토큰

### 요청 예제

```
GET https://geocode-api.gimi9.com/region?type=hd&code=1114055000&yyyymm=202506&token=YOUR_API_TOKEN
```

### curl 요청 예제

```
curl 'https://geocode-api.gimi9.com/region?type=hd&code=1114055000&yyyymm=202506&token=YOUR_API_TOKEN'
```

### python 요청 예제

```python
import requests

params = {
"type": "hd",           # 행정동
"code": "1114055000,4111760000'",  # 명동, 수원 광교1동
"yyyymm": "202506",    # 2025년 6월
"token": "YOUR_API_TOKEN"  # 여기에 실제 토큰을 입력하세요
}
url = "https://geocode-api.gimi9.com/region"
response = requests.get(url, params=params)
print(response.json())
```

### 응답 예제

```
[
{
"success": true,
"type": "hd",
"code": "1114055000",
"name": "명동",
"wkt": "POLYGON ((126.984159 37.553060, 126.982984 37.555564, ...))",
"yyyymm": "202506"
},
{
"success": true,
"type": "hd",
"code": "4111760000",
"name": "광교1동",
"wkt": "POLYGON ((127.062152 37.296292, 127.065016 37.291754, ...))",
"yyyymm": "202506"
}
]
```

### 응답 필드 설명 (배열)

필드
타입
설명

success
boolean
요청 성공 여부

type
string
영역 유형 ("h1", "h23", "hd")

code
string
행정구역 코드

name
string
행정구역 이름

wkt
string
WKT 형식의 지오메트리 정보 (행정구역 경계)

yyyymm
string
데이터 기준 년월 (YYYYMM 형식)

### 영역 유형 설명

- **h1**: 광역시도 (예: 11(서울특별시), 41(경기도))
- **h23**: 시군구 (예: 종로구(11140), 수원시 영통구(41117))
- **hd**: 행정동 (예: 명동(1114055000), 영통동(4111760000))

#### 활용 팁

반환된 WKT 형식의 지오메트리 정보는 GIS 소프트웨어나 지도 라이브러리에서 직접 사용할 수 있습니다.

특정 시점의 행정구역 경계가 필요한 경우 yyyymm 파라미터를 사용하여 과거 데이터를 조회할 수 있습니다.

행정표준코드관리시스템(https://www.code.go.kr) 에서 전체 행정표준코드를 조회하고 다운로드할 수 있습니다.

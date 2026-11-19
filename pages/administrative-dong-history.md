# 행정동 이력

지정된 좌표를 기준으로 행정동 이력 정보를 조회합니다.

GET /hd_history

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

yyyymm
string
아니오
조회할 년월 (YYYYMM 형식, 예: 202306). 지정하지 않으면 전체 이력 조회

token
string
예
API 인증 토큰

### 요청 예제

```
GET https://geocode-api.gimi9.com/hd_history?x=127.075074&y=37.143834&token=YOUR_API_TOKEN
```

### 응답 예제

```
[
{
"hd_cd": "4137052300",
"hd_nm": "대원1동",
"hd_en": "Daewon1(il)-dong",
"from_yyyymm": "202312",
"to_yyyymm": "202504"
},
{
"hd_cd": "4137057000",
"hd_nm": "대원동",
"hd_en": "Daewon-dong",
"from_yyyymm": "202305",
"to_yyyymm": "202311"
}
]
```

### 응답 필드 설명

필드
타입
설명

hd_cd
string
행정동 코드 (10자리 행정표준 코드)

hd_nm
string
행정동 한글 이름 (예: 대원1동)

hd_en
string
행정동 영어 이름 (예: Daewon1(il)-dong)

from_yyyymm
string
시작 년월 (YYYYMM 형식, 예: 202312)

to_yyyymm
string
종료 년월 (YYYYMM 형식, 예: 202504)

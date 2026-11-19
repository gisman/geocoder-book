# API 사용법

API는 주소를 좌표로 변환하거나 좌표에서 주소를 찾고, 행정구역 형상·행정동 이력·토큰 사용량을 조회합니다. 모든 엔드포인트의 기본 주소는 `https://geocode-api.gimi9.com`입니다.

## 인증

회원가입 시 발급되는 API 토큰은 프로필에서 확인합니다. 인증되지 않은 요청은 거부됩니다. 토큰을 URL 쿼리의 `token` 파라미터로 전달하거나, 외부에 URL이 노출되는 경우 HTTP 헤더 `Authorization: YOUR_API_TOKEN`으로 전달하세요. 토큰은 비밀번호처럼 안전하게 보관하고 공개하지 마세요.

## 주소 지오코딩

### `GET /geocode`

주소를 줄바꿈(`\n`)으로 구분한 문자열로 전달합니다. 최대 3,000건을 처리할 수 있습니다. 줄바꿈은 URL에서 `%0A`로 인코딩되며, 공백 및 특수 문자도 URL 인코딩해야 합니다.

| 파라미터 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `q` | string | 예 | 주소 하나 이상. 여러 주소는 줄바꿈으로 구분 |
| `token` | string | 예 | API 인증 토큰(또는 Authorization 헤더 사용) |

```bash
curl --get 'https://geocode-api.gimi9.com/geocode' \
  --data-urlencode $'q=서울특별시 송파구 송파대로8길 10\n김제 온천길 37' \
  --data-urlencode 'token=YOUR_API_TOKEN'
```

```python
import requests

response = requests.get(
    "https://geocode-api.gimi9.com/geocode",
    params={
        "q": "서울특별시 송파구 송파대로8길 10\n김제 온천길 37",
        "token": "YOUR_API_TOKEN",
    },
)
print(response.json())
```

### `POST /geocode`

대량의 주소는 POST 요청 본문에 JSON 배열로 전달합니다. GET과 동일한 응답 형식을 사용하며, GET보다 많은 주소를 한 번에 처리할 수 있습니다.

```bash
curl 'https://geocode-api.gimi9.com/geocode' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: YOUR_API_TOKEN' \
  --data-raw '{"q":["서울특별시 송파구 송파대로8길 10","김제 온천길 37"]}'
```

```python
import requests

response = requests.post(
    "https://geocode-api.gimi9.com/geocode",
    headers={"Authorization": "YOUR_API_TOKEN"},
    json={"q": ["서울특별시 송파구 송파대로8길 10", "김제 온천길 37"]},
)
print(response.json())
```

응답에는 전체 처리 시간과 요청·성공·실패 건수, 결과 배열이 포함됩니다.

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `total_time` | float | 총 처리 시간(초) |
| `total_count` | integer | 요청 주소 수 |
| `success_count` | integer | 성공한 주소 수 |
| `hd_success_count` | integer | 성공한 행정동 수 |
| `fail_count` | integer | 실패한 주소 수 |
| `results` | array | 주소별 결과 |

각 `results` 객체에는 입력 주소(`inputaddr`), 성공 여부(`success`), 오류 메시지(`errmsg`), 주소 유형(`addressCls`), 경도 `x_axis`, 위도 `y_axis`(EPSG:4326), 우편번호(`z`), 광역시도·시군구·행정동의 이름과 코드, 도로명(`rm`), 행정동 이력(`hd_history`), 통계청 코드, 건물 관리 번호(`bld_mgt_no`), 건물명(`bm`)이 포함될 수 있습니다.

## 역 지오코딩

### `GET /reverse_geocode`

경도와 위도를 주소로 변환합니다. 도로명 주소와 지번 주소를 반환하며 도로명 주소가 없으면 지번 주소만 반환됩니다. 좌표계는 EPSG:4326입니다.

| 파라미터 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `x` | float | 예 | 경도 |
| `y` | float | 예 | 위도 |
| `token` | string | 예 | API 인증 토큰 |

```bash
curl 'https://geocode-api.gimi9.com/reverse_geocode?x=127.1146829&y=37.5138498&token=YOUR_API_TOKEN'
```

응답의 `road_addr`(도로명 주소가 있는 경우), `jibun_addr`(지번 주소), `success`(전체 요청 성공 여부) 객체/필드에는 주소(`address`), 데이터 기준 년월(`yyyymm`), 성공 여부(`success`), WKT 형상(`geom`), 오류 메시지(`errmsg`)가 포함됩니다. 도로명 주소 객체에는 건물 관리 번호 `ADR_MNG_NO`, 지번 주소 객체에는 PNU `PNU`가 추가됩니다.

## 행정구역 형상 검색

### `GET /region`

행정구역 코드로 광역시도, 시군구, 행정동 형상을 조회합니다. `yyyymm`을 지정하면 해당 시점의 경계를 조회하며, 결과 형상은 WKT 형식입니다.

| 파라미터 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `type` | string | 예 | 영역 유형: `h1`(광역시도), `h23`(시군구), `hd`(행정동). 기본값은 `hd` |
| `code` | string | 예 | 행정구역 코드. 여러 코드는 쉼표로 구분 |
| `yyyymm` | string | 아니오 | 조회 기준 년월(YYYYMM). 생략하면 현재 기준 |
| `token` | string | 예 | API 인증 토큰 |

```bash
curl 'https://geocode-api.gimi9.com/region?type=hd&code=1114055000&yyyymm=202506&token=YOUR_API_TOKEN'
```

응답은 영역 결과의 배열입니다.

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `success` | boolean | 요청 성공 여부 |
| `type` | string | 영역 유형(`h1`, `h23`, `hd`) |
| `code` | string | 행정구역 코드 |
| `name` | string | 행정구역 이름 |
| `wkt` | string | WKT 형식의 행정구역 경계 |
| `yyyymm` | string | 데이터 기준 년월(YYYYMM) |

코드 예: `h1` 광역시도(서울 `11`, 경기 `41`), `h23` 시군구(종로구 `11140`), `hd` 행정동(명동 `1114055000`). 필요한 과거 경계는 `yyyymm`으로 지정합니다. 전체 행정표준코드는 [행정표준코드관리시스템](https://www.code.go.kr)에서 확인할 수 있습니다.

## 행정동 이력

### `GET /hd_history`

좌표를 기준으로 행정동 변경 이력을 조회합니다. `yyyymm`을 생략하면 전체 이력을 반환합니다.

| 파라미터 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `x` | float | 예 | 경도(EPSG:4326) |
| `y` | float | 예 | 위도(EPSG:4326) |
| `yyyymm` | string | 아니오 | 조회 년월(YYYYMM) |
| `token` | string | 예 | API 인증 토큰 |

```text
GET https://geocode-api.gimi9.com/hd_history?x=127.075074&y=37.143834&token=YOUR_API_TOKEN
```

응답은 이력 배열입니다. 각 항목은 10자리 행정표준 코드 `hd_cd`, 한글명 `hd_nm`, 영문명 `hd_en`, 시작 년월 `from_yyyymm`, 종료 년월 `to_yyyymm`을 포함합니다.

## 토큰 통계

### `GET /token/stats`

| 파라미터 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `token` | string | 예 | 통계를 조회할 API 토큰 |

```text
GET https://geocode-api.gimi9.com/token/stats?token=YOUR_API_TOKEN
```

응답에는 총 요청 수(`total_requests`), 지난달 요청 수(`last_month_requests`), 월간 할당량과 잔여량(`monthly_quota`, `remaining_quota`), 일일 할당량·요청 수·잔여량(`daily_quota`, `daily_requests`, `remaining_daily_quota`), 마지막 요청 시각(`last_request_date`, ISO 8601)이 포함됩니다.

## Swagger 및 Pandas

[Swagger API 문서](https://geocode-api.gimi9.com/docs)에서 엔드포인트, 파라미터와 응답 형식을 확인하고 브라우저에서 요청을 테스트할 수 있습니다. TOKEN 없이 테스트할 수도 있으며, 실제 토큰을 입력하면 실시간 결과를 확인할 수 있습니다.

Pandas와 함께 사용하는 [Colab Notebook 예제](https://colab.research.google.com/drive/1Er7h2DsWy3cnB28xXfX78iO2v2wDnzUV?usp=sharing)가 있습니다. Python 패키지는 다음과 같이 설치합니다.

```bash
pip install gimi9-geocoder
```

API 호출에는 실제 API 토큰을 사용하세요. 예제의 `DEMO_TOKEN`은 사용량이 제한된 데모용 토큰입니다.

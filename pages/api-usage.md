# API 사용법

지오코더 API를 사용하여 주소-좌표 변환, 행정구역 검색 등의 기능을 프로그래밍 방식으로 활용할 수 있습니다.

## 주요 API 엔드포인트

### 주소 지오코딩
주소를 위도/경도 좌표로 변환합니다.

- **GET** `/geocode` - 소수의 주소 지오코딩 (최대 3,000건)
- **POST** `/geocode` - 대량의 주소 지오코딩

[주소 지오코딩 API 문서](./address-geocoding.md)

### 역 지오코딩
위도/경도 좌표를 주소로 변환합니다.

- **GET** `/reverse_geocode` - 좌표를 주소로 변환

[역 지오코딩 API 문서](./reverse-geocoding.md)

### 행정구역 검색
주어진 코드 또는 좌표로 행정구역 형상을 조회합니다.

- **GET** `/region` - 코드로 행정구역 형상 검색
- **GET** `/region_by_coordinate` - 좌표로 행정구역 형상 검색

[코드로 행정구역 검색](./search-administrative-division-by-code.md)
[좌표로 행정구역 검색](./search-administrative-division-by-coordinate.md)

### 행정동 이력
지정된 좌표의 행정동 변경 이력을 조회합니다.

- **GET** `/hd_history` - 행정동 이력 조회

[행정동 이력 API 문서](./administrative-dong-history.md)

### 토큰 통계
API 토큰의 사용량 통계를 조회합니다.

- **GET** `/token/stats` - 토큰 사용량 통계

[토큰 통계 API 문서](./token-statistics.md)

## 인증

모든 API 요청은 토큰 기반 인증을 사용합니다.

[인증 방법](./authentication.md)

## 사용 예시

### Python

```python
import requests

# 토큰 설정
token = "YOUR_API_TOKEN"

# 주소 지오코딩
url = "https://geocode-api.gimi9.com/geocode"
params = {
    "q": "서울특별시 송파구 송파대로8길 10",
    "token": token
}
response = requests.get(url, params=params)
print(response.json())
```

### cURL

```bash
curl 'https://geocode-api.gimi9.com/geocode?q=서울특별시 송파구 송파대로8길 10&token=YOUR_API_TOKEN'
```

## 베이스 URL

```
https://geocode-api.gimi9.com
```

## 응답 형식

모든 API 응답은 JSON 형식입니다. 성공 시 HTTP 200 상태 코드를 반환하며, 각 엔드포인트의 응답 필드는 해당 API 문서를 참조하세요.

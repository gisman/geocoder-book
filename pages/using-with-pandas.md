# Pandas와 함께 사용하기

지오코더 라이브러리를 사용하면 Pandas DataFrame과 함께 대량의 주소 지오코딩을 프로그래밍 방식으로 처리할 수 있습니다.

## 설치 방법

pip를 사용하여 Gimi9 Geocoder를 설치할 수 있습니다:

```bash
pip install gimi9-geocoder
```

## 라이브러리 소개

Gimi9 Geocoder 라이브러리는 Python에서 주소-좌표 변환 및 역지오코딩을 쉽게 수행할 수 있도록 제공됩니다.

### 주요 기능

- **주소 지오코딩**: 주소를 위도/경도 좌표로 변환
- **역 지오코딩**: 위도/경도 좌표를 주소로 변환
- **대량 처리**: Pandas DataFrame을 직접 사용하여 대량의 데이터 처리
- **API 통합**: REST API를 통한 원격 지오코딩

## 기본 사용법

### 1. 단일 주소 지오코딩

```python
import requests

# API 호출
url = "https://geocode-api.gimi9.com/geocode"
params = {
    "q": "서울특별시 송파구 송파대로8길 10",
    "token": "YOUR_API_TOKEN"
}
response = requests.get(url, params=params)
result = response.json()

print(result)
```

### 2. 여러 주소 지오코딩 (대량)

```python
import requests
import json

# 여러 주소를 한 번에 처리
addresses = [
    "서울특별시 송파구 송파대로8길 10",
    "서울특별시 강남구 테헤란로 123",
    "서울특별시 마포구 월드컵로 200"
]

url = "https://geocode-api.gimi9.com/geocode"
params = {"token": "YOUR_API_TOKEN"}

results = []
for address in addresses:
    params["q"] = address
    response = requests.get(url, params=params)
    results.append(response.json())

print(results)
```

## Pandas DataFrame 활용

### 1. DataFrame 생성

```python
import pandas as pd

# 주소 데이터를 포함한 DataFrame 생성
df = pd.DataFrame({
    'name': ['위치1', '위치2', '위치3'],
    'address': [
        '서울특별시 송파구 송파대로8길 10',
        '서울특별시 강남구 테헤란로 123',
        '서울특별시 마포구 월드컵로 200'
    ]
})

print(df)
```

### 2. DataFrame에 지오코딩 결과 추가

```python
import pandas as pd
import requests

def geocode_address(address, token):
    """주소를 지오코딩하여 좌표 반환"""
    url = "https://geocode-api.gimi9.com/geocode"
    params = {"q": address, "token": token}
    response = requests.get(url, params=params)
    result = response.json()
    
    if result.get('x') and result.get('y'):
        return result['x'], result['y'], 'success'
    else:
        return None, None, 'failed'

# DataFrame에 지오코딩 결과 추가
df[['longitude', 'latitude', 'status']] = df['address'].apply(
    lambda addr: pd.Series(geocode_address(addr, 'YOUR_API_TOKEN'))
)

print(df)
```

### 3. 역 지오코딩

```python
import pandas as pd
import requests

def reverse_geocode(lat, lon, token):
    """좌표를 역지오코딩하여 주소 반환"""
    url = "https://geocode-api.gimi9.com/reverse_geocode"
    params = {
        "y": lat,
        "x": lon,
        "token": token
    }
    response = requests.get(url, params=params)
    result = response.json()
    
    if 'address' in result:
        return result['address']
    else:
        return None

# 좌표 데이터가 포함된 DataFrame
coord_df = pd.DataFrame({
    'latitude': [37.5138498, 37.4979554, 37.5505189],
    'longitude': [127.1146829, 127.0276005, 126.9522180]
})

# 역지오코딩 결과 추가
coord_df['address'] = coord_df.apply(
    lambda row: reverse_geocode(row['latitude'], row['longitude'], 'YOUR_API_TOKEN'),
    axis=1
)

print(coord_df)
```

### 4. 결과 필터링 및 처리

```python
# 성공한 지오코딩만 필터링
success_df = df[df['status'] == 'success'].copy()

# 좌표 범위로 필터링 (서울 지역만)
seoul_df = df[(df['latitude'] >= 37.4) & (df['latitude'] <= 37.6) & 
              (df['longitude'] >= 126.7) & (df['longitude'] <= 127.3)]

# 결과를 CSV로 저장
df.to_csv('geocoded_results.csv', index=False, encoding='utf-8')

# 결과를 Excel로 저장
df.to_excel('geocoded_results.xlsx', index=False)
```

## 고급 사용법

### 1. 대량 CSV 파일 처리

```python
import pandas as pd
import requests
from tqdm import tqdm  # 진행 상황 표시

# CSV 파일 읽기
df = pd.read_csv('addresses.csv')

def geocode_batch(addresses, token, batch_size=100):
    """배치 단위로 지오코딩 처리"""
    results = []
    
    for i in tqdm(range(0, len(addresses), batch_size)):
        batch = addresses[i:i + batch_size]
        # API를 배치로 호출하거나 각각 호출
        for addr in batch:
            url = "https://geocode-api.gimi9.com/geocode"
            params = {"q": addr, "token": token}
            response = requests.get(url, params=params)
            results.append(response.json())
    
    return results

# 처리 실행
results = geocode_batch(df['address'].tolist(), 'YOUR_API_TOKEN')

# 결과를 DataFrame에 추가
result_df = pd.DataFrame(results)
df = pd.concat([df, result_df], axis=1)
```

### 2. 에러 처리 및 재시도

```python
import pandas as pd
import requests
import time

def geocode_with_retry(address, token, max_retries=3):
    """재시도 로직이 포함된 지오코딩"""
    for attempt in range(max_retries):
        try:
            url = "https://geocode-api.gimi9.com/geocode"
            params = {"q": address, "token": token}
            response = requests.get(url, params=params, timeout=10)
            
            if response.status_code == 200:
                return response.json()
            elif response.status_code == 429:  # Rate limit
                time.sleep(2 ** attempt)  # 지수 백오프
                continue
            else:
                return {'error': f'HTTP {response.status_code}'}
        except requests.exceptions.RequestException as e:
            if attempt == max_retries - 1:
                return {'error': str(e)}
            time.sleep(2 ** attempt)
    
    return {'error': 'Max retries exceeded'}

# DataFrame에 적용
df['result'] = df['address'].apply(
    lambda addr: geocode_with_retry(addr, 'YOUR_API_TOKEN')
)
```

### 3. 지오코딩 결과 분석

```python
# 성공률 계산
success_count = df['status'].value_counts().get('success', 0)
total_count = len(df)
success_rate = (success_count / total_count) * 100

print(f"성공: {success_count}/{total_count} ({success_rate:.2f}%)")

# 지역별 분포
df['district'] = df['address'].str.extract(r'(\S+구)')
district_stats = df.groupby('district').size().sort_values(ascending=False)
print("\n지역별 분포:")
print(district_stats)

# 좌표 범위 확인
print(f"\n위도 범위: {df['latitude'].min():.4f} ~ {df['latitude'].max():.4f}")
print(f"경도 범위: {df['longitude'].min():.4f} ~ {df['longitude'].max():.4f}")
```

## 주의사항

**API 토큰 보안:**
- API 호출 시 실제 API 토큰을 사용하세요
- 토큰은 환경 변수로 관리하세요
- GitHub 등 공개 저장소에 토큰을 포함하지 마세요

```python
import os

# 환경 변수에서 토큰 읽기
token = os.getenv('GEOCODER_API_TOKEN')
```

**API 호출 제한:**
- 무료 계정: 월 3,000,000건
- Pro 계정: 월 100,000,000건
- 대량 요청 시 요청 간격을 두어 처리하세요

## 샘플 Notebook

[Google Colab Notebook 샘플](https://colab.research.google.com/drive/1Er7h2DsWy3cnB28xXfX78iO2v2wDnzUV?usp=sharing)에서 더 많은 예제를 확인할 수 있습니다.

## 추가 리소스

- [API 문서](./api-usage.md) - API 엔드포인트 명세
- [인증](./authentication.md) - API 토큰 관리 방법
- [주소 지오코딩](./address-geocoding.md) - 주소 지오코딩 API 상세 문서
- [역 지오코딩](./reverse-geocoding.md) - 역 지오코딩 API 상세 문서

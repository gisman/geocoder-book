# 토큰 통계

API 토큰의 사용량 통계를 조회합니다.

GET /token/stats

### 요청 파라미터

파라미터
타입
필수
설명

token
string
예
통계를 조회할 API 토큰

### 요청 예제

```
GET https://geocode-api.gimi9.com/token/stats?token=YOUR_API_TOKEN
```

### 응답 예제

```
{
"total_requests": 288945,
"last_month_requests": 949,
"monthly_quota": 100000000,
"remaining_quota": 99999051,
"daily_quota": 10000000,
"daily_requests": 3,
"remaining_daily_quota": 9999997,
"last_request_date": "2025-06-03T09:54:20.450054+09:00"
}
```

### 응답 필드 설명

필드
타입
설명

total_requests
integer
총 요청 수

last_month_requests
integer
지난 달 요청 수

monthly_quota
integer
월간 할당량

remaining_quota
integer
남은 월간 할당량

daily_quota
integer
일일 할당량

daily_requests
integer
오늘 요청 수

remaining_daily_quota
integer
남은 일일 할당량

last_request_date
string
마지막 요청 날짜 (ISO 8601 형식)

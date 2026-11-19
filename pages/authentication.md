# 인증

모든 API 요청은 토큰 기반 인증을 사용합니다. 회원 가입시 토큰이 발급됩니다. [내 프로필](/profile/)에서 발급된 토큰을 확인하세요.

토큰은 비밀번호와 같이 안전하게 보관하세요. 토큰에 일일 사용량과 월간 사용량이 할당됩니다.

토큰을 API 요청 시 전달해야 합니다. 인증되지 않은 요청은 거부됩니다.

예: `?token=YOUR_API_TOKEN`

외부에 URL이 노출되는 서비스에서 API를 호출할 때는 token parameter 대신 HTTP 헤더에 `Authorization: YOUR_API_TOKEN` 형식으로 전달할 수 있습니다.

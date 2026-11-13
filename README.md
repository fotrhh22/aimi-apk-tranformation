# AIMI · Android 전환용 소스

토스에 배포한 **AIMI**를 독립 Android 앱으로 전환하기 위한 동료 인계 저장소입니다.
기존 화면·게임·추천·서버 로직을 재사용하고, Capacitor와 Android 인증을 추가하는 것이 목표입니다.

**현재는 소스 인계 단계입니다. APK/AAB 또는 Capacitor Android 프로젝트가 구현된 상태는 아닙니다.**

- 원본: [fotrhh22/aimi-online](https://github.com/fotrhh22/aimi-online)
- 기준일: 2026-11-13. 원본의 최신 로컬 수정까지 포함한 선별 스냅샷입니다.
- 이 저장소를 수정해도 토스 운영 앱과 자동 동기화되지 않습니다.

## 포함된 기능

- 홈·출석·오늘의 팁·EP, 3종 미니게임
- 10일 이미지 챌린지·키워드 판정·일별 해설
- 정원·아이템·캐릭터 상점, 친구 요청·수락·방문
- Gemini 프롬프트 개선과 텍스트·이미지·영상 모델 추천
- Fastify API, 공유 Zod 계약, 게임 시뮬레이션·추천 엔진, Supabase migration
- 실제 UI 에셋·폰트·챌린지 이미지와 콘텐츠 데이터

## 로컬에서 확인하기

Node.js 22.12 이상과 npm을 사용합니다. 저장소 루트에서 실행하세요.

```sh
npm ci
AIMI_AI_MODE=fixture npm run dev
```

웹 화면: `http://127.0.0.1:5180` · 로컬 API: `http://127.0.0.1:8081`

개발용 API는 메모리 상태와 예시 AI 응답을 사용합니다. 원격 DB·토스 인증서·실제 Gemini 키 없이 확인할 수 있습니다. 기본적으로 API를 재시작하면 개발 기록이 초기화됩니다. 실제 토스 계정이나 운영 데이터와 연결되지 않습니다.

```sh
npm run typecheck
npm run build
npm test
```

`build`는 기존 웹/API 빌드이며 APK/AAB를 만들지 않습니다. `npm run build:web-demo`는 종료 안내만 만드는 옛 명령이므로 Android 빌드에 사용하지 마세요.

## Android 전환에서 할 일

1. Capacitor와 Android 프로젝트 추가, 앱 ID·아이콘·서명 설정.
2. Android 실행 환경 추가. 현재 런타임은 토스/로컬/이전 웹 데모만 구분합니다.
3. **토스 SDK·TDS 제거 또는 Android 번들에서 완전 분리.** 독립 WebView에서 그대로 실행하면 안 됩니다.
4. 토스 익명 식별자 대신 Android용 인증 도입. 서버에서 검증한 계정을 기존 내부 사용자 UUID에 연결합니다.
5. 상단 내비게이션·시스템 뒤로가기·안전 영역·키보드·공유·클립보드·저장소 연결.
6. 별도 개발 서버/DB에서 실제 AI·EP·소셜 검증 후 서명 AAB 생성 및 Play Console 테스트.

UI와 게임·보상 규칙은 유지합니다. 기존 토스 사용자의 기록은 Android 계정으로 자동 이전되지 않습니다.

## 구조

| 경로 | 역할 |
| --- | --- |
| `apps/miniapp/` | React 화면·게임 렌더링·토스 연동 코드·공개 에셋 |
| `apps/api/` | Fastify API·인증·DB·거래·Gemini |
| `packages/` | contracts / game-core / recommender |
| `content/` | 챌린지 정답·모델 목록·일정·정책. 서버용 정답은 공개 앱 에셋에 넣지 않기 |
| `supabase/migrations/` | 스키마 변경 이력. 운영 DB에 일괄 재실행 금지 |
| `tools/` | 개발·검증·콘텐츠·모델 관리 도구 |
| `docs/ANDROID_HANDOFF.md` | 교체할 코드 위치·권장 작업 순서·환경변수 |
| `docs/SOURCE_SNAPSHOT.json` | 인계한 원본 파일 목록·해시 |
| `docs/reference/` | 기존 정책 참고. 과거 토스 전용 내용은 Android에서 재검토 |

## 키·서버·개인정보

실제 환경변수·인증서·서명키·사용자 DB 데이터는 포함하지 않았습니다. 설정 예시는 `apps/api/.env.example`, `apps/miniapp/.env.example`을 참고하세요. Gemini 키는 서버에서만 사용합니다.

운영 Render/Supabase를 동료 개발에 바로 연결하지 마세요. 별도 환경에서 준비하고 운영 변경은 소유자와 협의합니다. 종료된 Vercel 데모는 다시 열지 않습니다.

Play 제출 전 개인정보처리방침 공개 URL·데이터 보안 설문·계정 삭제 경로와 당시 Google Play 정책을 확인해야 합니다.

## 인계본 검증

2026-11-13 별도 복사본에서 `npm ci`, `npm run build`, `npm test`를 실행했고 기존 테스트 **118개가 통과**했습니다. Android 실기기·Capacitor·AAB 검증은 아직 하지 않았습니다. 모델 검증 테스트에 필요한 공개 원본/공급사 근거 스냅샷도 포함했습니다.

# Langfuse + aim-intelligence 통합 실전 구현 계획 (복사/붙여넣기 기반)

---

## 1. aim-intelligence 앱 생성 및 환경 일치

1.1. **Next.js 앱 생성**

- 터미널에서 아래 명령어 실행:
  ```sh
  npx create-next-app apps/aim-intelligence --typescript --eslint
  ```
- 생성 옵션은 Langfuse(web)와 동일하게 선택

  1.2. **설정 파일 일치**

- `apps/web/package.json`, `tsconfig.json`, `.eslintrc`, `.prettierrc` 등 주요 설정 파일을
  `apps/aim-intelligence/`로 복사(덮어쓰기)
- Next.js, React, TypeScript, ESLint 등 버전도 Langfuse와 동일하게 맞춤
- 필요시 import alias, baseUrl 등도 동일하게 설정

  1.3. **의존성 동기화**

- 루트에서 아래 명령어 실행:
  ```sh
  pnpm install
  ```
- 의존성 충돌/누락이 없는지 확인

---

## 2. Langfuse OSS 코드 복사/붙여넣기 방식으로 재사용

2.1. **필요한 컴포넌트/로직 복사**

- 예시:
  - `web/src/features/auth/components/LoginForm.tsx` → `aim-intelligence/src/features/auth/components/LoginForm.tsx`
  - `web/src/features/org/components/OrgCreateForm.tsx` → `aim-intelligence/src/features/org/components/OrgCreateForm.tsx`
- 관련 context/provider, 스타일, 유틸리티, API 호출 코드 등도 함께 복사
- 복사한 파일에는 반드시 "원본 경로/버전" 주석 추가

  2.2. **경로/의존성 수정**

- import 경로, 스타일, API endpoint 등 aim-intelligence에 맞게 수정

---

## 3. NextAuth 인증 로직 복사

3.1. `web/pages/api/auth/[...nextauth].ts` 및 관련 인증 설정/로직 파일을
`aim-intelligence/pages/api/auth/[...nextauth].ts`로 복사
3.2. provider, Prisma adapter, callbacks 등 참조 파일도 함께 복사
3.3. import 경로, 환경변수, DB 연결 등 aim-intelligence에 맞게 수정

---

## 4. DB/환경변수 공유

4.1. `.env` 파일을 루트에 두고, DB/NextAuth/Provider 관련 환경변수 공통 관리
4.2. 두 앱 모두 같은 DB, Redis, Minio, Clickhouse 등 인프라를 사용하도록 설정

---

## 5. Dockerfile 및 docker-compose 설정

5.1. `apps/aim-intelligence/Dockerfile` 작성 (langfuse와 동일하게)
5.2. `docker-compose.yml`, `docker-compose.dev.yml`, `docker-compose.build.yml`에 aim-intelligence 서비스 추가
5.3. 포트 충돌 방지(예: langfuse는 3000, aim-intelligence는 4000 등)
5.4. 환경변수, depends_on 등 인프라 서비스 연동

---

## 6. 테스트 및 검증

6.1. aim-intelligence에서 로그인/회원가입/조직/프로젝트 생성이 정상 동작하는지 확인
6.2. 세션/회원/조직/프로젝트 정보가 langfuse와 완전히 공유되는지 검증

---

## 7. 문서화 및 유지보수 전략

7.1. 어떤 파일/로직/컴포넌트를 Langfuse에서 복사했는지 명확히 주석/문서화
7.2. Langfuse OSS가 업데이트될 때, 복사한 코드와의 차이(diff)를 수동으로 비교/동기화
7.3. breaking change 발생 시, aim-intelligence 쪽 코드를 직접 수정

---

## 8. Best Practice

- 복사한 코드에는 반드시 "원본 경로/버전" 주석 추가
- 정기적으로 Langfuse OSS와의 diff/동기화 작업 수행
- 복사한 코드가 많아질수록 유지보수/업데이트 부담이 커질 수 있으니, 꼭 필요한 부분만 복사

---

**이 문서를 따라가면, 코드 공유(import) 없이도 독립적으로 개발이 가능하지만, Langfuse OSS 업데이트 반영은 수동으로 해야 하므로 정기적인 diff/동기화와 문서화가 매우 중요합니다!**

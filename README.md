# Learn How to Use Supabase 

Connecting quiz application to a database

+ Supabase: This is a cloud platform that provides a database and backend services for web and mobile applications.
+ SQL (Structured Query Language): It is a language used to create, store, retrieve, and manage data in a database.


| Component | Role in the Quiz App |
|---|---|
| **HTML** | What the student sees |
| **CSS** | How the quiz looks |
| **JavaScript** | What happens when the student clicks an answer |
| **Supabase database** | Where the responses are stored |
| **SQL** | Instructions that tell the database how to organize and control those responses |

## Before You Get Started

+ GenAI account: ChatGPT, Gemini, Claude, etc.
+ Github account
+ Supabase account: [https://supabase.com](https://supabase.com)
  
## What Do We Do Today: Flow
영어 단어 4지선다 3개 문항 퀴즈앱 > Github Pages Deploy > 사용자 테스트 > Supabase에 정오답 저장 > 

① 데이터 설계 요청(AI) → ② HTML·SQL 생성(AI) → ③ Supabase 프로젝트 생성 → ④ SQL 실행 → ⑤ html 파일에 키 입력 → ⑥ GitHub 배포 → ⑦ 앱 테스트 및 Supabase에 데이터 기록 확인

# Step by Step Guidelines

## STEP 1: GenAI
### APP 설계 using AI prompt
Copy the following and use it as prompt on your AI

```
영어 단어 객관식 퀴즈 앱을 만들려고 해.
 
[앱 구상]
- 영어 단어 3문제, 각 문제는 4지선다형
- 한 문제에 한 번만 답을 선택할 수 있음
- 즉시 정오답 피드백과 마지막 총점 제공
- GitHub Pages에 배포
- Supabase에 문제별 응답 저장
- 이름, 이메일 등 개인정보는 수집하지 않음
 
이 앱에서 나중에 응답을 확인하고 분석하려면 어떤 데이터를 저장하는 것이 좋을까?
가능한 한 간단한 테이블 하나로 제안해 줘.
 
컬럼 이름, 자료형, 예시 값, 저장 이유를 표로 보여 주고,
한 번의 퀴즈 응시에서 나온 3개 응답을 묶는 방법과
각 문제의 응답 시간을 기록하는 방법도 설명해 줘.
 
아직 HTML이나 SQL 코드는 작성하지 마.
```

## STEP 2: GenAI
### Ask AI to create html and SQL
Copy the following and use it as prompt on your AI

```
좋아. 이제 데이터 구조를 확정하고 앱과 SQL을 함께 만들어 줘.
앞에서 논의한 내용을 참고하되, 이번 수업에서는 아래 구조를 정확히 사용하자.
 
[퀴즈 문제]
1. abundant / 보기: 조용한, 풍부한, 위험한, 느린 / 정답: 풍부한
2. reluctant / 보기: 주저하는, 열정적인, 정확한, 부지런한 / 정답: 주저하는
3. brief / 보기: 강력한, 평범한, 짧은, 불규칙한 / 정답: 짧은
 
[Supabase 테이블: public.quiz_answers]
- id: uuid, primary key, default gen_random_uuid()
- attempt_id: uuid, not null (퀴즈 1회 응시를 묶는 번호)
- question_no: smallint, not null, 1~3
- english_word: text, not null
- chosen_answer: text, not null
- is_correct: boolean, not null
- response_time_ms: integer, not null, 0 이상 (문제 제시 후 첫 선택까지 밀리초)
- created_at: timestamptz, not null, default now()
 
[결과물 A: index.html 전체 코드]
1. HTML, CSS, JavaScript를 하나의 index.html 파일에 작성해 줘.
2. GitHub Pages에서 빌드 도구 없이 실행되게 해 줘.
3. Supabase JavaScript v2 CDN과 window.supabase.createClient()를 사용해 줘.
4. 아래 두 빈칸을 만들어 두어 나중에 내가 값만 바꿀 수 있게 해 줘.
   const SUPABASE_URL = "YOUR_SUPABASE_URL";
   const SUPABASE_PUBLISHABLE_KEY = "YOUR_PUBLISHABLE_KEY";
5. 각 문제의 답을 처음 선택했을 때 quiz_answers 테이블에 INSERT 한 번만 실행해 줘.
6. 퀴즈를 새로 시작할 때 crypto.randomUUID()로 attempt_id를 하나 만들고,
   같은 3문제의 응답에는 같은 attempt_id를 사용해 줘.
   '다시 풀기'를 누르면 새로운 attempt_id를 만들어 줘.
7. performance.now()로 문제별 응답 시간(response_time_ms)을 계산해 줘.
8. 즉시 정오답 피드백, 3점 만점 총점, 다시 풀기 버튼을 제공해 줘.
9. 저장 성공/실패를 표시하되 저장에 실패해도 퀴즈는 계속되게 해 줘.
10. SELECT, UPDATE, DELETE와 insert().select()는 사용하지 마.
11. 로그인과 이름/이메일 수집 기능은 넣지 마.
 
[결과물 B: Supabase SQL Editor에 붙여 넣을 전체 SQL]
1. 위와 정확히 일치하는 public.quiz_answers 테이블을 생성해 줘.
2. question_no 1~3, response_time_ms 0 이상의 CHECK 제약을 포함해 줘.
3. RLS를 활성화해 줘.
4. anon/authenticated의 기존 테이블 권한을 REVOKE하고,
   anon 역할에 INSERT 권한만 GRANT해 줘.
5. anon INSERT만 허용하는 RLS 정책을 만들어 줘.
6. SELECT, UPDATE, DELETE 정책은 만들지 마.
7. secret/service_role 키, 데이터베이스 비밀번호를 요구하지 마.
 
두 결과물을 각각 별도의 코드 블록으로 제시해 줘.
SQL을 index.html 안에 넣지 마.
HTML의 INSERT 컬럼명과 SQL의 컬럼명이 일치하는지 마지막에 확인해 줘.
 
끝으로 (1) Supabase 프로젝트 만들기 → (2) SQL 실행 →
(3) Project URL과 sb_publishable_ 키 찾기 → (4) HTML 빈칸 교체 및
GitHub Pages 배포 → (5) 퀴즈 3문항 응답 후 Table Editor에서 3행 확인
순서로 매우 짧게 안내해 줘.

```

## STEP 3: Github

1. 새로운 repository 'W9-voca-quiz'를 만들고
2. 새 파일 만들기 > 파일이름 'index.html' > 내용은 AI가 만들어준 html 코드를 복사해서 넣기 > Commit Changes
3. 해당 레파지토리에 index.html 파일이 생성되었는지 확인할 것

## STEP 4: Supabase

1. supabase.com 로그인
2. New project > 이름은 'w9-voca-quiz' > 지역: Seoul (timezone related)
3. Password (본인용; Please don't forget your passcode): 주의할 점(비밀번호는 GenAI, Github에 공개하지 않는다)
4. 프로젝트 생성 완료

## STEP 5: Supabase
### SQL Query 실행하기

1. Supabase의 SQL Editor > New query 선택
2. GenAI가 생성한 SQL 코드 붙여 넣기 > Run
3. Table Editor > quiz answers 테이블 확인(처음에는 0행이고 정상임)

## STEP 6: Supabase / Github
### Project URL, Publishable key 연결하기

1. Supabase의 왼쪽 사이드바 > Project Overview에서 다음 두 개의 값을 찾는다
   + const SUPABASE_URL
   + const SUPABASE_PUBLISHABLE_KEY

2. Github repository (w9-voca-quiz)안에 있는 index.html 편집을 연다
3. 코드에서 아래처럼 된 코드줄을 찾아서, "YOUR_SUPABASE_URL", "YOUR_PUBLISHABLE_KEY" 대신 실제 값을 넣는다.

```
const SUPABASE_URL = "YOUR_SUPABASE_URL";
const SUPABASE_PUBLISHABLE_KEY = "YOUR_PUBLISHABLE_KEY";
```

## STEP 7: Github
### App deployment

1. Go to the repository (w9-voca-quiz)
2. 상단에 Settings > Pages
3. Deploy from a branch > main/(root) > Save
4. 배포 완료 후 Visit site를 눌러 앱 열기

## STEP 8: HTML app browser
### APP test

문제를 풀고 저장, 2회 하기 (답을 다르게도 해 보기) > Success message

## STEP 9: Supabase
### Check how the data is stored

1. Supabase > Table Editor > quiz_answers에서 새로운 3행 확인
2. 세 행의 attempt_id가 같은지, response_time_ms가 기록되었는지 확인


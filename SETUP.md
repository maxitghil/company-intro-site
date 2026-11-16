# 회원 기능 · 데이터베이스 설정 안내서

사이트는 **Supabase**(무료)를 데이터베이스로 씁니다. 회원 정보와 문의 내용이 여기에 저장돼요.
아래 5단계는 처음 한 번만 하면 되고, 15분 정도 걸립니다.

---

## 1단계. Supabase 가입하고 프로젝트 만들기

1. https://supabase.com 에 접속해 **Start your project**를 누르고 GitHub 계정으로 가입합니다.
2. **New project**를 누릅니다.
   - Name: `company-intro-site`
   - Database Password: 아무거나 길게 (따로 적어 두세요)
   - Region: **Northeast Asia (Seoul)**
3. **Create new project**를 누르고 1~2분 기다립니다.

## 2단계. 문의 저장용 표 만들기

1. 왼쪽 메뉴에서 **SQL Editor**를 엽니다.
2. 아래 내용을 통째로 복사해 붙여 넣고 **Run**을 누릅니다. "Success"가 나오면 끝입니다.

```sql
-- 문의하기 내용을 저장하는 표
create table public.inquiries (
  id bigint generated always as identity primary key,
  name text not null,
  email text not null,
  message text not null,
  created_at timestamptz not null default now()
);

-- 보안: 방문자는 "쓰기"만 가능, 남의 문의를 "읽기"는 불가
alter table public.inquiries enable row level security;

create policy "누구나 문의 작성 가능"
  on public.inquiries for insert
  to anon, authenticated
  with check (true);
```

> 회원 정보(이메일·비밀번호)는 Supabase가 자동으로 안전하게 관리하므로 따로 표를 만들 필요가 없어요.

## 3단계. 연결 키 두 개 복사하기

1. 왼쪽 아래 **Project Settings → API**(또는 **Data API**)로 갑니다.
2. 두 값을 복사합니다.
   - **Project URL** (예: `https://abcdxyz.supabase.co`)
   - **anon public** 키 (`eyJ...`로 시작하는 긴 글자)

> ⚠️ `service_role` 키는 절대 쓰지 마세요. `anon public` 키는 사이트에 넣어도 안전하도록 만들어진 키입니다.

## 4단계. index.html에 키 붙여 넣기

`index.html` 맨 아래쪽에서 이 부분을 찾아 따옴표 안에 붙여 넣습니다.

```js
const SUPABASE_URL = 'https://abcdxyz.supabase.co';
const SUPABASE_ANON_KEY = 'eyJ...';
```

그다음 GitHub 저장소에 파일을 다시 올리면(같은 업로드 페이지에 끌어다 놓기 → Commit changes) Vercel이 자동으로 다시 배포합니다.

## 5단계. 가입 확인 메일 주소 설정

회원가입하면 확인 메일이 가는데, 그 메일의 링크가 내 사이트로 오도록 설정합니다.

1. Supabase 왼쪽 메뉴 **Authentication → URL Configuration**으로 갑니다.
2. **Site URL**에 Vercel 주소를 넣습니다. (예: `https://company-intro-site.vercel.app`)
3. **Save**를 누릅니다.

> 확인 메일 없이 바로 가입되게 하고 싶다면: **Authentication → Sign In / Providers → Email**에서 **Confirm email**을 끄면 됩니다.

---

## 들어온 문의 확인하는 법

Supabase 왼쪽 메뉴 **Table Editor → inquiries**를 열면 문의가 표로 보입니다.

## 가입한 회원 확인하는 법

**Authentication → Users**에서 가입한 회원 목록을 볼 수 있습니다.

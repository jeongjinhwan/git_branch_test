# ✅ Git `force push` 후 fetch 오류 문제 해결 기록

# 🔍 상황 요약

- `develop` 브랜치를 `origin/release`로 덮기 위해 다음 작업 수행:

```bash
bash
복사편집
git checkout develop
git reset --hard origin/release
git push origin develop --force
```

- 이후, 다른 사용자들이 `git fetch origin` 또는 `git fetch origin +develop` 실행 시 다음 오류 발생:

```
bash
복사편집
! [rejected]        develop -> origin/develop (non-fast-forward)
```

---

## 📌 원인

- `git push --force`로 원격 `develop` 브랜치 히스토리가 바뀜
- 하지만 다른 사용자의 로컬에는 **기존 커밋 기반의 `origin/develop` 참조가 남아 있음**
- Git은 **remote-tracking 브랜치(`origin/develop`)에 대해 non-fast-forward 업데이트를 기본적으로 거부**함

---

## 🛠 해결 방법 (정석 루틴)

### 1. 최신 원격 커밋 ID 조회

```bash
bash
복사편집
git ls-remote origin refs/heads/develop
```

> 결과 예시:
> 
> 
> `abc1234abcdef1234567890abcdef1234567890  refs/heads/develop`
> 

### 2. `origin/develop` 참조 강제로 덮어쓰기

```bash
bash
복사편집
git update-ref refs/remotes/origin/develop abc1234abcdef1234567890abcdef1234567890
```

> ※ 위 커밋 해시는 1단계에서 조회한 값을 그대로 사용
> 

### 3. 로컬 브랜치 초기화

```bash
bash
복사편집
git checkout develop
git reset --hard origin/develop
```

---
# ✅ 요약 명령어 스크립트
```bash
bash
복사편집
# 1. 원격 커밋 해시 확인
git ls-remote origin refs/heads/develop

# 2. 로컬 origin/develop 강제 초기화
git update-ref refs/remotes/origin/develop <원격 커밋 해시>

# 3. 로컬 develop 브랜치 초기화
git checkout develop
git reset --hard origin/develop
```
---
# ✅ 팀원에게 안내 시 참고 문구
> 📢 develop 브랜치가 강제 푸시되어 로컬 fetch가 실패할 수 있습니다.
> 
> 
> 아래 순서로 로컬 브랜치를 재설정해 주세요:
> 

```bash
bash
복사편집
git fetch origin
git checkout develop
git reset --hard origin/develop
```
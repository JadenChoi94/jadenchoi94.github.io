# Jekyll 서버 실행 명령어 가이드

## 기본 명령어

### Jekyll 서버 실행
```bash
bundle exec jekyll serve
```
- 기본 포트: `http://localhost:4000`
- 기본적인 Jekyll 서버 실행

## 자주 사용하는 옵션들

### 1. 실시간 새로고침 (🌟 추천)
```bash
bundle exec jekyll serve --livereload
```
- 파일을 수정하면 브라우저가 자동으로 새로고침
- 개발할 때 가장 편리한 옵션

### 2. 초안 포스트 포함
```bash
bundle exec jekyll serve --drafts
```
- `_drafts` 폴더의 초안 글들도 사이트에 표시
- 작성 중인 글을 미리보기할 때 유용

### 3. 포트 변경
```bash
bundle exec jekyll serve --port 4001
```
- 기본 포트 4000 대신 다른 포트 사용
- 여러 Jekyll 사이트를 동시에 실행할 때 유용

### 4. 호스트 변경 (네트워크 접근 허용)
```bash
bundle exec jekyll serve --host 0.0.0.0
```
- 다른 기기에서도 접근 가능
- 모바일에서 테스트할 때 유용

### 5. 조합 사용 예시
```bash
# 실시간 새로고침 + 초안 포함 + 포트 변경
bundle exec jekyll serve --livereload --drafts --port 4001

# 네트워크 접근 허용 + 실시간 새로고침
bundle exec jekyll serve --host 0.0.0.0 --livereload
```

## 빌드 관련 명령어

### 사이트 빌드만 실행 (서버 실행 안함)
```bash
bundle exec jekyll build
```
- `_site` 폴더에 정적 파일들만 생성
- 배포용 파일 생성시 사용

### 증분 빌드 (빠른 빌드)
```bash
bundle exec jekyll serve --incremental
```
- 변경된 파일만 다시 빌드
- 큰 사이트에서 빌드 시간 단축

## 서버 관리

### 서버 중지
- **Mac/Linux**: `Ctrl + C`
- **Mac**: `Cmd + C`

### 현재 실행 중인 Jekyll 서버 확인
```bash
ps aux | grep jekyll
```

### 특정 포트 사용 중인 프로세스 확인
```bash
lsof -i :4000
```

### Jekyll 서버 강제 종료
```bash
# 프로세스 ID 확인 후
kill -9 [프로세스ID]

# 또는 포트 기반으로 종료
lsof -ti:4000 | xargs kill -9
```

## 개발 환경 설정

### 의존성 설치
```bash
bundle install
```

### Gemfile 업데이트
```bash
bundle update
```

### Jekyll 버전 확인
```bash
jekyll --version
bundle exec jekyll --version
```

## 유용한 팁

### 1. 자주 사용하는 명령어 (추천 조합)
```bash
bundle exec jekyll serve --livereload
```

### 2. 개발 시 권장 설정
- `--livereload`: 실시간 새로고침
- `--drafts`: 초안 글 포함 (필요시)
- `--incremental`: 빠른 빌드 (큰 사이트의 경우)

### 3. 문제 해결
```bash
# 캐시 정리 후 재빌드
bundle exec jekyll clean
bundle exec jekyll serve --livereload

# 완전 재설치
rm -rf _site
rm Gemfile.lock
bundle install
bundle exec jekyll serve --livereload
```

## 배포 관련

### GitHub Pages용 빌드 테스트
```bash
JEKYLL_ENV=production bundle exec jekyll build
```

### 로컬에서 프로덕션 환경 테스트
```bash
JEKYLL_ENV=production bundle exec jekyll serve
```

---

## 📝 참고사항

- Jekyll 서버는 파일 변경을 감지하여 자동으로 재빌드합니다
- `_config.yml` 파일을 수정한 경우 서버를 재시작해야 합니다
- `_site` 폴더는 자동 생성되므로 Git에 추가하지 마세요
- 포트 4000이 사용 중이면 자동으로 다른 포트를 사용합니다

---

*마지막 업데이트: 2025년 9월 17일*

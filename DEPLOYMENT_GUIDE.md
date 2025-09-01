# PyPI 배포 가이드

이 가이드는 `unknown-data` 패키지를 PyPI에 배포하여 전 세계적으로 `pip install unknown-data`로 설치 가능하게 만드는 방법을 설명합니다.

## 현재 상태
- ✅ 로컬 개발 환경에서 완전 동작
- ✅ 패키지 구조 완성 (`src/unknown_data/`)
- ✅ 테스트 36개 모두 통과
- ✅ pyproject.toml 설정 완료
- ❌ PyPI 배포 (아직 미완료)

## 배포 단계

### 1. PyPI 계정 생성
1. [PyPI 웹사이트](https://pypi.org/account/register/) 접속
2. 계정 생성
3. 이메일 인증 완료
4. 2FA (Two-Factor Authentication) 활성화 (권장)

### 2. API 토큰 생성
1. PyPI 로그인 후 [Account Settings](https://pypi.org/manage/account/) 접속
2. "API tokens" 섹션에서 "Add API token" 클릭
3. Scope: "Entire account" 선택 (또는 프로젝트별)
4. 토큰 이름: "unknown-data-deployment"
5. 생성된 토큰 안전하게 저장

### 3. 배포 도구 설치
```bash
# 가상환경 활성화
source venv/bin/activate

# 빌드 및 업로드 도구 설치
pip install build twine
```

### 4. 패키지 이름 충돌 확인
```bash
# PyPI에서 패키지 이름 확인
# 브라우저에서 https://pypi.org/project/unknown-data/ 접속
# 404 오류가 나오면 사용 가능한 이름
```

### 5. pyproject.toml 최종 점검
현재 설정 확인:
```toml
[project]
name = "unknown-data"  # 필요시 변경
version = "0.1.0"
description = "A library for parsing and processing forensic data artifacts."
# ... 기타 설정들
```

### 6. 배포 전 최종 테스트
```bash
# 모든 테스트 통과 확인
python -m pytest

# 패키지 빌드 테스트
python -m build

# 빌드된 파일 확인
ls dist/
# unknown_data-0.1.0-py3-none-any.whl
# unknown_data-0.1.0.tar.gz
```

### 7. TestPyPI에 먼저 배포 (권장)

**중요**: API 토큰은 PyPI와 TestPyPI에서 별도로 생성해야 합니다!

#### API 토큰 설정 방법

1. **TestPyPI 토큰 생성**:
   - [TestPyPI Account Settings](https://test.pypi.org/manage/account/) 접속
   - "API tokens" → "Add API token" 클릭
   - Token name: "unknown-data-test"
   - Scope: "Entire account"
   - 생성된 토큰 복사 (pypi-로 시작)

2. **PyPI 토큰 생성** (실제 배포용):
   - [PyPI Account Settings](https://pypi.org/manage/account/) 접속
   - 같은 방식으로 토큰 생성

#### 인증 설정 방법

**방법 1: 환경 변수 사용 (권장)**
```bash
# TestPyPI 토큰 설정
export TWINE_USERNAME=__token__
export TWINE_PASSWORD=pypi-YOUR_TESTPYPI_TOKEN_HERE

# 업로드
twine upload --repository testpypi dist/*
```

**방법 2: 명령줄에서 직접 입력**
```bash
# 토큰을 직접 입력하며 업로드
twine upload --repository testpypi dist/* --username __token__ --password pypi-YOUR_TOKEN_HERE
```

### 8. 실제 PyPI에 배포
```bash
# 실제 PyPI에 업로드
twine upload dist/*

# 업로드 확인
# https://pypi.org/project/unknown-data/ 접속하여 확인
```

### 9. 설치 테스트
```bash
# 새로운 가상환경에서 테스트
python -m venv test_env
source test_env/bin/activate
pip install unknown-data

# 테스트
python -c "from unknown_data import Category, DataLoader; print('SUCCESS!')"
```

## 배포 후 관리

### 버전 업데이트
1. `pyproject.toml`에서 버전 번호 증가
2. 변경사항을 `CHANGELOG.md`에 기록
3. 새 버전 빌드 및 업로드:
```bash
# 버전 변경 후
rm -rf dist/
python -m build
twine upload dist/*
```

### 자동화 (GitHub Actions)
`.github/workflows/publish.yml` 파일을 생성하여 자동 배포 설정 가능

## 주의사항

1. **패키지 이름**: PyPI에서 유일해야 함
2. **버전 관리**: 한번 업로드된 버전은 삭제/수정 불가
3. **라이선스**: MIT 라이선스 파일 포함됨
4. **보안**: API 토큰을 안전하게 관리
5. **테스트**: 배포 전 반드시 모든 테스트 통과 확인

## 문제 해결

### 패키지 이름 충돌
```bash
# 다른 이름으로 변경
# pyproject.toml에서 name 수정 후 다시 빌드
```

### 업로드 실패
```bash
# 자세한 오류 메시지 확인
twine upload --verbose dist/*
```

### 의존성 문제
```bash
# requirements.txt 확인
pip freeze > requirements.txt
```

## 배포 완료 후 확인 사항

- [ ] PyPI에서 패키지 페이지 정상 표시
- [ ] `pip install unknown-data` 정상 작동
- [ ] 새로운 환경에서 import 테스트 통과
- [ ] README.md의 설치 가이드 업데이트
- [ ] GitHub 저장소에 배포 태그 생성

배포가 완료되면 전 세계 어디서나 `pip install unknown-data`로 패키지를 설치할 수 있습니다!

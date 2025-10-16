# CI/CD Demo Project

Jenkins + Docker를 통한 멀티스테이지 CI/CD 파이프라인 데모 프로젝트입니다.

## 프로젝트 구성

- `server.js`: Express.js 웹 서버
- `package.json`: Node.js 프로젝트 설정 및 의존성
- `public/index.html`: 동적 웹페이지 (API 연동)
- `Dockerfile`: 멀티스테이지 Docker 빌드 설정
- `.dockerignore`: Docker 빌드 제외 파일 설정
- `README.md`: 프로젝트 설명서

## 기술 스택

- **Backend**: Node.js + Express.js
- **Frontend**: Vanilla JavaScript + CSS3
- **Container**: Docker (멀티스테이지 빌드)
- **CI/CD**: Jenkins Pipeline

## 로컬 실행 방법

### 1. 의존성 설치
```bash
npm install
```

### 2. 개발 서버 실행
```bash
npm run dev
```

### 3. 프로덕션 서버 실행
```bash
npm start
```

## Docker 빌드 및 실행

### 1. Docker 이미지 빌드
```bash
docker build -t cicd-demo:latest .
```

### 2. 컨테이너 실행
```bash
docker run -p 3000:3000 -e APP_VERSION=1.0.0 -e BUILD_NUMBER=#001 cicd-demo:latest
```

### 3. 멀티스테이지 빌드 확인
```bash
# 빌드 과정에서 각 스테이지 확인
docker build --progress=plain -t cicd-demo:latest .
```

## CI/CD 파이프라인 테스트

### 수정 가능한 요소들:
- **서버 코드**: `server.js`의 버전, 빌드 번호, 환경 변수
- **프론트엔드**: `public/index.html`의 타이틀, 스타일
- **Docker 설정**: `Dockerfile`의 Node.js 버전, 포트 설정
- **패키지 설정**: `package.json`의 버전, 의존성

### 테스트 시나리오:
1. 코드 수정 후 Git 커밋
2. Jenkins 파이프라인 자동 실행
3. Docker 멀티스테이지 빌드 진행
4. 컨테이너 배포 및 웹페이지 확인

## API 엔드포인트

- `GET /`: 메인 웹페이지
- `GET /api/health`: 서버 상태 확인
- `GET /api/info`: 애플리케이션 정보 (버전, 빌드 번호 등)

## 환경 변수

- `NODE_ENV`: 실행 환경 (development/production)
- `PORT`: 서버 포트 (기본값: 3000)
- `APP_VERSION`: 애플리케이션 버전
- `BUILD_NUMBER`: 빌드 번호

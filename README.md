# 🚀 Jenkins + Kubernetes CI/CD 파이프라인 실습 교재

이 프로젝트는 Jenkins와 Kubernetes를 활용한 현대적인 CI/CD 파이프라인 구축 방법을 단계별로 학습할 수 있는 실습 교재입니다.

## 📚 목차

- [프로젝트 개요](#-프로젝트-개요)
- [학습 목표](#-학습-목표)
- [사전 요구사항](#-사전-요구사항)
- [프로젝트 구조](#-프로젝트-구조)
- [실습 가이드](#-실습-가이드)
- [주요 기능](#-주요-기능)
- [기술 스택](#-기술-스택)
- [문제 해결](#-문제-해결)

## 🎯 프로젝트 개요

이 교재는 다음 내용을 포함합니다:

1. **단일 노드 Kubernetes 클러스터 구축**
2. **Jenkins를 Kubernetes에 배포**
3. **Kubernetes 기반 Jenkins Agent 구성**
4. **Buildah를 활용한 컨테이너 이미지 빌드**
5. **완전한 CI/CD 파이프라인 구현**

## 🎓 학습 목표

- Kubernetes 클러스터 구축 및 관리
- Jenkins를 Kubernetes 환경에 배포
- Jenkins Pipeline을 활용한 자동화된 CI/CD 프로세스
- Buildah를 사용한 컨테이너 이미지 빌드
- Docker Hub를 통한 컨테이너 레지스트리 연동
- 보안 인증서 및 RBAC 관리

## 🔧 사전 요구사항

### 시스템 요구사항
- **OS**: Ubuntu 24.04 LTS
- **CPU**: 최소 4 Core
- **RAM**: 최소 8GB
- **Disk**: 최소 50GB 여유 공간

### 필요 도구
- `curl`, `wget`, `git`
- `kubectl` (설치 과정에서 자동 설치)
- `helm` (설치 과정에서 자동 설치)

## 📁 프로젝트 구조

```
cicd-lab/
├── 02-kubernetes-init/          # Kubernetes 클러스터 초기화
│   ├── kube-init.sh            # 자동화된 클러스터 구축 스크립트
│   └── ingress-test.yaml       # Ingress 테스트용 매니페스트
├── 03-jenkins-install/         # Jenkins 설치 및 설정
│   ├── jenkins-rbac.yaml       # Jenkins RBAC 설정
│   ├── jenkins-values.yaml     # Jenkins Helm 차트 설정
│   └── jenkinsfile             # Kubernetes 통신 테스트 파이프라인
├── 04-jenkins-agent/           # Jenkins Agent 구성
│   └── buildah-jenkinsfile     # Buildah 테스트 파이프라인
├── 05-cicd-pipeline/           # 완전한 CI/CD 파이프라인
│   ├── cicd-demo/              # 데모 애플리케이션
│   │   ├── package.json        # Node.js 프로젝트 설정
│   │   ├── server.js           # Express 서버
│   │   ├── Dockerfile          # 멀티스테이지 빌드
│   │   └── public/
│   │       └── index.html      # 웹 인터페이스
│   └── cicd-demo-jenkinsfile   # 완전한 CI/CD 파이프라인
└── README.md                   # 이 파일
```

## 🚀 실습 가이드

### 1단계: Kubernetes 클러스터 구축

```bash
# 02-kubernetes-init 디렉토리로 이동
cd 02-kubernetes-init

# 실행 권한 부여
chmod +x kube-init.sh

# 클러스터 구축 실행 (약 10-15분 소요)
sudo ./kube-init.sh
```

**설치되는 구성요소:**
- Containerd 2.1.4 (Container Runtime)
- Kubernetes 1.34.1
- Cilium CNI (kube-proxy replacement 활성화)
- Helm CLI
- kubectl, kubeadm

### 2단계: Jenkins 설치

```bash
# 03-jenkins-install 디렉토리로 이동
cd ../03-jenkins-install

# Jenkins 네임스페이스 생성
kubectl create namespace jenkins

# RBAC 설정 적용
kubectl apply -f jenkins-rbac.yaml

# Jenkins Helm 차트 추가
helm repo add jenkins https://charts.jenkins.io
helm repo update

# Jenkins 설치
helm install jenkins jenkins/jenkins \
  --namespace jenkins \
  --values jenkins-values.yaml

# Jenkins Pod 실행 대기
kubectl wait --for=condition=ready pod -l app.kubernetes.io/component=jenkins-controller -n jenkins --timeout=300s
```

### 3단계: Jenkins 접속 및 설정

```bash
# Jenkins 접속 정보 확인
kubectl get ingress -n jenkins

# 관리자 비밀번호 확인
kubectl exec --namespace jenkins -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/admiral/jenkins/initialAdminPassword
```

**Jenkins 접속 정보:**
- **URL**: `http://jenkins.cicd.lab` (Ingress 설정 필요)
- **사용자명**: `admin`
- **비밀번호**: `ubuntu123`

### 4단계: Jenkins Agent 테스트

Jenkins 웹 UI에서 "New Item" → "Pipeline" 생성 후 다음 파이프라인 코드를 입력:

```groovy
// 04-jenkins-agent/buildah-jenkinsfile 내용 복사
```

### 5단계: 완전한 CI/CD 파이프라인 구축

#### 5-1. 인증서 설정

Jenkins에서 다음 인증서들을 설정:

1. **Git SSH Key** (`github-ssh-key`)
   - Kind: SSH Username with private key
   - Private Key: GitHub SSH private key

2. **Docker Hub Credentials** (`dockerhub-credentials`)
   - Kind: Username with password
   - Username: Docker Hub 사용자명
   - Password: Docker Hub 액세스 토큰

#### 5-2. 파이프라인 설정

`cicd-demo-jenkinsfile`의 다음 변수들을 수정:

```groovy
def DOCKERHUB_USERNAME = "your-dockerhub-username"
def GIT_REPO_URL = "git@github.com:your-username/your-repo.git"
```

## ✨ 주요 기능

### 🐳 컨테이너 이미지 빌드
- **Buildah**를 사용한 rootless 컨테이너 빌드
- 멀티스테이지 Dockerfile로 최적화된 이미지 생성
- 보안 강화된 non-root 사용자 실행

### 🔄 CI/CD 파이프라인
- **Git 소스코드 자동 체크아웃**
- **자동 컨테이너 이미지 빌드**
- **Docker Hub 자동 푸시**
- **빌드 번호 기반 태깅**

### 🛡️ 보안 기능
- **RBAC 기반 권한 관리**
- **Secret을 통한 인증서 관리**
- **Privileged 컨테이너 최소화**

### 📊 모니터링 및 로깅
- **실시간 파이프라인 상태 확인**
- **상세한 빌드 로그**
- **애플리케이션 헬스체크**

## 🛠️ 기술 스택

| 기술 | 버전 | 용도 |
|------|------|------|
| **Kubernetes** | 1.34.1 | 컨테이너 오케스트레이션 |
| **Jenkins** | Latest | CI/CD 플랫폼 |
| **Containerd** | 2.1.4 | 컨테이너 런타임 |
| **Cilium** | 1.18.2 | CNI 및 네트워크 정책 |
| **Buildah** | 1.41.5 | 컨테이너 이미지 빌드 |
| **Node.js** | 18 | 웹 애플리케이션 |
| **Express** | 4.18.2 | 웹 프레임워크 |

## 🔍 문제 해결

### 일반적인 문제들

#### 1. Jenkins Pod가 시작되지 않는 경우
```bash
# Pod 상태 확인
kubectl get pods -n jenkins

# 상세 로그 확인
kubectl logs -n jenkins <pod-name>
```

#### 2. Ingress 접속이 안 되는 경우
```bash
# Ingress 컨트롤러 확인
kubectl get pods -n kube-system | grep cilium

# Ingress 상태 확인
kubectl describe ingress -n jenkins
```

#### 3. Buildah 빌드 실패
```bash
# Privileged 모드 확인
kubectl describe pod -n jenkins <build-pod-name>

# 볼륨 마운트 확인
kubectl get pv,pvc -n jenkins
```

### 유용한 명령어

```bash
# 클러스터 상태 확인
kubectl get nodes
kubectl get pods --all-namespaces

# Jenkins 로그 실시간 확인
kubectl logs -f -n jenkins deployment/jenkins

# Cilium 상태 확인
cilium status

# Helm 차트 목록
helm list -n jenkins
```

## 📞 지원 및 문의

이 교재와 관련된 질문이나 문제가 있으시면 다음을 통해 문의해주세요:

- **Issues**: GitHub Issues를 통해 버그 리포트나 기능 요청
- **Discussions**: GitHub Discussions를 통한 질문 및 토론

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 제공됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

## 🙏 기여하기

이 교재를 개선하는 데 도움을 주시고 싶으시다면:

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

**Happy Learning! 🎉**

이 교재를 통해 현대적인 DevOps 기술을 마스터하시길 바랍니다!

# IT 헬프데스크 REST API

사내 IT 헬프데스크 시스템을 시뮬레이션하는 REST API 서버입니다.
**MCP 서버 없이** AWS Bedrock Agent Core Gateway와 연결하여 사내 시스템을 AI Agent tool로 사용할 수 있습니다.

## 🎯 프로젝트 목적

기업 내부 시스템(IT 헬프데스크)을 **OpenAPI 스펙만으로** AI Agent의 도구로 변환하는 데모입니다.
- MCP 서버 구현 불필요
- 기존 REST API를 그대로 활용
- Bedrock Agent Core Gateway가 자동으로 MCP 프로토콜로 변환

## 📋 주요 기능

### API 엔드포인트
- `POST /tickets` - 티켓 생성
- `GET /tickets` - 티켓 목록 조회 (필터링 지원)
- `GET /tickets/{id}` - 티켓 상세 조회
- `PATCH /tickets/{id}` - 티켓 상태 업데이트
- `DELETE /tickets/{id}` - 티켓 삭제
- `GET /stats` - 티켓 통계

### 샘플 데이터
5개의 샘플 티켓이 자동으로 생성됩니다:
- 하드웨어 문제 (노트북 화면, 모니터)
- 네트워크 문제 (VPN)
- 소프트웨어 문제 (프린터 드라이버)
- 계정 문제 (비밀번호 초기화)

## 🚀 빠른 시작

### 로컬 실행

```bash
# 의존성 설치
pip install -r requirements.txt

# 서버 실행
uvicorn main:app --reload --port 8000
```

### API 문서 확인
서버 실행 후 http://localhost:8000/docs 에서 Swagger UI를 통해 API를 테스트할 수 있습니다.

### 간단한 테스트

```bash
# 티켓 목록 조회
curl http://localhost:8000/tickets | jq

# 새 티켓 생성
curl -X POST http://localhost:8000/tickets \
  -H "Content-Type: application/json" \
  -d '{
    "title": "노트북이 느립니다",
    "description": "부팅에 10분 이상 걸립니다",
    "priority": "high",
    "category": "hardware",
    "requester": "홍길동"
  }' | jq
```

## 🏗️ 프로젝트 구조

```
it-helpdesk-api/
├── main.py                # FastAPI 애플리케이션
├── models.py              # Pydantic 데이터 모델
├── requirements.txt       # Python 패키지 의존성
├── openapi.json          # OpenAPI 3.0 스펙 (Bedrock 연동용)
├── deploy.sh             # EC2 배포 스크립트
├── helpdesk-api.service  # Systemd 서비스 파일
└── EC2_DEPLOY.md         # 상세 배포 가이드
```

## ☁️ EC2 배포

상세한 배포 가이드는 [EC2_DEPLOY.md](EC2_DEPLOY.md)를 참고하세요.

간단 요약:
```bash
# 1. 파일 압축
tar -czf api.tar.gz main.py models.py requirements.txt deploy.sh

# 2. EC2로 업로드
scp -i key.pem api.tar.gz ec2-user@EC2_IP:~

# 3. EC2에서 설치 및 실행
ssh -i key.pem ec2-user@EC2_IP
bash deploy.sh
uvicorn main:app --host 0.0.0.0 --port 8000
```

## 🔗 Bedrock Agent Core Gateway 연동

1. **OpenAPI 스펙 준비**: `openapi.json` 파일 사용
2. **퍼블릭 URL**: EC2 퍼블릭 IP 또는 도메인
3. **AWS Console**에서 Agent Core Gateway 설정
4. OpenAPI 스펙 업로드 및 엔드포인트 등록

## 🛠️ 기술 스택

- **FastAPI** - 고성능 Python 웹 프레임워크
- **Pydantic** - 데이터 검증 및 직렬화
- **Uvicorn** - ASGI 웹 서버
- **OpenAPI 3.0** - API 문서화 및 스펙 정의

## 📝 라이선스

MIT License

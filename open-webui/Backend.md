# Ollama와 Open Web UI

## 소개

작년 생성형 AI 가상인간으로 유명한 D사 시니어 엔지니어가 GPT 서비스를 단 하루만에 만든 것을 보고 놀랐던 경험이 있습니다. ChatGPT와 매우 유사한 서비스를 빠르게 구축할 수 있다는 것을 알게 되었죠.

Open WebUI와 Ollama는 현재 가장 인기 있는 오픈소스 LLM 서비스 구축 도구입니다. (작년까지는 Ollama WebUI였지만 현재는 Open WebUI가 공식이름입니다.)

더욱 빠르게 다양한 오픈소스들이 시장에 나타나고 있고, 이것을 활용하여 커스터마이징을 할 수 있는 풀스택 개발자들이 점점 시장에 필요해지는 시점입니다.

## 목차

1. Ollama 구축
2. Open Web UI
3. Open WebUI Pipelines

## 1. Ollama 구축

### 1.1. Linux 스크립트를 이용한 설치

Ubuntu를 기준으로 한 설치 방법입니다.

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

#### 커스텀 디렉토리 설치

기본적으로 `/home` 디렉토리에 설치되지만, 원하는 디렉토리에 설치할 수 있습니다:

```bash
wget https://ollama.com/install.sh
vi install.sh
```

`OLLAMA_INSTALL_DIR` 변수를 수정하여 원하는 설치 경로 지정:
```bash
OLLAMA_INSTALL_DIR="/workspace/ollama"
```

### 1.2. 서버 호스트 지정 및 LLM 디렉토리 지정 실행 방법

```bash
OLLAMA_HOST="0.0.0.0:11434" \
OLLAMA_MODELS="/workspace/ollama/models" \
nohup ollama serve > ollama.log &
```

### 1.3. 서버 실행 도중 모델이 유지하도록 실행 방법

5분 동안 사용하지 않으면 모델이 Unload되는 문제를 해결하기 위해 `OLLAMA_KEEP_ALIVE` 옵션을 사용합니다:

```bash
OLLAMA_KEEP_ALIVE=-1 \
OLLAMA_HOST="0.0.0.0:11434" \
OLLAMA_MODELS="/workspace/ollama/models" \
nohup ollama serve > ollama.log &
```

### 1.4. Docker 이미지를 이용한 실행 방법

```bash
docker run -d \
-p 11434:11434 \
-v $(pwd)/ollama:/root/.ollama \
-v $(pwd)/ollama/models:/ollama/models \
-e OLLAMA_KEEP_ALIVE=-1 \
-e OLLAMA_MODELS="/ollama/models" \
--name ollama \
ollama/ollama
```

```bash
docker run -d \
--name ollama \
--gpus all \
--restart=always \
--network ollama-net  \
-p 11434:11434  \
-v ollama-data:/root/.ollama ollama/ollama
```


## 2. Open Web UI

Open WebUI는 대형 언어 모델(LLM)과의 상호작용을 위한 웹 인터페이스입니다. 처음에는 Ollama 전용이었으나, 현재는 OpenAI도 지원합니다.

### 2.1. 주요 기능

- 로컬 RAG (Retrieval-Augmented Generation) 지원
- OpenAI API 통합
- 다국어 지원
- 음성 입력 및 응답 지원

### 2.2. Docker 이미지 실행 방법

#### 2.2.1. 기본적인 사용법

```bash
sudo docker run -d -p 3000:8080 \
-e OLLAMA_BASE_URL="https://4ib1pd2fcklvsf-11434.proxy.runpod.net" \
-v $(pwd)/open-webui/data:/data \
--name open-webui \
--restart always \
ghcr.io/open-webui/open-webui:main
```

```bash
docker run -d   \
--name open-webui  \
--gpus all  \
--restart=always  \
--network ollama-net \
-p 3000:8080 \
-e OLLAMA_HOST=http://ollama:11434  \
-e BASE_URL=https://ai.zeroth.run  \
-v open-webui-data:/app/backend/data ghcr.io/open-webui/open-webui:main
```

#### 2.2.2. 커스터마이징을 위한 실행 방법

```bash
sudo docker run -d -p 3000:8080 \
-v $(pwd)/open-webui/backend:/app/backend \
-v $(pwd)/open-webui/data:/app/backend/data \
-v $(pwd)/open-webui/frontend:/app/frontend \
-v $(pwd)/open-webui/static:/app/static \
-e OLLAMA_BASE_URL="https://4ib1pd2fcklvsf-11434.proxy.runpod.net" \
-e AIOHTTP_CLIENT_TIMEOUT=600 \
-e FRONTEND_BUILD_DIR="/app/frontend" \
-e STATIC_DIR="/app/static" \
--name open-webui \
--restart always \
ghcr.io/open-webui/open-webui:main
```

## 3. Open WebUI 커스터마이징

### 3.1. WebUI 백엔드 구성

Python 3.11 이상이 필요합니다:

```bash
conda create -n py311 python=3.11
conda activate py311
cd backend/
pip -r requirements.txt
./dev.sh
```

### 3.2. WebUI 프론트엔드 구성

Node.js 20 버전 이상 권장:

```bash
npm i
npm run dev
```

### 3.3. 프론트엔드 커스터마이징

#### 3.3.1. 브라우저 타이틀 변경

`app.html`과 `env.py`에서 타이틀을 수정합니다.

#### 3.3.2. 로그인 페이지 및 이미지 로고 변경

`src/routes/auth/+page.svelte` 파일을 수정하여 로그인 페이지를 커스터마이징합니다.

#### 3.3.3. 파일 업로드 막기

`src/components/chat/MessageInput.svelte` 파일에서 파일 업로드 관련 코드를 주석 처리합니다.

## 결론

Open Web UI는 Svelte와 FastAPI를 핵심 프레임워크로 사용하며, 커스터마이징하여 상용화 수준으로 완성하는데 일주일 정도가 소요됩니다. 풀스택 개발자(LLM 파인튜닝, RAG, 데이터분석, 프론트엔드, 백엔드, 아키텍처, 인프라 경험)라면 짧은 시간 내에 작업을 완료할 수 있습니다.

다음 글에서는 Open WebUI Pipelines를 통한 질의응답 필터링과 RAG 구현 방법에 대해 더 자세히 설명하겠습니다.
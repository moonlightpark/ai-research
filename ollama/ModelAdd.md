# 허깅페이스 모델 Ollama에 추가하기

허깅페이스(HuggingFace) 플랫폼에서 모델을 GGUF 파일 형식으로 다운로드하는 방법을 알아보자. 
이렇게 다운로드한 모델을 Ollama에 추가하는 방법에 대해서도 살펴보자. 
Llama-3-Open-Ko-8B-Instruct-preview 모델을 Ollama에 올려보자.

## 목차
1. [GGUF 형식의 모델 파일 다운로드](#gguf-형식의-모델-파일-다운로드)
   - [허깅페이스 저장소 접속](#허깅페이스-저장소-접속)
   - [다운로드 방법](#다운로드-방법)
2. [Modelfile 파일 생성](#modelfile-파일-생성)
3. [Ollama 모델 생성 및 실행](#ollama-모델-생성-및-실행)

## GGUF 형식의 모델 파일 다운로드

### 허깅페이스 저장소 접속
[Llama-3-Open-Ko-8B-Instruct-preview-gguf](https://huggingface.co/teddylee777/Llama-3-Open-Ko-8B-Instruct-preview-gguf) 저장소에 접속합니다.

### 다운로드 방법

#### 방법 1: git lfs 이용 다운로드
git이 설치되어 있다면 다음과 같이 git clone 명령을 통해 다운로드할 수 있습니다:

```bash
git lfs install
git clone https://huggingface.co/teddylee777/Llama-3-Open-Ko-8B-Instruct-preview-gguf
```

#### 방법 2: 직접 다운로드
1. Files 탭으로 이동
2. 'Llama-3-Open-Ko-8B-Instruct-preview-Q8_0.gguf' 파일 클릭
3. 다운로드 버튼을 클릭하여 모델 파일을 로컬에 저장

## Modelfile 파일 생성

다운로드한 GGUF 파일이 위치한 경로에 `Modelfile`을 생성하고 다음 내용을 입력합니다:

```text
FROM Llama-3-Open-Ko-8B-Instruct-preview-Q8_0.gguf

TEMPLATE """{{- if .System }}
<s>{{ .System }}</s>
{{- end }}
<s>Human:
{{ .Prompt }}</s>
<s>Assistant:
"""
SYSTEM """친절한 챗봇으로서 상대방의 요청에 최대한 자세하고 친절하게 답하자. 모든 대답은 한국어(Korean)으로 대답해줘."""

PARAMETER temperature 0
PARAMETER num_predict 3000
PARAMETER num_ctx 4096
PARAMETER stop <s>
PARAMETER stop </s>
```

## Ollama 모델 생성 및 실행

### 모델 생성
다음 명령어로 `kollama3`라는 이름으로 모델을 Ollama에 추가합니다:

```bash
ollama create kollama3 -f Modelfile
```

### 모델 실행
생성된 `kollama3` 모델을 실행합니다:

```bash
ollama run kollama3
```
허깅페이스 모델 Ollama에 추가하기

허깅페이스(HuggingFace) 플랫폼에서 모델을 GGUF 파일 형식으로 다운로드하는 방법을 알아보자. 
이렇게 다운로드한 모델을 Ollama에 추가하는 방법에 대해서도 살펴보자. 
Llama-3-Open-Ko-8B-Instruct-preview 모델을 Ollama에 올려보자.

'''
목차
GGUF 형식의 모델 파일 다운로드
허깅페이스 저장소 접속
(방법 1) git lfs 이용 다운로드
(방법 2) 직접 다운로드
Modelfile 파일 생성
Ollama Modelfile 생성
Ollama Model 실행
GGUF 형식의 모델 파일 다운로드
허깅페이스에 GGUF 형식으로 업로드 된 모델을 다운로드한다. 이 글에서는 Llama-3-Open-Ko-8B-Instruct-preview-gguf 파일을 예시로 사용한다.
'''

허깅페이스 저장소 접속
https://huggingface.co/teddylee777/Llama-3-Open-Ko-8B-Instruct-preview-gguf

(방법 1) git lfs 이용 다운로드
git이 설치되어 있다면 다음과 같이 git clone 명령을 통해 다운로드할 수 있다. git clone 명령 뒤에 허깅페이스 저장소 URL을 입력한다.

git lfs install
git clone https://huggingface.co/teddylee777/Llama-3-Open-Ko-8B-Instruct-preview-gguf
(방법 2) 직접 다운로드
Files 탭 이동 후 ‘Llama-3-Open-Ko-8B-Instruct-preview-Q8_0.gguf’를 클릭해서 직접 모델을 파일로 다운로드할 수도 있다.


모델 파일 페이지에 접속한 다음 다운로드 버튼을 클릭하면 모델 파일이 로컬에 다운로드된다.


Modelfile 파일 생성
다운로드한 GGUF 파일이 위치한 경로에 Modelfile 파일 생성 후 다음 내용 입력 후 저장한다.

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
Ollama Modelfile 생성
위에서 작성한 Modelfile을 바탕으로 kollama3라는 이름으로 모델을 Ollama에 추가한다.

ollama create kollama3 -f Modelfile
Ollama Model 실행
Ollama에 추가한 kollama3 모델을 실행한다.

ollama run kollama3
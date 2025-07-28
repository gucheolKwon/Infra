# Infra

MobaXterm - ssh용도로 사용

Docker Container
```bash
sudo docker build -t docker_image
docker run -it --gpus all -d --name container_name -p {port}:{port} -v {server_dir_path}:{docker_dir_path} docker_image

apt-get install -y tmux
tmux new -t jupyter

cd /home/pyadmin
jupyter notebook --ip=0.0.0.0 --allow-root --port=port_number
```

# LLM Infra
Ollama
```bash
curl -fssL https://ollama.com/install.sh | sh
wget -qO- https://ollama.com/install.sh | sh

# 외부망 연결이 안될 시, 수동으로 ide에 tgz 파일 업로드해서 설치
curl -L https://ollama.com/download/ollama-linux-amd64.tgz -o ollama-linux-amd64.tgz
sudo tar -C /usr -xzf ollama-linux-amd64.tgz

ollama serve
ollama -v
```

OpenWeb-UI
```bash
pip install open-webui
sudo docker run -d -p {port}:{port} -v {server_dir_path}/:/{docker_dir_path} -e OLLAMA_BASE_URL={ollama_ip:port} -e NO_PROXY={options} --restart always --name open_webui open_webui
```

Model Import (GGUF)
- Huggingface : model download
- /root/.ollama/models -> gguf file and Modelfile
- cmd : ollama create {gguf filename} -f {Modelfile name}
```vim
FROM Meta-Llama-3-8B-Instruct.Q8_0.gguf ## 해당 모델명에 맞게 수정
  
TEMPLATE """{{- if .System }}
<s>{{ .System }}</s>
{{- end }}
<s>Human:
{{ .Prompt }}</s>
<s>Assistant:
"""

SYSTEM """A chat between a curious user and an artificial intelligence assistant. The assistant gives helpful, detailed, and polite answers to the user's questions."""

PARAMETER stop <s>
PARAMETER stop </s>

## EXAONE과 Llama-3-Open-Ko는 한글 프롬프트로 추가
FROM Llama-3-Open-Ko-8B-Q8_0.gguf

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
```

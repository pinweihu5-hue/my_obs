---
aliases:
  - llm
id:
---

[[cursor_cli]]
[[llm_vscode]]



- [ ] [(488) 小發學姐 - YouTube](https://www.youtube.com/@little-fa) => 分享最新 AI 資訊，圖片、影片編輯的思路和技巧，與新 AI 工具的結合運用
      讓你不被這次 AI 革命所淘汰。




---


# cost
[GitHub - AgentOps-AI/tokencost: Easy token price estimates for 400+ LLMs](https://github.com/AgentOps-AI/tokencost)

| model             | per million usd |
| ----------------- | --------------- |
| 4o                | 5               |
| gpt-3.5-turbo     | 1.5             |
| claude-3-haiku    | 0.25            |
| claude-3-5-sonnet | 3               |


## ollama

### run in docker
```
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama


```

### model available
- [library](https://ollama.com/library)


### install
```bash fold title:install_and_usage

# model is store here, but you can't just remove it, need to use its api to remove it
/Users/re4388/.ollama
# see how big/how many model download it take you space
ollama list

# remove no needed model
ollama rm llama2

# to get more model
# to see more model avalilable:
# https://github.com/ollama/ollama?tab=readme-ov-file#model-library
# https://ollama.com/library
ollama pull xxxx

# how to stop
#1. go menu bar
#2.
pkill ollama
#3.
$ pgrep ollama
74877
$ kill 74877


# Copy a model
ollama cp llama2 my-llama2

# Multiline input
For multiline input, you can wrap text with """:

>>> """Hello,
... world!
... """
I'm a basic program that prints the famous "Hello, world!" message to the console.


# see image
>>> What's in this image? /Users/jmorgan/Desktop/smile.png
The image features a yellow smiley face, which is likely the central focus of the picture.

# Pass in prompt as arguments
$ ollama run llama2 "Summarize this file: $(cat README.md)"

# List models on your computer
ollama list


# start the server
# ollama serve is used when you want to start ollama without running the desktop application.
./ollama serve

# run it!
./ollama run llama2


# one time

curl http://localhost:11434/api/generate -d '{
  "model": "llama2",
  "prompt":"Why is the sky blue?"
}'


# chat mode

curl http://localhost:11434/api/chat -d '{
  "model": "mistral",
  "messages": [
    { "role": "user", "content": "why is the sky blue?" }
  ]
}'



# Ollama supports importing GGUF models in the Modelfile:

# Create a file named Modelfile, with a FROM instruction with the local filepath to the model you want to import.
# gguf file can get from https://huggingface.co/TheBloke
# like https://huggingface.co/TheBloke/stable-code-3b-GGUF/blob/main/stable-code-3b.Q4_0.gguf
FROM ./<xxxxx>.gguf

# Create the model in Ollama
ollama create <model_name> -f Modelfile

#Run the model
ollama run example



```

- ollama + ui + cpolar
	- [Windows 安装 Ollama 结合内网穿透实现公网访问本地大语言模型 web 交互界面 - cpolar 极点云官网](https://www.cpolar.com/blog/windows-installation-of-ollama-combined-with-intranet-penetration-to-achieve-public-network-access-to-local-large-languages)
	- [open-webui/open-webui: User-friendly WebUI for LLMs (Formerly Ollama WebUI)](https://github.com/open-webui/open-webui)


# tools

vscode ai chat, 大陸做的 https://youtu.be/e2481Xz4kqQ?si=9pssd2Gz0HtCpjos

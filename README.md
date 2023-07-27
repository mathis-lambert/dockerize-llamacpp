# Dockeriser LLama.cpp
------------------------
## Prérequis
- Docker-Engine
- git
## Ressources
- Repo Github : https://github.com/ggerganov/llama.cpp/tree/master
- Page HF TheBloke : https://huggingface.co/TheBloke/Llama-2-13B-chat-GGML

## Installation
#### 1. Cloner le repo
```bash
git clone https://github.com/ggerganov/llama.cpp.git llama
```
#### 2. Se placer dans le dossier du repo
```bash
cd llama
```
#### 3. Télécharger le modèle \{{bit_number}}, \{{version}}
```bash
llama-2-13b-chat.ggmlv3.q{{bit_number}}_{{version}}.bin
```
Par exemple pour la version 1.1 du q_4 bits : 
(Pour les autres versions, se réferer à : [Page HF TheBloke](https://huggingface.co/TheBloke/Llama-2-13B-chat-GGML))
```bash
wget https://huggingface.co/TheBloke/Llama-2-13B-chat-GGML/resolve/main/llama-2-13b-chat.ggmlv3.q4_1.bin
```
#### 4. Copier le modèle dans le dossier du ./models
```bash
mv llama-2-13b-chat.ggmlv3.q{{bit_number}}_{{version}}.bin ./models
```

## Utilisation

#### 1. Script sh à mettre dans llama.cpp
```bash
#!/bin/bash
./main -ins \
-f /llama/prompts/alpaca.txt \
-t 8 \
-ngl 1 \
-m /llama/models/llama-2-13b-chat.ggmlv3.q4_1.bin \
--color \
--temp 0.7 \
--repeat_penalty 1.1 \
-s 42 \
-n -1
```

#### 2. Dockerfile (dans llama.cpp)
```dockerfile
FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -y build-essential python3 python3-pip git

COPY . /llama

WORKDIR /llama

# RUN make
RUN make clean
RUN make
RUN chmod +x run.sh

CMD ["/llama/run.sh"]
```

#### 3. Build de l'image
```bash
docker build -t llama .
```

#### 4. Lancement du container
```bash
docker run -it llama
```

#### 5. Lancement du script
Le script démarrera automatiquement lors du démarrage du container en -it (interactive mode)

## DOCKER avec CUDA
Se renseigner [en cliquant ICI](https://github.com/ggerganov/llama.cpp/blob/master/README.md#docker-with-cuda)
- `make LLAMA_CUBLAS=1`
- `--gpus all`
- `--n-gpu-layers 1`

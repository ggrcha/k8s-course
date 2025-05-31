# Instalação do Minikube

O objetivo deste roteiro é apresentar os passos para instalação do Minikube

## Pré requisitos

Como pré requisito para instalação estabelecemos uma máquina física ou virtual com o sistema operacional Linux Ubuntu 24.04 e o Docker 28.1.1 instalado.

## Instalação

A instalação é bem simples e consiste na execução de 2 comandos no terminal:

1. Download o binário do Minikube
```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
```
2. Instalação do binário do Minikube
```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

Para confirmar se a instalação foi bem sucedida digite:
```bash
minikube version
```

O resultado esperado é semelhante a:
```bash
minikube version: v1.36.0
commit: f8f52f5de11fc6ad8244afac475e1d0f96841df1-dirty
```

## Pós instalação

Agora vamos executar mais alguns comandos que vão facilitar o uso do Minikube

1. Criação de um atalho para o comando mais utilizado para interagir com um cluster k8s, o kubectl
```bash
echo 'alias kubectl="minikube kubectl --"' >>~/.bashrc
```
2. Habilitar o "auto completion" que é um recurso que completa os comando através da tecla TAB
```bash
echo 'source /etc/bash_completion' >>~/.bashrc
```
3. Incluir o "auto completion" do Minikube na inicialização do terminal
```bash
echo 'source <(minikube completion bash)' >>~/.bashrc
```
4. Incluir o "auto completion" do kubectl na inicialização do terminal
```bash
echo 'source <(kubectl completion bash)' >>~/.bashrc
```
5. Ativar o "auto completion" no terminal atual
```bash
source ~/.bashrc
```

Para confirmar se a ativação do "auto completion" foi bem sucedida digite:
```bash
minikube ver # e pressione TAB
```

O resultado esperado é:
```bash
minikube version
```

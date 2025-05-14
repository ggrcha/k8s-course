# 📚 Roteiro para Condução da Aula 2 – Componentes do Kubernetes

---

## 🎯 Objetivo da Aula

Guiar os alunos no entendimento da arquitetura do Kubernetes, explicando como os componentes se relacionam, desde o envio de um manifesto até a execução do Pod e a atribuição de IPs por Services.

---

## 🕒 Tempo estimado: 90 minutos

---

## ✅ Materiais necessários

- Cluster Kubernetes ativo (minikube, kind ou remoto)
- Ferramentas: `kubectl`, terminal, VSCode
- Arquivos: `exemplo-pod.yaml`, `exemplo-service.yaml`
- Quadro ou slides para mostrar a arquitetura

---

## 🗓️ Etapas da Aula

### 1. 📣 Abertura (10 min)

- Relembre rapidamente os tópicos da Aula 1 (Pods, Nodes, Manifestos)
- Apresente o objetivo da Aula 2
- Compartilhe o arquivo `.md` da aula com os alunos

### 2. 🧱 Introdução teórica (20 min)

- Explique a arquitetura cliente-servidor no Kubernetes
- Diferencie Control Plane e Nodes
- Apresente cada componente do Control Plane e do Node:
  - Use um quadro ou o diagrama ASCII da aula
  - Relacione cada componente com sua função (ex: kubelet = executor, apiserver = interface)

### 3. 💻 Demonstração prática (20 min)

- Crie ao vivo o Pod `exemplo-pod` com o manifesto:

```bash
kubectl apply -f exemplo-pod.yaml
```

- Use `kubectl describe` para mostrar como o Pod é processado e em qual Node foi agendado
- Mostre o status usando:

```bash
kubectl get pods -o wide
```

- Simule a criação de um Service e mostre como o IP é atribuído:

```bash
kubectl apply -f exemplo-service.yaml
kubectl get svc exemplo-service
```

- Mostre que o tráfego é roteado corretamente (pode usar um `curl` interno via `kubectl exec`)

---

### 4. 🔍 Discussão orientada (10 min)

- Projete as **perguntas para reflexão**
- Separe a turma em duplas ou grupos pequenos por 5 minutos
- Volte e comente as **respostas sugeridas** para consolidar o conteúdo

---

### 5. 🧪 Exercício prático em grupo (20 min)

- Peça que cada aluno:
  - Liste os Nodes do cluster
  - Descreva um Node
  - Verifique os componentes com:

```bash
kubectl get componentstatuses
```

- Depois, simulem juntos uma falha com `minikube stop` (ou pause algum Node se tiver cluster real)
- Discutam o comportamento dos Pods e do cluster

---

### 6. 📌 Encerramento (10 min)

- Recapitule a arquitetura geral
- Explique que na próxima aula eles verão como trabalhar com Deployments, ReplicaSets e o controle declarativo de aplicações
- Oriente a leitura prévia dos recursos: `Pod`, `ReplicaSet`, `Deployment`

---

## 📝 Atividades para casa

- Refaça os comandos em um ambiente próprio
- Crie um novo Pod com uma imagem diferente
- Exponha esse Pod com um Service e teste a conectividade

---

## 🔗 Recursos adicionais

- https://kubernetes.io/docs/concepts/overview/components/
- https://kubernetes.io/docs/concepts/architecture/control-plane-node-communication/
- https://kubernetes.io/docs/concepts/services-networking/service/

---
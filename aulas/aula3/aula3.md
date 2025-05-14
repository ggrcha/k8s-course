# Kubernetes Básico - Aula 3: Objetos Fundamentais - Pods, ReplicaSets e Deployments

---

## 📅 Objetivos da Aula

- Compreender os principais objetos declarativos do Kubernetes
- Diferenciar `Pod`, `ReplicaSet` e `Deployment`
- Aplicar manifestações YAML para criar, atualizar e escalar aplicações
- Consolidar o conceito de "estado desejado" com prática real

---

## ✏️ Resumo Teórico

### O que é um Pod?

- É a menor unidade de execução no Kubernetes
- Representa **um ou mais containers** que compartilham:
  - o mesmo endereço IP e namespace de rede
  - volumes (armazenamento compartilhado)
  - variáveis de ambiente, configurações e contexto de execução
- O principal motivo de se agrupar containers em um Pod é **permitir que eles compartilhem recursos do sistema operacional**, como se fossem parte de um mesmo processo lógico (ex: um servidor web com um sidecar de log ou proxy).
- Os Pods são efêmeros: se falharem, são substituídos por novos Pods — mas **o Pod em si não se auto-recupera**

**Exemplo:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod
spec:
  containers:
  - name: app
    image: nginx
    ports:
    - containerPort: 80
```

🔗 [Documentação oficial sobre Pods](https://kubernetes.io/docs/concepts/workloads/pods/)

---

### O que é um ReplicaSet?

- Um objeto de controle que assegura que **um número fixo de réplicas de um Pod esteja sempre rodando**
- Se algum Pod gerenciado for excluído ou falhar, o ReplicaSet cria automaticamente um novo
- O motivo para sua existência é garantir **alta disponibilidade e escalabilidade horizontal** da aplicação, de forma que múltiplas réplicas estejam sempre ativas para balanceamento de carga e tolerância a falhas
- **Não é usado diretamente na maioria dos casos** porque **o ReplicaSet não oferece mecanismos de atualização de versão, rollback ou gerenciamento de mudanças** — ele é um controlador básico. Já o `Deployment` adiciona essa camada de inteligência, por isso é a forma recomendada e segura de usar ReplicaSets indiretamente.

**Exemplo:**
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: meu-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: minha-app
  template:
    metadata:
      labels:
        app: minha-app
    spec:
      containers:
      - name: app
        image: nginx
```

🔗 [Documentação oficial sobre ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

---

### O que é um Deployment?

- É o controlador mais utilizado no Kubernetes para **gerenciar ciclos de vida de aplicações**
- Cria e gerencia ReplicaSets de forma controlada
- Existe para facilitar a **implantação progressiva de mudanças**, **rollbacks** e **operações de escalabilidade** em ambientes de produção
- Permite:
  - Atualizações contínuas (rolling updates) com controle de disponibilidade
  - Rollbacks automáticos em caso de falha
  - Escalonamento de réplicas em tempo real
  - Pausar ou reverter mudanças
- Garante o estado desejado de toda a aplicação e historiza revisões
- Você pode definir a estratégia de atualização de forma declarativa com o campo `strategy`:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 1
```

> Isso significa que, durante a atualização:
> - No máximo 1 Pod a mais pode ser criado temporariamente (`maxSurge`)
> - No máximo 1 Pod pode estar indisponível (`maxUnavailable`)

**Exemplo:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: meu-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: minha-app
  template:
    metadata:
      labels:
        app: minha-app
    spec:
      containers:
      - name: app
        image: nginx:1.21
```

🔗 [Documentação oficial sobre Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

---

### Estado desejado

- O Kubernetes funciona com base em uma **arquitetura declarativa**
- O usuário descreve o **estado desejado** (ex: "quero 3 Pods com nginx versão 1.21")
- O controlador compara esse estado com o **estado atual do cluster** e executa ações para ajustar
- Esse processo é chamado de **reconciliação**, e é contínuo
- O conceito de estado desejado permite que a plataforma **seja resiliente e autônoma**, reagindo a falhas ou mudanças inesperadas automaticamente

🔗 [Documentação sobre estado desejado e reconciliação](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#desired-state)

---

## 📘 Ilustração dos Conceitos

```
[Deployment]
    |
    |-- cria --> [ReplicaSet]
                     |
                     |-- cria N --> [Pods]
```

---

## ❓ Perguntas para Reflexão

1. Qual a vantagem de usar um Deployment em vez de criar Pods diretamente?
2. O que o ReplicaSet faz quando um Pod é deletado manualmente?
3. Qual a diferença entre escalar Pods via Deployment ou via ReplicaSet?
4. Por que o Pod não é considerado "auto-recuperável" sozinho?
5. Como o conceito de estado desejado se aplica ao Deployment?

---

## 📋 Exercício Prático

### Parte 1: Criando um Deployment

1. Crie um manifesto YAML chamado `nginx-deployment.yaml` com o seguinte conteúdo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

2. Aplique o manifesto:

```bash
kubectl apply -f nginx-deployment.yaml
```

3. Verifique os recursos:

```bash
kubectl get deployments
kubectl get replicaset
kubectl get pods
```

4. Delete manualmente um Pod e observe o ReplicaSet agir:

```bash
kubectl delete pod <nome-do-pod>
```

5. Escale o Deployment para 5 réplicas:

```bash
kubectl scale deployment nginx-deploy --replicas=5
```

6. Atualize a imagem do nginx para `1.23`:

```bash
kubectl set image deployment nginx-deploy nginx=nginx:1.23
```

7. Observe o rollout:

```bash
kubectl rollout status deployment nginx-deploy
```

8. Faça rollback:

```bash
kubectl rollout undo deployment nginx-deploy
```

---

## 💡 Para a próxima aula

- Estude os tipos de controllers no Kubernetes:
  - DaemonSet
  - StatefulSet
  - Job e CronJob

- Reflita: "Quando usar um Deployment, um DaemonSet ou um StatefulSet?"

---
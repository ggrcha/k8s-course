# Kubernetes Básico - Aula 2: Componentes do Kubernetes

---

## 📅 Objetivos da Aula

- Compreender os principais componentes da arquitetura do Kubernetes
- Diferenciar o que é Control Plane e o que são os Nodes (trabalho e controle)
- Entender o papel de cada componente no funcionamento do cluster
- Consolidar os conceitos com exemplos visuais e práticos

---

## ✏️ Resumo Teórico

### Por que o Kubernetes segue uma arquitetura cliente-servidor?

O Kubernetes foi projetado para ser **modular, escalável e distribuído**. Por isso, sua arquitetura é baseada no modelo **cliente-servidor**, no qual:

- O **servidor** (Control Plane) é responsável por tomar decisões sobre o cluster — como agendar pods, gerenciar estado, manter consistência.
- O **cliente** (como o `kubectl`, CI/CD pipelines ou APIs externas) envia comandos ou consultas para o cluster através da **API Server**.

Essa separação permite que **vários clientes interajam simultaneamente com o cluster**, garantindo **controle centralizado e execução distribuída**.

---

### Componentes do Control Plane

1. **kube-apiserver**  
   Porta de entrada do cluster. Valida e processa solicitações e expõe a API REST do Kubernetes.

2. **etcd**  
   Banco de dados chave-valor que guarda todo o estado desejado do cluster. Altamente disponível e distribuído.

3. **kube-scheduler**  
   Decide em qual Node um novo Pod será alocado com base em critérios como CPU, memória, afinidades etc.

4. **kube-controller-manager**  
   Conjunto de controladores que garantem que o estado real se aproxime do desejado (ex: ReplicationController, NodeController).

---

### Componentes do Node

1. **kubelet**  
   Agente que roda em cada Node. Se comunica com o API Server e gerencia os Pods.

2. **kube-proxy**  
   Gerencia a rede do cluster. Cria regras de iptables/ipvs para expor Services.

3. **Container Runtime** (ex: containerd, cri-o)  
   Responsável por executar os containers propriamente ditos.

---

## 🔄 Exemplo Detalhado: Como os Componentes do Control Plane Interagem

Vamos supor que você aplique o seguinte manifesto no seu cluster Kubernetes:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: exemplo-pod
  labels:
     app: exemplo
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
```

### O que acontece nos bastidores, passo a passo:

#### 1. `kubectl apply` envia a requisição

- Envia uma requisição HTTP (REST) para o **`kube-apiserver`**.
- O `kube-apiserver` atua como porta de entrada do cluster.

#### 2. `kube-apiserver` valida e registra o Pod

- Valida o manifesto YAML e verifica permissões (RBAC).
- Armazena o objeto Pod no **etcd**.

#### 3. O `kube-scheduler` entra em ação

- Monitora Pods sem Node atribuído.
- Avalia recursos disponíveis e regras de agendamento.
- Define o melhor Node e atualiza o Pod via `kube-apiserver`.

#### 4. O `kubelet` do Node escolhido executa o Pod

- Detecta o novo Pod atribuído ao seu Node.
- Puxa a imagem, inicia o container e monitora seu status.

#### 5. O Pod está em execução

- O `kubelet` reporta o status ao `kube-apiserver`.
- O estado é armazenado no etcd e visível via `kubectl`.

#### 6. Como o kube-proxy atribui um IP a um Service

Se você criar um Service para expor esse Pod:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: exemplo-service
spec:
  selector:
    app: exemplo
  ports:
  - port: 80
    targetPort: 80
```

- O `kube-apiserver` valida e salva o objeto Service no `etcd`.
- O `kube-proxy` (em cada Node) monitora os serviços criados e:
  - Gera uma regra de iptables/ipvs para redirecionar o tráfego
  - Atribui automaticamente um **ClusterIP** para esse serviço
  - Roteia o tráfego para os Pods selecionados pelo `selector`

Você pode visualizar o IP com:

```bash
kubectl get svc exemplo-service
```

Utilize o comando abaixo para testar a resolução do seu serviço a partir do hostname

```bash
kubectl exec -it exemplo-pod -- curl http://exemplo-service
```

### 🔁 Resumo visual do fluxo dentro do apiserver

```
kubectl apply -f pod.yaml
        │
        ▼
[1] Authentication → quem está solicitando?
[2] Authorization  → pode fazer isso?
[3] Admission      → está conforme as regras?
        │
        ▼
Se aprovado, é persistido no etcd e processado pelos controladores
````

- 🔐 [Authentication - Kubernetes Documentation](https://kubernetes.io/docs/reference/access-authn-authz/authentication/)
- ✅ [Authorization - Kubernetes Documentation](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)
- 🧩 [Admission Controllers - Kubernetes Documentation](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/)

### 🔁 Resumo visual do fluxo

```
kubectl apply 
      │
      ▼
kube-apiserver (valida e grava no etcd)
      │
      ▼
kube-scheduler (detecta, decide Node, atualiza etcd)
      │
      ▼
kubelet do Node escolhido (executa container)
      │
      ▼
container runtime (roda o nginx)
      │
      ▼
kube-proxy (atribui IP de Service e redireciona tráfego)
```

---

## 📘 Ilustração da Arquitetura

```
+------------------------+
|     Control Plane      |
|------------------------|
|  kube-apiserver        |
|  etcd                  |
|  kube-scheduler        |
|  kube-controller-mgr   |
+------------------------+
           |
           | (API)
           v
+------------------------+       +------------------------+
|        Node 1          |       |        Node 2          |
|------------------------|       |------------------------|
|  kubelet               |       |  kubelet               |
|  kube-proxy            |       |  kube-proxy            |
|  container runtime     |       |  container runtime     |
|  [Pod A] [Pod B]       |       |  [Pod C] [Pod D]       |
+------------------------+       +------------------------+
```

---

## ❓ Perguntas para Reflexão

1. O que é o `kube-apiserver` e por que ele é considerado o "coração" do cluster?
2. O que o `kube-scheduler` leva em conta para decidir onde rodar um novo Pod?
3. Qual a importância do `etcd` para a consistência do cluster?
4. O que aconteceria se o `kubelet` de um Node falhasse?
5. Por que a separação entre Control Plane e Node é importante?

---

## 🔗 Referências Oficiais

- https://kubernetes.io/docs/concepts/architecture/control-plane-node-communication/
- https://kubernetes.io/docs/concepts/overview/components/
- https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/
- https://etcd.io/docs/
- https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/

---

## 📋 Exercício Prático

### Parte 1: Investigue seu cluster

Execute os comandos abaixo e responda:

```bash
kubectl get nodes
kubectl describe node <nome-do-node>
kubectl get componentstatuses
```

**Perguntas:**

- Quantos Nodes estão ativos?
- O que cada componente está reportando?
- Algum componente está em falha?

---

### Parte 2: Simule uma falha de Node

Se estiver usando `minikube`, pare o Node temporariamente:

```bash
minikube stop
```

Após alguns segundos, verifique:

```bash
kubectl get nodes
kubectl get pods -A -o wide
```

**Pergunta:**

- O que aconteceu com os Pods que estavam no Node desligado?

---

## 💡 Para a próxima aula

- Estude os objetos mais usados no Kubernetes:
  - `Pod`
  - `ReplicaSet`
  - `Deployment`

- Entenda o conceito de **estado desejado** e o uso de **manifestos YAML** no controle de recursos.

---
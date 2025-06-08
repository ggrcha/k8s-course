# Kubernetes Básico - Aula 6: Serviços e Rede no Kubernetes

---

## 📅 Objetivos da Aula

- Entender o que é um `Service` no Kubernetes
- Conhecer os principais tipos: `ClusterIP`, `NodePort`, `LoadBalancer`
- Compreender o papel do `kube-proxy` no roteamento de tráfego
- Diferenciar `LoadBalancer` de `Ingress`
- Identificar em que camada cada recurso atua
- Praticar com exemplos reais de criação de Services

---

## ✏️ Resumo Teórico

### 🔸 O que é um `Service`?

Um `Service` no Kubernetes é um recurso que **abstrai o acesso aos Pods**, criando um ponto de rede estável mesmo que os Pods sejam reiniciados ou substituídos (o que mudaria seus IPs). Ele facilita a descoberta e roteamento de tráfego para aplicações executando no cluster.

🔗 [Fonte: Kubernetes Services - Conceitos](https://kubernetes.io/docs/concepts/services-networking/service/)

---

### 🔸 Tipos de `Service`

#### 1. `ClusterIP` (padrão)

- Disponibiliza os Pods **somente dentro do cluster**
- Ideal para comunicação entre microserviços

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 3000
```

---

#### 2. `NodePort`

- Expõe os Pods em uma **porta específica em cada nó**
- Permite acesso externo sem um load balancer
- Porta padrão: entre 30000 e 32767

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-service
spec:
  type: NodePort
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 3000
      nodePort: 30080
```

---

#### 3. `LoadBalancer`

- Cria um **load balancer externo automático**, se estiver em um provedor de nuvem
- Ideal para expor serviços diretamente para usuários externos
- Internamente utiliza um `ClusterIP` e um `NodePort`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: lb-service
spec:
  type: LoadBalancer
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 3000
```

🔗 [Fonte: LoadBalancer Services](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)

---

### 🔍 O papel do `kube-proxy`

O `kube-proxy` é um componente que roda em cada nó do cluster e é responsável por **rotear o tráfego de rede para os Pods corretos**, com base nas definições dos `Services`.

#### Ele funciona assim:

- Observa as mudanças nos objetos `Service` e `Endpoints` via API Server
- Gera regras de roteamento usando **iptables** ou **IPVS**
- Encaminha as conexões recebidas nos IPs/ports do `Service` para um dos Pods associados via `label selector`

📌 Dessa forma, o tráfego que chega ao `Service` é balanceado entre os Pods disponíveis.

🔗 [Fonte: kube-proxy](https://kubernetes.io/docs/concepts/services-networking/service/#ips-and-vips)

---

### 🔍 Comparativo: `LoadBalancer` vs `Ingress`

#### ✅ `Service` do tipo `LoadBalancer`

- Atua na **camada 4 (TCP/UDP)** do modelo OSI
- Recebe tráfego externo via IP público
- Encaminha esse tráfego para **um único serviço Kubernetes**
- Pode distribuir para vários **Pods replicados** daquele serviço

#### ✅ `Ingress` (com Ingress Controller)

- Atua na **camada 7 (HTTP/HTTPS)** do modelo OSI
- Funciona como **gateway HTTP compartilhado** para múltiplos serviços
- Usa **regras de path e host** para rotear tráfego para serviços distintos
- Requer instalação de um **Ingress Controller** (como nginx ou traefik)

🔗 [Ingress - Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/)

---

### 🧠 Analogia

> Imagine o `LoadBalancer` como uma **porta exclusiva** que distribui usuários entre várias réplicas do mesmo serviço (ex: vários atendentes da mesma função).
>
> Já o `Ingress` é como uma **recepção principal** com uma central de triagem, que analisa sua solicitação HTTP e redireciona para o serviço correto com base no endereço acessado (ex: `/api`, `/admin`, `/loja`).

---

## 📘 Exemplo completo

### Pod + Service `ClusterIP`

```yaml
# pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-backend
  labels:
    app: backend
spec:
  containers:
    - name: backend
      image: hashicorp/http-echo
      args:
        - "-text=Olá, mundo!"
      ports:
        - containerPort: 3000
```

```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 3000
```

---

## ❓ Perguntas para Reflexão

1. Qual é a vantagem de um `Service` sobre acessar diretamente um Pod?
2. Em quais situações você usaria um `LoadBalancer`?
3. O que o `Ingress` resolve que o `LoadBalancer` não resolve?
4. Em qual camada do modelo OSI atuam o `Service` e o `Ingress`?
5. Qual o papel do `kube-proxy`?

---

## 📋 Exercício Prático

1. Crie um Pod `meu-backend` com `http-echo` na porta 3000.
2. Crie três tipos de Service:
   - Um `ClusterIP`
   - Um `NodePort`
   - Um `LoadBalancer` (simulado ou real)
3. Teste o acesso com:

```bash
minikube service backend-service --url
```

4. (Opcional) Crie um `Ingress` com duas rotas: `/api` e `/site`, apontando para dois serviços diferentes.

---

## 💡 Para a próxima aula

**Tópico: ConfigMap e Secret**
- Separação de configuração e código
- Injeção via variáveis de ambiente e volumes
- Segurança no armazenamento de dados sensíveis

---
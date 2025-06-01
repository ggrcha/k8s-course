# Kubernetes Básico - Aula 5: Estrutura dos arquivos YAML e anatomia dos manifestos Kubernetes

---

## 📅 Objetivos da Aula

- Compreender a estrutura do formato YAML
- Aprender a sintaxe básica: dicionários, listas e arrays
- Entender a anatomia dos manifestos usados no Kubernetes
- Identificar e escrever os campos: `apiVersion`, `kind`, `metadata` e `spec`
- Visualizar exemplos reais de diferentes objetos com foco no campo `spec`

---

## ✏️ Resumo Teórico

### O que é YAML?

YAML é uma linguagem de serialização de dados legível por humanos, amplamente usada para definição de configuração, especialmente em ferramentas como Kubernetes, Ansible, Docker Compose, etc.

A sigla YAML significa: **YAML Ain’t Markup Language**.

### Estrutura básica do YAML

#### 1. **Dicionários (mapas)**

Usam `chave: valor`, com indentação por espaços:
```yaml
pessoa: 
  nome: Guilherme
  idade: 41
  profissao: DevOps
```

#### 2. **Listas (arrays)**

Usam o `-` para indicar itens:
```yaml
habilidades:
  - Kubernetes
  - Docker
  - Terraform
```

#### 3. **Listas de dicionários**
```yaml
pessoas:
  - nome: Ana
    idade: 25
  - nome: João
    idade: 28
```

⚠️ **Atenção**: Indentação incorreta ou uso de tabulações ao invés de espaços gera erro no `kubectl apply`.

---

## 🧩 Anatomia dos Manifestos Kubernetes

Todo manifesto no Kubernetes possui a seguinte estrutura básica:

```yaml
apiVersion: <versão da API>
kind: <tipo de recurso>
metadata:
  name: <nome do objeto>
  labels:
    <chave>: <valor>
spec:
  <especificação do recurso>
```

---

### Explicação dos Campos

#### 🔹 `apiVersion`

Define a **versão da API** usada para gerenciar o tipo de recurso.
Exemplos:
- `v1` → para Pods, Services, ConfigMaps
- `apps/v1` → para Deployments, StatefulSets, DaemonSets
- `batch/v1` → para Jobs e CronJobs

#### 🔹 `kind`

Define o **tipo de recurso** que será criado:
- `Pod`, `Deployment`, `Service`, `ConfigMap`, `Job`, etc.

#### 🔹 `metadata`

Informações sobre o objeto:
- `name`: nome único dentro do namespace
- `labels`: chave-valor para identificação, seleção e organização
- `namespace`: (opcional) onde o recurso será criado

#### 🔹 `spec`

Contém os detalhes e configurações **específicas de cada tipo de recurso**. A estrutura **muda conforme o `kind`**. É o “coração” do objeto Kubernetes.

---

## 📘 Exemplos de Manifestos com Explicação da `spec`

### Exemplo 1: Pod simples

Cria um Pod chamado `meu-pod` com um container rodando NGINX.

**Descrição do `spec`:**
- `containers`: define os containers do Pod
  - `name`: nome do container
  - `image`: imagem que será executada

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod
spec:
  containers:
    - name: nginx
      image: nginx:1.21
```

---

### Exemplo 2: Service

Cria um `Service` chamado `meu-service` do tipo `ClusterIP`, que encaminha tráfego para os Pods com o label `app: minha-app`.

**Descrição do `spec`:**
- `selector`: define os Pods-alvo que devem receber tráfego
- `ports`: define as portas disponíveis para clientes externos
  - `port`: porta exposta pelo Service
  - `targetPort`: porta do container que será atingida internamente

```yaml
apiVersion: v1
kind: Service
metadata:
  name: meu-service
spec:
  selector:
    app: minha-app
  ports:
    - port: 80
      targetPort: 8080
```

---

### Exemplo 3: Deployment

Cria um `Deployment` com 3 réplicas de Pods baseados na imagem do NGINX.

**Descrição do `spec`:**
- `replicas`: define quantos Pods devem ser criados
- `selector`: indica os Pods que o Deployment deve gerenciar (baseado em label)
- `template`: define o template do Pod a ser replicado
  - `metadata.labels`: garante correspondência com o `selector`
  - `spec.containers`: especifica os containers de cada Pod criado

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: meu-deployment
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
        - name: nginx
          image: nginx:1.21
```

---

## ❓ Perguntas para Reflexão

1. Qual a diferença entre uma lista e um dicionário em YAML?
2. O que pode causar erro ao interpretar um arquivo YAML?
3. Qual a função do campo `metadata.labels`?
4. O que muda na `spec` de acordo com o tipo de objeto (`kind`)?
5. Por que o campo `apiVersion` é importante?

---

## 📋 Exercício Prático

### Parte 1 – Prática de YAML
1. Crie um arquivo `exercicio.yaml` com o seguinte conteúdo:
   - Um mapa contendo seus dados fictícios
   - Uma lista de 3 tecnologias
   - Uma lista de dicionários com 2 pessoas

2. Valide esse arquivo com:
```bash
python3 -c "import yaml, sys; yaml.safe_load(sys.stdin)" < exercicio.yaml
```

(opcional: use o VSCode com extensão YAML + Kubernetes para validação)

---

### Parte 2 – Anatomia de Manifestos

1. Crie um manifesto do tipo `Pod` com a imagem `busybox`, que execute o comando `sleep 3600`
2. Crie um manifesto do tipo `Service` expondo esse Pod via `ClusterIP`
3. Aplique os manifestos no cluster com `kubectl apply -f`

---

## 💡 Para a próxima aula

- Tópico: Serviços e Rede no Kubernetes
  - Tipos de Service: ClusterIP, NodePort, LoadBalancer, Headless
  - Como funciona o roteamento interno de rede com kube-proxy
  - Relação entre Service, Pod e Endpoint

---
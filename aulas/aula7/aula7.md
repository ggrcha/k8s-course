# Kubernetes Básico - Aula 7: ConfigMaps e Secrets

---

## 📅 Objetivos da Aula

- Entender o que são `ConfigMaps` e `Secrets`
- Saber como criar e consumir configurações em Pods
- Aprender as formas de injeção: variáveis de ambiente, argumentos, arquivos
- Praticar com exemplos reais

---

## ✍️ Resumo Teórico

### 🔹 Por que separar configuração do código?

Separar a configuração da aplicação torna os contêiners **mais reutilizáveis** e **portáteis**. Mudanças em ambientes (ex: URLs, senhas, chaves, etc.) podem ser feitas **sem reconstruir a imagem do container**.

---

### 🔑 O que é um `ConfigMap`?

Um `ConfigMap` é um objeto do Kubernetes usado para armazenar **dados de configuração em texto simples** (strings).

➜ Pode conter pares chave-valor, arquivos de configuração, scripts, variáveis de ambiente etc.

#### ✅ Formas de uso:

- Como variáveis de ambiente em Pods
- Como arquivos montados como volume
- Como argumentos na linha de comando

---

### 🔒 O que é um `Secret`?

Um `Secret` armazena **informações sensíveis codificadas em base64** (ex: senhas, tokens, chaves).

➜ Similar ao `ConfigMap`, mas com foco em segurança:

- Não aparece em `kubectl describe` completo
- Pode ser consumido por variáveis de ambiente ou como arquivo
- Requer codificação em base64 (não é criptografado por padrão)

---

### 🔑 Tipos de `Secret` no Kubernetes

| Tipo                    | Descrição                                                                 |
|-------------------------|---------------------------------------------------------------------------|
| `Opaque`                | Tipo genérico para dados arbitrários codificados em base64 (padrão).     |
| `kubernetes.io/basic-auth` | Armazena credenciais `username` e `password`.                          |
| `kubernetes.io/ssh-auth`   | Armazena chave privada SSH (campo obrigatório: `ssh-privatekey`).      |
| `kubernetes.io/tls`        | Armazena certificados TLS (`tls.crt` e `tls.key`).                      |
| `kubernetes.io/dockerconfigjson` | Armazena credenciais para registro de imagens Docker.           |

> 🔎 Referência: [Types of Secrets - Kubernetes Docs](https://kubernetes.io/docs/concepts/configuration/secret/#secret-types)

---

### ⚠️ Possíveis falhas de segurança no uso de Secrets

Apesar de sua finalidade, `Secrets` **não são seguros por padrão**. Algumas armadilhas comuns:

1. **Armazenamento em texto codificado (não criptografado):**
   - Por padrão, são armazenados no etcd codificados em base64, mas **não criptografados**.
   - Para segurança real, é necessário **habilitar criptografia em repouso** no cluster.

2. **Exposição via RBAC incorreto:**
   - Usuários com permissões inadequadas podem acessar Secrets facilmente via `kubectl get secret`.

3. **Exposição no ambiente do container:**
   - Ao injetar Secrets como variáveis de ambiente, **qualquer processo no container** pode ler os valores.

4. **Logs ou dumps de memória:**
   - Aplicações podem logar acidentalmente valores sensíveis.

5. **Volumes de Secret montados como arquivos:**
   - Embora mais seguros que variáveis de ambiente, ainda requerem atenção com permissões de leitura.

---

### 🔐 Boas práticas para uso de Secrets

- Prefira **montar Secrets como volumes** em vez de usar variáveis de ambiente
- Use `defaultMode: 0400` para restringir a leitura ao processo correto
- Sempre aplique **RBAC com o princípio do menor privilégio**
- Habilite a **criptografia de dados em repouso no etcd**
- Use ferramentas como **Vault, Sealed Secrets ou External Secrets Operator** para controle avançado, rotação e auditoria

🔗 Referência: [Securing Secrets - Kubernetes Docs](https://kubernetes.io/docs/concepts/configuration/secret/#security-properties)

---

## 🔍 Exemplo prático completo: Pod + ConfigMap + Secret

### 💡 Objetivo

Criar um Pod que:

1. Lê o valor de uma chave `mensagem` de um ConfigMap  
2. Lê a chave `segredo` de um Secret  
3. Imprime essas informações no log

---

### 📚 1. Criando o ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: config-demo
data:
  mensagem: "Bem-vindo à Aula 7 de Kubernetes!"
```

---

### 🔐 2. Criando o Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-demo
stringData:
  segredo: "valor-super-secreto"
```

---

### 🛋️ 3. Criando o Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod
spec:
  containers:
    - name: leitor
      image: busybox
      command: ["sh", "-c"]
      args:
        - |
          echo "Mensagem: $MENSAGEM"
          echo "Segredo: $SEGREDO"
          sleep 3600
      env:
        - name: MENSAGEM
          valueFrom:
            configMapKeyRef:
              name: config-demo
              key: mensagem
        - name: SEGREDO
          valueFrom:
            secretKeyRef:
              name: secret-demo
              key: segredo
```

---

## ✏️ Exercício Prático

1. Crie os manifests acima (ConfigMap, Secret e Pod)
2. Aplique com:

```bash
kubectl apply -f <arquivo>.yaml
```

3. Verifique os logs com:

```bash
kubectl logs demo-pod
```

4. Verifique o valor do Secret com:

```bash
kubectl get secret secret-demo -o yaml
```

5. (Opcional) Refaça usando `volumes` ao invés de variáveis de ambiente

---

## ❓ Perguntas para Reflexão

1. Por que é importante separar configuração do código?
2. Qual diferença entre `ConfigMap` e `Secret`?
3. Quais riscos estão associados ao uso de Secrets?
4. Como proteger dados sensíveis em um cluster Kubernetes?
5. O que acontece se o ConfigMap ou Secret referenciado não existir?

---

## 📄 Para a próxima aula

**Tópico: Volumes e PersistentVolumes**

- Volumes temporários vs persistentes  
- Reuso de dados entre Pods  
- Provisionamento dinâmico  
- StatefulSets e armazenamento

🧪 **Tarefa preparatória:** Acesse [https://go.dev/learn/](https://go.dev/learn/) e conclua o tutorial básico de Golang no site oficial da linguagem. Isso ajudará nos exemplos práticos com aplicativos reais nas próximas aulas.

---

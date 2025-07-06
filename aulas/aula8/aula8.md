# Kubernetes Básico - Aula 8: Volumes e PersistentVolumes

---

## 📅 Objetivos da Aula

- Entender por que volumes existem no Kubernetes
- Diferenciar volumes efêmeros e persistentes
- Conhecer `Volume`, `PersistentVolume` e `PersistentVolumeClaim`
- Aprender a montar volumes em Pods
- Praticar com exemplos reais

---

## ✍️ Resumo Teórico

### 🔹 Por que volumes existem?

Por padrão, o sistema de arquivos de um container **é descartável**:
- Se o Pod reinicia, os dados salvos desaparecem.
- Se o container é reescalado ou migrado, nada persiste.

**Volumes** resolvem esse problema criando **espaços de armazenamento persistente** ou pelo menos compartilhados entre containers.

---

### 🔸 Volumes efêmeros

São volumes que **só existem enquanto o Pod existe**.
São úteis para compartilhamento de dados entre containers no mesmo Pod.

✅ Exemplos:
- `emptyDir`: diretório vazio criado no nó
- `configMap`: montado como arquivos
- `secret`: montado como arquivos

---

### 🔸 Volumes persistentes

São volumes que **persistem mesmo após o Pod ser destruído**.
Permitem guardar dados de longo prazo.

✅ Usados com:

- `PersistentVolume (PV)`: recurso que representa um volume físico ou lógico no cluster.
- `PersistentVolumeClaim (PVC)`: pedido de uso do volume feito pelo Pod.

---

### 🗄️ O que é um `PersistentVolume`?

Um `PersistentVolume` (PV) é um recurso que define **um pedaço de armazenamento provisionado no cluster**, que pode ser:

- Um disco físico montado no nó
- Um volume em nuvem (EBS, Azure Disk, GCE PersistentDisk)
- Um servidor NFS

Ele é **independente dos Pods** e fica disponível para ser reclamado pelos `PersistentVolumeClaims`.

---

### 📘 Exemplo de manifesto de `PersistentVolume`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-exemplo
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/dados
```

📌 **Explicação:**
- `capacity`: tamanho do volume
- `accessModes`: como ele pode ser montado (ex.: leitura e escrita por um nó)
- `hostPath`: caminho no nó (útil apenas em clusters de teste)

---

### 💡 Fluxo resumido

1. O administrador cria um PV.
2. O desenvolvedor cria um PVC.
3. O Kubernetes liga os dois.
4. O Pod usa o PVC como volume.

---

### 📘 Exemplo prático: PVC + Pod

#### 1. Criando o PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: meu-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

---

#### 2. Criando o Pod que monta o PVC

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-pvc
spec:
  containers:
    - name: app
      image: busybox
      command: ["sh", "-c"]
      args:
        - echo 'Persistindo dado...' > /dados/arquivo.txt && sleep 3600
      volumeMounts:
        - mountPath: "/dados"
          name: meu-volume
  volumes:
    - name: meu-volume
      persistentVolumeClaim:
        claimName: meu-pvc
```

---

### 📂 Exemplo de `emptyDir`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-emptydir
spec:
  containers:
    - name: writer
      image: busybox
      command: ["sh", "-c"]
      args:
        - echo 'Arquivo temporário' > /cache/temp.txt && sleep 3600
      volumeMounts:
        - mountPath: "/cache"
          name: cache-volume
  volumes:
    - name: cache-volume
      emptyDir: {}
```

---

### ⚠️ Diferença importante

| Volume             | Persistência      | Compartilhamento                  |
|--------------------|-------------------|-----------------------------------|
| `emptyDir`         | Até o Pod morrer  | Entre containers no Pod          |
| `PersistentVolume` | Sobrevive ao Pod  | Entre Pods (se reusado via PVC)  |

---

## ✏️ Exercício Prático

1. Crie um `PersistentVolume` chamado `pv-aula8` com 1Gi usando `hostPath`.
2. Crie um PVC chamado `dados-pvc` que se vincule ao PV.
3. Crie um Pod que monte esse PVC em `/data` e grave um arquivo.
4. Delete o Pod e recrie outro que monte o mesmo PVC.
5. Verifique se o arquivo permanece.

---

## ❓ Perguntas para Reflexão

1. Por que volumes são necessários em aplicações stateful?
2. Qual a diferença entre `emptyDir` e `PersistentVolume`?
3. Como ocorre a ligação entre PVC e PV?
4. Quais tipos de acesso podem ser declarados no PVC?
5. Quando usar um `emptyDir` em vez de armazenamento persistente?

---

## 📄 Para a próxima aula

**Tópico: Operadores e Aplicações que consomem a API Kubernetes**

- O que são Operators
- Como aplicações interagem com o Kubernetes via client-go
- Exemplos de casos de uso reais

---

🧪 **Tarefa preparatória:**
Estude os materiais abaixo para se familiarizar com o uso de Go em aplicações que interagem com Kubernetes:

- Introdução ao Go: [https://go.dev/learn/](https://go.dev/learn/)
- Conceitos básicos de client-go: [https://github.com/kubernetes/client-go](https://github.com/kubernetes/client-go)
- Criando seu primeiro operador com kubebuilder: [https://book.kubebuilder.io/quick-start.html](https://book.kubebuilder.io/quick-start.html)

---

Se possível, já instale o Go e configure seu ambiente de desenvolvimento.

---
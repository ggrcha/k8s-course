# Kubernetes Básico - Aula 4: Outros Controladores de Workload

---

## 📅 Objetivos da Aula

- Entender o papel de controladores especializados além do Deployment
- Conhecer os tipos: `DaemonSet`, `StatefulSet`, `Job` e `CronJob`
- Saber quando usar cada tipo de controlador com base em casos de uso reais
- Exercitar a criação de objetos com características específicas

---

## ✏️ Resumo Teórico

### O que são Controladores no Kubernetes?

Controladores são **componentes responsáveis por garantir que o estado atual do cluster se aproxime do estado desejado**, conforme declarado pelos usuários. Eles monitoram constantemente os recursos do cluster (como Pods, Nodes, etc.) e realizam **ações automáticas de reconciliação** quando detectam desvios.

Por exemplo:
- Se você disser que quer 3 réplicas de um Pod, o controlador responsável vai garantir que elas existam, criando ou removendo instâncias conforme necessário.
- Se um Pod falhar, o controlador decide se deve reiniciá-lo, substituí-lo ou mantê-lo parado, dependendo do tipo de controlador em uso.

Cada tipo de workload (como aplicação web, banco de dados, tarefa agendada) pode ser gerenciado por um controlador diferente, ideal para aquele caso.

## 🧩 Hierarquia e Tipos de Controladores no Kubernetes

```
                        +------------------------+
                        |     Controladores      |
                        |     (Controllers)      |
                        +-----------+------------+
                                    |
    +-------------------------------+----------------------------------+
    |               |                   |                 |            |
    |               |                   |                 |            |
+--------+    +-------------+     +------------+     +---------+  +-------------+
| ReplicaSet |    Deployment |     StatefulSet |     DaemonSet |     Job / CronJob
+--------+    +-------------+     +------------+     +---------+  +-------------+
    |              |                   |                 |            |
    |              |                   |                 |            |
    |      Gerencia rollout      Pods com nome      1 Pod por    Execução única
    |     e versionamento       fixo e disco      Node (ou grupo)   ou agendada
    |                           persistente
    |
  Garante número fixo
  de réplicas de Pods
```

> 💡 **Nota**:  
> - `Deployment` gerencia internamente um `ReplicaSet`  
> - `StatefulSet` é usado quando **ordem, identidade e persistência** importam  
> - `DaemonSet` roda 1 Pod **por Node** (útil para agentes)  
> - `Job` executa uma tarefa **uma vez**, enquanto `CronJob` **agenda tarefas recorrentes**

### DaemonSet

- Um DaemonSet garante que **um Pod seja executado em todos (ou em alguns) nós do cluster**
- Ideal para **tarefas de sistema**, como coleta de logs, monitoramento, agentes de rede ou antivírus
- Quando um novo nó entra no cluster, o DaemonSet automaticamente agenda um Pod para ele

**Exemplo de uso:** Prometheus Node Exporter, Fluentd, agentes do Datadog

**Exemplo de manifesto:**
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: exporter
  template:
    metadata:
      labels:
        app: exporter
    spec:
      containers:
      - name: exporter
        image: prom/node-exporter
```

🔗 [DaemonSet - Kubernetes Docs](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

---

### StatefulSet

- É usado para aplicações **com identidade persistente**, como bancos de dados e sistemas distribuídos que precisam manter consistência entre réplicas
- Cada Pod gerado por um StatefulSet recebe:
  - um **nome fixo** baseado no nome do recurso (ex: `meu-banco-0`, `meu-banco-1`)
  - um **volume persistente dedicado** (ex: `pvc-meu-banco-0`) que **não é destruído** mesmo se o Pod for recriado
  - uma **ordem de criação** e, se necessário, **ordem de terminação**, o que é importante em aplicações que exigem inicialização coordenada
- O `StatefulSet` depende de um **Service Headless** (sem ClusterIP) para fornecer **resolução de DNS estável por Pod**

**Quando usar:**
- Bancos de dados como MySQL, PostgreSQL, MongoDB, Cassandra
- Filas como RabbitMQ ou Kafka
- Aplicações que precisam de identificação estável, como clusters Elasticsearch

**Por que não usar um Deployment neste caso?**
- Embora seja possível usar um `Deployment` com múltiplos Pods acessando um banco de dados, isso só é viável quando **os Pods não precisam ter identidade ou armazenamento persistente exclusivo**
- O `Deployment` cria réplicas intercambiáveis — os Pods podem ser substituídos a qualquer momento com nomes diferentes e volumes reaproveitados
- Em aplicações como clusters de banco de dados, cada instância pode precisar:
  - de um nome fixo para se comunicar com as outras instâncias
  - de um volume com dados que persistam entre reinicializações
  - de uma ordem específica de boot (por exemplo: primário antes dos réplicas)
- Nestes casos, **StatefulSet é o único recurso que oferece tais garantias**

**Como o manifesto se materializa no cluster:**
- Ao aplicar o manifesto, o Kubernetes cria um Pod por vez, nomeado com sufixo ordinal
- Para cada Pod, é criado um PersistentVolumeClaim associado

```bash
kubectl get pods -l app=banco
kubectl get pvc
```

**Exemplo de manifesto (simplificado):**
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: meu-banco
spec:
  serviceName: "banco"
  replicas: 2
  selector:
    matchLabels:
      app: banco
  template:
    metadata:
      labels:
        app: banco
    spec:
      containers:
      - name: mysql
        image: mysql:8
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "senha"
        volumeMounts:
        - name: dados
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: dados
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```

🔗 [Documentação oficial do StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

---

### Job

- Um Job executa **uma tarefa única até a conclusão com sucesso**
- Ideal para scripts, migrações de dados, compressões, backups
- O controlador **garante a execução até o fim**, mesmo que o Pod falhe e precise ser reiniciado

**Exemplo de manifesto:**
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: job-exemplo
spec:
  template:
    spec:
      containers:
      - name: job
        image: busybox
        command: ["echo", "Tarefa executada!"]
      restartPolicy: OnFailure
```

🔗 [Job - Kubernetes Docs](https://kubernetes.io/docs/concepts/workloads/controllers/job/)

---

### CronJob

- Um CronJob agenda Jobs recorrentes
- Usa a sintaxe padrão de cron (minuto, hora, dia, mês, semana)
- Ideal para tarefas programadas: limpeza de logs, backups periódicos, envio de relatórios

**Exemplo de manifesto:**
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cron-exemplo
spec:
  schedule: "*/5 * * * *"  # A cada 5 minutos
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron
            image: busybox
            command: ["echo", "Executando cron job"]
          restartPolicy: OnFailure
```

🔗 [CronJob - Kubernetes Docs](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

---

## ❓ Perguntas para Reflexão

1. Quando usar um DaemonSet em vez de um Deployment?
2. Qual a principal característica que diferencia um StatefulSet de um Deployment?
3. Por que o Job precisa de restartPolicy: OnFailure?
4. Em que situações você escolheria um CronJob ao invés de um Job?
5. O que acontece se um Pod de um DaemonSet for deletado manualmente?

---

## 📋 Exercício Prático

### Parte 1 - DaemonSet
1. Crie um DaemonSet com a imagem `busybox` executando o comando `sleep 3600`
2. Verifique se ele foi criado em todos os nós:
```bash
kubectl get daemonsets
kubectl get pods -o wide
```

### Parte 2 - Job e CronJob
3. Crie um Job que imprima "Hello from Job!" no log
4. Aguarde a execução e inspecione os logs:
```bash
kubectl logs job/job-exemplo
```
5. Crie um CronJob que imprima "Executando CronJob" a cada minuto
6. Espere 2 execuções e liste os Jobs criados:
```bash
kubectl get cronjobs
kubectl get jobs
```

---

## 💡 Para a próxima aula

- Tópico: Recursos e Networking no Kubernetes
  - Services: ClusterIP, NodePort, LoadBalancer
  - Endpoints e discovery de rede
  - kube-proxy e roteamento

- Reflita: como os Pods se comunicam entre si? Como expor serviços fora do cluster?

---
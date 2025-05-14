# Resumo: Virtualização, Containers e Orquestração

---

## Virtualização e Máquinas Virtuais

Virtualização é a tecnologia que permite executar múltiplos sistemas operacionais independentes em uma única máquina física, criando ambientes isolados chamados de Máquinas Virtuais (VMs). Cada VM simula todo o hardware necessário e possui seu próprio sistema operacional, consumindo consideráveis recursos de memória e processamento.

Essa tecnologia foi essencial para otimizar o uso de servidores físicos e reduzir custos de infraestrutura a partir dos anos 2000.

🔗 [Leia mais: VMware - What is Virtualization?](https://www.vmware.com/topics/glossary/content/virtualization.html)

---

## O Surgimento dos Containers

Enquanto a virtualização tradicional virtualiza o hardware, os containers surgiram para virtualizar apenas o nível da aplicação. Um container embala uma aplicação junto com suas dependências, mas compartilha o mesmo sistema operacional do host, o que o torna muito mais leve e rápido do que uma máquina virtual.

O Docker, lançado em 2013, popularizou o conceito de containers ao facilitar sua criação, gerenciamento e distribuição. Ele trouxe uma padronização para o empacotamento de aplicações em containers portáveis.

🔗 [Leia mais: Docker - What is a Container?](https://docs.docker.com/get-started/overview/)

---

## Evolução dos Containers: Alternativas ao Docker

Após a popularização do Docker, surgiram outras alternativas e padrões para gerenciamento de containers:

- A ***OCI*** - Open Container Iniciative - criou o padrão para que qualquer container funcione em qualquer lugar, não importa quem construiu ou quem está rodando.
- **CRI-O:** Uma implementação de runtime de containers para o Kubernetes, mais leve e compatível com a especificação OCI.
- **containerd:** Um runtime independente originalmente extraído do Docker, hoje mantido pela CNCF.
- **Podman:** Ferramenta de containers sem necessidade de daemon, focada em segurança e compatibilidade.

Essas alternativas surgiram tanto para diversificar o ecossistema quanto para atender exigências de segurança, desempenho e integração com orquestradores.

🔗 [Leia mais: CNCF - Cloud Native Landscape: Runtimes](https://landscape.cncf.io/category=container-runtimes)

---

## A Necessidade de Orquestração de Containers

Com o crescimento do uso de containers em produção, as empresas perceberam que precisavam de soluções para:

- Implantar centenas (ou milhares) de containers com facilidade
- Garantir alta disponibilidade
- Fazer atualizações sem downtime
- Escalar aplicações automaticamente
- Recuperar containers que falharam
- Balancear carga entre containers

Esses desafios deram origem à necessidade de **orquestradores de containers**, capazes de gerenciar automaticamente aplicações em grande escala.

---

## Docker Swarm e Kubernetes

**Docker Swarm** foi a primeira tentativa do próprio Docker de oferecer orquestração nativa. Ele permitia criar clusters de containers de forma simples, mas apresentava limitações em cenários mais complexos de escalabilidade, rede e automação.

Em paralelo, o **Kubernetes** foi criado em 2014 por engenheiros do Google, baseado nas experiências com sistemas internos como o Borg e o Omega.  
O Kubernetes se destacou rapidamente pela sua capacidade avançada de:

- Autocorreção de aplicações
- Escalonamento horizontal automático
- Gerenciamento declarativo do estado dos recursos
- Rede e armazenamento nativos
- Ecossistema extensível (plugins, operadores, etc.)

Hoje, o Kubernetes é o padrão de mercado para orquestração de containers.

🔗 [Leia mais: Kubernetes Official Site - What is Kubernetes?](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

---

## O que é um Pod?

No Kubernetes, um **Pod** é a menor unidade executável que você pode criar ou gerenciar.  
Um Pod representa **um ou mais containers** que compartilham a mesma rede (IP e portas) e armazenamento.  
Normalmente, um Pod contém **apenas um container**, mas em casos específicos pode conter vários containers que precisam estar muito próximos (por exemplo, compartilhando arquivos via volume).

🔗 [Leia mais: Kubernetes - Pods Concept](https://kubernetes.io/docs/concepts/workloads/pods/)

---

## Qual a principal função de um Nó (Node) em um cluster Kubernetes?

Um **Node** é uma máquina física ou virtual que executa aplicações em containers no Kubernetes.  
Ele oferece os recursos de **CPU, memória, armazenamento e rede** necessários para que os Pods rodem.  
Cada Node é gerenciado pela **Control Plane** do Kubernetes, e executa componentes como:

- **kubelet** (o agente que fala com o API Server)
- **kube-proxy** (para networking)
- **runtime de container** (ex: containerd, cri-o)

🔗 [Leia mais: Kubernetes - Nodes](https://kubernetes.io/docs/concepts/architecture/nodes/)

---

## O que é um Manifesto YAML e sua importância?

No Kubernetes, os recursos (Pods, Services, Deployments, etc.) são definidos através de **arquivos de configuração YAML**, chamados de **Manifestos**.  
Esses manifestos descrevem **o estado desejado** de um recurso.  
O Kubernetes lê esses manifestos e usa seu mecanismo de **reconciliation** para garantir que o estado real no cluster corresponda ao que foi definido.

Sem os manifestos, a operação de clusters seria manual e inconsistente — com os manifestos, tudo é **declarativo**, **auditável** e **automatizável**.

🔗 [Leia mais: Kubernetes - Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)

---


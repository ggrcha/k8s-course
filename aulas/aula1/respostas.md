# Respostas Sugeridas - Perguntas de Reflexão

---

## 1. Qual é o papel principal do Kubernetes em um ambiente moderno de infraestrutura?

O Kubernetes é uma plataforma de orquestração de containers que automatiza a implantação, o gerenciamento, o escalonamento e a operação de aplicações containerizadas. Ele ajuda a garantir que as aplicações sejam executadas de forma resiliente, escalável e eficiente.

🔗 [Saiba mais: Kubernetes - What is Kubernetes? (kubernetes.io)](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

---

## 2. Explique com suas palavras a diferença entre containers e máquinas virtuais. Qual das abordagens consome menos recursos e por quê?

Máquinas virtuais (VMs) virtualizam hardware completo, incluindo sistema operacional, enquanto containers virtualizam apenas a aplicação e suas dependências, usando o kernel do sistema operacional anfitrião. Containers são mais leves e rápidos que VMs, consumindo menos CPU, memória e armazenamento.

🔗 [Saiba mais: Containers vs VMs - Docker Documentation](https://docs.docker.com/engine/docker-overview/#what-is-a-container)

---

## 3. O que é um Pod no Kubernetes e por que ele é a menor unidade gerenciável pelo K8s?

Um Pod é a menor unidade implantável no Kubernetes e representa uma ou mais aplicações (containers) que compartilham o mesmo ambiente de rede e armazenamento. O Kubernetes sempre gerencia containers dentro de Pods e não diretamente os containers individuais.

🔗 [Saiba mais: Kubernetes - Pods Concept (kubernetes.io)](https://kubernetes.io/docs/concepts/workloads/pods/)

---

## 4. Qual a função principal dos Nodes em um cluster Kubernetes?

Nodes são as máquinas (físicas ou virtuais) que executam as aplicações containerizadas. Cada Node contém os serviços necessários para rodar os Pods, como o kubelet (agente do Kubernetes) e o runtime de containers.

🔗 [Saiba mais: Kubernetes - Nodes (kubernetes.io)](https://kubernetes.io/docs/concepts/architecture/nodes/)

---

## 5. O que é um Manifesto YAML e por que ele é tão importante na operação de um cluster Kubernetes?

Um Manifesto YAML é um arquivo de configuração que descreve o estado desejado de um recurso Kubernetes, como Pods, Services, Deployments, entre outros. O Kubernetes lê esses manifestos para criar e manter o estado dos objetos no cluster, garantindo a automação e consistência da infraestrutura.

🔗 [Saiba mais: Kubernetes - Configuration Best Practices (kubernetes.io)](https://kubernetes.io/docs/concepts/configuration/overview/)

---
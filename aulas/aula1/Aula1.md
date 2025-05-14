# Kubernetes Básico - Aula 1: Introdução ao Kubernetes

---

## 🤓 Cursos auxiliares

1. Preparatório CKAD -  https://www.udemy.com/share/101Eno3@9NDEG8-Y3KfjZb7TNHWfDBL4FijpT08RsbPQ_5z7UK2UEJa61x-jQgjkkAtmf_Sz/

2. Docker - https://www.udemy.com/course/learn-docker/?couponCode=25BBPMXACCAGE1

---

## ✏️ Perguntas para Reflexão

Responda essas perguntas para entender melhor os conceitos básicos do Kubernetes discutidos em aula:

1. **Qual é o papel principal do Kubernetes em um ambiente moderno de infraestrutura?**

2. **Explique com suas palavras a diferença entre containers e máquinas virtuais. Qual das abordagens consome menos recursos e por quê?**

3. **O que é um Pod no Kubernetes e por que ele é a menor unidade gerenciável pelo K8s?**

4. **Qual a função principal dos Nodes em um cluster Kubernetes?**

5. **O que é um Manifesto YAML e por que ele é tão importante na operação de um cluster Kubernetes?**

---

## 🛠️ Exercício Prático

### Parte 1: Primeiro YAML

1. Crie seu primeiro manifesto YAML para criar um Pod simples com a imagem do nginx:
   - Nome do Pod: `meu-primeiro-pod`
   - Imagem: `nginx:1.21`

**Exemplo (incompleto propositalmente para você completar):**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-primeiro-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
```

2. Aplique esse YAML no cluster e verifique se o Pod está rodando:

```bash
kubectl apply -f meu-pod.yaml
kubectl get pods
```

3. Acesse o Pod criado usando o comando abaixo para verificar seu funcionamento interno:

```bash
kubectl exec -it meu-primeiro-pod -- bash
```

---

## 🔍 Desafio para Praticar Troubleshooting

Você recebeu o seguinte YAML que apresenta um erro proposital:

Arquivo: `pod-bug.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-erro
spec:
  containers:
  - name: nginx
    image: nginx:aula1
```

### Passos:

1. Aplique o YAML no cluster:

```bash
kubectl apply -f pod-bug.yaml
```

2. Identifique o problema usando:

```bash
kubectl describe pod pod-com-erro
kubectl logs pod-com-erro
```

3. Explique o que aconteceu e corrija o YAML para que o Pod suba corretamente.

---

## 📚 Para a Próxima Aula

- Estude os principais componentes de um cluster Kubernetes:
  - `kube-apiserver`
  - `kube-scheduler`
  - `kube-controller-manager`
  - `kubelet`
  - `kube-proxy`

- Traga suas dúvidas para discutirmos juntos!

---
## ❓ Perguntas para Reflexão

1. O que é o `kube-apiserver` e por que ele é considerado o "coração" do cluster?
2. O que o `kube-scheduler` leva em conta para decidir onde rodar um novo Pod?
3. Qual a importância do `etcd` para a consistência do cluster?
4. O que aconteceria se o `kubelet` de um Node falhasse?
5. Por que a separação entre Control Plane e Node é importante?

---

1. O `kube-apiserver` é a interface central de comunicação no cluster. Ele valida, autentica e roteia todas as requisições.
2. O `kube-scheduler` considera CPU, memória, afinidades, restrições, zonas, etc.
3. O `etcd` guarda o estado desejado do cluster. Se corrompido, o cluster perde sua referência.
4. O Node fica `NotReady` e o Kubernetes tenta redistribuir os Pods automaticamente.
5. Para garantir resiliência. Se os Nodes falham, o plano de controle continua operando.
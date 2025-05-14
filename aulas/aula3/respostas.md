## ✅ Respostas Sugeridas

1. O Deployment permite atualizações seguras, rollback e escalabilidade automática, enquanto Pods diretos não oferecem esses controles.
2. O ReplicaSet detecta a redução no número de réplicas e cria automaticamente um novo Pod para manter o estado desejado.
3. Escalar via Deployment mantém o controle centralizado e atualizado; escalar diretamente o ReplicaSet quebra o ciclo de atualização e rollback controlado.
4. O Pod por si só não possui lógica de monitoramento ou recriação — se ele falha, morre. O ReplicaSet (ou outro controlador) é quem garante resiliência.
5. O Deployment declara o número de réplicas, a imagem e a estratégia de atualização — o Kubernetes garante que o estado real siga esse estado desejado.

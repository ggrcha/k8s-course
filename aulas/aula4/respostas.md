## ✅ Respostas Sugeridas

1. Quando você precisa garantir um Pod rodando em todos os nós — como agentes de monitoramento ou rede.
2. O StatefulSet mantém a identidade estável dos Pods (nome fixo, ordem, volume fixo), o que é essencial para dados persistentes.
3. Porque a tarefa deve ser reexecutada se falhar — mas não reiniciada continuamente se estiver rodando com sucesso.
4. Quando a tarefa precisa ocorrer em um agendamento fixo — ex: todos os dias às 3h da manhã.
5. O DaemonSet detecta e recria automaticamente o Pod para manter o estado desejado.
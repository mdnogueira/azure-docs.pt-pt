A tabela seguinte descreve cada um dos principais quotas, limites, predefinições e limitações no agendador do Azure.

| Recurso | Descrição de limite |
| --- | --- |
| **Tamanho de tarefa** |O tamanho de tarefas máxima é 16K. Se um PUT ou de um PATCH resulta numa tarefa maior do que estes limites, é devolvido um código de estado pedido incorreto 400. |
| **Tamanho do URL do pedido** |Tamanho máximo do URL do pedido é 2048 carateres. |
| **Tamanho do cabeçalho agregado** |Tamanho do cabeçalho de agregação máximo é 4096 carateres. |
| **Contagem de cabeçalho** |Contagem de cabeçalho máximo é 50 cabeçalhos. |
| **Tamanho do corpo** |Tamanho máximo de corpo é 8192 carateres. |
| **Intervalo de periodicidade** |O intervalo de periodicidade máximo é 18 meses. |
| **Hora a hora de início** |"Hora a hora de início" do máximo é 18 meses. |
| **Histórico de tarefas** |Corpo da resposta máximo armazenado no histórico da tarefa é 2048 bytes. |
| **Frequência** |A quota de frequência máximo predefinido é 1 hora de uma coleção de tarefas gratuitas e 1 minuto uma coleção de tarefas padrão. A frequência máxima é configurável na coleção de tarefas para ser inferior ao máximo. Todas as tarefas na coleção de tarefas estão limitadas o valor definido na coleção de tarefas. Se tentar criar uma tarefa com uma frequência superior à frequência da máxima na coleção de tarefas pedido irá falhar com um código de estado de conflito 409. |
| **Tarefas** |A quota de tarefas máximo predefinido é 5 tarefas numa coleção de tarefas gratuitas e 50 tarefas numa coleção de tarefas padrão. O número máximo de tarefas é configurável na coleção de tarefas. Todas as tarefas na coleção de tarefas estão limitadas o valor definido na coleção de tarefas. Se tentar criar o maior número de tarefas que a quota máxima de tarefas, em seguida, o pedido irá falhar com um código de estado de conflito 409. |
| **Coleções de tarefas** |Número máximo de coleção de tarefas por subscrição é inferior 200.000. |
| **Retenção do histórico de tarefas** |Histórico da tarefa é retido para 2 meses ou até as 1000 últimas execuções. |
| **Retenção de tarefa concluídas e falhadas** |As tarefas concluídas e falhadas são retidas por 60 dias. |
| **Tempo limite** |Não há um tempo de limite de pedido (não configurável) estático de 60 segundos para ações de HTTP. Para mais operações em execução, siga protocolos assíncronos de HTTP; Por exemplo, devolver um 202 imediatamente mas continuar a trabalhar em segundo plano. |


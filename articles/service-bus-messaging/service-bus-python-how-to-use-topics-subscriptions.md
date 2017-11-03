---
title: "Como utilizar os tópicos de Service Bus do Azure com o Python | Microsoft Docs"
description: "Saiba como utilizar os tópicos de Service Bus do Azure e as subscrições do Python."
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 15269f9728e9dc45e6436e53b1859f76d4a7a0c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Como utilizar os tópicos do Service Bus e as subscrições com o Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como utilizar os tópicos e as subscrições do Service Bus. Os exemplos são escritos no Python e utilize o [pacote do SDK do Azure Python][Azure Python package]. Os cenários abrangidos incluem **criação de tópicos e subscrições**, **criar filtros de subscrição**, **envio de mensagens para um tópico**, **receber mensagens de uma subscrição**, e **eliminar tópicos e subscrições**. Para obter mais informações sobre os tópicos e subscrições, consulte o [passos](#next-steps) secção.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Se precisar de instalar o Python ou [pacote do Azure Python][Azure Python package], consulte o [guia de instalação do Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Criar um tópico
O **ServiceBusService** objeto permite-lhe funcionar com tópicos. Adicione o seguinte perto da parte superior de qualquer ficheiro de Python no qual pretende aceder programaticamente ao Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

O código seguinte cria um **ServiceBusService** objeto. Substitua `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` com o seu espaço de nomes real, valor de nome e chave de chave de assinatura de acesso partilhado (SAS).

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Pode obter os valores para o nome da chave SAS e valor a partir do [portal do Azure][Azure portal].

```python
bus_service.create_topic('mytopic')
```

O `create_topic` método também suporta opções adicionais, que permitem-lhe substituir as definições predefinidas da tópico como hora TTL da mensagem ou o tamanho máximo do tópico. O exemplo a seguir define o tamanho máximo de tópico 5 GB e um período de tempo (TTL) valor de 1 minuto de TTL:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar subscrições
Subscrições de tópicos também são criadas com o **ServiceBusService** objeto. As subscrições são denominadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues nos fila virtual da subscrição.

> [!NOTE]
> As subscrições sejam persistentes e continuarão a existir até serem, ou o tópico em que tiver subscrito, são eliminados.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
O filtro **MatchAll** é o filtro predefinido utilizado se não for especificado qualquer filtro na criação de uma nova subscrição. Quando o **MatchAll** filtro é utilizado, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição designada `AllMessages` e utiliza a predefinição **MatchAll** filtro.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode definir filtros que permitem especificar que as mensagens enviadas para um tópico devem aparecer dentro de uma subscrição de tópico específica.

O tipo mais flexível do filtro suportado pelas subscrições é um **SqlFilter**, que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre as expressões que podem ser utilizadas com um filtro de SQL, veja a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Pode adicionar filtros numa subscrição utilizando o **criar\_regra** método o **ServiceBusService** objeto. Este método permite-lhe adicionar novos filtros para uma subscrição existente.

> [!NOTE]
> Porque o filtro predefinido é aplicado automaticamente para todas as novas subscrições, primeiro tem de remover o filtro predefinido ou o **MatchAll** substituirão quaisquer outros filtros pode especificar. Pode remover a regra predefinida, utilizando o `delete_rule` método o **ServiceBusService** objeto.
> 
> 

O exemplo seguinte cria uma subscrição designada `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens com um personalizado `messagenumber` propriedade superior a 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Da mesma forma, o exemplo seguinte cria uma subscrição designada `LowMessages` com um **SqlFilter** que seleciona apenas as mensagens com um `messagenumber` propriedade inferior ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Agora, quando é enviada uma mensagem para `mytopic` sempre é entregue aos subscrever o **AllMessages** subscrição do tópico e entregue seletivamente subscrever o **HighMessages**e **LowMessages** subscrições (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico de barramento de serviço, a aplicação tem de utilizar o `send_topic_message` método o **ServiceBusService** objeto.

O exemplo seguinte mostra como enviar cinco mensagens de teste para `mytopic`. Tenha em atenção que o `messagenumber` varia de acordo com o valor da propriedade de cada mensagem no iteração do ciclo (Isto determina quais as subscrições receberem):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB. Para mais informações sobre as quotas, consulte [quotas de Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
As mensagens são recebidas a partir de uma subscrição utilizando o `receive_subscription_message` método no **ServiceBusService** objeto:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

As mensagens são eliminadas da subscrição como estes são lidos quando o parâmetro `peek_lock` está definido como **falso**. Pode ler (peek) e bloquear a mensagem sem eliminá-lo a partir da fila, definindo o parâmetro `peek_lock` para **verdadeiro**.

O comportamento de ler e eliminar a mensagem como parte da operação de receção é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Porque o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha.

Se o `peek_lock` parâmetro está definido como **verdadeiro**, a receção torna-se uma operação de duas fases, o que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazena a mesma forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao chamar `delete` método no **mensagem** objeto. O `delete` método marca a mensagem como consumida e remove a mesma da subscrição.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, em seguida, pode chamar o `unlock` método no **mensagem** objeto. Isto irá fazer com que o Service Bus desbloqueie a mensagem dentro da subscrição e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na subscrição e, se a aplicação não conseguir processar a mensagem antes do bloqueio de tempo limite expirar (por exemplo, se a falha da aplicação), em seguida, Service Bus desbloqueia automaticamente a mensagem e torna disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes o `delete` método é denominado, em seguida, a mensagem será reenviada para a aplicação quando esta reiniciar. Isto é frequentemente designado *, pelo menos, uma vez processamento*, ou seja, cada mensagem será processada, pelo menos, uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido utilizando o **MessageId** propriedade da mensagem, que permanecerá constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Os tópicos e subscrições sejam persistentes e tem de ser explicitamente eliminado quer através de [portal do Azure] [ Azure portal] ou através de programação. O exemplo seguinte mostra como eliminar o tópico com o nome `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O código seguinte mostra como eliminar uma subscrição designada `HighMessages` do `mytopic` tópico:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas de tópicos do Service Bus, siga estas ligações para saber mais.

* Consulte [filas, tópicos e subscrições][Queues, topics, and subscriptions].
* Referência para [Sqlexpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 

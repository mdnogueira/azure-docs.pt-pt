---
title: "Como utilizar os tópicos de Service Bus do Azure com Java | Microsoft Docs"
description: "Utilize os tópicos do Service Bus e subscrições no Azure."
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/17/2017
ms.author: sethm
ms.openlocfilehash: 632af7294a7e6766d791d1d9ab08f98308fb2c02
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Como utilizar os tópicos do Service Bus e as subscrições com Java

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este guia descreve como utilizar tópicos do Service Bus e as subscrições. Os exemplos são escritos em Java e utilize o [Azure SDK para Java][Azure SDK for Java]. Os cenários abrangidos incluem **criação de tópicos e subscrições**, **criar filtros de subscrição**, **envio de mensagens para um tópico**, **receber mensagens de uma subscrição**, e **eliminar tópicos e subscrições**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as subscrições do Service Bus?
Os tópicos e as subscrições do Service Bus suportam um modelo de comunicação de mensagens de *publicação/subscrição*. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de um tópico, que funciona como um intermediário.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Ao contrário das filas do Service Bus, em que cada mensagem é processada por um único consumidor, os tópicos e as subscrições proporcionam uma forma de comunicação de “um-para-muitos”, utilizando um padrão de publicação/subscrição. É possível registar várias subscrições num tópico. Quando uma mensagem é enviada para um tópico, é depois disponibilizada para que cada subscrição a processe de forma independente.

Uma subscrição num tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, pode registar regras de filtro para um tópico numa base por subscrição, que lhe permite filtrar/restringir que mensagens para um tópico são recebidas por que subscrições desse tópico.

Tópicos do Service Bus e subscrições permitem-lhe dimensionar para processar um grande número de mensagens através de um grande número de utilizadores e aplicações.

## <a name="create-a-service-namespace"></a>Criar um espaço de nomes de serviço
Para começar a utilizar o Service Bus tópicos e subscrições no Azure, primeiro tem de criar um *espaço de nomes*, que fornece um contentor de âmbito para endereçamento de recursos do Service Bus na sua aplicação.

Para criar um espaço de nomes:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Certifique-se de que instalou o [Azure SDK para Java] [ Azure SDK for Java] antes de criar este exemplo. Se estiver a utilizar Eclipse, pode instalar o [Toolkit do Azure para o Eclipse] [ Azure Toolkit for Eclipse] que inclua o Azure SDK para Java. Em seguida, pode adicionar o **bibliotecas do Microsoft Azure para Java** ao seu projeto:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Adicione o seguinte `import` declarações na parte superior do ficheiro de Java:

```java
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Adicione as bibliotecas do Azure para Java para o caminho de compilação e incluí-la na assemblagem de implementação do projeto.

## <a name="create-a-topic"></a>Criar um tópico
Operações de gestão para tópicos do Service Bus podem ser efetuadas através de **ServiceBusContract** classe. A **ServiceBusContract** objecto é construído com uma configuração apropriada que encapsula o token SAS com permissões para geri-lo, e o **ServiceBusContract** classe é o único ponto de comunicação com o Azure.

O **ServiceBusService** classe fornece métodos para criar, enumerar e eliminar tópicos. O seguinte exemplo mostra como um **ServiceBusService** objeto pode ser utilizado para criar um tópico com o nome `TestTopic`, com um espaço de nomes chamado `HowToSample`:

```java
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
      "HowToSample",
      "RootManageSharedAccessKey",
      "SAS_key_value",
      ".servicebus.windows.net"
      );

ServiceBusContract service = ServiceBusService.create(config);
TopicInfo topicInfo = new TopicInfo("TestTopic");
try  
{
    CreateTopicResult result = service.createTopic(topicInfo);
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Existem métodos em **TopicInfo** que permitem que as propriedades do tópico para ser definido (por exemplo: definir o valor (TTL) predefinido time-to-live a ser aplicado às mensagens enviadas para o tópico). O exemplo seguinte mostra como criar um tópico com o nome `TestTopic` com um tamanho máximo de 5 GB:

```java
long maxSizeInMegabytes = 5120;  
TopicInfo topicInfo = new TopicInfo("TestTopic");  
topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateTopicResult result = service.createTopic(topicInfo);
```

Pode utilizar o **listTopics** método no **ServiceBusContract** objetos para verificar se um tópico com um nome especificado já existe num espaço de nomes do serviço.

## <a name="create-subscriptions"></a>Criar subscrições
Subscrições de tópicos também são criadas com o **ServiceBusService** classe. As subscrições têm um nome e podem ter um filtro opcional que restringe o conjunto de mensagens transmitidas para a fila virtual da subscrição.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se não for especificado nenhum filtro aquando da criação de uma nova subscrição, será utilizado o filtro predefinido **MatchAll**. Quando o **MatchAll** filtro é utilizado, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição com o nome "AllMessages" e utiliza o filtro **MatchAll** predefinido.

```java
SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
CreateSubscriptionResult result =
    service.createSubscription("TestTopic", subInfo);
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode criar filtros que permitem ao âmbito de que as mensagens enviadas para um tópico devem aparecer dentro de uma subscrição de tópico específica.

O tipo mais flexível do filtro suportado pelas subscrições é a [SqlFilter][SqlFilter], que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser utilizadas com um filtro SQL, consulte o [Sqlexpression] [ SqlFilter.SqlExpression] sintaxe.

O exemplo seguinte cria uma subscrição designada `HighMessages` com um [SqlFilter] [ SqlFilter] objeto que seleciona apenas as mensagens com um personalizado **MessageNumber** propriedade é superior a 3:

```java
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Da mesma forma, o exemplo seguinte cria uma subscrição designada `LowMessages` com um [SqlFilter] [ SqlFilter] objeto que seleciona apenas as mensagens com um **MessageNumber** propriedade inferior ou igual a 3:

```java
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Quando agora é enviada uma mensagem para `TestTopic`, sempre é entregue aos subscrever o `AllMessages` subscrição e entregue seletivamente subscrever o `HighMessages` e `LowMessages` subscrições (consoante o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico de barramento de serviço, a aplicação obtém um **ServiceBusContract** objeto. O código seguinte demonstra como enviar uma mensagem o `TestTopic` tópico criado anteriormente dentro de `HowToSample` espaço de nomes:

```java
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

As mensagens enviadas para tópicos de Service Bus são instâncias do [BrokeredMessage] [ BrokeredMessage] classe. [BrokeredMessage][BrokeredMessage]* objetos têm um conjunto de métodos padrão (tais como **setLabel** e **TimeToLive**), um dicionário utilizado para conter personalizado propriedades específicas da aplicação e um corpo de dados arbitrários da aplicação. Uma aplicação pode definir o corpo da mensagem através da transmissão de qualquer objeto serializável para o construtor do [BrokeredMessage][BrokeredMessage]e o adequado **DataContractSerializer** , em seguida, é utilizado para serializar o objeto. Em alternativa, uma **java.io.InputStream** podem ser fornecidos.

O exemplo seguinte mostra como enviar cinco mensagens de teste para o `TestTopic` **MessageSender** obteve no fragmento de código anterior.
Tenha em atenção o **MessageNumber** varia de acordo com o valor da propriedade de cada mensagem no iteração do ciclo (este valor determina quais as subscrições receberem):

```java
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite no número de mensagens contidas num tópico mas não há um limite de tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma subscrição
Para receber mensagens de uma subscrição, utilize um **ServiceBusContract** objeto. Mensagens recebidas podem funcionar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock** (predefinição).

Ao utilizar o **ReceiveAndDelete** modo, receber é uma operação única - ou seja, quando o Service Bus recebe um pedido de leitura de uma mensagem, marca a mensagem como consumida e devolve a mesma à aplicação. **ReceiveAndDelete** modo é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem se ocorrer uma falha. Por exemplo, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falhas antes do processamento-lo. Porque o Service Bus marcou a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, tem estado em falta a mensagem consumida antes da falha.

No **PeekLock** modo, receção torna-se uma operação de duas fases, o que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazena a mesma forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao chamar **eliminar** à mensagem recebida. Quando o Service Bus vê a **eliminar** chamada, marca a mensagem como consumida e remove-o do tópico.

O exemplo seguinte demonstra como podem ser recebidas mensagens e processados utilizando **PeekLock** (o modo predefinido). O exemplo executa um ciclo e processa mensagens no `HighMessages` subscrição e, em seguida, sai quando existem não existem mais mensagens (em alternativa, pode ser definido para aguardar novas mensagens).

```java
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, em seguida, pode chamar o **unlockMessage** método à mensagem recebida (em vez do **deleteMessage** método). Isto faz com que o Service Bus desbloqueie a mensagem na tópico e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada no tópico, e se a aplicação não conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus desbloqueia automaticamente a mensagem e torna está disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes o **deleteMessage** pedido é emitido, em seguida, a mensagem é reenviada para a aplicação quando esta reiniciar. Este processo é frequentemente designado **, pelo menos, uma vez processamento**; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido utilizando o **getMessageId** método da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
A forma como principal para eliminar tópicos e subscrições é utilizar um **ServiceBusContract** objeto. A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente.

```java
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas de filas do Service Bus, consulte [filas do Service Bus, tópicos e subscrições] [ Service Bus queues, topics, and subscriptions] para obter mais informações.

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.filters.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.filters.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png

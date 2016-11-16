---
title: "Utilizar os tópicos do Service Bus com o .NET | Microsoft Docs"
description: "Saiba como utilizar os tópicos e as subscrições do Service Bus com o .NET no Azure. Os exemplos de código são escritos para aplicações .NET."
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b90d2b49807b39bb7a71315877a8e84550efc9cc


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como utilizar os tópicos e as subscrições do Service Bus
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como utilizar os tópicos e as subscrições do Service Bus. Os exemplos são escritos em C# e utilizam as APIs .NET. Os cenários abrangidos incluem a criação de tópicos e subscrições, a criação de filtros de subscrição, o envio de mensagens para um tópico, a receção de mensagens de uma subscrição e a eliminação de tópicos e subscrições. Para obter mais informações sobre os tópicos e as subscrições, veja a secção [Passos seguintes](#next-steps).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o Service Bus
Quando cria uma aplicação que utiliza o Service Bus, deve adicionar uma referência à assemblagem do Service Bus e incluir os espaços de nomes correspondentes. A forma mais fácil de fazê-lo é através da transferência do pacote [NuGet](https://www.nuget.org) adequado.

## <a name="get-the-service-bus-nuget-package"></a>Obter o pacote NuGet do Service Bus
O [Pacote NuGet do Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a forma mais fácil de obter a API do Service Bus e de configurar a sua aplicação com todas as dependências necessárias do Service Bus. Para instalar o pacote NuGet do Service Bus no projeto, realize o seguinte:

1. No Explorador de Soluções, clique com o botão direito em **Referências** e, em seguida, clique em **Gerir Pacotes NuGet**.
2. Localize "Service Bus" e selecione o item **Service Bus do Microsoft Azure**. Clique em **Instalar** para concluir a instalação e, em seguida, feche a caixa de diálogo seguinte:
   
   ![][7]

Está agora preparado para escrever o código para o Service Bus.

## <a name="create-a-service-bus-connection-string"></a>Criar uma cadeia de ligação do Service Bus
O Service Bus utiliza uma cadeia de ligação para armazenar pontos finais e credenciais. Pode colocar a cadeia de ligação num ficheiro de configuração em vez de pré-programar a mesma:

* Ao utilizar os serviços do Azure, é recomendado o armazenamento da cadeia de ligação utilizando o sistema de configuração do serviço do Azure (ficheiros .csdef e .cscfg).
* Ao utilizar os Web sites Azure ou as Virtual Machines do Azure, é recomendado o armazenamento da cadeia de ligação utilizando o sistema de configuração do .NET (por exemplo, o ficheiro Web.config).

Em ambos os casos, pode obter a cadeia de ligação utilizando o método `CloudConfigurationManager.GetSetting`, conforme mostrado posteriormente neste artigo.

### <a name="configure-your-connection-string"></a>Configurar a cadeia de ligação
O mecanismo de configuração do serviço permite alterar dinamicamente as definições de configuração do [Portal do Azure][Portal do Azure] sem reimplementar a aplicação. Por exemplo, adicione uma etiqueta `Setting` ao ficheiro de definição do serviço (**.csdef**), conforme mostrado no exemplo seguinte.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Em seguida, especifique os valores no ficheiro de configuração do serviço (.cscfg).

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Utilize os valores e o nome da chave de Assinatura de Acesso Partilhado (SAS) obtidos no portal, conforme descrito anteriormente.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurar a cadeia de ligação na utilização dos Web sites Azure ou das Virtual Machines do Azure
Na utilização de Web sites ou de Virtual Machines, é recomendada a utilização do sistema de configuração do .NET (por exemplo, Web.config). Armazene a cadeia de ligação utilizando o elemento `<appSettings>`.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Utilize o nome e os valores da chave SAS obtidos no [Portal do Azure][Portal do Azure], conforme descrito anteriormente.

## <a name="create-a-topic"></a>Criar um tópico
Pode efetuar operações de gestão para tópicos e subscrições do Service Bus utilizando a classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Esta classe fornece métodos para criar, enumerar e eliminar tópicos.

O exemplo seguinte constrói um objeto `NamespaceManager` utilizando a classe `CloudConfigurationManager` do Azure com uma cadeia de ligação composta pelo endereço de base de um espaço de nomes do Service Bus e pelas credenciais de SAS adequadas com permissões para a respetiva gestão. Esta cadeia de ligação tem a seguinte forma:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Utilize o exemplo seguinte, tendo em conta as definições de configuração na secção anterior.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Existem sobrecargas do método [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) que permitem definir propriedades do tópico; por exemplo, para definir o valor de TTL (time-to-live) predefinido a ser aplicado às mensagens enviadas para o tópico. Estas definições são aplicadas utilizando a classe [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). O exemplo seguinte mostra como criar um tópico com o nome **TestTopic** com um tamanho máximo de 5 GB e um TTL da mensagem predefinido de 1 minuto.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> Pode utilizar o método [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) nos objetos [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para verificar se um tópico com um nome especificado já existe num espaço de nomes.
> 
> 

## <a name="create-a-subscription"></a>Criar uma subscrição
Também pode criar subscrições de tópicos utilizando a classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). As subscrições têm um nome e podem ter um filtro opcional que restringe o conjunto de mensagens transmitidas para a fila virtual da subscrição.

> [!IMPORTANT]
> Para que uma subscrição possa receber mensagens, tem de criar essa subscrição antes de enviar mensagens para o tópico. Se não existirem subscrições para um tópico, o tópico rejeita essas mensagens.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se não for especificado nenhum filtro aquando da criação de uma nova subscrição, será utilizado o filtro predefinido **MatchAll**. Quando utiliza o filtro **MatchAll**, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição com o nome "AllMessages" e utiliza o filtro **MatchAll** predefinido.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode definir filtros que permitem especificar quais as mensagens enviadas para um tópico a serem apresentadas numa subscrição de tópico específica.

O tipo de filtro mais flexível suportado pelas subscrições é a classe [SqlFilter][SqlFilter], que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre as expressões que podem ser utilizadas com um filtro do SQL, veja a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

O exemplo seguinte cria uma subscrição com o nome **HighMessages** com um objeto [SqlFilter][SqlFilter] que seleciona apenas as mensagens com uma propriedade **MessageNumber** personalizada superior a 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Do mesmo modo, o exemplo seguinte cria uma subscrição com o nome **LowMessages** com um [SqlFilter][SqlFilter] que seleciona apenas as mensagens com uma propriedade **MessageNumber** inferior ou igual a 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Agora quando uma mensagem for enviada para `TestTopic`, será sempre entregue aos destinatários com subscrição do tópico **AllMessages** e entregue seletivamente aos destinatários com subscrições dos tópicos **HighMessages** e **LowMessages** (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico do Service Bus, a sua aplicação cria um objeto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) utilizando a cadeia de ligação.

O código seguinte mostra como criar um objeto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) para o tópico **TestTopic** criado anteriormente utilizando a chamada à API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

As mensagens enviadas para os tópicos do Service Bus são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Os objetos [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) têm um conjunto de propriedades padrão (tais como [Etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário utilizado para reter propriedades personalizadas específicas da aplicação e um corpo de dados arbitrários da aplicação. Uma aplicação pode definir o corpo da mensagem através da transmissão de qualquer objeto serializável para o construtor do objeto [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) e o **DataContractSerializer** adequado é, em seguida, utilizado para serializar o objeto. Em alternativa, pode ser pode ser fornecido um **System.IO.Stream**.

O exemplo seguinte mostra como enviar cinco mensagens de teste para o objeto **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) obtido no exemplo de código anterior. Tenha em atenção que o valor da propriedade [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de cada mensagem varia dependendo da iteração do ciclo (isto determina quais as subscrições que recebem a mesma).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB. Se a criação de partições estiver ativada, o limite superior é mais elevado. Para obter mais informações, consulte [Entidades de mensagens particionadas](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma subscrição
O modo recomendado para receber mensagens de uma subscrição é utilizar um objeto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx). Os objetos [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) podem funcionar em dois modos diferentes: [*ReceiveAndDelete* e *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Na utilização do modo **ReceiveAndDelete**, a receção é uma operação única; ou seja, quando o Service Bus recebe um pedido de leitura para uma mensagem numa subscrição, aquele marca a mensagem como consumida e devolve a mesma à aplicação. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Uma vez que o Service Bus marcou a mensagem como consumida, quando a aplicação reiniciar e começar a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha de sistema.

No modo **PeekLock** (o modo predefinido), o processo de receção torna-se uma operação de duas etapas que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazenar a mesma de forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao atribuir o nome [Concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) à mensagem recebida. Quando o Service Bus vê a chamada **Concluída**, marca a mensagem como consumida e remove a mesma da subscrição.

O exemplo seguinte mostra como as mensagens podem ser recebidas e processadas utilizando o modo **PeekLock** predefinido. Para especificar um valor [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) diferente, pode utilizar outra sobrecarga para [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Este exemplo utiliza a chamada de retorno [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) para processar mensagens à medida que chegam à subscrição **HighMessages**.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Este exemplo configura a chamada de retorno [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) utilizando um objeto [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). A [Conclusão Automática](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) é definida para **falso** para permitir o controlo manual para atribuição do nome [Concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) à mensagem recebida. O [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) é definido para 1 minuto, o que faz com que o cliente aguarde até um minuto antes de terminar a funcionalidade de renovação automática e o cliente efetuar uma nova chamada para procurar mensagens. Este valor da propriedade reduz o número de vezes que o cliente efetua chamadas cobráveis que não obtêm mensagens.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem por qualquer motivo, pode chamar o método [Cancelar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) na mensagem recebida (em vez do método [Concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Tal faz com que o Service Bus desbloqueie a mensagem na subscrição e torna a mesma disponível para ser novamente recebida, quer pela mesma aplicação de consumo quer por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na subscrição e, se a aplicação não conseguir processar a mensagem antes de o tempo limite de bloqueio expirar (por exemplo, no caso de falha da aplicação), o Service Bus desbloqueia automaticamente a mensagem e torna a mesma disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes de ser emitido o pedido [Concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), a mensagem será reenviada para a aplicação quando esta reiniciar. Tal é, frequentemente, designado *Processar Pelo Menos Uma Vez*; ou seja, cada mensagem é processada pelo menos uma vez, contudo, em determinadas situações, a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Tal é, frequentemente, conseguido através da propriedade [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
O exemplo seguinte mostra como eliminar o tópico **TestTopic** do espaço de nomes de serviço **HowToSample**.

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O código seguinte mostra como eliminar uma subscrição designada **HighMessages** do tópico **TestTopic**.

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas dos tópicos e das subscrições do Service Bus, siga estas hiperligações para saber mais.

* [Filas, tópicos e subscrições][Filas, tópicos e subscrições].
* [Exemplo de filtros de tópico][Exemplo de filtros de tópico]
* Referência da API para [SqlFilter][SqlFilter].
* Compile uma aplicação de trabalho que envie e receba mensagens para/de uma fila do Service Bus: [Tutorial de .NET de mensagens mediadas do Service Bus][Tutorial de .NET de mensagens mediadas do Service Bus].
* Exemplos do Service Bus: transfira a partir dos [exemplos do Azure][exemplos do Azure] ou consulte a [descrição geral](service-bus-samples.md).

[Portal do Azure]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
[Exemplo de filtros de tópicos]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Tutorial de .NET de mensagens mediadas do Service Bus]: service-bus-brokered-tutorial-dotnet.md
[Exemplos do Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2



<!--HONumber=Nov16_HO2-->



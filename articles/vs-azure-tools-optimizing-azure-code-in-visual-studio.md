---
title: "Otimizar o seu código do Azure no Visual Studio | Microsoft Docs"
description: "Saiba mais sobre código como Azure otimização as ferramentas do Visual Studio ajudam a tornar o seu código mais robusto e better-realizar."
services: visual-studio-online
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: ed48ee06-e2d2-4322-af22-07200fb16987
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: cawa
ms.openlocfilehash: a606c8e7d8b730b67bd8481656e099900eb39fbc
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="optimizing-your-azure-code"></a>Otimizar o seu código do Azure
Quando estiver a programação aplicações que utilizam o Microsoft Azure, existem algumas práticas de codificação que deverá seguir para ajudar a evitar problemas com a escalabilidade de aplicação, o comportamento e o desempenho num ambiente de nuvem. A Microsoft fornece uma ferramenta de análise de código do Azure que reconhece e identifica os vários destes problemas normalmente encontrado e ajuda-o a resolvê-los. Pode transferir a ferramenta no Visual Studio através do NuGet.

## <a name="azure-code-analysis-rules"></a>Regras de análise de código do Azure
A ferramenta de análise de código do Azure utiliza as seguintes regras para o sinalizador automaticamente o código do Azure quando encontrar problemas conhecidos afetar o desempenho. Detetou problemas de aparecem como um avisos ou erros do compilador. Correções de código ou sugestões para resolver o aviso ou erro, muitas vezes, são fornecidos através de um ícone de lâmpada.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Evite utilizar o modo predefinido de estado de sessão (em processo)
### <a name="id"></a>ID
AP0000

### <a name="description"></a>Descrição
Se utilizar o modo de estado de sessão (em processo) predefinido para aplicações em nuvem, pode perder o estado da sessão.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Por predefinição, o modo de estado de sessão especificado no ficheiro Web. config está no processo. Além disso, se nenhuma entrada especificada no ficheiro de configuração, o modo de estado da sessão por predefinição no processo. O modo no processo armazena o estado da sessão na memória no servidor web. Quando uma instância é reiniciada ou uma nova instância é utilizada para balanceamento de carga ou suporte de ativação pós-falha, não é guardado o estado da sessão armazenado na memória no servidor web. Esta situação impede que a aplicação a ser dimensionável na nuvem.

Estado da sessão ASP.NET suporta várias opções de armazenamento diferentes para dados de estado de sessão: InProc, StateServer, SQLServer, personalizada e terminar a sessão. É recomendado que utilize o modo personalizado para alojar dados de um arquivo de estado da sessão externo, tal como [fornecedor de estado da sessão do Azure para Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Solução
É uma solução recomendada armazenar o estado da sessão num serviço de cache gerida. Saiba como utilizar [fornecedor de estado da sessão do Azure para Redis](http://go.microsoft.com/fwlink/?LinkId=401521) para armazenar o estado da sessão. Pode também armazenar o estado da sessão em outros locais para garantir a sua aplicação dimensionável na nuvem. Para saber mais sobre soluções alternativas leia [modos de estado de sessão](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Método Execute não deve ser assíncrona
### <a name="id"></a>ID
AP1000

### <a name="description"></a>Descrição
Criar métodos assíncronos (tais como [await](https://msdn.microsoft.com/library/hh156528.aspx)) fora do [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método e, em seguida, chamar os métodos assíncronos de [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Declarar o [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método como assíncrono faz com que a função de trabalho introduzir um ciclo de reinício.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Chamar os métodos async dentro de [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método faz com que o tempo de execução do serviço em nuvem reciclar a função de trabalho. Quando uma função de trabalho é iniciado, todos os execução do programa ocorre dentro do [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método. A sair com o [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método faz com que a função de trabalho reiniciar. Quando o tempo de execução de função de trabalho pedidos com êxito no método assíncrono, dispatches todas as operações após no método assíncrono e, em seguida, devolve. Isto faz com que a função de trabalho sair do [ [ [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método e reinício. Iteração seguinte de execução, a função de trabalho pedidos com êxito no método assíncrono novamente e reinicia, fazendo com que a função de trabalho reciclar novamente bem.

### <a name="solution"></a>Solução
Coloque todas as operações assíncronas fora do [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método. Em seguida, chame o método de refatorizado assíncrona de dentro de [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método, como runasync com .wait de (). A ferramenta de análise de código do Azure pode ajudar a corrigir este problema.

O fragmento de código seguinte demonstra a correção de código para este problema:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Utilize a autenticação da assinatura de acesso partilhado do Service Bus
### <a name="id"></a>ID
AP2000

### <a name="description"></a>Descrição
Utilize a assinatura de acesso partilhado (SAS) para autenticação. Serviço de controlo de acesso (ACS) está a ser preterido para autenticação de barramento de serviço.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Para maior segurança, do Azure Active Directory está a substituir a autenticação dos ACS com autenticação SAS. Consulte [do Azure Active Directory é o futuro da ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) para obter informações sobre o plano de transição.

### <a name="solution"></a>Solução
Utilize a autenticação de SAS nas suas aplicações. O exemplo seguinte mostra como utilizar um token SAS existente para aceder a um espaço de nomes de barramento de serviço ou de uma entidade.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Consulte os tópicos seguintes para obter mais informações.

* Para obter uma descrição geral, consulte [autenticação de assinatura de acesso partilhado com o Service Bus](https://msdn.microsoft.com/library/dn170477.aspx)
* [Como utilizar a autenticação da assinatura de acesso partilhado com o Service Bus](https://msdn.microsoft.com/library/dn205161.aspx)
* Para um projeto de exemplo, consulte [autenticação a utilizar acesso assinatura partilhado (SAS) com as subscrições do Service Bus](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Considere utilizar o método OnMessage para evitar "ciclo de recepção"
### <a name="id"></a>ID
AP2002

### <a name="description"></a>Descrição
Para evitar que para um "loop, receber" ao chamar o **OnMessage** método é a melhor solução para receber mensagens ao chamar o **Receive** método. No entanto, se tiver de utilizar o **Receive** método e especifique um tempo de espera de servidor não predefinido, certifique-se de que o tempo de espera de servidor mais do que um minuto.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Quando chamar **OnMessage**, o cliente inicia uma bomba mensagem interno que consulta constantemente a fila ou a subscrição. Este bomba mensagem contém um ciclo infinito que emite uma chamada para receber mensagens. Se a chamada de exceder o tempo limite, emite uma nova chamada. O intervalo de tempo limite é determinado pelo valor o [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) propriedade o [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)que está a ser utilizado.

A vantagem de utilizar **OnMessage** em comparação com **Receive** é que os utilizadores não têm manualmente consultar mensagens, lidar com exceções, processar mensagens múltiplas em paralelo e concluir as mensagens.

Se chamar **Receive** sem utilizar o valor predefinido, é necessário o *ServerWaitTime* valor é a mais do que um minuto. Definição *ServerWaitTime* a mais do que um minuto impede o servidor exceder o tempo limite antes da mensagem é recebida completamente.

### <a name="solution"></a>Solução
Consulte os seguintes exemplos de código para utilizações recomendadas. Para obter mais detalhes, consulte [QueueClient.OnMessage método (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)e [QueueClient.Receive método (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Para melhorar o desempenho da infraestrutura de mensagens do Azure, consulte o padrão de conceção [manual de mensagens assíncronas](https://msdn.microsoft.com/library/dn589781.aspx).

Segue-se um exemplo de utilização **OnMessage** para receber mensagens.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Segue-se um exemplo de utilização **Receive** com o servidor de predefinição de tempo de espera.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Segue-se um exemplo de utilização **Receive** com um servidor não predefinidas de tempo de espera.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Considere a utilização de métodos assíncronos do Service Bus
### <a name="id"></a>ID
AP2003

### <a name="description"></a>Descrição
Utilize métodos assíncronos do Service Bus para melhorar o desempenho com mensagens mediadas.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Utilizar métodos assíncronos permite a simultaneidade de programa de aplicação porque a execução de cada chamada não bloqueia o thread principal. Ao utilizar o Service Bus métodos de mensagens, efetuar uma operação (enviar, receber, eliminar, etc.) demora tempo. Neste momento inclui o processamento da operação pelo serviço do Service Bus para além de latência do pedido e a resposta. Para aumentar o número de operações, por hora, operações tem de ser executado em simultâneo. Para obter mais informações, consulte [melhores práticas para desempenho melhoramentos utilizando mediadas mensagens do Service Bus](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Solução
Consulte [QueueClient classe (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) para obter informações sobre como utilizar o método assíncrono recomendado.

Para melhorar o desempenho da infraestrutura de mensagens do Azure, consulte o padrão de conceção [manual de mensagens assíncronas](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Considere a criação de partições tópicos e filas do Service Bus
### <a name="id"></a>ID
AP2004

### <a name="description"></a>Descrição
Tópicos para um melhor desempenho com mensagens do Service Bus e de filas do Service Bus de partição.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Criação de partições de tópicos e filas do Service Bus aumenta a disponibilidade de débito e o serviço de desempenho porque o débito global de uma fila particionada ou um tópico já não é limitado pelo desempenho de um mediador de mensagens única ou um arquivo de mensagens. Além disso, uma falha temporária de um arquivo de mensagens não fazer uma fila particionada ou tópico indisponível. Para obter mais informações, consulte [entidades de mensagens de criação de partições](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Solução
O fragmento de código seguinte mostra como entidades de mensagens de partição.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Para obter mais informações, consulte [tópicos e filas de barramento de serviço particionada | Blogue do Microsoft Azure](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) e veja o [fila de partições do Microsoft Azure Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) exemplo.

## <a name="do-not-set-sharedaccessstarttime"></a>Não defina SharedAccessStartTime
### <a name="id"></a>ID
AP3001

### <a name="description"></a>Descrição
Deve evitar utilizar SharedAccessStartTimeset à hora atual para iniciar imediatamente a política de acesso partilhado. Só tem de definir esta propriedade se pretende iniciar a política de acesso partilhado numa altura posterior.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Sincronização do relógio faz com que uma diferença ligeiras entre centros de dados. Por exemplo, considera que seria logicamente definir a hora de início de uma política SAS de armazenamento como a hora atual utilizando DateTime.Now ou um método semelhante fará com que a política SAS entre em vigor imediatamente. No entanto, as diferenças de tempo ligeiras entre centros de dados podem provocar problemas com esta, uma vez que algumas vezes do Centro de dados poderão ser ligeiramente posteriores à hora de início, enquanto outros à frente das-lo. Como resultado, a política SAS pode expirar rapidamente (ou mesmo imediatamente) se a duração da política estiver definida demasiado curta.

Para obter mais orientações sobre a utilização de assinatura de acesso partilhado no armazenamento do Azure, consulte [introduzir tabela SAS (assinatura de acesso partilhado), SAS de fila e a atualização de SAS do Blob - blogue da equipa do Storage de Azure de Microsoft - base - Site MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solução
Remova a declaração de que define a hora de início da política de acesso partilhado. A ferramenta de análise de código do Azure fornece uma correção para este problema. Para obter mais informações sobre a gestão de segurança, consulte o padrão de conceção [Valet chave padrão](https://msdn.microsoft.com/library/dn568102.aspx).

O fragmento de código seguinte demonstra a correção de código para este problema.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Hora de expiração tem de ser superior a cinco minutos de política de acesso de partilhado
### <a name="id"></a>ID
AP3002

### <a name="description"></a>Descrição
Podem existir como diferença de cinco minutos no relógios entre centros de dados em diferentes localizações devido a uma condição ao conhecido como "dissimetrias do relógio." Para evitar a SAS o token de política de expirar anteriormente a ativação pós-falha planeada, defina a hora de expiração para ser mais de cinco minutos.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Os Datacenters em diferentes localizações em todo o mundo sincronizar por um sinal de relógio. Porque demora algum tempo para o sinal de relógio viajar para localizações diferentes, pode haver um desvio de tempo entre centros de dados em localizações geográficas diferentes embora tudo supposedly está sincronizado. Esta diferença de tempo que pode afetar o acesso partilhado início hora e a expiração do intervalo da política. Por conseguinte, para garantir a política de acesso partilhado entra em vigor imediatamente, não de especificar a hora de início. Além disso, certifique-se a hora de expiração mais de 5 minutos para impedir que o antecipado tempo limite.

Para obter mais informações sobre a utilização de assinatura de acesso partilhado no armazenamento do Azure, consulte [introduzir tabela SAS (assinatura de acesso partilhado), SAS de fila e a atualização de SAS do Blob - blogue da equipa do Storage de Azure de Microsoft - base - Site MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solução
Para obter mais informações sobre a gestão de segurança, consulte o padrão de conceção [Valet chave padrão](https://msdn.microsoft.com/library/dn568102.aspx).

Segue-se um exemplo de não especificar uma hora de início de política de acesso partilhado.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Segue-se um exemplo de especificar uma hora de início de política de acesso partilhado com uma duração de expiração de política superior a cinco minutos.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Para obter mais informações, consulte [criar e utilizar uma assinatura de acesso partilhado](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Utilizar CloudConfigurationManager
### <a name="id"></a>ID
AP4000

### <a name="description"></a>Descrição
Utilizar o [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) para projetos de classe, tais como o Web site do Azure e mobile services do Azure não irão introduzir problemas de tempo de execução. Como melhor prática, no entanto, é uma boa ideia utilizar a Cloud[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) como uma forma unificada de gestão de configurações para todas as aplicações de nuvem do Azure.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
CloudConfigurationManager lê o ficheiro de configuração adequado para o ambiente da aplicação.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Solução
Refatorar código para utilizar o [classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Uma correção de código para este problema é fornecida pela ferramenta de análise de código do Azure.

O fragmento de código seguinte demonstra a correção de código para este problema. Substituir

`var settings = ConfigurationManager.AppSettings["mySettings"];`

com o

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Eis um exemplo de como armazenar a definição de configuração num ficheiro App. config ou Web. config. Adicione as definições para a secção appSettings do ficheiro de configuração. Segue-se o ficheiro Web. config para o exemplo de código anterior.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Evite utilizar cadeias de ligação hard-coded
### <a name="id"></a>ID
AP4001

### <a name="description"></a>Descrição
Se utilizar cadeias de ligação hard-coded e terá de atualizá-las mais tarde, terá de efetuar alterações ao código de origem e recompilar a aplicação. No entanto, se armazenar as cadeias de ligação num ficheiro de configuração, pode alterá-los mais tarde, basta atualizar o ficheiro de configuração.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Pré-programar cadeias de ligação é uma prática incorreta porque introduz problemas quando as cadeias de ligação têm de ser alterada rapidamente. Além disso, se o projeto tem de ser verificado para controlo de origem, as cadeias de ligação hard-coded introduzem vulnerabilidades de segurança, uma vez que as cadeias podem ser visualizadas no código fonte.

### <a name="solution"></a>Solução
Armazenar cadeias de ligação nos ficheiros de configuração ou ambientes do Azure.

* Para aplicações de autónomo, utilize o App. config para armazenar as definições de cadeia de ligação.
* Para aplicações web alojado no IIS, utilize o Web. config para armazenar cadeias de ligação.
* Para aplicações do ASP.NET vNext, utilize configuration.json para armazenar cadeias de ligação.

Para obter informações sobre como utilizar ficheiros de configurações, tais como Web. config ou o App. config, consulte [diretrizes de configuração de Web de ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx). Para obter informações sobre como Azure trabalho de variáveis de ambiente, consulte [Azure Web Sites: como as cadeias de aplicação e a funcionar de cadeias de ligação](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Para obter informações sobre o armazenamento de cadeia de ligação no controlo de origem, consulte [evitar colocar informações confidenciais, tais como cadeias de ligação em ficheiros que estão armazenados no repositório de código de origem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Utilize o ficheiro de configuração de diagnóstico
### <a name="id"></a>ID
AP5000

### <a name="description"></a>Descrição
Em vez de configurar as definições de diagnóstico no seu código, tal como utilizando o Microsoft.WindowsAzure.Diagnostics programação API, deve configurar as definições de diagnóstico no ficheiro diagnostics.wadcfg. (Ou, diagnostics.wadcfgx se utilizar o Azure SDK 2.5). Ao fazê-lo, pode alterar as definições de diagnóstico sem ser necessário recompilar o seu código.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Antes do Azure SDK 2.5 (que utiliza o diagnóstico do Azure 1.3), diagnóstico do Azure (WAD) pode ser configurado utilizando vários métodos diferentes: adicioná-lo para o blob de configuração no armazenamento, utilizando código imperativo, declarativa configuração ou a predefinição configuração. No entanto, a forma preferencial para configurar diagnósticos é utilizar um ficheiro de configuração XML (diagnostics.wadcfg ou diagnositcs.wadcfgx para SDK 2.5 e posterior) no projeto de aplicação. Esta abordagem, o ficheiro diagnostics.wadcfg completamente define a configuração e pode ser atualizado em implementada novamente à vontade. Combinar a utilização do ficheiro de configuração diagnostics.wadcfg com os métodos programáticos de definir as configurações utilizando a [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)ou [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)classes podem levar a confusão. Consulte [inicializar ou alterar a configuração do diagnóstico do Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) para obter mais informações.

A partir do 1.3 WAD (incluído com o Azure SDK 2.5), já não é possível utilizar o código para configurar diagnósticos. Como resultado, apenas pode fornecer a configuração ao aplicar ou atualizar a extensão de diagnóstico.

### <a name="solution"></a>Solução
Utilize o estruturador de configuração de diagnósticos para mover as definições de diagnóstico para o ficheiro de configuração de diagnóstico (diagnositcs.wadcfg ou diagnositcs.wadcfgx para SDK 2.5 e posterior). Também é recomendável que instale [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) e utilizar a funcionalidade de diagnóstico mais recente.

1. No menu de atalho para a função que pretende configurar, escolha as propriedades e, em seguida, escolha o separador de configuração.
2. No **diagnóstico** secção, certifique-se de que o **ativar diagnósticos** caixa de verificação está selecionada.
3. Escolha o **configurar** botão.

   ![Aceder a opção de ativar o diagnóstico](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   Consulte [configurar diagnósticos para máquinas virtuais e serviços Cloud do Azure](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) para obter mais informações.

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Evitar declarar DbContext objetos como estático
### <a name="id"></a>ID
AP6000

### <a name="description"></a>Descrição
Para guardar a memória, evite declarar DBContext objetos como estático.

. Partilhe as suas ideias e comentários em [comentários de análise do Azure código](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Razão
Objetos de DBContext manter os resultados da consulta de cada chamada. Objetos de DBContext estáticos não são eliminados depois até que o domínio aplicacional seja descarregado. Por conseguinte, um objeto de DBContext estático pode consumir grandes quantidades de memória.

### <a name="solution"></a>Solução
Declarar DBContext como uma variável local ou um campo de instância não estático, utilizá-lo para uma tarefa e, em seguida, permitir que o mesmo ser eliminado após a utilização.

A classe de controlador MVC de exemplo seguinte mostra como utilizar o objeto de DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre otimizar e resolução de problemas de aplicações do Azure, consulte [resolver problemas de uma aplicação web no serviço de aplicações do Azure com o Visual Studio](app-service/web-sites-dotnet-troubleshoot-visual-studio.md).

<properties
    pageTitle="Como utilizar o reencaminhamento do Service Bus com o .NET | Microsoft Azure"
    description="Saiba como utilizar o serviço de reencaminhamento do Service Bus do Azure para ligar duas aplicações alojadas em localizações diferentes."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="05/06/2016"
    ms.author="sethm"/>


# Como utilizar o serviço de reencaminhamento do Service Bus do Azure

Este artigo descreve como utilizar o serviço de reencaminhamento do Service Bus. Os exemplos são escritos em C# e utilizam a API do Windows Communication Foundation (WCF) com extensões contidas na assemblagem do Service Bus. Para obter mais informações sobre o reencaminhamento do Service Bus, consulte a descrição geral [Mensagens retransmitidas do Service Bus](service-bus-relay-overview.md).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## O que é o reencaminhamento do Service Bus?

O [serviço de *reencaminhamento do* Service Bus](service-bus-relay-overview.md) permite compilar aplicações híbridas que se executam tanto num datacenter do Azure como no seu próprio ambiente empresarial no local. O reencaminhamento do Service Bus facilita isto, permitindo expor, de forma segura, os serviços Windows Communication Foundation (WCF) que se encontram numa rede empresarial na nuvem pública, sem ter de abrir uma ligação de firewall ou sem ter de fazer alterações intrusivas na infraestrutura da rede empresarial.

![Conceitos de Reencaminhamento](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

O reencaminhamento do Service Bus permite alojar serviços de WCF no seu ambiente empresarial existente. Deste modo, pode delegar a escuta de sessões e pedidos de entrada desses serviços de WCF para o serviço do Service Bus em execução no Azure. Tal permite expor esses serviços no código da aplicação em execução no Azure ou em ambientes de parceiros na extranet ou de trabalho móveis. O Service Bus permite controlar de forma segura quem pode aceder a esses serviços a um nível detalhado. Além disso, fornece uma forma poderosa e segura de expor os dados e as funcionalidades da aplicação a partir das suas soluções empresariais existentes e de tirar partido dos mesmos a partir da nuvem.

Este artigo mostra como utilizar o reencaminhamento do Service Bus para criar um serviço Web de WCF, exposto através de um enlace de canal TCP, que implementa uma conversação segura entre duas partes.

## Criar um espaço de nomes de serviço

Para começar a utilizar o reencaminhamento do Service Bus no Azure, deve criar, em primeiro lugar, um espaço de nomes. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Service Bus na sua aplicação.

Para criar um espaço de nomes de serviço:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Obter o pacote NuGet do Service Bus

O [Pacote NuGet do Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a forma mais fácil de obter a API do Service Bus e de configurar a sua aplicação com todas as dependências do Service Bus. Para instalar o pacote NuGet na sua aplicação, efetue o seguinte:

1.  No Explorador de Soluções, clique com o botão direito em **Referências** e, em seguida, clique em **Gerir Pacotes NuGet**.
2.  Localize "Service Bus" e selecione o item **Service Bus do Microsoft Azure**. Clique em **Instalar** para concluir a instalação e, em seguida, feche a caixa de diálogo seguinte.

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)


## Utilizar o Service Bus para expor e consumir um serviço Web SOAP com TCP

Para expor um serviço Web SOAP de WCF existente para consumo externo, deve efetuar alterações aos endereços e aos enlaces do serviço. Tal poderá exigir alterações ao ficheiro de configuração ou poderá exigir alterações do código, dependendo de como tiver definido e configurado os serviços de WCF. Tenha em atenção que o WCF permite ter vários pontos finais de rede no mesmo serviço, pelo que pode reter os pontos finais internos existentes enquanto adiciona pontos finais do Service Bus para acesso externo ao mesmo tempo.

Nesta tarefa, compilará um serviço de WCF simples e adicionará um serviço de escuta do Service Bus ao mesmo. Este exercício parte do princípio de que está familiarizado com o Visual Studio e, portanto, não explica todos os detalhes da criação de um projeto. Em vez disso, concentra-se no código.

Antes de iniciar os passos a seguir, conclua o procedimento seguinte para configurar o ambiente:

1.  No Visual Studio, crie uma aplicação de consola que contenha dois projetos, "Cliente" e "Serviço", na solução.
2.  Adicione o pacote NuGet do Service Bus do Microsoft Azure a ambos os projetos. Tal adiciona todas as referências de assemblagem necessárias aos projetos.

### Como criar o serviço

Em primeiro lugar, crie o próprio serviço. Qualquer serviço de WCF é constituído por, no mínimo, três partes distintas:

-   Definição de um contrato que descreve quais são as mensagens trocadas e quais são as operações a invocar.
-   Implementação desse contrato.
-   Anfitrião que aloja o serviço de WCF e expõe vários pontos finais.

Os exemplos de código nesta secção abordam cada um destes componentes.

O contrato define uma única operação, `AddNumbers`, que adiciona dois números e devolve o resultado. A interface `IProblemSolverChannel` permite ao cliente gerir mais facilmente a duração do proxy. A criação dessa interface é considerada uma prática recomendada. É uma boa ideia colocar esta definição do contrato num ficheiro separado, para poder referenciar esse ficheiro a partir de ambos os projetos "Cliente" e "Serviço", bem como poder copiar o código para ambos os projetos.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Com o contrato em vigor, a implementação é trivial.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### Configurar um anfitrião do serviço através de programação

Com o contrato e a implementação em vigor, pode agora alojar o serviço. O alojamento ocorre num objeto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx), que trata da gestão de instâncias do serviço e aloja os pontos finais que escutam as mensagens. O código seguinte configura o serviço com um ponto final local regular e um ponto final do Service Bus para ilustrar o aspeto, lado a lado, dos pontos finais internos e externos. Substitua o *espaço de nomes* da cadeia pelo nome do espaço de nomes e *yourKey* pela chave SAS obtida no passo de configuração anterior.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "yourKey")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

No exemplo, cria dois pontos finais que estão na mesma implementação do contrato. Um é local e o outro é criado através do Service Bus. As principais diferenças entre ambos são os enlaces; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) para o ponto final local e [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) para os endereços e o ponto final do Service Buso. O ponto final local tem um endereço de rede local com uma porta distinta. O ponto final do Service Bus tem um endereço do ponto final composto pela cadeia `sb`, pelo nome do espaço de nomes e pelo "solucionador" do caminho. Tal resulta no URI `sb://[serviceNamespace].servicebus.windows.net/solver`, que identifica o ponto final de serviço como um ponto final TCP do Service Bus com um nome DNS externo completamente qualificado. Se colocar o código que substitui os marcadores de posição conforme explicado acima para a função `Main` da aplicação de **Serviço**, terá um serviço funcional. Caso pretenda que o serviço escute exclusivamente no Service Bus, remova a declaração de ponto final local.

### Configurar um anfitrião do serviço no ficheiro App.config

Também pode configurar o anfitrião através do ficheiro App.config. O código de alojamento do serviço é, neste caso, apresentando no exemplo seguinte.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

As definições do ponto final são movidas para o ficheiro App.config. Tenha em atenção que o pacote NuGet já adicionou um intervalo de definições ao ficheiro App.config, as quais são as extensões de configuração necessárias para o Service Bus. O exemplo seguinte, que é o equivalente exato do código anterior, deverá aparecer diretamente sob o elemento **system.serviceModel**. Este exemplo de código parte do princípio de que o espaço de nomes em C# do projeto tem o nome **Serviço**.
Substitua os marcadores de posição pelo espaço de nomes de serviço do Service Bus e pela chave SAS.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Após efetuar estas alterações, o serviço inicia como anteriormente, porém, com dois pontos finais dinâmicos: um local e um com escuta na nuvem.

### Criar o cliente

#### Configurar um cliente através de programação

Para consumir o serviço, pode criar um cliente de WCF utilizando um objeto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). O Service Bus utiliza um modelo de segurança baseado em tokens implementado através de SAS. A classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um fornecedor de tokens de segurança com métodos de fábrica incorporados que devolvem alguns fornecedores de tokens conhecidos. O exemplo seguinte utiliza o método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para processar a aquisição do token SAS adequado. O nome e a chave são aqueles obtidos a partir do portal conforme descrito na secção anterior.

Em primeiro lugar, referencie ou copie o código de contrato `IProblemSolver` do serviço para o projeto do cliente.

Em seguida, substitua o código no método `Main` do cliente, substituindo novamente o texto do marcador de posição pelo espaço de nomes do Service Bus e pela chave SAS.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","yourKey") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Agora pode compilar o cliente e o serviço, executar os mesmos (execute, em primeiro lugar, o serviço) e o cliente chamará o serviço e imprimirá **9**. Pode executar o cliente e o servidor em computadores, ou até mesmo redes, diferentes e a comunicação continuará a funcionar. O código de cliente também pode ser executado na nuvem ou localmente.

#### Configurar um cliente no ficheiro App.config

O código seguinte mostra como configurar o cliente utilizando o ficheiro App.config.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

As definições do ponto final são movidas para o ficheiro App.config. O exemplo seguinte, que é o mesmo do código listado anteriormente, deverá aparecer diretamente sob o elemento **system.serviceModel**. Aqui, tal como anteriormente, deve substituir os marcadores de posição pelo espaço de nomes do Service Bus e pela chave SAS.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## Passos seguintes

Agora que aprendeu as noções básicas do serviço de reencaminhamento do Service Bus, siga estas hiperligações para saber mais.

- [Descrição geral de mensagens retransmitidas do Service Bus](service-bus-relay-overview.md)
- [Descrição geral da arquitetura do Service Bus do Azure ](service-bus-fundamentals-hybrid-solutions.md)
- Transfira os exemplos do Service Bus a partir dos [exemplos do Azure][] ou consulte a [descrição geral dos exemplos do Service Bus][].

  [Autenticação da Assinatura de Acesso Partilhado com o Service Bus]: service-bus-shared-access-signature-authentication.md
  [exemplos do Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [descrição geral dos exemplos do Service Bus]: service-bus-samples.md


<!--HONumber=ago16_HO4-->



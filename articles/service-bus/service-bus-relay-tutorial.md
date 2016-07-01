<properties 
    pageTitle="Tutorial de mensagens retransmitidas do Service Bus | Microsoft Azure"
    description="Criar um serviço e uma aplicação cliente do Service Bus, utilizando as mensagens retransmitidas do Service Bus."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/17/2016"
    ms.author="sethm" />

# Tutorial de mensagens retransmitidas do Service Bus

Este tutorial descreve como criar um serviço e uma aplicação cliente de Service Bus simples, utilizando as capacidades de "reencaminhamento" do Service Bus. Para um tutorial correspondente que utiliza as [mensagens mediadas](service-bus-messaging-overview.md#Brokered-messaging) do Service Bus, consulte o [Tutorial de Mensagens Mediadas .NET do Service Bus](service-bus-brokered-tutorial-dotnet.md).

Ao trabalhar com este tutorial ficará a saber os passos necessários para criar uma aplicação cliente e de serviço do Service Bus. Como os seus homólogos WCF, um serviço é uma construção que expõe um ou mais pontos finais, cada um dos quais expõe uma ou mais operações de serviço. O ponto final de um serviço especifica um endereço onde pode ser encontrado o serviço, um enlace que contém as informações que um cliente deve comunicar com o serviço e um contrato que define a funcionalidade fornecida pelo serviço aos seus clientes. A principal diferença entre um serviço WCF e um do Service Bus é que o ponto final é exposto na nuvem, em vez de localmente no seu computador.

Quando completar a sequência de tópicos deste tutorial, terá um serviço em execução e um cliente que pode invocar as operações do serviço. O primeiro tópico descreve como configurar uma conta. Os passos seguintes descrevem como definir um serviço que utiliza um contrato, como implementar esse serviço e como configurá-lo em código. Também descreve como alojar e executar o serviço. O serviço criado aloja-se a si mesmo e o cliente e o serviço são executados no mesmo computador. Pode configurar o serviço utilizando código ou um ficheiro de configuração.

Os últimos três passos descrevem como criar uma aplicação cliente, configurá-la, e criar e utilizar um cliente que possa aceder à funcionalidade do anfitrião.

Todos os tópicos nesta secção partem do princípio de que está a utilizar o Visual Studio como ambiente de desenvolvimento.

## Inscrever-se para obter uma conta

O primeiro passo é criar um espaço de nomes de serviço do Service Bus e obter uma chave de Assinatura de Acesso Partilhado (SAS). Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através do Service Bus. A combinação do espaço de nomes de serviço e da chave SAS fornece uma credencial para o Service Bus autenticar o acesso a uma aplicação.

1. Para criar um espaço de nomes, visite o [Portal Clássico do Azure][]. No lado esquerdo, clique em **Service Bus** e em **Criar**. Escreva um nome para o espaço de nomes, aceite as predefinições para todos os outros valores e, em seguida, clique na marca de verificação OK.

    >[AZURE.NOTE] Não é necessário utilizar o mesmo espaço de nomes para aplicações cliente e serviço.

    ![][4]

1. Na janela principal do portal, clique no nome do espaço de nomes que criou no passo anterior.

2. Clique no separador **Configurar** para ver as políticas e as chaves de acesso partilhadas predefinidas do espaço de nomes.

    ![][1]

3. Tome nota da chave primária para a política **RootManageSharedAccessKey** ou copie-a para a área de transferência. Utilizará este valor mais adiante neste tutorial.

## Definir um contrato de serviço de WCF para utilizá-lo com o Service Bus

O contrato de serviço especifica as operações (a terminologia do serviço Web para funções ou métodos) que o serviço suporta. Os contratos são criados através da definição de uma interface em C++, C# ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específica. Todas as interfaces devem ter aplicado o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) e todas as operações devem ter aplicado o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx). Se um método numa interface que tem o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx), não tem o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), esse método não será exposto. O código utilizado nestas tarefas surge no exemplo que segue o procedimento. Para uma descrição completa de contratos e serviços, consulte o artigo [Desenhar e implementar serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação do WCF.

### Para criar um contrato de Service Bus com uma interface

1. Abra o Visual Studio como administrador, para tal clique com o botão direito no programa no menu **Iniciar** e selecione **Executar como administrador**.

2. Crie um novo projeto de aplicação de consola. Clique no menu **Ficheiro**, selecione **Novo** e clique em **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**). Clique no modelo **Aplicação de Consola** e dê-lhe o nome **EchoService**. Clique em **OK** para criar o projeto.

    ![][2]

3. Instale o pacote NuGet do Service Bus. Este pacote adiciona automaticamente referências para as bibliotecas do Service Bus, bem como o **System.ServiceModel** do WCF. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o espaço de nomes que permite o acesso através de programação às funcionalidades básicas do WCF. O Service Bus utiliza muitos dos objetos e atributos de WCF para definir os contratos de serviço.

    No Explorador de Soluções, clique com o botão direito na solução e, em seguida, clique em **Gerir Pacotes NuGet para Solução**. Clique no separador **Procurar** e, em seguida, procure `Microsoft Azure Service Bus`. Certifique-se de que o nome do projeto está selecionado na caixa **Versões**. Clique em **Instalar** e aceite os termos de utilização.

    ![][3]

3. No Explorador de Soluções, faça duplo clique no ficheiro Program.cs para abri-lo no editor, caso não esteja ainda aberto.

4. Adicione o seguinte utilizando declarações na parte superior do ficheiro:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Altere o nome de espaço de nomes a partir do respetivo nome predefinido de **EchoService** para **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] Este tutorial utiliza o espaço de nomes **Microsoft.ServiceBus.Samples** de C#, que é o espaço de nomes do tipo gerido por contrato que é utilizado no ficheiro de configuração no passo [Configurar o cliente do WCF](#configure-the-wcf-client). Pode especificar o espaço de nomes que quiser quando construir este exemplo; no entanto, o tutorial não funcionará, a menos que modifique os espaços de nomes de contrato e serviço em conformidade, no ficheiro de configuração de aplicação. O espaço de nomes especificado no ficheiro App.config tem de ser o mesmo que o espaço de nomes especificado nos seus ficheiros C#.

1. Imediatamente depois da declaração de espaço de nomes `Microsoft.ServiceBus.Samples`, mas dentro do espaço de nomes, defina uma nova interface com o nome `IEchoContract` e aplique o atributo `ServiceContractAttribute` para a interface com um valor de espaço de nomes de **http://samples.microsoft.com/ServiceModel/Relay/**. O valor do espaço de nomes difere do espaço de nomes que utiliza em todo o âmbito do seu código. Em vez disso, o valor do espaço de nomes utiliza-se como identificador exclusivo para este contrato. A especificação explícita do espaço de nomes impede que o valor de espaço de nomes predefinido seja adicionado ao nome do contrato.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] Normalmente, o espaço de nomes do contrato de serviço contém um esquema de nomenclatura que inclui a informação da versão. Ao incluir a informação de versão no espaço de nomes de contrato de serviço, tal permite que os serviços possam isolar as alterações mais importantes, através da definição de um novo contrato de serviço com um novo espaço de nomes e a sua exposição num novo ponto final. Desta forma, os clientes podem continuar a utilizar o contrato de serviço anterior sem ter de fazer atualizações. A informação de versão pode consistir numa data ou número de compilação. Para obter mais informações, consulte o artigo [Controlo de Versões do Serviço](http://go.microsoft.com/fwlink/?LinkID=180498). Para este tutorial, o esquema de nomenclatura do espaço de nomes de contrato de serviço não contém informação da versão.

1. Dentro da interface `IEchoContract`, declare um método para a operação única que o contrato `IEchoContract` expõe na interface e aplique o atributo `OperationContractAttribute` ao método que pretende expor como parte do contrato público do Service Bus.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Imediatamente depois da definição de interface `IEchoContract`, declare um canal que herda de `IEchoContract` e também da interface `IClientChannel`, conforme se mostra aqui:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Um canal é o objeto de WCF através do qual o anfitrião e o cliente passam informações entre si. Mais tarde, escreverá código para o canal a fim de enviar informações entre as duas aplicações.

1. No menu **Compilar**, clique em **Compilar Solução** ou prima **Ctrl+Shift+B** para confirmar a precisão do seu trabalho até ao momento.

### Exemplo

O código seguinte mostra uma interface básica que define um contrato de Service Bus.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Agora que a interface está criada, pode implementá-la.

## Implementar o contrato de WCF para utilizar o Service Bus

A criação de um reencaminhamento do Service Bus requer que se crie primeiro o contrato, que é definido através de uma interface. Para obter mais informações sobre como criar a interface, consulte o passo anterior. O passo seguinte consiste em implementar a interface. Isto implica a criação de uma classe com o nome `EchoService` que implementa a interface `IEchoContract` definida pelo utilizador. Depois de implementar a interface, esta deve ser configurada utilizando um ficheiro de configuração App.config. O ficheiro de configuração contém a informação necessária para a aplicação, como o nome do serviço, o nome do contrato e o tipo de protocolo que é utilizado para comunicar com o Service Bus. O código utilizado para estas tarefas surge no exemplo que segue o procedimento. Para um debate mais geral sobre como implementar um contrato de serviço, consulte o artigo [Implementação de Contratos de Serviço](https://msdn.microsoft.com/library/ms733764.aspx) na documentação de WCF.

1. Criar uma nova classe com o nome `EchoService` imediatamente depois da definição da interface `IEchoContract`. A classe `EchoService` implementa a interface `IEchoContract`. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Tal como noutras implementações de interface, pode implementar a definição num ficheiro diferente. No entanto, neste tutorial, a implementação aparece no mesmo ficheiro que a definição de interface e o método `Main`.

1. Aplique o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à interface `IEchoContract`. O atributo especifica o nome de serviço e o espaço de nomes. Depois de fazê-lo, aparecerá a classe `EchoService` da seguinte forma:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implemente o método `Echo` definido na interface `IEchoContract` na classe `EchoService`. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Clique em **Compilar** e, em seguida, em **Compilar Solução** para confirmar a precisão do seu trabalho.

### Para definir a configuração para o anfitrião do serviço

1. O ficheiro de configuração é muito semelhante a um ficheiro de configuração do WCF. Inclui o nome do serviço, o ponto final (ou seja, a localização do Service Bus exposta pelos clientes e anfitriões para comunicar entre si) e o enlace (o tipo de protocolo que é utilizado para comunicar). A principal diferença é que o ponto final de serviço configurado se refere a um enlace [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx), que não faz parte do .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) é uma dos enlaces definidos pelo Service Bus.

1. No **Explorador de Soluções**, faça duplo clique no ficheiro App.config para abri-lo no editor do Visual Studio.

2. No elemento`<appSettings>`, substitua os marcadores de posição pelo nome do seu espaço de nomes do serviço e a chave SAS que copiou num passo anterior. 

1. Dentro das etiquetas `<system.serviceModel>`, adicione um elemento `<services>`. Pode definir várias aplicações de Service Bus num único ficheiro de configuração. No entanto, este tutorial define apenas um.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Dentro do elemento `<services>`, adicione um elemento `<service>` para definir o nome do serviço.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Dentro do elemento `<service>`, defina a localização do contrato de ponto final, bem como o tipo de enlace para o ponto final.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    O ponto final define onde o cliente procurará a aplicação anfitriã. Mais adiante, o tutorial utiliza este passo para criar um URI que expõe totalmente o anfitrião através do Service Bus. O enlace declara que estamos a utilizar TCP como protocolo de comunicação com o Service Bus.

1. No menu **Compilar**, clique em **Compilar Solução** para confirmar a precisão do seu trabalho.

### Exemplo

O código seguinte mostra a implementação do contrato de serviço.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

O código seguinte mostra o formato básico do ficheiro de configuração App.config associado ao anfitrião do serviço.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Alojar e executar um serviço Web básico para registar o Service Bus

Este passo descreve como executar um serviço básico do Service Bus.

### Para criar as credenciais do Service Bus

1. Em `Main()`, crie duas variáveis para armazenar o espaço de nomes e a chave SAS que são lidos a partir da janela da consola.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    A chave SAS será posteriormente utilizada para aceder ao seu projeto de Service Bus. O espaço de nomes é passado como parâmetro em `CreateServiceUri` para criar um URI de serviço.

4. Com um objeto [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx), declare que estará a utilizar uma chave SAS como tipo de credencial. Adicione o seguinte código imediatamente depois do código adicionado no último passo.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### Para criar um endereço base para o serviço

1. A seguir ao código que adicionou no último passo, crie uma instância `Uri` para o endereço base do serviço. Este URI especifica o esquema de Service Bus, o espaço de nomes e o percurso da interface de serviço.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    "sb" é a abreviatura de esquema de Service Bus e indica que está a utilizar o TCP como protocolo. Isto também foi indicado anteriormente no ficheiro de configuração, quando [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) se especificou como enlace.
    
    Para este tutorial, o URI é `sb://putServiceNamespaceHere.windows.net/EchoService`.

### Para criar e configurar o anfitrião do serviço

1. Defina o modo de conectividade como `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    O modo de conectividade descreve o protocolo utilizado pelo serviço para comunicar com o Service Bus; pode ser HTTP ou TCP. Com as definições predefinidas `AutoDetect`, o serviço tenta estabelecer ligação ao Service Bus através do TCP, se estiver disponível, e através de HTTP, se não estiver. Tenha em atenção que isto difere do protocolo especificado pelo serviço para a comunicação do cliente. Esse protocolo é determinado pelo enlace utilizado. Por exemplo, um serviço pode utilizar o enlace [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), o que especifica que o seu ponto final (exposto no Service Bus) comunica com os clientes através de HTTP. O mesmo serviço poderia especificar **ConnectivityMode.AutoDetect** para que o serviço comunique com o Service Bus através de TCP.

1. Crie o anfitrião do serviço, utilizando o URI criado anteriormente nesta secção.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    O anfitrião do serviço é o objeto de WCF que cria uma instância no serviço. Neste caso, passa-se o tipo de serviço que pretende criar (um tipo `EchoService`) e também o endereço onde pretende expor o serviço.

1. Na parte superior do ficheiro Program.cs, adicione as referências [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. De novo em `Main()`, configure o ponto final para ativar o acesso público.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Este passo informa o Service Bus que a aplicação pode ser encontrada publicamente, ao examinar a fonte ATOM do Service Bus do seu projeto. Se definir **DiscoveryType** como **privado**, um cliente poderia continuar a ter acesso ao serviço. No entanto, o serviço não apareceria quando se procura o espaço de nomes do Service Bus. Em vez disso, o cliente terá de conhecer de antemão o percurso do ponto final.

1. Aplique as credenciais de serviço para os pontos finais de serviço definidos no ficheiro de App.config:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Conforme se indicou no passo anterior, pode ter declarado vários serviços e pontos finais no ficheiro de configuração. Se o tiver feito, este código percorreria o ficheiro de configuração e pesquisaria cada ponto final para o qual se devem aplicar as credenciais. No entanto, para este tutorial, o ficheiro de configuração tem apenas um ponto final.

### Para abrir o serviço de host

1. Abra o serviço.

    ```
    host.Open();
    ```

1. Informe o utilizador que o serviço está em execução e explique-lhe como encerrá-lo.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Quando terminar, feche o serviço anfitrião.

    ```
    host.Close();
    ```

1. Prima **Ctrl + Shift + B**para compilar o projeto.

### Exemplo

O exemplo seguinte inclui o contrato de serviço e de implementação dos passos anteriores no tutorial, e aloja o serviço numa aplicação de consola. Compile o seguinte num executável com o nome EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## Criar um cliente WCF para o contrato de serviço

O passo seguinte é criar uma aplicação cliente básica do Service Bus e definir o contrato de serviço que implementará em passos posteriores. Tenha em atenção que muitos destes passos assemelham-se os passos que seguem para criar um serviço: definir um contrato, editar um ficheiro App.config, utilizar credenciais para ligar ao Service Bus, etc. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

1. Crie um novo projeto na atual solução de Visual Studio para o cliente efetuando o seguinte procedimento:
    1. No Explorador de Soluções, na mesma solução que contém o serviço, clique com o botão direito na solução atual (não o projeto) e clique em **Adicionar**. Em seguida, clique em **Novo Projeto**.
    2. Na caixa de diálogo **Adicionar Novo Projeto** , clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**), selecione o modelo **Aplicação da Consola** e atribua-lhe o nome **EchoClient**.
    3. Clique em **OK**.
<br />

1. No Explorador de Soluções, faça duplo clique no ficheiro Program.cs no projeto **EchoClient** para abri-lo no editor, caso não esteja ainda aberto.

1. Altere o nome do espaço de nomes a partir do respetivo nome predefinido de `EchoClient` para `Microsoft.ServiceBus.Samples`.

1. Instale o [Pacote NuGet do Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus). No Explorador de Soluções, clique com o botão direito no projeto **ImageListener** e, em seguida, clique em **Gerir Pacotes NuGet**. Clique no separador **Procurar** e, em seguida, procure `Microsoft Azure Service Bus`. Clique em **Instalar** e aceite os termos de utilização.

    ![][3]

1. Adicione uma declaração `using`para o espaço de nomes [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) no ficheiro Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Adicione a definição do contrato de serviço ao espaço de nomes, conforme se mostra no exemplo seguinte. Tenha em atenção que esta definição é idêntica à definição utilizada no projeto **Serviço**. Deve adicionar este código na parte superior do espaço de nomes `Microsoft.ServiceBus.Samples`.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Prima **Ctrl+Shift+B** para compilar o cliente.

### Exemplo

O código seguinte mostra o estado atual do ficheiro Program.cs no projeto EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## Configurar o cliente de WCF

Neste passo, criará um ficheiro App.config para uma aplicação cliente básica que acede ao serviço criado anteriormente neste tutorial. Este ficheiro App.config define o contrato, o enlace e o nome do ponto final. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

1. No Explorador de Soluções, no projeto **EchoClient**, faça duplo clique **App.config**para abrir o ficheiro no editor de Visual Studio.

2. No elemento`<appSettings>`, substitua os marcadores de posição pelo nome do seu espaço de nomes do serviço e a chave SAS que copiou num passo anterior.

1. Dentro do elemento system.serviceModel, adicione um elemento `<client>`.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Este passo declara que está a definir uma aplicação cliente de estilo WCF.

1. Dentro do elemento `client`, defina o nome, o contrato e o tipo de enlace para o ponto final.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Este passo define o nome do ponto final, o contrato definido no serviço e o facto de que a aplicação de cliente utiliza TCP para comunicar com o Service Bus. O nome do ponto final é utilizado no próximo passo para ligar esta configuração de ponto final com o URI do serviço.

1. Clique em **Ficheiro** e em **Guardar Tudo**.

## Exemplo

O código seguinte mostra o ficheiro App.config para o cliente de Echo.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Implementar o cliente de WCF para chamar o Service Bus

Neste passo, implementará uma aplicação cliente básica que acede ao serviço que criou anteriormente neste tutorial. Semelhante ao serviço, o cliente efetua muitas das mesmas operações para aceder ao Service Bus:

1. Define o modo de conectividade.
1. Cria o URI que localiza o serviço anfitrião.
1. Define as credenciais de segurança.
1. Aplica as credenciais para a ligação.
1. Abre a ligação.
1. Efetua as tarefas específicas da aplicação.
1. Fecha a ligação.

No entanto, uma das principais diferenças é que a aplicação de cliente utiliza um canal para ligar ao Service Bus, enquanto o serviço utiliza uma chamada para **ServiceHost**. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

### Para implementar uma aplicação cliente

1. Defina o modo de conectividade como **AutoDetect**. Adicione o seguinte código dentro do método `Main()` da aplicação **EchoClient**.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Defina variáveis para conter os valores para o espaço de nomes do serviço e a chave SAS que são lidos desde a consola.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Crie o URI que define a localização do anfitrião do projeto do Service Bus.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Crie o objeto de credencial para o ponto final do espaço de nomes do serviço.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Crie a fábrica de canais que carrega a configuração descrita no ficheiro App.config.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Uma fábrica de canais é um objeto de WCF que cria um canal através do qual comunicam as aplicações cliente e de serviço.

1. Aplique as credenciais do Service Bus.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Crie e abra o canal para o serviço.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Escreva a interface do utilizador básico e a funcionalidade para o eco.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Tenha em atenção que o código utiliza a instância do objeto de canal como um proxy para o serviço.

1. Feche o canal e feche a fábrica.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## Para executar as aplicações

1. Prima **Ctrl+Shift+B** para compilar a solução. Isto compila o projeto de cliente e o projeto de serviço que criou nos passos anteriores.

2. Antes de executar a aplicação cliente, tem de se certificar de que a aplicação de serviço está em execução. No Explorador de Soluções no Visual Studio, clique com o botão direito na solução **EchoService** e, em seguida, clique em **Propriedades**.

3. Na caixa de diálogo das propriedades da solução, clique em **Projeto de Arranque** e no botão **Vários projetos de arranque**. Certifique-se **EchoService** aparece em primeiro lugar na lista. 

4. Defina a caixa **Ação** para os projetos **EchoService** e **EchoClient** para **Iniciar**.

    ![][5]

5. Clique em **Dependências do Projeto**. Na caixa **Projetos**, selecione **EchoClient**. Na caixa **Depende de**, certifique-se que a opção **EchoService** está marcada.

    ![][6]

6. Clique em **OK** para dispensar a caixa de diálogo **Propriedades**.

7. Prima **F5** para executar ambos os projetos.

8. Ambas as janelas de consola abrem-se e solicitam-lhe o nome do espaço de nomes. Primeiro tem de ser executado o serviço, por isso, além da janela de consola **EchoService**, introduza o espaço de nomes e, em seguida, prima **Enter**.

9. Em seguida, é-lhe pedida novamente a sua chave SAS. Introduza a chave SAS e prima ENTER.

    Este é o exemplo da saída da janela da consola. Tenha em atenção que os valores aqui fornecidos servem apenas como exemplo.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    A aplicação de serviço imprime na janela de consola o endereço no qual está a escutar, como se vê no exemplo a seguir.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Na janela de consola **EchoClient**, introduza as mesmas informações que introduziu anteriormente para a aplicação de serviço. Siga os passos anteriores para introduzir o mesmo espaço de nomes do serviço e valores de chave SAS para a aplicação cliente.

11. Depois de introduzir estes valores, o cliente abrirá um canal para o serviço e pede-lhe que introduza algum texto, conforme se verifica no seguinte exemplo de saída de consola.

    `Enter text to echo (or [Enter] to exit):` 

    Introduza algum texto para enviar para a aplicação de serviço e prima Enter. Este texto é enviado para o serviço através da operação de serviço Eco e é apresentado na janela da consola de serviço, como se mostra na saída no exemplo seguinte.

    `Echoing: My sample text`

    A aplicação cliente recebe o valor devolvido da operação `Echo`, que é o texto original, e imprime-o para a janela de consola. A seguir está o exemplo da saída da janela da consola do cliente.

    `Server echoed: My sample text`

12. Pode continuar a enviar mensagens de texto do cliente para o serviço desta forma. Quando tiver terminado, prima Enter nas janelas de consola do cliente e do serviço para terminar as duas aplicações.

## Exemplo

O exemplo seguinte mostra como criar uma aplicação de cliente, como chamar as operações do serviço e como fechar o cliente após a conclusão da chamada de operação.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## Passos seguintes

Este tutorial mostrou como criar um serviço e uma aplicação cliente do Service Bus, utilizando as capacidades de "reencaminhamento" do Service Bus. Para um tutorial semelhante que utiliza as [mensagens mediadas](service-bus-messaging-overview.md#Brokered-messaging) do Service Bus, consulte o [Tutorial de Mensagens Mediadas .NET do Service Bus](service-bus-brokered-tutorial-dotnet.md).

Para mais informações acerca do Service Bus, consulte os seguintes tópicos.

- [Descrição geral de mensagens do Service Bus](service-bus-messaging-overview.md)
- [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitetura do Service Bus](service-bus-architecture.md)

[Portal Clássico do Azure]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png



<!--HONumber=Jun16_HO2-->



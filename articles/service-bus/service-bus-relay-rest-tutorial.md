<properties
    pageTitle="Tutorial de REST do Service Bus com mensagens retransmitidas | Microsoft Azure"
    description="Criar uma simples aplicação anfitriã de reencaminhamento do Service Bus que expõe uma interface baseada em REST."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/03/2016"
    ms.author="sethm" />

# Tutorial de REST do Service Bus

Este tutorial descreve como criar uma simples aplicação anfitriã de Service Bus que expõe uma interface baseada em REST. O REST permite que um cliente web, como um navegador, aceda às APIs do Service Bus através de pedidos de HTTP.

Este tutorial utiliza o modelo de programação REST do Windows Communication Foundation (WCF) para construir um serviço REST no Service Bus. Para obter mais informações, consulte o artigo [Modelo de Programação REST de WCF](https://msdn.microsoft.com/library/bb412169.aspx) e [Desenho e Implementação de Serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação do WCF.

## Passo 1: Criar um espaço de nomes de serviço

O primeiro passo é criar um espaço de nomes e obter uma chave de Assinatura de Acesso Partilhado (SAS). Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através do Service Bus. O sistema gera uma chave SAS automaticamente quando se cria um espaço de nomes de serviço. A combinação do espaço de nomes de serviço e da chave SAS fornece as credenciais do Service Bus para autenticar o acesso a uma aplicação.

1. Para criar um espaço de nomes de serviço, visite o [Portal Clássico do Azure][]. No lado esquerdo, clique em **Service Bus** e em **Criar**. Escreva um nome para o espaço de nomes e, em seguida, clique na marca de verificação.

2. Na janela principal do [Portal Clássico do Azure ][], clique no nome do espaço de nomes que criou no passo anterior.

3. Clique no separador **Configurar**.

4. Na secção **Gerador de chaves de acesso partilhado**, tome nota da **chave primária** associada à política **RootManageSharedAccessKey** ou copie-a para a área de transferência. Utilizará este valor mais adiante neste tutorial.

## Passo 2: Definir um contrato de serviço de WCF baseado em REST para utilizar com o Service Bus

Tal como acontece com outros serviços do Service Bus, quando se cria um serviço de estilo REST, é preciso definir o contrato. O contrato especifica quais as operações suportadas pelo anfitrião. Uma operação de serviço pode considerar-se como um método de serviço web. Os contratos são criados através da definição de uma interface em C++, C# ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específica. O atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) tem de ser aplicado a cada interface e o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) tem de ser aplicado a cada operação. Se um método numa interface que tem [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) não tem [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), esse método não será exposto. O código utilizado para estas tarefas é mostrado no exemplo que segue o procedimento.

A principal diferença entre um contrato de Service Bus básico e um contrato de estilo REST consiste na adição de uma propriedade para a [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Esta propriedade permite-lhe mapear um método na sua interface para um método no outro lado da interface. Neste caso, utilizaremos [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) para ligar um método a HTTP GET. Isto permite que o Service Bus obtenha e interprete com precisão os comandos enviados para a interface.

### Para criar um contrato de Service Bus com uma interface

1. Abra o Visual Studio como administrador: clique com o botão direito no programa no menu **Iniciar** e, em seguida, clique em **Executar como administrador**.

2. Crie um novo projeto de aplicação de consola. Clique no menu **Ficheiro** menu e selecione **Novo**, em seguida, selecione **Projeto**. Na caixa de diálogo de **Novo projeto** , clique em **Visual C#**, selecione a **Aplicação de Consola** e atribua-lhe o nome **ImageListener**. Utilize a **Localização** predefinida. Clique em **OK** para criar o projeto.

3. Para um projeto de C#, o Visual Studio cria um ficheiro `Program.cs`. Esta classe contém método `Main()` vazio, necessário para que um projeto de aplicação de consola seja criado corretamente.

4. Adicionar as referências ao Service Bus e **System.ServiceModel.dll** ao projeto através da instalação do pacote NuGet do Service Bus. Este pacote adiciona automaticamente referências para as bibliotecas do Service Bus, bem como o **System.ServiceModel** do WCF. No Explorador de soluções, com o botão direito do **ImageListener** do projeto e, em seguida, clique em **Gerir Pacotes NuGet**. Clique no separador **Procurar** e, em seguida, procure `Microsoft Azure Service Bus`. Clique em **Instalar** e aceite os termos de utilização.

5. Tem de adicionar explicitamente uma referência a **System.ServiceModel.Web.dll** ao projeto:

    a. No Explorador de Soluções, clique com o botão direito na pasta **Referências** sob a pasta do projeto e, em seguida, clique em **Adicionar Referência**.

    b. Na caixa de diálogo **Adicionar Referência**, clique no separador no lado esquerdo **Framework** e na caixa**Pesquisa**, escreva **System.ServiceModel.Web**. Selecione a caixa de verificação **System.ServiceModel.Web** e clique em **OK**.

6. Adicione as seguintes declarações `using` na parte superior do ficheiro Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o espaço de nomes que permite o acesso através de programação a funcionalidades básicas do WCF. O Service Bus utiliza muitos dos objetos e atributos de WCF para definir os contratos de serviço. Utilizará este espaço de nomes na maioria das suas aplicações de reencaminhamento do Service Bus. Da mesma forma, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) ajuda a definir o canal, que é o objeto através do qual se comunica com o Service Bus e o navegador Web do cliente. Por último, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contém os tipos que permitem criar aplicações baseadas na Web.

7. Mude o nome de espaço de nomes de `ImageListener` para **Microsoft.ServiceBus.Samples**.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Imediatamente depois da chaveta de abertura da declaração do espaço de nomes, defina uma nova interface com o nome **IImageContract** e aplique o atributo **ServiceContractAttribute** à interface com um valor `http://samples.microsoft.com/ServiceModel/Relay/`. O valor do espaço de nomes difere do espaço de nomes que utiliza em todo o âmbito do seu código. O valor do espaço de nomes utiliza-se como identificador exclusivo para este contrato e deve conter a informação da versão. Para obter mais informações, consulte o artigo [Controlo de Versões do Serviço](http://go.microsoft.com/fwlink/?LinkID=180498). A especificação explícita do espaço de nomes impede que o valor de espaço de nomes predefinido seja adicionado ao nome do contrato.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Dentro da interface `IImageContract`, declare um método para a operação única que o contrato `IImageContract` expõe na interface e aplique o atributo `OperationContractAttribute` ao método que pretende expor como parte do contrato público do Service Bus.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. No atributo **OperationContract**, adicione o valor **WebGet**.

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Deste modo, o Service Bus poderá reencaminhar os pedidos de HTTP GET para `GetImage` e para traduzir os valores devolvidos de `GetImage` numa resposta HTTP GETRESPONSE. Mais adiante no tutorial, utilizará um navegador web para aceder a este método e para apresentar a imagem no navegador.

11. Imediatamente depois da definição`IImageContract`, declare um canal que herde das interfaces `IImageContract` e `IClientChannel`.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    Um canal é o objeto de WCF através do qual o serviço e o cliente passam informações entre si. Mais tarde, criará o canal na sua aplicação anfitriã. O Service Bus utiliza depois este canal para passar os pedidos HTTP GET do navegador para a implementação **GetImage**. O Service Bus também utiliza o canal para obter o valor devolvido de **GetImage** e convertê-lo em HTTP GETRESPONSE para o navegador do cliente.

12. No menu **Compilar**, clique em **Compilar Solução** para confirmar a precisão do seu trabalho até ao momento.

### Exemplo

O código seguinte mostra uma interface básica que define um contrato de Service Bus.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## Passo 3: Implementar um contrato de serviço de WCF baseado em REST para utilizar o Service Bus

A criação de um Serviço do Service Bus do estilo REST requer que se crie primeiro o contrato, que é definido através de uma interface. O passo seguinte consiste em implementar a interface. Isto implica a criação de uma classe com o nome **ImageService** que implementa a interface **IImageContract** definida pelo utilizador. Depois de implementar o contrato, em seguida, configura-se a interface utilizando um ficheiro de App.config. O ficheiro de configuração contém a informação necessária para a aplicação, como o nome do serviço, o nome do contrato e o tipo de protocolo que é utilizado para comunicar com o Service Bus. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

Tal como acontece com os passos anteriores, há poucas diferenças entre a implementação de um contrato de estilo REST e um contrato básico do Service Bus.

### Para implementar um contrato do Service Bus de estilo REST

1. Criar uma nova classe com o nome **ImageService** diretamente da definição da interface **IImageContract**. A classe **ImageService** implementa a interface **IImageContract**.

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Tal como noutras implementações de interface, pode implementar a definição num ficheiro diferente. No entanto, neste tutorial, a implementação aparece no mesmo ficheiro que a definição de interface e o método`Main()`.

2. Aplique o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à classe **IImageService** para indicar que a classe é uma implementação de um contrato de WCF.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Como se mencionou anteriormente, este espaço de nomes não é um espaço de nomes tradicional. Em vez disso, forma parte da arquitetura de WCF que identifica o contrato. Para obter mais informações, consulte o tópico [Nomes de Contrato de Dados](https://msdn.microsoft.com/library/ms731045.aspx) na documentação de WCF.

3. Adicione uma imagem. jpg ao seu projeto.  

    Trata-se de uma imagem que o serviço apresenta no navegador recetor. Com o botão direito clique no projeto em seguida clique em **Adicionar**. Em seguida, clique em **Item Existente**. Utilize a caixa de diálogo **Adicionar Item Existente** para navegar para um ficheiro. jpg adequadas e, em seguida, clique em **Adicionar**.

    Ao adicionar o ficheiro, certifique-se de que a opção **Todos os Ficheiros** está selecionada na lista pendente junto ao campo **Nome de ficheiro:**. O resto deste tutorial parte do princípio de que o nome da imagem é "image.jpg". Se tiver um ficheiro diferente, terá de mudar o nome da imagem ou alterar o código para compensar.

4. Certifique-se de que o serviço em execução pode localizar o ficheiro de imagem, em ** Explorador de Soluções** clique com o botão direito no ficheiro de imagem e, em seguida, clique em **Propriedades**. No painel **Propriedades**, definir **Copiar para o diretório de saída** para **Copiar se for mais recente**.

5. Adicione uma referência à assemblagem **System.Drawing.dll** do projeto e adicione também as seguintes instruções `using` associadas.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. Na classe **ImageService**, adicione o seguinte construtor que carrega o mapa de bits e prepara o seu envio para o navegador do cliente.

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Diretamente depois do código anterior, adicione o seguinte método **GetImage** à classe **ImageService** para devolver uma mensagem HTTP que contém a imagem.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Esta implementação utiliza **MemoryStream** para obter a imagem e prepará-la para a transmissão para o navegador. Inicia a posição de transmissão em zero, declara o conteúdo da transmissão como um jpeg e transmite a informação.

8. No menu **Compilar**, clique em **Compilar Solução**.

### Para definir a configuração para executar o serviço Web no Service Bus

1. No **Explorador de Soluções**, faça duplo clique em **App.config** para abri-lo no editor do Visual Studio.

    O ficheiro **App.config** assemelha-se um ficheiro de configuração de WCF e inclui o nome do serviço, o ponto final (ou seja, a localização do Service Bus exposta pelos clientes e anfitriões para comunicar entre si) e o enlace (do tipo de protocolo que é utilizado para comunicar). A principal diferença aqui é que o ponto final de serviço configurado se refere a um enlace [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx), o que não faz parte do .NET Framework.

2. O elemento XML `<system.serviceModel>` é um elemento de WCF que define um ou vários serviços. Neste caso, utiliza-se para definir o nome de serviço e o ponto final. Na parte inferior do elemento `<system.serviceModel>` (mas ainda dentro de `<system.serviceModel>`), adicionar um elemento `<bindings>` que tem o seguinte conteúdo. Isto define os enlaces utilizados na aplicação. Pode definir vários enlaces, mas neste tutorial define-se apenas um.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Este passo define um enlace [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) do Service Bus com **relayClientAuthenticationType** definido como **Nenhum**. Esta definição indica que um ponto final com este enlace não necessita de uma credencial de cliente.

3. Depois do elemento `<bindings>`, adicione um elemento `<services>`. Assim como nos enlaces, pode definir vários serviços num único ficheiro de configuração. No entanto, neste tutorial, definirá apenas um.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Este passo configura um serviço que utiliza o valor previamente definido **webHttpRelayBinding**. Também utiliza o **sbTokenProvider** predefinido, que se define no passo a seguir.

4. Depois do elemento `<services>`, crie um elemento `<behaviors>` com o seguinte conteúdo, substituindo "SAS_KEY" pela chave *Assinatura de Acesso Partilhado* (SAS) que obteve no [Portal Clássico do Azure][] no Passo 1.

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Ainda no App.config, no elemento `<appSettings>`, substitua o valor total da cadeia de ligação pela cadeia previamente obtida no portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. No menu **Compilar**, clique em **Compilar Solução** para compilar a solução completa.

### Exemplo

O código seguinte mostra a implementação de contrato e serviço para um serviço baseado em REST que está em execução no Service Bus com o enlace **WebHttpRelayBinding**.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

O exemplo seguinte mostra o ficheiro de App.config associado ao serviço.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## Passo 4: Alojar o serviço de WCF baseado em REST para utilizar o Service Bus

Este passo descreve como executar um serviço Web utilizando uma aplicação de consola no Service Bus. No exemplo que segue o procedimento fornece uma lista completa de código escrito neste passo.

### Para criar um endereço base para o serviço

1. Na declaração de função `Main()`, crie uma variável para armazenar o espaço de nomes do seu projeto de Service Bus. Certifique-se de substituir `yourNamespace` pelo nome do espaço de nomes de serviço criado previamente.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    O Service Bus utiliza o nome do seu espaço de nomes para criar um URI exclusivo.

2. Crie uma instância `Uri` para o endereço base do serviço que se baseia no espaço de nomes.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### Para criar e configurar o anfitrião do serviço Web

- Criar o host do serviço Web, utilizando o endereço URI criado anteriormente nesta secção.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    O anfitrião do serviço é o objeto de WCF que cria uma instância na aplicação anfitriã. Neste exemplo, passa-se o tipo de anfitrião que pretende criar (um **ImageService**) e também o endereço no qual pretende expor a aplicação anfitriã.

### Para executar o anfitrião do serviço Web

1. Abra o serviço.

    ```
    host.Open();
    ```
    O serviço está agora em execução.

2. Apresente uma mensagem que indique que o serviço está em execução e como pará-lo.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Quando terminar, feche o serviço anfitrião.

    ```
    host.Close();
    ```

## Exemplo

O exemplo seguinte inclui o contrato de serviço e de implementação dos passos anteriores no tutorial e aloja o serviço numa aplicação de consola. Compile o código seguinte para criar um executável com o nome ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### Compilação do código

Depois de compilar a solução, faça o seguinte procedimento para executar a aplicação:

1. Prima **F5** ou navegue para a localização do ficheiro executável (ImageListener\bin\Debug\ImageListener.exe), para executar o serviço. Manter a aplicação em execução dado que é necessário para efetuar o passo seguinte.

2. Copie e cole o endereço a partir da linha de comandos para um navegador para ver a imagem.

3. Quando tiver terminado, prima **Enter** na janela da linha de comandos para fechar a aplicação.

## Passos seguintes

Agora que criou uma aplicação que utiliza o serviço de reencaminhamento do Service Bus, consulte os artigos seguintes para obter mais informações sobre as mensagens retransmitidas:

- [Descrição geral da arquitetura do Service Bus do Azure ](service-bus-fundamentals-hybrid-solutions.md#relays)

- [Como utilizar o serviço de reencaminhamento do Service Bus](service-bus-dotnet-how-to-use-relay.md)

[Portal Clássico do Azure]: http://manage.windowsazure.com


<!--HONumber=Jun16_HO2-->



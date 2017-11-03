---
title: "A criação de partições de serviços do Service Fabric | Microsoft Docs"
description: "Descreve como serviços do Service Fabric com monitorização de estado de partição. As partições permite que o armazenamento de dados nas máquinas locais para que dados e computação podem ser ampliadas em conjunto."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 3b7248c8-ea92-4964-85e7-6f1291b5cc7b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: msfussell
ms.openlocfilehash: 3c1e80305cb65f41a6981b99f69e8b87f89599ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="partition-service-fabric-reliable-services"></a>Serviços de partição fiáveis do Service Fabric
Este artigo fornece uma introdução aos conceitos básicos de criação de partições de serviços fiáveis do Service Fabric do Azure. O código de origem utilizado no artigo também está disponível no [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Criação de partições
Criação de partições não é exclusiva para o Service Fabric. Na verdade, é um padrão de núcleos de criação de serviços dimensionáveis. Uma noção mais vasto, iremos pode pensar sobre a criação de partições, como um conceito de divisão de estado (dados) e de computação em mais pequeno acessíveis unidades para melhorar o desempenho e escalabilidade. Uma forma bem conhecida de criação de partições é [a criação de partições de dados][wikipartition], também conhecido como fragmentação.

### <a name="partition-service-fabric-stateless-services"></a>Serviços sem monitorização de estado do Service Fabric de partição
Para os serviços sem monitorização de estado, pode pensar sobre uma partição que está a ser uma unidade lógica que contém uma ou mais instâncias de um serviço. Figura 1 mostra um serviço sem monitorização de estado com cinco instâncias distribuídas por um cluster com uma partição.

![Sem estado](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Realmente existem dois tipos de soluções de serviços sem monitorização de estado. A primeira é um serviço que persiste Estado externamente, por exemplo numa base de dados SQL do Azure (por exemplo, um Web site que armazena as informações de sessão e os dados). Segunda é só de cálculo serviços (como thumbnailing calculadora ou image) que não gere a qualquer estado persistente.

No caso, um serviço sem monitorização de estado de criação de partições é um cenário muito raros – escalabilidade e disponibilidade são normalmente conseguido ao adicionar mais instâncias. É o único momento em que pretende considerar várias partições para instâncias de serviço sem monitorização de estado quando é necessário satisfazer os pedidos especiais de encaminhamento.

Por exemplo, considere o caso em que os utilizadores com os IDs de um determinado intervalo tem apenas de ser servidos por uma instância de serviço específica. Outro exemplo de quando foi particionar um serviço sem monitorização de estado é se tiver um back-end verdadeiramente particionado (por exemplo, a base de dados SQL) e pretende controlar que instância de serviço deve escrever para a base de dados ID de partição horizontal – ou efetuar outras tarefas de preparação dentro do serviço sem monitorização de estado que requer as mesmas informações de criação de partições que é utilizado no back-end. Os tipos de cenários também podem ser resolvidos formas diferentes e não exige necessariamente de criação de partições de serviço.

O resto esta explicação passo a passo concentra-se nos serviços de monitorização de estado.

### <a name="partition-service-fabric-stateful-services"></a>Serviços de monitorização de estado de Service Fabric de partição
Service Fabric torna mais fácil desenvolver serviços com monitorização de estado dimensionáveis por oferta de uma forma de primeira classe para o estado de partição (dados). Concecionais, pode pensar sobre uma partição de um serviço de estado monitorizado como uma unidade de escala é altamente fiável através de [réplicas](service-fabric-availability-services.md) que são distribuídas e equilibrada em nós no cluster.

A criação de partições no contexto de serviços com monitorização de estado do Service Fabric refere-se ao processo de determinar se uma partição de serviço específica é responsável por uma parte do Estado do serviço concluída. (Tal como mencionado anteriormente, uma partição é um conjunto de [réplicas](service-fabric-availability-services.md)). Um excelente sobre recursos de infraestrutura de serviço é que coloca as partições em nós diferentes. Isto permite-lhes aumente o limite de recursos de um nó. À medida que os dados necessitam aumentam, partições de crescimento e Service Fabric efetua novamente o balanceamento partições entre nós. Isto garante que a utilização eficaz contínua de recursos de hardware.

Para lhe fornecer um exemplo, diga a que começar com um cluster de nó de 5 e um serviço que está configurado para ter 10 partições e o destino de três réplicas. Neste caso, Service Fabric seria equilibrar e distribuir as réplicas no cluster - e iria acaba por ficar com dois principais [réplicas](service-fabric-availability-services.md) por nó.
Se agora precisa de aumentar horizontalmente o 10 nós do cluster, o Service Fabric seria rebalancear primário [réplicas](service-fabric-availability-services.md) em todos os nós de 10. Da mesma forma, se ampliada para 5 nós, o Service Fabric seria rebalancear todas as réplicas em todos os nós de 5.  

Figura 2 mostra a distribuição de 10 partições antes e depois do cluster de dimensionamento.

![Serviço com estado](./media/service-fabric-concepts-partitioning/partitions.png)

Como resultado, o Escalamento horizontal é efetuado uma vez que os pedidos de clientes são distribuídos por computadores, o desempenho global da aplicação é melhorado e contenção de acesso a segmentos de dados é reduzida.

## <a name="plan-for-partitioning"></a>Plano para criação de partições
Antes de implementar um serviço, deve considerar sempre a estratégia de criação de partições que é necessário para aumentar horizontalmente. Existem várias formas, mas todos eles concentrar-se em que a aplicação tem de conseguir. Para o contexto deste artigo, vamos considere alguns dos aspetos mais importantes.

Uma boa abordagem é pensar sobre a estrutura de estado que tem de ser particionado, como o primeiro passo.

Vamos um exemplo simples. Se criar um serviço num inquérito countywide, pode criar uma partição para cada cidade no county. Em seguida, pode armazenar os votos para cada pessoa em cidade na partição que corresponde a essa localidade. Figura 3 ilustra um conjunto de pessoas e Cidade na qual residem.

![Partição Simple](./media/service-fabric-concepts-partitioning/cities.png)

Como a população de cidades varia amplamente, poderá acabar com algumas partições que contêm uma grande quantidade de dados (por exemplo, Seattle) e outras partições com pouco Estado (por exemplo, Kirkland). Por isso, o que é o impacto da eliminação de partições com desigual quantidades de estado?

Se tem em consideração o exemplo novamente, pode facilmente verá que a partição que contém os votos para Seattle irá receber mais tráfego que Kirkland um. Por predefinição, o Service Fabric certifica-se de que não há sobre o mesmo número de réplicas primária e secundária em cada nó. Por isso, poderá acabar connosco que contêm as réplicas que fornecer mais tráfego e a outras pessoas que servem menos tráfego. De preferência iria querer evitar oportunidades frequente e amovíveis, como esta, num cluster.

Para evitar isto, deve efetuar duas coisas, do ponto de criação de partições de vista:

* Tente particionar o estado de modo a que é distribuído uniformemente em todas as partições.
* Carga de relatório de cada uma das réplicas para o serviço. (Para obter informações sobre como, consulte este artigo no [métricas e carga](service-fabric-cluster-resource-manager-metrics.md)). O Service Fabric fornece a capacidade para comunicar carga consumida pelos serviços, tais como a quantidade de memória ou o número de registos. Com base nas métricas comunicadas, o Service Fabric Deteta que algumas partições cargas superiores que outros estão a funcionar e de que efetua novamente o balanceamento de cluster movendo réplicas para nós mais adequados, para que global nenhum nó está sobrecarregado.

Por vezes, não é possível saber a quantidade de dados será numa determinada partição. Para que seja uma recomendação geral efetuar ambos - em primeiro lugar, por adotar uma estratégia de criação de partições que propaga os dados uniformemente em de partições e o segundo, por carga de relatórios.  O primeiro método impede situações descritas no exemplo de voto, durante o segundo ajuda uniforme saída diferenças temporárias de acesso ou carga ao longo do tempo.

Outro aspeto do planeamento de partição é escolher o número correto de partições lugar.
Numa perspetiva de Service Fabric, não há nada que o impede de começar com um número mais alto de partições que o antecipado para o seu cenário.
Na verdade, assumindo que o número máximo de partições é uma abordagem válida.

Em casos raros, poderá acabar por necessitar de mais partições que inicialmente selecionou. Como não é possível alterar a contagem da partição após o facto de, terá de aplicar algumas abordagens de partição avançadas, tais como criar uma nova instância de serviço do mesmo tipo de serviço. Também terá de implementar a lógica algum lado do cliente que encaminha os pedidos para a instância de serviço correto, com base nos dados de conhecimento do lado do cliente que o código de cliente tem de manter.

Outra consideração para criação de partições de planeamento é os recursos do computador disponíveis. Como o estado tem de ser acedido e armazenados, estão vinculados a seguir:

* Limites de largura de banda de rede
* Limites de memória de sistema
* Limites de armazenamento do disco

Por isso, o que acontece caso se depare com restrições de recursos num cluster em execução? A resposta é que é possível simplesmente ampliar o cluster para acomodar os requisitos de novo.

[O guia de planeamento de capacidade](service-fabric-capacity-planning.md) oferece orientação sobre como determinar quantos nós tem do cluster.

## <a name="get-started-with-partitioning"></a>Introdução à criação de partições
Esta secção descreve como começar com o serviço de criação de partições.

Recursos de infraestrutura de serviço oferece uma opção de três esquemas de partição:

* Ranged a criação de partições (conhecida como UniformInt64Partition).
* Com o nome de criação de partições. As aplicações que utilizam, normalmente, este modelo têm dados que podem ser bucketed, dentro de um conjunto vinculado. Alguns exemplos comuns de campos de dados utilizados como chaves de partição denominado seria regiões, códigos postais, grupos de cliente ou outros limites de negócio.
* Criação de partições singleton. As partições de singleton são normalmente utilizadas quando o serviço não requerem qualquer encaminhamento adicionais. Por exemplo, serviços sem monitorização de estado utilizam este esquema de partições por predefinição.

O nome e esquemas de criação de partições de Singleton são formulários especiais de partições ranged. Por predefinição, os modelos do Visual Studio para utilização do Service Fabric ranged criação de partições, como o é o mais comum e útil. O resto deste artigo foca-se no esquema de partições ranged.

### <a name="ranged-partitioning-scheme"></a>Ranged esquema partições
Isto é utilizado para especificar um intervalo de número inteiro (identificado por uma chave baixa e alta chave) e um número de partições (n). Cria partições n, cada responsáveis por um subrange sem sobreposição do intervalo de chave de partição geral. Por exemplo, um esquema de partições ranged com uma chave baixa de 0, uma chave superior de 99 e uma contagem de 4 criaria quatro partições, conforme mostrado abaixo.

![Intervalo de criação de partições](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Uma abordagem comum é criar um hash com base numa chave exclusiva no conjunto de dados. Alguns exemplos comuns das chaves teriam de ser um número de identificação de vehicle (VIN), um ID de empregado ou uma cadeia exclusiva. Ao utilizar esta chave exclusiva, em seguida, teria de gerar um código de hash, módulo o intervalo de chave, para utilizar como a chave. Pode especificar os limites superior e inferiores do intervalo permitido chave.

### <a name="select-a-hash-algorithm"></a>Selecione um algoritmo hash
Uma parte importante de hash é selecionar o algoritmo de hash. Uma consideração é se o objetivo é semelhantes chaves perto de si (Localidade confidenciais hash) – de grupo ou se a atividade deve ser distribuída amplamente em todas as partições (distribuição hash), que é mais comum.

As características de um algoritmo hash da distribuição boa são que é fácil de computação, tem algumas colisões e distribui as chaves uniformemente. É um bom exemplo de um algoritmo hash eficiente a [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) algoritmo hash.

Um recurso boa para opções de algoritmo de código hash geral é o [Wikipedia página em funções de hash](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Criar um serviço com monitorização de estado com várias partições
Vamos criar primeiro serviço com estado fiável com várias partições. Neste exemplo, irá criar uma aplicação muito simple onde pretende armazenar todos os nomes de último que começam com a mesma letra da mesma partição.

Antes de escrever qualquer código, tem de pensar sobre as partições e chaves de partição. Tem de 26 partições (um para cada letra do alfabeto), mas que sobre as chaves baixas e alta?
Como queremos literalmente ter uma partição por uma letra, podemos utilizar 0 como a chave baixa e 25 como chave superior, como cada letra é a sua própria chave.

> [!NOTE]
> Este é um cenário simplificado, tal como na realidade a distribuição seria desigual. Última nomes começados por letras "S" ou "M" são mais comuns que o começados por "X" ou "Y".
> 
> 

1. Abra **Visual Studio** > **ficheiro** > **novo** > **projeto**.
2. No **novo projeto** diálogo caixa, escolha a aplicação de Service Fabric.
3. Chame o projeto "AlphabetPartitions".
4. No **criar um serviço** diálogo caixa, escolha **com monitorização de estado** de serviço e chamá-lo "Alphabet.Processing" conforme mostrado na imagem abaixo.
       ![Caixa de diálogo novo serviço no Visual Studio][1]

  <!--  ![Stateful service screenshot](./media/service-fabric-concepts-partitioning/createstateful.png)-->

5. Defina o número de partições. Abra o ficheiro de Applicationmanifest.xml localizado na pasta do projeto AlphabetPartitions ApplicationPackageRoot e atualizar o parâmetro Processing_PartitionCount para 26, conforme mostrado abaixo.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Terá também de atualizar as propriedades de LowKey e HighKey do elemento StatefulService no ApplicationManifest.xml conforme mostrado abaixo.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Para o serviço esteja acessível, abra um ponto final numa porta ao adicionar o elemento de ponto final de ServiceManifest.xml (localizado na pasta PackageRoot) para o serviço de Alphabet.Processing conforme mostrado abaixo:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Agora, o serviço está configurado para escutar para um ponto final interno com 26 partições.
7. Em seguida, terá de substituir o `CreateServiceReplicaListeners()` método à classe de processamento.
   
   > [!NOTE]
   > Para este exemplo, partimos do pressuposto que está a utilizar um HttpCommunicationListener simple. Para obter mais informações sobre a comunicação de serviço fiável, consulte [modelo de comunicação de serviço fiável a](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Um padrão para o URL que uma réplica escuta recomendado é o seguinte formato: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Por isso, se quiser configurar o serviço de escuta de comunicação para escutar os pontos finais corretos em com este padrão.
   
    Várias réplicas deste serviço podem ser alojadas no mesmo computador, para que este endereço deve ser exclusivo para a réplica. Esta é a razão pela qual o ID de partição + ID de réplica é no URL. HttpListener pode estão à escuta em vários endereços na mesma porta, desde que o prefixo de URL é exclusivo.
   
    O extra GUID existe para um cenário avançado onde as réplicas secundárias são também escutam para pedidos de só de leitura. Quando for esse o caso, deve certificar-se de que um novo endereço exclusivo é utilizado quando a transição do principal para secundária para forçar os clientes para voltar a resolver o endereço. '+' é utilizado como o endereço aqui para que a réplica escuta em todos os anfitriões disponíveis (IP, FQDM, localhost, etc.) O código abaixo mostra um exemplo.
   
    ```CSharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    É também vale a pena realçar que o URL publicado é ligeiramente diferente do prefixo de URL está a escutar.
    O URL do serviço de escuta é atribuído HttpListener. O URL publicado é o URL que é publicado para o serviço Service Fabric Naming, que é utilizado para a deteção do serviço. Os clientes pedirá para este endereço através de que o serviço de deteção. O endereço que os clientes obtêm tem de ter o IP ou FQDN do nó real para se ligar. Por isso terá de substituir '+' com o nó IP ou FQDN, conforme mostrado acima.
9. O último passo consiste em Adicionar a lógica de processamento para o serviço, conforme mostrado abaixo.
   
    ```CSharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "sucessfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest`lê os valores do parâmetro de cadeia de consulta utilizada para chamar a partição e chamadas `AddUserAsync` para adicionar o apelido ao dicionário fiável `dictionary`.
10. Vamos adicionar um serviço sem monitorização de estado para o projeto para ver como pode ligar a uma determinada partição.
    
    Este serviço funciona como uma interface web simples que aceita o apelido como um parâmetro de cadeia de consulta, determina a chave de partição e envia-a para o serviço de Alphabet.Processing para processamento.
11. No **criar um serviço** diálogo caixa, escolha **Stateless** de serviço e chamá-lo "Alphabet.Web" conforme mostrado abaixo.
    
    ![Captura de ecrã do serviço sem monitorização de estado](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Atualize as informações de ponto final de ServiceManifest.xml do serviço Alphabet.WebApi para abrir uma porta conforme mostrado abaixo.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Tem de devolver uma coleção de ServiceInstanceListeners na classe Web. Novamente, pode optar por implementar um HttpCommunicationListener simple.
    
    ```CSharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Agora tem de implementar a lógica de processamento. As chamadas de HttpCommunicationListener `ProcessInputRequest` quando um pedido é apresentada no. Por isso, vamos avançar e adicione o código abaixo.
    
    ```CSharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Vamos guiá-lo passo a passo. O código lê a primeira letra do parâmetro de cadeia de consulta `lastname` para um char. Em seguida, determina a chave de partição para esta letra subtraindo o valor hexadecimal de `A` do valor hexadecimal letra primeiro os nomes de último.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Lembre-se, neste exemplo, que estamos a utilizar 26 partições com a chave de uma partição por partição.
    Em seguida, obtemos a partição de serviço `partition` para esta chave, utilizando o `ResolveAsync` método no `servicePartitionResolver` objeto. `servicePartitionResolver`está definido como
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    O `ResolveAsync` demora método, o URI do serviço, a chave de partição e um cancelamento token como parâmetros. O serviço de URI para o serviço de processamento é `fabric:/AlphabetPartitions/Processing`. Em seguida, vamos obter o ponto final da partição.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Por fim, vamos construir o URL de ponto final e a cadeia de consulta e chamar o serviço de processamento.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Depois do processamento é concluído, vamos escrever novamente a saída.
15. O último passo consiste em testar o serviço. Visual Studio utiliza aplicação parâmetros local e implementação de nuvem. Para testar o serviço com 26 partições localmente, terá de atualizar o `Local.xml` ficheiros na pasta do projeto AlphabetPartitions ApplicationParameters, conforme mostrado abaixo:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Depois de concluir a implementação, pode verificar o serviço e todas as respetivas partições no Service Fabric Explorer.
    
    ![Captura de ecrã do Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. Num browser, pode testar a lógica de criação de partições introduzindo `http://localhost:8081/?lastname=somename`. Verá que cada apelido que começa com a mesma letra está a ser armazenado na mesma partição.
    
    ![Captura de ecrã do browser](./media/service-fabric-concepts-partitioning/samplerunning.png)

O código de origem completo de exemplo está disponível no [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre os conceitos de Service Fabric, consulte o seguinte:

* [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
* [Escalabilidade dos serviços do Service Fabric](service-fabric-concepts-scalability.md)
* [Planeamento da capacidade para aplicações de Service Fabric](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
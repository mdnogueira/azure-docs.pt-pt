---
title: "Escalabilidade dos serviços do Service Fabric | Microsoft Docs"
description: "Descreve como dimensionar serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 680b996e370f66a5e22644ae1d1bf41d314bb4de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-in-service-fabric"></a>Dimensionamento nos recursos de infraestrutura de serviço
Azure Service Fabric torna mais fácil criar aplicações dimensionáveis ao gerir os serviços, partições e réplicas em nós do cluster. Muitas cargas de trabalho a ser executado no mesmo hardware permite a utilização máxima de recursos, mas também fornece flexibilidade em termos de como escolher dimensionar as cargas de trabalho. 

Dimensionamento no Service Fabric é conseguido várias formas diferentes:

1. Dimensionamento ao criar ou remover instâncias de serviço sem monitorização de estado
2. Dimensionamento ao criar ou remover novos com o nome de serviços
3. Dimensionamento ao criar ou remover novos com o nome de instâncias de aplicações
4. Dimensionamento utilizando os serviços particionados
5. Dimensionamento ao adicionar e remover nós do cluster 
6. Dimensionamento, através de métricas de Gestor de recursos de Cluster

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Dimensionamento ao criar ou remover instâncias de serviço sem monitorização de estado
Uma das formas mais simples para dimensionar dentro do Service Fabric funciona com os serviços sem monitorização de estado. Quando cria um serviço sem monitorização de estado, obter uma oportunidade para definir um `InstanceCount`. `InstanceCount`Define o número de cópias em execução de código esse serviço é criado quando inicia o serviço cópia de segurança. Vamos supor que, por exemplo, se existirem 100 nós no cluster. Também Digamos que é criado um serviço com um `InstanceCount` de 10. Durante o tempo de execução, esses 10 cópias em execução do código pode todas ficar demasiado ocupadas (ou pode não ser suficientemente ocupadas). É uma forma de escala que carga de trabalho para alterar o número de instâncias. Por exemplo, alguns fragmento de código de monitorização ou a gestão de pode alterar o número de instâncias existente para 50 ou para 5, dependendo se a carga de trabalho tem de aumentar ou reduzir baseia-se a carga. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Utilizar contagem de instâncias dinâmica
Especificamente para os serviços sem monitorização de estado, o Service Fabric oferece uma forma automática para alterar a contagem de instâncias. Isto permite que o serviço para dimensionar, dinamicamente, com o número de nós que estão disponíveis. A forma de participar neste comportamento é para definir a contagem de instâncias = -1. InstanceCount = -1 é uma instrução para o Service Fabric que diz "Executar este serviço sem monitorização de estado em cada nó." Se alterar o número de nós, o Service Fabric altera automaticamente a contagem de instâncias para fazer corresponder, garantindo que o serviço está em execução em todos os nós válido. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Dimensionamento ao criar ou remover novos com o nome de serviços
Uma instância de serviço com nome é uma instância específica de um tipo de serviço (consulte [ciclo de vida de aplicação de Service Fabric](service-fabric-application-lifecycle.md)) dentro de alguns instância da aplicação com o nome do cluster. 

Novas instâncias de serviço com nome podem ser criadas (ou removidas) como serviços que tornam-se mais ou menos ocupados. Isto permite que os pedidos a serem distribuídos por mais instâncias de serviço, normalmente, permitindo que carga nos serviços existentes para diminuir. Ao criar serviços, o Gestor de recursos de Cluster do Service Fabric coloca os serviços no cluster de forma distribuída. As decisões exatas são regidas pelo [métricas](service-fabric-cluster-resource-manager-metrics.md) no cluster e de outras regras de posicionamento. Os serviços podem ser criados várias formas diferentes, mas as mais comuns são através de ações administrativas como alguém chamar [ `New-ServiceFabricService` ](https://docs.microsoft.com/en-us/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), ou ao chamar código [ `CreateServiceAsync` ](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync`Pode ainda ser chamado a partir de dentro de outros serviços em execução no cluster.

Criar serviços dinamicamente podem ser utilizados em todos os tipos de cenários e é um padrão comum. Por exemplo, considere um serviço com estado que representa um fluxo de trabalho específico. Chamadas que representa o trabalho vai aparecer para este serviço e este serviço vai executar os passos para esse fluxo de trabalho e o registo de progresso. 

Forma como iria tornar o desta escala determinado serviço? O serviço pode ser multi-inquilino de alguma forma, aceita chamadas e iniciar os passos para várias instâncias diferentes do mesmo fluxo de trabalho, uma vez. No entanto, que pode tornar o código mais complexas, dado que agora tem de preocupar com várias instâncias diferentes do mesmo fluxo de trabalho, todos os em diferentes fases e de diferentes clientes. Além disso, a processar vários fluxos de trabalho em simultâneo não resolver o problema de escala. Isto acontece porque, a determinada altura, este serviço irá consumir demasiados recursos para caber num computador específico. Em primeiro lugar a vários serviços não criados para este padrão também experiência dificuldade em devido a algum estrangulamento inerente ou slowdown no seu código. Estes tipos de problemas de fazer com que o serviço não funciona bem quando o número de fluxos de trabalho simultâneos está a controlar obtém maior.  

É uma solução criar uma instância deste serviço para cada instância diferente do fluxo de trabalho que pretende controlar. Esta é um excelente padrão e funciona se o serviço está sem monitorização de estado ou com monitorização de estado. Para este padrão funcionar, há normalmente, outro serviço que age como um "serviço Gestor da carga de trabalho". A tarefa deste serviço é para receber pedidos de e para encaminhar esses pedidos para outros serviços. O gestor pode criar dinamicamente uma instância do serviço de carga de trabalho quando recebe a mensagem e, em seguida, passa nos pedidos a esses serviços. O serviço Gestor também foi receber chamadas de retorno quando concluir a tarefa a um serviço de fluxo de trabalho especificado. Quando o Gestor de recebe estas chamadas de retorno-foi elimine dessa instância do serviço de fluxo de trabalho, ou deixe-o se são esperadas mais chamadas. 

Versões avançadas deste tipo de Gestor mesmo podem criar conjuntos dos serviços que gere. O conjunto de ajuda Certifique-se de que quando um novo pedido é apresentada no não tem de aguardar que o serviço de rotação de cópia de segurança. Em vez disso, o Gestor de pode apenas escolher um serviço de fluxo de trabalho que não está atualmente ocupado do conjunto ou encaminhar aleatoriamente. Manter um conjunto de serviços disponíveis faz com que novos pedidos de processamento mais rápido, uma vez que é menos provável que o pedido tem de aguardar por um novo serviço de ser aceleradas. A criação de novos serviços é rápida, mas não livre ou instantânea. O conjunto de ajuda a minimizar o período de tempo que do pedido tem de aguardar antes de a ser reparado. Muitas vezes, verá esta manager e o padrão de agrupamento quando tempos de resposta é mais relevante. Colocação em fila o pedido e criar o serviço em segundo plano e _, em seguida,_ passou-a também é um padrão de manager populares, tal como está a criar e eliminar serviços com base nos algum controlo da quantidade de trabalho que o serviço atualmente tem pendente . 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Dimensionamento ao criar ou remover novos com o nome de instâncias de aplicações
Criar e eliminar as instâncias de toda a aplicação são semelhante para o padrão de criar e eliminar serviços. Para este padrão, há alguns service manager que está a efetuar a decisão com base nos pedidos que está a ver e as informações que recebe os outros serviços dentro do cluster. 

Quando deve criar uma nova instância de aplicação com o nome utilizado em vez de criar um novo instâncias de serviço com nome aplicação já existente? Há alguns casos:

  * A nova instância de aplicação é de um cliente cujo código tem de ser executado algumas identidade específica ou definições de segurança.
    * Serviço de recursos de infraestrutura permite definir pacotes de código diferentes para ser executado identidades específicas. Para iniciar o mesmo pacote de código em identidades diferentes, as ativações tem de ocorrer em instâncias de aplicação diferente. Considere o caso em que tenha implementadas cargas de trabalho de um cliente existente. Estes poderão estar em execução com uma identidade específica para que possa monitorizar e controlar o acesso a outros recursos, tais como bases de dados remotos ou a outros sistemas. Neste caso, quando um cliente novo inicia a cópia de segurança, provavelmente não pretende ativar o respetivo código no contexto mesmo (espaço de processo). Enquanto foi, isto torna mais difícil para o seu código de serviço funcionar no contexto de uma determinada identidade. Normalmente, tem de ter mais de segurança, isolamento e código de gestão de identidade. Em vez de utilizar outro serviço, as instâncias nomeadas dentro da mesma instância de aplicação e, por conseguinte, o mesmo processo espaço, pode utilizar várias instâncias de aplicação de recursos de infraestrutura de serviço com nome. Isto torna mais fácil definir os contextos de identidade diferentes.
  * A nova instância de aplicação também funciona como um meio de configuração
    * Por predefinição, todas as instâncias de serviço com nome de um tipo de serviço específica dentro de uma instância de aplicação é executada no mesmo processo num determinado nó. Isto significa que embora possa configurar cada instância de serviço diferente, fazê-lo, por isso, é complicado. Serviços têm de ter alguns tokens que utilizam para procurar a respetiva configuração dentro de um pacote de configuração. Normalmente, isto é apenas nome do serviço. Isto funciona bem, mas couples-lo a configuração para os nomes das instâncias do serviço com nome individuais dentro dessa instância da aplicação. Isto pode ser confuso e difícil de gerir uma vez que a configuração é, normalmente, um artefacto de tempo de design com valores específicos de instância de aplicação. Criar mais serviços de sempre significa mais as atualizações de aplicações para alterar as informações nos pacotes de configuração ou para implementar novos para que os novos serviços podem procurar as suas informações específicas. Muitas vezes, é mais fácil criar uma instância com nome de aplicação todo novo. Em seguida, pode utilizar os parâmetros da aplicação para definir não é necessária para os serviços de qualquer configuração. Desta forma, todos os serviços que são criados dentro do que com o nome a instância da aplicação pode herdar as definições de configuração específica. Por exemplo, em vez de ter um único ficheiro de configuração com as definições e personalizações de cada cliente, tais como segredos ou por limites de recursos de cliente, em vez disso, terá de uma instância de aplicação diferente para cada cliente com estas definições substituído. 
  * A nova aplicação funciona como um limite de atualização
    * Dentro do Service Fabric, instâncias de diferentes aplicações nomeado servirem como limites para a atualização. Uma atualização de uma instância com nome de aplicação não irá afetar o código que está em execução outra instância de aplicação com nome. As aplicações diferentes irão surgir a executar versões diferentes do mesmo código em nós do mesmos. Isto pode ser um fator quando precisa de tomar uma decisão de dimensionamento, porque pode escolher se o novo código deve seguir o mesmo é atualizado como outro serviço ou não. Por exemplo, imagine que uma chamada chega o serviço manager que é responsável por dimensionamento de cargas de trabalho de um cliente específico, criar e eliminar serviços dinamicamente. Neste caso, no entanto, a chamada é para uma carga de trabalho associada a um _novo_ ao cliente. Maioria dos clientes, como estando isoladas entre si, não apenas para as razões de segurança e a configuração listadas anteriormente, mas porque fornece mais flexibilidade em termos de versões específicas do software em execução e escolher quando obter atualizados. Também pode criar uma nova instância de aplicação e criar o serviço simplesmente a partição mais a quantidade dos seus serviços que serão touch qualquer uma atualização. Instâncias da aplicação separado fornecem maior granularidade ao efetuar as atualizações de aplicações e também ativar A / implementações de teste e Blue/verde B. 
  * A instância da aplicação existente está cheia
    * No Service Fabric, [capacidade de aplicação](service-fabric-cluster-resource-manager-application-groups.md) é um conceito que pode utilizar para controlar a quantidade de recursos disponíveis para as instâncias da aplicação em particular. Por exemplo, pode decidir que um determinado serviço tem de ter a outra instância criada para dimensionar. No entanto, esta instância de aplicação é sem capacidade de uma métrica de determinadas. Se este cliente específico ou a carga de trabalho ainda deverá ser concedida mais recursos, em seguida, foi a aumentar a capacidade para essa aplicação existente ou crie uma nova aplicação. 

## <a name="scaling-at-the-partition-level"></a>Dimensionamento ao nível da partição
Serviço de recursos de infraestrutura suporta a criação de partições. Criação de partições divide um serviço em várias secções de lógicas e físicas, cada um dos quais funciona de forma independente. Isto é útil com os serviços com monitorização de estado, uma vez que nenhum conjunto de réplicas tem de processar todas as chamadas e manipular todas do Estado de uma só vez. O [descrição geral da criação de partições](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de criação de partições que são suportados. As réplicas de cada partição são distribuídas por nós num cluster, distribuição de carga esse serviço e garantir que nem o serviço como um todo ou qualquer partição tem um ponto único de falha. 

Considere um serviço que utiliza um esquema de partições ranged com uma chave baixa de 0, uma chave superior de 99 e uma contagem da partição de 4. Num cluster de três nós, o serviço poderá ser apresentado com quatro réplicas que partilham recursos em cada nó, conforme mostrado aqui:

<center>
![Esquema de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Se aumentar o número de nós, o Service Fabric irá mover algumas das réplicas existentes não existe. Por exemplo, vamos diga o número de nós aumentam quatro e as réplicas obter redistribuído. O serviço agora tem agora três réplicas em execução em cada nó, cada que pertencem a diferentes partições. Isto permite uma melhor utilização de recursos, uma vez que o novo nó não amovíveis. Normalmente, este também melhora o desempenho como cada serviço tem mais recursos disponíveis.

<center>
![Esquema de partição com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Dimensionamento utilizando o Gestor de recursos de Cluster de recursos de infraestrutura de serviço e métricas
[Métricas](service-fabric-cluster-resource-manager-metrics.md) são como serviços express o respetivo consumo de recursos para o Service Fabric. Utilizar métricas dá-o Gestor de recursos de Cluster uma oportunidade para reorganize e otimizar o esquema do cluster. Por exemplo, poderão existir muitos recursos no cluster, mas não pode ser alocados para os serviços que estão atualmente a fazer o trabalho. Utilizar métricas permite que o Gestor de recursos de Cluster para reorganizar o cluster para garantir que os serviços têm acesso aos recursos disponíveis. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Dimensionamento ao adicionar e remover nós do cluster 
Outra opção de dimensionamento com o Service Fabric está a alterar o tamanho do cluster. Alteração do tamanho do cluster significa adicionar ou remover nós de um ou mais dos tipos de nó do cluster. Por exemplo, considere o caso em que todos os nós do cluster estão ativos. Isto significa que os recursos do cluster estão quase todo consumido. Neste caso, a adição de mais nós para o cluster é a melhor forma de dimensionar. Assim que os novos nós aderirem ao cluster do Gestor de recursos de Cluster do serviço de recursos de infraestrutura move serviços aos mesmos, resultando em carga menos total de nós existentes. Para serviços sem monitorização de estado com uma contagem de instâncias = -1, serviço mais instâncias são criadas automaticamente. Isto permite que algumas chamadas mover de nós existentes para os novos nós. 

Adicionar e remover nós ao cluster podem ser efetuadas através do módulo do PowerShell de Gestor de recursos do Service Fabric do Azure.

```posh
Add-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName  -NumberOfNodesToAdd 5 
Remove-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName -NumberOfNodesToRemove 5
```

## <a name="putting-it-all-together"></a>Passar todos os em conjunto
Vamos ideias que tiver abordados aqui e falar através de um exemplo. Considere o seguinte serviço: está a tentar criar um serviço que age como um livro de endereços, que contém nomes e informações de contacto. 

À direita adiantado tiver um bunch das perguntas relacionadas com a escala: quantos utilizadores vão continuar a ter? Contactos quantos irão armazenar por cada utilizador? Quando lhe é colocado o serviço pela primeira vez ao tentar identificar esta terminar todas as é difícil. Imaginemos que foram vai aceda com um único serviço estático com uma contagem de partição específica. As consequências de diretriz a contagem da partição errado pode fazer com que a ter problemas de dimensionamento mais tarde. Da mesma forma, mesmo que se escolher a contagem à direita que não pode ter todas as informações de precisa. Por exemplo, também terá de decidir o tamanho do cluster adiantado, tanto em termos do número de nós e os respetivos tamanhos. É normalmente difícil de prever a quantidade de recursos um serviço irá consumir longo da respetiva duração. Também pode ser difícil saber antecedência o padrão de tráfego que o serviço, na verdade, vê. Por exemplo, pessoas maybe adicionar e remover os seus contactos apenas primeiro thing da manhã ou talvez que é distribuído uniformemente ao longo do dia. Com base neste que poderá precisar de aumentar o limite e no dinamicamente. Talvez pode saber prever quando que vai precisar de aumentar o limite e no, mas qualquer forma que, provavelmente, vai precisar de reagir a alterar o consumo de recursos pelo seu serviço. Isto pode envolver a alteração do tamanho do cluster para fornecer mais recursos quando reorganizar utilização dos recursos existentes não é suficiente. 

Mas a razão pela qual mesmo tente escolher um esquema de partição única para todos os utilizadores? Por que motivo o limite por si para um serviço e um cluster estático? A situação real é normalmente mais dinâmica. 

Ao criar para escala, considere o seguinte padrão dinâmico. Poderá ter de adaptá-lo à sua situação:

1. Em vez de tentar a escolha de um esquema de partições para todas as pessoas adiantado, crie um serviço"Gestor".
2. A tarefa do serviço Gestor é ver informações de cliente quando se inscrevem no seu serviço. Em seguida, dependendo das informações do service manager criar uma instância do seu _real_ serviço de armazenamento de contacto _apenas para esse cliente_. Que exijam configuração específica, isolamento ou atualizações, também pode optar por rotação de uma instância de aplicação para este cliente. 

Esta criação dinâmica padrão várias vantagens:

  - Não está a tentar adivinhar a contagem da partição correto para todos os utilizadores adiantado ou criar um único serviço infinitamente dimensionável todos no seu próprio. 
  - Diferentes utilizadores não têm de ter o mesmo número de partição, contagem de réplicas, restrições de posicionamento, métricas, predefinição carrega, nomes de serviço, as definições de dns ou qualquer uma das outras propriedades especificadas no nível de serviço ou aplicação. 
  - Obtém a segmentação de dados adicionais. Cada cliente tem as seus próprios cópia do serviço
    - Cada serviço de cliente pode ser configurado de forma diferente e concedido mais ou menos recursos, com mais ou menos partições ou réplicas conforme necessário, com base na respetiva escala esperada.
      - Por exemplo, supor que o cliente pagamento para a camada "Gold" - foi obtêm mais réplicas ou maior contagem da partição e potencialmente recursos dedicado para os seus serviços através das capacidades de métricas e aplicações.
      - Ou diga que são fornecidas informações que indicam o número de contactos que necessário foi "Pequeno" - teria de obter apenas algumas partições, ou mesmo pode ser inseridas um agrupamento de serviço partilhado com outros clientes.
  - Não está a executar um bunch de instâncias de serviço ou réplicas enquanto aguarda para os clientes apresentar
  - Se alguma vez perder um cliente, em seguida, remover as respetivas informações do seu serviço é tão simple como tendo o Gestor de eliminar esse serviço ou aplicação que tenha criado.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Service Fabric, consulte os artigos seguintes:

* [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
* [A criação de partições de serviços do Service Fabric](service-fabric-concepts-partitioning.md)

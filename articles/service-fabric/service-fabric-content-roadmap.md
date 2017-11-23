---
title: Saiba mais sobre o Azure Service Fabric | Microsoft Docs
description: "Saiba mais sobre os conceitos principais e áreas principais do Azure Service Fabric. Fornece uma descrição geral expandida do Service Fabric e como criar micro-serviços."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: 05b57a065f6d92c7c285ef5178b465dc8f419dbc
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Por isso que pretende saber mais sobre o Service Fabric?
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  Service Fabric tem uma área de superfície grande, no entanto, e houver muita para saber mais.  Este artigo fornece um resumo dos recursos de infraestrutura de serviço e descreve os conceitos principais, modelos, o ciclo de vida de aplicação, testar, clusters e monitorização de estado de funcionamento de programação. Leia o [descrição geral](service-fabric-overview.md) e [quais são micro-serviços?](service-fabric-overview-microservices.md) para uma introdução e como os recursos de infraestrutura de serviço podem ser utilizados para criar micro-serviços. Este artigo não contém uma lista completa de conteúdo, mas a ligação para descrição geral e obter artigos iniciados para cada área de Service Fabric. 

## <a name="core-concepts"></a>Conceitos-chave
[Terminologia de Service Fabric](service-fabric-technical-overview.md), [modelo de aplicação](service-fabric-application-model.md), e [suportados modelos de programação](service-fabric-choose-framework.md) fornecer mais conceitos e as descrições, mas aqui são as noções básicas.

<table><tr><th>Conceitos-chave</th><th>Tempo de design</th><th>Tempo de execução</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Tempo de design: tipo de aplicação, o tipo de serviço, pacote de aplicação e manifesto, pacote de serviço e o manifesto
Um tipo de aplicação é o nome/versão atribuído a uma coleção de tipos de serviço. Isto está definido um *ApplicationManifest.xml* ficheiro, que está incorporado num diretório do pacote de aplicação. O pacote de aplicação, em seguida, é copiado para o arquivo de imagens do cluster do Service Fabric. Em seguida, pode criar uma aplicação com o nome deste tipo de aplicação, que, em seguida, é executado no cluster. 

Um tipo de serviço é o nome/versão atribuído a pacotes de código, os pacotes de dados e pacotes de configuração de um serviço. Isto é definido num ficheiro ServiceManifest.xml, que está incorporado num diretório de pacote de serviço. O diretório de pacote de serviço, em seguida, é referenciado por um pacote de aplicação *ApplicationManifest.xml* ficheiro. No cluster, depois de criar uma aplicação com nome, pode criar um serviço com nome de um dos tipos de serviço do tipo de aplicação. Um tipo de serviço é descrito pelo respetivo *ServiceManifest.xml* ficheiro. O tipo de serviço é composto por definições de configuração do código executável serviço, que são carregadas no tempo de execução, e os dados estáticos, que são consumidos pelo serviço.

![Tipos de aplicação de Service Fabric e os tipos de serviço][cluster-imagestore-apptypes]

O pacote de aplicações é um diretório de disco que contém o tipo de aplicação *ApplicationManifest.xml* ficheiro, que referencia os pacotes de serviço para cada tipo de serviço que faz com que o se o tipo de aplicação. Por exemplo, um pacote de aplicação de um tipo de aplicação de e-mail pode conter referências a um pacote de serviço fila, um pacote de serviço de front-end e um pacote de serviço de base de dados. Os ficheiros no diretório do pacote de aplicação são copiados para o arquivo de imagens do cluster do Service Fabric. 

Um pacote de serviço é um diretório de disco que contém o tipo de serviço *ServiceManifest.xml* ficheiro, que referencia o código, dados estáticos e os pacotes de configuração para o tipo de serviço. Os ficheiros no diretório do pacote de serviço são referenciados pelo tipo de aplicação *ApplicationManifest.xml* ficheiro. Por exemplo, um pacote de serviço foi consulte o código, dados estáticos e pacotes de configuração que compõem um serviço de base de dados.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Tempo de execução: clusters e nós, com o nome de aplicações, com o nome de serviços, partições e réplicas
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Clusters podem dimensionar a milhares de máquinas.

Um computador ou a VM que faz parte de um cluster é designado por um nó. Cada nó é atribuído um nome de nó (uma cadeia). Os nós têm características como propriedades de colocação. Cada computador ou a VM tem um serviço de início automático do Windows, `FabricHost.exe`, que inicia a executar após o arranque e, em seguida, inicia duas executáveis: `Fabric.exe` e `FabricGateway.exe`. Estes dois executáveis constituem o nó. Para programação ou para cenários de teste, pode alojar vários nós num único computador ou VM ao executar várias instâncias do `Fabric.exe` e `FabricGateway.exe`.

Uma aplicação com o nome é uma coleção de serviços com nome que efetua um determinado tipo de função ou funções. Um serviço executa uma função de completa e autónoma (que pode iniciar e executar independentemente outros serviços) e é composto de código, configuração e dados. Depois de um pacote de aplicação é copiado para o arquivo de imagens, cria uma instância da aplicação no cluster especificando um tipo de aplicação do pacote de aplicações (utilizando o respetivo nome/versão). Cada instância de tipo de aplicação é atribuída um nome URI que se assemelha *fabric: / MyNamedApp*. Num cluster, pode criar várias aplicações com o nome de um tipo de aplicação único. Também pode criar aplicações com o nome dos tipos de aplicação diferente. Cada aplicação nomeada é gerido e com a versão independentemente.

Depois de criar uma aplicação com nome, pode criar uma instância de um dos respetivos tipos de serviço (um serviço com nome) no cluster, especificando o tipo de serviço (utilizando o respetivo nome/versão). Cada instância de tipo de serviço é atribuída um nome URI em URI da sua aplicação com o nome de âmbito. Por exemplo, se criar um serviço dentro de uma aplicação com o nome "MyNamedApp" com o nome "MyDatabase", o URI aspeto: *fabric: / MyNamedApp/MyDatabase*. Dentro de uma aplicação com nome, pode criar um ou mais serviços com nome. Cada serviço com o nome pode ter o seu próprio esquema de partição e réplica/instância contagens. 

Existem dois tipos de serviços: sem monitorização de estado e com monitorização de estado. Serviços sem monitorização de estado podem armazenar o estado persistente num serviço de armazenamento externo, como o Storage do Azure, SQL Database do Azure ou do Azure Cosmos DB. Utilize um serviço sem monitorização de estado quando o serviço não tem nenhum armazenamento persistente de todo. Um serviço com estado utiliza recursos de infraestrutura de serviço para gerir o estado do seu serviço através das suas coleções fiável ou modelos de programação Reliable Actors. 

Ao criar um serviço com nome, especifique um esquema de partição. Serviços com grandes quantidades de estado de dividir os dados em partições. Cada partição é responsável por uma parte do Estado do serviço, que é distribuído por todos nós do cluster completo.  

O diagrama seguinte mostra a relação entre as aplicações e instâncias de serviço, partições e réplicas.

![As partições e réplicas dentro de um serviço][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Criação de partições, dimensionamento e disponibilidade
[Criação de partições](service-fabric-concepts-partitioning.md) não é exclusivo para o Service Fabric. Uma forma bem conhecida de criação de partições é a criação de partições de dados de mensagens em fila ou a fragmentação. Serviços com monitorização de estado com grandes quantidades de estado de dividir os dados em partições. Cada partição é responsável por uma parte do Estado do serviço concluída. 

As réplicas de cada partição são distribuídas por nós do cluster, que permite que o estado do seu serviço nomeado para [escala](service-fabric-concepts-scalability.md). À medida que os dados necessitam aumentam, aumentam as partições e o Service Fabric efetua novamente o balanceamento partições entre nós para utilizar de forma eficaz de recursos de hardware. Se adicionar novos nós ao cluster, o Service Fabric rebalancear as réplicas de partição entre o aumento do número de nós. Em geral melhora o desempenho da aplicação e diminui a contenção de acesso à memória. Se não estão a ser utilizados nós no cluster de forma eficiente, pode reduzir o número de nós no cluster. Service Fabric novamente efetua novamente o balanceamento de réplicas de partição entre o menor número de nós para tornar uma melhor utilização de hardware em cada nó.

Dentro de uma partição, os serviços com nome sem monitorização de estado ter instâncias enquanto serviços nomeados com monitorização de estado têm as réplicas. Normalmente, sem monitorização de estado com nome serviços apenas alguma vez de ter uma partição, uma vez que não têm nenhum Estado interno. Fornecem as instâncias de partição para [disponibilidade](service-fabric-availability-services.md). Se uma instância falhar, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Com monitorização de estado com o nome dos serviços de manter o respetivo estado dentro de réplicas e de cada partição tem a suas próprias réplica definido. Operações de leitura e escrita são efetuadas a uma réplica (denominada principal). Escrita de alterações para o estado de operações são replicadas para várias outras réplicas (denominadas bases de dados secundárias Active Directory). Se uma réplica falhar, o Service Fabric cria uma nova réplica a partir das réplicas existentes.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Micro-serviços sem monitorização de estado e com monitorização de estado para o Service Fabric
Recursos de infraestrutura de serviço permite-lhe criar aplicações que são compostos micro-serviços ou contentores. Sem monitorização de estado micro-serviços (como gateways de protocolo e web proxies) não manter um Estado mutável fora de um pedido e a resposta do serviço. As funções de trabalho de serviços em nuvem do Azure são um exemplo de um serviço sem estado. Com monitorização de estado micro-serviços (por exemplo, contas de utilizador, as bases de dados, dispositivos, carts compras e filas) mantêm um Estado mutável, autoritativo, para além do pedido e a respetiva resposta. Aplicações de dimensionamento de Internet de hoje consistem de uma combinação de micro-serviços sem monitorização de estado e com monitorização de estado. 

Um differentation chave com o Service Fabric é o foco forte na criação de serviços com monitorização de estado, com o [incorporada modelos de programação ](service-fabric-choose-framework.md) ou de serviços com monitorização de estado. O [cenários de aplicação](service-fabric-application-scenarios.md) descrevem os cenários em que são utilizados serviços com monitorização de estado.

Por que razão tem micro-serviços com monitorização de estado, juntamente com aqueles sem monitorização de estado? Os dois principais motivos pelos quais são:

* Pode criar débito elevado, latência baixa, com tolerância a falha de processamento de transações online services (OLTP) por manter o código e feche a dados no mesmo computador. Alguns exemplos são storefronts interativas, pesquisa, sistemas de Internet das coisas (IoT), sistemas de negociação, sistemas de deteção de processamento e fraude do cartão de crédito e gestão de registo pessoal.
* Pode simplificar a estrutura de aplicação. Com monitorização de estado micro-serviços remover a necessidade de filas adicionais e caches, que são tradicionalmente necessários para abordar os requisitos de disponibilidade e a latência de uma aplicação puramente sem estado. Serviços com monitorização de estado são naturalmente elevada disponibilidade e baixa latência, que reduz o número de peças mover para gerir na sua aplicação como um todo.

## <a name="supported-programming-models"></a>Modelos de programação suportados
Recursos de infraestrutura de serviço oferece várias formas para escrever e gerir os seus serviços. Serviços podem utilizar as APIs de recursos de infraestrutura de serviço para tirar partido das funcionalidades e estruturas de aplicações da plataforma. Os serviços também podem ser qualquer programa do executável compilado escritos em qualquer idioma e alojadas num cluster do Service Fabric. Para obter mais informações, consulte [suportados modelos de programação](service-fabric-choose-framework.md).

### <a name="containers"></a>Contentores
Por predefinição, o Service Fabric implementa e ativa serviços como processos. Serviço de recursos de infraestrutura também pode implementar serviços no [contentores](service-fabric-containers-overview.md). Importante ainda, pode combinar os serviços de processos e serviços nos contentores na mesma aplicação. Serviço de recursos de infraestrutura suporta a implementação do Linux contentores contentores do Windows no Windows Server 2016. Pode implementar as aplicações existentes, serviços sem monitorização de estado ou serviços com monitorização de Estado nos contentores. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) é uma estrutura simples de escrita de serviços que integram com a plataforma de Service Fabric e beneficiam o conjunto completo de funcionalidades da plataforma. Reliable Services podem ser sem monitorização de estado (semelhantes a maioria das plataformas de serviço, tais como servidores web ou funções de trabalho na Cloud Services do Azure), em que estado é persistente numa solução externa, como a base de dados do Azure ou o Table Storage do Azure. Reliable Services também podem ser com monitorização de estado, onde o estado é persistente diretamente no serviço de si próprio ao utilizar coleções fiável. Estado é efetuado [elevada](service-fabric-availability-services.md) através da replicação e distribuídos através de [criação de partições](service-fabric-concepts-partitioning.md), todos os geridos automaticamente pelo Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Desenvolvida Reliable Services, o [Ator fiável](service-fabric-reliable-actors-introduction.md) framework é uma arquitetura de aplicações que implementa o padrão de Ator Virtual, com base num padrão de conceção ator. A estrutura de Ator fiável utiliza independentes unidades de estado e de computação com single-threaded execução chamada atores. O Ator fiável framework fornece criada na comunicação de atores e previamente definir configurações de persistência e escalável de estado.

### <a name="aspnet-core"></a>Núcleo de ASP.NET
Integra de Service Fabric [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) como um modelo de programação de primeira classe para criar aplicações API e web.  ASP.NET Core podem ser utilizada duas formas diferentes no Service Fabric:

- Alojado como um executável de convidado. Isto é principalmente utilizado para executar aplicações ASP.NET Core existentes em recursos de infraestrutura de serviço sem alterações de código.
- Execute dentro de um serviço fiável. Isto permite uma melhor integração com o tempo de execução do Service Fabric e permite que o ASP.NET Core com monitorização de estado dos serviços.

### <a name="guest-executables"></a>Executáveis de convidado
A [executável convidado](service-fabric-deploy-existing-app.md) é um existente, arbitrário executável (escrito em qualquer idioma) alojado num cluster do Service Fabric juntamente com outros serviços. Executáveis convidado não integrar diretamente com APIs de recursos de infraestrutura de serviço. No entanto, ainda beneficiam das funcionalidades a plataforma oferece, tais como estado de funcionamento personalizado e carregar o relatório e o serviço deteção ao chamar as APIs REST. Também têm o suporte de ciclo de vida de aplicação completa. 

## <a name="application-lifecycle"></a>Ciclo de vida da aplicação
Com outras plataformas, uma aplicação no Service Fabric normalmente realiza as seguintes fases: estrutura, desenvolvimento, teste, implementação, a atualização, manutenção e remoção. O Service Fabric fornece suporte de primeira classe para o ciclo de vida de aplicação completa das aplicações em nuvem, de desenvolvimento através da implementação, gestão diária e manutenção para desativar eventual. O modelo de serviço permite várias funções diferentes para participar independentemente no ciclo de vida de aplicação. [Ciclo de vida de aplicação de Service Fabric](service-fabric-application-lifecycle.md) fornece uma descrição geral de APIs e como são utilizados pelas funções de diferentes durante as fases do ciclo de vida de aplicação de Service Fabric. 

O ciclo de vida de aplicação completa pode ser gerido utilizando [cmdlets do PowerShell](/powershell/module/ServiceFabric/), [c# APIs](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [APIs de Java](/java/api/system.fabric._application_management_client), e [REST APIs](/rest/api/servicefabric/). Também pode configurar a integração contínua/contínua implementação pipelines com ferramentas como [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) ou [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

O vídeo Microsoft Virtual Academy seguinte descreve como gerir o ciclo de vida de aplicação:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Testar aplicações e serviços
Para criar verdadeiramente serviços de escala da nuvem, é fundamental para verificar que as suas aplicações e serviços podem conseguir falhas do mundo real. O serviço de análise de falhas foi concebido para serviços que são criados no Service Fabric de teste. Com o [falhas Analysis Service](service-fabric-testability-overview.md), pode induce falhas significativas e executar cenários de teste concluída contra as suas aplicações. Estes cenários de falhas e exercer e validar a vários Estados e transições que irá ocorrer um serviço em toda a respetiva duração, todos os de forma consistente, controlada e segura.

[Ações](service-fabric-testability-actions.md) um serviço para testes com falhas individuais de destino. Um programador de serviço pode utilizá-las como blocos modulares para escrever cenários mais complicados. Exemplos de simulada falhas são:

* Reinicia um nó para simular qualquer número de situações em que uma VM ou o computador for reiniciado.
* Mova uma réplica do seu serviço de monitorização de estado para simular o balanceamento de carga, ativação pós-falha ou atualização da aplicação.
* Invocar perda de quórum num serviço com monitorização de estado para criar uma situação em que as operações de escrita não é possível continuar porque não existe não são suficientes réplicas "cópia de segurança" ou "secundários" para aceitar novos dados.
* Invocar perda de dados num serviço com monitorização de estado para criar uma situação em que todos os Estados de memória é completamente eliminados.

[Cenários](service-fabric-testability-scenarios.md) são operações complexas de compostas por uma ou mais ações. O serviço de análise de falhas fornece dois cenários completos incorporados:

* [Cenário de Chaos](service-fabric-controlled-chaos.md)-simula contínuas, intercaladas falhas (correto e ungraceful) em todo o cluster ao longo de períodos de tempo prolongados.
* [Cenário de ativação pós-falha](service-fabric-testability-scenarios.md#failover-test)-uma versão do cenário de teste chaos que tenha como destino uma partição de serviço específicos, mantendo a outros serviços afetados.

## <a name="clusters"></a>Clusters
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Clusters podem dimensionar a milhares de máquinas. Um computador ou a VM que faz parte de um cluster é designado por um nó de cluster. Cada nó é atribuído um nome de nó (uma cadeia). Os nós têm características como propriedades de colocação. Cada computador ou a VM tem um serviço de início automático, `FabricHost.exe`, que inicia a executar após o arranque e, em seguida, inicia duas executáveis: Fabric.exe e FabricGateway.exe. Estes dois executáveis constituem o nó. Para cenários de teste, pode alojar vários nós num único computador ou VM ao executar várias instâncias do `Fabric.exe` e `FabricGateway.exe`.

Clusters de Service Fabric podem ser criados na máquinas virtuais ou físicas que executem Windows Server ou Linux. Conseguir implementar e executar aplicações de Service Fabric em qualquer ambiente em que tem um conjunto de computadores Windows Server ou Linux que estão interligados: no local, no Microsoft Azure ou em qualquer fornecedor de nuvem.

A seguinte Microsoft Virtual Academy vídeo descreve clusters de Service Fabric:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Clusters no Azure
Service Fabric os clusters em execução no Azure disponibiliza a integração com outras funcionalidades do Azure e os serviços, tornando operações e gestão do cluster mais fácil e mais fiável. Um cluster é um recurso do Azure Resource Manager, para modelar os clusters como quaisquer outros recursos no Azure. Gestor de recursos também fornece o facilitar a gestão de todos os recursos utilizados pelo cluster, como uma única unidade. Clusters no Azure estão integradas no diagnóstico do Azure e análise de registos. Os tipos de nó de cluster são [conjuntos de dimensionamento de máquina virtual](/azure/virtual-machine-scale-sets/index), pelo que a funcionalidade de dimensionamento automático é incorporada.

Pode criar um cluster no Azure através de [portal do Azure](service-fabric-cluster-creation-via-portal.md), de um [modelo](service-fabric-cluster-creation-via-arm.md), ou a partir de [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric no Linux permite-lhe criar, implementar e gerir aplicações de elevada disponibilidade e altamente dimensionáveis no Linux, tal como faria no Windows. As estruturas de Service Fabric (Reliable Services e Reliable Actors) estão disponíveis em Java em Linux, além de c# (.NET Core). Também pode criar [os serviços de convidados executável](service-fabric-deploy-existing-app.md) com qualquer linguagem ou arquitetura. Também é suportada a da orquestração contentores de Docker. Os contentores de docker podem executar executáveis de convidado ou serviços do Service Fabric nativos, que utilizam as estruturas de Service Fabric. Para obter mais informações, leia sobre [Service Fabric no Linux](service-fabric-deploy-anywhere.md).

Existem algumas funcionalidades que são suportadas no Windows, mas não no Linux. Para obter mais informações, leia [diferenças entre o Service Fabric no Linux e Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Clusters autónomos
O Service Fabric fornece um pacote de instalação para que possa criar autónomo Service Fabric clusters no local ou em qualquer fornecedor de nuvem. Clusters autónomos dão-lhe ter a liberdade de um cluster de anfitriões onde quiser. Se os dados estão sujeitos a conformidade ou regulamentação restrições, ou se pretende manter os seus dados local, pode alojar o seu próprio cluster e aplicações. Aplicações de Service Fabric podem ser executadas em vários ambientes de alojamento sem alterações, pelo que os dados de conhecimento da criação de aplicações acarreta de um ambiente de alojamento para outro. 

[Criar o primeiro cluster de autónoma do Service Fabric](service-fabric-get-started-standalone-cluster.md)

Clusters do Linux autónomos ainda não são suportadas.

### <a name="cluster-security"></a>Segurança do cluster
Clusters tem de estar protegidos para impedir que utilizadores não autorizados a ligar ao seu cluster, sobretudo quando tem cargas de trabalho de produção em execução no mesmo. Embora seja possível criar um cluster não segura, fazê-lo, por isso, permite que os utilizadores anónimos ligar ao mesmo se os pontos finais de gestão estão expostos à internet pública. Não é possível que mais tarde ative a segurança num cluster protegida: segurança do cluster está ativada no momento de criação do cluster.

Estes são os cenários de segurança do cluster:
* Segurança de nó de nó
* Segurança de nó de cliente
* Controlo de acesso baseado em funções (RBAC)

Para obter mais informações, leia o artigo [proteger um cluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Dimensionamento
Se adicionar novos nós ao cluster, Service Fabric efetua novamente o balanceamento as réplicas de partição e as instâncias no aumento do número de nós. Em geral melhora o desempenho da aplicação e diminui a contenção de acesso à memória. Se não estão a ser utilizados nós no cluster de forma eficiente, pode reduzir o número de nós no cluster. Service Fabric novamente efetua novamente o balanceamento as réplicas de partição e as instâncias em que o menor número de nós para tornar uma melhor utilização de hardware em cada nó. Pode dimensionar clusters no Azure ou [manualmente](service-fabric-cluster-scale-up-down.md) ou [programaticamente](service-fabric-cluster-programmatic-scaling.md). Podem ser escalados para clusters autónomos [manualmente](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Atualizações de cluster
Periodicamente, são lançadas novas versões de tempo de execução do Service Fabric. Efetua as atualizações de tempo de execução ou recursos de infraestrutura, no seu cluster, para que executem sempre um [versão suportada](service-fabric-support.md). Para além das atualizações de recursos de infraestrutura, também pode atualizar a configuração de cluster, tais como certificados ou as portas de aplicação.

Um cluster do Service Fabric é um recurso que possui, mas está parcialmente gerida pela Microsoft. Microsoft é responsável pela aplicação de patches de SO subjacente e efetuar as atualizações de recursos de infraestrutura no seu cluster. Pode definir o cluster para receber atualizações de recursos de infraestrutura automática, quando a Microsoft disponibiliza uma nova versão, ou optar por selecionar uma versão suportada de recursos de infraestrutura que pretende. As atualizações de recursos de infraestrutura e a configuração podem ser definidas através do portal do Azure ou através do Resource Manager. Para obter mais informações, leia o artigo [atualizar um cluster do Service Fabric](service-fabric-cluster-upgrade.md). 

Um cluster autónomo é um recurso que é completamente próprio. É responsável pela aplicação de patches de SO subjacente e o início de atualizações de recursos de infraestrutura. Se o cluster pode ligar ao [https://www.microsoft.com/download](https://www.microsoft.com/download), pode definir o cluster para transferir automaticamente e aprovisionar o novo pacote de tempo de execução do Service Fabric. Em seguida, deverá iniciar a atualização. Se o cluster não conseguir aceder [https://www.microsoft.com/download](https://www.microsoft.com/download), pode transferir manualmente o novo pacote de tempo de execução a partir de uma máquina ligado à internet e, em seguida, iniciar a atualização. Para obter mais informações, leia o artigo [atualizar um cluster de Service Fabric autónomo](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorização do estado de funcionamento
Service Fabric apresenta um [modelo de estado de funcionamento](service-fabric-health-introduction.md) concebido para sinalizador cluster mau estado de funcionamento e condições de aplicação no entidades específicas (como nós de cluster e réplicas do serviço). O modelo de estado de funcionamento utiliza Informadores de estado de funcionamento (componentes de sistema e watchdogs). O objetivo é rápido e fácil de diagnóstico e de reparação. Os escritores de serviço tem de pensar compromisso sobre estado de funcionamento e como [relatórios de estado de funcionamento de design](service-fabric-report-health.md#design-health-reporting). Qualquer condição que pode afetar o estado de funcionamento deve ser comunicada, especialmente se pode ajudar a problemas de sinalizador próximo raiz. As informações de estado de funcionamento podem poupar tempo e esforço na depuração e investigação de uma vez, o serviço está a funcionar à escala na produção.

O monitor do Service Fabric Informadores identificados condições de interesse. Estes relatórios sobre as condições com base na respetiva vista local. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) agrega dados de estado de funcionamento enviados por todos os Informadores para determinar se entidades são bom estado de funcionamento global. O modelo se destina a ser avançado, flexíveis e fáceis de utilizar. A qualidade dos relatórios de estado de funcionamento determina a precisão da vista de estado de funcionamento do cluster. Falsos positivos que mostram erradamente problemas mau estado de funcionamento podem afetar negativamente as atualizações ou outros serviços que utilizam dados de estado de funcionamento. Exemplos desses serviços são serviços de reparação e mecanismos de alertas. Por conseguinte, algumas profundamente é necessário para fornecer relatórios que capturam as condições de interesse da melhor forma possível.

Relatórios podem ser feito de:
* A réplica de serviço do Service Fabric monitorizados ou a instância.
* Watchdogs internos implementados como um serviço do Service Fabric (por exemplo, um serviço sem monitorização de estado serviço Fabric que monitoriza condições e relatórios de problemas). Os watchdogs podem ser implementados em todos os nós ou podem ser com afinidade com o serviço monitorizado.
* Watchdogs internos que executam em nós de Service Fabric, mas não estão implementados como serviços do Service Fabric.
* Watchdogs externos que o recurso a partir de fora do cluster de Service Fabric (por exemplo, serviço de monitorização, como Gomez) de pesquisa.

A Box, componentes do Service Fabric reportam o estado de funcionamento em todas as entidades no cluster. [Relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) proporcionam visibilidade do cluster e aplicação funcionalidade e sinalizador problemas através do Estado de funcionamento. Para aplicações e serviços, relatórios de estado de funcionamento do sistema Certifique-se de que as entidades são implementadas e se comportam corretamente da perspetiva de tempo de execução do Service Fabric. Os relatórios não fornecem qualquer monitorização de estado de funcionamento da lógica de negócio do serviço ou detetar processos bloqueados. Para adicionar informações de estado de funcionamento específicas a lógica do seu serviço, [implementar o relatório de estado de funcionamento personalizado](service-fabric-report-health.md) nos serviços.

O Service Fabric fornece várias formas de [ver relatórios de estado de funcionamento](service-fabric-view-entities-aggregated-health.md) agregados no arquivo de estado de funcionamento:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização.
* Consultas de estado de funcionamento (através de [PowerShell](/powershell/module/ServiceFabric/), a [c# FabricClient APIs](/dotnet/api/system.fabric.fabricclient.healthclient) e [Java FabricClient APIs](/java/api/system.fabric._health_client), ou [REST APIs](/rest/api/servicefabric)).
* Geral consulta retorno ou uma lista de entidades que tenham o estado de funcionamento como uma das propriedades (através do PowerShell, a API ou REST).

O vídeo Microsoft Virtual Academy seguinte descreve o modelo de estado de funcionamento de recursos de infraestrutura de serviço e como são utilizadas:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autónomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Experimente criar um serviço com os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como [migrar a partir de serviços em nuvem](service-fabric-cloud-services-migration-differences.md).
* Saiba como [monitorizar e diagnosticar os serviços](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Saiba como [testar as suas aplicações e serviços](service-fabric-testability-overview.md).
* Saiba como [gerir e orquestram os recursos do cluster](service-fabric-cluster-resource-manager-introduction.md).
* Examine o [exemplos de Service Fabric](http://aka.ms/servicefabricsamples).
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md).
* Leia o [blogue de equipa](https://blogs.msdn.microsoft.com/azureservicefabric/) para artigos e anúncios.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

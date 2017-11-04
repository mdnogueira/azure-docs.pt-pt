---
title: Saiba terminologia do Azure Service Fabric | Microsoft Docs
description: "Uma descrição geral de terminologia do Service Fabric. Descreve os conceitos de terminologia-chave e termos utilizados no resto da documentação."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: 2204fe7393cddb88c18415768a4c6f8494e83d7b
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="service-fabric-terminology-overview"></a>Descrição geral de terminologia do Service Fabric
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. Este artigo fornece detalhes sobre a terminologia utilizada pelo serviço de recursos de infraestrutura para compreender os termos utilizados na documentação.

Os conceitos apresentados nesta secção são também apresentados os vídeos seguintes do Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">principais conceitos</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">conceitos do momento da conceção</a>, e <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">conceitos de tempo de execução</a>.

## <a name="infrastructure-concepts"></a>Conceitos de infraestrutura
**Cluster**: um conjunto de ligados à rede de máquinas virtuais ou físicos para o qual os micro-serviços são implementados e geridos.  Clusters podem dimensionar a milhares de máquinas.

**Nó**: um computador ou a VM que faz parte de um cluster é designado uma *nó*. Cada nó é atribuído um nome de nó (uma cadeia). Os nós têm características, tais como as propriedades de colocação. Cada computador ou a VM tem um serviço de início automático do Windows, `FabricHost.exe`, que inicia a executar após o arranque e, em seguida, inicia duas executáveis: `Fabric.exe` e `FabricGateway.exe`. Estes dois executáveis constituem o nó. Para cenários de teste, pode alojar vários nós num único computador ou VM ao executar várias instâncias do `Fabric.exe` e `FabricGateway.exe`.

## <a name="application-concepts"></a>Conceitos da aplicação
**Tipo de aplicação**: O nome/versão atribuído a uma coleção de tipos de serviço. Está definido num `ApplicationManifest.xml` de ficheiros e incorporado num diretório do pacote de aplicação. O diretório é, em seguida, copiado para o arquivo de imagens do cluster do Service Fabric. Em seguida, pode criar uma aplicação com o nome deste tipo de aplicação no cluster.

Leia o [modelo de aplicação](service-fabric-application-model.md) artigo para obter mais informações.

**Pacote de aplicação**: um diretório de disco que contém o tipo de aplicação `ApplicationManifest.xml` ficheiro. Referencia os pacotes de serviço para cada tipo de serviço que faz com que o se o tipo de aplicação. Os ficheiros no diretório do pacote de aplicação são copiados para o arquivo de imagens do cluster do Service Fabric. Por exemplo, um pacote de aplicação de um tipo de aplicação de e-mail pode conter referências a um pacote de serviço fila, um pacote de serviço de front-end e um pacote de serviço de base de dados.

**Com o nome de aplicação**: depois de copiar um pacote de aplicação para o arquivo de imagens, criar uma instância da aplicação no cluster. Criar uma instância quando especificar o tipo de aplicação do pacote de aplicações, utilizando o respetivo nome ou versão. Cada instância de tipo de aplicação é atribuída um nome de identificador (URI) de recurso uniforme que se assemelha: `"fabric:/MyNamedApp"`. Num cluster, pode criar várias aplicações com o nome de um tipo de aplicação único. Também pode criar aplicações com o nome dos tipos de aplicação diferente. Cada aplicação nomeada é gerido e com a versão independentemente.      

**Tipo de serviço**: O nome/versão atribuído a pacotes de código, os pacotes de dados e pacotes de configuração de um serviço. O tipo de serviço está definido no `ServiceManifest.xml` de ficheiros e incorporado num diretório do pacote de serviço. O diretório de pacote de serviço, em seguida, é referenciado por um pacote de aplicação `ApplicationManifest.xml` ficheiro. No cluster, depois de criar uma aplicação com nome, pode criar um serviço com nome de um dos tipos de serviço do tipo de aplicação. O tipo de serviço `ServiceManifest.xml` ficheiro descreve o serviço.

Leia o [modelo de aplicação](service-fabric-application-model.md) artigo para obter mais informações.

Existem dois tipos de serviços:

* **Sem monitorização de estado**: utilizar um serviço sem monitorização de estado quando o estado do serviço persistentes é armazenado num serviço armazenamento externo, como o Storage do Azure, SQL Database do Azure ou do Azure Cosmos DB. Utilize um serviço sem monitorização de estado quando o serviço não tem nenhum armazenamento persistente. Por exemplo, para um serviço de Calculadora onde os valores são transmitidos para o serviço, a computação é realizada que utiliza estes valores e, em seguida, é devolvido um resultado.
* **Com monitorização de estado**: Utilize um serviço com estado quando pretender que o serviço de recursos de infraestrutura para gerir o estado do seu serviço através das suas coleções fiável ou Reliable Actors modelos de programação. Quando cria um serviço com nome, especifique quantos partições que pretende para distribuir o seu estado através de escalabilidade. Também pode especificar quantas vezes ao replicar o seu estado em nós de fiabilidade. Cada serviço com nome tem uma única réplica primária e várias réplicas secundárias. Modificar o estado do serviço com nome quando escreve para a réplica primária. Em seguida, o Service Fabric replica neste estado para todas as réplicas secundárias para manter o estado sincronizado. Service Fabric Deteta automaticamente quando uma réplica primária falha e promove uma réplica secundária existente para uma réplica primária. Service Fabric, em seguida, cria uma nova réplica secundária.  

**As réplicas ou instâncias** referir-se a código (e estado de serviços com monitorização de estado) de um serviço implementado e em execução. Consulte [réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md).

**Reconfiguração** refere-se ao processo de qualquer alteração no conjunto de réplicas de um serviço. Consulte [reconfiguração](service-fabric-concepts-reconfiguration.md).

**Pacote de serviço**: um diretório de disco que contém o tipo de serviço `ServiceManifest.xml` ficheiro. Este ficheiro referencia o código, dados estáticos e os pacotes de configuração para o tipo de serviço. Os ficheiros no diretório do pacote de serviço são referenciados pelo tipo de aplicação `ApplicationManifest.xml` ficheiro. Por exemplo, um pacote de serviço pode referir-se ao código, dados estáticos e os pacotes de configuração que compõem um serviço de base de dados.

**Com o nome de serviço**: depois de criar uma aplicação com nome, pode criar uma instância de um dos respetivos tipos de serviço no cluster. Especifique o tipo de serviço utilizando o respetivo nome/versão. Cada instância de tipo de serviço é atribuída um nome URI em URI da sua aplicação com o nome de âmbito. Por exemplo, se criar um serviço dentro de uma aplicação com o nome "MyNamedApp" com o nome "MyDatabase", o URI aspeto: `"fabric:/MyNamedApp/MyDatabase"`. Dentro de uma aplicação com nome, pode criar vários serviços com nome. Cada serviço com o nome pode ter o seu próprio esquema de partição e a instância ou contagens de réplica.

**O pacote do código**: um diretório de disco que contém os ficheiros executáveis o tipo de serviço, ficheiros. EXE/DLL normalmente. Os ficheiros no diretório do pacote de código são referenciados pelo tipo de serviço `ServiceManifest.xml` ficheiro. Quando cria um serviço com nome, o pacote do código é copiado para o nó ou nós selecionados para executar o serviço com nome. Em seguida, o código começa a ser executada. Existem dois tipos de executáveis de pacote do código:

* **Executáveis convidado**: executáveis que são executados como-no sistema operativo anfitrião (Windows ou Linux). Estes executáveis não ligar a ou referenciar os ficheiros de tempo de execução do Service Fabric e, por conseguinte, não utilize os modelos de programação do Service Fabric. Estes executáveis não conseguem utilizar algumas funcionalidades do Service Fabric, tais como o serviço de nomenclatura para a deteção de ponto final. Executáveis convidado não é possível comunicar as métricas de carga que são específicas para cada instância de serviço.
* **Serviço de anfitrião executáveis**: executáveis que utilizam o Service Fabric modelos de programação, ligando os ficheiros de tempo de execução do Service Fabric, ativar funcionalidades do Service Fabric. Por exemplo, uma instância de serviço com o nome pode registar os pontos finais Naming Service do Service Fabric e também pode comunicar métricas de carga.      

**Pacote de dados**: um diretório de disco que contém ficheiros de dados estático só de leitura do tipo de serviço, normalmente, fotografias, ficheiros de som e vídeos. Os ficheiros no diretório do pacote de dados são referenciados pelo tipo de serviço `ServiceManifest.xml` ficheiro. Quando cria um serviço com nome, o pacote de dados é copiado para o nó ou nós selecionados para executar o serviço com nome. O código de entra em execução e pode agora aceder aos ficheiros de dados.

**Pacote de configuração**: um diretório de disco que contém ficheiros de configuração estático só de leitura do tipo de serviço, normalmente, os ficheiros de texto. Os ficheiros no diretório do pacote de configuração são referenciados pelo tipo de serviço `ServiceManifest.xml` ficheiro. Quando cria um serviço com nome, os ficheiros no pacote de configuração são copiados de um ou mais nós selecionados para executar o serviço com nome. Em seguida, inicia o código executar e pode agora acesso os ficheiros de configuração.

**Contentores**: por predefinição, o Service Fabric implementa e ativa serviços como processos. Serviço de recursos de infraestrutura também pode implementar serviços nas imagens de contentor. Contentores são uma tecnologia de Virtualização que Virtualiza do sistema operativo subjacente de aplicações. Uma aplicação e o tempo de execução, dependências e bibliotecas de sistema executam dentro de um contentor. O contentor tem completa, acesso privado para a vista de contentor próprio isolada das construções de sistema operativo. Service Fabric suporta contentores de Docker em contentores de Linux e Windows Server. Para obter mais informações, leia o artigo [Service Fabric e contentores](service-fabric-containers-overview.md).

**Esquema de partição**: ao criar um serviço com nome, especifique um esquema de partição. Serviços com quantidades significativas de estado dividir os dados por partições, que se propaga o estado de entre os nós do cluster. Ao dividir os dados em partições, pode dimensionar o estado do serviço com nome. Dentro de uma partição, os serviços com nome sem monitorização de estado ter instâncias, enquanto com o nome dos serviços de monitorização de estado têm as réplicas. Normalmente, denominados de serviços sem monitorização de estado tem apenas uma partição, porque não têm nenhum Estado interno. Fornecem as instâncias de partição de disponibilidade. Se uma instância falhar, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Com monitorização de estado com o nome dos serviços de manter o respetivo estado dentro de réplicas e de cada partição tem a suas próprias réplica definida para que o estado é mantido sincronizado. Se uma réplica falhar, o Service Fabric cria uma nova réplica a partir das réplicas existentes.

Leia o [serviços fiáveis partição Service Fabric](service-fabric-concepts-partitioning.md) artigo para obter mais informações.

## <a name="system-services"></a>Serviços do sistema
Existem serviços de sistema que são criados em cada cluster que fornece as capacidades de plataforma do Service Fabric.

**Serviço de nomenclatura**: cluster de cada Service Fabric tem um serviço de nomenclatura, que é resolvido nomes de serviço para uma localização no cluster. Gerir os nomes de serviço e propriedades, como um internet sistema de nome de domínio (DNS) para o cluster. Os clientes comunicarem de forma segura com qualquer nó no cluster utilizando o serviço de nomenclatura para resolver um nome de serviço e a respetiva localização. Mover as aplicações no cluster. Por exemplo, isto pode ser devido a falhas, balanceamento de recurso ou o redimensionamento do cluster. Pode desenvolver serviços e os clientes que resolver a localização de rede atual. Os clientes obter o endereço IP da máquina real e a porta em que está atualmente a ser executado.

Leitura [Communicate com serviços](service-fabric-connect-and-communicate-with-services.md) para obter mais informações sobre a comunicação de cliente e o serviço de APIs que funcionam com o serviço de nomenclatura.

**Serviço de arquivo de imagens**: cluster de cada Service Fabric tem um serviço de arquivo de imagens onde os pacotes de aplicações implementadas, com a versão são mantidos. Copiar um pacote de aplicação para o arquivo de imagens e, em seguida, registe o tipo de aplicação contido nesse pacote de aplicação. Depois do tipo de aplicação é aprovisionado, criar uma aplicação com o nome do mesmo. Pode anular o registo de um tipo de aplicação do serviço de arquivo de imagens depois de todas as aplicações com nome tem sido eliminadas.

Leitura [compreender a definição de ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para obter mais informações sobre o serviço de arquivo de imagens.

Leia o [implementar uma aplicação](service-fabric-deploy-remove-applications.md) artigo para obter mais informações sobre como implementar aplicações para o serviço de arquivo de imagens.

**Serviço do Gestor de ativação pós-falha**: cluster de cada Service Fabric tem um serviço do Gestor de ativação pós-falha que é responsável pelas seguintes ações:
   - Efetua funções relacionadas com elevada disponibilidade e a consistência dos serviços.
   - Orquestra as atualizações de aplicações e do cluster.
   - Interage com outros componentes do sistema.

## <a name="built-in-programming-models"></a>Modelos de programação incorporados
Modelos de programação do .NET Framework estão disponíveis para que possa criar serviços do Service Fabric:

**Reliable Services**: uma API para criar serviços sem monitorização de estado e com monitorização de estado. Serviços com monitorização de estado armazenam o respetivo estado em coleções fiável, como um dicionário ou uma fila. Também pode fixação em vários pilhas de comunicação, tais como Web API e Windows Communication Foundation (WCF).

**Reliable Actors**: uma API para criar objetos sem monitorização de estado e com monitorização de estado através do modelo de programação de Ator virtual. Este modelo é útil quando tem muitas unidades independentes de cálculo ou estado. Este modelo utiliza um baseado em Ativar modelo de thread, pelo é melhor evitar o código que chama a outros serviços de atores ou porque uma ator individuais não é possível processar outros pedidos recebidos, até que todos os pedidos saídos estiverem concluídos.

Leia o [escolher um modelo de programação para o seu serviço](service-fabric-choose-framework.md) artigo para obter mais informações.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Service Fabric:

* [Descrição geral do Service Fabric](service-fabric-overview.md)
* [Por que motivo um micro-serviços abordagem de criação de aplicações?](service-fabric-overview-microservices.md)
* [Cenários de aplicações](service-fabric-application-scenarios.md)



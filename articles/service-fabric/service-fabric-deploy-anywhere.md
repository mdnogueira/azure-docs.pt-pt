---
title: Criar clusters do Azure Service Fabric no Windows Server e do Linux | Microsoft Docs
description: "Clusters de Service Fabric executadas no servidor do Windows e Linux, que significa que poderá implementar e aplicações em qualquer local do anfitrião do Service Fabric pode executar o Windows Server ou Linux."
services: service-fabric
documentationcenter: .net
author: Chackdan
manager: timlt
editor: 
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: chackdan
ms.openlocfilehash: e3cfad19e42af24edd68befd7b1eac8cef41a1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Criar clusters de Service Fabric no Windows Server ou Linux
Um cluster do Service Fabric do Azure é um conjunto de ligados à rede de máquinas virtuais ou físicos para o qual os micro-serviços são implementados e geridos. Um computador ou a VM que faz parte de um cluster é designado por um nó de cluster. Clusters podem dimensionar a milhares de nós. Se adicionar novos nós ao cluster, Service Fabric efetua novamente o balanceamento as réplicas de partição de serviço e as instâncias no aumento do número de nós. Em geral melhora o desempenho da aplicação e diminui a contenção de acesso à memória. Se não estão a ser utilizados nós no cluster de forma eficiente, pode reduzir o número de nós no cluster. Service Fabric novamente efetua novamente o balanceamento as réplicas de partição e as instâncias em que o menor número de nós para tornar uma melhor utilização de hardware em cada nó.

Service Fabric permite a criação de clusters de Service Fabric em quaisquer VMs ou computadores que executam o Windows Server ou Linux. Isto significa que é capazes de implementar e executar aplicações de Service Fabric em qualquer ambiente em que tem um conjunto de computadores Windows Server ou Linux que estão interligados, sê-lo no local, Microsoft Azure ou com qualquer fornecedor de nuvem.

## <a name="create-service-fabric-clusters-on-azure"></a>Criar clusters do Service Fabric no Azure
Criar um cluster no Azure é efetuada uma através de um modelo de modelo de recursos ou o [portal do Azure](https://portal.azure.com). Leitura [criar um cluster do Service Fabric utilizando um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md) ou [criar um cluster do Service Fabric do portal do Azure](service-fabric-cluster-creation-via-portal.md) para obter mais informações.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Sistemas operativos suportados para clusters no Azure
Conseguir criar clusters em máquinas virtuais em execução nesses sistemas operativos:

* Windows Server 2012 R2
* Windows Server 2016 
* Linux Ubuntu 16.04  

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Criar autónoma do Service Fabric clusters no local ou com qualquer fornecedor de nuvem
O Service Fabric fornece um pacote de instalação para que possa criar autónomo Service Fabric clusters no local ou em qualquer fornecedor de nuvem.

Para obter mais informações sobre a configuração autónoma do Service Fabric clusters no Windows Server, leia [criação do cluster de Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Clusters autónomos não são atualmente suportadas de Linux. Linux é suportada na caixa de um para desenvolvimento e clusters de múltiplos computadores Linux do Azure.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Quaisquer implementações de nuvem vs. as implementações no local
O processo para criar um cluster do Service Fabric no local é semelhante ao processo de criação de um cluster em nenhuma nuvem da sua preferência com um conjunto de VMs. Os passos iniciais para aprovisionar as VMs estão regidos pelo fornecedor de nuvem ou de ambiente no local que está a utilizar. Depois de ter um conjunto de VMs com conectividade de rede ativada entre eles, em seguida, os passos para configurar o pacote de Service Fabric, edite as definições de cluster e executar a criação do cluster e scripts de gestão são idênticas. Isto garante que os dados de conhecimento e a experiência de operar e gerir clusters de Service Fabric é transferable quando opta por novos ambientes de alojamento de destino.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Vantagens da criação de clusters de Service Fabric autónomo
* Está livre para escolher um fornecedor de nuvem para alojar o seu cluster.
* Aplicações de Service Fabric, escritas uma vez, podem ser executadas em vários ambientes de alojamento com o mínimo para sem alterações.
* Dados de conhecimento da criação de aplicações do Service Fabric acarreta de um ambiente de alojamento para outro.
* Operacional experiência de execução e gestão do Service Fabric clusters ativada através de um ambiente para outro.
* Cliente abrangente alcance é unbounded por restrições de ambiente de alojamento.
* Existe uma camada adicional de proteção contra falhas ampla e fiabilidade porque pode mover os serviços de ativação pós-falha para outro ambiente de implementação se um fornecedor de nuvem ou centro de dados tem um blackout.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Sistemas operativos suportados para clusters autónomos
Conseguir criar clusters em computadores com estes sistemas operativos (Linux ainda não é suportado) ou de VMs:

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Vantagens dos clusters de Service Fabric no Azure através de autónomos, que clusters de Service Fabric criado no local
Service Fabric os clusters em execução no Azure fornece a opção vantagens relativamente à no local, por isso, se não tiver necessidades específicas para onde executa os clusters, em seguida, sugerimos que executar no Azure. No Azure, podemos fornecer integração com outros serviços, tornando operações e gestão do cluster mais fácil e mais fiável e funcionalidades do Azure.

* **Portal do Azure:** portal do Azure torna mais fácil criar e gerir clusters.
* **O Azure Resource Manager:** utilização do Gestor de recursos do Azure permite que o facilitar a gestão de todos os recursos utilizados pelo cluster, como uma unidade e simplifica a faturação e o controlo de custo.
* **Cluster do Service Fabric como um recurso do Azure** cluster A Service Fabric é um recurso do Azure, pelo que pode ser modelo como fazer outros recursos no Azure.
* **Integração com a infraestrutura do Azure** Service Fabric coordena com a infraestrutura subjacente do Azure para o SO, rede e outras atualizações melhorar a disponibilidade e fiabilidade das suas aplicações.  
* **Diagnósticos:** no Azure, fornecemos integração com o diagnóstico do Azure e análise de registos.
* **Dimensionamento automático:** para os clusters no Azure, podemos fornecer a funcionalidade de dimensionamento automático incorporada devido a conjuntos de dimensionamento de Máquina Virtual. No local e noutros ambientes de nuvem, é necessário que criar a sua própria funcionalidade ou escala manualmente utilizando as APIs que expõe os recursos de infraestrutura de serviço para o dimensionamento de clusters de dimensionamento automático.

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster no VMs ou computadores que executam o Windows Server: [criação do cluster de Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster no VMs ou computadores que executam o Linux: [criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)


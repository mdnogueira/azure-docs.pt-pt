---
title: Visualizar o cluster utilizando o Service Fabric Explorer | Microsoft Docs
description: "Explorador do Service Fabric é uma ferramenta baseada na web para inspecionar e gerir aplicações em nuvem e de nós num cluster do Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: ryanwi
ms.openlocfilehash: 965ffc0f8cec26cccbe6e6459731afc234111f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualizar o cluster com o Service Fabric Explorer
Explorador do Service Fabric é uma ferramenta baseada na web para inspecionar e gerir aplicações e nós de um cluster do Service Fabric do Azure. Explorador do Service Fabric está alojado diretamente no cluster, pelo que está sempre disponível, independentemente de onde o cluster está em execução.

## <a name="video-tutorial"></a>Tutorial de vídeo

Para saber como utilizar o Service Fabric Explorer, veja o vídeo seguinte do Microsoft Virtual Academy:

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="connect-to-service-fabric-explorer"></a>Ligar ao Service Fabric Explorer
Se seguiu as instruções para [preparar o ambiente de desenvolvimento](service-fabric-get-started.md), pode iniciar o Explorador do Service Fabric no seu cluster local, navegando até http://localhost:19080/Explorer.

## <a name="understand-the-service-fabric-explorer-layout"></a>Compreender o esquema de Service Fabric Explorer
Pode navegar através do Explorador de recursos de infraestrutura de serviço através da utilização de árvore à esquerda. Na raiz da árvore, o dashboard do cluster fornece uma descrição geral do cluster, incluindo um resumo das aplicações e estado de funcionamento do nó.

![Dashboard de cluster do Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Ver o esquema do cluster
Nós num cluster de Service Fabric são colocados entre uma grelha two-dimensional de domínios de falhas e domínios de atualização. Esta colocação assegura que as aplicações permanecem disponíveis na presença de falhas de hardware e as atualizações de aplicações. Pode ver o modo como o cluster atual é disposto utilizando o mapa de cluster.

![Mapa de cluster do Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Ver aplicações e serviços
O cluster contém dois subárvores: um para aplicações e outra para nós.

Pode utilizar a vista da aplicação para navegar através da hierarquia de lógica de Service Fabric: aplicações, serviços, partições e réplicas.

No exemplo abaixo, a aplicação **MyApp** é composta por dois serviços, **MyStatefulService** e **WebService**. Uma vez que **MyStatefulService** tem o estado monitorizado, inclui uma partição com um site primário e de duas réplicas secundárias. Por outro lado, WebSvcService é sem monitorização de estado e contém uma única instância.

![Vista de aplicação de Service Fabric Explorer][sfx-application-tree]

Cada nível de árvore, o painel principal mostra pertinentes informações sobre o item. Por exemplo, pode ver o estado de funcionamento e a versão para um serviço específico.

![Painel do Service Fabric Explorer essentials][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Visualizar os nós do cluster
A vista do nó mostra o esquema físico do cluster. Para um determinado nó, pode inspecionar as aplicações que têm um código implementado nesse nó. Mais especificamente, pode ver as réplicas estão atualmente em execução não existe.

## <a name="actions"></a>Ações
Service Fabric Explorer proporciona uma forma rápida de invocar ações em nós, aplicações e serviços no seu cluster.

Por exemplo, para eliminar uma instância de aplicação, escolha a aplicação de árvore à esquerda e, em seguida, escolha **ações** > **eliminar aplicação**.

![Eliminar uma aplicação no Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Pode efetuar as mesmas ações clicando no botão de reticências junto a cada elemento.
>
>

A tabela seguinte lista as ações disponíveis para cada entidade:

| **Entidade** | **Ação** | **Descrição** |
| --- | --- | --- |
| Tipo de aplicação |Tipo de não aprovisionamento |Remove o pacote de aplicações do arquivo de imagens do cluster. Requer que todas as aplicações desse tipo a ser removida primeiro. |
| Aplicação |Eliminar Aplicação |Elimine a aplicação, incluindo todos os seus serviços e o respetivo estado (se aplicável). |
| Serviço |Eliminar o serviço |Elimine o serviço e o respetivo estado (se aplicável). |
| Nó |Ativar |Ative o nó. |
| Nó | Desativar (colocar em pausa) | Colocar em pausa o nó no seu estado atual. Serviços continuam a ser executado mas Service Fabric não proativamente mover nada para ou desative-lo, exceto se for necessário para impedir a uma inconsistência de dados ou falha. Esta ação é normalmente utilizada para ativar os serviços de depuração num nó específico para se certificar de que não mudam durante a inspeção. | |
| Nó | Desativar (reiniciar) | Ser movida com segurança todos os serviços de dentro da memória desativado um nó e fechar serviços persistentes. Normalmente utilizado quando a máquina ou processos de anfitrião tem de ser reiniciado. | |
| Nó | Desativar (remover dados) | Feche em segurança todos os serviços em execução no nó após a criação de réplicas de reserva suficientes. Normalmente utilizado quando um nó (ou, pelo menos, o armazenamento) está a ser permanentemente retirada do commission. | |
| Nó | Remover o estado do nó | Remova dados de conhecimento das réplicas de um nó do cluster. Normalmente utilizado quando um nó já falhado é considerado irrecuperável. | |
| Nó | Reiniciar | Simule uma falha de nó reiniciando o nó. Obter mais informações [aqui](/powershell/module/servicefabric/restart-servicefabricnode?view=azureservicefabricps) | |

Uma vez que muitos ações são destrutivas, poderá ser-lhe pedido para confirmar a sua intenção antes de concluída a ação.

> [!TIP]
> Cada ação que pode ser realizada através do Service Fabric Explorer também pode ser efetuada através do PowerShell ou uma API REST, para ativar a automatização.
>
>

Também pode utilizar o Service Fabric Explorer para criar instâncias da aplicação para um tipo de aplicação e versão. Escolha o tipo de aplicação na vista de árvore, em seguida, clique em de **criar instância de aplicação** ligação junto a versão que pretende no painel direito.

![Criar uma instância de aplicação no Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Atualmente não podem ser parametrizadas instâncias de aplicações criadas através do Service Fabric Explorer. Estes são criados utilizando valores de parâmetro predefinidos.
>
>

## <a name="connect-to-a-remote-service-fabric-cluster"></a>Ligar a um cluster de Service Fabric remoto
Se souber o ponto final do cluster e se tem permissões suficientes pode aceder a Service Fabric Explorer a partir de qualquer browser. Isto acontece porque o Service Fabric Explorer é apenas de outro serviço que é executado no cluster.

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>Detetar o ponto final do Service Fabric Explorer para um cluster remoto
Para alcançar o Service Fabric Explorer para um determinado cluster, aponte o browser para:

http://&lt;endpoint seu cluster&gt;: 19080/Explorer

Para os clusters do Azure, o URL completo também está disponível no painel de essentials de cluster do portal do Azure.

### <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro
Pode controlar o acesso de cliente ao cluster do Service Fabric com certificados ou utilizar o Azure Active Directory (AAD).

Se tentar ligar ao Service Fabric Explorer num cluster seguro, em seguida, consoante a configuração do cluster, irá ser necessário para apresentar um certificado de cliente ou inicie sessão com o AAD.

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral de teste](service-fabric-testability-overview.md)
* [Gerir as aplicações de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Implementação de aplicação de Service Fabric com o PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png

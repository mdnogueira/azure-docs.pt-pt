---
title: Configurar um cluster do Azure Service Fabric | Microsoft Docs
description: "Guia de introdução - criar um cluster do Service Fabric do Windows ou Linux no Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 813217567c7e1fa5fc9ac11492933ad0c34553d1
ms.lasthandoff: 04/18/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Crie o primeiro cluster Service Fabric no Azure
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Este guia de introdução ajuda-o a criar um cluster de cinco nós, em execução no Windows ou Linux, através do [portal do Azure](http://portal.azure.com) em apenas alguns minutos.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="create-the-cluster"></a>Criar o cluster

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** a partir do painel **Novo** e, em seguida, selecione **Cluster do Service Fabric** a partir do painel **Computação**.
3. Preencha o formulário **Informações básicas** do Service Fabric. Para o **Sistema operativo**, selecione a versão do Windows ou do Linux que pretende que os nós do cluster executem. O nome de utilizador e palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. Para o **Grupo de recursos** crie um novo. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são criados e geridos coletivamente. Quando terminar, clique em **OK**.

    ![Saída do programa de configuração do cluster][cluster-setup-basics]

4. Preencha o formulário **Configuração de cluster**.  Para **Contagem do tipo de nó**, introduza "1" e o [Escalão de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) para "Bronze."

5. Selecione **Configurar cada tipo de nó** e preencha o formulário **Configuração do tipo de nó**. Os tipos de nós definem o tamanho da VM, o número de VMs, os pontos finais personalizados e outras definições para as VMs desse tipo. Cada tipo de nó definido é configurado como um conjunto de dimensionamento de máquinas virtuais separado, que é utilizado para implementar e gerir as máquinas virtuais como um conjunto. Cada tipo de nó pode ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente.  O primeiro, ou primário, tipo de nó é onde os serviços do sistema do Service Fabric estão alojados e devem ter cinco ou mais VMs.

    Para qualquer implementação de produção, o [planeamento da capacidade](service-fabric-cluster-capacity.md) é um passo importante.  Para este guia de introdução, no entanto, não está a executar aplicações, pelo que deve selecionar um tamanho da VM *DS1_v2 Standard* .  Selecione "Prata" para o [escalão de fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) e uma capacidade do conjunto de dimensionamento de máquinas virtuais inicial de cinco.  

    Os pontos finais personalizados abrem portas no balanceador de carga do Azure para que se possa ligar a aplicações em execução no cluster.  Introduza "80, 8172" para abrir as portas 80 e 8172.

    Não assinale a caixa **Configurar definições avançadas**, que é utilizada para personalizar os pontos finais de gestão TCP/HTTP, os intervalos de portas de aplicação, as [restrições de posicionamento](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) e as [propriedades de capacidade](service-fabric-cluster-resource-manager-metrics.md).    

    Selecione **OK**.

6. No formulário **Configuração de cluster**, defina os **Diagnósticos** como **Ativados**.  Para este guia de introdução, não tem de introduzir quaisquer propriedades de [definição de recursos de infraestrutura](service-fabric-cluster-fabric-settings.md).  Na **versão dos Recursos de infraestrutura**, selecione o modo de atualização **Automático**, para que a Microsoft atualize automaticamente a versão do código dos recursos de infraestrutura em execução no cluster.  Defina o modo para **Manual**, se pretender [escolher uma versão suportada](service-fabric-cluster-upgrade.md) para se atualizar. 

    ![Configuração do tipo de nó][node-type-config]

    Selecione **OK**.

7. Preencha o formulário **Segurança**.  Para este guia de introdução, selecione **Inseguro**.  É altamente recomendado que crie um cluster seguro para cargas de trabalho de produção, uma vez que qualquer pessoa pode ligar a um cluster inseguro anonimamente e realizar operações de gestão.  

    Os certificados são utilizados no Service Fabric para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações sobre como os certificados são utilizados no Service Fabric, consulte [Service Fabric cluster security scenarios (Cenários de segurança do cluster do Service Fabric)](service-fabric-cluster-security.md).  Para ativar a autenticação de utilizador com o Azure Active Directory ou configurar certificados para a segurança da aplicação, [crie um cluster a partir de um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Selecione **OK**.

8. Reveja o resumo.  Se gostaria de transferir um modelo do Resource Manager criado a partir das definições que introduziu, selecione **Transferir modelo e parâmetros**.  Selecione **Criar** para criar o cluster.

    Pode ver o progresso da criação nas notificações. (Clique no ícone de "Sino", junto a barra de estado no canto superior direito do ecrã.) Se clicou em **Afixar ao Startboard** ao criar o cluster, verá **Implementar o Cluster do Service Fabric** afixado ao painel de **Início**.

## <a name="view-cluster-status"></a>Ver o estado do cluster
Quando o cluster for criado, pode inspecionar o seu cluster no painel **Descrição geral** no portal. Agora pode ver os detalhes do seu cluster no dashboard, incluindo o ponto final público do cluster e uma ligação para o Service Fabric Explorer.

![Estado do cluster][cluster-status]

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualize o cluster com o explorador do Service Fabric
O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma boa ferramenta para visualizar o seu cluster e gerir aplicações.  O Service Fabric Explorer é um serviço executado no cluster.  Aceda ao mesmo com um browser, ao clicar na ligação **Service Fabric Explorer** da página de **Overview (Descrição geral)** do cluster no portal.  Também pode introduzir o endereço diretamente no browser: [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

O dashboard do cluster fornece uma descrição geral do cluster, incluindo um resumo de aplicações e do estado de funcionamento do nó. A vista do nó mostra o esquema físico do cluster. Para um determinado nó, pode inspecionar as aplicações que têm um código implementado nesse nó.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="connect-to-the-cluster-using-powershell"></a>Ligar ao cluster com o PowerShell
Certifique-se de que o cluster está em execução ao ligar ao PowerShell.  O módulo do ServiceFabric PowerShell é instalado com o [SDK do Service Fabric](service-fabric-get-started.md).  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/ServiceFabric/Connect-ServiceFabricCluster) estabelece uma ligação ao cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Consulte [Connect to a secure cluster (Ligar a um cluster seguro)](service-fabric-connect-to-secure-cluster.md) para outros exemplos de ligação a um cluster. Depois de ligar ao cluster, utilize o cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) para apresentar uma lista de nós no cluster e informações de estado para cada nó. O **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

## <a name="remove-the-cluster"></a>Remover o cluster
Um cluster do Service Fabric é constituído por outros recursos do Azure, além do próprio recurso do cluster. Por isso, para eliminar completamente um cluster do Service Fabric também tem de eliminar todos os recursos de que é constituído. A forma mais simples de eliminar o cluster e todos os respetivos recursos é eliminando o grupo de recursos. Para outras formas de eliminar um cluster ou para eliminar alguns (embora não todos) recursos num grupo de recursos, consulte [Delete a cluster (Eliminar um cluster)](service-fabric-cluster-delete.md)

Eliminar um grupo de recursos no portal do Azure:
1. Navegue para o cluster do Service Fabric que pretende eliminar.
2. Clique no nome **Grupo de Recursos** na página de conceitos básicos do cluster.
3. Na página **Conceitos Básicos do Grupo de Recursos**, clique em **Eliminar** e siga as instruções nessa página para concluir a eliminação do grupo de recursos.
    ![Eliminar o grupo de recursos][cluster-delete]

## <a name="next-steps"></a>Passos seguintes
Agora que configurou um cluster autónomo de desenvolvimento, experimente o seguinte:
* [Criar um cluster seguro no portal](service-fabric-cluster-creation-via-portal.md)
* [Criar um cluster a partir de um modelo](service-fabric-cluster-creation-via-arm.md) 
* [Implementar aplicações com o Powershell](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png

---
title: 'Criar e modificar um circuito ExpressRoute: portal do Azure | Microsoft Docs'
description: Este artigo descreve como criar, aprovisionar, certifique-se, atualizar, eliminar e retirar o aprovisionamento um circuito ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: a21fdfbc4396f2b7aff50fae4ca796d8ea6a733b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Criar e modificar um circuito do ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo descreve como criar um circuito ExpressRoute do Azure utilizando o portal do Azure e o modelo de implementação Azure Resource Manager. Os seguintes passos também mostram-lhe como verificar o estado do circuito, a atualização, ou eliminar e retirar o aprovisionamento do mesmo.


## <a name="before-you-begin"></a>Antes de começar
* Reveja o [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Certifique-se de que tem acesso para o [portal do Azure](https://portal.azure.com).
* Certifique-se de que tem permissões para criar novos recursos de rede. Contacte o administrador de conta se não tiver as permissões corretas.
* Pode [ver um vídeo](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de iniciar para compreender melhor os passos.

## <a name="create"></a>Criar e aprovisionar um circuito do ExpressRoute
### <a name="1-sign-in-to-the-azure-portal"></a>1. Iniciar sessão no portal do Azure
Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Criar um novo circuito do ExpressRoute
> [!IMPORTANT]
> O circuito ExpressRoute é faturado a partir do momento em que é emitida uma chave de serviço. Certifique-se de que efetuar esta operação quando o fornecedor de conectividade está pronto para aprovisionar o circuito.
> 
> 

1. Pode criar um circuito do ExpressRoute, selecionando a opção para criar um novo recurso. Clique em **novo** > **redes** > **ExpressRoute**, conforme mostrado na imagem seguinte:

  ![Criar um circuito do ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Depois de clicar em **ExpressRoute**, verá o **circuito ExpressRoute criar** página. Quando estiver a preencher os valores nesta página, certifique-se de que especifica a camada SKU correta (Standard ou Premium) e os dados medição modelo de faturação (ilimitada ou Metered).

  ![Configurar a camada SKU e medição de dados](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **Camada** determina se a um padrão de ExpressRoute ou de um suplemento ExpressRoute premium está ativado. Pode especificar **padrão** para obter o SKU standard ou **Premium** para o suplemento premium.
  * **Medição de dados** determina o tipo de faturação. Pode especificar **Metered** para um plano de dados limitados e **ilimitada** para um plano de dados ilimitados. Tenha em atenção que pode alterar o tipo de faturação **Metered** para **ilimitada**, mas não é possível alterar o tipo de **ilimitada** para **Metered**.
  * **Localização de peering** é a localização física onde são peering com a Microsoft.

    > [!IMPORTANT]
    > Indica a localização de Peering o [localização física](expressroute-locations.md) onde são peering com a Microsoft. Este é **não** ligadas a propriedade de "Localização", que se refere a geografia onde está localizado o fornecedor de recursos de rede do Azure. Enquanto não estão relacionadas, é uma boa prática para escolher um fornecedor de recursos de rede geograficamente próximo a localização de Peering do circuito.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. Ver propriedades de circuitos e
**Ver todos os circuitos**

Pode ver todos os circuitos que criou, selecionando **todos os recursos** no menu do lado esquerdo.

![Circuitos de vista](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Ver as propriedades**

Pode ver as propriedades do circuito, selecionando-o. No **descrição geral** página para o seu circuito, a chave do serviço é apresentada no campo chave do serviço. Tem de copiar a chave do serviço para o seu circuito e transmita-a para baixo para o fornecedor de serviços para concluir o processo de aprovisionamento. A chave do serviço de circuito é específica para o seu circuito.

![Ver propriedades](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Enviar a chave do serviço para o seu fornecedor de conectividade para aprovisionamento
Nesta página, **Estado fornecedor** fornece informações sobre o estado atual do aprovisionamento do lado do fornecedor de serviços. **Estado de circuitos** fornece o estado do lado do Microsoft. Para obter mais informações sobre os Estados de aprovisionamento do circuito, consulte o [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) artigo.

Quando cria um novo circuito do ExpressRoute, o circuito é no seguinte estado:

Estado do fornecedor: não aprovisionado<BR>
Estado de circuitos: ativado

![Iniciar o processo de aprovisionamento](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

O circuito muda para o seguinte estado quando o fornecedor de conectividade está no processo de ativação-lo por si:

Estado do fornecedor: aprovisionamento<BR>
Estado de circuitos: ativado

Para que possa ser capazes de utilizar um circuito do ExpressRoute, tem de ser no seguinte estado:

Estado do fornecedor: aprovisionado<BR>
Estado de circuitos: ativado

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Verificar periodicamente o estado e o estado da chave do circuito
Pode ver as propriedades do circuito que lhe está interessados, selecionando-o. Verifique o **Estado fornecedor** e certifique-se de que foi movida para **aprovisionado** antes de continuar.

![Estado do circuito e fornecedor](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Criar a configuração de encaminhamento
Para obter instruções passo a passo, consulte o [configuração de encaminhamento de circuito de ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) artigo para criar e modificar peerings do circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de 2 conectividade de camada. Se estiver a utilizar um fornecedor de serviço que oferece gerido layer 3 serviços (normalmente, uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Ligar uma rede virtual a um circuito ExpressRoute
Em seguida, ligar uma rede virtual para o circuito do ExpressRoute. Utilize o [ligar redes virtuais para circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) artigo quando trabalha com o modelo de implementação Resource Manager.

## <a name="status"></a>Obter o estado de um circuito ExpressRoute
Pode ver o estado de um circuito, selecionando-o e visualize a página de descrição geral. 

## <a name="modify"></a>Modificar um circuito do ExpressRoute
Pode modificar algumas propriedades de um circuito ExpressRoute sem afetar a conectividade. Pode modificar o modelo de faturação de largura de banda, SKU e permitir operações clássicas no **configuração** página. Para obter informações sobre limites e limitações, consulte o [FAQ do ExpressRoute](expressroute-faqs.md). 

Pode efetuar as seguintes tarefas sem período de indisponibilidade:

* Ativar ou desativar um suplemento ExpressRoute Premium para o seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito do ExpressRoute, desde há capacidade disponível na porta. Desatualização de largura de banda de um circuito não é suportada. 
* Alterar o plano de medição de *limitados dados* para *dados ilimitados*. Não é suportada a alteração do plano de medição de dados ilimitados dados limitados.
* Pode ativar e desativar *permitir operações clássicas*.

> [!IMPORTANT]
> Poderá ter de recriar o circuito do ExpressRoute, se existir capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver nenhuma capacidade adicional nessa localização.
>
> Não é possível reduzir a largura de banda de um circuito de ExpressRoute sem interrupção. Desatualização de largura de banda requer a retirar o aprovisionamento do circuito ExpressRoute e, em seguida, reaprovisionar um circuito de ExpressRoute novo.
> 
> Desativar a operação de suplemento Premium pode falhar se estiver a utilizar recursos que são maiores que o que é permitido para o circuito standard.
> 
> 

Para modificar um circuito do ExpressRoute, clique em **configuração**.

![Modificar o circuito](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>Desaprovisionamento e eliminar um circuito do ExpressRoute
Pode eliminar o circuito do ExpressRoute, selecionando o **eliminar** ícone. Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito ExpressRoute. Se esta operação falhar, verifique se as redes virtuais ligadas ao circuito.
* Se o fornecedor de serviço de circuito de ExpressRoute estado de aprovisionamento é **aprovisionamento** ou **aprovisionado** tem de trabalhar com o fornecedor de serviços para retirar o aprovisionamento do circuito no seu lado. Vamos continuar a reserva de recursos e faturar-lhe até que o fornecedor do serviço concluída desaprovisionamento o circuito e notifica-nos.
* Se o fornecedor de serviços tem desaprovisionada o circuito (o fornecedor do serviço de estado de aprovisionamento está definido como **não aprovisionado**), pode eliminar o circuito. Este deixa de faturação para o circuito.

## <a name="next-steps"></a>Passos seguintes
Depois de criar o seu circuito, continue com os seguintes passos:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligar a rede virtual para o circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
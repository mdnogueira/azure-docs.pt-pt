---
title: 'Como configurar o encaminhamento circuito (peering) para o ExpressRoute: o Gestor de recursos: Azure | Microsoft Docs'
description: "Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2017
ms.author: cherylmc
ms.openlocfilehash: be25e9ffab4fee79b8d9cc6c88c6ffb3e852af0d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Criar e modificar o peering de um circuito ExpressRoute

Este artigo ajuda-o a criar e gerir a configuração de encaminhamento para um circuito de ExpressRoute no modelo de implementação Resource Manager no portal do Azure. Também pode verificar o estado, update ou delete e retirar o aprovisionamento do peerings para um circuito ExpressRoute. Se pretender utilizar um método diferente para trabalhar com o seu circuito, selecione um artigo na lista seguinte:


## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute tem de ser num Estado aprovisionado e ativado para que possa ser capaz de executar os cmdlets nas secções seguintes.
* Se planeia utilizar um hash MD5/chave partilhado, lembre-se de que utilize esta opção em ambos os lados do túnel e limitar o número de carateres para um máximo de 25.

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um fornecedor de serviço que oferece gerido Layer 3 serviços (normalmente, uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si. 

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Consulte o seu fornecedor de serviço antes de configurar os peerings BGP.
> 
> 

Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [domínios de encaminhamento do ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Peering da Microsoft

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering da Microsoft para um circuito ExpressRoute.

> [!IMPORTANT]
> Peering da Microsoft dos circuitos ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através da Microsoft, peering, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos ExpressRoute que estão configurados em ou após 1 de Agosto de 2017 não terão qualquer prefixos anunciados até um filtro de rota está ligado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para peering da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

[!INCLUDE [Premium](../../includes/expressroute-mspeering-premium-include.md)]

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de prosseguir. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode pedir ao seu fornecedor de conectividade para ativar o Microsoft peering por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gerir encaminhamento por si, depois de criar o seu circuito, continue a configuração utilizando os passos seguintes.

  ![lista o peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering da Microsoft para o circuito. Confirme que tem as seguintes informações antes de continuar.

  * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
  * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
  * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
  * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
  * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR.
  * **Opcional -** cliente ASN: Se estiver a anunciar prefixos que não estão registados para o número AS de peering, pode especificar o número AS no qual estão registados.
  * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
  * **Opcional -** um hash MD5 se optar por utilizar um.
3. Pode selecionar o peering que pretende configurar, conforme mostrado no exemplo seguinte. Selecione a linha de peering da Microsoft.

  ![Selecione a linha de peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configure o peering da Microsoft. A imagem seguinte mostra um exemplo de configuração:

  ![configurar o peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Guarde a configuração depois de especificar todos os parâmetros.

  Se o seu circuito chegar a um 'Validation necessário' Estado (conforme mostrado na imagem), tem de abrir um pedido de suporte para demonstrar prova de titularidade dos prefixos à nossa equipa de suporte.

  ![Guardar a configuração do peering da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

  Pode abrir um pedido de suporte diretamente no portal, conforme mostrado no exemplo seguinte:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Depois da configuração foi aceite com êxito, verá algo semelhante a imagem seguinte:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Para ver os detalhes do peering Microsoft

Pode ver as propriedades do peering público do Azure ao selecionar o peering.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Ao atualizar a configuração do peering da Microsoft

Pode selecionar a linha para peering e modificar as propriedades do peering.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>Para eliminar o peering da Microsoft

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado na imagem seguinte:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de continuar. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode pedir ao seu fornecedor de conectividade para ativar o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gerir encaminhamento por si, depois de criar o seu circuito, continue a configuração utilizando os passos seguintes.

  ![lista](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

  * Uma sub-rede /30 para a ligação primária. A sub-rede não tem de fazer parte de qualquer espaço de endereços reservado para redes virtuais.
  * Uma sub-rede /30 para a ligação secundária. A sub-rede não tem de fazer parte de qualquer espaço de endereços reservado para redes virtuais.
  * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
  * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Assegure que não está a utilizar 65515.
  * **Opcional -** um hash MD5 se optar por utilizar um.
3. Selecione a linha de peering privado do Azure, conforme mostrado no exemplo seguinte:

  ![Privada](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configure o peering privado. A imagem seguinte mostra um exemplo de configuração:

  ![Configure o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Guarde a configuração depois de especificar todos os parâmetros. Depois da configuração foi aceite com êxito, verá algo semelhante ao seguinte exemplo:

  ![guardar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Para ver detalhes do peering privados do Azure

Pode ver as propriedades do peering privado do Azure ao selecionar o peering.

![ver o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Ao atualizar a configuração do peering privado do Azure

Pode selecionar a linha para peering e modificar as propriedades do peering.

![Atualizar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Para eliminar o peering privado do Azure

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado na imagem seguinte:

![eliminar o peering privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Peering público do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering pública do Azure para um circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure

1. Configure o circuito ExpressRoute. Assegure que o circuito é totalmente aprovisionado pelo fornecedor de conectividade antes de prosseguir. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode pedir ao seu fornecedor de conectividade para ativar o peering público do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gerir encaminhamento por si, depois de criar o seu circuito, continue a configuração utilizando os passos seguintes.

  ![lista o peering público](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o peering público do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

  * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
  * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
  * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
  * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
  * **Opcional -** um hash MD5 se optar por utilizar um.
3. Selecione a linha de peering pública do Azure, conforme mostrado na imagem seguinte:

  ![Selecione a linha de peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configure o peering público. A imagem seguinte mostra um exemplo de configuração:

  ![Configure o peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Guarde a configuração depois de especificar todos os parâmetros. Depois da configuração foi aceite com êxito, verá algo semelhante ao seguinte exemplo:

  ![Guardar a configuração do peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Para ver detalhes do peering públicos do Azure

Pode ver as propriedades do peering público do Azure ao selecionar o peering.

![Ver propriedades do peering públicas](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Ao atualizar a configuração do peering público do Azure

Pode selecionar a linha para peering e modificar as propriedades do peering.

![Selecione a linha de peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Para eliminar o peering público do Azure

Pode remover a configuração do peering selecionando o ícone Eliminar, conforme mostrado no exemplo seguinte:

![eliminar o peering público](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>Passos seguintes

Passo seguinte, [ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
---
title: "Criar um endereço IP público zoned com o Portal do Azure | Microsoft Docs"
description: "Crie um endereço IP público numa zona de disponibilidade com o portal do Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 2fcbed2f83d66a0b4336cd1c464bb02eff3ef229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Criar um endereço IP público numa zona de disponibilidade com o portal do Azure

Pode implementar um endereço IP público numa zona de disponibilidade do Azure (pré-visualização). Uma zona de disponibilidade é uma zona separada fisicamente numa região do Azure. Aprenda a:

> * Criar um endereço IP público numa zona de disponibilidade
> * Identificar recursos relacionados criados na zona de disponibilidade

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Zonas de disponibilidade estão em pré-visualização e estão prontas para o seu desenvolvimento e teste cenários. O suporte está disponível para famílias de tamanho VM, selecionados de recursos do Azure e regiões. Para obter mais informações sobre como começar a utilizar e os recursos do Azure, regiões e famílias de tamanho VM pode tentar zonas de disponibilidade com, consulte [zonas de descrição geral da disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com. 

## <a name="create-a-zonal-public-ip-address"></a>Criar um endereço IP público zonal

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2. Selecione **redes**e, em seguida, selecione **endereço IP público**.
3. Introduza ou selecione os valores para as seguintes definições, selecione a sua subscrição, aceite as predefinições para as restantes definições e clique em **criar**:

    |Definição|Valor|
    |---|---|
    |SKU| **Básico**: atribuído com o método de alocação estático ou dinâmico. Podem ser atribuídos a qualquer recurso do Azure que pode ser atribuído um endereço IP público, como as interfaces de rede, Gateways de VPN, Gateways de aplicação e Balanceadores de carga para a Internet. Pode atribuir o endereço IP público para uma zona específica no **zona disponibilidade** definição. Não são com redundância de zona. Para saber mais sobre as zonas de disponibilidade, veja [Descrição geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). **Standard**: atribuído com o método de alocação estático só. Podem ser atribuídos a interfaces de rede ou padrão balanceadores de carga para a Internet. Se atribuir o endereço IP público para um balanceador de carga do padrão para a Internet, tem de selecionar padrão. Para obter mais informações sobre os SKUs de balanceador de carga do Azure, veja [SKU standard de balanceador de carga do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). São com redundância de zona, por predefinição. Podem ser criados zonais e garantidos numa zona de disponibilidade específica. O SKU standard encontra-se em versão de pré-visualização. Antes de criar um endereço IP público do Standard SKU, primeiro tem de registar para a pré-visualização. Para se registar na pré-visualização, veja [Registar-se na pré-visualização do SKU standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up). O SKU Standard só podem ser criado numa localização suportada.  Para obter uma lista de localizações (regiões) suportadas, veja [Disponibilidade de região](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability) e monitorize a página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network) para obter suporte de região adicional.|   
    |Nome|O nome tem de ser exclusivo no grupo de recursos que selecionar.|
    |Grupo de recursos|Clique em criar novo e introduza myResourceGroup|
    |Localização|Europa Ocidental|
    |Zona de disponibilidade|Se tiver selecionado o **padrão** SKU, pode selecionar *zona redundante* se pretender que o endereço IP para ser resiliente através de zonas. Se selecionar a **básico** SKU, o endereço IP não é resiliente através de zonas. Independentemente do SKU que escolher, pode atribuir o endereço para uma zona específica, se escolher. |

    As definições são apresentadas no portal, conforme mostrado na imagem seguinte:

    ![Criar zonal endereço IP público](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Saiba mais sobre [endereços IP públicos](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
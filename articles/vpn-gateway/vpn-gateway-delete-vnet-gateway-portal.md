---
title: 'Eliminar um gateway de rede virtual: portal do Azure: Resource Manager | Microsoft Docs'
description: "Elimine um gateway de rede virtual no portal do Azure no modelo de implementação Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: cherylmc
ms.openlocfilehash: 1d289c09465cb8d5e4bfa569441dffcbf562b3bf
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Eliminar um gateway de rede virtual através do portal

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Existem duas abordagens diferentes que pode tomar quando pretender eliminar um gateway de rede virtual para uma configuração de gateway VPN.

- Se pretender eliminar tudo e comece de novo, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Quando elimina um grupo de recursos, elimina todos os recursos dentro do grupo. Este é o método só é recomendada se não pretender manter qualquer um dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas alguns recursos através desta abordagem.

- Se pretender manter alguns dos recursos no seu grupo de recursos, ao eliminar um gateway de rede virtual fica ligeiramente mais complicado. Antes de poder eliminar o gateway de rede virtual, primeiro tem de eliminar todos os recursos dependentes no gateway. Os passos a que seguir dependem do tipo de ligações que criou e os recursos dependentes para cada ligação.

## <a name="delete-a-vpn-gateway"></a>Eliminar um gateway de VPN

Para eliminar um gateway de rede virtual, deve primeiro eliminar cada recurso que diz respeito ao gateway de rede virtual. Recursos tem de ser eliminados por uma ordem determinada devido a dependências.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Neste momento, o gateway de rede virtual é eliminado. Os passos seguintes ajudam a eliminar todos os recursos que já não estão a ser utilizados.

### <a name="to-delete-the-local-network-gateway"></a>Para eliminar o gateway de rede local

1. No **todos os recursos**, localize os gateways de rede local que estavam associados a cada ligação.
2. No **descrição geral** painel para o gateway de rede local, clique em **eliminar**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Para eliminar o recurso de endereço IP público do gateway

1. No **todos os recursos**, localize o recurso de endereço IP público que estava associado ao gateway. Se o gateway de rede virtual foi ativo-ativo, verá dois endereços IP públicos. 
2. No **descrição geral** página para o endereço IP público, clique em **eliminar**, em seguida, **Sim** para confirmar.

### <a name="to-delete-the-gateway-subnet"></a>Para eliminar a sub-rede de gateway

1. No **todos os recursos**, localize a rede virtual. 
2. No **sub-redes** painel, clique em de **GatewaySubnet**, em seguida, clique em **eliminar**. 
3. Clique em **Sim** para confirmar que pretende eliminar a sub-rede do gateway.

## <a name="deleterg"></a>Eliminar um gateway de VPN, ao eliminar o grupo de recursos

Se não estiver preocupado sobre manter qualquer um dos seus recursos no grupo de recursos e pretender começar de novo, pode eliminar um grupo de recursos completo. Esta é uma forma rápida de remover tudo. Os seguintes passos aplicam-se apenas ao modelo de implementação Resource Manager.

1. No **todos os recursos**, localize o grupo de recursos e clique para abrir o painel.
2. Clique em **Eliminar**. No painel de eliminação, ver os recursos afetados. Certifique-se de que pretende eliminar todos estes recursos. Se não estiver, utilize os passos em [eliminar um gateway de VPN](#deletegw) na parte superior deste artigo.
3. Para continuar, escreva o nome do grupo de recursos que pretende eliminar, em seguida, clique em **eliminar**.
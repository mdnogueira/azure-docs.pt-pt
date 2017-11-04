---
title: "Não é possível eliminar uma rede virtual no Azure | Microsoft Docs"
description: "Saiba como resolver o problema no qual não é possível eliminar uma rede virtual no Azure."
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: e0e58e443897adad2b6029db4e64fa220cebb6b8
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Resolução de problemas: Falha ao eliminar uma rede virtual no Azure

Poderá receber erros ao tentar eliminar uma rede virtual no Microsoft Azure. Este artigo fornece os passos de resolução de problemas para o ajudar a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se um gateway de rede virtual está em execução na rede virtual](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Verifique se um gateway de aplicação está em execução na rede virtual](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Verifique se o serviço de domínio do Active Directory do Azure está ativado na rede virtual](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Certifique-se a rede virtual está ligada a outro recurso](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Verifique se uma máquina virtual ainda está em execução na rede virtual](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Verifique se a rede virtual está encravada no migração](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Verifique se um gateway de rede virtual está em execução na rede virtual

Para remover a rede virtual, tem de remover primeiro o gateway de rede virtual.

Para redes virtuais clássicas, vá para o **descrição geral** página da rede virtual clássica no portal do Azure. No **ligações VPN** secção, se o gateway está em execução na rede virtual, verá o endereço IP do gateway. 

![Verifique se o gateway está em execução](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Para redes virtuais, vá para o **descrição geral** página da rede virtual. Verifique **dispositivos ligados** para o gateway de rede virtual.

![Verifique o dispositivo ligado](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Antes de poder remover o gateway, primeiro remova qualquer **ligação** objetos no gateway. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Verifique se um gateway de aplicação está em execução na rede virtual

Vá para o **descrição geral** página da rede virtual. Verifique o **dispositivos ligados** para o gateway de aplicação.

![Verifique o dispositivo ligado](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Se existir um gateway de aplicação, tem de removê-lo antes de poder eliminar a rede virtual.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Verifique se o serviço de domínio do Active Directory do Azure está ativado na rede virtual

Se o serviço de domínio do Active Directory é ativado e ligado à rede virtual, não é possível eliminar esta rede virtual. 

![Verifique o dispositivo ligado](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Para desativar o serviço, siga estes passos:

1. Aceda ao [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel esquerdo, selecione **do Active Directory**.
3. Selecione o diretório do Azure Active Directory (Azure AD) com o serviço de domínio do Active Directory ativada.
4. Selecione o separador **Configurar**.
5. Em **dos serviços de domínio**, altere o **ativar os serviços de domínio para este diretório** opção para **não**.  

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Verifique se a rede virtual está ligada a outros recursos

Verifique a existência de ligações de circuito, ligações e peerings de rede virtual. Qualquer uma destas pode provocar a eliminação de rede virtual falhe. 

A ordem de eliminação recomendada é o seguinte:

1. Ligações de gateway
2. Gateways
3. IPs
4. Peerings de rede virtual
5. Ambiente de serviço de aplicações (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Verifique se uma máquina virtual ainda está em execução na rede virtual

Certifique-se de que nenhuma máquina virtual na rede virtual.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Verifique se a rede virtual está encravada no migração

Se a rede virtual está encravada no estado de migração, não pode ser eliminada. Execute o seguinte comando para abortar da migração e, em seguida, elimine a rede virtual.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Passos seguintes

- [Rede Virtual do Azure](virtual-networks-overview.md)
- [Perguntas mais frequentes (FAQ) da Rede Virtual do Azure](virtual-networks-faq.md)

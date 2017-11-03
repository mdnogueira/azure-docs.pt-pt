---
title: "Certifique-se de tráfego com o IP de observador de rede de Azure fluxo verifique - portal do Azure | Microsoft Docs"
description: "Este artigo descreve como verificar se o tráfego de ou para uma máquina virtual é permitido ou negado"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8333ccfd1e4cc917c8af4b3006292e43b77ecc7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Verifique se o tráfego é permitido ou negado de uma VM com o IP fluxo verificar ou para um componente do observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST do Azure](network-watcher-check-ip-flow-verify-rest.md)


Verificar o fluxo IP é uma funcionalidade do observador de rede que permite-lhe verificar se o tráfego é permitido para ou a partir de uma máquina virtual. A validação pode ser executada para o tráfego de entrada ou de saída. Este cenário é útil para obter um estado atual do se uma máquina virtual pode comunicar com um recurso externo ou outro recurso. Verificar o fluxo IP podem ser utilizadas para verificar se as regras do grupo de segurança de rede (NSG) estão configuradas corretamente e resolver problemas relacionados com fluxos que estão a ser bloqueados por regras NSG. Outra razão para utilizar o IP fluxo verificar é certificar-se de que o tráfego que pretende que sejam bloqueadas está a ser bloqueado corretamente por NSG.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede ou ter uma instância existente do observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

Este cenário utiliza fluxo Certifique-se de IP para verificar se uma máquina virtual pode comunicar com outro computador através da porta 443. Se o tráfego for recusado, devolve a regra de segurança que está a negar esse tráfego. Para mais informações sobre o fluxo Certifique-se de IP, visite [IP fluxo Certifique-se de descrição geral](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>Certifique-se de execução de fluxo de IP

Navegue para o observador de rede e clique em **Certifique-se de fluxo IP**. Selecione a máquina virtual e a interface de rede que pretende verificar o tráfego do. Introduza qualquer informação adicional sobre filtragem e clique em **verifique**.

Assim que clicar em **verifique**, o fluxo com base nos critérios que forneceu está marcado. O resultado é **acesso permitido** ou **acesso negado**. Se for negado o acesso, a regra de segurança e de grupo de segurança de rede (NSG) que bloqueia o tráfego é fornecida. Se a recusa do tráfego de comportamento está previsto, a regra foi efetuada com êxito.

> [!NOTE]
> Fluxo IP verificar requer que o recurso VM está alocado.

Como pode ver na imagem seguinte, foi permitido o tráfego de saída do HTTPS.

![Certifique-se de fluxo de IP descrição geral][1]

Como mostrado na imagem seguinte, o tráfego é alterado para entrada e a porta de entrada foi alterado para 123. O tráfego é agora negado, a mensagem "Acesso negado" for fornecido juntamente com a regra de segurança e de grupo de segurança de rede que negar o tráfego.

![resultados de fluxo de IP][2]

## <a name="next-steps"></a>Passos seguintes

Se o tráfego está a ser bloqueado e não deve ser, consulte [gerir grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para identificar as regras de segurança e de grupo de segurança de rede que estão definidas.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














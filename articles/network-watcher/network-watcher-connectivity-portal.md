---
title: Verifique a conectividade com o observador de rede do Azure - portal do Azure | Microsoft Docs
description: "Esta página explica como utilizar a verificação de conectividade com o observador de rede através do portal do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 1f19da71731039e1a39c4440f925b1369886a993
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>Verifique a conectividade com o observador de rede do Azure no portal do Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [API REST do Azure](network-watcher-connectivity-rest.md)

Saiba como utilizar conectividade para verificar se é possível estabelecer uma ligação de TCP direta de uma máquina virtual para um ponto final especificado.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem os seguintes recursos:

* Uma instância do observador de rede na região que pretende verificar a conectividade.

* Máquinas virtuais para verificar a conectividade com.

> [!IMPORTANT]
> Verificação da conectividade requer uma extensão da máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão numa Windows VM visite [extensão da máquina virtual de agente de observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para, visite VM com Linux [extensão da máquina virtual de agente de observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="check-connectivity-to-a-virtual-machine"></a>Verifique a conectividade a uma máquina virtual

Neste exemplo verifica a conectividade a uma máquina virtual de destino através da porta 80.

Navegue para o observador de rede e clique em **verificação da conectividade (pré-visualização)**. Selecione a máquina virtual para verificar a conectividade do. No **destino** secção escolha **Selecione uma máquina virtual** e escolha o correta máquina virtual e a porta para testar.

Assim que clicar em **verifique**, a conectividade entre as máquinas virtuais na porta especificada está marcada. No exemplo, o destino da VM está inacessível, serão mostrados uma listagem de saltos.

![Resultados da verificação de conectividade para uma máquina virtual][1]

## <a name="check-remote-endpoint-connectivity"></a>Verifique a conectividade de ponto final remoto

Para verificar a conectividade e latência para um ponto final remoto, escolha o **especificar manualmente** botão de opção no **destino** secção, o url e a porta de entrada e clique em **verifique**.  Isto é utilizado para pontos finais remotos como pontos finais de Web sites e de armazenamento.

![Resultados da verificação de conectividade para um web site][2]

## <a name="next-steps"></a>Passos seguintes

Saiba como automatizar capturas de pacotes com alertas de Máquina Virtual visualizando [criar uma captura de pacotes accionadas alerta](network-watcher-alert-triggered-packet-capture.md)

Determinar se determinados o tráfego é permitido dentro ou fora da sua VM, visitando [Certifique-se de fluxo de verificação de IP](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png

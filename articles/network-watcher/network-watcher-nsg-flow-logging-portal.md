---
title: "Gerir registos da fluxo de grupo de segurança de rede com o observador de rede do Azure | Microsoft Docs"
description: "Esta página explica como gerir os registos de fluxo do grupo de segurança de rede no observador de rede do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 633543aba99f5c09b14a9e4b11adf59ca04d0fe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Gerir a rede registos de fluxo do grupo de segurança no portal do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Registos de fluxo de grupo de segurança de rede são uma funcionalidade do observador de rede que permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede. Estes registos de fluxo são escritos no formato JSON e fornecem informações importantes, incluindo: 

- Fluxos de saída e entrados numa base por regras.
- O NIC que se aplica o fluxo.
- 5 cadeias de identificação informações sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo).
- Informações sobre se o tráfego foi permitido ou negado.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar uma instância de observador de rede](network-watcher-create.md). O cenário também parte do princípio de que uma tem um grupo de recursos com uma máquina virtual válida.

## <a name="register-insights-provider"></a>Fornecedor de informações de registo

Fluxo de registo para trabalhar com êxito, o **insights** fornecedor tem de estar registado. Para registar o fornecedor, siga os passos seguintes: 

1. Aceda a **subscrições**e, em seguida, selecione a subscrição para o qual pretende ativar registos de fluxo. 
2. No **subscrição** painel, selecione **fornecedores de recursos**. 
3. Observe a lista de fornecedores e certifique-se de que o **insights** fornecedor está registado. Se não estiver, em seguida, selecione **registar**.

![Fornecedores de vista][providers]

## <a name="enable-flow-logs"></a>Ativar registos de fluxo

Estes passos leva-o ao longo do processo de ativação de registos de fluxo num grupo de segurança de rede.

### <a name="step-1"></a>Passo 1

Aceda a uma instância de observador de rede e, em seguida, selecione **registos de NSG fluxo**.

![Descrição geral de registos de fluxo][1]

### <a name="step-2"></a>Passo 2

Selecione um grupo de segurança de rede a partir da lista.

![Descrição geral de registos de fluxo][2]

### <a name="step-3"></a>Passo 3 

No **as definições de registos de fluxo** painel, defina o estado como **no**e, em seguida, configurar uma conta de armazenamento.  Quando estiver pronto, selecione **OK**. Em seguida, selecione **guardar**.

![Descrição geral de registos de fluxo][3]

## <a name="download-flow-logs"></a>Transferir os registos de fluxo

Registos de fluxo são guardados numa conta do storage. Transferir os registos de fluxo a visualizá-las.

### <a name="step-1"></a>Passo 1

Para transferir os registos de fluxo, selecione **pode transferir os registos de fluxo de contas de armazenamento configurados**. Este passo leva-o para uma vista de conta de armazenamento onde pode escolher quais os registos para transferir.

![As definições de registos de fluxo][4]

### <a name="step-2"></a>Passo 2

Vá para a conta de armazenamento correta. Em seguida, selecione **contentores** > **insights-registo-networksecuritygroupflowevent**.

![As definições de registos de fluxo][5]

### <a name="step-3"></a>Passo 3

Vá para a localização do registo de fluxo, selecione-o e, em seguida, selecione **transferir**.

![As definições de registos de fluxo][6]

Para obter informações sobre a estrutura do registo, visite [rede grupo fluxo registo descrição geral de segurança](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Passos seguintes

Saiba como [visualizar os registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png

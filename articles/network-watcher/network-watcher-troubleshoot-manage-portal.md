---
title: "Resolver problemas de Gateway de rede Virtual do Azure e ligações - PowerShell | Microsoft Docs"
description: "Esta página explica como utilizar o observador de rede de Azure resolver problemas relacionados com o cmdlet do PowerShell"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: c3fa22bd599026b0838b134e26062d9837df703e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Resolver problemas de Gateway de rede Virtual e ligações com o Azure PowerShell de observador de rede

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Observador de rede oferece muitas funcionalidades no que respeita à compreender os seus recursos de rede no Azure. Uma dessas funcionalidades é recursos de resolução de problemas. Resolução de problemas de recursos pode ser chamada através do portal, o PowerShell, a CLI ou a REST API. Quando chamado, o observador de rede inspeciona o estado de funcionamento de um Gateway de rede Virtual ou uma ligação e devolve conclusões.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para obter uma lista de visita de tipos de gateway suportados, [tipos de Gateway suportado](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

Resolução de problemas de recursos fornece a capacidade de resolução de problemas que surgir com Gateways de rede Virtual e ligações. Quando é efetuado um pedido para a resolução de problemas de recursos, os registos estão a ser consultado e inspecioná-los. Quando a inspeção estiver concluída, os resultados são devolvidos. Recursos pedidos de resolução de problemas são execução longos pedidos, que pode demorar vários minutos a devolver um resultado. Os registos de resolução de problemas são armazenados num contentor numa conta de armazenamento que é especificado.

## <a name="troubleshoot-a-gateway-or-connection"></a>Resolver problemas de gateway ou ligação

1. Navegue para o [portal do Azure](https://portal.azure.com) e clique em **redes** > **observador de rede** > **diagnósticos de VPN**
2. Selecione um **subscrição**, **grupo de recursos**, e **localização**.
3. Resolução de problemas de recursos devolve dados sobre o estado de funcionamento do recurso. Também economizam registos relevantes para uma conta de armazenamento para ser revisto. Clique em **conta de armazenamento** para selecionar uma conta de armazenamento.
4. No **contas do Storage** painel, selecione uma conta de armazenamento existente ou clique em **+ contas de armazenamento** para criar um novo.
5. No **contentores** painel, selecione um contentor existente ou clique em **+ contentor** para criar um novo contentor. Quando concluir clique **selecione**
6. Selecione os recursos de ligação de Gateway e e clique em resolver **Iniciar resolução de problemas**

Se estiverem selecionados vários recursos, resolução de problemas é executada em simultâneo em recursos de gateway. Não pode ser executado numa ligação e gateway está associado ao mesmo tempo. É recomendado para resolver problemas de gateways pela primeira vez, dado que a resolução de problemas de ligação é um processo mais. Enquanto o diagnóstico de VPN está em execução num recurso de **estado de resolução de problemas** coluna mostrará um Estado de **em execução**

Quando terminar, a coluna de estado é alterado para **bom estado de funcionamento** ou **mau estado de funcionamento**.

![resolução de problemas concluída][2]

## <a name="understanding-the-results"></a>Compreender os resultados

No **detalhes** secção da janela, o **estado** separador mostra o estado de resolução última execução no recurso selecionado. Resultados do diagnóstico mais recente são mostrados xx minutos após a última execução.

|Propriedade  |Descrição  |
|---------|---------|
|Recurso     | Uma ligação para o recurso.        |
|Caminho de armazenamento     |  Caminho para a conta de armazenamento e o contentor que contêm os registos (se qualquer foram produzidos durante a execução). Esta definição não persiste depois de deixar o portal.        |
|Resumo     | Resumo do Estado de funcionamento de recursos.        |
|Detalhe     | Obter informações detalhadas sobre o estado de funcionamento do recurso.        |
|Última execução     | A hora do último tempo de resolução de problemas foi executado.        |


O **ação** separador fornece orientações gerais sobre como resolver o problema. Se uma ação pode ser levada para o problema, é fornecida uma ligação com orientações adicionais. No caso em que não existe nenhuma orientação adicional, a resposta fornece o url para abrir um incidente de suporte.  Para obter mais informações sobre as propriedades de resposta e que está incluído, visite [descrição geral de resolução de problemas de observador de rede](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>Passos seguintes

Se foram alteradas as definições que conectividade VPN de parar, consulte o artigo [gerir grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para identificar as regras de segurança e de grupo de segurança de rede que poderão estar em questão.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png

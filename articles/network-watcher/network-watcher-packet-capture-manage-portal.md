---
title: Gerir capturas de pacotes com observador de rede do Azure - portal do Azure | Microsoft Docs
description: "Esta página explica como gerir a funcionalidade de captura de pacotes do observador de rede através do portal do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 818f6513625a2677668dd6b6869ef969fe015bf7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gerir capturas de pacotes com observador de rede do Azure através do portal

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

Captura de pacotes do observador de rede permite-lhe criar sessões de captura para controlar o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura garantir que apenas o tráfego que pretende capturar. Captura de pacotes ajuda-o para diagnosticar anomalias de rede de forma reativa e de forma pró-ativa. Outras utilizações incluem a recolha de estatísticas de rede, obtenha informações sobre intrusions de rede, para depurar as comunicações cliente-servidor e muito mais. Sendo possível para acionar remotamente capturas de pacotes, esta capacidade facilita o fardo de executar uma captura de pacotes manualmente e na máquina pretendida, que poupa tempo importante.

Este artigo orienta-as tarefas de gestão diferentes que estão atualmente disponíveis para captura de pacotes.

- [**Inicie uma captura de pacotes**](#start-a-packet-capture)
- [**Parar uma captura de pacotes**](#stop-a-packet-capture)
- [**Eliminar uma captura de pacotes**](#delete-a-packet-capture)
- [**Transferir uma captura de pacotes**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem os seguintes recursos:

- Uma instância do observador de rede na região onde pretende criar uma captura de pacotes
- Uma máquina virtual com a extensão de captura de pacotes ativada.

> [!IMPORTANT]
> Captura de pacotes requer uma extensão da máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão numa Windows VM visite [extensão da máquina virtual de agente de observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para, visite VM com Linux [extensão da máquina virtual de agente de observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Extensão de agente de captura de pacotes através do portal

Para instalar o agente VM de captura de pacotes através do portal, navegue para a máquina virtual, clique em **extensões** > **adicionar** e procure **rede observador de agente do Windows**

![Vista de agentes][agent]

## <a name="packet-capture-overview"></a>Descrição geral de captura de pacotes

Navegue para o [portal do Azure](https://portal.azure.com) e clique em **redes** > **observador de rede** > **captura de pacotes**

A página de descrição geral mostra que uma lista de todos os pacotes captura que existe, independentemente do Estado.

> [!NOTE]
> Captura de pacotes necessita de conectividade para a conta de armazenamento através da porta 443.

![ecrã de descrição geral de captura de pacotes][1]

## <a name="start-a-packet-capture"></a>Inicie uma captura de pacotes

Clique em **adicionar** para criar uma captura de pacotes.

As propriedades que podem ser definidas numa captura de pacotes são:

**Definições principais**

- **Subscrição** -este valor é a subscrição que é utilizada, é necessária uma instância do observador de rede em cada subscrição.
- **Grupo de recursos** -o grupo de recursos da máquina virtual que está a ser a visada.
- **Máquina virtual de destino** -a máquina virtual que está a executar a captura de pacotes
- **O nome de captura de pacotes** -este valor é o nome de captura de pacotes.

**Captura de configuração**

- **Conta de armazenamento** -determina se a captura de pacotes é guardada numa conta do storage.
- **Ficheiro** -determina se uma captura de pacotes é guardada localmente na máquina virtual.
- **As contas do Storage** - a conta de armazenamento para guardar a captura de pacotes no selecionada. Localização predefinida é o id da https://{storage conta name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription} /resourcegroups/ {nome da máquina do name}/providers/microsoft.compute/virtualmachines/{virtual de grupo de recursos} / {AA} / {MM} / {DD} / packetcapture_ {HH}_{MM}_.cap _ {XXX} de {SS}. (Apenas ativada se **armazenamento** está selecionado)
- **Caminho do ficheiro local** -o caminho local numa máquina virtual para guardar a captura de pacotes. (Apenas ativada se **ficheiro** está selecionado). Tem de ser fornecido um caminho válido
- **Bytes máximos por pacote** - o número de bytes de cada pacote que são capturadas, todos os bytes são capturados se deixado em branco.
- **Bytes máximos por sessão** - Total número de bytes que são capturadas, assim que o valor for atingido a deixa de captura de pacotes.
- **Tempo limite (segundos)** -define um limite de tempo para a captura de pacotes parar. Predefinição é 18000 segundos.

> [!NOTE]
> As contas de armazenamento Premium não são atualmente suportadas para armazenar o pacote de captura.

**Filtragem (opcional)**

- **Protocolo** -o protocolo para filtrar a captura de pacotes. Os valores disponíveis são TCP, UDP e qualquer.
- **Endereço IP local** -este valor filtra a captura de pacotes para os pacotes em que o endereço IP local corresponde a este valor de filtro.
- **Porta local** -este valor filtra a captura de pacotes para os pacotes em que a porta local corresponde a este valor de filtro.
- **Endereço IP remoto** -este valor filtra a captura de pacotes para os pacotes em que o IP remoto corresponde a este valor de filtro.
- **Porta remota** -este valor filtra a captura de pacotes para os pacotes em que a porta remota corresponde a este valor de filtro.

> [!NOTE]
> Valores de endereço IP e porta podem ser um valor único, intervalo de valores ou um conjunto. (ou seja, porta 80-1024 para) Pode definir filtros tantos conforme pretender.

Assim que os valores são preenchidos, clique em **OK** para criar a captura de pacotes.

![criar uma captura de pacotes][2]

Depois do limite de tempo definido na captura de pacotes expirou, a captura de pacotes irá parar e pode ser revista. Pode também manualmente parar as sessões de capturas de pacotes.

## <a name="delete-a-packet-capture"></a>Eliminar uma captura de pacotes

Na vista de captura de pacote, clique o **menu de contexto** (…) ou clique com o botão direito e, em **eliminar** para parar a captura de pacotes

![Eliminar uma captura de pacotes][3]

> [!NOTE]
> Eliminar uma captura de pacotes não elimina o ficheiro na conta de armazenamento.

É-lhe pedido para confirmar que pretende eliminar a captura de pacotes, clique em **Sim**

![Confirmação][4]

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacotes

Na vista de captura de pacote, clique o **menu de contexto** (…) ou clique com o botão direito e, em **parar** para parar a captura de pacotes

## <a name="download-a-packet-capture"></a>Transferir uma captura de pacotes

Depois de concluída a sua sessão de captura de pacote, o ficheiro de captura é carregado para o blob storage ou para um ficheiro local na VM. A localização de armazenamento da captura de pacotes está definida na criação da sessão. Uma ferramenta conveniente para aceder a estes ficheiros de captura guardados para uma conta de armazenamento é Explorador de armazenamento do Microsoft Azure, que pode ser transferida aqui: http://storageexplorer.com/

Se for especificada uma conta de armazenamento, ficheiros de captura de pacotes são guardados para uma conta de armazenamento disponíveis na seguinte localização:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Passos seguintes

Saiba como automatizar capturas de pacotes com alertas de Máquina Virtual visualizando [criar uma captura de pacotes accionadas alerta](network-watcher-alert-triggered-packet-capture.md)

Determinar se determinados o tráfego é permitido dentro ou fora da sua VM, visitando [Certifique-se de fluxo de verificação de IP](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png














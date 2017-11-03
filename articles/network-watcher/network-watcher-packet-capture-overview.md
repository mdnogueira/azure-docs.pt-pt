---
title: "Introdução à captura de pacotes no observador de rede do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral da capacidade de captura de pacotes de observador de rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 152cc8fb61aa6115c7b5863e4d798db9e7aa5b7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introdução à captura de pacotes variável na observador de rede do Azure

Captura de pacotes de variável de observador de rede permite-lhe criar sessões de captura de pacotes para controlar o tráfego de e para uma máquina virtual. Ajuda a captura de pacotes para diagnosticar anomalias rede de forma reativa e proactivity. Outras utilizações incluem a recolha de estatísticas de rede, obtenha informações sobre intrusions de rede, para depurar as comunicações cliente-servidor e muito mais.

Captura de pacotes é uma extensão de máquina virtual que esteja iniciada remotamente através do observador de rede. Esta capacidade facilita o fardo de executar uma captura de pacotes manualmente na máquina virtual pretendida, que poupa tempo importante. Captura de pacotes pode ser acionada através do portal, o PowerShell, a CLI ou a REST API. Um exemplo de como pode ser acionada captura de pacotes é com alertas de Máquina Virtual. Os filtros são fornecidos para a sessão de captura garantir a que capturar o tráfego que pretende monitorizar. Filtros baseiam-se no 5 cadeias de identificação (protocolo, endereço IP local, endereço IP remoto, porta local e porta remota) informações. Os dados capturados são armazenados no disco local ou um blob de armazenamento. Não há um limite de 10 sessões de captura de pacotes por região por subscrição. Este limite aplica-se apenas para as sessões e não se aplica aos ficheiros de captura de pacotes guardados localmente na VM ou numa conta do storage.

> [!IMPORTANT]
> Captura de pacotes requer uma extensão da máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão numa Windows VM visite [extensão da máquina virtual de agente de observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para, visite VM com Linux [extensão da máquina virtual de agente de observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

Para reduzir as informações de que captura para apenas as informações que pretende, as seguintes opções estão disponíveis para uma sessão de captura de pacotes:

**Captura de configuração**

|Propriedade|Descrição|
|---|---|
|**Bytes máximos por pacote (bytes)** | O número de bytes de cada pacote que são capturadas, todos os bytes são capturados se deixado em branco. O número de bytes de cada pacote que são capturadas, todos os bytes são capturados se deixado em branco. Se tiver apenas o cabeçalho de IPv4 – indicar 34 aqui |
|**Bytes máximos por sessão (bytes)** | Número total de bytes que é capturado, assim que o valor for atingido a sessão é terminada.|
|**Tempo limite (segundos)** | Define uma restrição de tempo no pacote capturar sessão. O valor predefinido é 18000 segundos ou 5 horas.|

**Filtragem (opcional)**

|Propriedade|Descrição|
|---|---|
|**Protocolo** | O protocolo para filtrar a captura de pacotes. Os valores disponíveis são TCP, UDP e todos os.|
|**Endereço IP local** | Este valor filtra a captura de pacotes para os pacotes em que o endereço IP local corresponde a este valor de filtro.|
|**Porta local** | Este valor filtra a captura de pacotes para os pacotes em que a porta local corresponde a este valor de filtro.|
|**Endereço IP remoto** | Este valor filtra a captura de pacotes para os pacotes em que o IP remoto corresponde a este valor de filtro.|
|**Porta remota** | Este valor filtra a captura de pacotes para os pacotes em que a porta remota corresponde a este valor de filtro.|

### <a name="next-steps"></a>Passos seguintes

Saiba como pode gerir capturas de pacotes através do portal, visitando [gerir captura de pacotes no portal do Azure](network-watcher-packet-capture-manage-portal.md) ou com o PowerShell, visitando [gerir pacotes de captura com o PowerShell](network-watcher-packet-capture-manage-powershell.md).

Saiba como criar capturas de pacotes proativa com base em alertas de máquina virtual, visitando [criar uma captura de pacotes accionadas alerta](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png














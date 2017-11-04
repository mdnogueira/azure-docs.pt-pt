---
title: "Definir Suportabilidade da adição de VMs do Azure para um disponibilidade existente | Microsoft Docs"
description: "Suporte de adição de VMs do Azure para um conjunto de disponibilidade existente."
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: delhan
ms.openlocfilehash: 8bf2a55563772e26239445732b2b08df677436ef
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Suporte de adição de VMs do Azure para um conjunto de disponibilidade existente

Pode ocasionalmente encontrar limitações quando adicionar novas máquinas virtuais (VMs) para um conjunto de disponibilidade existente. O gráfico seguinte fornece detalhes sobre qual série VM pode misturar no mesmo conjunto de disponibilidade.

Segue-se a matriz de Suportabilidade para misturar diferentes tipos de VMs:

Série & conjunto de disponibilidade|VM segundo|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primeira VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todos os outro série não foi possível no mesmo conjunto pois requerem que um hardware específico de disponibilidade.

Tamanho da VM a8/A9 não é possível misturar devido a requirment na rede de back-end dedicado RDMA.

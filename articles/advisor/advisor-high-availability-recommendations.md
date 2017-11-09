---
title: "Recomendações do Advisor elevada disponibilidade do Azure | Microsoft Docs"
description: "Utilize o Advisor do Azure para melhorar a elevada disponibilidade das implementações do Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: e1cd7948e1969cd4ddb926e428c09b559190a805
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-high-availability-recommendations"></a>Recomendações de elevada disponibilidade do Assistente

Azure Advisor ajuda a garantir e melhorar a continuidade das suas aplicações empresariais vitais. Pode obter recomendações de elevada disponibilidade por Advisor a partir do **elevada disponibilidade** separador do dashboard do Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Certifique-se de tolerância a falhas de máquina virtual

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. Advisor identifica as máquinas virtuais que não façam parte de um conjunto de disponibilidade e recomenda movê-los para um conjunto de disponibilidade. Esta configuração assegura que durante a um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível e que cumpre o SLA de máquina virtual do Azure. Pode escolher para criar um conjunto de disponibilidade para a máquina virtual ou ao adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se optar por criar um conjunto de disponibilidade, tem de adicionar pelo menos uma máquina virtual mais para a mesma. Recomendamos que agrupa dois ou mais máquinas de virtuais na disponibilidade de um conjunto para se certificar de que pelo menos uma máquina está disponível durante uma falha.

## <a name="ensure-availability-set-fault-tolerance"></a>Certifique-se de tolerância a falhas de conjunto de disponibilidade 

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. Advisor identifica os conjuntos de disponibilidade que contenham uma única máquina virtual e recomenda a adição de um ou mais máquinas virtuais ao mesmo. Esta configuração assegura que durante a um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível e que cumpre o SLA de máquina virtual do Azure. Pode escolher para criar uma máquina virtual ou para adicionar uma máquina virtual existente para o conjunto de disponibilidade.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Certifique-se a tolerância de falhas de gateway de aplicação
Para assegurar a continuidade do negócio das aplicações fundamentais que são utiliza a tecnologia de gateways de aplicação, Advisor identifica as instâncias de gateway de aplicação que não estão configuradas para tolerância a falhas e sugere ações de remediação que pode tomar. Advisor identifica os gateways de aplicação de instância única médias ou grandes e recomenda adicionar pelo menos uma instância mais. Também identifica única ou várias instance pequeno gateways de aplicação e recomenda a migrar para SKUs médios ou grandes. Advisor recomenda estas ações para garantir que as instâncias de gateway de aplicação estão configuradas para satisfazer os requisitos de SLA atuais para estes recursos.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Melhorar o desempenho e fiabilidade dos discos da máquina virtual

Advisor identifica as máquinas virtuais com discos padrão e recomenda a atualização para os discos premium.
 
Armazenamento Premium do Azure fornece suporte de disco de elevado desempenho, baixa latência para máquinas virtuais que executam cargas de trabalho de I/O intensivo. Discos de máquinas virtuais que utilizam contas de armazenamento premium armazenam dados em unidades de estado sólido (SSDs). Para obter o melhor desempenho para a sua aplicação, recomendamos que migrar qualquer discos de máquinas virtuais que requerem o IOPS elevado para o premium storage. 

Se os discos não necessitam de IOPS elevado, pode limitar os custos, mantendo-los no armazenamento standard. Armazenamento Standard armazena dados de disco da máquina virtual em unidades de disco rígido (HDDs), em vez de SSDs. Pode optar por migrar os discos da máquina virtual para os discos premium. Os discos Premium são suportados na máquina virtual a maioria das SKUs. No entanto, em alguns casos, se pretender utilizar discos premium, poderá ter atualizar a máquina virtual SKUs bem.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteger os seus dados de máquina virtual contra a eliminação acidental
Configurar a cópia de segurança da máquina virtual garante a disponibilidade dos dados empresariais vitais e oferece proteção contra eliminação acidental ou danificados.  Advisor identifica as máquinas virtuais em que a cópia de segurança não está ativada e recomenda a ativação da cópia de segurança. 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como aceder recomendações de elevada disponibilidade no Advisor

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2.  No dashboard do Advisor, clique em de **elevada disponibilidade** separador.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as recomendações do assistente, consulte:
* [Introdução ao Advisor do Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custo do Assistente](advisor-performance-recommendations.md)
* [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)


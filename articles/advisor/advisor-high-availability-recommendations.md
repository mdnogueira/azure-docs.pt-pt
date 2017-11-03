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
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>Recomendações de elevada disponibilidade do Assistente

Azure Advisor ajuda a garantir e melhorar a continuidade das suas aplicações empresariais vitais. Pode obter recomendações de elevada disponibilidade por Advisor a partir do **elevada disponibilidade** separador do dashboard do Advisor.

![Botão de disponibilidade elevado no dashboard do Advisor](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>Certifique-se de tolerância a falhas de máquina virtual

Advisor identifica as máquinas virtuais que não façam parte de um conjunto de disponibilidade e recomenda movê-los para um conjunto de disponibilidade. Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. Esta configuração assegura que durante a um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível e que cumpre o SLA de máquina virtual do Azure. Pode escolher para criar um conjunto de disponibilidade para a máquina virtual ou ao adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se optar por criar um conjunto de disponibilidade, tem de adicionar pelo menos uma máquina virtual mais para a mesma. Recomendamos que agrupa dois ou mais máquinas de virtuais na disponibilidade de um conjunto para se certificar de que pelo menos uma máquina está disponível durante uma falha.

![Recomendação do Advisor: para a redundância de máquina virtual, utilize conjuntos de disponibilidade](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>Certifique-se de tolerância a falhas de conjunto de disponibilidade 

Advisor identifica os conjuntos de disponibilidade que contenham uma única máquina virtual e recomenda a adição de um ou mais máquinas virtuais ao mesmo. Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. Esta configuração assegura que durante a um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível e que cumpre o SLA de máquina virtual do Azure. Pode escolher para criar uma máquina virtual ou seja para utilizar uma máquina virtual existente e adicioná-lo para o conjunto de disponibilidade.  

![Recomendação do Advisor: adicionar uma ou mais VMs a este conjunto de disponibilidade](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>Certifique-se a tolerância de falhas de gateway de aplicação
Para assegurar a continuidade do negócio das aplicações fundamentais que são utiliza a tecnologia de gateways de aplicação, Advisor identifica as instâncias de gateway de aplicação que não estão configuradas para tolerância a falhas e sugere ações de remediação que pode tomar. Advisor identifica os gateways de aplicação de instância única médias ou grandes e recomenda adicionar pelo menos uma instância mais. Também identifica única ou várias instance pequeno gateways de aplicação e recomenda a migrar para SKUs médios ou grandes. Advisor recomenda estas ações para garantir que as instâncias de gateway de aplicação estão configuradas para satisfazer os requisitos de SLA atuais para estes recursos.

![Recomendação do Advisor: implementar duas ou mais instâncias de gateway de aplicação de tamanho médias ou grandes](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Melhorar o desempenho e fiabilidade dos discos da máquina virtual

Advisor identifica as máquinas virtuais com discos padrão e recomenda a atualização para os discos premium.
 
Armazenamento Premium do Azure fornece suporte de disco de elevado desempenho, baixa latência para máquinas virtuais que executam cargas de trabalho de I/O intensivo. Discos de máquinas virtuais que utilizam contas de armazenamento premium armazenam dados em unidades de estado sólido (SSDs). Para obter o melhor desempenho para a sua aplicação, recomendamos que migrar qualquer discos de máquinas virtuais que requerem o IOPS elevado para o premium storage. 

Se os discos não necessitam de IOPS elevado, pode limitar os custos, mantendo-los no armazenamento standard. Armazenamento Standard armazena dados de disco da máquina virtual em unidades de disco rígido (HDDs), em vez de SSDs. Pode optar por migrar os discos da máquina virtual para os discos premium. Os discos Premium são suportados na máquina virtual a maioria das SKUs. No entanto, em alguns casos, se pretender utilizar discos premium, poderá ter atualizar a máquina virtual SKUs bem.

![Recomendação do Advisor: melhorar a fiabilidade dos discos da máquina virtual através da atualização para os discos premium](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteger os seus dados de máquina virtual contra a eliminação acidental
Advisor identifica as máquinas virtuais em que a cópia de segurança não está ativada e recomenda a ativação da cópia de segurança. Configurar a cópia de segurança da máquina virtual garante a disponibilidade dos dados empresariais vitais e oferece proteção contra eliminação acidental ou danificados.

![Recomendação do Advisor: configurar a cópia de segurança de máquina virtual para proteger os seus dados fundamentais](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Recomendações de elevada disponibilidade de acesso no Advisor

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **mais serviços**.

3. No painel de menu de serviço, em **monitorização e gestão**, clique em **Azure Advisor**.  
 É apresentado o dashboard do Advisor.

4. No dashboard do Advisor, clique em de **elevada disponibilidade** separador e, em seguida, selecione a subscrição para o qual pretende receber recomendações.

> [!NOTE]
> Para obter recomendações do Assistente de acesso, primeiro tem *registar a sua subscrição* com o Advisor. Uma subscrição é registada quando um *subscrição proprietário* inicia o dashboard do Advisor e clica no **obter recomendações** botão. Este é um *operação única*. Depois da subscrição está registada, pode aceder recomendações do assistente como *proprietário*, *contribuinte*, ou *leitor* para uma subscrição, um grupo de recursos ou um recurso específico.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as recomendações do assistente, consulte:
* [Introdução ao Advisor do Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custo do Assistente](advisor-performance-recommendations.md)
* [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)


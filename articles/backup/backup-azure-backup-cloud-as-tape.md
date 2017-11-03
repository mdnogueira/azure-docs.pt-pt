---
title: Utilizar o Backup do Azure para substituir a infraestrutura da banda | Microsoft Docs
description: "Saiba como cópia de segurança do Azure fornece semântica semelhantes a banda que lhe permite criar cópias de segurança e restaurar dados no Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
ms.assetid: 2e1bb67d-986c-4437-8056-3a63169b4214
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/10/2017
ms.author: saurse;trinadhk;markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0f3152daf5f91f7c9e540797bf09b21969d2d33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Mover o armazenamento de longa duração a partir de banda para a nuvem do Azure
Clientes de cópia de segurança e o System Center Data Protection Manager do Azure podem:

* Fazer uma cópia de segurança de dados numa agendas que melhor necessidades da organização.
* Manter os dados de cópia de segurança por períodos mais longos
* Certifique-Azure necessita de uma parte do respetiva retenção de longa duração (em vez de banda).

Este artigo explica como os clientes podem ativar políticas de cópia de segurança e retenção. Os clientes que utilizar bandas para endereçar os seus longo-prazo retenção agora tem de ter uma alternativa viável e poderosa com a disponibilidade desta funcionalidade. A funcionalidade está ativada na versão mais recente da cópia de segurança do Azure (que está disponível [aqui](http://aka.ms/azurebackup_agent)). Os clientes do System Center DPM tem de atualizar para, pelo menos, o DPM 2012 R2 UR5 antes de utilizar o DPM com o serviço de cópia de segurança do Azure.

## <a name="what-is-the-backup-schedule"></a>O que é a agenda de cópia de segurança?
A agenda de cópia de segurança indica a frequência da operação de cópia de segurança. Por exemplo, as definições no ecrã seguinte indicam que são efetuadas cópias de segurança diariamente às 18: 00 e à meia-noite.

![Agenda diária](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Os clientes também podem agendar uma cópia de segurança semanal. Por exemplo, as definições no ecrã seguinte indicam que as cópias de segurança são executadas cada Domingo alternativo & quarta-feira no 9:30 AM e 1:00:00.

![Agenda semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>O que é a política de retenção?
A política de retenção Especifica a duração para o qual a cópia de segurança têm de ser armazenada. Em vez de apenas especificar uma "política simples" para todos os pontos de cópia de segurança, os clientes podem especificar diferentes políticas de retenção com base na quando é efetuada a cópia de segurança. Por exemplo, o ponto de cópia de segurança efetuado diariamente, que funciona como um ponto de recuperação operacional, é preservado durante 90 dias. O ponto de cópia de segurança efetuado no final de cada trimestre para fins de auditoria é preservado durante um período de tempo.

![Política de retenção](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

O número total de "pontos de retenção" especificados nesta política é 90 (pontos diários) + 40 (cada um trimestre para 10 anos) = 130.

## <a name="example--putting-both-together"></a>Exemplo – colocando ambos em conjunto
![Ecrã de exemplo](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Política de retenção diárias**: executadas diariamente de cópias de segurança são armazenadas durante sete dias.
2. **Política de retenção semanais**: cópias de segurança criadas todos os dias à meia-noite e 18: 00 Sábado são preservadas para quatro semanas
3. **Política de retenção mensais**: cópias de segurança criadas meia-noite e 18: 00 no último Sábado de cada mês são preservadas durante 12 meses
4. **Política de retenção anuais**: cópias de segurança criadas à meia-noite no último Sábado de cada Março são preservadas para 10 anos

O número total de "pontos de retenção" (pontos do que um cliente pode restaurar dados) no diagrama anterior é calculada da seguinte forma:

* dois pontos por dia para sete dias = 14 pontos de recuperação
* dois pontos por semana para os quatro semanas = 8 pontos de recuperação
* dois pontos por mês durante 12 meses = 24 pontos de recuperação
* um ponto por ano por recuperação 10 anos = 10 pontos

O número total de pontos de recuperação é 56.

> [!NOTE]
> Cópia de segurança do Azure não tem uma restrição no número de pontos de recuperação.
>
>

## <a name="advanced-configuration"></a>Configuração avançada
Ao clicar em **modificar** no ecrã anterior, os clientes têm mais flexibilidade na especificação agendas de retenção.

![Modificar](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o Backup do Azure, consulte:

* [Introdução ao Azure Backup](backup-introduction-to-azure-backup.md)
* [Experimentar o Backup do Azure](backup-try-azure-backup-in-10-mins.md)

---
title: "Cenários de recuperação após desastre para as VMs do Azure | Microsoft Docs"
description: "Saiba o que fazer no caso de uma interrupção do serviço do Azure tem impacto sobre máquinas virtuais do Azure."
services: virtual-machines
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb986a41e33501ee71c93a48457ac4114e33c671
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>O que fazer no caso de uma interrupção do serviço do Azure tem impacto sobre as VMs do Azure
Na Microsoft, trabalhamos rígido para assegurar que os nossos serviços de sempre estão disponíveis para si quando precisá-los. Força se para além dos nosso controlo, por vezes, um impacto no-nos de formas que provocar interrupções de serviço não planeada.

A Microsoft fornece um contrato de nível de serviço (SLA) para os respetivos serviços, como um compromisso de tempo de atividade e conectividade. O SLA para serviços do Azure individuais pode ser encontrado em [contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitas funcionalidades de plataforma incorporado que suportam aplicações altamente disponíveis. Para mais informações sobre estes serviços, leia o artigo [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo abrange um cenário de recuperação de desastre verdadeiro, quando uma região toda sofre uma falha devido a desastre natural principais ou interrupção do serviço ampla. Estes são ocorrências raras, mas tem de preparar para a possibilidade de se houver uma falha de uma região de toda. Se uma região toda sofre uma interrupção do serviço, as cópias dos seus dados localmente redundantes seria temporariamente indisponíveis. Se tiver ativado a georreplicação, três cópias adicionais dos blobs de armazenamento do Azure e tabelas são armazenadas numa região diferente. Em caso de uma falha regional concluída ou um desastre no qual a região primária não é recuperável, a Azure remaps todas as entradas DNS para a região de georreplicação.

Para ajudar a lidar com estas ocorrências raras, podemos fornecer as seguintes orientações para máquinas virtuais do Azure no caso de uma interrupção do serviço da região de toda, onde a aplicação de máquina virtual do Azure está implementada.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 1: Iniciar uma ativação pós-falha utilizando o Azure Site Recovery
Pode configurar o Azure Site Recovery para as suas VMs, de modo a que pode recuperar a sua aplicação com um único clique no fim de minutos. Pode replicar a região do Azure à sua escolha e não restringida a regiões emparelhadas. Pode começar a utilizar pelo [replicar as máquinas virtuais](https://aka.ms/a2a-getting-started). Pode [criar um plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md) para que pode automatizar o processo de ativação pós-falha completa para a sua aplicação. Pode [as ativações pós-falha de teste](../site-recovery/site-recovery-test-failover-to-azure.md) porta antecipadamente sem afetar a aplicação de produção ou a replicação em curso. Em caso de uma interrupção de região primária, acabou [inicie uma ativação pós-falha](../site-recovery/site-recovery-failover.md) e colocar a sua aplicação na região de destino.


## <a name="option-2-wait-for-recovery"></a>Opção 2: Aguardar para recuperação
Neste caso, não é necessária qualquer ação da sua parte. Sabe que estamos a trabalhar diligently para restaurar a disponibilidade do serviço. Pode ver o estado atual do serviço no nosso [Dashboard de estado de funcionamento do serviço de Azure](https://azure.microsoft.com/status/).

Esta é a melhor opção se não tiver definido cópias de segurança do Azure Site Recovery, o armazenamento georredundante com acesso de leitura ou o armazenamento georredundante antes da interrupção. Se configurou armazenamento georredundante ou armazenamento georredundante com acesso de leitura para a conta de armazenamento onde estão armazenados os seus discos rígidos virtuais (VHDs) do VM, pode parecer para recuperar a imagem de base de VHD e tente aprovisionar uma nova VM a partir do mesmo. Não é uma opção preferencial porque existem sem garantias de sincronização de dados. Por conseguinte, esta opção não é garantida funcione.


> [!NOTE]
> Lembre-se de que não têm qualquer controlo sobre este processo e ocorrerá apenas para interrupções ao serviço de toda a região. Por este motivo, deve também dependem outras estratégias de cópia de segurança específicos da aplicação para atingir o nível mais elevado de disponibilidade. Para obter mais informações, consulte a secção sobre [estratégias de dados para recuperação após desastre](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Passos seguintes

- Iniciar [proteger as suas aplicações em execução em máquinas virtuais do Azure](https://aka.ms/a2a-getting-started) utilizando o Azure Site Recovery

- Para obter mais informações sobre como implementar uma estratégia de elevada disponibilidade e recuperação após desastre, consulte o artigo [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Para desenvolver uma compreensão técnica detalhada das capacidades de uma plataforma em nuvem, consulte [orientações técnica do Azure resiliência](../resiliency/resiliency-technical-guidance.md).


- Se as instruções que se não desmarque ou, se pretende que a Microsoft execute as operações em seu nome, contacte [suporte ao cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

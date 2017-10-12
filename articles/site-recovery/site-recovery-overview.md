---
title: "O que é o Azure Site Recovery? | Microsoft Docs"
description: "Fornece uma descrição geral do serviço Azure Site Recovery e resume cenários de implementação."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: aa657c92f347f7529affee78ad1842e5e066b74d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-site-recovery"></a>O que é a Recuperação de Sites?

Bem-vindo ao serviço do Azure Site Recovery! Este artigo fornece uma descrição geral rápida do serviço.

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Continuidade do negócio e recuperação após desastre (BCDR) com os Serviços de Recuperação do Azure

Sendo uma organização, tem de compreender como vai manter os dados protegidos e as aplicações/cargas de trabalho em execução quando ocorrem interrupções planeadas e não planeadas.

Os Serviços de Recuperação do Azure contribuem para a sua estratégia BCDR:

- **O serviço de Recuperação de Sites**: o Site Recovery ajuda a garantir a continuidade do negócio, mantendo as aplicações em execução em VMs e os servidores físicos disponíveis, se um site ficar em baixo. O Site Recovery replica as cargas de trabalho em execução em VMs e servidores físicos para que permaneçam disponíveis num local secundário, se o site primário não estiver disponível. Recupera as cargas de trabalho para o site primário quando estiver novamente operacional.
- **Serviço de Cópia de Segurança**: além disso, o serviço [Azure Backup](https://docs.microsoft.com/azure/backup/) mantém os seus dados seguros e recuperáveis, fazendo uma cópia de segurança dos mesmos para o Azure.

O Site Recovery pode gerir a replicação de:

- VMs do Azure a replicarem entre regiões do Azure.
- Máquinas virtuais no local e servidores físicos que replicam para o Azure ou para um site secundário.


## <a name="what-does-site-recovery-provide"></a>O que proporciona o Site Recovery?

**Funcionalidade** | **Detalhes**
--- | ---
**Implementar uma solução BCDR simples** | Com o Site Recovery, pode configurar e gerir a replicação, ativação pós-falha e reativação pós-falha a partir de uma única localização no portal do Azure.
**Replicar VMs do Azure** | Pode configurar a sua estratégia BCDR, de forma a que as VMs do Azure sejam replicadas entre regiões do Azure.
**Replicar VMs locais fora do local** | Pode replicar VMs no local e servidores físicos para o Azure ou para uma localização secundária no local. A replicação para o Azure elimina o custo e a complexidade de manter um datacenter secundário.
**Replicar qualquer carga de trabalho** | Pode replicar qualquer carga de trabalho em execução em VMs do Azure, VMs Hyper-V no local, VMs VMware e servidores físicos do Windows/Linux suportados.
**Manter os dados resiliente e seguros** | A recuperação de sites orquestra a replicação, sem intercetar dados da aplicação. Os dados replicados são armazenados no armazenamento do Azure, com a resiliência que oferece. Quando ocorre a ativação pós-falha, as VMs do Azure são criadas com base nos dados replicados.
**Cumprir os RTOs e RPOs** | Mantenha os objetivos de tempo de recuperação (RTO) e os objetivos de ponto de recuperação (RPO) dentro dos limites da organização. O Site Recovery fornece replicação contínua para as VMs do Azure e as VMs VMware, e frequência de replicação de apenas 30 segundos para Hyper-V. Pode reduzir ainda mais os objetivos de tempo de recuperação (RTO), integrando com a integração do [Gestor de Tráfego do Azure](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Manter as aplicações consistentes durante uma ativação pós-falha** | Pode configurar pontos de recuperação com instantâneos consistentes com a aplicação. Os instantâneos consistentes com a aplicação capturam os dados do disco, todos os dados na memória e todas as transações em processamento.
**Testar sem interrupções** | Pode facilmente executar ativações pós-falha de teste que admitem testes de recuperação após desastre, sem afetar a replicação em curso.
**Executar ativações pós-falha flexíveis** | Pode executar as ativações pós-falha planeadas para as falhas esperadas sem nenhuma perda de dados ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação), perante desastres inesperados. Pode fazer facilmente a reativação pós-falha para o site primário quando voltar a estar disponível.
**Criar planos de recuperação** | Pode personalizar e sequenciar a ativação pós-falha e a recuperação de aplicações multicamada em várias VMs, com planos de recuperação. Pode agrupar as máquinas em planos e adicionar scripts e ações manuais. Os planos de recuperação podem ser integrados nos runbooks de automatização do Azure.
**Integrar com tecnologias BCDR existentes** | O Site Recovery integra-se com outras tecnologias BCDR. Por exemplo, pode utilizar a Recuperação de Sites para proteger o back-end do SQL Server de cargas de trabalho corporativas, incluindo suporte nativo para o SQL Server AlwaysOn gerir a ativação pós-falha de grupos de disponibilidade.
**Integrar com a biblioteca de automatização** | Uma biblioteca de Automatização do Azure completa fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com o Site Recovery.
**Gerir definições de rede** | O Site Recovery integra-se no Azure com uma gestão simples da rede de aplicações, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração do Gestor de Tráfego do Azure para alternância de rede eficiente.


## <a name="what-can-i-replicate"></a>O que posso replicar?

**Suportado** | **Detalhes**
--- | ---
**O que posso replicar?** | VMs do Azure entre regiões do Azure (em pré-visualização)<br/><br/>  VMs VMware no local, VMs Hyper-V, servidores físicos (Windows e Linux) para o Azure<br/<br/> VMs VMware no local, VMs Hyper-V, servidores físicos para um site secundário. Para VMs Hyper-V, a replicação para um site secundário apenas é suportada se os anfitriões Hyper-V forem geridos pelo VMM do System Center.
**Que regiões são suportadas pelo Site Recovery?** | [Regiões suportadas](https://azure.microsoft.com/regions/services/) |
**Quais são os sistemas operativos de que as máquinas replicadas precisam?** | [Requisitos de VMs do Azure](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)<br></br>[Requisitos de VMs VMware](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Para VMs Hyper-V, qualquer [SO convidado](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) suportado pelo Azure e Hyper-V é suportado.<br/><br/> [Requisitos do servidor físico](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**De que servidores/anfitriões de VMware preciso?** | As VMs VMware podem estar localizadas em [servidores de anfitriões vSphere/VCenter suportados](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Que cargas de trabalho posso replicar?** | Pode replicar qualquer carga de trabalho em execução numa máquina de replicação suportada. Além disso, a equipa do Site Recovery executou testes específicos da aplicação a [várias aplicações](site-recovery-workload.md#workload-summary).


## <a name="azure-portal-considerations"></a>Considerações sobre o portal do Azure

* O Site Recovery pode ser implementado no [portal do Azure](https://portal.azure.com).
* No Portal Clássico do Azure, pode gerir o Site Recovery com o modelo clássico de gestão de serviços.
- O portal clássico só deve ser utilizado para manter as implementações existentes do Site Recovery. Não é possível criar novos cofres no portal clássico.

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o [suporte de cargas de trabalho](site-recovery-workload.md)
* Introdução à [replicação de VMs do Azure entre regiões](site-recovery-azure-to-azure.md), [Replicação de VMware para o Azure](vmware-walkthrough-overview.md) ou [Replicação de Hyper-V para o Azure](hyper-v-site-walkthrough-overview.md).

---
title: "O que é a Recuperação de Sites? | Microsoft Docs"
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
ms.date: 03/14/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: d8e4e4bb7dd1e40d8c561adba04b8346fcb2127d
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-site-recovery"></a>O que é a Recuperação de Sites?

Bem-vindo ao serviço do Azure Site Recovery! Este artigo fornece uma descrição geral rápida do serviço.

As interrupções são causadas por eventos naturais e falhas operacionais. A sua organização necessita de uma estratégia de continuidade empresarial e de recuperação após desastre (BCDR) de modo a que, durante o período de indisponibilidade planeado e não planeado, os dados estejam seguros, as aplicações continuem disponíveis e o negócio recupere as condições de trabalho normais com a maior brevidade possível.

Os Serviços de Recuperação do Azure contribuem para a sua estratégia BCDR. O serviço [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/) mantém os dados seguros e recuperáveis. O Site Recovery replica, efetua a ativação pós-falha e recupera cargas de trabalho, para que estas permaneçam disponíveis quando ocorre uma falha.

## <a name="what-does-site-recovery-provide"></a>O que proporciona o Site Recovery?

- **Recuperação após desastre na cloud**— Pode replicar cargas de trabalho em execução em VMs e servidores físicos para o Azure, em vez de recorrer a um site secundário. Esta ação elimina o custo e a complexidade de manter um datacenter secundário.
- **Replicação flexível para ambientes híbridos**— Pode replicar qualquer carga de trabalho em execução em VMs de Hyper-V no local, VMs de VMware e servidores físicos do Windows/Linux suportados.
- **Migração**— Pode utilizar o Site Recovery para migrar instâncias de AWS no local para VMs do Azure ou para migrar VMs do Azure entre regiões do Azure.
- **BCDR simplificado**— Pode implementar a replicação a partir de uma única localização no portal do Azure.  Pode executar ativações pós-falha simples e a reativação pós-falha de máquinas individuais e múltiplas.
- **Resiliência**— A recuperação de sites orquestra a replicação e a ativação pós-falha, sem intercetar dados da aplicação.
Os dados replicados são armazenados no armazenamento do Azure, com a resiliência que oferece. Quando ocorre a ativação pós-falha, as VMs do Azure são criadas com base nos dados replicados.
- **Desempenho de replicação**— O Site Recovery fornece frequência de replicação de apenas 30 segundos para Hyper-V e replicação contínua para VMware. Pode definir limiares de objetivo de ponto de recuperação (RPO) para controlar a frequência com que os pontos de recuperação de dados são criados e pode reduzir o objetivo de ponto de recuperação (RTO) com o processo de recuperação automatizado do Site Recovery e integração com o [Gestor de Tráfego do Azure](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)
- **Consistência de aplicação**— As máquinas são replicadas com instantâneos consistentes com a aplicação. Para além da captura de dados do disco, a captura de instantâneos consistentes com aplicações retém todos os dados na memória e todas as transações no processo.
- **Testes sem interrupção**— Pode facilmente executar ativações pós-falha de teste que suportam testes de recuperação após desastre, sem afetar os ambientes de produção.
- **Ativação pós-falha e recuperação flexíveis**— Pode executar ativações pós-falha planeadas para falhas esperadas sem nenhuma perda de dados ou ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação), perante desastres inesperados. Pode fazer facilmente a reativação pós-falha para o site primário quando voltar a estar disponível.
- **Planos de recuperação personalizados** — Os planos de recuperação permitem-lhe modelar e personalizar a ativação pós-falha e a recuperação de aplicações de várias camadas que estão distribuídas por várias VMs. Pode ordenar grupos em planos e adicionar scripts e ações manuais. Os planos de recuperação podem ser integrados nos runbooks de automatização do Azure.
- **Aplicações de várias camadas**— Pode criar planos de recuperação para uma ativação pós-falha sequenciada e recuperação de aplicações de várias camadas. Pode agrupar máquinas em camadas diferentes (por exemplo, base de dados, Web, aplicação) dentro de um plano de recuperação e personalizar a forma como cada grupo faz a ativação pós-falha e o arranque.
* **Integração com tecnologias BCDR existentes** – O Site Recovery é integrado com outras tecnologias BCDR. Por exemplo, pode utilizar a Recuperação de Sites para proteger o back-end do SQL Server de cargas de trabalho corporativas, incluindo suporte nativo para o SQL Server AlwaysOn gerir a ativação pós-falha de grupos de disponibilidade.
* **Integração com a biblioteca de automatização** – uma biblioteca de Automatização do Azure completa fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com o Site Recovery.
* **Gestão de rede simples** – a gestão de rede avançada na Recuperação de Sites e no Azure simplifica os requisitos de rede da aplicação, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração do Gestor de Tráfego do Azure para alternância de rede eficiente.


## <a name="whats-supported"></a>O que é suportado?

**Suportado** | **Detalhes**
--- | ---
**Que regiões são suportadas pelo Site Recovery?** | [Regiões suportadas](https://azure.microsoft.com/en-us/regions/services/) |
**O que posso replicar?** | VMs de VMware no local, VMs de Hyper-V, servidores físicos do Windows e Linux.
**Quais são os sistemas operativos de que as máquinas replicadas precisam?** | [Sistemas operativos suportados](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para VMs VMware<br/><br/> Para VMs Hyper-V, qualquer [SO convidado](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) suportado pelo Azure e Hyper-V é suportado.<br/><br/> [Sistemas operativos](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para servidores físicos
**Para onde posso replicar?** | Para armazenamento do Azure ou para um centro de dados secundário<br/><br/> No Hyper-V, apenas as VMs em anfitriões Hyper-V geridos em clouds de VMM do System Center podem replicar para um datacenter secundário.
**De que servidores/anfitriões de VMware preciso?** | As VMs de VMware que pretende replicar podem ser geridas pelos [anfitriões vSphere/servidores vCenter suportados](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Que cargas de trabalho posso replicar** | Pode replicar qualquer carga de trabalho em execução numa máquina de replicação suportada. Além disso, a equipa do Site Recovery executou testes específicos da aplicação a [várias aplicações](site-recovery-workload.md#workload-summary).


## <a name="which-azure-portal"></a>Em qual Portal do Azure?

* O Site Recovery pode ser implementado tanto no [portal do Azure](https://portal.azure.com) mais recente, como no [portal clássico do Azure](https://manage.windowsazure.com/).
* No Portal Clássico do Azure, pode suportar a Recuperação de Sites com o modelo clássico de gestão de serviços.
* No portal do Azure, pode suportar o modelo clássico ou [modelo de implementação do Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) mais recente.
- O portal clássico só deve ser utilizado para manter as implementações existentes do Site Recovery. Não é possível criar novos cofres no portal clássico.

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre o [suporte de cargas de trabalho](site-recovery-workload.md)
* Saiba mais sobre a [arquitetura e os componentes do Site Recovery](site-recovery-components.md)


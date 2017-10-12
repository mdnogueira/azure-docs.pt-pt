---
title: Arquitetura do Operations Management Suite (OMS) | Microsoft Docs
description: "O Microsoft Operations Management Suite (OMS) é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e a proteger a sua infraestrutura no local e na nuvem.  Este artigo identifica os diferentes serviços incluídos no OMS e fornece ligações para o respetivo conteúdo detalhado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 76f69946724b5297b1f9a1f715819c69c4a4a51d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="oms-architecture"></a>Arquitetura de OMS
O [Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) é uma coleção de serviços baseados na nuvem para gerir os seus ambientes no local e na nuvem.  Este artigo descreve os diferentes componentes no local e na nuvem do OMS e a respetiva arquitetura de informática em nuvem de nível elevado.  Para obter mais informações, pode consultar a documentação de cada serviço.

## <a name="log-analytics"></a>Log Analytics
Todos os dados recolhidos pelo [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) são armazenados no repositório do OMS que está alojado no Azure.  Origens Ligadas geram os dados recolhidos para o repositório do OMS.  Atualmente, existem três tipos de origens de dados ligadas.

* Um agente instalado num computador [Windows](../log-analytics/log-analytics-windows-agents.md) ou [Linux](../log-analytics/log-analytics-linux-agents.md) ligado diretamente ao OMS.
* Um grupo de gestão do System Center Operations Manager (SCOM) [ligado ao Log Analytics](../log-analytics/log-analytics-om-agents.md).  Os agentes do SCOM continuam a comunicar com servidores de gestão, que reencaminham eventos e dados de desempenho para o Log Analytics.
* Uma [conta de armazenamento do Azure](../log-analytics/log-analytics-azure-storage.md) que recolhe dados do [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) a partir de uma função de trabalho, função Web ou máquina virtual no Azure.

As origens de dados definem os dados recolhidos pelo Log Analytics a partir de origens ligadas, incluindo registos de eventos e contadores de desempenho.  As soluções adicionam funcionalidades à OMS e podem ser adicionadas facilmente à área de trabalho a partir da [Galeria de Soluções do OMS](../log-analytics/log-analytics-add-solutions.md).  Algumas soluções podem implicar uma ligação direta ao Log Analytics a partir de agentes do SCOM, enquanto que outras podem solicitar a instalação de um agente adicional.

O Log Analytics tem um portal baseada na web que pode utilizar para gerir recursos do OMS, adicionar e configurar soluções do OMS e ver e analisar dados no repositório do OMS.

![Arquitetura do Log Analytics de elevado nível](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Automatização do Azure
Os [Runbooks da Automatização do Azure](http://azure.microsoft.com/documentation/services/automation) são executados na nuvem do Azure, podem aceder a recursos no Azure, noutros serviços em nuvem ou acessíveis a partir da Internet pública.  Também pode designar máquinas no local no seu centro de dados locais com o [Função de Trabalho de Runbook Híbrida](../automation/automation-hybrid-runbook-worker.md) para que os runbooks podem aceder a recursos locais.

As [configurações DSC](../automation/automation-dsc-overview.md) armazenadas na Automatização do Azure podem ser aplicadas diretamente a máquinas virtuais do Azure.  Outras máquinas físicas e virtuais podem solicitar configurações do servidor de solicitação do Automation DSC do Azure.

A Automatização do Azure tem uma solução de OMS que mostra as estatísticas e ligações para iniciar o portal do Azure para quaisquer operações.

![Arquitetura de Automatização do Azure de elevado nível](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure Backup
Os dados protegidos na [Azure Backup](http://azure.microsoft.com/documentation/services/backup) são armazenados num cofre de cópias de segurança localizado numa região geográfica específica.  Os dados são replicados na mesma região e, dependendo do tipo de cofre, também podem ser replicados para outra região para uma maior redundância.

A Cópia de Segurança do Azure tem três cenários fundamentais.

* Máquinas Windows com a Cópia de Segurança do Azure.  Isto permite criar uma cópia de segurança de ficheiros e pastas a partir de qualquer servidor ou cliente Windows diretamente para o cofre de cópias de segurança Azure.  
* System Center Data Protection Manager (DPM) ou Servidor do Microsoft Azure Backup. Isto permite-lhe tirar partido do DPM ou do Servidor do Microsoft Azure Backup para cópia de segurança de ficheiros e pastas, além de cargas de trabalho de aplicação, como o SQL e o SharePoint para o armazenamento local e, em seguida, replicar para o cofre de cópias de segurança do Azure.
* Extensões da Máquina Virtual do Azure.  Isto permite-lhe fazer cópias de segurança de máquinas virtuais do Azure para o cofre de cópias de segurança do Azure.

A Azure Backup tem uma solução de OMS que mostra as estatísticas e ligações para iniciar o portal do Azure para quaisquer operações.

![Arquitetura do Azure Backup de elevado nível](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
O [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) programa a replicação, ativação pós-falha e reativação pós-falha de máquinas virtuais e servidores físicos. Os dados de replicação são trocados entre anfitriões Hyper-V, hipervisores de VMware e servidores físicos em centros de dados principais e secundários, ou entre o centro de dados e o armazenamento do Azure.  O Site Recovery armazena metadados em cofres localizados numa região geográfica específica do Azure. Os dados replicados não são armazenados pelo serviço do Site Recovery.

O Azure Site Recovery tem três cenários de replicação fundamentais.

**Replicação de máquinas virtuais de Hyper-V**

* Se as máquinas virtuais de Hyper-V forem geridas nas nuvens VMM, pode replicar para um centro de dados secundária ou para o armazenamento do Azure.  A replicação no Azure é efetuada através de uma ligação à Internet segura.  A replicação para um centro de dados secundário é através da LAN.
* Se as máquinas virtuais de Hyper-V não forem geridas pelo VMM, pode replicar apenas para o armazenamento do Azure.  A replicação no Azure é efetuada através de uma ligação à Internet segura.

**Replicar máquinas virtuais VMWare**

* Pode replicar máquinas virtuais VMware num centro de dados secundário que execute o VMware ou para o armazenamento do Azure.  A replicação para o Azure pode ocorrer através de uma VPN de site para site, através do ExpressRoute do Azure ou através de uma ligação à Internet segura. Ocorre uma replicação para um centro de dados secundário através do canal de dados do InMage Scout.

**Replicação de servidores físicos do Windows e Linux** 

* É possível replicar servidores físicos para um centro de dados secundário ou para o armazenamento do Azure. A replicação para o Azure pode ocorrer através de uma VPN de site para site, através do ExpressRoute do Azure ou através de uma ligação à Internet segura. Ocorre uma replicação para um centro de dados secundário através do canal de dados do InMage Scout.  O Azure Site Recovery tem uma solução de OMS que apresenta algumas estatísticas, mas tem de utilizar o portal do Azure para quaisquer operações.

![Arquitetura do Azure Site Recovery de elevado nível](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Saiba mais sobre o [Automatização do Azure](https://azure.microsoft.com/documentation/services/automation).
* Saiba mais sobre o [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Saiba mais sobre o [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).


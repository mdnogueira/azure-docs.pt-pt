---
title: "Melhores práticas do Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve as melhores práticas para implementação do Azure Site Recovery"
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.contentlocale: pt-pt
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>Preparar a implementação do Azure Site Recovery

Este artigo descreve como se preparar para a implementação do Azure Site Recovery

## <a name="protecting-hyper-v-virtual-machines"></a>Proteger máquinas virtuais de Hyper-V

Tem duas opções de implementação para proteger máquinas virtuais de Hyper-V. Pode replicar VMs de Hyper-V no local para o Azure ou para um datacenter secundário. Cada implementação tem requisitos diferentes.

**Requisito** | **Replicar para o Azure (com o VMM)** | **Replicar VMs de Hyper-V para o Azure (sem VMM)** | **Replicar VMs de Hyper-V para um site secundário (com VMM)** | **Detalhes**
---|---|---|---|---
**VMM** | VMM em execução no System Center 2012 R2 <br/><br/>Pelo menos, uma cloud do VMM que contenha um ou mais grupos de anfitriões VMM. | ND | Servidores VMM nos sites primário e secundário em execução, pelo menos, no System Center 2012 SP1 com as atualizações mais recentes. <br/><br/> Pelo menos, uma cloud em cada servidor VMM. As clouds devem ter o conjunto de perfis de capacidade de Hyper-V.<br/><br/> A cloud de origem deve ter, pelo menos, um grupo de anfitriões VMM. | Opcional. Não tem de ter implementado o System Center VMM para replicar as máquinas virtuais de Hyper-V para o Azure; contudo, se o tiver, tem de confirmar que o servidor VMM está configurado adequadamente. Isto inclui verificar que está a executar a versão certa do VMM e que as clouds estão devidamente configuradas.
**Hyper-V** | Pelo menos, um servidor anfitrião de Hyper-V no site no local a executar o Windows Server 2012 R2 ou posterior | Pelo menos, um servidor de Hyper-V nos sites de origem e destino a executar, pelo menos, o Windows Server 2012 com as atualizações mais recentes instaladas e ligado à Internet.<br/><br/> Os servidores de Hyper-V têm de estar num grupo de anfitriões numa cloud do VMM. | Pelo menos, um servidor de Hyper-V nos sites de origem e destino a executar, pelo menos, o Windows Server 2012 com as atualizações mais recentes instaladas e ligado à Internet.<br/><br/> Os servidores de Hyper-V têm de estar localizados num grupo de anfitriões numa cloud do VMM. |
**Máquinas virtuais** | Pelo menos, uma VM no servidor anfitrião de Hyper-V de origem. | Pelo menos, uma VM no servidor anfitrião de Hyper-V na cloud do VMM de origem | Pelo menos, uma VM no servidor anfitrião de Hyper-V na cloud do VMM de origem. |  As VMs que sejam replicadas para o Azure têm de cumprir os pré-requisitos de máquinas virtuais do Azure
**Conta do Azure** | Precisa de uma conta do Azure e de uma subscrição no serviço Site Recovery. | Precisa de uma conta do Azure e de uma subscrição no serviço Site Recovery. | ND | Se não tiver uma conta, comece com uma avaliação gratuita.
**Armazenamento do Azure** | Precisa de uma subscrição numa conta de armazenamento no Azure que tenha a georreplicação ativada. | Precisa de uma subscrição numa conta de armazenamento no Azure que tenha a georreplicação ativada. | ND | A conta deve de estar na mesma região que o cofre do Azure Site Recovery e estar associada à mesma subscrição.
**Redes** | Configure o mapeamento de rede, para garantir que todas as máquinas que fazem a ativação pós-falha na mesma rede do Azure podem ligar-se entre si, independentemente do plano de recuperação onde se encontram. Além disso, se um gateway de rede estiver configurado na rede de destino do Azure, as máquinas virtuais podem ligar-se a outras máquinas virtuais no local. Se não configurar o mapeamento de rede, só as máquinas que fazem a ativação pós-falha no mesmo plano de recuperação se podem ligar. | ND |  <br/><br/>Configure o mapeamento de rede para garantir que as máquinas virtuais são ligadas às redes adequadas após a ativação pós-falha e que as máquinas virtuais de réplica são colocadas em servidores anfitrião de Hyper-V da melhor maneira. Se não configurar o mapeamento de rede, as máquinas replicadas não se ligarão a nenhuma rede de VMs após a ativação pós-falha. |  Para configurar o mapeamento de rede com o VMM, tem de se certificar de que a lógica do VMM e as redes de VMs estão configuradas corretamente.
**Fornecedores e agentes** | Durante a implementação, vai instalar o Fornecedor do Azure Site Recovery nos servidores VMM. Em servidores Hyper-V em clouds do VM, vai instalar o agente dos Serviços de Recuperação do Azure. | Durante a implementação, vai instalar o Fornecedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure no servidor anfitrião de Hyper-V ou no cluster| Durante a implementação, vai instalar o Fornecedor do Azure Site Recovery nos servidores VMM. Em servidores Hyper-V em clouds do VM, vai instalar o agente dos Serviços de Recuperação do Azure. | Os fornecedores e os agentes ligam-se ao Site Recovery através da Internet, utilizando uma ligação HTTPS encriptada. Não tem de adicionar exceções de firewall nem de criar um proxy específico para a ligação do Fornecedor.
**Ligação à Internet** | Só os servidores VMM precisam de ligação à Internet | Só os servidores anfitriões de Hyper-V precisam de ligação à Internet | Só os servidores VMM precisam de ligação à Internet | Não é preciso instalar nada nas máquinas virtuais, as quais não se ligam diretamente à Internet.



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>Proteger máquinas virtuais de VMware ou servidores físicos

Tem duas opções de implementação para proteger máquinas virtuais de VMware ou servidores físicos do Windows/Linux. Pode replicá-los para o Azure ou para um datacenter secundário. Cada implementação tem requisitos diferentes.

**Requisito** | **Replicar VMs de VMware/servidores físicos para o Azure** | * **Replicar VMs de VMware/servidores físicos para um site secundário**  
---|---|---
**Site primário** | **Servidor de processos**: um servidor Windows dedicado (físico ou virtual) | **Servidor de processos**: um servidor Windows dedicado (físico ou máquina virtual de VMware)<br/><br/>  
**Site no local secundário** | ND | **Servidor de configuração**: um servidor Windows dedicado (físico ou virtual) <br/><br/> **Servidor de destino mestre**: um servidor dedicado (físico ou virtual). Configure com o Windows para proteger computadores Windows ou com o Linux para proteger Linux.
**Azure** | **Subscrição**: precisa de uma subscrição para o serviço Site Recovery. <br/><br/> **Conta de armazenamento**: precisa de uma conta de armazenamento com a georreplicação ativada. A conta deve de estar na mesma região que o cofre do Site Recovery e estar associada à mesma subscrição. <br/><br/> **Servidor de configuração**: tem de configurar o servidor de configuração como uma VM do Azure <br/><br/> **Servidor de destino mestre**: tem de configurar o servidor de destino mestre como uma VM do Azure <br/><br/> Configure com o Windows para proteger computadores Windows ou com o Linux para proteger Linux.<br/><br/> **Rede virtual do Azure**: precisa de uma rede virtual do Azure na qual o servidor de configuração e o servidor de destino mestre serão implementados. Deve estar na mesma subscrição e região que o cofre do Azure Site Recovery. | ND  
**Máquinas virtuais/servidores físicos** | Pelo menos, uma máquina virtual de VMware ou servidor físico do Windows/Linux<br/><br/>Durante a implementação, é instalado o serviço de Mobilidade em cada máquina| Pelo menos, uma VM de VMware ou servidor físico do Windows/Linux.<br/><br/> Durante a implementação, o Agente Unificado é instalado em cada máquina.




## <a name="azure-virtual-machine-requirements"></a>Requisitos das máquinas Virtuais do Azure

Pode implementar o Site Recovery para replicar máquinas virtuais e servidores físicos que executem qualquer sistema operativo que o Azure suporte. Estes incluem a maioria das versões do Windows e do Linux. Tem de confirmar que as máquinas virtuais no local que quer proteger estão em conformidade com os requisitos do Azure.


## <a name="optimizing-your-deployment"></a>Otimizar a implementação

Utilize as sugestões seguintes para ajudar a otimizar e dimensionar a sua implementação.

- **Tamanho do volume do sistema operativo**: quando replica uma máquina virtual para o Azure, o volume do sistema operativo tem de ser inferior a 1 TB. Se tiver mais volumes do que este, pode movê-los manualmente para outro disco antes de iniciar a implementação.
- **Tamanho do disco de dados**: se estiver a replicar para o Azure, pode ter até 32 discos de dados numa máquina virtual, cada um com um máximo de 1 TB. Pode replicar e fazer a ativação pós-falha eficazmente de máquinas virtuais com aproximadamente&32; TB.
- **Limites do plano de recuperação**: o Site Recovery pode dimensionar-se para milhares de máquinas virtuais. Os planos de recuperação foram concebidos como um modelo para as aplicações que devem fazer a ativação pós-falha em conjunto, pelo que limitamos o número de máquinas num plano de recuperação a 50.
- **Limites do serviço do Azure**: cada subscrição do Azure inclui um conjunto de limites predefinidos relativos a núcleos, serviços cloud, etc. Recomendamos que execute uma ativação pós-falha de teste para verificar a disponibilidade dos recursos na sua subscrição. Pode modificar estes limites através do suporte do Azure.
- **Planeamento da capacidade**: planeie o dimensionamento e o desempenho.
- **Largura de banda de replicação**: se tiver pouca largura de banda, tenha em conta que:
    - **ExpressRoute**: o Site Recovery funciona com o Azure ExpressRoute e otimizadores WAN, como o Riverbed.
    - **Tráfego de replicação**: o Site Recovery inicia uma replicação inicial inteligente, utilizando apenas blocos de dados e não o VHD inteiro. Durante a replicação contínua, só são replicadas as alterações.
    - **Tráfego de rede**: pode controlar o tráfego de rede utilizado para replicação ao configurar o Windows QoS com uma política baseada no endereço IP e na porta de destino.  Além disso, se estiver a replicar para o Azure Site Recovery com o agente do Azure Backup. Pode configurar a limitação para esse agente.
- **RTO**: se quiser medir o objetivo de tempo de recuperação (RTO) que pode esperar com o Site Recovery, sugerimos que execute uma ativação pós-falha de teste e que veja as tarefas do Site Recovery, para analisar quanto tempo as operações levam a ser concluídas. Se estiver a fazer uma ativação pós-falha para o Azure, para obter o melhor RTO, recomendamos que automatize todas as ações manuais, integrando na automatização e nos planos de recuperação do Azure.
- **RPO**: o Site Recovery suporta um objetivo de ponto de recuperação (RPO) quase síncrono quando replica para o Azure. Tal pressupõe uma largura de banda suficiente entre o seu datacenter e o Azure.


<properties
    pageTitle="O que é a Recuperação de Sites? | Microsoft Azure" 
    description="Fornece uma descrição geral do serviço Azure Site Recovery e explica a forma como o serviço pode ser implementado." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="02/22/2016" 
    ms.author="raynew"/>

#  O que é a Recuperação de Sites?

Bem-vindo ao Azure Site Recovery! Comece com este artigo para obter uma descrição geral rápida do serviço de Recuperação de Sites e como este pode contribuir para a continuidade de negócio e recuperação face a desastres (BCDR).

## Descrição geral

As organizações necessitam de uma estratégia BCDR que determine como as aplicações, cargas de trabalho e dados continuam em execução e disponíveis durante o período de indisponibilidade planeado e imprevisto, e como retomar as condições de trabalho normais com a maior brevidade possível. A estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis, e garantir que as cargas de trabalho continuam a estar continuamente disponíveis quando ocorrer um desastre. 

A Recuperação de Sites é um serviço Azure que contribui para a sua estratégia BCDR através da orquestração da replicação dos servidores físicos no local e das máquinas virtuais para a nuvem (Azure) ou para um datacenter secundário. Quando ocorrem falhas na sua localização principal, faz-se a ativação pós-falha para a localização secundária para manter disponíveis as aplicações e cargas de trabalho. A localização principal é reativada quando se retomam as operações normais. Saiba mais em [O que é a Recuperação de Sites?](site-recovery-overview.md)

## Recuperação de Sites no Portal do Azure

O Azure tem dois [modelos de implementação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: o modelo Azure Resource Manager e o modelo clássico de gestão de serviços. O Azure também tem dois portais: o [Portal Clássico do Azure](https://manage.windowsazure.com/), que suporta o modelo de implementação clássica, e o [Portal do Azure](https://portal.azure.com), com suporte para ambos os modelos de implementação.

A Recuperação de Sites está disponível no Portal Clássico e no Portal do Azure. No Portal Clássico do Azure, pode suportar a Recuperação de Sites com o modelo clássico de gestão de serviços. No Portal do Azure, pode suportar as implementações do modelo clássico ou do Resource Manager. [Saiba mais](site-recovery-overview.md#site-recovery-in-the-azure-portal) sobre a implementação com o Portal do Azure.

As informações contidas neste artigo aplicam-se às implementações do Portal Clássico do Azul e do Portal do Azure. As diferenças são assinaladas quando aplicável.


## Por que utilizar a Recuperação de Sites? 

Saiba o que a Recuperação de Sites pode fazer pela sua empresa:

- **Simplificar a estratégia BCDR** – a Recuperação de Sites facilita o controlo da replicação, a ativação pós-falha e a recuperação de várias cargas de trabalho e aplicações de empresas a partir de uma única localização. A Recuperação de Sites controla a replicação e ativação pós-falha, mas não interceta os dados da aplicação nem obtém informação sobre a mesma.
- **Proporciona replicação flexível** – com a Recuperação de Sites, pode replicar as cargas de trabalho em execução em máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos do Windows/Linux. 
- **Fácil ativação pós-falha e recuperação** – a Recuperação de Sites fornece ativações pós-falha de teste que admitem manobras de recuperação após desastre sem afetar os ambientes de produção. Também pode executar as ativações pós-falha planeadas sem nenhuma perda de dados para as falhas esperadas ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. Após a ativação pós-falha pode fazer a reativação nos sites primários. A Recuperação de Sites fornece planos de recuperação que podem incluir scripts e livros de automatização do Azure, para que possa personalizar a ativação pós-falha e a recuperação de aplicações de várias camadas. 
- **Eliminar o datacenter secundário** – é possível replicar para um site secundário no local ou para o Azure. Se utilizar o Azure como destino para a recuperação após desastre, elimina o custo e a complexidade de manter um site secundário, e os dados replicados estão armazenados no Storage do Azure, com toda a resiliência que proporciona.
- **Integrar com tecnologias BCDR existentes** – parceiros da Recuperação de Sites com outras funcionalidades de aplicação de BCDR. Por exemplo, pode utilizar a Recuperação de Sites para proteger o back-end do SQL Server de cargas de trabalho corporativas, incluindo suporte nativo para o SQL Server AlwaysOn gerir a ativação pós-falha de grupos de disponibilidade. 

## O que posso replicar?

Apresentamos a seguir um resumo daquilo que pode replicar com a Recuperação de Sites.

![Local para local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICAR** | **REPLICAR A PARTIR DE (NO LOCAL)** | **REPLICAR PARA** | **ARTIGO**
---|---|---|---
VMs VMware | Servidor VMware | Azure | [Saiba mais](site-recovery-vmware-to-azure-classic.md)
VMs VMware | Servidor VMware | Site VMware secundário | [Saiba mais](site-recovery-vmware-to-vmware.md) 
VMs Hyper-V | Anfitrião Hyper-V numa nuvem VMM | Azure | [Saiba mais](site-recovery-vmm-to-azure.md) 
VMs Hyper-V | Anfitrião Hyper-V numa nuvem VMM | Site secundário do VMM | [Saiba mais](site-recovery-vmm-to-vmm.md)
VMs Hyper-V | Anfitrião Hyper-V na nuvem VMM com armazenamento SAN| Site VMM secundário com armazenamento SAN | [Saiba mais](site-recovery-vmm-san.md)
VMs Hyper-V | Anfitrião Hyper-V (sem VMM) | Azure | [Saiba mais](site-recovery-hyper-v-site-to-azure.md)
Servidores físicos do Windows/Linux | Servidor físico | Azure | [Saiba mais](site-recovery-vmware-to-azure-classic.md)
Cargas de trabalho em execução nos servidores físicos do Windows/Linux | Servidor físico | Datacenter secundário | [Saiba mais](site-recovery-vmware-to-vmware.md) 


## Que cargas de trabalho posso proteger?

A Recuperação de Sites pode ajudar na BCDR com suporte para aplicações para que as cargas de trabalho e as aplicações continuem em execução de uma forma consistente quando ocorrerem falhas. A Recuperação de Sites proporciona: 

- **Instantâneos consistentes com a aplicação** – replicação através de instantâneos consistentes com a aplicação para aplicações de uma única ou N camadas.
- **Replicação quase síncrona** – frequência de replicação de 30 segundos para Hyper-V e replicação contínua para VMware.
- **Integração com o SQL Server AlwaysOn** – pode gerir a ativação pós-falha de grupos de disponibilidade no plano de Recuperação de Sites. 
- **Planos de recuperação flexíveis** – pode criar e personalizar planos de recuperação com scripts externos, ações manuais e runbooks de Automatização do Azure que lhe permitem recuperar toda uma pilha de aplicação com um único clique.
- **Biblioteca de Automatização** – uma biblioteca de Automatização do Azure completa fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com a Recuperação de Sites.
- ** Gestão de rede simples** – a gestão de rede avançada na Recuperação de Sites e no Azure simplifica os requisitos de rede da aplicação, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração do Gestor de Tráfego do Azure para alternância de rede eficiente.


## Passos seguintes

- Leia mais em [Que cargas de trabalho pode proteger a Recuperação de Sites?](site-recovery-workload.md)
- Saiba mais sobre a arquitetura de Recuperação de Sites em [Como funciona a Recuperação de Sites?](site-recovery-components.md)
 



<!--HONumber=Aug16_HO1-->



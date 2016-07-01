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

O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../resource-manager-deployment-model.md). Este artigo aplica-se a ambos os modelos. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

## Descrição geral

Uma parte importante da estratégia da BCDR da sua organização é perceber como manter as aplicações e cargas de trabalho empresariais prontas para funcionar quando ocorrerem falhas planeadas ou imprevistas.

A Recuperação de Sites ajuda-o nisto através da coordenação da replicação, a ativação pós-falha e a recuperação de cargas de trabalho e aplicações para que estejam disponíveis a partir de uma localização secundária se a localização principal ficar inativa. 

## Por que utilizar a Recuperação de Sites? 

Saiba o que a Recuperação de Sites pode fazer pela sua empresa:

- **Simplificar a estratégia BCDR** – a Recuperação de Sites facilita o controlo da replicação, a ativação pós-falha e a recuperação de várias cargas de trabalho e aplicações de empresas a partir de uma única localização. A Recuperação de Sites controla a replicação e ativação pós-falha, mas não interceta os dados da aplicação nem obtém informação sobre a mesma.
- **Proporciona replicação flexível** – com a Recuperação de Sites, pode replicar as cargas de trabalho em execução em máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos do Windows/Linux. 
- **Fácil ativação pós-falha e recuperação** – a Recuperação de Sites fornece ativações pós-falha de teste que admitem manobras de recuperação após desastre sem afetar os ambientes de produção. Também pode executar as ativações pós-falha planeadas sem nenhuma perda de dados para as falhas esperadas ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. Após a ativação pós-falha pode fazer a reativação nos sites primários. A Recuperação de Sites fornece planos de recuperação que podem incluir scripts e livros de automatização do Azure, para que possa personalizar a ativação pós-falha e a recuperação de aplicações de várias camadas. 
- **Eliminar o datacenter secundário** – é possível replicar para um site secundário no local ou para o Azure. Se utilizar o Azure como destino para a recuperação após desastre, elimina o custo e a complexidade de manter um site secundário, e os dados replicados estão armazenados no Storage do Azure, com toda a resiliência que proporciona.
- **Integrar com tecnologias BCDR existentes** – parceiros da Recuperação de Sites com outras funcionalidades de aplicação de BCDR. Por exemplo, pode utilizar a Recuperação de Sites para proteger o back-end do SQL Server de cargas de trabalho corporativas, incluindo suporte nativo para o SQL Server AlwaysOn gerir a ativação pós-falha de grupos de disponibilidade. 

## O que posso replicar?

Apresentamos a seguir um resumo daquilo que a Recuperação de Sites pode replicar.

![Local para local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICAR** | **REPLICAR DE** | **REPLICAR PARA** | **ARTIGO**
---|---|---|---
Cargas de trabalho em execução em VMs VMware | Servidor VMware no local | Storage do Azure | [Implementação](site-recovery-vmware-to-azure-classic.md)
Cargas de trabalho em execução em VMs VMware | Servidor VMware no local | Site VMware secundário | [Implementação](site-recovery-vmware-to-vmware.md) 
Cargas de trabalho em execução em VMs Hyper-V | Servidor de anfitrião Hyper-V no local na nuvem VMM | Storage do Azure | [Implementação](site-recovery-vmm-to-azure.md)
Cargas de trabalho em execução em VMs Hyper-V | Servidor de anfitrião Hyper-V no local na nuvem VMM | Site secundário do VMM | [Implementação](site-recovery-vmm-to-vmm.md)
Cargas de trabalho em execução em VMs Hyper-V | Servidor de anfitrião Hyper-V no local na nuvem VMM com armazenamento SAN| Site VMM secundário com armazenamento SAN | [Implementação](site-recovery-vmm-san.md)
Cargas de trabalho em execução em VMs Hyper-V | Site Hyper-V no local (sem VMM) | Storage do Azure | [Implementação](site-recovery-hyper-v-site-to-azure.md)
Cargas de trabalho em execução nos servidores físicos do Windows/Linux | Servidor físico no local | Storage do Azure | [Implementação](site-recovery-vmware-to-azure-classic.md)
Cargas de trabalho em execução nos servidores físicos do Windows/Linux | Servidor físico no local | Datacenter secundário | [Implementação](site-recovery-vmware-to-vmware.md) 


## Que cargas de trabalho posso proteger?

A Recuperação de Sites pode ajudar na BCDR com suporte para aplicações para que as cargas de trabalho e as aplicações continuem em execução de uma forma consistente quando ocorrerem falhas. A Recuperação de Sites proporciona: 

- **Instantâneos consistentes com a aplicação** – replicação através de instantâneos consistentes com a aplicação para aplicações de uma única ou N camadas.
**Replicação quase síncrona** – frequência de replicação de 30 segundos para Hyper-V e replicação contínua para VMware.
**Integração com o SQL Server AlwaysOn** – pode gerir a ativação pós-falha de grupos de disponibilidade no plano de Recuperação de Sites. 
- **Planos de recuperação flexíveis** – pode criar e personalizar planos de recuperação com scripts externos, ações manuais e runbooks de Automatização do Azure que permitem-lhe recuperar toda a pilha de aplicação com um único clique.
- **Biblioteca de Automatização** – uma biblioteca de Automatização do Azure completa fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com a Recuperação de Sites. 
-** Gestão de rede simples** – a gestão de rede avançada na Recuperação de Sites e Azure simplifica os requisitos de rede da aplicação, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração do Traffic Manager do Azure para alternância de rede eficiente.


## Passos seguintes

- Leia mais em [Que cargas de trabalho pode proteger a Recuperação de Sites?](site-recovery-workload.md)
- Saiba mais sobre a arquitetura de Recuperação de Sites em [Como funciona a Recuperação de Sites?](site-recovery-components.md)
 



<!--HONumber=Jun16_HO2-->



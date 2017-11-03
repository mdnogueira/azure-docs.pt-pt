---
title: "Replicar uma implementação de várias camada Citrix XenDesktop e XenApp utilizando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como proteger e recuperar implementações Citrix XenDesktop e XenApp utilizando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: ponatara
ms.openlocfilehash: 52b123b598226e7b03ea9a31c40dd192fd76b191
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>Replicar uma implementação de várias camada Citrix XenApp e XenDesktop utilizando o Azure Site Recovery

## <a name="overview"></a>Descrição geral

Citrix XenDesktop é uma solução de Virtualização de ambientes de trabalho que fornece ambientes de trabalho e aplicações como um serviço ondemand para qualquer utilizador em qualquer lugar. Com a tecnologia de entrega de FlexCast, XenDesktop pode rapidamente e segura fornecer aplicações e ambientes de trabalho aos utilizadores.
Hoje em dia, Citrix XenApp não fornece qualquer desastre funcionalidades de recuperação.

Uma solução de recuperação de desastre boa, deve permitir a modelação de planos de recuperação em torno acima arquiteturas de aplicações complexas e também de ter a capacidade de adicionar passos personalizados para processar os mapeamentos de aplicação entre várias camadas, por conseguinte, certifique-se a fornecer um único clique captura a solução em caso de desastre, originando um RTO inferior.

Este documento fornece uma orientação passo a passo para criar uma solução de recuperação após desastre para os on-premises Citrix XenApp implementações em plataformas de vSphere Hyper-V e VMware. Este documento descreve também como efetuar uma ativação pós-falha de teste (exercício de recuperação após desastre) e a ativação pós-falha não planeada para o Azure utilizando os planos de recuperação, a pré-requisitos e configurações suportadas.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende o seguinte:

1. [Uma máquina virtual a replicar para o Azure](site-recovery-vmware-to-azure.md)
1. Como [estruturar uma rede de recuperação](site-recovery-network-design.md)
1. [Efetuar uma ativação pós-falha de teste para o Azure](site-recovery-test-failover-to-azure.md)
1. [Efetuar uma ativação pós-falha para o Azure](site-recovery-failover.md)
1. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
1. Como [replicar do SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implementação

Um farm Citrix XenApp e XenDesktop normalmente tem o padrão de implementação seguintes:

**Padrão de implementação**

Citrix XenApp e XenDesktop implementação com o servidor DNS de AD, SQL Server da base de dados, controlador de entrega do Citrix, StoreFront servidor, XenApp mestre (VDA), servidor de licenças de XenApp Citrix

![Implementação padrão 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Suporte de recuperação de site

Para efeitos deste artigo, Citrix implementações em máquinas virtuais VMware geridos pelo vSphere 6.0 / System Center VMM 2012 R2 foram utilizadas para DR o programa de configuração.

### <a name="source-and-target"></a>A origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Não está no âmbito | Sim
**VMware** | Não está no âmbito | Sim
**Servidor físico** | Não está no âmbito | Sim

### <a name="versions"></a>Versões
Os clientes podem implementar componentes XenApp como máquinas virtuais em execução no Hyper-V ou VMware ou servidores físicos. O Azure Site Recovery pode proteger implementações físicas e virtuais no Azure.
Uma vez que XenApp 7.7 ou posterior é suportado no Azure, apenas implementações com estas versões podem ser a ativação pós-falha para o Azure para a migração ou de recuperação após desastre.

### <a name="things-to-keep-in-mind"></a>Coisas a lembrar

1. Proteção e recuperação do local implementações de SO do servidor a utilizar máquinas para fornecer XenApp aplicações publicadas e XenApp publicados ambientes de trabalho é suportada.

2. Proteção e recuperação de implementações no local utilizando o ambiente de trabalho máquinas de SO para fornecer o ambiente de trabalho VDI para cliente ambientes de trabalho virtuais, incluindo o Windows 10, não é suportada. Isto acontece porque o ASR não suporta a recuperação de máquinas com o ambiente de trabalho OS'es.  Além disso, alguns virtual ambiente de trabalho sistemas operativos cliente (ex. Windows 7) não são suportadas ainda para licenciamento no Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre o licenciamento para ambientes de trabalho de cliente/servidor no Azure.

3.  O Azure Site Recovery não é possível replicar os dados existentes no local MCS ou PVS clones de proteger.
Será necessário recriá-estes clones através do Azure RM aprovisionamento de controlador de entrega.

4. Não é possível proteger NetScaler utilizando o Azure Site Recovery conforme NetScaler baseia-se no FreeBSD e do Azure Site Recovery não suporta a proteção do SO FreeBSD. Terá de implementar e configurar um novo dispositivo NetScaler do Azure Marketplace após a ativação pós-falha para o Azure.


## <a name="replicating-virtual-machines"></a>Replicar máquinas virtuais

Os seguintes componentes da implementação da Citrix XenApp tem de ser protegidas para ativar a replicação e recuperação.

* Proteção do servidor DNS de AD
* Proteção do servidor de base de dados do SQL Server
* Proteção de controlador de entrega do Citrix
* Proteção do servidor de StoreFront.
* Proteção de XenApp mestre (VDA)
* Proteção do servidor de licenças Citrix XenApp


**Replicação do AD DNS**

Consulte [proteger o Active Directory e DNS com o Azure Site Recovery](site-recovery-active-directory.md) na documentação de orientação para replicar e configurar um controlador de domínio no Azure.

**Replicação de servidor de base de dados do SQL Server**

Consulte [proteger o SQL Server com a recuperação de desastres do SQL Server e o Azure Site Recovery](site-recovery-sql.md) para orientações detalhadas de técnica sobre as opções recomendadas para proteger servidores SQL.

Siga [esta orientação](site-recovery-vmware-to-azure.md) iniciar a replicação as outras máquinas virtuais do componente para o Azure.

![Proteção de XenApp componentes](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**As definições de rede e de computação**

Depois das máquinas estão protegidas (estado é apresentado como "Protegido" em itens replicados), as definições de rede e computação tem de ser configurado.
Além de computação e rede > computação propriedades, pode especificar o tamanho de nome e o destino da VM do Azure.
Altere o nome para cumprir os requisitos do Azure se for necessário. Também pode ver e adicionar informações sobre a rede de destino, a sub-rede e o endereço IP que será atribuído à VM do Azure.

Tenha em atenção o seguinte:

* Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina na ativação pós-falha utilizará o DHCP. Se definir um endereço que não está disponível na ativação pós-falha, a ativação pós-falha não funcionará. O mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste se o endereço está disponível na rede de ativação pós-falha de teste.

* Para o servidor do AD/DNS, manter o endereço no local permite-lhe especificar o mesmo endereço como o servidor DNS para a rede Virtual do Azure.

O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:

*   Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que os de origem.
*   Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
* Por exemplo, se uma máquina de origem tiver duas placas de rede e o tamanho da máquina de destino suportar quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suportar um, a máquina de destino terá apenas um adaptador.
*   Se a máquina virtual tem vários adaptadores de rede, todos serão ligados à mesma rede.
*   Se a máquina virtual tem vários adaptadores de rede, em seguida, o primeiro um mostrado na lista torna-se o adaptador de rede predefinido na máquina virtual do Azure.


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação

Depois da replicação está ativada para as VMs de componente XenApp, o passo seguinte consiste em criar um plano de recuperação.
Uma recuperação Planear grupos máquinas de virtuais em conjunto com os requisitos de semelhantes para ativação pós-falha e recuperação.  

**Passos para criar um plano de recuperação**

1. Adicione as máquinas de virtuais do componente de XenApp no plano de recuperação.
2. Clique em planos de recuperação -> + plano de recuperação. Forneça um nome intuitivo para o plano de recuperação.
3. Máquinas virtuais VMware: selecionar origem como servidor de processos de VMware, destino como o Microsoft Azure e o modelo de implementação Resource Manager e clique em selecionadas itens.
4. Máquinas virtuais do Hyper-V: selecione a origem de servidor do VMM, como o Microsoft Azure e o modelo de implementação do Gestor de recursos de destino e clique em selecionadas itens e, em seguida, selecione a implementação de XenApp VMs.

### <a name="adding-virtual-machines-to-failover-groups"></a>A adicionar máquinas virtuais para grupos de ativação pós-falha

Planos de recuperação podem ser personalizados para adicionar grupos de ativação pós-falha para a sequência de arranque específico, scripts ou ações manuais. Os seguintes grupos tem de ser adicionadas ao plano de recuperação.

1. Group1 de ativação pós-falha: DNS de AD
2. Ativação pós-falha grupo2: VMs SQL Server
2. Ativação pós-falha Group3: VDA mestre de imagem de VM
3. Group4 de ativação pós-falha: Controlador de entrega e StoreFront servidor VMs


### <a name="adding-scripts-to-the-recovery-plan"></a>A adição de scripts para o plano de recuperação

Scripts podem ser executados antes ou depois de um grupo específico, num plano de recuperação. Ações manuais podem ser também de ser incluídos e executadas durante a ativação pós-falha.

O plano de recuperação personalizado é semelhante a abaixo:

1. Group1 de ativação pós-falha: DNS de AD
2. Ativação pós-falha grupo2: VMs SQL Server
3. Ativação pós-falha Group3: VDA mestre de imagem de VM

   >[!NOTE]     
   >Os passos 4, 6 e 7 que contém ações manuais ou de script são aplicáveis a apenas um XenApp no local > ambiente com catálogos MCS/PVS.

4. Ação de Manual ou script grupo 3: encerramento mestre VDA a mestre VDA VM VM quando a ativação pós-falha para o Azure estará num Estado de execução. Para criar novos catálogos MCS através de alojamento de ARM do Azure, é necessária ser parado (Alemanha alocado) a VM principal do VDA estado. Encerre a VM a partir do Portal do Azure.

5. Group4 de ativação pós-falha: Controlador de entrega e StoreFront servidor VMs
6. Group3 manual ou script ação 1:

    ***Adicionar ligação de anfitrião do Azure RM***

    Crie a ligação de anfitrião de ARM do Azure na máquina de controlador de entrega para aprovisionar novos catálogos MCS no Azure. Siga os passos conforme explicado neste [artigo](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Group3 manual ou script ação 2:

    ***Voltar a criar MCS catálogos no Azure***

    Os clones MCS ou PVS existentes no site primário não serão replicadas para o Azure. Será necessário recriá-estes clones utilizando o replicadas VDA principal e o Azure ARM aprovisionamento de controlador de entrega. Siga os passos conforme explicado neste [artigo](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) criar catálogos MCS no Azure.

![Plano de recuperação para os componentes de XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Pode utilizar scripts em [localização](https://github.com/Azure/azure-quickstart-templates/blob/>master/asr-automation-recovery/scripts) atualizar o DNS com o novo IPs da ativação pós-falha > máquinas virtuais ou ligar um balanceador de carga na qual foi através de máquina virtual, se necessário.


## <a name="doing-a-test-failover"></a>Efetuar uma ativação pós-falha de teste

Siga [esta orientação](site-recovery-test-failover-to-azure.md) para efetuar uma ativação pós-falha de teste.

![Plano de recuperação](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Efetuar uma ativação pós-falha

Siga [esta orientação](site-recovery-failover.md) quando estão a fazer uma ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes

Pode [mais](https://aka.ms/citrix-xenapp-xendesktop-with-asr) sobre implementações de Citrix XenApp e XenDesktop neste documento em branco a replicar. Observe as orientações para [replicar outras aplicações](site-recovery-workload.md) utilizando a recuperação de sites.

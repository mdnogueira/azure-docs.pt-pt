---
title: "Replicar uma implementação de Dynamics AX com várias camadas utilizando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como replicar e proteger Dynamics AX através da utilização do Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/24/2017
ms.author: asgang
ms.openlocfilehash: c235102a60b6d11c8b77203121352bd1400f4325
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Replicar uma aplicação com várias camadas do Dynamics AX através da utilização do Azure Site Recovery

## <a name="overview"></a>Descrição geral


 Dynamics AX é uma das soluções ERP mais populares utilizadas por empresas Uniformize os processos entre localizações, gerir recursos e simplificando a conformidade. Porque a aplicação é fundamental para uma organização, em caso de desastre, a aplicação deve estar em execução no tempo mínimo.

Hoje em dia, Dynamics AX não fornece qualquer desastre out of box funcionalidades de recuperação. Dynamics AX é composta por vários componentes de servidor, tais como servidor de objeto de aplicação do Windows, do Azure Active Directory, SQL Database do Azure, SharePoint Server e do Reporting Services. Para gerir desastre a recuperação de cada um destes componentes manualmente não é só dispendiosa, mas também suscetíveis de erros.

Este artigo explica como pode criar uma solução de recuperação após desastre para a sua aplicação Dynamics AX utilizando [do Azure Site Recovery](site-recovery-overview.md). Também inclui as ativações pós-falha de teste planeadas/imprevistas através da utilização de um plano de recuperação de um clique, pré-requisitos e configurações suportadas.

Uma solução de recuperação baseados no Site de recuperação após desastre totalmente é testada, certificada e recomendada pela Dynamics AX.


## <a name="prerequisites"></a>Pré-requisitos

Implementar a recuperação após desastre para aplicações do Dynamics AX utilizando a recuperação de Site requer os seguintes pré-requisitos:

• Configurar uma implementação de Dynamics AX no local.

• Crie um cofre de recuperação de sites numa subscrição do Azure.

• Se a Azure é o site de recuperação, execute a ferramenta de avaliação de preparação de Máquina Virtual do Azure nas VMs. Têm de ser compatíveis com os serviços de recuperação de Site e Virtual Machines do Azure.

## <a name="site-recovery-support"></a>Suporte de recuperação de site

Para fins de criação deste artigo, vamos utilizar máquinas virtuais VMware com Dynamics AX 2012 R3 no Windows Server 2012 R2 Enterprise. Como a replicação de recuperação do site desconhece de aplicação, esperamos as recomendações fornecidas aqui para conter para os seguintes cenários.

### <a name="source-and-target"></a>A origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Ativar a recuperação de desastre da aplicação Dynamics AX utilizando a recuperação de Site
### <a name="protect-your-dynamics-ax-application"></a>Proteger a sua aplicação Dynamics AX
Para ativar a replicação de aplicação completa e a recuperação, cada componente do Dynamics AX têm de ser protegida. 

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Configurar a replicação do Active Directory e DNS

Do Active Directory é necessário no site de recuperação após desastre para a aplicação Dynamics AX para a função. Recomendamos as seguintes opções de dois com base na complexidade do ambiente de no local do cliente.

**Opção 1**

O cliente tem um pequeno número de aplicações e um único controlador de domínio para todo o site no local e planos efetuar a ativação pós-falha de todo o local em conjunto. Recomendamos que utilize replicação de recuperação de sites para replicar a máquina de controlador de domínio para um site secundário (aplicável para cenários de site a site e site para o Azure).

**Opção 2**

O cliente tem um grande número de aplicações e está em execução numa floresta do Active Directory e planos para efetuar a ativação pós-falha algumas aplicações de cada vez. Recomendamos que configure um controlador de domínio adicional no site de recuperação após desastre (um site secundário ou no Azure).

 Para obter mais informações, consulte [disponibilizar um controlador de domínio um site de recuperação após desastre](site-recovery-active-directory.md). Para o resto deste documento, partimos do pressuposto que está disponível um controlador de domínio no site de recuperação de desastre.

### <a name="2-set-up-sql-server-replication"></a>2. Configurar a replicação do SQL Server
Para obter técnica orientações sobre a opção recomendada para proteger a camada SQL, consulte [replicar aplicações com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Ativar a proteção para o cliente de Dynamics AX e VMs de servidor do objeto de aplicação
Efetuar configurações relevantes de recuperação de Site com base em se as VMs implementadas em [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Recomendamos que configure a frequência consistentes com falhas para 15 minutos.
>

O instantâneo seguinte mostra o estado de proteção de VMs de componente de Dynamics num cenário de proteção do site para o Azure VMware.

![Itens protegidos](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configurar o funcionamento em rede
**Processamento da VM e as definições de rede**

Para o cliente de Dynamics AX e VMs de servidor do objeto de aplicação, configure definições de rede na recuperação de Site para que as redes VM obterem ligadas à rede de recuperação após desastre direita após a ativação pós-falha. Certifique-se de que a rede de recuperação de desastres para estas camadas é encaminhável para a camada SQL.

Pode selecionar a VM nos itens replicados para configurar as definições de rede, conforme mostrado no instantâneo seguinte:

* Para os servidores do servidor de objeto de aplicação, selecione o conjunto de disponibilidade correto.

* Se estiver a utilizar um IP estático, especifique o IP que pretende que a VM considerar o **IP de destino** caixa de texto.

    ![Definições de rede ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png).


### <a name="5-create-a-recovery-plan"></a>5. Criar um plano de recuperação

Pode criar um plano de recuperação no Site Recovery para automatizar o processo de ativação pós-falha. Adicione uma camada de aplicação e uma camada web no plano de recuperação. Ordená-los em diferentes grupos para que o front-end será encerrado antes da camada de aplicação.

1. Selecione o Cofre da recuperação de Site na sua subscrição e selecione o **planos de recuperação** mosaico.

2. Selecione **+ plano de recuperação**e especifique um nome.

3. Selecione o **origem** e **destino**. O destino pode ser Azure ou um site secundário. Se escolher do Azure, tem de especificar o modelo de implementação.

    ![Criar plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selecione o servidor de objeto de aplicação e o cliente VMs para o plano de recuperação e selecione o ✓.

    ![Selecionar itens](./media/site-recovery-dynamics-ax/selectvms.png)

    Exemplo de plano de recuperação:

    ![Detalhes do plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplan.png)

Pode personalizar o plano de recuperação para a aplicação Dynamics AX adicionando os seguintes passos. O instantâneo anterior mostra o plano de recuperação concluída depois de adicionar todos os passos.


* **Passos de ativação pós-falha do SQL Server**: para obter informações sobre os passos de recuperação específicos para o SQL server, consulte [aplicações de replicação com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

* **Grupo de ativação pós-falha 1**: efetuar uma ativação pós-falha de VMs de servidor de objeto aplicação.
Certifique-se de que o ponto de recuperação selecionado como fechar quanto possível para a base de dados PIT, mas não à frente das-lo.

* **Script**: adicionar balanceador de carga (apenas I-D).
Adicione um script (através da automatização do Azure) após o grupo de VM de servidor do objeto de aplicação é apresentada para adicionar um balanceador de carga ao mesmo. Pode utilizar um script para executar esta tarefa. Para obter mais informações, consulte [como adicionar um balanceador de carga para a recuperação de desastre de aplicações com múltiplas camadas](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Grupo 2 da ativação pós-falha**: ativação pós-falha de VMs de cliente do Dynamics AX. Ativação pós-falha de VMs de camada web como parte do plano de recuperação.


### <a name="perform-a-test-failover"></a>Execute uma ativação pós-falha de teste

Para mais informações específicas do Active Directory durante a ativação pós-falha de teste, consulte o guia complementar de "Solução de recuperação de desastre do Active Directory". 

Para obter mais informações específicas do SQL Server durante a ativação pós-falha de teste, consulte [replicar aplicações com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

1. Aceda ao portal do Azure e selecione o Cofre de recuperação de sites.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **ativação pós-falha de teste**.

4. Selecione a rede virtual para iniciar o processo de ativação pós-falha de teste.

5. Depois do ambiente secundário cópias de segurança, pode realizar as validações.

6. Depois de validações estiverem concluídas, selecione **validações concluir** e o ambiente de ativação pós-falha de teste é limpa.

Para mais informações sobre como efetuar uma ativação pós-falha de teste, consulte [testar a ativação pós-falha para o Azure Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Execute uma ativação pós-falha

1. Aceda ao portal do Azure e selecione o Cofre de recuperação de sites.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **ativação pós-falha**e selecione **ativação pós-falha**.

4. Selecione a rede de destino e selecione **✓** para iniciar o processo de ativação pós-falha.

Para mais informações sobre como efetuar uma ativação pós-falha, consulte [ativação pós-falha na recuperação de Site](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Efetuar uma reativação pós-falha

Para obter considerações específicas para o SQL Server durante a reativação pós-falha, consulte [replicar aplicações com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

1. Aceda ao portal do Azure e selecione o Cofre de recuperação de sites.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **ativação pós-falha**e selecione **ativação pós-falha**.

4. Selecione **mudar a direção**.

5. Selecione as opções apropriadas: sincronização de dados e a criação de VM.

6. Selecione **✓** para iniciar o processo de reativação pós-falha.


Para mais informações sobre como fazer uma reativação pós-falha, consulte [VMs de VMware reativação pós-falha a partir do Azure para o local](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Resumo
Utilizando a recuperação de Site, pode criar um plano de recuperação após desastre automatizado completa para a sua aplicação Dynamics AX. Em caso de uma interrupção, pode iniciar a ativação pós-falha em segundos a partir de qualquer lugar e obter a aplicação em execução em minutos.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como proteger cargas de trabalho empresariais com a recuperação de Site, consulte [que cargas de trabalho posso proteger?](site-recovery-workload.md).

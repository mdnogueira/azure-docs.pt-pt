---
title: "Configure a recuperação de desastre para o Azure para servidores físicos no local com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação após desastre para o Azure para os servidores de Windows e Linux no local, com o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 805f6946-c6da-491f-980e-bf724bebdf0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: raynew
ms.openlocfilehash: ceb4b13e326b24360799c1a7a25fe48f213fabd7
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Configure a recuperação de desastre para o Azure para servidores físicos no local

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre, gerir e da orquestração de replicação, ativação pós-falha e a reativação pós-falha de máquinas no local e máquinas de virtuais (VMs) do Azure.

Este tutorial mostra como configurar a recuperação após desastre de servidores no local físicos Windows e Linux no Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar os pré-requisitos do Azure e no local
> * Criar um cofre dos serviços de recuperação para a recuperação de sites 
> * Configurar a origem e ambientes de replicação de destino
> * Criar uma política de replicação
> * Ativar a replicação para um servidor

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Certifique-se de que compreende o [arquitetura do cenário e componentes](concepts-physical-to-azure-architecture.md).
- Reveja o [suportar requisitos](site-recovery-support-matrix-to-azure.md) para todos os componentes.
- Certifique-se de que estão em conformidade com os servidores que pretende replicar [requisitos de VM do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Prepare o Azure. Precisa de uma subscrição do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- Prepare uma conta de instalação automática do serviço de mobilidade em cada servidor que pretende replicar.



### <a name="set-up-an-azure-account"></a>Configurar uma conta do Azure

Obter um Microsoft [conta do Azure](http://azure.microsoft.com/).

- Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Saiba mais sobre [preços da recuperação de Site](site-recovery-faq.md#pricing)e obter [detalhes de preços](https://azure.microsoft.com/pricing/details/site-recovery/).
- Saber que [regiões são suportadas](https://azure.microsoft.com/pricing/details/site-recovery/) para a recuperação de Site.

### <a name="verify-azure-account-permissions"></a>Verifique as permissões de conta do Azure

Certifique-se a que sua conta do Azure tem permissões para a replicação de VMs no Azure.

- Reveja o [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) terá de replicar máquinas para o Azure.
- Certifique-se e modificar [acesso baseado em funções](../active-directory/role-based-access-control-configure.md) permissões. 



### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configurar um [rede Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- As VMs do Azure são colocadas nesta rede quando são criados após a ativação pós-falha.
- A rede deve estar na mesma região que o Cofre dos serviços de recuperação


## <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Configurar um [conta do storage do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Recuperação de site são replicados máquinas no local para o armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento após a ocorrência da ativação pós-falha.
- A conta de armazenamento tem de ser na mesma região que o Cofre dos serviços de recuperação.
- A conta de armazenamento pode ser padrão ou [premium](../virtual-machines/windows/premium-storage.md).
- Se configurar uma conta de premium, também terá de uma conta standard adicional para dados de registo.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta de instalação do serviço de mobilidade

O serviço de mobilidade tem de ser instalado em cada servidor que pretende replicar. Recuperação de site instala automaticamente este serviço ao ativar a replicação para o servidor. Para instalar automaticamente, terá de preparar uma conta que a recuperação de sites irá utilizar para aceder ao servidor.

- Pode utilizar um domínio ou conta local
- Para VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso de utilizador remoto no computador local. Para tal, no registo em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor de 1.
- Para adicionar a entrada de registo para desativar a definição de um CLI, escreva:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para Linux, a conta deve ser raiz no servidor de Linux de origem.


## <a name="create-a-vault"></a>Criar um cofre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecione um objetivo de proteção

Selecione o para replicar e para replicar a.

1. Clique em **cofres dos serviços de recuperação** > cofre.
2. No Menu de recursos, clique em **recuperação de Site** > **preparar infraestrutura** > **objetivo de proteção**.
3. No **objetivo de proteção**, selecione **para o Azure** > **não virtualizados/outras**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Configurar o servidor de configuração, registá-lo no cofre e detetar VMs.

1. Clique em **recuperação de sites** > **preparar infraestrutura** > **origem**.
2. Se não tiver um servidor de configuração, clique em **+ o servidor de configuração**.
3. No **Adicionar servidor**, verifique se **servidor de configuração** aparece no **tipo de servidor**.
4. Transfira o ficheiro de instalação de configuração do Unified Site Recovery.
5. Transferir a chave de registo do cofre. Isto tem quando executar a configuração do Unified. A chave é válida durante cinco dias depois de gerá-la.

   ![Configurar a origem](./media/tutorial-physical-to-azure/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registar o servidor de configuração no Cofre

Efetue o seguinte procedimento antes de começar: 

- Na máquina do servidor de configuração, certifique-se de que o relógio do sistema está sincronizado com um [hora Server](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deve corresponder ao. Se estiver à frente 15 minutos ou para trás, a configuração poderá falhar.
- Certifique-se que a máquina pode aceder estes URLs:[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

- Regras de firewall baseadas no endereço IP devem permitir a comunicação com o Azure.
- Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
- Permita os intervalos de endereços IP da região do Azure da sua subscrição e para a região E.U.A. Oeste (utilizada para Controlo de Acesso e Gestão de Identidades).

Execute a configuração do Unified como um administrador Local, para instalar o servidor de configuração. O servidor de processos e o servidor de destino principal também são instalados por predefinição no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após a conclusão de registo, o servidor de configuração é apresentado no **definições** > **servidores** página no cofre.


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e certifique-se de recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implementação de destino.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![destino](./media/tutorial-physical-to-azure/network-storage.png)


## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em **infraestrutura de recuperação de Site** > **políticas de replicação** > **+ política de replicação**.
2. No **criar política de replicação**, especifique um nome de política.
3. No **limiar RPO**, especifique o limite (RPO) da objetivo de ponto de recuperação. Este valor Especifica com que frequência são criados pontos de recuperação de dados. É gerado um alerta se a replicação contínua excede este limite.
4. No **retenção do ponto de recuperação**, especificar quanto tempo (em horas) é a janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto numa janela. Retenção de 24 horas até é suportada em máquinas replicadas para o premium storage e 72 horas para armazenamento standard.
5. No **frequência de instantâneos consistentes com aplicação**, especifique com que frequência (em minutos) que contêm os instantâneos consistentes com aplicações de pontos de recuperação serão criados. Clique em **OK** para criar uma política.

    ![Política de replicação](./media/tutorial-physical-to-azure/replication-policy.png)


A política é automaticamente associada ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação é **rep política** , em seguida, uma política de reativação pós-falha **rep-política-reativação pós-falha** é criado. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

## <a name="enable-replication"></a>Ativar a replicação

Ative a replicação para cada servidor.

- Recuperação de sites irá instalar o serviço de mobilidade quando a replicação está ativada.
- Quando ativar a replicação para um servidor, pode demorar 15 minutos ou mais para que as alterações entrem em vigor e aparecer no portal.

1. Clique em **replicar aplicação** > **origem**.
2. No **origem**, selecione o servidor de configuração.
3. No **máquina tipo**, selecione **máquinas físicas**.
4. Selecione o servidor de processos (o servidor de configuração). Em seguida, clique em **OK**.
5. No **destino**, selecione a subscrição e o grupo de recursos no qual pretende criar as VMs do Azure após a ativação pós-falha. Escolha o modelo de implementação que pretende utilizar no Azure (clássica ou recurso management).
6. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. 
9. No **máquinas físicas**e clique em **+ máquina física**. Especifique o nome e endereço IP. Selecione o sistema operativo da máquina que pretende replicar. Demora alguns minutos para que os servidores a ser detetados e listados. 
10. No **propriedades** > **configurar propriedades**, selecione a conta que será utilizada pelo servidor de processos para instalar automaticamente o serviço de mobilidade na máquina.
11. No **as definições de replicação** > **configurar as definições de replicação**, certifique-se de que a política de replicação correto está selecionada. 
12. Clique em **ativar a replicação**. Pode controlar o progresso do **ativar proteção** da tarefa no **definições** > **tarefas** > **tarefas de recuperação de Site**. Depois da tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.


Para monitorizar os servidores que adiciona, pode verificar a última vez detetada na **servidores de configuração** > **último contacto em**. Ao adicionar computadores sem aguardar por um período de tempo de deteção agendada, realce o servidor de configuração (não clique nele) e clique em **atualizar**.

## <a name="next-steps"></a>Passos seguintes

[Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)

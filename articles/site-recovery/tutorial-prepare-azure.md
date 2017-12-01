---
title: "Criar recursos para utilização com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como preparar o Azure para a replicação das máquinas no local utilizando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 321e304f-b29e-49e4-aa64-453878490ea7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2fa7e731a05e19697603058829f130074bb5b522
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Preparar os recursos no Azure para a replicação de máquinas locais

O [Azure Site Recovery](site-recovery-overview.md) serviço que contribui para a sua estratégia de recuperação (BCDR) de continuidade negócio e desastre ao manter as suas aplicações empresariais uma cópia de segurança em execução, disponíveis durante a falhas não planeadas e planeadas. A recuperação de site gere e orquestra a recuperação de desastre de máquinas no locais e máquinas virtuais no Azure (VMs), incluindo a replicação, ativação pós-falha e recuperação.

Este tutorial mostra como preparar os componentes do Azure quando pretende replicar VMs locais e servidores físicos para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Certificar de que a sua conta possui permissões de replicação
> * Criar uma conta de armazenamento no Azure.
> * Criar uma rede no Azure. Quando as VMs no Azure são criadas após a ativação pós-falha, estas estão associados a esta rede no Azure.

Se não tiver uma subscrição no Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar a sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="verify-account-permissions"></a>Verifique as permissões de conta

Se acabou de criar a conta gratuita no Azure é o administrador da sua subscrição. Se não for o administrador da subscrição, trabalhe com o administrador, para atribuir as permissões que precisa. Para ativar a replicação para uma nova máquina virtual, tem de ter:

- Permissão para criar uma máquina virtual no grupo de recursos selecionado
- Permissão para criar uma máquina virtual na rede virtual selecionada
- Permissão para escrever na conta de armazenamento selecionada

A função incorporada 'Contribuinte de Máquina Virtual' com estas permissões. Também necessita de permissão para gerir as operações do Azure Site Recovery. A função 'Contribuinte de recuperação do Site' tem todas as permissões necessárias para gerir as operações de recuperação de Site num cofre dos serviços de recuperação.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Imagens de máquinas replicadas são guardadas no armazenamento no Azure. As VMs do Azure são criadas no armazenamento, quando a ativação pós-falha no local para o Azure.

1. No [portal do Azure](https://portal.azure.com) menu, clique em **novo** -> **armazenamento** -> **conta de armazenamento**.
2. Introduza um nome para a conta do armazenamento. Para estes tutoriais utilizamos o nome **contosovmsacct1910171607**. O nome tem de ser exclusivo no Azure e de ter entre 3 e 24 carateres, com números e apenas letras minúsculas.
3. Utilize o **Resource Manager** modelo de implementação.
4. Selecione **fins gerais** > **padrão**.
5. Selecionar a predefinição **RA-GRS** para a redundância de armazenamento.
6. Selecione a subscrição na qual pretende criar a nova conta do armazenamento.
7. Especifique um novo grupo de recursos. Um grupo de recursos no Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Para este tutorial utilizamos o nome **ContosoRG**.
8. Selecione a localização geográfica para a conta do armazenamento. A conta de armazenamento tem de ser na mesma região que o Cofre dos serviços de recuperação. Para estes tutorial utilizamos a localização **Europa Ocidental**.

   ![storageacct criar](media/tutorial-prepare-azure/create-storageacct.png)

9. Clique em **Criar** para criar a conta do Storage.

## <a name="create-a-vault"></a>Criar um cofre

1. No menu do portal do Azure, clique em **novo** > **de monitorização e gestão** >
    **cópia de segurança e recuperação de Site**.
2. Em **Nome**, especifique um nome amigável para identificar o cofre. Para este tutorial utilizamos **ContosoVMVault**.
3. Selecione o grupo de recursos existente com o nome **contosoRG**.
4. Especificar a região do Azure **Europa Ocidental**.
5. Para aceder rapidamente ao Cofre a partir do dashboard, clique em **afixar ao dashboard** > **criar**.

   ![Novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre é apresentado no **Dashboard** > **todos os recursos**e no principal **cofres dos serviços de recuperação** página.

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs no Azure são criadas a partir de armazenamento após a ativação pós-falha, irão estar a associadas a esta rede.

1. No [portal do Azure](https://portal.azure.com) menu, clique em **novo** > **redes** >
    **rede Virtual**
2. Deixe **Resource Manager** selecionado como o modelo de implementação. Gestor de recursos é o modelo de implementação preferenciais.
   - Especifique o nome da rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Iremos utilizar o nome **ContosoASRnet**
   - Utilize o grupo de recursos existente **contosoRG**.
   - Especifique o intervalo endereço de rede 10.0.0.0/24.
   - Para este tutorial, não precisa de uma sub-rede.
   - Selecione a subscrição na qual pretende criar a rede.
   - Selecione a localização **Europa Ocidental**. A rede tem de estar na mesma região que o cofre de Serviços de Recuperação.
3. Clique em **Criar**.

   ![criar a rede](media/tutorial-prepare-azure/create-network.png)

   A rede virtual demora alguns segundos a criar. Depois de criada, ira vê-la no dashboard do portal do Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Preparar a infraestrutura do VMware no local para a recuperação após desastre para o Azure](tutorial-prepare-on-premises-vmware.md)

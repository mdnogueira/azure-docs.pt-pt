---
title: "Configurar as definições de replicação do Azure Site Recovery | Microsoft Docs"
description: "Descreve como implementar o Site Recovery para orquestrar a replicação, a ativação pós-falha e a recuperação de VMs de Hyper-V em clouds do VMM para o Azure."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2017
ms.author: sutalasi
ms.openlocfilehash: 73a1f19177f23441f5f7165cf2bc92ba85e62aa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Gerir a política de replicação do VMware para o Azure


## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Selecione **Gerir** > **Infraestrutura do Site Recovery**.
2. Selecione **Políticas de replicação** em **Para VMware e Máquinas físicas**.
3. Selecione **+Política de replicação**.

    ![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Introduza o nome da política.

5. No **limiar RPO**, especifique o limite RPO. Serão gerados alertas quando a replicação contínua exceder este limite.
6. Na **retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As máquinas protegidas podem ser recuperadas para qualquer ponto numa janela de retenção.

    > [!NOTE]
    > É suportada até 24 horas de retenção para máquinas replicadas para o armazenamento premium. É suportada até 72 horas de retenção para máquinas replicadas para o armazenamento standard.

    > [!NOTE]
    > É criada automaticamente uma política de replicação para reativação pós-falha.

7. Em **Frequência de instantâneos consistentes com a aplicação**, especifique a frequência (em minutos) com que os pontos de recuperação que contêm os instantâneos consistentes com aplicações serão criados.

8. Clique em **OK**. A política deve ser criada em 30 segundos a 60 segundos.

![Geração da política de replicação](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>Associe um servidor de configuração a uma política de replicação
1. Escolha a política de replicação à qual quer associar o servidor de configuração.
2. Clique em **Associar**.
![Associar servidor de configuração](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Selecione o servidor de configuração' na lista de servidores.
4. Clique em **OK**. O servidor de configuração deve ser associado em um ou dois minutos.

![Associação do servidor de configuração](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>Editar uma política de replicação
1. Escolha a política de replicação para a quer editar as definições de replicação.
![Editar a política de replicação](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Clique em **Editar Definições**.
![Editar definições da política de replicação](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Altere as definições com base nas suas necessidades.
4. Clique em **Guardar**. A política deverá ser guardada em dois a cinco minutos, consoante o número de VMs que estão a utilizar essa política de replicação.

![Guardar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>Desassocie um servidor de configuração de uma política de replicação
1. Escolha a política de replicação à qual quer associar o servidor de configuração.
2. Clique em **Desassociar**.
3. Selecione o servidor de configuração' na lista de servidores.
4. Clique em **OK**. O servidor de configuração deve ser desassociado em um ou dois minutos.

    > [!NOTE]
    > Não é possível desassociar um servidor de configuração se existir, pelo menos, um item replicado a utilizar a política. Certifique-se de que não existem itens replicados a utilizar a política antes de desassociar o servidor de configuração.

## <a name="delete-a-replication-policy"></a>Eliminar uma política de replicação

1. Escolha a política de replicação que quer eliminar.
2. Clique em **Eliminar**. A política deve ser eliminada em 30 segundos a 60 segundos.

    > [!NOTE]
    > Não é possível eliminar uma política de replicação se esta tiver, pelo menos, um servidor de configuração associado. Certifique-se de que não existem itens replicados a utilizar a política e elimine todos os servidores de configuração associados antes de eliminar a política.

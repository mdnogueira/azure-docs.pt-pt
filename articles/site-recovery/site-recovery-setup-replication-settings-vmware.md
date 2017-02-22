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
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Gerir a política de replicação do VMware para o Azure


## <a name="create-a-new-replication-policy"></a>Criar uma nova política de replicação

1. Clique em ''Gerir -> Infraestrutura do Site Recovery'' no menu do lado esquerdo. 
2. Selecione ''Políticas de replicação'' na secção ''VMware e Máquinas físicas''.
3. Clique em ''+Política de replicação'' na parte superior.

    ![Criar a política de replicação](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Introduza o nome da política.

5. No limiar RPO, especifique o limite RPO. Serão gerados alertas quando a replicação contínua exceder este limite.
6. Em Retenção do ponto de recuperação, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. As máquinas protegidas podem ser recuperadas para qualquer ponto nessa janela. 

    > [!NOTE] 
    > É suportada uma retenção até 24 horas para máquinas replicadas para o armazenamento premium e 72 horas para máquinas replicadas para o armazenamento standard.
    
    > [!NOTE] 
    > Será criada automaticamente uma política de replicação para reativação pós-falha.

7. Em Frequência de instantâneos consistentes com a aplicação, especifique a frequência (em minutos) com que os pontos de recuperação que contêm os instantâneos consistentes com aplicações serão criados.

8. Clique em ''OK''. A política deve ser criada em cerca de 30 segundos a 1 minuto.

![Criar a política de replicação](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>Associar o servidor de configuração à política de replicação
1. Clique na política de replicação à qual quer associar o servidor de configuração.
2. Clique em "Associar" na parte superior.
![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Selecione ''Servidor de configuração'' na lista de servidores.
4. Clique em OK. O servidor de configuração deve ser associado em cerca de 1 a 2 minutos.

![Criar a política de replicação](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>Editar a política de replicação
1. Clique na política de replicação para a quer editar as definições de replicação.
![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Clique em ''Editar Definições'' na parte superior.
![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Altere as definições com base nas suas necessidades.
4. Clique em ''Guardar'' na parte superior. A política deverá ser guardada em cerca de 2 a 5 minutos consoante o número de VMs que estão a utilizar essa política de replicação.

![Criar a política de replicação](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>Desassociar o servidor de configuração da política de replicação
1. Clique na política de replicação à qual quer associar o servidor de configuração.
2. Clique em "Desassociar" na parte superior.
3. Selecione ''Servidor de configuração'' na lista de servidores.
4. Clique em OK. O servidor de configuração deve ser desassociado em cerca de 1 a 2 minutos.
    
    > [!NOTE] 
    > Não é possível desassociar um servidor de configuração se existir, pelo menos, um item replicado a utilizar a política. Certifique-se de que não existem itens replicados a utilizar a política antes de desassociar o servidor de configuração.

## <a name="delete-replication-policy"></a>Eliminar a política de replicação 

1. Clique na política de replicação que quer eliminar.
2. Clique em Eliminar. A política deve ser eliminada em cerca de 30 segundos a 1 minuto.

    > [!NOTE] 
    > Não é possível eliminar uma política de replicação se esta tiver, pelo menos, um servidor de configuração associado. Certifique-se de que não existem itens replicados a utilizar a política e elimine todos os servidores de configuração associados antes de eliminar a política.


<!--HONumber=Jan17_HO4-->



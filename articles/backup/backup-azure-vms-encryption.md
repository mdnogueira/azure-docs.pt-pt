---
title: "Cópia de segurança e restaurar VMs encriptadas utilizando a cópia de segurança do Azure"
description: "Este artigo aborda a experiência de cópia de segurança e restauro para VMs encriptados utilizando a Azure Disk Encryption."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2425523dacd9a0e1e078ec8cd082ac40534d25a
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Cópia de segurança e restaurar encriptadas máquinas virtuais com o Backup do Azure
Este artigo aborda os passos para criar cópias de segurança e restaurar máquinas virtuais (VMs) com o Backup do Azure. Também fornece detalhes sobre cenários suportados, pré-requisitos e passos de resolução de problemas de casos de erro.

## <a name="supported-scenarios"></a>Cenários suportados

 * Cópia de segurança e restauro de VMs encriptadas só é suportada para VMs que utilizam o modelo de implementação Azure Resource Manager. Não é suportada para VMs que utilizam o modelo de implementação clássica. <br>
 * Cópia de segurança e restauro de VMs encriptados é suportada para o Windows e as VMs do Linux que utilizam o Azure Disk Encryption. Encriptação de disco utiliza a funcionalidade do BitLocker de padrão da indústria do Windows e a funcionalidade de dm-crypt do Linux para fornecer a encriptação dos discos. <br>
 
 A tabela seguinte mostra os cenários suportados para a chave de encriptação do BitLocker (BEK) - chave de encriptação apenas e chaves (KEK) - encriptados VMs:
 
 
   |  | BEK + KEK VMs | VMs só BEK |
   | --- | --- | --- |
   | **VMs em**  | Sim | Sim  |
   | **VMs gerido**  | Sim | Sim  |

## <a name="prerequisites"></a>Pré-requisitos
* A VM foi encriptada utilizando [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Um cofre dos serviços de recuperação foi criado e a replicação de armazenamento foi definida por seguir os passos em [preparar o ambiente para cópia de segurança](backup-azure-arm-vms-prepare.md).

* Cópia de segurança fornecida [permissões para aceder a um cofre de chaves](#provide-permissions-to-azure-backup) que contém chaves e segredos para encriptados VMs.

## <a name="backup-encrypted-vm"></a>VM de cópia de segurança encriptada
Utilize os seguintes passos para definir um objetivo de cópia de segurança, definir uma política, configurar os itens e acione uma cópia de segurança.

### <a name="configure-backup"></a>Configurar a cópia de segurança
1. Se já tiver um cofre dos serviços de recuperação, abrir, avance para o passo seguinte. Se não tiver um cofre dos serviços de recuperação, abrir, mas está no portal do Azure, no **Hub** menu, selecione **procurar**.

   a. Na lista de recursos, escreva **Serviços de Recuperação**.

   b. À medida que começa a escrever, a lista filtra com base na sua entrada. Quando vir **cofres dos serviços de recuperação**, selecione-o.

      ![Cofre dos Serviços de Recuperação](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. É apresentada a lista dos cofres dos Serviços de Recuperação. Selecione um cofre da lista.

     O dashboard do cofre selecionado é aberto.
2. Na lista de itens que é apresentado o cofre, selecione **cópia de segurança** para iniciar a cópia de segurança a VM encriptada.

      ![Painel de cópia de segurança](./media/backup-azure-vms-encryption/select-backup.png)
3. No **cópia de segurança** mosaico, selecione **objetivo de cópia de segurança**.

      ![Painel cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. Em **onde está a carga de trabalho em execução?**, selecione **Azure**. Em **que itens pretende cópia de segurança?**, selecione **Máquina Virtual**. Em seguida, selecione **OK**.

   ![Abrir o painel Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. Em **escolher a política de cópia de segurança**, selecione a política de cópia de segurança que pretende aplicar ao cofre. Em seguida, selecione **OK**.

      ![Selecionar política de cópia de segurança](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Os detalhes da política predefinida são listados. Se pretender criar uma política, selecione **criar novo** na lista pendente. Depois de selecionar **OK**, a política de cópia de segurança é associada ao cofre.

6. Escolha as VMs encriptadas para associar à política especificada e selecione **OK**.

      ![Selecione VMs encriptadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Esta página mostra uma mensagem sobre cofres de chaves associadas às VMs encriptadas que selecionou. Cópia de segurança requer acesso só de leitura para as chaves e segredos no Cofre de chaves. Utiliza estas permissões para criar cópias de segurança de chaves e segredos, juntamente com as VMs associados. *Tem de fornecer permissões para o serviço de cópia de segurança para aceder ao Cofre de chaves para cópias de segurança funcionam*. Pode fornecer estas permissões ao seguir o [passos mencionados na secção seguinte](#provide-permissions-to-azure-backup).

      ![Mensagem de VMs encriptada](./media/backup-azure-vms-encryption/encrypted-vm-warning-message.png)

      Agora que definiu todas as definições para o cofre, selecione **ativar Backup** na parte inferior da página. **Ativar a cópia de segurança** implementa a política para o Cofre e as VMs.
8. A fase seguinte em preparação está a instalar o agente da VM ou certificar-se de que o agente VM está instalado. Para o fazer, siga os passos no [preparar o ambiente para cópia de segurança](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Acionar uma tarefa de cópia de segurança
Siga os passos no [cópia de segurança VMs do Azure para um cofre dos serviços de recuperação](backup-azure-arm-vms.md) para acionar uma tarefa de cópia de segurança.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuar cópias de segurança de VMs de cópia de segurança já com encriptação ativada  
Se tiver VMs já a ser efetuadas num cofre dos serviços de recuperação que estão ativadas para encriptação mais tarde, deve fornecer permissões para cópia de segurança para aceder ao Cofre de chaves para cópias de segurança continuar. Pode fornecer estas permissões ao seguir o [os passos na secção seguinte](#provide-permissions-to-azure-backup). Ou, pode seguir os passos de PowerShell na secção "Permitir cópia de segurança" o [PowerShell documentação](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Fornecer permissões para cópia de segurança
Utilize os seguintes passos para fornecer permissões relevantes à cópia de segurança para o Cofre de chaves de acesso e efetuar cópia de segurança de VMs encriptadas.
1. Selecione **mais serviços**e procure **cofres de chaves**.

    ![Cofres de chaves](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Na lista de cofres de chaves, selecione o Cofre de chaves associado a VM encriptada que precisa de fazer cópias de segurança.

     ![Seleção do Cofre de chaves](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Selecione **políticas de acesso**e, em seguida, selecione **adicionar novo**.

    ![Adicionar novos](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Selecione **principal selecione**e, em seguida, escreva **serviço de gestão de cópia de segurança** na caixa de pesquisa. 

    ![Pesquisa de serviço de cópia de segurança](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Selecione **serviço de gestão de cópia de segurança**e, em seguida, selecione **selecione**.

    ![Seleção de serviço de cópia de segurança](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. Em **configurar a partir do modelo (opcional)**, selecione **cópia de segurança do Azure**. As permissões necessárias são prefilled para **permissões da chave** e **permissões secretas**. Se a VM é encriptada utilizando **BEK apenas**, apenas para segredos são necessárias permissões de, pelo que tem de remover a seleção das **permissões da chave**.

    ![Seleção de cópia de segurança do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Selecione **OK**. Repare que **serviço de gestão de cópia de segurança** obtém adicionado no **políticas de acesso**. 

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Selecione **guardar** para conceder as permissões necessárias para cópia de segurança.

    ![Política de cópia de segurança de acesso](./media/backup-azure-vms-encryption/save-access-policy.png)

Depois de permissões são fornecidas com êxito, pode avançar com a ativação da cópia de segurança para VMs encriptadas.

## <a name="restore-an-encrypted-vm"></a>Restaurar uma VM encriptada
Para restaurar uma VM encriptada, restaure primeiro discos seguindo os passos na secção "Restaurar os discos de cópia de segurança" [escolha uma configuração de restauro de VM](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Depois disso, pode utilizar uma das seguintes opções:

* Siga os passos do PowerShell em [criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar uma VM completa dos discos restaurados.
* Ou, [utilizar modelos para personalizar uma VM restaurada](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) criar VMs de discos restaurados. Modelos podem ser utilizados apenas para pontos de recuperação criados após 26 de Abril de 2017.

## <a name="troubleshooting-errors"></a>Resolução de problemas de erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
|Cópia de segurança | Cópia de segurança não tem permissões suficientes para o Cofre de chaves para cópia de segurança de VMs encriptadas. | Cópia de segurança deve ser fornecida estas permissões ao seguir o [os passos na secção anterior](#provide-permissions-to-azure-backup). Ou, pode seguir os passos de PowerShell na secção de documentação do PowerShell em "Ativar a proteção" [AzureRM.RecoveryServices.Backup de utilização de cmdlets para fazer uma cópia de segurança de máquinas virtuais](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Restauro |Não é possível restaurar esta VM encriptada porque não existe o Cofre de chaves associado esta VM. |Criar um cofre de chaves utilizando [introdução ao Cofre de chaves do Azure](../key-vault/key-vault-get-started.md). Consulte [restaurar uma chave de Cofre de chaves e um segredo utilizando cópia de segurança do Azure](backup-azure-restore-key-secret.md) para restaurar uma chave e um segredo se não estão presentes. |
| Restauro |Não é possível restaurar esta VM encriptado, porque a chave e o segredo associados a esta VM não existem. |Consulte [restaurar uma chave de Cofre de chaves e um segredo utilizando cópia de segurança do Azure](backup-azure-restore-key-secret.md) para restaurar uma chave e um segredo se não estão presentes. |
| Restauro |Cópia de segurança não tem autorização para aceder aos recursos na sua subscrição. |Restaurar, tal como mencionado anteriormente, discos primeiro ao seguir os passos na secção "Restaurar os discos de cópia de segurança" [escolha uma configuração de restauro de VM](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Depois disso, utilize o PowerShell para [criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |

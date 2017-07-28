
---
title: FAQ sobre o Azure VM Backup | Microsoft Docs
description: "Respostas a perguntas comuns sobre como funcionam as cópias de segurança de VMs do Azure, as limitações e o que ocorre quando há alterações a políticas"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "cópia de segurança de vm do azure, restauro de vm do azure, política de cópias de segurança"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 8c06a90f58cf56ebb4e75e7567e237de7414a300
ms.contentlocale: pt-pt
ms.lasthandoff: 07/20/2017

---
# <a name="questions-about-the-azure-vm-backup-service"></a>Perguntas sobre o serviço Azure VM Backup
Este artigo tem respostas a perguntas comuns para o ajudar a compreender rapidamente os componentes do Azure VM Backup. Em algumas das respostas, existem ligações para os artigos que incluem informação abrangente. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurar a cópia de segurança
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Os cofres dos Serviços de Recuperação suportam VMs clássicas ou VMs no Resource Manager? <br/>
Os cofres dos Serviços de Recuperação suportam ambos os modelos.  Pode criar uma cópia de segurança de uma VM clássica (criada no Portal clássico) ou de uma VM do Resource Manager (criada no portal do Azure) num cofre dos Serviços de Recuperação.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup-"></a>Que configurações não são suportadas pelo Azure VM Backup?
Leia [Supported operating systems](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup) (Sistemas operativos suportados) e [Limitations of VM backup](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm) (Limitações das cópias de segurança de VMs)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Por que motivo não vejo a minha VM no assistente de configuração de cópias de segurança?
No Assistente de configuração de cópias de segurança, o Azure Backup só apresenta VMs que:
* Ainda não estejam protegidas. Pode verificar o estado da cópia de segurança ao aceder ao painel da VM e ver o estado Cópia de Segurança, no Menu de Definições do painel. Saiba mais sobre como [Verificar o estado da cópia de segurança de uma VM](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)
* Pertençam à mesma região que a VM

## <a name="backup"></a>Cópia de segurança
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>As tarefas de cópias de segurança a pedido seguem a mesma agenda de retenção que as cópias de segurança agendadas?
Não. Tem de especificar o período de retenção para as tarefas de cópias de segurança a pedido. Por predefinição, será retida durante 30 dias, quando acionada a partir do portal. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ativei recentemente o Azure Disk Encryption em algumas VMs. As minhas cópias de segurança vão continuar a funcionar?
Tem de dar permissões ao serviço Azure Backup para aceder ao Key Vault. Pode conceder estas permissões no PowerShell com os passos mencionados na secção *Enable Backup* (Ativar Cópia de Segurança) da documentação do [PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Migrei discos de uma VM para discos geridos. As minhas cópias de segurança vão continuar a funcionar?
Sim, as cópias de segurança funcionam sem problemas e não é necessário reconfigurá-las. 

## <a name="restore"></a>Restauro
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Como posso decidir entre o restauro de discos e o restauro de VMs completo?
Pense no restauro de VMs completo do Azure como uma opção de criação rápida para VMs restauradas. A opção Restaurar VM altera os nomes dos discos, os contentores utilizados pelos discos, os endereços IP públicos, os nomes das interfaces de rede para que sejam criados recursos exclusivos como parte da criação de VMs. Também não adiciona as VMs a conjuntos de disponibilidade. 

Utilize os discos de restauro para:
* Personalizar as VMs que são criadas a partir da configuração de ponto no tempo, como, por exemplo, alterar o tamanho da configuração da cópia de segurança.
* Adicionar configurações que não estão presentes no momento da cópia de segurança 
* Controlar a convenção de nomenclatura para os recursos que são criados
* Adicionar a VM ao conjunto de disponibilidade
* Se tiver uma configuração que só pode ser obtida com o PowerShell ou uma definição de modelo declarativo.

## <a name="manage-vm-backups"></a>Gerir cópias de segurança de VMs
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>O que acontece quando altero uma política de cópias de segurança numa ou em várias VMs?
Quando é aplicada uma política nova a uma ou mais VMs, a agenda e a retenção da política nova são seguidas. Se a retenção for estendida, os pontos de recuperação existentes serão marcados, para que estejam em conformidade com a política nova. Se for reduzida, são marcados para eliminação na tarefa de limpeza seguinte. 


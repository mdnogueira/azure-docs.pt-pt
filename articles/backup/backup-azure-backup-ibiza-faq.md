---
title: "FAQ do cofre dos Serviços de Recuperação | Microsoft Docs"
description: "Esta versão de FAQ suporta o lançamento da Pré-visualização Pública do serviço do Backup do Azure. Respostas a perguntas mais frequentes sobre o agente da cópia de segurança, a cópia de segurança e a retenção, recuperação, segurança e outras perguntas comuns sobre a solução do Backup do Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: jwhit
editor: 
keywords: "solução de cópia de segurança; serviço de cópia de segurança"
ms.assetid: 5f55b500-1ee9-4f64-9306-02d6f7a8eded
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: trinadhk; markgal; jimpark;
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fdc596def57968381ea48dfc5df2269a682c9de7


---
# <a name="recovery-services-vault-faq"></a>Cofre dos Serviços de Recuperação – FAQ
Este artigo indica informações específicas do cofre dos Serviços de Recuperação e complementa o [FAQ do Azure Backup](backup-azure-backup-faq.md). As FAQ sobre o Backup do Azure fornecem o conjunto completo de perguntas e respostas sobre o serviço de Backup do Azure.  

Pode colocar perguntas sobre o Backup do Azure na secção Disqus deste artigo ou um artigo relacionado. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Os cofres dos Serviços de Recuperação baseiam-se no Resource Manager. Os cofres do Backup (modo clássico) ainda são suportados? <br/>
Sim, os cofres da Cópia de Segurança ainda são suportados. Crie cofres da Cópia de Segurança no [Portal Clássico](https://manage.windowsazure.com). Crie cofres dos Serviços de Recuperação no [portal do Azure](https://portal.azure.com). No entanto, recomendamos vivamente que crie o cofre dos serviços de recuperação, dado que todas as futuras melhorias estarão disponíveis apenas através do cofre dos Serviços de Recuperação.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Posso migrar um cofre da Cópia de Segurança para um cofre dos Serviços de Recuperação? <br/>
Infelizmente não, neste momento não é possível migrar os conteúdos de um cofre da Cópia de Segurança para um cofre dos Serviços de Recuperação. Estamos a tentar adicionar esta funcionalidade, mas não está disponível como parte da Pré-visualização Pública.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Os cofres dos Serviços de Recuperação suportam VMs clássicas ou VMs no Resource Manager? <br/>
Os cofres dos Serviços de Recuperação suportam ambos os modelos.  Pode efetuar uma cópia de segurança de uma VM criada no portal Clássico (que são VMs em modo clássico) ou de uma VM criada no portal do Azure (que se baseiam no Resource Manager) para um cofre dos Serviços de Recuperação.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault"></a>Efetuei cópia de segurança das minhas VMs clássicas no cofre de cópia de segurança. Agora, quero migrar as minhas VMs do modo clássico para o modo do Resource Manager.  Como posso efetuar uma cópia de segurança das mesmas no cofre dos serviços de recuperação?
As cópias de segurança de VMs clássicas no cofre de cópia de segurança não são automaticamente migradas para o cofre dos serviços de recuperação quando migrar as VMs do modo clássico para o modo do Resource Manager. Siga estes passos para migrar as cópias de segurança de VMs:

1. No cofre de cópia de segurança, aceda ao separador **Itens Protegidos** e selecione a VM. Clique em [Parar Proteção](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deixe a opção *Eliminar dados de cópia de segurança associados* **desmarcada**.
2. Migre a máquina virtual do modo clássico para o modo do Resource Manager. Certifique-se de que o armazenamento e a rede correspondentes à máquina virtual também são migrados para o modo do Resource Manager.
3. Crie um cofre dos serviços de recuperação e configure a cópia de segurança na máquina virtual migrada utilizando a ação **Cópia de Segurança** no dashboard do cofre. Saiba mais sobre como [ativar a cópia de segurança no cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md)




<!--HONumber=Nov16_HO2-->



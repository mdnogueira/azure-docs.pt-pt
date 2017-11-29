---
title: "Descrição geral dos serviços de recuperação cofres dos | Microsoft Docs"
description: "Uma descrição geral e uma comparação entre os cofres dos serviços de recuperação e cofres de cópia de segurança do Azure."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/15/2017
ms.author: markgal;arunak;sogup
ms.openlocfilehash: ad685744595ab86def8d226d7c2fb5455af98da4
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="recovery-services-vaults-overview"></a>Descrição geral de cofres de serviços de recuperação

Este artigo descreve as funcionalidades de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é uma entidade de armazenamento no Azure que aloja os dados. Os dados são normalmente cópias dos dados ou informações de configuração para máquinas virtuais (VMs), cargas de trabalho, servidores ou estações de trabalho. Pode utilizar os cofres dos serviços de recuperação para armazenar dados de cópia de segurança para vários serviços do Azure, tais como VMs do IaaS (Linux ou Windows) e bases de dados SQL do Azure. Sistema de suporte de cofres de serviços de recuperação System Center DPM, Windows Server, servidor de cópia de segurança do Azure e muito mais. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão. 

Dentro de uma subscrição do Azure, pode criar até 25 cofres dos serviços de recuperação por região.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Cofres dos serviços de recuperação compara e cofres de cópia de segurança dos

Se ainda tiver cofres de cópia de segurança, estes estão a ser atualizadas automaticamente para os cofres dos serviços de recuperação. Até Novembro de 2017, todos os cofres de cópia de segurança foram atualizados para os cofres dos serviços de recuperação. 

Os cofres dos serviços de recuperação baseiam-se no modelo Azure Resource Manager do Azure, enquanto cofres de cópia de segurança foram baseadas no modelo de Gestor do serviço do Azure. Ao atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação, os dados de cópia de segurança permanecem intactos durante e após o processo de atualização. Os cofres dos serviços de recuperação fornecem funcionalidades não está disponíveis para cofres de cópia de segurança, tais como:

- **Capacidades para ajudar a proteger os dados da cópia de segurança melhoradas**: cofres dos serviços de recuperação com, cópia de segurança do Azure fornece capacidades de segurança para proteger cópias de segurança de nuvem. As funcionalidades de segurança Certifique-se pode proteger as cópias de segurança e recuperar em segurança dados, mesmo se os servidores de produção e de cópia de segurança sejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitorização central para o ambiente de TI de híbrida**: cofres dos serviços de recuperação com, pode monitorizar não apenas o [VMs IaaS do Azure](backup-azure-manage-vms.md) , mas também o [recursos no local](backup-azure-manage-windows-server.md#manage-backup-items) de um portal central. [Saiba mais](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Controlo de acesso baseado em funções (RBAC)**: RBAC fornece controlo de gestão de acesso detalhado no Azure. [O Azure oferece várias funções incorporadas](../active-directory/role-based-access-built-in-roles.md), e cópia de segurança do Azure tem três [funções incorporadas para gerir pontos de recuperação](backup-rbac-rs-vault.md). Os cofres dos serviços de recuperação são compatíveis com o RBAC, que restringe a cópia de segurança e restaurar o acesso ao conjunto definido de funções de utilizador. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteger todas as configurações de Virtual Machines do Azure**: cofres dos serviços de recuperação protegem VMs baseadas no Resource Manager, incluindo discos Premium, discos geridos e VMs encriptados. Atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação dá-lhe a oportunidade para atualizar as VMs baseadas no Service Manager para as VMs baseadas no Resource Manager. Ao atualizar o cofre, pode manter os pontos de recuperação VM baseadas no Service Manager e configure a proteção para VMs atualizadas (ativado Resource Manager). [Saiba mais](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauro instantânea para as VMs de IaaS**: cofres dos serviços de recuperação a utilizar, pode restaurar ficheiros e pastas a partir de uma VM do IaaS sem restaurar toda a VM, que permite que os tempos de restauro mais rápidos. Restauro instantânea para as VMs de IaaS está disponível para o Windows e VMs com Linux. [Saiba mais](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Gerir os cofres dos serviços de recuperação no portal
Criação e gestão de cofres dos serviços de recuperação no portal do Azure é fácil porque o serviço de cópia de segurança está integrado no menu de definições do Azure. Esta integração significa que pode criar ou gerir um cofre dos serviços de recuperação *no contexto do serviço de destino*. Por exemplo, para ver os pontos de recuperação para uma VM, selecione-o e clique em **cópia de segurança** no menu definições. As informações de cópia de segurança específicas para essa VM aparecem. No exemplo seguinte, **ContosoVM** é o nome da máquina virtual. **ContosoVM demovault** é o nome do cofre dos serviços de recuperação. Não precisa de memorizar o nome do cofre dos serviços de recuperação que armazena os pontos de recuperação, pode aceder a estas informações da máquina virtual.  

![Detalhes do Cofre de serviços de recuperação VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

Se vários servidores estiverem protegidos com o mesmo Cofre de serviços de recuperação, poderá ser mais lógica para ver o Cofre dos serviços de recuperação. Pode procurar todos os cofres dos serviços de recuperação na subscrição e selecione um na lista.

As secções seguintes contêm ligações para artigos que explicam como utilizar um cofre dos serviços de recuperação em cada tipo de atividade.

### <a name="back-up-data"></a>Fazer uma cópia de segurança de dados
- [Cópia de segurança de uma VM do Azure](backup-azure-vms-first-look-arm.md)
- [Criar cópias de segurança do Windows Server ou estação de trabalho do Windows](backup-try-azure-backup-in-10-mins.md)
- [Fazer cópias de segurança de cargas de trabalho do DPM no Azure](backup-azure-dpm-introduction.md)
- [Preparar a cópia de segurança de cargas de trabalho utilizando o servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Gerir pontos de recuperação
- [Gerir cópias de segurança de VM do Azure](backup-azure-manage-vms.md)
- [Gestão de ficheiros e pastas](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Restaurar dados a partir do Cofre
- [Recuperar ficheiros individuais a partir de uma VM do Azure](backup-azure-restore-files-from-vm.md)
- [Restaurar uma VM do Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Proteger o Cofre
- [Proteger dados de cópia de segurança de nuvem no cofres dos serviços de recuperação](backup-azure-security-feature.md)



## <a name="next-steps"></a>Passos Seguintes
Utilize os artigos seguintes para:</br>
[Cópia de segurança de uma VM do IaaS](backup-azure-arm-vms-prepare.md)</br>
[Cópia de segurança de um servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Cópia de segurança de um servidor do Windows](backup-configure-vault.md)

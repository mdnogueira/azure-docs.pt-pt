---
title: "Atualização Cofre de cópia de segurança para o Cofre dos serviços de recuperação de cópia de segurança do Azure | Microsoft Docs"
description: "Atualização Cofre de cópia de segurança para o Cofre dos serviços de recuperação para obter novas funcionalidades, como a cópia de segurança de VMs, a segurança avançada, a cópia de segurança de VM de VMware e a cópia de segurança do Estado do sistema para servidores do Windows do Gestor de recursos"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: cfc2fde552b029412042474e31a1b28dd80b3021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Atualizado para o Cofre dos serviços de recuperação Cofre de cópia de segurança
Este artigo fornece uma descrição geral fornece o Cofre dos serviços de recuperação, perguntas mais frequentes sobre a atualização de cópia de segurança existente do cofre dos serviços de recuperação cofre e passos pós-atualização. Um cofre dos serviços de recuperação é o equivalente do Azure Resource Manager um cofre de cópia de segurança que aloja os seus dados de cópia de segurança. Os dados são, geralmente, cópias de dados ou informações de configuração para máquinas virtuais (VMs), cargas de trabalho, servidores ou estações de trabalho, se no local ou no Azure.

## <a name="what-is-a-recovery-services-vault"></a>O que é um cofre dos Serviços de Recuperação?
Um cofre dos Serviços de Recuperação é uma entidade de armazenamento online no Azure utilizada para armazenar dados, como cópias de segurança, pontos de recuperação e políticas de cópia de segurança. Pode utilizar os cofres dos serviços de recuperação para armazenar dados de cópia de segurança para vários serviços do Azure, tais como VMs do IaaS (Linux ou Windows) e bases de dados SQL do Azure. Sistema de suporte de cofres de serviços de recuperação System Center DPM, Windows Server, servidor de cópia de segurança do Azure e muito mais. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Cofres dos serviços de recuperação compara e cofres de cópia de segurança dos
Os cofres dos serviços de recuperação baseiam-se no modelo Azure Resource Manager do Azure, enquanto que cofres de cópia de segurança baseiam-se no modelo do Azure Service Manager. Ao atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação, os dados de cópia de segurança permanecem intactos durante e após o processo de atualização. Os cofres dos serviços de recuperação fornecem funcionalidades não está disponíveis para cofres de cópia de segurança, tais como:

- **Capacidades para ajudar a proteger os dados da cópia de segurança melhoradas**: cofres dos serviços de recuperação com, cópia de segurança do Azure fornece capacidades de segurança para proteger cópias de segurança de nuvem. Estas funcionalidades de segurança, certifique-se que pode proteger as cópias de segurança e, em segurança recuperar dados de cópias de segurança de nuvem, mesmo se os servidores de produção e de cópia de segurança sejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitorização central para o ambiente de TI de híbrida**: cofres dos serviços de recuperação com, pode monitorizar não apenas o [VMs IaaS do Azure](backup-azure-manage-vms.md) , mas também o [recursos no local](backup-azure-manage-windows-server.md#manage-backup-items) de um portal central. [Saiba mais](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Controlo de acesso baseado em funções (RBAC)**: RBAC fornece controlo de gestão de acesso detalhado no Azure. [O Azure oferece várias funções incorporadas](../active-directory/role-based-access-built-in-roles.md), e cópia de segurança do Azure tem três [funções incorporadas para gerir pontos de recuperação](backup-rbac-rs-vault.md). Os cofres dos serviços de recuperação são compatíveis com o RBAC, que restringe a cópia de segurança e restaurar o acesso ao conjunto definido de funções de utilizador. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteger todas as configurações de Virtual Machines do Azure**: cofres dos serviços de recuperação protegem VMs baseadas no Resource Manager, incluindo discos Premium, discos geridos e VMs encriptados. Atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação dá-lhe a oportunidade para atualizar as VMs baseadas no Service Manager para as VMs baseadas no Resource Manager. Ao atualizar o cofre, pode manter os pontos de recuperação VM baseadas no Service Manager e configure a proteção para VMs atualizadas (ativado Resource Manager). [Saiba mais](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauro instantânea para as VMs de IaaS**: cofres dos serviços de recuperação a utilizar, pode restaurar ficheiros e pastas a partir de uma VM do IaaS sem restaurar toda a VM, que permite que os tempos de restauro mais rápidos. Restauro instantânea para as VMs de IaaS está disponível para o Windows e VMs com Linux. [Saiba mais](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Se tiver registados para um cofre de cópia de segurança com o agente MARS anteriores ao 2.0.9083.0, de itens [transferir o agente MARS mais recente]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) versão a tomar as vantagens de todas as funcionalidades do Cofre de serviços de recuperação. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Gerir os cofres dos serviços de recuperação
Os ecrãs seguintes mostram um novo cofre de serviços de recuperação, atualizado a partir do Cofre de cópia de segurança, no portal do Azure. O Cofre atualizado estará presente no grupo de recursos predefinido com o nome "Predefinição-RecoveryServices-ResourceGroup-georreplicação". Exemplo: Se o seu Cofre de cópia de segurança foi localizado no EUA oeste, este será colocada cópias de segurança no RG com o nome predefinido RecoveryServices-ResourceGroup westus default.
> [!NOTE]
> Para clientes de CPS padrão, o grupo de recursos não é alterado após a atualização do cofre e permanece o mesmo que estava antes da atualização.

O primeiro ecrã mostra o dashboard do cofre que apresenta entidades-chave do cofre.
![exemplo do Cofre de serviços de recuperação atualizado a partir de um cofre de cópia de segurança](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

O segundo ecrã mostra a ajuda das ligações disponíveis para o ajudar a começar a utilizar o Cofre dos serviços de recuperação.

![ligações de ajuda, no painel de início rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Passos pós-atualização
O Cofre de serviços de recuperação suporta a especificação informações de fuso horário na política de cópia de segurança. Depois de cofre é atualizado com êxito, vá para políticas de cópia de segurança a partir do menu de definições do cofre e atualizar as informações de fuso horário para cada uma das políticas configuradas no cofre. Este ecrã mostra já o tempo de agenda de cópia de segurança especificado como por fuso horário local utilizado quando criou a política. 

## <a name="enhanced-security"></a>Segurança melhorada
Quando um cofre de cópia de segurança é atualizado para um cofre dos serviços de recuperação, as definições de segurança para essa cofre automaticamente estão ativadas. Quando as definições de segurança são em determinadas operações, tais como as cópias de segurança a eliminar ou alterar uma frase de acesso necessitar de um [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) PIN. Para obter mais informações sobre a segurança avançada, consulte o artigo [funcionalidades de segurança para proteger cópias de segurança híbrida](backup-azure-security-feature.md). Quando a segurança avançada estiver ativada, os dados são mantidos cópias de segurança para 14 dias depois das informações de ponto de recuperação foi eliminadas do cofre. Os clientes são cobrados para armazenamento destes dados de segurança. Retenção de dados de segurança se aplica a pontos de recuperação direcionados para o agente de cópia de segurança do Azure, servidor de cópia de segurança do Azure e System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Recolher dados do seu Cofre
Uma vez atualizar para um cofre dos serviços de recuperação, configure os relatórios para cópia de segurança do Azure (para o agente de VMs de IaaS e dos serviços de recuperação do Microsoft Azure) e utilizar o Power BI para aceder aos relatórios. Para obter informações adicionais sobre a recolha de dados, consulte o artigo [relatórios de configurar a cópia de segurança do Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O plano de atualização afeta a minha cópias de segurança em curso?**</br>
Não. As cópias de segurança em curso continuam sem interrupções durante e após a atualização.

**O que faz esta atualização média para os meus ferramentas existentes?**</br>
Tem de atualizar a automatização existente ou ferramentas para o modelo de implementação Resource Manager para se certificar de que continua a funcionar após a atualização. Consulte as referências de cmdlets do PowerShell para o [modelo de implementação do Service Manager](backup-client-automation-classic.md) e [modelo de implementação do Resource Manager](backup-client-automation.md).

**Pode posso reverter após a atualização?**</br>
Não. A reversão não é suportada depois dos recursos foram atualizados com êxito.

**Pode ver o meu cofre clássico post atualização?**</br>
Não. Não é possível ver ou gerir o seu Cofre clássico post atualização. Apenas poderá utilizar o novo portal do Azure para todas as ações de gestão no cofre.

**Por que motivo não pode ver os servidores protegidos pelo agente MARS no meu cofre atualizado?**</br>
Tem de instalar o agente MARS mais recente para ver todos os servidores protegidos pelo agente MARS no seu cofre. Pode transferir a versão mais recente do agente de [aqui]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Posso não pode ver a política de cópia de segurança para os servidores protegidos pelo agente MARS após a atualização**</br>
Política de cópia de segurança do cofre pode estar desatualizada e, por conseguinte, não foi possível sincronizar para o Cofre atualizado. Atualize a política para garantir a que continuar a ver as suas políticas no cofre atualizado.
Para atualizar a política, vá para o agente MARS e atualizar a política de cópia de segurança configurada.

**Por que motivo não é possível atualizar os meus política de cópia de segurança após a atualização?**</br>
Isto acontece quando estão num agente de cópia de segurança antigo e selecionar o período de retenção mínimo para ser inferior ao valor mínimo permitido. Quando um cofre de cópia de segurança é atualizado para um cofre dos serviços de recuperação, as definições de segurança para essa cofre automaticamente estão ativadas. Para se certificar de que existem sempre um número válido de pontos de recuperação disponíveis, há algum período de retenção mínimo tem de ser mantidos de acordo com a funcionalidade de segurança. Para obter mais detalhes, consulte [aqui](backup-azure-security-feature.md).
Além disso, terá de atualizar os agentes de Backup do Azure para a versão mais recente para efetuar as vantagens das funcionalidades mais recentes do Backup do Azure.

**Tem a atualizar o meu agente, mas ainda não é possível ver os objetos que está a ser sincronizados com êxito até mesmo dias após a atualização**</br>
Verifique se registou a mesma máquina para vários cofres. Certifique-se de que está a visualizar o mesmo cofre em que o agente MARS está registado. Para saber qual cofre o agente MARS está registado, abra o registo do Windows e verifique o valor de chave ServiceResourceName em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config cofre registada para que o agente MARS aparecerá não existe. Se a chave de ServiceResourceName não está visível no seu sistema, entrar-nos com o valor das chaves ResourceId e MachineId em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config e iremos ajudar a resolver o problema.

**Por que motivo não vejo as informações de tarefas para os meus recursos após a atualização?**</br>
A monitorização para cópias de segurança (o agente MARS e IaaS) é uma funcionalidade nova que obtém quando atualizar o seu Cofre de cópia de segurança para o Cofre dos serviços de recuperação. As informações de monitorização demora até 12 horas para sincronizar com o serviço.

**Como posso comunicar um problema?**</br>
Se falhar qualquer parte da atualização do cofre, tenha em atenção que o OperationId listado no erro. Microsoft Support proativamente irá funcionar para resolver o problema. Pode aceder à suporte ou e-mail-nos rsvaultupgrade@service.microsoft.com com o ID de subscrição, nome do cofre e OperationId. Vamos tentar resolver o problema mais rapidamente possível. Não repita a operação, a menos que explicitamente instruído para fazer pela Microsoft.

## <a name="next-steps"></a>Passos seguintes
Utilize os seguintes artigos para:</br>
[Cópia de segurança de uma VM do IaaS](backup-azure-arm-vms-prepare.md)</br>
[Cópia de segurança de um servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Cópia de segurança de um servidor do Windows](backup-configure-vault.md)

---
title: "Atualizar um cofre de recuperação de Site para um cofre dos serviços de recuperação do Azure"
description: "Saiba como atualizar um cofre de recuperação de sites do Azure para um cofre dos serviços de recuperação"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Atualizar um cofre de recuperação de Site para um cofre de serviços de recuperação baseados no Azure Resource Manager

Este artigo descreve como atualizar cofres de recuperação de sites do Azure para o serviço de recuperação baseado no Azure Resource Manager cofres sem qualquer impacto na replicação em curso. Para obter mais informações sobre as funcionalidades do Azure Resource Manager e vantagens, consulte [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introdução
Um cofre dos serviços de recuperação é um recurso do Azure Resource Manager para gerir a cópia de segurança e recuperação após desastre nativamente na nuvem. É um cofre unificado que pode utilizar no portal do Azure, e este substitui a cópia de segurança clássica e recuperação de Site cofres dos.

Os cofres dos serviços de recuperação oferecem uma matriz de funcionalidades, incluindo:

* Suporte de Gestor de recursos do Azure: pode proteger e efetuar a ativação pós-falha as máquinas virtuais e máquinas físicas para uma pilha do Azure Resource Manager.

* Excluir o disco: Se tiver de ficheiros temporários ou dados de elevado volume de alterações que não pretende utilizar todos os seus largura de banda, pode excluir volumes de replicação. Esta capacidade tem sido ativada atualmente no *VMware para Azure* e *Hyper-V para o Azure* e é expandido para outros cenários bem.

* Suporte para premium e do armazenamento localmente redundante: agora pode proteger servidores no armazenamento premium contas que permitem aos clientes a proteger as aplicações com superior entradam/saídam operações por segundo (IOPS). Esta capacidade está atualmente ativada na *VMware para Azure*.

* Está mais simples de experiência de introdução: A experiência avançada de introdução tem foi concebida para facilitar a configuração de recuperação após desastre.

* Cópia de segurança e gestão de recuperação de sites do mesmo cofre: agora pode proteger servidores para recuperação após desastre ou efetuar cópia de segurança do cofre do mesmo, que poderá reduzir a sobrecarga significativamente a gestão.

Para obter mais informações sobre a experiência atualizada e funcionalidades, consulte o [blogue de armazenamento, a cópia de segurança e recuperação](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Funcionalidades importantes

* Sem afetar a replicação em curso: replicações em curso continuar sem qualquer interrupção durante e após a atualização.

* Sem custos adicionais: obter um conjunto completo de capacidades atualizados sem custos adicionais.

* Nenhuma perda de dados: porque este processo é uma atualização e não uma migração, as definições e de pontos de recuperação de replicação existentes permanecem intactas durante e após a atualização.


## <a name="what-happens-during-the-vault-upgrade"></a>O que acontece durante a atualização do Cofre?

Durante a atualização, não é possível efetuar operações como registar um novo servidor ou ativar a replicação para uma máquina virtual (VM). As operações que envolvem a leitura de dados de ou escrever dados para o cofre, tais como replicação em curso de itens protegidos para o cofre, continuam sem interrupções.

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>Alterações à automatização e ferramentas após a atualização
Como atualizar o tipo de cofre do modelo de implementação clássica para o modelo de implementação Resource Manager, atualize a automatização existente ou ferramentas para assegurar que continua a funcionar após a atualização.

### <a name="prepare-your-environment-for-the-upgrade"></a>Preparar o ambiente para a atualização

* [Instale o PowerShell ou a atualizá-lo para a versão 5 ou posterior](https://www.microsoft.com/download/details.aspx?id=50395)
* [Instale a versão mais recente do MSI do Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Transferir o script de actualização do cofre dos serviços de recuperação](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>Pré-requisitos
Para atualizar a partir de cofres de recuperação de sites para cofres de serviço de recuperação baseado no Azure Resource Manager, tem de cumprir os seguintes requisitos:

* Versão do agente mínimo: A versão do fornecedor do Azure Site Recovery instalado no seu servidor tem de ser 5.1.1700.0 ou posterior.

* Configuração suportada: não é possível configurar o seu Cofre com rede de armazenamento (SAN) ou grupos de Disponibilidade AlwaysOn do SQL Server. Todas as outras configurações são suportadas.

    >[!NOTE]
    >Após a atualização, pode gerir o mapeamento de armazenamento apenas através do PowerShell.

* Cenário de implementação suportado: O Cofre não deve ser o *VMware para Azure* modelo de implementação de legado. Antes de continuar, mova primeiro para o modelo de implementação melhorada.

* Não existem tarefas ativas iniciada pelo utilizador que envolvem gestão plane operações: porque o acesso ao plane de gestão é restrito durante a atualização, concluir todos os seus plane as ações de gestão antes de acionar a atualização. Este processo não inclui a replicação em curso.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Esta atualização afeta a minha replicação em curso?**

Não. A replicação em curso continua ininterrupta durante e após a atualização.

**O que acontece definições de rede, tais como definições de IP e de VPN de site a site?**

A atualização não afeta as definições de rede. Todas as ligações do Azure-para-no local permanecem intactas.

**O que acontece ao meu cofres se não a planear a atualização num futuro próximo?**

Suporte para o Cofre de recuperação de sites no portal do Azure antigo vai ser preterido a partir de Setembro de 2017. Recomendamos vivamente que utilize a funcionalidade de atualização para mover para o novo portal.

**O que significa este plano de migração para a minha ferramentas existentes?**  

Atualizar as ferramentas para o modelo de implementação Resource Manager é uma das alterações mais importantes que que tem em conta os planos de atualização. Os cofres dos serviços de recuperação são baseados no modelo de implementação Resource Manager. 

**Quanto o período de indisponibilidade gestão plane último?**

A atualização normalmente demora cerca de 15 a 30 minutos e pode demorar até um máximo de uma hora.

**Pode posso reverter após a atualização?**

Não. A reversão não é suportada depois dos recursos foram atualizados com êxito.

**Posso validar a minha subscrição ou recursos para ver se possa ser atualizados?**

Sim. A plataforma suportada opção de atualização, é o primeiro passo na atualização do validar se os recursos são capazes de uma atualização. Se a validação falhar, irá receber mensagens de erro apropriada ou avisos.

**Como posso comunicar um problema com a atualização?**

Se ocorrer eventuais falhas durante a atualização, tenha em atenção o ID de operação que está listado no erro. Microsoft Support proativamente funcionará sobre como resolver o problema. Também pode contactar a equipa de suporte com o ID de subscrição, o nome do cofre e o ID de operação. Suporte irá funcionar para resolver o problema mais rapidamente possível. Não repita a operação, a menos que explicitamente instruído para fazer pela Microsoft.

## <a name="run-the-script"></a>Execute o script

No PowerShell, execute o seguinte comando:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID: O ID de subscrição associado ao cofre que estiver a atualizar.

* VaultName: O nome do cofre que estiver a atualizar.

* Localização: A localização do cofre que estiver a atualizar.

* O ResourceType: Cofres dos HyperVRecoveryManagerVault para a recuperação de Site.

* TargetResourceGroupName: O grupo de recursos no qual pretende que o Cofre atualizado para ser colocada. TargetResourceGroupName pode ser um grupo de recursos existente no Azure Resource Manager ou um novo. Se o TargetResourceGroupName fornecido não existe, é criado como parte da atualização na mesma localização que o cofre. Para obter mais informações, consulte a secção "Grupos de recursos" [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

    >[!NOTE]
    >Nomenclatura do grupo de recursos está sujeita a determinados restrições. Para impedir a falha na atualização do cofre, lembre-se de que observar a Convenção de nomenclatura cuidadosamente.
    >
    >Por exemplo:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 - SubscriptionId 1234-54123-354354-56416-8645 - VaultName gen2dr-localização "Europa do Norte" - ResourceType hypervrecoverymanagervault - TargetResourceGroupName abc

Em alternativa, pode executar o script seguinte. Introduza os valores para os parâmetros obrigatórios.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. O script do PowerShell pede-lhe para introduzir as suas credenciais. Introduzi-las duas vezes, uma vez para a conta de modelo de implementação clássica e uma vez para a conta do Azure Resource Manager.

2. Após introduzir as suas credenciais, o script executa uma verificação para determinar se a configuração de infraestrutura satisfaz os requisitos mencionados anteriormente.

3. Depois dos pré-requisitos foram verificados e confirmados, lhe for pedido para continuar com a atualização do cofre. O processo de atualização começa a atualizar o seu cofre. A atualização completa pode demorar 15 a 30 minutos a concluir.

4. Após a atualização foi concluída com êxito, pode aceder no cofre atualizado no portal do Azure.

## <a name="post-upgrade-vault-management"></a>Gestão do Cofre de pós-atualização

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Replicar utilizando o Azure Site Recovery no cofre dos serviços de recuperação

* Agora pode proteger as suas VMs do Azure a partir de uma região para outro. Para obter mais informações, consulte [replicar as VMs do Azure entre regiões com o Azure Site Recovery](site-recovery-azure-to-azure.md).

* Para obter mais informações sobre a replicar VMware VMs para o Azure, consulte [replicar as VMs VMware para o Azure com a recuperação de Site](vmware-walkthrough-overview.md).

* Para obter mais informações sobre a replicar VMs de Hyper-V (sem VMM) para o Azure, consulte [Hyper-V replicar máquinas virtuais (sem VMM) para o Azure](hyper-v-site-walkthrough-overview.md).

* Para obter mais informações sobre a replicar VMs de Hyper-V (com o VMM) para o Azure, consulte [Hyper-V replicar máquinas virtuais em nuvens VMM para o Azure utilizando a recuperação de sites no portal do Azure](vmm-to-azure-walkthrough-overview.md).

* Para obter mais informações sobre a replicar VMs de Hyper (com o VMM) para um site secundário, consulte [Hyper-V replicar máquinas virtuais em nuvens VMM para um site VMM secundário com o portal do Azure](site-recovery-vmm-to-vmm.md).

* Para obter mais informações sobre a replicar VMs de VMware para um site secundário, consulte [replicar no local em máquinas virtuais VMware ou servidores físicos para um site secundário no portal clássico do Azure](site-recovery-vmware-to-vmware.md).

### <a name="view-your-replicated-items"></a>Ver os itens replicados

A imagem seguinte mostra a página do dashboard de cofre dos serviços de recuperação que apresenta entidades-chave do cofre. Para ver uma lista de entidades protegidas no cofre, selecione **recuperação de Site** > **replicado itens**.


![Itens replicados](./media/upgrade-site-recovery-vaults/replicateditems.png)

A imagem seguinte mostra o fluxo de trabalho para visualizar os itens replicados e **ativação pós-falha** comando para iniciar uma ativação pós-falha.

![Itens replicados](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>Ver as definições de replicação

O Cofre de recuperação de sites, cada grupo de proteção é configurado com a frequência de cópia, retenção do ponto de recuperação, frequência de instantâneos consistentes da aplicação e outras definições de replicação. No cofre dos serviços de recuperação, estas definições são configuradas como uma política de replicação. O nome da política é o nome do grupo de proteção ou o *primarycloud_Policy*.

Para obter mais informações sobre a política de replicação, consulte [gerir a política de replicação de VMware para Azure](site-recovery-setup-replication-settings-vmware.md).

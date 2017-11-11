---
title: "Atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação (pré-visualização) | Microsoft Docs"
description: "As instruções e informações de suporte para atualizar o seu Cofre de cópia de segurança do Azure para um cofre dos serviços de recuperação."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/09/2017
ms.author: sogup;markgal;arunak
ms.openlocfilehash: 4867a43aab1357cb8e01c2ddcef74cdebb41a84a
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação

Este artigo explica como atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação. O processo de atualização não tem impacto quaisquer tarefas de cópia de segurança está em execução e não se tenha perdido nenhum dado cópia de segurança. Principais razões para atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação:
- Todas as funcionalidades de um cofre de cópia de segurança são mantidas num cofre dos serviços de recuperação.
- Os cofres dos serviços de recuperação tem mais funcionalidades do que cofres de cópia de segurança, incluindo: melhor monitorização de segurança, integrada, restauros mais rápidos e restauros ao nível do item.
- Gerir itens de cópia de segurança a partir de um portal melhorado, simplificado.
- Novas funcionalidades só se aplicam aos cofres dos serviços de recuperação.

## <a name="impact-to-operations-during-upgrade"></a>Impacto para operações durante a atualização

Ao atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação, não há nenhum impacto para as suas operações de plane de dados. Todas as tarefas de cópia de segurança continuar como normal e quaisquer tarefas de restauro ativa continuar sem interrupção. Durante a atualização, operações de gestão cobrado um curto período de indisponibilidade e não é possível proteger novos itens ou criar adhoc tarefas de cópias de segurança. Não executam as tarefas de restauro para VMs de IaaS durante a atualização. A atualização de cofre demora menos de uma hora para concluir. Depois de terminar, um cofre dos serviços de recuperação substitui o Cofre de cópia de segurança.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Alterações para a automatização e a ferramenta após a atualização

Durante a preparação da sua infraestrutura para a atualização do cofre, tem de atualizar o seu automatização existente ou ferramentas para assegurar que continua a funcionar após a atualização.
Consulte as referências de cmdlets do PowerShell para o [modelo de implementação do Service Manager](backup-client-automation-classic.md) e [modelo de implementação do Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Antes da atualização

Verifique os seguintes problemas antes de atualizar os cofres de cópia de segurança cofres de serviço de recuperação.

- **Versão do agente mínimo**: para atualizar o seu Cofre, certifique-se de que o agente de serviços de recuperação do Azure (MARS) da Microsoft, pelo menos, versão 2.0.9083.0. Se o agente MARS é anterior ao 2.0.9083.0, atualize o agente antes de iniciar o processo de atualização.
- **Modelo de faturação baseada na instância**: cofres de serviço de recuperação só suportam o modelo de faturação baseada na instância. Se tiver um cofre de cópia de segurança que está a utilizar o modelo de faturação baseada no armazenamento antigo, converter o modelo de faturação durante a atualização.
- **Não existem operações de configuração de cópia de segurança em curso**: durante a atualização, o acesso ao plane de gestão é restrito. Conclua todas as ações de plane de gestão e, em seguida, iniciar a atualização.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Utilizar scripts do PowerShell para atualizar os cofres

Pode utilizar scripts do PowerShell para atualizar os cofres de cópia de segurança para os cofres dos serviços de recuperação. Certifique-se de que tem os componentes necessários do PowerShell para acionar a atualização do cofre. Scripts do PowerShell para cofres de cópia de segurança não funcionam para cofres dos serviços de recuperação. Prepare o ambiente para atualizar os cofres:

1. Instalar ou atualizar [Windows Management Framework (WMF) para a versão 5](https://www.microsoft.com/download/details.aspx?id=50395) ou superior.
2. [Instalar o Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Transferir o [script do PowerShell](https://aka.ms/vaultupgradescript2) para atualizar os cofres.

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

Utilize o seguinte script para atualizar os cofres. O seguinte script de exemplo tem uma explicação dos parâmetros.

RecoveryServicesVaultUpgrade 1.0.2.ps1 **- SubscriptionID** `<subscriptionID>` **- VaultName** `<vaultname>` **-localização** `<location>` **- ResourceType** `BackupVault` **- TargetResourceGroupName**`<rgname>`

**SubscriptionID** -o número de ID de subscrição do cofre que está a ser atualizado.<br/>
**VaultName** -o nome do Cofre de cópia de segurança que está a ser atualizado.<br/>
**Localização** -localização do cofre que está a ser atualizado.<br/>
**ResourceType** -utilizar BackupVault.<br/>
**TargetResourceGroupName** - uma vez que estiver a atualizar o cofre para uma implementação baseada no Resource Manager, especifique um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um, fornecendo um nome novo. Se misspell o nome de um grupo de recursos, pode criar um novo grupo de recursos. Para obter mais informações sobre grupos de recursos, leia este [descrição geral sobre grupos de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Os nomes de grupo de recursos ter restrições. Lembre-se de que siga as orientações; Falha ao fazê-lo pode fazer com que o Cofre atualizações falhar.
>
>**Azure US Government** clientes tem de definir o ambiente para "AzureUSGovernment" ao executar o script.
>**Azure China** clientes tem de definir o ambiente para "AzureChinaCloud" ao executar o script.

O fragmento de código seguinte é um exemplo de que o comando do PowerShell deve ter o seguinte aspeto:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Também pode executar o script sem quaisquer parâmetros e lhe for pedido que forneça entradas para todos os parâmetros necessários.

O script do PowerShell pede-lhe para introduzir as suas credenciais. Introduza as suas credenciais duas vezes: uma vez para a conta do Service Manager e uma segunda vez para a conta de Gestor de recursos.

### <a name="pre-requisites-checking"></a>A verificação de pré-requisitos
Depois de introduzir as suas credenciais do Azure, Azure verifica se o seu ambiente cumpre os seguintes pré-requisitos:

- **Versão do agente mínimo** -atualizar cofres de cópia de segurança para os cofres dos serviços de recuperação requer que o agente MARS ser, pelo menos, versão 2.0.9083.0. Se tiver registados para um cofre de cópia de segurança com um agente anteriores ao 2.0.9083.0 de itens, a verificação de pré-requisitos falha. Se falhar a verificação de pré-requisitos, atualize o agente e tente atualizar novamente o cofre. Pode transferir a versão mais recente do agente de [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Tarefas de configuração em curso**: se alguém está a configurar as tarefas de um cofre de cópia de segurança definido para ser actualizado ou registar um item, a verificação de pré-requisitos falha. Concluir a configuração, ou terminou o registo de item e, em seguida, inicie o processo de atualização do cofre.
- **Modelo de faturação baseada no armazenamento**: cofres dos serviços de recuperação suportam o modelo de faturação baseada na instância. Se executar a atualização do cofre num cofre de cópia de segurança que utiliza o modelo de faturação baseada no armazenamento, são-lhe pedido para atualizar o modelo de faturação, juntamente com o cofre. Caso contrário, pode atualizar o modelo de faturação em primeiro lugar, e, em seguida, executar a atualização do cofre.
- Identifica um grupo de recursos para o Cofre dos serviços de recuperação. Para tirar partido das funcionalidades de implementação Resource Manager, tem de colocar um cofre dos serviços de recuperação num grupo de recursos. Se não souber o grupo de recursos para utilizar, forneça um nome e o processo de atualização cria o grupo de recursos por si. O processo de atualização também associa o Cofre de novo grupo de recursos.

Depois do processo de atualização estiver concluída a verificação de pré-requisitos, o processo de pede-lhe para iniciar a atualização do cofre. Depois de confirmar, o processo de atualização demora, normalmente, cerca de 15 a 20 minutos a concluir, dependendo do tamanho do seu cofre. Se tiver um cofre de grandes dimensões, a atualização pode demorar até 90 minutos.

## <a name="managing-your-recovery-services-vaults"></a>Gerir os cofres dos serviços de recuperação

Os ecrãs seguintes mostram um novo cofre de serviços de recuperação, atualizado a partir do Cofre de cópia de segurança, no portal do Azure. O primeiro ecrã mostra o dashboard do cofre que apresenta entidades-chave do cofre.

![exemplo do Cofre de serviços de recuperação atualizado a partir de um cofre de cópia de segurança](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

O segundo ecrã mostra a ajuda das ligações disponíveis para o ajudar a começar a utilizar o Cofre dos serviços de recuperação.

![ligações de ajuda, no painel de início rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Passos pós-atualização
O Cofre de serviços de recuperação suporta a especificação informações de fuso horário na política de cópia de segurança. Depois de cofre é atualizado com êxito, vá para políticas de cópia de segurança a partir do menu de definições do cofre e atualizar as informações de fuso horário para cada uma das políticas configuradas no cofre. Este ecrã mostra já o tempo de agenda de cópia de segurança especificado como por fuso horário local utilizado quando criou a política. 

## <a name="enhanced-security"></a>Segurança melhorada

Quando um cofre de cópia de segurança é atualizado para um cofre dos serviços de recuperação, as definições de segurança para essa cofre automaticamente estão ativadas. Quando as definições de segurança são em determinadas operações, tais como as cópias de segurança a eliminar ou alterar uma frase de acesso necessitar de um [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) PIN. Para obter mais informações sobre a segurança avançada, consulte o artigo [funcionalidades de segurança para proteger cópias de segurança híbrida](backup-azure-security-feature.md). 

Quando a segurança avançada estiver ativada, os dados são mantidos cópias de segurança para 14 dias depois das informações de ponto de recuperação foi eliminadas do cofre. Os clientes são cobrados para armazenamento destes dados de segurança. Retenção de dados de segurança se aplica a pontos de recuperação direcionados para o agente de cópia de segurança do Azure, servidor de cópia de segurança do Azure e System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Recolher dados do seu Cofre

Depois de atualizar para um cofre dos serviços de recuperação, configure os relatórios para cópia de segurança do Azure (para VMs de IaaS e os serviços de recuperação do Azure (MARS) da Microsoft) e utilizar o Power BI para aceder aos relatórios. Para obter informações adicionais sobre a recolha de dados, consulte o artigo [relatórios de configurar a cópia de segurança do Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O plano de atualização afeta a minha cópias de segurança em curso?**</br>
Não. As cópias de segurança em curso continuam sem interrupções durante e após a atualização.

**Se não planear a sobre a atualização em breve, o que acontece ao meu cofres?**</br>
Uma vez que todas as novas funcionalidades aplicam apenas aos cofres dos serviços de recuperação, recomendamos vivamente a atualizar os cofres. Microsoft, eventualmente, irá Preterir o portal clássico. A partir de 1 de Setembro de 2017, Microsoft começará cofres de cópia de segurança a atualização automática para os cofres dos serviços de recuperação. Depois de Novembro 30,2017, já não pode criar cofres de cópia de segurança através do PowerShell. O Cofre pode ser atualizado automaticamente qualquer altura entre. A Microsoft recomenda a que atualizar o seu Cofre logo que possível.

**O que faz esta atualização média para os meus ferramentas existentes?**</br>
Atualize as ferramentas para o modelo de implementação Resource Manager. Os serviços de recuperação cofres foram criadas para utilizar no modelo de implementação Resource Manager. Planeamento para o modelo de implementação Resource Manager e a gestão de contas para a diferença na sua cofres é importante. 

**Durante a atualização, é muito período de indisponibilidade?**</br>
Depende do número de recursos que estão a ser atualizado. Para implementações mais pequenas (alguns dezenas de instâncias protegidas), a atualização toda deve demorar menos de 20 minutos. Para implementações maiores, deve demorar um máximo de uma hora.

**Pode posso reverter após a atualização?**</br>
Não. A reversão não é suportada depois dos recursos foram atualizados com êxito.

**Posso validar a minha subscrição ou recursos para ver se forem capazes de atualização?**</br>
Sim. O primeiro passo na atualização valida que os recursos são capazes de atualização. No caso de falha de validação de pré-requisitos, poderá recebe mensagens para todos os motivos pelos quais que não foi possível concluir a atualização.

**Que permissões devem ter acionar a atualização do Cofre?**</br>
Para efetuar a atualização do cofre, tem de ser adicionado como coadministrador da subscrição no portal clássico do Azure. Isto é necessário, mesmo que já estão listados como proprietário no portal do Azure. Tente adicionar um coadministrador da subscrição no portal clássico do Azure para saber se está coadministrador da subscrição. Se não é possível adicionar um coadministrador, contacte um administrador de serviço ou coadministrador da subscrição, o que pode adicionar como coadministrador.

**Pode atualizar o meu Cofre de cópia de segurança com base CSP?**</br>
Não. Atualmente, não é possível atualizar os cofres de cópia de segurança com base CSP. Iremos adicionar suporte para atualização cofres de cópia de segurança com base CSP em versões do seguintes.

**Pode ver o meu cofre clássico post atualização?**</br>
Não. Não é possível ver ou gerir o seu Cofre clássico post atualização. Apenas poderá utilizar o novo portal do Azure para todas as ações de gestão no cofre.

**Falha ao atualizar o meu, mas a máquina que contido agente necessidade de atualizar, não já existe. O que posso fazer nesse caso?**</br>
Se precisar de utilizar a loja, as cópias de segurança desta máquina para a retenção de longa duração, em seguida, que não será possível atualizar o cofre. Em versões futuras iremos adicionar suporte para atualizar essa um cofre.
Se não for necessário armazenar as cópias de segurança desta máquina deixam de ser, em seguida, volte anular o registo nesta máquina a partir do cofre e repita a atualização.

**Por que motivo não vejo as informações de tarefas para os meus recursos após a atualização?**</br>
A monitorização para cópias de segurança (o agente MARS e IaaS) é uma funcionalidade nova que obtém quando atualizar o seu Cofre de cópia de segurança para o Cofre dos serviços de recuperação. As informações de monitorização demora até 12 horas para sincronizar com o serviço.

**Como posso comunicar um problema?**</br>
Se falhar qualquer parte da atualização do cofre, tenha em atenção que o OperationId listado no erro. Microsoft Support proativamente irá funcionar para resolver o problema. Pode aceder à suporte ou e-mail-nos rsvaultupgrade@service.microsoft.com com o ID de subscrição, nome do cofre e OperationId. Vamos tentar resolver o problema mais rapidamente possível. Não repita a operação, a menos que explicitamente instruído para fazer pela Microsoft.


## <a name="next-steps"></a>Passos seguintes
Utilize o seguinte artigo para:</br>
[Cópia de segurança de uma VM do IaaS](backup-azure-arm-vms-prepare.md)</br>
[Cópia de segurança de um servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Cópia de segurança um Windows Server](backup-configure-vault.md).

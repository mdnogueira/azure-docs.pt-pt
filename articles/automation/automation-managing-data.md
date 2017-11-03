---
title: "Gestão de dados de automatização do Azure | Microsoft Docs"
description: "Este artigo contém vários tópicos para gerir um ambiente de automatização do Azure.  Inclui atualmente a retenção de dados e a cópia de segurança de recuperação de desastre de automatização do Azure na automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: 2896f129-82e3-43ce-b9ee-a3860be0423a
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/02/201
ms.author: magoedte;bwren;sngun
ms.openlocfilehash: e4a90f47167cfa2497e1ad5ae9db025d2d7d91dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-automation-data"></a>Gerir os dados da Automatização do Azure
Este artigo contém vários tópicos para gerir um ambiente de automatização do Azure.

## <a name="data-retention"></a>Retenção de dados
Quando elimina um recurso na automatização do Azure, são mantida durante 90 dias para fins de auditorias antes de ser removidos permanentemente.  Não é possível ver ou utilizar o recurso durante este período.  Esta política aplica-se também a recursos que pertencem a uma conta de automatização foi eliminada.

A automatização do Azure elimina automaticamente e elimina permanentemente trabalhos com mais de 90 dias.

A tabela seguinte resume a política de retenção para os recursos diferentes.

| Dados | Política |
|:--- |:--- |
| Contas |Remover permanentemente os 90 dias após a conta for eliminada por um utilizador. |
| Elementos |Remover permanentemente a 90 dias após o elemento é eliminado por um utilizador ou de 90 dias após a conta que contém que o elemento é eliminado por um utilizador. |
| Módulos |Remover permanentemente a 90 dias após o módulo é eliminado por um utilizador ou de 90 dias após a conta que contém que o módulo é eliminado por um utilizador. |
| Runbooks |Remover permanentemente a 90 dias após o recurso é eliminado por um utilizador ou de 90 dias após a conta que possui que o recurso é eliminado por um utilizador. |
| Tarefas |Permanentemente removidos e eliminados 90 dias após o último a ser modificado. Isto pode dever-se ao após a tarefa estiver concluída, está parada ou é suspenso. |
| Ficheiros MOF/configurações de nó |Configuração do nó antigo é permanentemente removida 90 dias após é gerada uma nova configuração de nó. |
| Nós de DSC |Remover permanentemente os 90 dias após o nó é anular o registo da conta de automatização com o portal do Azure ou o [Unregister-AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) cmdlet no Windows PowerShell. Nós permanentemente também são removidos 90 dias após a conta que contém que o nó é eliminado por um utilizador. |
| Relatórios de nó |Remover permanentemente os 90 dias após é gerado um novo relatório para esse nó |

A política de retenção aplica-se a todos os utilizadores e atualmente não é possível personalizar.

No entanto, se pretender manter os dados para um período de tempo, pode reencaminhar runbook os registos da tarefa para análise de registos.  Para obter mais informações, consulte [reencaminhar dados de tarefa de automatização do Azure para a análise de registos do OMS](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Fazer cópia de segurança da Automatização do Azure
Quando elimina uma conta de automatização no Microsoft Azure, todos os objetos na conta são eliminados, incluindo runbooks, módulos, configurações, definições, tarefas e recursos. Não não possível recuperar objetos depois da conta for eliminada.  Pode utilizar as seguintes informações para o conteúdo da sua conta de automatização de cópia de segurança antes de eliminá-lo. 

### <a name="runbooks"></a>Runbooks
Pode exportar os runbooks para ficheiros de script com o Portal de gestão do Azure ou o [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) cmdlet no Windows PowerShell.  Estes ficheiros de script podem ser importados para outra conta de automatização, tal como explicado [criar ou importar um Runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Módulos de integração
Não é possível exportar os módulos de integração da automatização do Azure.  Tem de se certificar de que estão disponíveis fora a conta de automatização.

### <a name="assets"></a>Elementos
Não é possível exportar [ativos](https://msdn.microsoft.com/library/dn939988.aspx) da automatização do Azure.  Utilizar o Portal de gestão do Azure, tem tome nota dos detalhes de variáveis, as credenciais, certificados, ligações e as agendas.  Em seguida, deve criar manualmente quaisquer recursos que são utilizados por runbooks que importar para a automatização de outra.

Pode utilizar [cmdlets do Azure](https://msdn.microsoft.com/library/dn690262.aspx) para obter detalhes do ativos não encriptados e guarde-as para consulta futura ou criar recursos equivalentes na outra conta de automatização.

Não é possível obter o valor para as variáveis encriptadas ou o campo de palavra-passe de credenciais utilizando cmdlets.  Se não souber estes valores, então possível obtê-las de um runbook com o [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) atividades.

Não é possível exportar certificados da automatização do Azure.  Tem de se certificar de que todos os certificados estão disponíveis fora do Azure.

### <a name="dsc-configurations"></a>Configurações de DSC
Pode exportar as configurações para ficheiros de script com o Portal de gestão do Azure ou o [exportação AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) cmdlet no Windows PowerShell. Estas configurações podem ser importadas e utilizadas em outra conta de automatização.

## <a name="geo-replication-in-azure-automation"></a>Georreplicação na automatização do Azure
A georreplicação, padrão em contas de automatização do Azure, efetua cópias de segurança de dados de conta noutra região geográfica para fins de redundância. Pode escolher uma região principal ao configurar a sua conta e, em seguida, uma região secundária é atribuída automaticamente. Os dados secundários, copiados da região primária, é constantemente atualizados em caso de perda de dados.  

A tabela seguinte mostra os emparelhamentos disponíveis região primária e secundária.

| Primária | Secundária |
| --- | --- |
| EUA Centro-Sul |EUA Centro-Norte |
| Este dos EUA 2 |EUA Central |
| Europa Ocidental |Europa do Norte |
| Sudeste Asiático |Ásia Oriental |
| Leste do Japão |Oeste do Japão |

Na improvável eventualidade que os dados de uma região primária são perdidos, Microsoft tenta recuperar. Se não não possível recuperar os dados primários, em seguida, é efetuada a ativação de pós-falha georreplicação e os clientes afetados serão notificados sobre esta através da respetiva subscrição.


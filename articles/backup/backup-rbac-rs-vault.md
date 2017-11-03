---
title: "Gerir cópias de segurança com controlo de acesso baseado em funções do Azure | Microsoft Docs"
description: "Utilize o controlo de acesso baseado em funções para gerir o acesso às operações de gestão de cópia de segurança no Cofre de serviços de recuperação."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 3bd46b97-4b29-47a5-b5ac-ac174dd36760
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/22/2017
ms.author: trinadhk;markgal
ms.openlocfilehash: b6e4c6761e1bd5c17c9c3428491113042d3b1d31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Utilizar o controlo de acesso baseado em funções para gerir pontos de recuperação de cópia de segurança do Azure
O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso aos utilizadores que precisam para desempenhar as suas funções.

> [!IMPORTANT]
> Funções fornecidas pela cópia de segurança do Azure estão limitadas a ações que podem ser efetuadas no portal do Azure ou os cmdlets do PowerShell do cofre dos serviços de recuperação. Ações executadas no Azure cópia de segurança Centro de IU do agente de cliente ou sistema da IU do Gestor de proteção de dados ou a IU do servidor de cópia de segurança do Azure estão fora do controlo de uma destas funções.

Cópia de segurança do Azure fornece 3 funções incorporadas para controlar as operações de gestão de cópia de segurança. Saber mais sobre [funções incorporadas do RBAC do Azure](../active-directory/role-based-access-built-in-roles.md)

* [Cópia de segurança de contribuinte](../active-directory/role-based-access-built-in-roles.md#backup-contributor) -esta função tem todas as permissões para criar e gerir a cópia de segurança, exceto criar Cofre de serviços de recuperação e conceder acesso a outras pessoas. Imagine esta função de administrador de gestão de cópia de segurança que pode fazer cada operação de cópia de segurança de gestão.
* [Operador de cópia de segurança](../active-directory/role-based-access-built-in-roles.md#backup-operator) -esta função tem permissões para tudo contribuinte exceto a remoção de políticas de cópia de segurança e gestão. Esta função é equivalente ao Contribuidor, exceto que não é possível efetuar operações destrutivas como parar a cópia de segurança com eliminar dados ou remover registo de recursos no local.
* [Leitor de cópia de segurança](../active-directory/role-based-access-built-in-roles.md#backup-reader) -esta função tem permissões para ver todas as operações de gestão de cópia de segurança. Imagine desta função de ser uma pessoa de monitorização.

Se estiver à procura para definir as suas próprias funções para o controlo ainda mais, consulte como [criar funções personalizadas](../active-directory/role-based-access-control-custom-roles.md) no RBAC do Azure.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>O mapeamento de funções de cópia de segurança incorporadas para ações de gestão de cópia de segurança
A tabela seguinte captura as ações de gestão de cópia de segurança e correspondente função RBAC mínimo necessário para executar essa operação.

| Operação de gestão | Funções RBAC mínimo necessária |
| --- | --- |
| Criar cofre dos serviços de recuperação | Contribuidor no grupo de recursos do Cofre de |
| Ativar a cópia de segurança de VMs do Azure | Operador de cópia de segurança no cofre, contribuinte de Máquina Virtual em VMs |
| Cópia de segurança a pedido de VM | Operador de cópia de segurança |
| Restaurar a VM | Operador de cópia de segurança, contribuinte de grupo de recursos no qual VM e as Vnets que vão implementado |
| Restaurar discos, ficheiros individuais da cópia de segurança VM | Operador de cópia de segurança, contribuinte de Máquina Virtual em VMs |
| Criar política de cópia para cópia de segurança de VM do Azure | Cópia de segurança contribuinte |
| Modificar a política de cópia de segurança da cópia de segurança de VM do Azure | Cópia de segurança contribuinte |
| Eliminar política de cópia de segurança da cópia de segurança de VM do Azure | Cópia de segurança contribuinte |
| Pare a cópia de segurança (mantendo os dados ou eliminar dados) numa cópia de segurança VM | Cópia de segurança contribuinte |
| Registar no local Windows servidor SCDPM/cliente ou servidor de cópia de segurança do Azure | Operador de cópia de segurança |
| Eliminar registados no local Windows servidor SCDPM/cliente ou servidor de cópia de segurança do Azure | Cópia de segurança contribuinte |

## <a name="next-steps"></a>Passos seguintes
* [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-configure.md): começar a utilizar o RBAC no portal do Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [CLI do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [API REST](../active-directory/role-based-access-control-manage-access-rest.md)
* [Resolução de problemas de controlo de acesso baseado em funções](../active-directory/role-based-access-control-troubleshooting.md): Obtenha sugestões sobre como corrigir problemas comuns.

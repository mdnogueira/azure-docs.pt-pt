---
title: "Gerir o armazenamento do Azure com a automatização do Azure"
description: "Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir o armazenamento do Azure à escala."
services: storage, automation
documentationcenter: 
author: jodoglevy
manager: eamono
editor: 
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-storage-using-azure-automation"></a>Gerir o armazenamento do Azure através da automatização do Azure
Este guia apresenta-lhe o serviço de automatização do Azure e como podem ser utilizada para simplificar a gestão da sua blobs, tabelas e filas do Storage do Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A automatização do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar a gestão de nuvem através de automatização de processos. Utilizar a automatização do Azure, as tarefas de longa execução, manuais, propensas ao erro e frequentemente repetidas podem ser automatizadas para aumentar a fiabilidade e a eficiência e reduzir o tempo de valor para a sua organização.

A automatização do Azure fornece um motor de execução do fluxo de trabalho altamente fiáveis e elevada disponibilidade preparada para satisfazer as suas necessidades, à medida que aumenta a sua organização. Na automatização do Azure, os processos podem ser arrancou manualmente, por sistemas de terceiros 3rd ou em intervalos agendados para que as tarefas acontecer exatamente quando necessário.

Reduzir a sobrecarga operacional e libertar IT / equipa de DevOps focar-se no trabalho que adiciona o negócio value ao mover as tarefas de gestão de nuvem a ser executada automaticamente pela automatização do Azure.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Como pode que o automatização do Azure ajuda a gerir o armazenamento do Azure?
Armazenamento do Azure pode ser gerido na automatização do Azure, utilizando os cmdlets do PowerShell que estão disponíveis no [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). A automatização do Azure tem estes cmdlets PowerShell do armazenamento disponíveis a Box, para que possam executar todos os seus blob, tabela e tarefas de gestão de fila do serviço. Também pode ser emparelhado estes cmdlets na automatização do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas em todos os serviços do Azure e 3rd sistemas de terceiros.

O [Galeria de runbooks de automatização do Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks de Comunidade e a equipa de produto para começar a automatizar a gestão de armazenamento do Azure, outros serviços do Azure e sistemas de terceiros 3rd. Galeria runbooks incluem:

* [Remova os Blobs Storage do Azure são Certain dias antigo utilizando o serviço de automatização](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Transferir um Blob Storage do Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Todos os discos de cópia de segurança para uma única VM do Azure ou para todas as VMs num serviço em nuvem](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas de automatização do Azure e como podem ser utilizada para gerir os blobs, tabelas e filas de armazenamento do Azure, siga estas ligações para saber mais sobre a automatização do Azure.

Consulte o tutorial de automatização do Azure [criar ou importar um runbook na automatização do Azure](../../automation/automation-creating-importing-runbook.md).


---
title: "Utilizar o serviço importar/exportar do Azure REST API | Microsoft Docs"
description: "Saiba onde encontrar recursos para utilizar o serviço importar/exportar do Azure API REST, incluindo o material de referência e procedimentos."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: b780385ad0af34bcb15639683d1aa5d689b38b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Utilizar a API REST do serviço Importar/Exportar do Azure

O serviço de importação/exportação do Microsoft Azure expõe uma API REST para permitir o controlo programático das tarefas de importação/exportação. Pode utilizar a API REST para executar todas as operações de importação/exportação que pode realizar com o [portal do Azure](https://portal.azure.com/). Além disso, pode utilizar a API REST para efetuar determinadas operações granulares, como a percentagem de conclusão de uma tarefa, o que não está atualmente disponível no portal clássico do Azure a consultar.

Consulte [utilizando o serviço de importação/exportação do Microsoft Azure para transferir dados para o Blob Storage](../storage-import-export-service.md) para uma descrição geral do serviço de importação/exportação e um tutorial que demonstra como utilizar o portal clássico para criar e gerir importação e exportação tarefas.

## <a name="service-endpoints"></a>pontos finais de serviço

O serviço importar/exportar do Azure é um fornecedor de recursos do Azure Resource Manager e fornece um conjunto de REST APIs, o seguinte ponto final HTTPS para gerir as tarefas de importação/exportação:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Controlo de versões

Pedidos para o serviço de importação/exportação tem de especificar o `api-version` parâmetro e defina o respetivo valor `2016-11-01`.

## <a name="importexport-service-operations"></a>Operações de serviço para importar/exportar

[Criação de uma tarefa de importação](../storage-import-export-creating-an-import-job.md)

[Criação de uma tarefa de exportação](../storage-import-export-creating-an-export-job.md)

[Obter informações de estado para uma tarefa](storage-import-export-retrieving-state-info-for-a-job.md)

[Enumerar tarefas](../storage-import-export-enumerating-jobs.md)

[Cancelar e eliminar tarefas](storage-import-export-cancelling-and-deleting-jobs.md)

[Cópia de segurança manifestos de unidade](../storage-import-export-backing-up-drive-manifests.md)

[Recuperação de diagnósticos e erros para tarefas de Importação/Exportação](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Passos seguintes

* [Armazenamento para importar/exportar REST](/rest/api/storageimportexport)

---
title: "Lista todas as tarefas de importação/exportação do Azure | MicrosoftDocs"
description: "Saiba como lista de todas as tarefas de serviço do Azure para importar/exportar numa subscrição."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumerar tarefas no serviço do Azure para importar/exportar
Para enumerar todas as tarefas numa subscrição, chame o [tarefas lista](/rest/api/storageimportexport/jobs#Jobs_List) operação. `List Jobs`Devolve uma lista de tarefas, bem como os seguintes atributos:

-   O tipo de tarefa (importação ou exportação)

-   O estado atual da tarefa

-   A tarefa do associado à conta de armazenamento

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o REST API do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)

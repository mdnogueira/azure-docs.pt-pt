---
title: Evento completa eliminar o conjunto do Azure Batch | Microsoft Docs
description: "Referência para o conjunto do Batch eliminar eventos concluído."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: 890f2ba7fda37060c56177868d6214d517d91831
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="pool-delete-complete-event"></a>Evento de completa de eliminação do conjunto

 Este evento é emitido quando concluir uma operação de eliminação do conjunto.

 O exemplo seguinte mostra o corpo de um evento de conclusão de eliminação de agrupamento.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|ID|Cadeia|O id do conjunto.|
|startTime|DateTime|O tempo a eliminar o conjunto foi iniciado.|
|endTime|DateTime|A hora de eliminar o conjunto foi concluída.|

## <a name="remarks"></a>Observações
Para obter mais informações sobre os Estados e códigos de erro de operação de redimensionamento do conjunto, consulte [eliminar um conjunto de uma conta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
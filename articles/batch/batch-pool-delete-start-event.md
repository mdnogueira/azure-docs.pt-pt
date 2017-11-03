---
title: "Azure eventos de início do Batch conjunto eliminar | Microsoft Docs"
description: "Referência para o evento de início de eliminação de conjunto de Batch."
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
ms.openlocfilehash: f8a5241dce422e5c826ab428da6d7bc93284a1cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="pool-delete-start-event"></a>Eventos de início de eliminação do conjunto

 Este evento é emitido quando foi iniciada uma operação de eliminação do conjunto. Uma vez que a eliminação do conjunto é um evento assíncrono, que pode esperar um conjunto eliminação completa evento ser emitidos uma vez concluída a operação de eliminação.

 O exemplo seguinte mostra o corpo de um evento de início de eliminação do conjunto.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|ID|Cadeia|O id do conjunto.|
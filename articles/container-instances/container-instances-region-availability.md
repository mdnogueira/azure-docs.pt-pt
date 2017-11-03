---
title: "Disponibilidade do Azure de região e recursos de instâncias de contentor | Documentos do Azure"
description: "Detete as regiões do Azure suportam a implementação de instâncias de contentor e os limites de CPU e memória para as instâncias."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 2b9b1b864bbfd73383759212dd7d91f8e4941544
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Disponibilidade de região para instâncias de contentor do Azure

Durante a pré-visualização, instâncias de contentor do Azure estão disponíveis as seguintes regiões com a CPU especificada e os limites de memória.

| Localização | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| EUA Leste Europa Ocidental, EUA oeste, | Linux | 2 | 7 |
| EUA Leste Europa Ocidental, EUA oeste, | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>Disponibilidade de recursos

Instâncias de contentor criadas dentro estes limites de recursos estão sujeitos a disponibilidade numa região de implementação. Quando uma região esteja com muita carga, podem ocorrer uma falha quando implementar instâncias.

Para mitigar uma falha de implementação, experimente implementar instâncias com definições de memória e CPU inferior ou tente a sua implementação numa altura posterior.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre resolução de problemas de implementação de instância de contentor, consulte [resolver problemas de implementação com instâncias de contentor do Azure](container-instances-troubleshooting.md).
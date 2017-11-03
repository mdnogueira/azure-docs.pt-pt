---
title: "Repositórios de registo de contentor do Azure | Microsoft Docs"
description: "Como utilizar os repositórios de registo de contentor do Azure para imagens de Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.openlocfilehash: 2090d4c951e2261529bf1b7b361510d5822060a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Repositórios de registo de contentor do Azure

Os registos do contentor do Azure são compatíveis com uma inúmeras orchestrators e serviços. Para tornar mais fácil controlar os serviços de origem e os agentes a partir do qual é utilizado o ACR, ter iniciamos utilizando o campo de cabeçalho de Docker no ficheiro Docker.config.



## <a name="viewing-repositories-in-the-portal"></a>Repositórios de visualização no Portal

Os cabeçalhos ACR seguem o formato:
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Nuvem: Azure, Azure pilha, ou outros government ou nuvens do Azure específico do país. Embora a pilha do Azure e government nuvens não são atualmente suportadas, este parâmetro permite suporte futuro.
* Serviço: nome do serviço.
* Optionalservicename: o parâmetro opcional para os serviços com subservices ou especificar um SKU (ex: correspondem as web apps do Azure /-/ web-apps do app service).

Serviços de parceiros e orchestrators são encouraged para utilizar os valores de cabeçalho específicos para o ajudar com a nossa telemetria. Os utilizadores também podem modificar o valor transmitido ao cabeçalho caso o pretendam por isso.

Os valores queremos parceiros ACR a utilizar para preencher o campo "Client-X-Meta-origem" estão abaixo:

| Nome do Serviço              | Cabeçalho                                |
| ------------------------- | ------------------------------------- |
| Azure Container Service   | / computação/azure--serviço de contentor Azure |
| Serviço de aplicações - as Web Apps    | Azure /-/ web-apps do app service            |
| Serviço de aplicações - as Logic Apps  | Azure /-/ logic-apps do app service          |
| Batch                     | computação/Azure/batch                   |
| Consola de nuvem             | Azure/nuvem-consola                   |
| Funções                 | / computação/das funções do Azure               |
| Internet das coisas - Hub  | Azure/iothub                         |
| HDInsight                 | dados/Azure/hdinsight                  |
| Jenkins                   | Azure/jenkins                         |
| Machine Learning          | Azure/dados/machile-learning           |
| Service Fabric            | / computação/service fabric do Azure          |
| VSTS                      | Azure/vsts                            |


## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre os registos e a serviços suportados e orchestrators](container-registry-intro.md)

---
title: "Definine e gerir o estado no Azure micro-serviços | Microsoft Docs"
description: "Como definir e gerir o estado do serviço no Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 103fd6c3d536bc11f4e39444043a332a1d8f6c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-state"></a>Estado do serviço
**Estado do serviço** refere-se para a memória ou nos dados de disco que necessita de um serviço de função. Inclui, por exemplo, as estruturas de dados e variáveis de membro que o serviço de leituras e escritas para trabalhar. Dependendo de como o serviço é criado, também pode incluir ficheiros ou outros recursos que são armazenados no disco. Por exemplo, os ficheiros de uma base de dados utilizará para armazenar os registos de dados e a transação.

Como um serviço de exemplo, vamos considere uma calculadora. Um serviço de calculadora básica utiliza dois números e devolve os respetivos soma. Este cálculo a efetuar envolve nenhuma variável de membro ou outras informações.

Agora, considere a Calculadora mesma, mas com um método adicional para armazenar e devolver a soma de último tem calculada. Este serviço está agora com monitorização de estado. Com monitorização de estado significa contém algum Estado que, quando escreve calcula a soma de novo e lê a partir da quando a peça para devolver a soma de calculada último.

No Azure Service Fabric, o primeiro serviço chama-se um serviço sem estado. O serviço do segundo denomina-se um serviço com monitorização de estado.

## <a name="storing-service-state"></a>Armazenar o estado do serviço
Estado pode ser externalized ou localizado conjuntamente com o código que é a manipulação de estado. Externalization de estado é geralmente feito utilizando uma base de dados externo ou noutro arquivo de dados que é executado em computadores diferentes através da rede ou fora do processo no mesmo computador. No nosso exemplo de calculadora, o arquivo de dados pode ser uma base de dados do SQL Server ou a instância do arquivo de tabela do Azure. Todos os pedidos para a soma de computação efetua uma atualização, estes dados e os pedidos para o serviço para devolver o resultado de valor num valor atual a ser obtido a partir da loja. 

Também pode ser localizado conjuntamente com o código que manipula o estado de estado. Serviços com monitorização de estado no Service Fabric são normalmente criados utilizando este modelo. O Service Fabric fornece a infraestrutura para se certificar de que este estado é altamente disponível, consistente e durável e de que os serviços incorporados desta forma podem Dimensionar facilmente.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Service Fabric, consulte os artigos seguintes:

* [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
* [Escalabilidade dos serviços do Service Fabric](service-fabric-concepts-scalability.md)
* [A criação de partições de serviços do Service Fabric](service-fabric-concepts-partitioning.md)
* [Serviços do Service Fabric fiável](service-fabric-reliable-services-introduction.md)

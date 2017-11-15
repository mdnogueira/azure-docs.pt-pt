---
title: Cadeia do connnection de arquivo de imagem do Azure Service Fabric | Microsoft Docs
description: "Compreender a cadeia de ligação do arquivo de imagens"
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: 
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: alexwun
ms.openlocfilehash: 723cf7d98022e8bbb4f1e3c8c0836a4c53c6a078
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Compreender a definição de ImageStoreConnectionString

Em algumas das nossa documentação, iremos brevemente mencionar a existência de um parâmetro de "ImageStoreConnectionString" sem que descrevem o que realmente significa. E depois vai através de um artigo como [implementar e remover aplicações utilizando o PowerShell][10], aspeto que tem tudo o que fazer é o valor de copiar/colar como é apresentado no manifesto do cluster do cluster de destino. Para a definição tem de ser configurável por cluster, mas quando criar um cluster através de [portal do Azure][11], não existe nenhuma opção para configurar esta definição e está sempre "fabric: arquivo de imagens". O que é o objetivo, em seguida, esta definição?

![Manifesto do cluster][img_cm]

Service Fabric iniciado como uma plataforma de consumo interno do Microsoft por várias equipas diversificadas, pelo que alguns aspetos do mesmo são altamente personalizáveis - o "arquivo de imagens" é um desse aspeto. Essencialmente, o arquivo de imagens é um repositório incorporável para armazenar pacotes de aplicações. Quando a aplicação for implementada para um nó do cluster, esse nó transfere os conteúdos do seu pacote de aplicação a partir do arquivo de imagens. O ImageStoreConnectionString é uma definição que inclui todas as informações necessárias para clientes e nós localizar o arquivo de imagens correto para um determinado cluster.

Existem três tipos possíveis de fornecedores de arquivo de imagens e os respetivos correspondente cadeias de ligação são os seguintes:

1. Serviço de arquivo de imagens: "fabric: arquivo de imagens"

2. Sistema de ficheiros: "caminho do sistema file:[file]"

3. Storage do Azure: "xstore:DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Contentor = [...] "

O tipo de fornecedor utilizado na produção é o serviço de arquivo de imagem, que é um serviço de sistema persistente com monitorização de estado que pode ver a partir do Explorador de recursos de infraestrutura de serviço. 

![Serviço de arquivo de imagens][img_is]

Que aloja o arquivo de imagens num serviço de sistema do cluster em si elimina dependências externas para o repositório de pacote e nos dá mais controlo sobre a localização de armazenamento. Melhoramentos futuros à volta o arquivo de imagens são provável que o fornecedor de arquivo de imagens de destino, primeiro, caso contrário, exclusivamente. A cadeia de ligação para o fornecedor de serviço de arquivo de imagens não tem qualquer informação exclusiva, uma vez que o cliente já está ligado ao cluster de destino. O cliente só tem de saber que os protocolos direcionada para o serviço de sistema devem ser utilizados.

O fornecedor do sistema de ficheiros é utilizado em vez do serviço de arquivo de imagens para clusters de caixa de um locais, durante o desenvolvimento arranque ligeiramente mais rapidamente o cluster. A diferença é pequena, normalmente, mas é uma otimização útil para a maioria dos utilizadores durante o desenvolvimento. É possível implementar um cluster de caixa de um local com os outros fornecedor tipos de armazenamento bem, mas não existe, normalmente, nenhum motivo para tal, uma vez que o fluxo de trabalho de desenvolver/teste permanece igual independentemente do fornecedor. Diferente esta utilização, os fornecedores de sistema de ficheiros e armazenamento do Azure existem apenas para o suporte legacy.

Por isso, enquanto o ImageStoreConnectionString é configurável, é geralmente apenas utilizar a predefinição. Quando a publicação no Azure através do Visual Studio, o parâmetro é automaticamente definido para si em conformidade. Para a implementação programática para clusters alojado no Azure, a cadeia de ligação é sempre "fabric: arquivo de imagens". Embora quando em dúvida, o valor sempre pode ser verificado ao obter o manifesto do cluster por [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), ou [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Teste no local e clusters de produção, devem sempre ser configuradas para utilizar o fornecedor de serviço de arquivo de imagens.

### <a name="next-steps"></a>Passos seguintes
[Implementar e remover aplicações utilizando o PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md


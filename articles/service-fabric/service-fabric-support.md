---
title: "Saiba mais sobre as opções de suporte de recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Pedidos de suporte do Azure versões de cluster do Service Fabric suportadas e ligações para o ficheiro"
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2017
ms.author: pkc
ms.openlocfilehash: a3ab41d1aa6051e5d4021443c3fe581cbf4c880f
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-service-fabric-support-options"></a>Opções de suporte do Azure Service Fabric

Para fornecer o suporte adequado para os clusters de Service Fabric que estejam a executar as cargas de trabalho de aplicações, ter configuramos várias opções para si. Consoante o nível de suporte necessário e a gravidade do problema, obter e escolha as opções corretas. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Comunicar problemas de produção ou de pedido de suporte pago do Azure

Para comunicar problemas no seu cluster de Service Fabric implementado no Azure, abra um pedido de suporte [no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou [portal de suporte da Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:
 
- [Suporte da Microsoft para o Azure](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Suporte da Microsoft premier](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Comunicar problemas de produção ou de pedido de suporte pago autónomo que clusters do Service Fabric

Para os relatórios de problemas no seu cluster do Service Fabric implementado no local ou outras nuvens, abra um pedido de suporte profissional no [portal de suporte da Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:

- [Suporte profissional da Microsoft para on-premises](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Suporte da Microsoft premier](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Problemas de relatório do Azure Service Fabric 
Ter configuramos um repositório do GitHub para relatórios de problemas do Service Fabric.  Podemos também ativamente está a monitorizar os fóruns do seguintes.

### <a name="github-repo"></a>Repositório do GitHub 
Comunicar problemas de recursos de infraestrutura de serviço do Azure no [repositório de git de problemas de recursos de infraestrutura de serviço](https://github.com/Azure/service-fabric-issues). Este repositório destina-se de relatórios e controlo problemas com o Azure Service Fabric e para efetuar pedidos de funcionalidades pequeno. **Não utilize esta opção para problemas de site em direto de relatório**.

### <a name="stackoverflow-and-msdn-forums"></a>Fóruns StackOverflow e MSDN
O [etiquetas de Service Fabric no StackOverflow] [ stackoverflow] e o [fórum de Service Fabric no MSDN] [ msdn-forum] são melhor utilizada para fazer perguntas sobre como funciona a plataforma e como pode realizar algumas tarefas com o mesmo.

### <a name="azure-feedback-forum"></a>Fórum de comentários do Azure
O [fórum de comentários do Azure para o Service Fabric] [ uservoice-forum] é o melhor local para submeter ideias grande funcionalidade tiver para o produto como podemos rever os pedidos mais populares fazem parte do nosso média para planear a longo prazo. Aconselhamo-lo para rally suporte para as sugestões dentro da Comunidade.


## <a name="supported-service-fabric-versions"></a>Versões suportadas do Service Fabric.

Certifique-se de que o cluster sempre está em execução uma versão suportada do Service Fabric. Como e quando é anunciar o lançamento da nova versão do Service Fabric, a versão anterior está marcada para fim de suporte após 60 dias dessa data, no mínimo. Os novos lançamentos sejam anunciados [no blogue de equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte os seguintes documentos em obter detalhes sobre como manter o seu cluster executar uma versão suportada do Service Fabric.

- [Atualizar a versão de Service Fabric num cluster do Azure](service-fabric-cluster-upgrade.md)
- [Atualizar a versão de Service Fabric num cluster de servidores windows autónomo](service-fabric-cluster-upgrade-windows-server.md)
 
Seguem-se a lista das versões do Service Fabric que são suportadas e as respetivas datas de fim de suporte.

| **Tempo de execução do Service Fabric no cluster** | **Pode atualizar diretamente a partir da versão do cluster** |**SDK compatível / versões do pacote NuGet** | **Fim do suporte de data** |
| --- | --- |--- | --- |
| Todas as versões de cluster antes de 5.3.121 | 5.1.158* |Menor ou igual a versão 2.3 |20 de Janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menor ou igual a versão 2.3 |24 de Fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menor ou igual a versão 2.4 |Pode 10,2017       |
| 5.5.* | 5.4.164.* |Menor ou igual a versão 2.5 |Agosto 10,2017    |
| 5.6.* | 5.4.164.* |Menor ou igual a versão 2.6 |Outubro 13,2017   |
| 5.7.* | 5.4.164.* |Menor ou igual a versão 2.7 |Dezembro 15,2017  |
| 6.0.* | 5.6.205.* |Menor ou igual a versão 2.8 |Versão atual e, por isso, sem data de fim |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versões de pré-visualização de recursos de infraestrutura do serviço - não suportadas para utilização em produção.
Ocasionalmente, Lançamos versões com funcionalidades significativas queremos comentários, que são lançadas como pré-visualizações. Estas versões de pré-visualização só devem ser utilizadas para fins de teste. O cluster de produção deve sempre ser em execução uma versão de Service Fabric suportada e estável. Uma versão de pré-visualização sempre começa com um número de versão principal e secundária de 255. Por exemplo, se vir uma versão 255.255.5703.949 do Service Fabric, essa versão de lançamento só deve ser utilizado em clusters de teste e está em pré-visualização. Estas versões de pré-visualização também sejam anunciadas no [blogue da equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) e irá ter detalhes sobre as funcionalidades incluídas.

Não há nenhuma opção de suporte pago para estas versões de pré-visualização. Utilize uma das opções listadas em [relatório Azure Service Fabric emite](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) para colocar questões ou comentários.

## <a name="next-steps"></a>Passos seguintes

- [Versão de recursos de infraestrutura de serviço de atualização num cluster do Azure](service-fabric-cluster-upgrade.md)
- [Atualizar a versão de Service Fabric num cluster de servidores windows autónomo](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples

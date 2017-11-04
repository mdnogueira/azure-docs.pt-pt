---
title: "Versões de API do Azure Search | Microsoft Docs"
description: "Política de versão para as APIs REST do Azure Search e a biblioteca de clientes no SDK do .NET."
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 0458053a-164e-4682-a802-00097ecde981
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.openlocfilehash: 4b4cb2d168676d52a90942a116905e6669b16639
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="api-versions-in-azure-search"></a>Versões de API na Azure Search
A pesquisa do Azure lança atualizações de funcionalidade regularmente. Por vezes, mas nem sempre, estas atualizações requerem-nos publicar uma nova versão da nossa API para preservar a compatibilidade com versões anteriores. Publicar uma nova versão permite-lhe controlar quando e como integrar atualizações de serviço de pesquisa no seu código.

Como uma regra, vamos tentar publicar novas versões apenas quando necessário, uma vez que pode envolver algum esforço para atualizar o seu código para utilizar uma nova versão de API. Só podemos irá publicar uma nova versão se é necessário alterar algum aspeto da API de uma forma de quebras de retro-compatibilidade. Isto pode acontecer devido a correções a funcionalidades existentes, ou devido a novas funcionalidades que altere a área de superfície de API existente.

Iremos siga a mesma regra para atualizações SDK. O SDK de pesquisa do Azure segue a [versioning semântica](http://semver.org/) regras, o que significa que a versão tem três partes: principal, secundária e (por exemplo, 1.1.0) do número de compilação. Iremos vai lançar uma versão principal nova do SDK só em caso de alterações que interromper a compatibilidade com versões anteriores. Para as atualizações de funcionalidade sem quebra, iremos irá incrementar a versão secundária e para correções irá aumentar apenas a versão de compilação.

> [!NOTE]
> A instância de serviço de pesquisa do Azure suporta várias versões de REST API, incluindo o mais recente. Pode continuar a utilizar uma versão quando já não é um mais recente, mas recomendamos que migre código para utilizar a versão mais recente. Quando utilizar a API REST, tem de especificar a versão da API em cada pedido através do parâmetro de versão da api. Ao utilizar o SDK .NET, a versão do SDK está a utilizar determina a versão da REST API correspondente. Se estiver a utilizar um SDK anterior, pode continuar a executar esse código sem alterações, mesmo se o serviço é atualizado para suportar uma versão mais recente da API.

## <a name="snapshot-of-current-versions"></a>Instantâneo de versões atuais
Abaixo é um instantâneo das versões atuais do todas programação interfaces para a Azure Search.

| Interfaces | Mais recente versão principal | Estado |
| --- | --- | --- |
| [SDK do .NET](https://aka.ms/search-sdk) |3.0 |Geralmente disponível, lançada Novembro de 2016 |
| [Pré-visualização do SDK .NET](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Pré-visualização, lançada Maio de 2017 |
| [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |Geralmente disponível |
| [Pré-visualização da API de REST do serviço](search-api-2016-09-01-preview.md) |2016-09-01-preview |Pré-visualização |
| [SDK de gestão de .NET](https://aka.ms/search-mgmt-sdk) |2015-08-19 |Geralmente disponível |
| [API REST de Gestão](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Geralmente disponível |

Para as APIs REST, incluindo o `api-version` em cada chamada é necessária. Isto torna mais fácil para uma versão específica, tal como uma pré-visualização API de destino. O exemplo a seguir ilustra como o `api-version` parâmetro for especificado:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> Apesar de cada pedido tem um `api-version`, recomendamos que utilize a mesma versão para todos os pedidos de API. Isto é especialmente verdadeiro quando novas versões de API introduzem atributos ou operações que não são reconhecidas por versões anteriores. A combinação de versões de API pode ter consequências inesperadas e devem ser evitados.
>
> A API REST do serviço e a API de REST de gestão são com versão independentemente entre si. Qualquer semelhança números de versão é coincidental.

Geralmente disponível (ou GA) APIs pode ser utilizadas em produção e estão sujeitos aos contratos de nível de serviço do Azure. Versões de pré-visualização têm experimental funcionalidades que não são migradas sempre para uma versão GA. **Aconselhamos vivamente contra a utilização de APIs de pré-visualização em aplicações de produção.**

## <a name="about-preview-and-generally-available-versions"></a>Sobre as versões de pré-visualização e geralmente disponível
A pesquisa do Azure sempre previamente versões funcionalidades experimental através da API REST em primeiro lugar, em seguida, através de versões de pré-lançamento do SDK do .NET.

Não são garantidas que funcionalidades de pré-visualização para ser migrada para uma versão GA. Enquanto que as funcionalidades numa versão GA são consideradas estável e pouco provável alterar com a exceção pequenas correções de retro-compatibilidade e melhoramentos, funcionalidades de pré-visualização estão disponíveis para teste e a experimentação, com o objetivo de recolha de comentários sobre a funcionalidade de design e implementação.

No entanto, como funcionalidades de pré-visualização estão sujeitos a alterações, recomendamos relativamente ao escrever o código de produção que assume uma dependência em versões de pré-visualização. Se estiver a utilizar uma versão de pré-visualização mais antiga, recomendamos a migrar para a versão (GA) geralmente disponível.

Para o SDK .NET: orientações para a migração de código podem ser encontradas em [atualizar o SDK .NET](search-dotnet-sdk-migration.md).

Disponibilidade geral significa que da Azure Search agora sob o contrato de nível de serviço (SLA). É possível localizar o SLA [contratos de nível de serviço de pesquisa do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

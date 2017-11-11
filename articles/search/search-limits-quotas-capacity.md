---
title: "Serviço limites na Azure Search | Microsoft Docs"
description: "Limites de serviço utilizados para o planeamento de capacidade e os limites máximos nos pedidos e respostas de pesquisa do Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 3deb0ff81114c840798c5927ad7311d7e603813d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="service-limits-in-azure-search"></a>Limites de serviço da Azure Search
Máximo limita-se no armazenamento, as cargas de trabalho e quantidades de índices, documentos, e outros objetos dependem se [aprovisionar da Azure Search](search-create-service-portal.md) num **livres**, **básico**, ou **padrão** escalão de preço.

* **Livre** é um serviço partilhado do multi-inquilino que vem com a sua subscrição do Azure. 
* **Básico** fornece recursos de informática dedicados para cargas de trabalho de produção em escala mais pequena.
* **Standard** é executado em máquinas dedicadas com mais capacidade de armazenamento e processamento em cada nível. Standard, é apresentada no quatro níveis: S1, S2, S3 e S3 alta densidade (S3 HD).

> [!NOTE]
> Um serviço é aprovisionado em nenhum escalão específico. Jumping camadas para obterem capacidade envolve o aprovisionamento de um novo serviço (não há nenhuma atualização no local). Para obter mais informações, consulte [escolha um SKU ou camada](search-sku-tier.md). Para obter mais informações sobre como ajustar capacidade dentro de um serviço que já tenha sido já aprovisionada, consulte [Dimensionar níveis de recursos de consulta e cargas de trabalho de indexação](search-capacity-planning.md).
>

## <a name="per-subscription-limits"></a>Por limites de subscrição
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="per-service-limits"></a>Por limites de serviço
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="per-index-limits"></a>Por limites de índice
Há uma correspondência de e-mail de um para um entre limites em índices e limites de indexadores. Tendo em conta um limite de 200 índices, o limite máximo de indexadores também é 200 para o mesmo serviço.

| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| O índice: campos máximos por índice |1000 |100 <sup>1</sup> |1000 |1000 |1000 |1000 |
| Índice: máximo perfis por índice de classificação |100 |100 |100 |100 |100 |100 |
| O índice: funções máximas por perfil |8 |8 |8 |8 |8 |8 |
| Indexadores: máxima carga indexação por invocação |10 000 documentos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |N/D <sup>2</sup> |
| Indexadores: tempo de execução máximo | 1 a 3 minutos <sup>3</sup> |24 horas |24 horas |24 horas |24 horas |N/D <sup>2</sup> |
| Indexador de blob: o tamanho máximo de blob, MB |16 |16 |128 |256 |256 |N/D <sup>2</sup> |
| Indexador de blob: número máximo de conteúdo extraídos de um blob caracteres |32,000 |64,000 |milhões de 4 |milhões de 4 |milhões de 4 |N/D <sup>2</sup> |

<sup>1</sup> escalão básico destina o SKU apenas com um limite inferior de 100 campos por índice.

<sup>2</sup> S3 HD atualmente não suporta indexadores. Contacte o suporte do Azure se tiver uma necessidade urgente para esta capacidade.

<sup>3</sup> indexador tempo de execução máximo para o escalão gratuito é 3 minutos para as origens de blob e 1 minuto para todas as outras origens de dados.

## <a name="document-size-limits"></a>Limites de tamanho do documento
| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Tamanho de documentos individuais por API de índice |< 16 MB |< 16 MB |< 16 MB |< 16 MB |< 16 MB |< 16 MB |

Refere-se ao tamanho máximo do documento ao chamar uma API de índice. O tamanho do documento é, na verdade, de um limite no tamanho do corpo do pedido de API do índice. Uma vez que pode passar um lote de vários documentos para a API de índice de uma só vez, o limite de tamanho, na verdade, depende de documentos quantos são no lote. Para um lote com um único documento, o tamanho máximo do documento é 16 MB de JSON.

Para manter o tamanho do documento para baixo, lembre-se excluir os dados não consultável do pedido. Imagens e outros dados binários não são diretamente consultável e não devem ser armazenados no índice. Para integrar os dados não consultável os resultados da pesquisa, defina um campo não pesquisáveis que armazena uma referência de URL para o recurso.

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

Calcula QPS tem de ter sido programada independentemente por cada cliente. Índice de tamanho e complexidade, tamanho de consulta e complexidade e a quantidade de tráfego são determinants primários de QPS. Não é possível para oferecer estimativas significativas quando esses fatores são desconhecidos.

Calcula é mais previsível quando calculado nos serviços em execução em recursos dedicados (escalões básico e padrão). Pode fazer a estimativa QPS mais detalhadamente porque tem controlo sobre mais dos parâmetros. Para obter orientações sobre a estimativa da abordagem, consulte [desempenho de pesquisa do Azure e a otimização de](search-performance-optimization.md).

## <a name="api-request-limits"></a>Limites de pedido de API
* Máximo de 16 MB por pedido <sup>1</sup>
* Comprimento máximo do URL de 8 KB
* Documentos máximo de 1000 por lote de índice carrega, intercala ou elimina
* Campos máximos de 32 na cláusula $orderby
* Tamanho do termo de pesquisa máximo é 32,766 bytes (32 KB menos de 2 bytes) de texto codificado UTF-8

<sup>1</sup> na Azure Search, o corpo de um pedido está sujeita a um limite superior de 16 MB, impor um limite prático no conteúdo do campos individuais ou de coleções que caso contrário, não estão restritos pelo teórico limites (consulte [tipos de dados suportados](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter mais informações sobre restrições de composição de campo e).

## <a name="api-response-limits"></a>Limites de resposta de API
* Máximos 1000 documentos devolvidos por página de resultados de pesquisa
* Sugestões de 100 máximos devolvidos por pedido de API Sugerir

## <a name="api-key-limits"></a>Limites de chave de API
Chaves de API são utilizadas para autenticação do serviço. Existem dois tipos. Chaves de administração são especificadas no cabeçalho do pedido e concedam acesso de leitura e escrita completa para o serviço. Chaves de consulta são só de leitura, especificado no URL e, normalmente distribuídas por aplicações cliente.

* Máximo de 2 chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço

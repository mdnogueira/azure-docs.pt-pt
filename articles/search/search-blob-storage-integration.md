---
title: "A adição de pesquisa do Azure para o Blob Storage | Microsoft Docs"
description: "Crie um índice no código utilizando a API REST de HTTP da Azure Search."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.openlocfilehash: 15469e8a2d28bdf00d6e8d8c9f823c51975ee90e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="searching-blob-storage-with-azure-search"></a>Pesquisar o armazenamento de Blobs com o Azure Search

Procurar em toda a variedade de tipos de conteúdo armazenado no armazenamento de Blobs do Azure pode ser um problema difíceis de resolver. No entanto, pode indexar e pesquisa o conteúdo dos Blobs em apenas alguns cliques através da utilização da Azure Search. A procurar através de armazenamento de BLOBs requer o aprovisionamento de um serviço da Azure Search. As várias limites de serviço e escalões de preços de pesquisa do Azure podem ser encontrados no [página de preços](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>O que é o Azure Search?
[A pesquisa do Azure](https://aka.ms/whatisazsearch) é um serviço de pesquisa que torna mais fácil para os programadores adicionar experiências de pesquisa de texto completo robusta para aplicações móveis e web. Como um serviço da Azure Search remove a necessidade de gerir a qualquer infra-estrutura de pesquisa ao oferta um [SLA de disponibilidade de 99,9%](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>O índice e pesquisa formatos de documento de enterprise
Com suporte para [documentar extração](https://aka.ms/azsblobindexer) no Blob storage do Azure, pode indexar o seguinte conteúdo:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Por extrair texto e metadados destes tipos de ficheiro, pode procurar em vários formatos de ficheiro com uma única consulta. 

## <a name="search-through-your-blob-metadata"></a>Procurar os seus metadados do blob
Um cenário comum que torna mais fácil ordenar através de blobs de qualquer tipo de conteúdo é índice metadados personalizados e propriedades do sistema de cada blob. Desta forma, as informações de todos os blobs estão indexadas independentemente do tipo de documento. Em seguida, pode avançar para ordenação, filtro e aspeto de restrições em todos os conteúdos de armazenamento de Blobs.

[Saiba que mais sobre a indexação de metadados do blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Pesquisa de imagem
Pesquisa em texto completo, navegação por facetas e capacidades de ordenação de pesquisa do Azure agora podem ser aplicadas aos metadados de imagens armazenados em blobs.

Se estas imagens são pré-processadas utilizando o [computador visão API](https://www.microsoft.com/cognitive-services/computer-vision-api) de serviços cognitivos da Microsoft, em seguida, é possível indexar o visual foi encontrado em cada imagem incluindo reconhecimento OCR e de escrita de conteúdo. Estamos a trabalhar para adicionar OCR e outras capacidades de processamento de imagem diretamente para a Azure Search, se estiver interessado nestas capacidades, submeter um pedido no nosso [UserVoice](https://aka.ms/azsuv) ou [-nos de correio eletrónico](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>O índice e a pesquisa através de blobs JSON
A pesquisa do Azure pode ser configurada para extrair conteúdo estruturado encontrado em blobs que contêm JSON. A pesquisa do Azure pode ser de leitura de blobs JSON e analisar o conteúdo do estruturados nos campos adequados de um documento de pesquisa do Azure. A pesquisa do Azure também pode tirar blobs que contenham uma matriz de objetos JSON e cada elemento do mapa para um documento de Azure Search separado.

Análise de JSON não é atualmente configurável através do portal. [Saiba mais sobre a análise de JSON na Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Início rápido
A pesquisa do Azure pode ser adicionada para blobs diretamente a partir da página de portal de armazenamento de Blobs.

![](./media/search-blob-storage-integration/blob-blade.png)

Clique em **adicionar Azure Search** para iniciar um fluxo de onde pode selecionar um serviço de pesquisa do Azure existente ou criar um novo serviço. Se criar um novo serviço, são navegar fora de experiência do portal da sua conta de armazenamento. Pode navegue de volta para a página de portal de armazenamento e selecione novamente a **adicionar Azure Search** opção, onde pode selecionar o serviço existente.

### <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o indexador de Blob de pesquisa do Azure no completo [documentação](https://aka.ms/azsblobindexer).

---
title: "A pesquisa do Azure serviço REST versão da API 2016-09-01-pré-visualização | Microsoft Docs"
description: "Azure pesquisa serviço REST versão da API 2016-09-01-Preview inclui funcionalidades experimental como sinónimos e moreLikeThis pesquisas."
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>API de REST do serviço de pesquisa do Azure: Versão 2016-09-01-Preview
Este artigo é a documentação de referência para `api-version=2016-09-01-Preview`. Esta pré-visualização expande a versão atual do geralmente disponível, [api-version = 2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), fornecendo as seguintes funcionalidades experimental:

* [API de sinónimos](search-synonyms.md) para carregar o sinónimo maps e expandir a pesquisa.
* [`moreLikeThis`parâmetro de consulta](search-more-like-this.md) localizar documentos que são relevantes para um documento específico.

Certifique-se para a versão de pré-visualização API de destino `api-version=2016-09-01-Preview` para experimentar estas funcionalidades experimental. O exemplo a seguir ilustra como a pré-visualização versão da api é especificada em efetuar uma consulta mais-como-este.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Funcionalidades de pré-visualização estão disponíveis para testar e experiência com o objetivo de recolha de comentários e estão sujeitos a alterações. **Aconselhamos vivamente contra a utilização de APIs de pré-visualização em aplicações de produção.**

Serviço de pesquisa do Azure está disponível em múltiplas versões. Consulte [controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter mais detalhes.

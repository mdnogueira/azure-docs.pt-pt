---
title: "Atualizar para a API de REST de serviço de pesquisa do Azure versão 2016-09-01 | Microsoft Docs"
description: "Atualizar para a API de REST de serviço de pesquisa do Azure versão 2016-09-01"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Atualizar para a API de REST de serviço de pesquisa do Azure versão 2016-09-01
Se estiver a utilizar a versão 2015-02-28 ou de pré-visualização 2015-02-28-do [API de REST do serviço de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a próxima versão de API geralmente disponível, 2016-09-01.

Versão 2016-09-01 da REST API contém algumas alterações de versões anteriores. Estes são principalmente retrocompatível, para que alterar o seu código deverá ser preciso apenas esforço, consoante a versão que estava a utilizar antes. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o código para utilizar a nova versão de API.

> [!NOTE]
> A instância de serviço de pesquisa do Azure suporta várias versões de REST API, incluindo o mais recente. Pode continuar a utilizar uma versão quando já não é um mais recente, mas recomendamos que migre código para utilizar a versão mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>Novidades na versão 2016-09-01
Versão 2016-09-01 é a segunda versão geralmente disponível da API de REST do serviço de pesquisa do Azure. Novas funcionalidades nesta versão de API incluem:

* [Analisadores personalizados](https://aka.ms/customanalyzers), que permitem-lhe assumir o controlo sobre o processo de conversão de texto em tokens indexable e pesquisáveis.
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) e [Table Storage do Azure](search-howto-indexing-azure-tables.md) indexadores, que lhe permitem facilmente importar dados de armazenamento do Azure na Azure Search num agendamento ou a pedido.
* [Campo mapeamentos](search-indexer-field-mappings.md), que permitem-lhe personalizar a forma como os indexadores importar os dados na Azure Search.
* Etags são, que lhe permitem atualizar as definições de índices, indexadores e origens de dados de forma segura de concorrência. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Se estiver a atualizar a partir da versão 2015-02-28, provavelmente, não terá efetue as alterações ao código, diferente de alterar o número de versão. As únicas situações em que poderá ter de alterar o código de são quando:

* O código de falha quando uma resposta de API, são devolvidas propriedades não reconhecidas. Por predefinição, a aplicação deve ignorar propriedades que não compreender.
* O código de persistir pedidos de API e tenta reenviá-los para a nova versão de API. Por exemplo, isto pode acontecer se a aplicação persistir tokens de continuação devolvidos a partir da API de pesquisa (para obter mais informações, procure `@search.nextPageParameters` no [referência da API de pesquisa](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Se qualquer uma destas situações se aplicar ao utilizador, poderá ter de alterar o código em conformidade. Caso contrário, não existem alterações devem ser necessárias a menos que pretenda começar a utilizar o [novas funcionalidades](#WhatsNew) da versão 2016-09-01.

Se estiver a atualizar a partir da versão 2015-02-28-pré-visualização, acima também se aplicam, mas deve também estar ciente de que algumas funcionalidades de pré-visualização não estão disponíveis na versão 2016-09-01:

* Suporte do indexador de armazenamento de Blobs do Azure para blobs que contém matrizes JSON e de ficheiros CSV.
* Sinónimos
* Consultas de "Mais como"

Se o seu código utiliza estas funcionalidades, não poderá atualizar para 2016-09-01 sem remover a sua utilização dos mesmos.

> [!IMPORTANT]
> . Lembre-se, pré-visualizar APIs foram concebidas para avaliação e de teste e não deve ser utilizadas em ambientes de produção.
> 
> 

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como utilizar a API de REST do serviço de pesquisa do Azure, consulte recentemente atualizado [referência da API](https://msdn.microsoft.com/library/azure/dn798935.aspx) no MSDN.

Apreciamos os seus comentários na Azure Search. Se tiver problemas, não hesite e peça-nos para obter ajuda no [fórum MSDN de pesquisa do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) ou [StackOverflow](http://stackoverflow.com/). Se está a solicitar uma pergunta sobre a Azure Search no StackOverflow, certifique-se a etiqueta com `azure-search`.

Obrigado por utilizar a pesquisa do Azure!


---
title: "Corte de segurança com a pesquisa do Azure"
description: "Implementar o corte de segurança utilizando o filtro de pesquisa do Azure."
ms.custom: 
ms.date: 08/07/2017
ms.service: search
ms.reviewer: 
ms.suite: 
ms.tgt_pltfrm: 
caps.latest.revision: "26"
author: revitalbarletz
ms.author: revitalb
manager: jlembicz
ms.openlocfilehash: 28f400abbafcc3cff35bd4575b975c1e7150a6f0
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="security-trimming-with-azure-search"></a>Corte de segurança com a pesquisa do Azure

Pode aplicar filtros de segurança nos resultados de pesquisa para restringir o acesso de documentos com base na identidade do utilizador. Esta experiência de pesquisa, geralmente, necessita de comparar a identidade de quem os pedidos de pesquisa em relação a um campo que contém os princípios de quem tem permissões para o documento. Quando é encontrada uma correspondência, o principal (por exemplo, um grupo ou função) ou o utilizador tem acesso a esse documento.

Uma forma de alcançar segurança filtragem é efetuada através de uma disjunção complicada de expressões de igualdade: por exemplo, `Id eq 'id1' or Id eq 'id2'`, etc. Esta abordagem é propensas ao erro, difícil manter e pode atrasar nos casos em que a lista contém centenas ou milhares de valores, tempo de resposta de consulta por número de segundos. 

Uma abordagem mais simples e rápida é através de `search.in` função. Se utilizar `search.in(Id, 'id1, id2, ...')` em vez de uma expressão de igualdade, que pode esperar resposta segundo secundárias vezes.

Este artigo mostra como realizar a filtragem de segurança utilizando os seguintes passos:
> [!div class="checklist"]
> * Criar um campo que contém os identificadores principais 
> * Push ou atualizar documentos existentes com os identificadores de principais relevantes
> * Emitir um pedido de pesquisa com `search.in``filter`

>[!NOTE]
> O processo de obtenção os identificadores de principais não é abordado neste documento. Pode ser obtido a partir do seu fornecedor de serviços de identidade.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem um [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [serviço da Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal), e [índice da Azure Search](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Criar o campo de segurança

Os documentos têm de incluir um campo que especifica a que os grupos que têm acesso. Estas informações torna-se os critérios de filtro relativamente ao qual os documentos são selecionados ou rejeitados do conjunto de resultados devolvido para o emissor.
Vamos assumir que temos um índice de ficheiros protegidos, e cada ficheiro está acessível por um conjunto diferente de utilizadores.
1. Adicionar campo `group_ids` (pode escolher qualquer nome aqui) como um `Collection(Edm.String)`. Certifique-se de que o campo tem um `filterable` atributo definido como `true` para que os resultados da pesquisa são filtrados com base no acesso do utilizador tem. Por exemplo, se definir o `group_ids` campo para `["group_id1, group_id2"]` para o documento com `file_name` "secured_file_b", apenas os utilizadores que pertencem ao grupo ids "group_id1" ou "group_id2" tem acesso de leitura ao ficheiro.
   Certifique-se de que o campo `retrievable` atributo está definido como `false` para que não é devolvido como parte do pedido de pesquisa.
2. Também adicionar `file_id` e `file_name` campos com vista à, neste exemplo.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Enviar por push dados para o seu índice utilizando a API REST
  
Emita um pedido de HTTP POST ao ponto final do URL do seu índice. O corpo do pedido HTTP é um objeto JSON que contém os documentos a adicionar:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

No corpo do pedido, especifique o conteúdo dos seus documentos:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Se precisar de atualizar um documento existente com a lista de grupos, pode utilizar o `merge` ou `mergeOrUpload` ação:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Para obter detalhes completos sobre adicionar ou atualizar documentos, pode ler [editar documentos](https://docs.microsoft.com/en-us/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Aplique o filtro de segurança

Para compactar documentos com base na `group_ids` acesso, deve emitir uma consulta de pesquisa com um `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtro, onde 'group_id1, group_id2,...' são os grupos a que pertence o emissor de pedido de pesquisa.
Este filtro corresponde a todos os documentos para o qual o `group_ids` campo contém uma dos identificadores indicados.
Para obter detalhes completos na procura de documentos através da Azure Search, pode ler [documentos sobre pesquisa](https://docs.microsoft.com/en-us/rest/api/searchservice/search-documents).
Tenha em atenção que este exemplo mostra como procurar documentos através de um pedido POST.

Emita o pedido de HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Especifique o filtro no corpo do pedido:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Deve obter documentos novamente onde `group_ids` contém "group_id1" ou "group_id2". Por outras palavras, obtenha os documentos para que o emissor de pedido tem acesso de leitura.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Conclusão

Esta é a forma como pode filtrar os resultados com base na identidade de utilizador e da Azure Search `search.in()` função. Pode utilizar esta função para passar identificadores principais para o utilizador que pede para correspondência identificadores principais associados a cada documento de destino. Quando um pedido de pesquisa é processado, o `search.in` função filtra os resultados da pesquisa para que nenhuma das principais do utilizador tem acesso de leitura. Os identificadores principais podem representar coisas como grupos de segurança, funções ou mesmo a identidade do utilizador.
 

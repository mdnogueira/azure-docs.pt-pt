---
title: Como modelo tipos de dados complexas na Azure Search | Microsoft Docs
description: "Aninhada ou podem ser modeladas estruturas de dados hierárquica num índice da Azure Search utilizando o conjunto de linhas simplificado e tipo de dados de coleções."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: complex data types; compound data types; aggregate data types
ms.assetid: e4bf86b4-497a-4179-b09f-c1b56c3c0bb2
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: d576fd7bb267ae7a100589413185b595e3b2be42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Como modelo tipos de dados complexas na Azure Search
Conjuntos de dados externos utilizados para preencher um índice da Azure Search, por vezes, incluem substructures hierárquicas ou aninhadas que não quebrar neatly um conjunto de linhas de tabela. Exemplos de tais estruturas podem incluir várias localizações e números de telefone para um único cliente, vários cores e tamanhos de um SKU único, vários autores de um livro único e assim sucessivamente. Em termos de modelação, poderá ver estas estruturas que referida como *tipos de dados complexos*, *composta tipos de dados*, *tipos de dados composto*, ou *agregar os tipos de dados*, algumas o nome.

Tipos de dados complexas não são suportados nativamente na Azure Search, mas uma solução comprovada inclui um processo de dois passos pela estrutura e, em seguida, utiliza um **coleção** tipo de dados para reconstitute a estrutura interior. Seguir técnica descrita neste artigo permite que o conteúdo ser pesquisados por facetas, filtrada e ordenados.

## <a name="example-of-a-complex-data-structure"></a>Exemplo de uma estrutura de dados complexas
Normalmente, os dados em questão residem como um conjunto de documentos JSON ou XML ou como itens num arquivo NoSQL, tais como a base de dados do Azure Cosmos. Estruturalmente, o desafio stems de ter vários itens subordinados que precisam de ser procurado e filtrados.  Como um ponto de partida para ilustrar a solução, efetue o seguinte documento JSON que indica um conjunto de contactos como exemplo:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Embora os campos com o nome 'id' 'name' e 'empresa' podem facilmente ser mapeadas um para um como campos dentro de um índice da Azure Search, o campo 'localizações' contém uma matriz de localizações, ter ambos um conjunto de IDs de localização, bem como as descrições de localização. Dado que a Azure Search não tem um tipo de dados que suporta isto, temos de forma diferente para modelar isto na Azure Search. 

> [!NOTE]
> Esta técnica também está descrita pelo Kirk Evans um blogue [indexação DocumentDB do Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), que mostra uma técnica chamada "pelos dados," whereby que teria um campo chamado `locationsID` e `locationsDescription` que são ambos [coleções](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Aplanar a matriz em campos individuais
Para criar um índice de pesquisa do Azure que permite a este conjunto de dados, criar campos individuais para o substructure aninhada: `locationsID` e `locationsDescription` com um tipo de dados de [coleções](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias). Estes campos teria de índice os valores '1' e '2' para o `locationsID` campo para João Silva e os valores '3' & '4' para o `locationsID` campo para Jen Campbell.  

Os dados na Azure Search seriam ter este aspeto: 

![dados de exemplo, 2 linhas](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Adicionar um campo de coleção na definição do índice
O esquema de índice, as definições de campo podem ter um aspeto semelhantes para este exemplo.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validar comportamentos de pesquisa e optar por alargar o índice
Pressupondo que criou o índice e carregar os dados, pode agora testar a solução para verificar a execução de consultas de pesquisa contra o conjunto de dados. Cada **coleção** campo deve ser **pesquisável**, **filtrável** e **facetável**. Deve ser capaz de executar consultas, como:

* Localize todas as pessoas que trabalham na sede especiais da Adventureworks.
* Obter uma contagem do número de pessoas que trabalham no escritório especiais de casa.  
* Das pessoas que trabalham no escritório especiais de casa, mostram que outros escritórios funcionam juntamente com uma contagem das pessoas em cada localização.  

Em que esta técnica recai apart é quando precisa de fazer uma pesquisa que combina simultaneamente o id de localização, bem como a descrição da localização. Por exemplo:

* Localizar todas as pessoas que em que disponham de um escritório em casa e tem um ID de localização de 4.  

Se se recorda, o conteúdo original comparados como esta:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

No entanto, agora que iremos foram separadas os dados em campos separados, não temos nenhuma forma de saber se a home page do Office para Jen Campbell está relacionada com `locationsID 3` ou `locationsID 4`.  

Para processar este cenário, defina outro campo no índice que combine todos os dados uma única coleção.  Para o nosso exemplo, que iremos chamar este campo `locationsCombined` e iremos separará o conteúdo com um `||` embora pode escolher qualquer separador que pensa que seria um conjunto exclusivo de carateres para o seu conteúdo. Por exemplo: 

![dados de exemplo, 2 linhas com separador](./media/search-howto-complex-data-types/sample-data-2.png)

Utilizar esta `locationsCombined` campo, iremos pode agora acomodar ainda mais consultas, tal como:

* Mostra uma contagem das pessoas que trabalham num 'Home Office' com o Id de localização de '4'.  
* Procurar pessoas que trabalham no escritório especiais de home page com a localização Id '4'. 

## <a name="limitations"></a>Limitações
Esta técnica é útil para um número de cenários, mas não é aplicável em cada caso.  Por exemplo:

1. Se não tiver um conjunto estático de campos no seu tipo de dados complexos e não havia nenhuma forma de efetuar para mapear todos os tipos possíveis para um único campo. 
2. Atualizar os objetos aninhados requer algum trabalho adicional para determinar exatamente que tem de ser atualizados no índice da Azure Search

## <a name="sample-code"></a>Código de exemplo
Pode ver um exemplo sobre como um conjunto de dados JSON complexo de índice na Azure Search e executar um número de consultas sobre este conjunto de dados neste [repositório do GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Passo seguinte
[Os votos para o suporte nativo para tipos de dados complexos](https://feedback.azure.com/forums/263029-azure-search) no UserVoice de pesquisa do Azure página e qualquer intervenção adicional que pretende que enviemos a considerar relativamente à implementação de funcionalidade. Pode também aceder à-me diretamente no Twitter em @liamca.


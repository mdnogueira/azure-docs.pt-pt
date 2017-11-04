---
pageTitle: Synonyms in Azure Search (preview) | Microsoft Docs
description: "Preliminar documentação para a funcionalidade de sinónimos (pré-visualização), exposta na API de REST da Azure Search."
services: search
documentationCenter: 
authors: mhko
manager: pablocas
editor: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/07/2016
ms.author: nateko
ms.openlocfilehash: 447abc48cca3dee398e641f8458e52a5b2cb8e42
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="synonyms-in-azure-search-preview"></a>Sinónimos na pesquisa do Azure (pré-visualização)

Sinónimos no motores de busca associar termos equivalentes que implicitamente expande o âmbito de uma consulta, sem o utilizador ter de fornecer, na verdade, o termo. Por exemplo, tendo em conta os termo "preguiçoso" e o sinónimo associações "canine" e "puppy", os documentos que contenham "preguiçoso", "canine" ou "puppy" será se encontra no âmbito da consulta.

Na Azure Search, expansão de sinónimo é feito no momento da consulta. Pode adicionar o sinónimo maps para um serviço com operações existentes sem interrupção. Pode adicionar um **synonymMaps** propriedade a uma definição de campo sem ter de reconstruir o índice.

## <a name="feature-availability"></a>Disponibilidade de funcionalidades

A funcionalidade de sinónimos está atualmente em pré-visualização e só é suportada na api-version de pré-visualização mais recente (api-version = 2016-09-01-pré-visualização). Não existe suporte do portal do Azure neste momento. Porque a versão da API é especificada no pedido, é possível combinar geralmente disponível (GA) e APIs de pré-visualização na mesma aplicação. No entanto, as APIs não estão sob o SLA e funcionalidades de pré-visualização pode alterar, pelo que recomendamos que não utilizá-los em aplicações de produção.

## <a name="how-to-use-synonyms-in-azure-search"></a>Como utilizar sinónimos na pesquisa do Azure

Na Azure Search, baseia-se o suporte de sinónimo no maps sinónimo que definir e carregar para o seu serviço. Estes mapeamentos constituem um recurso independente (como índices ou origens de dados) e podem ser utilizados por qualquer campo pesquisável no qualquer índice no serviço de pesquisa.

Sinónimo mapeia e índices são mantidos de forma independente. Depois de definir um mapa de sinónimo e carregá-la ao seu serviço, pode ativar a funcionalidade de sinónimo num campo ao adicionar uma nova propriedade denominada **synonymMaps** na definição do campo. Criar, atualizar e eliminar um mapa de sinónimo sempre são uma operação de documento inteiro, que significa que não é possível criar, atualizar ou eliminar partes do mapa sinónimo de forma incremental. Atualizar até uma única entrada exige um recarregar.

Incorporando sinónimos na sua aplicação de pesquisa é um processo de dois passos:

1.  Adicione um mapeamento de sinónimo ao seu serviço de pesquisa através das APIs abaixo.  

2.  Configure um campo pesquisável a utilizar o mapa de sinónimo na definição do índice.

### <a name="synonymmaps-resource-apis"></a>APIs de SynonymMaps recursos

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adicionar ou atualizar um mapa de sinónimo no seu serviço, utilizando publicar ou colocar.

Mapas de sinónimo são carregados para o serviço através de POST ou PUT. Cada regra tem de ser delimitada pelo caráter linha nova ('\n'). Pode definir até 5 000 regras por sinónimo mapa num serviço livre e 10 000 regras em todos os outros SKUs. Cada regra pode ter até 20 expansions.

Nesta pré-visualização, sinónimo maps tem de estar no formato Apache Solr que é explicado abaixo. Se tiver um dicionário sinónimo existente num formato diferente e pretender utilizá-lo diretamente, indique no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Pode criar um mapa de sinónimo novo utilizando o HTTP POST, como no exemplo seguinte:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Em alternativa, pode utilizar PUT e especifique o nome de mapa de sinónimo no URI. Se o mapa de sinónimo não existir, será criado.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formato do Apache Solr sinónimo

O formato de Solr suporta mapeamentos sinónimo equivalentes e explícita. As regras de mapeamento de respeitar a especificação de filtro de sinónimo de código aberto do Apache Solr, descritas neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Segue-se uma regra de exemplo para sinónimos equivalentes.
```
USA, United States, United States of America
```

Com a regra acima, uma consulta de pesquisa "EUA" irá expandir "EUA" ou "Estados Unidos" ou "Estados Unidos da América".

Mapeamento explícito está em falta por que uma seta "= >". Quando especificado, uma sequência de prazo de uma consulta de pesquisa que corresponda ao lado esquerdo do "= >" será substituído por alternativas no lado direito. Tendo em conta a regra abaixo, consultas de pesquisa "Washington", "Wash." ou "WA" será todos ser rescrita para "WA". Mapeamento explícito só aplica-se na direção especificada e não volte a escrever a consulta "WA" para "Washington" neste caso.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Mapeia o sinónimo de lista no seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Obter um mapa de sinónimo no seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Elimine um mapa de sinónimos no seu serviço.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configure um campo pesquisável a utilizar o mapa de sinónimo na definição do índice.

Uma nova propriedade de campo **synonymMaps** pode ser utilizado para especificar um mapa de sinónimo a utilizar para um campo pesquisável. Mapas de sinónimo recursos de nível de serviço e podem ser referenciados por qualquer campo de um índice no serviço.

    POST https://[servicename].search.windows.net/indexes?api-version=2016-09-01-Preview
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** podem ser especificados para os campos pesquisáveis do tipo 'EDM' ou 'Collection(Edm.String)'.

> [!NOTE]
> Nesta pré-visualização só pode ter um sinónimo mapear por campo. Se pretender utilizar vários sinónimo maps, indique no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impacto da sinónimos em outras funcionalidades de pesquisa

A funcionalidade de sinónimos reescreve da consulta original com sinónimos com o operador OR. Por este motivo, o realce e perfis de classificação acessos tratar termo e sinónimos originais como equivalentes.

Funcionalidade de sinónimo aplica-se a consultas de pesquisa e não é aplicável a filtros ou facetas. Da mesma forma, sugestões baseiam apenas o termo original; correspondências sinónimo não aparecem na resposta.

Expansions sinónimo não se aplicam a termos de pesquisa de caráter universal; prefixo, difusa e os termos de regex não são expandidos.

## <a name="tips-for-building-a-synonym-map"></a>Sugestões para a criação de um mapa de sinónimo

- É mais eficiente do que uma lista exaustiva de possíveis correspondências um mapa de sinónimo concisas, devidamente concebida. Dicionários excessivamente grandes ou complexos demorar mais tempo a analisar e afetam a latência da consulta se expande a consulta para muitos sinónimos. Em vez de estimativa que podem ser utilizados os termos, pode obter os termos de licenciamento reais através de um [pesquisar o relatório de análise do tráfego](search-traffic-analytics.md).

- Como um preliminar e validação exercer, ativar e, em seguida, utilize este relatório para determinar precisamente que termos beneficiar uma correspondência de sinónimo e, em seguida, continuar a utilizá-lo como a validação de que o mapa de sinónimo é produzir um resultado de uma melhor. No relatório predefinido, os mosaicos "mais comuns consultas de pesquisa" e "consultas de pesquisa de resultado de Zero" irão dar-lhe as informações necessárias.

- Pode criar várias sinónimo maps para a sua aplicação de pesquisa (por exemplo, por idioma se a aplicação suportar uma base de clientes com várias lingual). Atualmente, um campo só pode utilizar um deles. Pode atualizar synonymMaps propriedade um campo em qualquer altura.

## <a name="next-steps"></a>Passos seguintes

- Se tiver um índice existente num ambiente de desenvolvimento (não a produção), experimente um dicionário pequeno para ver como a adição de sinónimos altera a experiência de pesquisa, incluindo o impacto nos perfis de classificação, detetor de ocorrências e sugestões.

- [Ativar a análise de tráfego de pesquisa](search-traffic-analytics.md) e utilize o relatório do Power BI predefinido para saber os termos são utilizados mais e aqueles devolvem zero documentos. Armed com estas informações, reveja o dicionário de sinónimos para consultas unproductive que devem ser resolvido a documentos no seu índice de incluir.

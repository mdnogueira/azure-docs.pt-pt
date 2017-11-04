---
title: Analisadores na Azure Search | Microsoft Docs
description: "Analisadores de atribuir aos campos pesquisáveis texto num índice para substituir a predefinição Lucene padrão com alternativas personalizadas, predefinidas ou específicas do idioma."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/11/2017
ms.author: heidist
ms.openlocfilehash: 1b9dea2978c11955da3ea4df8b90dc10a866d3f1
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="analyzers-in-azure-search"></a>Analisadores no Azure Search

Um *analisador* é um componente do [pesquisa em texto completo](search-lucene-query-architecture.md) responsável por processar texto em cadeias de consulta e documentos indexados. As transformações seguintes são normais durante a análise:

+ Não essencial palavras (palavras de paragem) e pontuação são removidos.
+ Expressões e hifenizadas palavras são divididas em partes de componente.
+ Maiúsculas palavras são cased de inferior.
+ Palavras são reduzidas formulários de raiz, para que pode ser encontrada uma correspondência, independentemente da tense.

Analisadores linguístico converter um valor de texto em primitivo ou formulários de raiz que são eficientes para armazenamento de informações e a obtenção. Conversão ocorre durante a indexação, quando o índice é criado e, em seguida, novamente durante a pesquisa quando o índice é de leitura. É mais provável obter os resultados da pesquisa espera que o se utilizar o mesmo analisador de texto para ambas as operações.

A pesquisa do Azure utiliza o [Lucene padrão analisador](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) como predefinição. Pode substituir a predefinição de forma de campo ao campo. Este artigo descreve o intervalo de opções e oferece melhores práticas para análise personalizada. Também fornece configurações de exemplo para cenários-chave.

## <a name="supported-analyzers"></a>Analisadores suportados

A lista seguinte descreve os analisadores são suportados na Azure Search.

| Categoria | Descrição |
|----------|-------------|
| [Analisador de Lucene padrão](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | A predefinição. Não é necessária nenhum especificação ou a configuração. Executa este analisador para fins gerais para a maioria dos cenários e idiomas.|
| Analisadores predefinidas | Oferecido como um produto concluído se destina a ser utilizado como-está, com a personalização limitada. <br/>Existem dois tipos: especializados e de idioma. O que se tornarem "predefinida" está a referenciar pelo nome, com nenhuma personalização. <br/><br/>[Especializada analisadores de (agnóstico de idioma)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) são utilizados quando entradas de texto requerem processamento especializado ou processamento mínimo. Analisadores de predefinida de idioma não incluem **Asciifolding**, **palavra-chave**, **padrão**, **simples**, **parar**, **Espaços em branco**.<br/><br/>[Analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) são utilizados quando necessitar de suporte linguístico avançado para idiomas individuais. A pesquisa do Azure suporta 35 Lucene analisadores de idioma e 50 analisadores de processamento de linguagem natural de Microsoft. |
|[Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Uma configuração definida pelo utilizador de uma combinação de elementos existentes, constituída por um atomizador (obrigatório) e os filtros opcionais (char ou token).|

Pode personalizar um analisador predefinido, tais como **padrão** ou **parar**, para utilizar opções alternativas documentadas [predefinidas de analisador de referência](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Apenas alguns dos analisadores de predefinida tem opções que pode definir. Como com qualquer personalização, fornecer a configuração de novo com um nome, tal como *myPatternAnalyzer* distinguir do analisador de Lucene padrão.

## <a name="how-to-specify-analyzers"></a>Como especificar analisadores

1. (para apenas analisadores personalizados) Criar um **analisador** secção na definição do índice. Para obter mais informações, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) e também [analisadores de personalizado > criar](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Num [campo definição](https://docs.microsoft.com/rest/api/searchservice/create-index) no índice, defina o **analisador** propriedade para o nome de um analisador de destino (por exemplo, `"analyzer" = "keyword"`. Os valores válidos incluem o nome de um analisador predefinida, o analisador de idioma ou o analisador personalizado também são definidos no esquema do índice.

3. Opcionalmente, em vez de um **analisador** , pode definir a propriedade diferentes analisadores de indexação e consultar utilizando o **indexAnalyzer** e **searchAnalyzer'** campo parâmetros. 

3. Adicionar um analisador a uma definição de campo implica uma operação de escrita no índice. Se adicionar um **analisador** a um índice existente, tenha em atenção os seguintes passos:
 
 | Cenário | Impacto | Passos |
 |----------|--------|-------|
 | Adicionar um novo campo | mínimo | Se o campo ainda não existe no esquema, não há nenhum revisão de campo para tornar porque o campo ainda não tem uma presença física no seu índice. Utilize [atualização índice](https://docs.microsoft.com/rest/api/searchservice/update-index) e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para esta tarefa.|
 | Adicione um analisador de um campo indexado existente. | Reconstrução | O índice inverted para esse campo deve ser recriado a partir do zero cópias de segurança e o conteúdo para os campos têm de ser reindexed. <br/> <br/>Para índices em desenvolvimento Active Directory, [eliminar](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [criar](https://docs.microsoft.com/rest/api/searchservice/create-index) o índice para recolher a nova definição de campo. <br/> <br/>Para os índices na produção, deve criar um novo campo para fornecer a definição revista e começar a utilizá-la. Utilize [atualização índice](https://docs.microsoft.com/rest/api/searchservice/update-index) e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para incorporar o campo de novo. Mais tarde, como parte da manutenção planeado índice, é possível limpar o índice de remover campos obsoletos. |

## <a name="tips-and-best-practices"></a>Dicas e melhores práticas

Esta secção oferece conselhos sobre como trabalhar com analisadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para leitura e escrita, salvo se tiver requisitos específicos

A pesquisa do Azure permite-lhe especificar diferentes analisadores de indexação e procurar através de adicionais `indexAnalyzer` e `searchAnalyzer` parâmetros de campo. Se não for indicado, o analisador de definir com a `analyzer` propriedade é utilizada para indexação e pesquisa. Se `analyzer` é especificado, o analisador de Lucene padrão predefinido utilizado.

Regra geral consiste em utilizar o mesmo analisador para indexação e consultar, a menos que os requisitos específicos determinem o contrário. Lembre-se de que testar cuidadosamente. Quando o processamento de texto difere em pesquisa e indexação de tempo, corre o risco de erro de correspondência entre os termos de consulta e termos indexados quando a pesquisa e indexação de analisador de configurações não estiverem alinhada.

### <a name="test-during-active-development"></a>Testar durante o desenvolvimento de Active Directory

Substituir o analisador de padrão requer uma reconstrução do índice. Se for possível, opte por utilizar os analisadores a utilizar durante o desenvolvimento de Active Directory, antes de a disponibilizar um índice em produção.

### <a name="inspect-tokenized-terms"></a>Inspecione atomizados termos

Se não for possível devolver resultados esperados uma pesquisa, o cenário mais provável é o token discrepâncias entre entradas termo na consulta e termos atomizados no índice. Se os tokens não são iguais, correspondências não materializar. Para inspecionar os saída atomizador, recomendamos que utilize o [analisar API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) como uma ferramenta de investigação. A resposta é composta por tokens, à medida que gerados por um analisador específico.

### <a name="compare-english-analyzers"></a>Comparar analisadores inglês

O [pesquisa analisador demonstração](http://alice.unearth.ai/) é uma aplicação de demonstração de terceiros que mostra uma comparação lado a lado do analisador de Lucene padrão, o analisador de idioma inglês do Lucene e o processador de linguagem natural a inglesa da Microsoft. O índice é fixa; contém o texto de uma história popular. Para cada entrada de pesquisa que fornece, os resultados de cada analisador são apresentados nos painéis adjacentes, dando-lhe um sentido de como cada analisador processa a mesma cadeia. 

## <a name="examples"></a>Exemplos

Os exemplos abaixo mostram as definições de analisador para alguns cenários-chave.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Exemplo 1: As opções personalizadas

Este exemplo ilustra uma definição de analisador com opções personalizadas. Opções personalizadas para os filtros de char, tokenizers e filtros de token são especificadas em separado como construções nomeadas e, em seguida, referenciadas na definição do analisador. Elementos predefinidos são utilizados como-é e simplesmente referenciada por nome.

Walking através deste exemplo:

* Analisadores são uma propriedade da classe de campo para um campo pesquisável.
* Um analisador personalizado faz parte de uma definição de índice. Pode ser ligeiramente personalizada (por exemplo, personalizar uma única opção num filtro) ou personalizada em vários locais.
* Neste caso, o analisador personalizado é "my_analyzer", que por sua vez utiliza um atomizador padrão personalizado "my_standard_tokenizer" e dois filtros de token: filtro de asciifolding em minúsculas e personalizadas "my_asciifolding".
* Também define um filtro de char map_dash"personalizada" para substituir todos os traços com carateres de sublinhado antes de atomização (as padrão atomizador quebras de linha no dash, mas não no caráter de sublinhado).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Exemplo 2: O analisador de predefinição de substituição

O analisador de padrão é a predefinição. Suponha que pretende substituir a predefinição com um analisador predefinido diferentes, tais como o analisador de padrão. Se não forem opções personalizadas de definição, só tem de especificar, pelo nome na definição do campo.

O elemento "analisador" substitui o analisador de padrão de forma de campo ao campo. Não há nenhuma substituição global. Neste exemplo, `text1` utiliza o analisador de padrão e `text2`, que não especificar um analisador, utiliza a predefinição.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Exemplo 3: Analisadores diferentes para as operações de pesquisa e indexação

As APIs incluem os atributos de índice adicional para especificar diferentes analisadores de pesquisa e indexação. O `searchAnalyzer` e `indexAnalyzer` atributos tem de ser especificados como um par, substituindo o único `analyzer` atributo.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>Exemplo 4: Analisador de idioma

Os campos com cadeias em idiomas diferentes podem usar um analisador de idioma, enquanto outros campos mantenha a predefinição (ou utilizam outra analisador personalizado ou predefinido). Se utilizar um analisador de idioma, tem de ser utilizado para operações de indexação e pesquisa. Os campos que utilizam um analisador de idioma não podem ter diferentes analisadores de indexação e de pesquisa.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>Passos seguintes

+ Rever a nossa explicação abrangente de [como completa a pesquisa em texto funciona na Azure Search](search-lucene-query-architecture.md). Este artigo utiliza exemplos para explicar comportamentos que podem parecer counter-intuitive na superfície.

+ Tente sintaxe de consulta adicionais do [documentos sobre pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) secção de exemplo ou a partir de [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) no Explorador de pesquisa no portal.

+ Saiba como aplicar [analisadores de lexical específicas do idioma](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configurar analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para processamento mínimo ou processamento especializado em campos individuais.

+ [Comparar analisadores de padrão e inglês](http://alice.unearth.ai/) nos painéis adjacentes neste web site de demonstração. 

## <a name="see-also"></a>Consultar também

 [API de REST de documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Total de consulta lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Processar os resultados da pesquisa](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png

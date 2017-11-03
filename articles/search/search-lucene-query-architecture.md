---
title: Total de arquitetura de motor (Lucene) de pesquisa de texto na Azure Search | Microsoft Docs
description: "Explicação de Lucene consulta processamento e o documento obtenção conceitos para pesquisa em texto completo, como relacionadas com a Azure Search."
services: search
manager: jhubbard
author: yahnoosh
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/06/2017
ms.author: jlembicz
ms.openlocfilehash: 0b2e66cd40c1b49832b865e5bf59edcf78996eb8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="how-full-text-search-works-in-azure-search"></a>Como completa a pesquisa em texto funciona na Azure Search

Este artigo é para os programadores que necessitem de uma compreensão mais aprofundada sobre como funciona a pesquisa em texto completo Lucene na Azure Search. Para consultas de texto, pesquisa do Azure irá fornecer perfeitamente resultados esperados na maioria dos cenários, mas ocasionalmente, poderá obter um resultado que parece "off" preso de algum modo. Nestas situações, ter um fundo as quatro fases da execução de consulta Lucene (consultar a análise de análise, lexical, documentar correspondente, a classificação) pode ajudar a identificar as alterações específicas para parâmetros de consulta ou a configuração de índice que irá fornecer o resultado desejado. 

> [!Note] 
> A pesquisa do Azure utiliza Lucene para pesquisa em texto completo, mas não é exaustiva Lucene integração. Iremos seletivamente expor e expandam a funcionalidade de Lucene para permitir os cenários importantes para a Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Descrição geral da arquitetura e diagrama

Processamento de uma consulta de pesquisa de texto completo começa com a análise do texto de consulta para extrair os termos da procura. O motor de busca utiliza um índice para obter documentos com termos correspondentes. Termos de consulta individuais, por vezes, são divididos e reconstituted em formulários de novo para converter uma rede mais abrangente sobre o que podem ser considerado como uma correspondência potencial. Um conjunto de resultados, em seguida, é ordenado por uma pontuação de relevância atribuída para cada documento correspondente individuais. Os na parte superior da lista de classificados são devolvidos para a aplicação de chamada.

Restated, execução de consultas tem quatro fases: 

1. Consulta de análise 
2. Análise lexical 
3. Obtenção de documento 
4. A classificação 

O diagrama abaixo ilustra os componentes utilizados para processar um pedido de pesquisa. 

 ![Diagrama de arquitetura de consulta Lucene na Azure Search][1]


| Componentes principais | Descrição funcional | 
|----------------|------------------------|
|**Parsers de consulta** | Separe os termos de consulta de operadores de consulta e criar a estrutura de consulta (uma árvore de consulta) sejam enviados para o motor de busca. |
|**Analisadores** | Execute uma análise lexical em termos de consulta. Este processo pode envolver transformar, remover ou de expansão de termos de consulta. |
|**Índice** | Uma estrutura de dados eficiente utilizada para armazenar e organizar os termos pesquisáveis extraídos de indexar documentos. |
|**Motor de busca** | Obtém e pontuações correspondentes documentos com base nos conteúdos do índice inverted. |

## <a name="anatomy-of-a-search-request"></a>Anatomy de um pedido de pesquisa

Um pedido de pesquisa é uma especificação de conclua de que deve ser devolvido num conjunto de resultados. A forma mais simples, é uma consulta vazia com sem critérios de qualquer tipo. Um exemplo mais realista inclui a parâmetros, vários termos de consulta, talvez um âmbito para determinados campos, com possivelmente uma expressão de filtro e ordem regras.  

O exemplo seguinte é um pedido de pesquisa pode enviar para a Azure Search utilizando o [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2016-09-01 
{  
    "search": "Spacious, air-condition* +\"Ocean view\"",  
    "searchFields": "description, title",  
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",  
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
 } 
~~~~

Para este pedido, o motor de busca faz o seguinte:

1. Filtros de saída de documentos onde o preço é de, pelo menos, 60 $ e inferior a 300 $.
2. Executa a consulta. Neste exemplo, a consulta de pesquisa consiste em expressões e termos: `"Spacious, air-condition* +\"Ocean view\""` (utilizadores, normalmente, não introduzem pontuação, mas incluir no exemplo permite-nos explicar como analisadores o processar). Para esta consulta, o motor de busca analisa a descrição e campos de título especificado no `searchFields` para documentos que contenham "Vista Ocean" e adicionalmente o termo "spacious" ou medida que começa com o prefixo "air-condition". O `searchMode` parâmetro é utilizado para correspondência com qualquer termo (predefinição) ou em todos eles, nos casos em que não é necessário explicitamente um termo (`+`).
3. Ordena resultante definir dos hotéis proximidade para uma localização de geografia indicado e, em seguida, é encaminhado para a aplicação de chamada. 

A maioria deste artigo é sobre o processamento do *consulta de pesquisa*: `"Spacious, air-condition* +\"Ocean view\""`. Filtragem e ordenação estão fora do âmbito. Para obter mais informações, consulte o [documentação de referência da API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fase 1: Análise de consulta 

Conforme indicado, a cadeia de consulta é a primeira linha do pedido: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Operadores de separa o parser de consulta (tais como `*` e `+` no exemplo) a partir de pesquisa de termos e deconstructs a consulta de pesquisa no *subconsultas* de um tipo suportado: 

+ *consulta do termo* termos autónomo (como spacious)
+ *consulta de expressão* termos entre aspas (como ver ocean)
+ *consulta de prefixo* termos seguido de um operador de prefixo `*` (como air-condition)

Para obter uma lista completa dos tipos de consulta suportada consulte [consulta lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operadores associados uma subconsulta determinam se a consulta "tem de ser" ou "deve ser" satisfeitos para que um documento para ser considerado uma correspondência. Por exemplo, `+"Ocean view"` é "tem" devido a `+` operador. 

O parser de consulta restructures subconsultas para um *árvore de consulta* (uma estrutura interna que representa a consulta) tenha sido enviado para o motor de busca. Na primeira fase de análise de consulta, a árvore de consulta tem este aspeto.  

 ![Valor booleano consultar searchmode qualquer][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Suportado parsers: simples e Lucene completa 

 A pesquisa do Azure expõe dois idiomas de consulta diferentes, `simple` (predefinição) e `full`. Ao definir o `queryType` parâmetro com o seu pedido de pesquisa, pode saber o parser de consulta o idioma de consulta que escolher, para que o se sabe como interpretar os operadores e sintaxe. O [idioma de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) é intuitiva e robusta, muitas vezes, adequado interpretar intervenção do utilizador como-é sem processamento do lado do cliente. Suporta os operadores de consulta familiares web dos motores de busca. O [idioma de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), os quais obtém definindo `queryType=full`, expande o idioma de consulta simples predefinido, adicionando suporte para obter mais operadores e tipos de consulta como caráter universal, difusa, regex e consultas de âmbito de campo. Por exemplo, uma expressão regular enviada na sintaxe de consulta simples seria ser interpretada como uma cadeia de consulta e não uma expressão. O pedido de exemplo neste artigo utiliza o idioma de consulta Lucene completa.

### <a name="impact-of-searchmode-on-the-parser"></a>Impacto da searchMode no parser de 

Outro parâmetro de pedido de pesquisa que afeta a análise é o `searchMode` parâmetro. Controla o operador predefinido para consultas booleanos: quaisquer (predefinição) ou a totalidade.  

Quando `searchMode=any`, que é a predefinição, o delimitador de espaço entre spacious e air-condition ou (`||`), tornando o texto da consulta de exemplo equivalente ao: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Operadores explícitos, tais como `+` no `+"Ocean view"`, são inequívoca na construção de consultas booleano (o termo *tem* corresponder). Menos óbvios é como interpretar os termos de licenciamento restantes: spacious e air-condition. Deve o motor de busca localizar correspondências na vista de ocean *e* spacious *e* air-condition? Ou deverá encontrar-vista ocean plus *uma* dos termos restantes? 

Por predefinição (`searchMode=any`), o motor de busca assume a interpretação mais ampla. O campo *deve* corresponder, ao refletir semântica "ou". A árvore de consulta inicial ilustrado anteriormente, com as duas "deve" operações, mostra as predefinições.  

Suponha que definimos agora `searchMode=all`. Neste caso, o espaço é interpretado como uma operação "and". Cada um dos termos de licenciamento restantes deve estar ambos presente no documento para qualificar como uma correspondência. O exemplo de consulta resultante iria devem ser interpretado da seguinte forma: 

~~~~
+Spacious,+air-condition*+"Ocean view"  
~~~~

Uma árvore de modificação da consulta para esta consulta seria o seguinte, em que um documento correspondente é a intersecção de todas as três subconsultas: 

 ![Consulta booleano searchmode todos os][3]

> [!Note] 
> Escolher `searchMode=any` através de `searchMode=all` é uma decisão melhor chegou ao executar consultas representativos. Os utilizadores que são provável que incluem os operadores (comum quando documento pesquisa armazena) poderá encontrar resultados mais intuitiva se `searchMode=all` informa construções de consulta booleano. Para mais informações sobre o interplay entre `searchMode` e operadores, consulte [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fase 2: Análise de Lexical 

Processo de analisadores lexical *termo consultas* e *frase consultas* depois da árvore de consulta está estruturada. Um analisador aceita as entradas de texto atribuídas pelo parser, processa o texto e, em seguida, envia novamente atomizada termos para ser incorporados na árvore da consulta. 

É a forma mais comuns de análise lexical *analysis linguístico* que transformações consultar termos com base nas regras específicas para um determinado idioma: 

* Reduzir um termo de consulta para a forma de raiz de uma palavra 
* A remover não essencial palavras (palavras de paragem, tais como "a" ou "e" em inglês) 
* Eliminar uma palavra composta em partes de componente 
* Inferior as maiúsculas e minúsculas uma palavra de maiúsculas 

Todas estas operações tendem a apagar as diferenças entre a entrada de texto fornecida pelo utilizador e os termos de licenciamento armazenados no índice. Operações ponha o processamento de texto e requerem conhecimentos aprofundados do idioma si próprio. Para adicionar esta camada de sensibilização linguístico, pesquisa do Azure suporta uma longa lista de [analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) Lucene e Microsoft.

> [!Note]
> Requisitos de análise podem variar entre mínima e elaboradas, dependendo do cenário. Pode controlar a complexidade da análise lexical ao selecionar um dos analisadores de predefinidas ou criar os seus próprios [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analisadores estão confinadas aos campos pesquisáveis e são especificadas como parte de uma definição de campo. Isto permite-lhe variar analysis lexical numa base por campo. Não, o *padrão* Lucene analisador é utilizado.

No nosso exemplo, antes da análise, a árvore de consulta inicial, tem o termo "Spacious", com uma maiúscula "S" e uma vírgula que o parser de consulta interpreta como parte do termo de consulta (uma vírgula não é considerada um operador de idioma de consulta).  

Quando o analisador de predefinida processa o termo, possa minúsculas de "ocean view" e "spacious" e remover o caráter de vírgulas. A árvore de modificação da consulta irão ter o seguinte aspeto: 

 ![Consulta booleana com termos analisados][4]

### <a name="testing-analyzer-behaviors"></a>Comportamentos do analisador de teste 

O comportamento de um analisador pode ser testado usando o [analisar API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Forneça o texto que pretende analisar para ver o que irão gerar termos fornecidos analisador. Por exemplo, para ver como o analisador de padrão seria processar o texto "air-condition", pode emitir o pedido seguinte:

~~~~
{ 
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

O analisador de padrão interrompe o texto de entrada para os tokens de dois seguintes, anotá-los com os atributos como iniciar e desvios de fim (utilizados para acessos realce), bem como a respetiva posição (utilizado para a correspondência de expressão):

~~~~
{  
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
~~~~

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Exceções para análise lexical 

Análise lexical aplica-se apenas aos tipos de consulta que requerem concluídos termos – uma consulta do prazo ou uma consulta de expressão. Não se aplica aos tipos de consulta com os termos de incompletos – consulta de prefixo, consulta de caráter universal, consulta regex – ou para uma consulta por semelhantes. Os tipos, incluindo a consulta de prefixo com o termo de consulta *air-condition\**  no nosso exemplo, são adicionados diretamente à árvore de consulta, ignorando a fase de análise. A transformação apenas efetuada em termos de consulta desses tipos é lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fase 3: Obtenção de documento 

Obtenção de documento refere-se para localizar documentos com correspondência termos no índice. Nesta fase é compreendida melhor através de um exemplo. Vamos começar com um índice de hotéis ter o esquema simple seguinte: 

~~~~
{   
    "name": "hotels",     
    "fields": [     
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },     
        { "name": "title", "type": "Edm.String", "searchable": true },     
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
~~~~

Mais partem do princípio de que este índice contém quatro documentos que o seguintes: 

~~~~
{ 
    "value": [
        {         
            "id": "1",         
            "title": "Hotel Atman",         
            "description": "Spacious rooms, ocean view, walking distance to the beach."   
        },       
        {         
            "id": "2",         
            "title": "Beach Resort",        
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."     
        },       
        {         
            "id": "3",         
            "title": "Playa Hotel",         
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },       
        {         
            "id": "4",         
            "title": "Ocean Retreat",         
            "description": "Quiet and secluded"
        }    
    ]
}
~~~~

**Como são indexados termos**

Para compreender a obtenção, ajuda a saber algumas noções básicas sobre a indexação. A unidade de armazenamento é um índice inverted, um para cada campo pesquisável. Dentro de um índice inverted é uma lista ordenada de todos os termos de todos os documentos. Cada termo mapeia para a lista de documentos em que ocorrer, como evidente no exemplo abaixo.

Para produzir os termos de um índice inverted, o motor de busca efetua a análise lexical sobre o conteúdo de documentos, semelhantes ao que acontece durante o processamento de consulta:

1. *Entradas de texto* são transferidos para um analyzer, cased inferior repartidas de pontuação e assim sucessivamente, dependendo da configuração do analisador. 
2. *Tokens* são o resultado da análise de texto.
3. *Termos* são adicionados ao índice.

É comum, mas não é necessário, para utilizar os mesmos analisadores de pesquisa e indexação operações, para que os termos de consulta parecer mais como termos dentro do índice.

> [!Note]
> A pesquisa do Azure permite-lhe especificar diferentes analisadores de indexação e procurar através de adicionais `indexAnalyzer` e `searchAnalyzer` parâmetros de campo. Se não for indicado, o analisador de definir com a `analyzer` propriedade é utilizada para indexação e pesquisa.  

**Inverted índice para documentos de exemplo**

Regressar ao nosso exemplo, para o **título** campo, o índice inverted tem este aspeto:

| Termo | Lista de documentos |
|------|---------------|
| atman | 1 |
| Beach | 2 |
| átrios | 1, 3 |
| Ocean | 4  |
| playa | 3 |
| recurso | 3 |
| Retreat | 4 |

No campo do título, apenas *átrios* aparece em dois documentos: 1, 3.

Para o **Descrição** campo, o índice é o seguinte:

| Termo | Lista de documentos |
|------|---------------|
| ar | 3
| e | 4
| Beach | 1
| ter | 3
| -Se confortáveis ao | 3
| Distância | 1
| Ilha | 2
| kauaʻi | 2
| localizado | 2
| Norte | 2
| Ocean | 1, 2, 3
| de | 2
| no |2
| silenciosos | 4
| gabinetes  | 1, 3
| secluded | 4
| shore | 2
| spacious | 1
| o | 1, 2
| para | 1
| ver | 1, 2, 3
| walking | 1
| com o | 3


**Termos de consulta correspondente contra indexados termos**

Tendo em conta os índices inverted acima, vamos voltar para a consulta de exemplo e ver documentos correspondentes como encontram-se para a nossa consulta de exemplo. Recuperar-se de que a árvore de consulta final este aspeto: 

 ![Consulta booleana com termos analisados][4]

Durante a execução de consulta, consultas individuais são executadas contra os campos pesquisáveis independentemente. 

+ Correspondências "spacious" TermQuery, documentar 1 (átrios Atman). 

+ O PrefixQuery "air-condition *", não corresponde a quaisquer documentos. 

  Este é um comportamento que confuses, por vezes, os programadores. Embora o termo air-conditioned existe no documento, é dividida em duas termos pelo analisador de predefinição. Recuperar-se de que as consultas de prefixo, que contêm termos parciais, não são analisadas. Por conseguinte termos com o prefixo "air-condition" são pesquisados no índice inverted e não foi encontrados.

+ PhraseQuery, "vista ocean", procura o ocean"termos" e "ver" e verifica a proximidade dos termos de licenciamento no documento original. Documentos 1, 2 e 3 correspondem a esta consulta no campo de descrição. Tenha em atenção documento 4 tem o ocean termo no título mas não é considerado uma correspondência, tal como estamos procuram o frase de "ocean view" em vez de palavras individuais. 

> [!Note]
> Uma consulta de pesquisa é executada de forma independente em relação a todos os campos pesquisáveis no índice da Azure Search a menos que limitam os campos definidos com o `searchFields` parâmetro, conforme ilustrado no exemplo de pedido de pesquisa. Documentos que correspondam em qualquer um dos campos selecionados são devolvidos. 

Em todo, para a consulta em questão, os documentos que correspondam a são 1, 2, 3. 

## <a name="stage-4-scoring"></a>Testar 4: classificação  

Todos os documentos de um conjunto de resultados de pesquisa é atribuído uma pontuação de importância. A função de pontuação de relevância é superior a posição esses documentos que melhor de responder a uma utilizador pergunta, expressa pelo consulta de pesquisa. A classificação é calculada com base nas propriedades de análises de termos correspondentes. O núcleo da fórmula classificação é [TF/IDF (frequência de documento de frequência inverso termo)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Em consultas que contém termos raros e comuns, TF/IDF promove resultados que contém o termo raro. Por exemplo, um índice hipotético com todos os artigos Wikipedia, de documentos que correspondam a consulta *o president*, documentos correspondente no *president* são consideradas mais relevante que documentos correspondente no *o*.


### <a name="scoring-example"></a>A classificação de exemplo

Recuperar os três documentos que correspondeu à nossa consulta de exemplo:
~~~~
search=Spacious, air-condition* +"Ocean view"  
~~~~
~~~~
{  
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
~~~~

Documento 1 corresponder, a consulta melhor porque tanto o termo *spacious* e o frase necessário *vista ocean* ocorrer no campo de descrição. Os dois documentos correspondem apenas o frase *vista ocean*. Poderá ser surprising-se de que a classificação de importância para documento 2 e 3 é diferente, apesar da consulta correspondência da mesma forma. É porque a fórmula da classificação tem mais componentes que acabou de TF/IDF. Neste caso, o documento 3 foi atribuído uma pontuação ligeiramente porque a respetiva descrição é mais pequeno. Saiba mais sobre [práticas da classificação de fórmula do Lucene](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) para compreender como o comprimento de campo e ainda outros fatores podem influenciar a classificação de importância.

Alguns tipos (universal, prefixo, regex) de consulta contribuir sempre uma pontuação constante para o modelo de pontuação de documento geral. Isto permite correspondências encontradas através da expansão de consulta sejam incluídos nos resultados, mas sem afetar a classificação. 

Um exemplo ilustra a razão pela qual isto é importante. Pesquisas com carateres universais, incluindo as pesquisas de prefixo, são ambíguas por definição, porque a entrada é uma cadeia parcial com potenciais correspondências num número muito grande de diferentes termos (considere uma entrada de "apresentação *", com correspondências encontradas em "tours", "tourettes" e "tourmaline"). Tendo em conta a natureza estes resultados, não há nenhuma forma razoável inferir os termos encontram-se mais importantes do que outros. Por este motivo, estamos a ignorar frequências termo quando a classificação de resultados em consultas de caráter universal de tipos, o prefixo e o regex. Num pedido de várias partes de pesquisa que inclui termos parciais e integrais, os resultados da entrada parcial estão incorporados com uma pontuação constante para evitar bias para correspondências potencialmente inesperadas.

### <a name="score-tuning"></a>Otimização de pontuação

Existem duas formas de otimizar as pontuações de relevância na pesquisa do Azure:

1. **Perfis de classificação** promover documentos na lista de classificados de resultados com base num conjunto de regras. No nosso exemplo, podemos considerar documentos correspondentes no campo do título mais relevante que documentos correspondentes no campo de descrição. Além disso, se o nosso índice tinha um campo de preço para cada átrios, iremos promover documentos com preço inferior. Saiba como mais [adicionar perfis de classificação de um índice de pesquisa.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Termo aumento** (disponível apenas na consulta lucene completo) fornece um operador de aumento `^` que podem ser aplicadas a qualquer parte da árvore de consulta. No nosso exemplo, em vez de procurar no prefixo *air-condition*\*, um pode procurar um o termo exato *air-condition* ou de prefixo, mas o documentos que correspondem o termo exato estão ordenadas superior aplicando intensificação à consulta termo: *ondas condição ^ 2 | | Air-condition**. Saiba mais sobre [termo aumento](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Classificação de um índice distribuído

Todos os índices na pesquisa do Azure são automaticamente divididos em várias shards, permitir-na distribuir rapidamente o índice entre vários nós durante a escala do serviço de segurança ou reduzir verticalmente. Quando é emitido um pedido de pesquisa, este é emitido em relação a cada partição horizontal independentemente. Os resultados de cada ID de partição horizontal, em seguida, são intercalados e ordenados pelo modelo de pontuação (se não existem outros ordenação está definido). É importante saber que as ponderações de função de classificação consultar frequência termo contra a frequência de documento inverso em todos os documentos dentro de partições horizontais, não em todas as partições horizontais!

Isto significa uma pontuação de relevância *foi* ser diferentes para documentos idênticos se que residem no shards diferentes. Felizmente, estas diferenças tendem a desaparecer à medida que cresce o número de documentos no índice devido a mais mesmo termo distribuição. Não é possível assumir no qual partições horizontais qualquer documento fornecido será efetuado. No entanto, partindo do princípio de que uma chave de documento não irá alterar, este será sempre atribuído para o ID de partição horizontal mesmo.

Em geral, pontuação de documento não é o atributo melhor para ordenação documentos se estabilidade ordem é importante. Por exemplo, tendo em conta dois documentos com um modelo de pontuação idêntico, não há nenhuma garantia que aparece primeiro na execuções subsequentes da mesma consulta. Classificação do documento só deve dar uma noção geral do relevância de documentos relativo de outros documentos no conjunto de resultados.

## <a name="conclusion"></a>Conclusão

A taxa de êxito de motores de busca internet gerou expetativas para pesquisa em texto completo sobre dados privados. Para praticamente todos os tipos de experiência de pesquisa, esperamos agora o motor de compreender a nossa intenção, mesmo quando os termos encontram-se com erros ortográficos ou está incompleto. Esperamos poderá mesmo correspondências com base nas quase equivalentes termos ou sinónimos que nunca, na verdade, especificamos.

A partir de um ponto de vista técnico, pesquisa em texto completo é altamente complexa, exigindo que uma análise sofisticada linguístico e uma abordagem systematic para processamento de formas que distill, expanda e transformar os termos de consulta para fornecer um resultado relevante. Tendo em conta as complexidades inerentes, existem muitos fatores que podem afetar o resultado de uma consulta. Por este motivo, o tempo para compreender o mechanics de pesquisa em texto completo o investimento oferece vantagens tangible ao tentar completar resultados inesperados.  

Este artigo explorou a pesquisa em texto completo no contexto da Azure Search. Esperamos que dá-lhe suficiente em segundo plano para reconhecer possíveis causas e soluções para problemas comuns de consulta de endereçamento. 

## <a name="next-steps"></a>Passos seguintes

+ Crie o índice de exemplo, experimentar diferentes consultas e reveja os resultados. Para obter instruções, consulte [compilar e consultar um índice no portal do](search-get-started-portal.md#query-index).

+ Tente sintaxe de consulta adicionais do [documentos sobre pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) secção de exemplo ou a partir de [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) no Explorador de pesquisa no portal.

+ Reveja [classificação perfis](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) se pretender otimizar classificação na sua aplicação de pesquisa.

+ Saiba como aplicar [analisadores de lexical específicas do idioma](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configurar analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para processamento mínimo ou processamento especializado em campos específicos.

+ [Comparar analisadores de padrão e inglês](http://alice.unearth.ai/)) lado a lado neste web site de demonstração. 

## <a name="see-also"></a>Consultar também

[API de REST de documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Total de consulta lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Processar os resultados da pesquisa](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png

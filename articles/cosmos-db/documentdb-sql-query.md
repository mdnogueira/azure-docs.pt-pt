---
title: As consultas SQL para a API do Azure Cosmos DB DocumentDB | Microsoft Docs
description: Saiba mais sobre a sintaxe SQL, conceitos de base de dados e as consultas SQL para a base de dados do Azure Cosmos. SQL Server pode utilizado como um idioma de consulta JSON do BD Azure Cosmos.
keywords: "sintaxe de SQL, consulta sql, as consultas sql, idioma de consulta json, conceitos de base de dados e as consultas de sql, as funções de agregação"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: a73b4ab3-0786-42fd-b59b-555fce09db6e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: arramac
ms.openlocfilehash: 862594bcbd6df8a2c62a12340ceb8096fb6bd691
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sql-queries-for-azure-cosmos-db-documentdb-api"></a>Consultas SQL para a API de DocumentDB do Azure Cosmos DB
Base de dados do Microsoft Azure Cosmos suporta a consulta de documentos através de SQL (Structured Query Language) como uma linguagem de consulta JSON. BD do cosmos é verdadeiramente sem esquema. Em virtude da respetivo compromisso para o modelo de dados JSON diretamente dentro do motor de base de dados, fornece a indexação automática de documentos JSON sem necessidade de um esquema explícito ou criação de índices secundários. 

Ao conceber o idioma de consulta de base de dados do Cosmos, tivemos de dois objetivos em mente:

* Em vez de inventing novo idioma de consulta JSON, queremos que suportam o SQL Server. SQL Server é um dos idiomas de consulta mais familiar e populares. Cosmos BD SQL fornece um modelo de programação formal para consultas avançadas através de documentos JSON.
* Como um documento base de dados JSON capacidade para executar o JavaScript diretamente no motor de base de dados, queremos de utilizar o modelo de programação do JavaScript como a base para o nosso idioma de consulta. A API do DocumentDB SQL é ROOT no sistema de tipo do JavaScript, avaliação de expressões e invocação de função. Esta por sua vez fornece um modelo de programação natural para projeções relacionais, hierárquica navegação em documentos JSON, associações automática, consultas geográficos e invocação de funções definidas pelo utilizador (UDFs) escritos inteiramente no JavaScript, entre outras funcionalidades. 

Acreditamos que estas capacidades são fundamentais para reduzir o friction entre a aplicação e a base de dados e são cruciais para produtividade de programador.

Recomendamos que comece por ver o seguinte vídeo, onde Aravind Ramachandran mostra Cosmos DB da capacidades de consulta, e ao visitar nosso [Query Playground](http://www.documentdb.com/sql/demo), onde pode experimentar a base de dados do Cosmos e executar consultas SQL no nosso conjunto de dados.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/DataExposedQueryingDocumentDB/player]
> 
> 

Em seguida, regresse a este artigo, onde iremos começar com um tutorial de consulta SQL que explica-lhe algumas simples documentos JSON e comandos SQL.

## <a id="GettingStarted"></a>Introdução aos comandos SQL na base de dados do Cosmos
Para ver Cosmos BD SQL no trabalho, vamos começar por alguns documentos JSON simples e guiá algumas consultas simples nele. Considere estes dois documentos JSON sobre famílias de dois. Com base de dados do Cosmos, não temos de criar explicitamente quaisquer esquemas ou índices secundários. Precisamos simplesmente de inserir os documentos JSON para uma coleção de BD do Cosmos e subsequentemente consultar. Aqui temos um JSON simples documentos para a família ou seja, os principais, elementos subordinados (e as respetivas pets), endereço e informações de registo. O documento tem as cadeias, números, em booleanos, matrizes e propriedades aninhadas. 

**Documento**  

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow", 
         "gender": "female", 
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Eis um segundo documento com uma diferença subtis – `givenName` e `familyName` são utilizados em vez de `firstName` e `lastName`.

**Documento**  

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

Agora vamos tentar algumas consultas contra estes dados para compreender alguns dos aspetos fundamentais do SQL Server de API do DocumentDB. Por exemplo, a seguinte consulta indica os documentos em que o campo id corresponde `AndersenFamily`. Uma vez que é um `SELECT *`, o resultado da consulta é o documento JSON concluído:

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Agora, considere o caso em que precisamos de reformatar a saída JSON numa forma diferentes. Esta consulta projeta um novo objeto JSON com dois campos selecionados, o nome e a cidade, quando cidade do endereço tem o mesmo nome como o estado. Neste caso, corresponde a "NY, NY".

**Consulta**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Resultados**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


A seguinte consulta devolve todos os nomes de especificado de elementos subordinados na família cujo id corresponde ao `WakefieldFamily` ordenados por cidade de residência.

**Consulta**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Gostaríamos de desenhar atenção para alguns aspetos noteworthy o idioma de consulta de base de dados do Cosmos através de exemplos que viu até ao momento:  

* Uma vez que o SQL do DocumentDB API funciona em valores JSON, lida com árvore em forma de entidades em vez de linhas e colunas. Por conseguinte, o idioma permite-lhe fazer referência a nós da árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem`, semelhante a SQL relacional referir-se para a referência de dois parte de `<table>.<column>`.   
* O idioma de consulta estruturada funciona com os dados sem esquema. Por conseguinte, o sistema de tipo tem de ser vinculado dinamicamente. A mesma expressão pode produzir diferentes tipos de documentos diferentes. O resultado de uma consulta é um valor JSON válido, mas não é assegurado para ser de um esquema fixo.  
* BD do cosmos só suporta strict documentos JSON. Isto significa que o sistema de tipo e expressões estão limitadas a lidar apenas com os tipos JSON. Consulte o [especificação JSON](http://www.json.org/) para obter mais detalhes.  
* Uma coleção de BD do Cosmos é um contentor sem esquema de documentos JSON. As relações em entidades de dados dentro e entre documentos numa coleção implicitamente são capturadas pela contenção e não por chave primária e relações de chaves externas. Este é um aspeto importante vale apontar in light of a associa intra-documento abordada posteriormente neste artigo.

## <a id="Indexing"></a>A indexação do cosmos DB
Antes de obtemos para a sintaxe de SQL de API do DocumentDB, é importante, explorar a estrutura de indexação na base de dados do Cosmos. 

O objetivo dos índices da base de dados é servir consultas as várias formas e formulários com consumo de mínima de recursos (como a CPU e a entrada/saída) ao fornecer boa débito e latência baixa. Muitas vezes, a escolha do índice direito para consultar uma base de dados requer muito planeamento e a experimentação. Esta abordagem opção origina um desafio de bases de dados sem esquema em que os dados não está em conformidade com um esquema strict e medida que evolui rapidamente. 

Por conseguinte, quando é concebida o subsistema de indexação do Cosmos DB, iremos definir os seguintes objetivos:

* Indexar os documentos sem necessidade de esquema: O subsistema de indexação não necessita de quaisquer informações de esquema ou efetuar quaisquer pressupostos sobre o esquema dos documentos. 
* Suporte para consultas eficiente, avançadas hierárquicas e relacionais: O índice suporta o idioma de consulta de base de dados do Cosmos de forma eficiente, incluindo suporte para as projeções hierárquicos e relacionais.
* Suporte para consultas consistentes in face of um volume constante de escritas: para escrita elevada a cargas de trabalho de débito com consultas consistentes, é atualizado o índice de forma incremental, de forma eficiente e online a face a um volume constante das escritas. A atualização do índice consistente é fundamental para servir as consultas ao nível de consistência em que o utilizador configurado o serviço de documento.
* Suporte para vários inquilinos: tendo em conta o modelo baseadas na reserva de governação de recursos através de inquilinos, índice atualizações são executadas dentro a atribuição de recursos do sistema (CPU, memória e operações de entrada/saída por segundo) alocadas pela réplica. 
* Eficiência de armazenamento: para eficácia de custo, os custos de armazenamento no disco o índice está vinculada e previsíveis. Isto é crucial, porque a BD do Cosmos permite ao programador tornar baseada no custo fala entre overhead de índice em relação ao desempenho de consulta.  

Consulte o [amostras de base de dados do Azure Cosmos](https://github.com/Azure/azure-documentdb-net) no MSDN exemplos que mostra como configurar a política de indexação de uma coleção. Agora vamos para os detalhes da sintaxe de BD SQL do Azure Cosmos.

## <a id="Basics"></a>Noções básicas de uma consulta de base de dados SQL do Azure Cosmos
Cada consulta é constituída por uma cláusula SELECT e FROM opcional e cláusulas WHERE por normas ANSI SQL. Normalmente, para cada consulta é enumerar a origem na cláusula FROM. Em seguida, o filtro na cláusula WHERE é aplicado a origem para obter um subconjunto de documentos JSON. Por fim, a cláusula SELECT é utilizada para projetar pedidos JSON valores existentes na lista de seleção.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>A cláusula FROM
O `FROM <from_specification>` cláusula é opcional, a menos que a origem é filtrada ou projetada mais tarde na consulta. O objetivo deste cláusula é especificar a origem de dados no qual a consulta tem de operar. Normalmente toda a coleção é a origem, mas um pode especificar um subconjunto da coleção em vez disso. 

Como uma consulta `SELECT * FROM Families` indica que o conjunto completo de famílias é a origem ao longo do que a enumeração. Um identificador especial raiz pode ser utilizado para representar a coleção em vez de utilizar o nome da coleção. A lista seguinte contém as regras que são impostas por consulta:

* A coleção pode ser um alias, tais como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o equivalente `Families`. `AS`é o identificador de uma palavra-chave opcional para alias.
* Uma vez um alias, não é possível vincular a origem original. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválida uma vez que o identificador "Famílias" já não é possível resolver.
* Todas as propriedades que têm de ser referenciados tem de ser totalmente qualificadas. Ausência de aderência do esquema strict, isto é imposto para evitar quaisquer enlaces ambíguas. Por conseguinte, `SELECT id FROM Families f` é sintaticamente inválida dado que a propriedade `id` não está vinculado.

### <a name="subdocuments"></a>Subdocuments
A origem também pode ser reduzida para um subconjunto mais pequeno. Por exemplo, ao enumerar apenas uma subárvore cada documento, o subroot foi, em seguida, tornar-se a origem, conforme mostrado no exemplo seguinte:

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Enquanto o exemplo acima utilizado uma matriz, como a origem, um objeto também pode ser utilizado como origem, o que é o que é mostrado no exemplo seguinte: qualquer valor JSON válido (não indefinido) que pode ser encontrado na origem é contemplado para inclusão no resultado da consulta. Se não tem algumas famílias um `address.state` valor, que estão excluídos no resultado da consulta.

**Consulta**

    SELECT * 
    FROM Families.address.state

**Resultados**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>Cláusula WHERE
A cláusula WHERE (**`WHERE <filter_condition>`**) é opcional. Especifica as condições que os documentos JSON fornecidos pela origem de devem satisfazer para poder ser incluídas como parte do resultado. De qualquer documento JSON tem de avaliar as condições especificadas como "true" para ser considerado para o resultado. A cláusula WHERE é utilizada pela camada de índice para determinar o subconjunto mais pequeno absoluto dos documentos de origem que podem fazer parte do resultado. 

A seguinte consulta pedidos documentos que contenham uma propriedade de nome cujo valor é `AndersenFamily`. Qualquer documento que não tem uma propriedade de nome, ou onde o valor não corresponde ao `AndersenFamily` está excluído. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


O exemplo anterior mostrou uma consulta simples de igualdade. SQL Server de API do DocumentDB também suporta uma variedade de expressões escalares. Frequentemente utilizadas são binário e unário expressões. As referências de propriedade do objeto JSON de origem também são as expressões válidas. 

Os seguintes operadores binários são atualmente suportados e podem ser utilizados em consultas, conforme mostrado nos exemplos seguintes:  

<table>
<tr>
<td>Aritmética</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Totalmente</td>    
<td>|, &, ^, <<>>,, >>> (zero preenchimento à direita shift)</td>
</tr>
<tr>
<td>Lógica</td>
<td>E, EM ALTERNATIVA, NÃO</td>
</tr>
<tr>
<td>Comparação</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Cadeia</td>    
<td>|| (concatenar)</td>
</tr>
</table>  


Vamos Observe algumas consultas com operadores binários.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Os operadores unários +,-, ~ não são também suportadas e podem ser utilizados em consultas, conforme mostrado no exemplo seguinte:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Para além de operadores unários de binários e, também são permitidas referências de propriedade. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o documento JSON que contém a propriedade `isRegistered` onde o valor da propriedade é igual ao JSON `true` valor. Quaisquer outros valores (false, null, indefinido, `<number>`, `<string>`, `<object>`, `<array>`, etc.) servem como o documento de origem que está a ser excluído do resultado. 

### <a name="equality-and-comparison-operators"></a>Operadores de comparação de igualdade e
A tabela seguinte mostra o resultado da comparações de igualdade no DocumentDB API SQL entre os dois tipos JSON.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>OP</strong>
         </td>
         <td valign="top">
            <strong>Indefinido</strong>
         </td>
         <td valign="top">
            <strong>Valor nulo</strong>
         </td>
         <td valign="top">
            <strong>Valor booleano</strong>
         </td>
         <td valign="top">
            <strong>Número</strong>
         </td>
         <td valign="top">
            <strong>Cadeia</strong>
         </td>
         <td valign="top">
            <strong>Objeto</strong>
         </td>
         <td valign="top">
            <strong>Matriz</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Indefinido<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Valor nulo<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Valor booleano<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Número<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Cadeia<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objeto<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Matriz<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Para outros operadores de comparação, tais como >, > =,! =, < e < =, as seguintes regras aplicam-se:   

* Comparação entre tipos resulta numa Undefined.
* Comparação entre dois objetos ou duas matrizes resulta em Undefined.   

Se o resultado da expressão escalar no filtro é não definida, o documento correspondente seria não incluído no resultado, uma vez que Undefined logicamente não equacionar como "true".

### <a name="between-keyword"></a>ENTRE palavra-chave
Também pode utilizar a palavra-chave BETWEEN para expressar as consultas em relação a intervalos de valores como ANSI SQL. ENTRE pode ser utilizados em relação a cadeias ou números.

Por exemplo, esta consulta devolve todos os documentos famílias em que o nível do primeiro subordinado é entre 1 a 5 (ambos inclusive). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Ao contrário no ANSI-SQL, também pode utilizar a cláusula BETWEEN na cláusula FROM como no exemplo seguinte.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Para tempos de execução de consulta mais rápidos, lembre-se criar uma política de indexação que utiliza um tipo de índice do intervalo em relação a quaisquer propriedades/caminhos numérico que são filtrados na cláusula BETWEEN. 

A principal diferença entre utilizar BETWEEN na API do DocumentDB e ANSI SQL é que pode express consultas de intervalo em relação a propriedades de tipos mistos – por exemplo, poderá ter de ser um número (5) "nível" em algumas documentos e cadeias no outras pessoas ("grade4"). Nestes casos, tal como em JavaScript, uma comparação entre dois tipos diferentes resulta em "Indefinido" e o documento será ignorada.

### <a name="logical-and-or-and-not-operators"></a>Lógica (AND, OR e não) operadores
Operadores lógicos operam nos valores booleanos. As tabelas de truth lógicas para destes operadores são mostradas nas tabelas seguintes.

| OU | Verdadeiro | Falso | Indefinido |
| --- | --- | --- | --- |
| Verdadeiro |Verdadeiro |Verdadeiro |Verdadeiro |
| Falso |Verdadeiro |Falso |Indefinido |
| Indefinido |Verdadeiro |Indefinido |Indefinido |

| E | Verdadeiro | Falso | Indefinido |
| --- | --- | --- | --- |
| Verdadeiro |Verdadeiro |Falso |Indefinido |
| Falso |Falso |Falso |Falso |
| Indefinido |Indefinido |Falso |Indefinido |

| NÃO |  |
| --- | --- |
| Verdadeiro |Falso |
| Falso |Verdadeiro |
| Indefinido |Indefinido |

### <a name="in-keyword"></a>NA palavra-chave
A palavra-chave in pode ser utilizado para verificar se um valor especificado corresponde a qualquer valor numa lista. Por exemplo, esta consulta devolve todos os documentos famílias onde o id é "WakefieldFamily" ou "AndersenFamily". 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Neste exemplo devolve todos os documentos onde o estado é a qualquer um dos valores especificados.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Operadores de Coalesce (?) e ternary (?)
Os operadores Ternary e Coalesce podem ser utilizados para criar expressões condicionais, semelhantes a populares linguagens de programação, como c# e JavaScript. 

O operador de Ternary (?) pode ser muito útil quando construir novas propriedades JSON no momento. Por exemplo, agora pode escrever consultas para classificar os níveis de classe num formulário legível humano como Beginner/intermédio/avançadas, como mostrado abaixo.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Também pode ser aninhado as chamadas para o operador como na consulta abaixo.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Como com outros operadores de consulta, se as propriedades referenciadas na expressão condicional estão em falta no qualquer documento ou se os tipos que está a ser comparados forem diferentes, em seguida, esses documentos são excluídos nos resultados da consulta.

O operador de Coalesce (?) pode ser utilizado para forma eficiente (a.k.a. a verificar a presença de uma propriedade está definido) num documento. Isto é útil quando consultas nos semiestruturados ou dados de tipos mistos. Por exemplo, esta consulta devolve o "lastName" se estiver presente, ou "Apelido", se não estiver presente.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>Acessor de propriedade entre aspas
Pode também aceder às propriedades utilizando o operador de propriedade delimitado por aspas `[]`. Por exemplo, `SELECT c.grade` e `SELECT c["grade"]` são equivalentes. Esta sintaxe é útil quando é necessário utilizar como uma propriedade que contém carateres especiais, espaços ou acontece partilhar o mesmo nome de uma palavra-chave do SQL Server ou uma palavra reservada.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>Cláusula SELECT
A cláusula SELECT (**`SELECT <select_list>`**) é obrigatória e especifica quais os valores são obtidos a partir da consulta, tal como no ANSI SQL. O subconjunto que é foram filtrado por cima os documentos de origem são transmitidos para a projecção fase, onde os valores JSON especificados são obtidos e é criado um novo objeto JSON, para cada entrada transmitida no mesmo. 

O exemplo seguinte mostra uma consulta SELECT típica. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propriedades aninhadas
No exemplo seguinte, está a projetar duas propriedades aninhadas `f.address.state` e `f.address.city`.

**Consulta**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projecção também suporta expressões de JSON, conforme mostrado no exemplo seguinte:

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Vamos ver a função de `$1` aqui. O `SELECT` cláusula tem de criar um objeto JSON e uma vez que é fornecida nenhuma chave, utilizamos o argumento implícita os nomes de variáveis começadas `$1`. Por exemplo, esta consulta devolve duas variáveis de argumento implícita, com a etiqueta `$1` e `$2`.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Aliasing
Agora vamos expandir o exemplo acima com aliasing explícita de valores. Tal como está a utilizar para o aliasing de palavra-chave. É opcional, conforme mostrado ao projetar o segundo valor como `NameInfo`. 

No caso de uma consulta tem duas propriedades com o mesmo nome, o aliasing deve ser utilizada para mudar o nome de uma ou ambas as propriedades, para que possam resolver esta ambiguidade no resultado de prevista.

**Consulta**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expressões escalares
Para além das referências de propriedade, cláusula SELECT também suporta expressões escalares constantes, expressões aritméticas, expressões lógicas, etc. Por exemplo, aqui está uma consulta de "Olá mundo" simples.

**Consulta**

    SELECT "Hello World"

**Resultados**

    [{
      "$1": "Hello World"
    }]


Eis um exemplo mais complexo que utiliza uma expressão escalar.

**Consulta**

    SELECT ((2 + 11 % 7)-2)/3    

**Resultados**

    [{
      "$1": 1.33333
    }]


No exemplo seguinte, o resultado da expressão escalar é um valor booleano.

**Consulta**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**Resultados**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Criação do objeto e a matriz
Outra funcionalidade chave do SQL Server de API do DocumentDB é a criação do objecto/matriz. No exemplo anterior, tenha em atenção que é criado um novo objeto JSON. Da mesma forma, um pode também criar matrizes conforme mostrado nos exemplos seguintes:

**Consulta**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**Resultados**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a id="ValueKeyword"></a>Palavra-chave do valor
O **valor** palavra-chave fornece uma forma para devolver o valor JSON. Por exemplo, a consulta abaixo devolve o valor de escalar `"Hello World"` em vez de `{$1: "Hello World"}`.

**Consulta**

    SELECT VALUE "Hello World"

**Resultados**

    [
      "Hello World"
    ]


A seguinte consulta devolve o valor JSON sem o `"address"` etiqueta nos resultados.

**Consulta**

    SELECT VALUE f.address
    FROM Families f    

**Resultados**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

O exemplo seguinte expande esta opção para mostrar como devolver valores de primitivos JSON (nível de folha da árvore JSON). 

**Consulta**

    SELECT VALUE f.address.state
    FROM Families f    

**Resultados**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>* Operador
O operador especial (*) é suportado para projetar o documento como-é. Quando utilizado, tem de ser o único campo previsto. Enquanto uma consulta como `SELECT * FROM Families f` for válido, `SELECT VALUE * FROM Families f ` e `SELECT *, f.id FROM Families f ` não são válidos.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

### <a id="TopKeyword"></a>Operador TOP
A palavra-chave superior pode ser utilizada para limitar o número de valores de uma consulta. Quando superior é utilizado em conjunto com a cláusula ORDER BY, o conjunto de resultados é limitado para o primeiro número de N dos valores ordenados; caso contrário, devolve o primeiro número de N de resultados por uma ordem não definida. Como melhor prática, numa instrução SELECT, utilize sempre uma cláusula ORDER BY com a cláusula TOP. Esta é a única forma de forma previsível indicam as linhas são afetadas pelos principais. 

**Consulta**

    SELECT TOP 1 * 
    FROM Families f 

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

Parte superior pode ser utilizado com um valor constante (conforme mostrado acima) ou com um valor de variável utilizar consultas parametrizadas. Para obter mais detalhes, consulte consultas parametrizadas abaixo.

### <a id="Aggregates"></a>Funções de agregação
Também pode efetuar agregações na `SELECT` cláusula. As funções de agregação executar um cálculo num conjunto de valores e devolvem um valor único. Por exemplo, a seguinte consulta devolve a contagem de documentos famílias dentro da coleção.

**Consulta**

    SELECT COUNT(1) 
    FROM Families f 

**Resultados**

    [{
        "$1": 2
    }]

Também pode devolver o valor escalar o agregado utilizando o `VALUE` palavra-chave. Por exemplo, a seguinte consulta devolve a contagem de valores como um número único:

**Consulta**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Resultados**

    [ 2 ]

Também pode efetuar agregações em combinação com os filtros. Por exemplo, a seguinte consulta devolve a contagem de documentos com o endereço do Estado de Washington.

**Consulta**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Resultados**

    [ 1 ]

A tabela seguinte mostra a lista de funções de agregação suportadas na API do DocumentDB. `SUM`e `AVG` são efetuadas através de valores numéricos, enquanto que `COUNT`, `MIN`, e `MAX` pode ser efetuada através de números, cadeias, em booleanos e valores nulos. 

| Utilização | Descrição |
|-------|-------------|
| CONTAGEM | Devolve o número de itens na expressão. |
| SOMA   | Devolve a soma de todos os valores na expressão. |
| MÍN.   | Devolve o valor mínimo na expressão. |
| MÁX.   | Devolve o valor máximo na expressão. |
| MÉDIA   | Devolve a média dos valores existentes na expressão. |

Os agregados também podem ser efetuados através de resultados de uma iteração de matriz. Para obter mais informações, consulte [iteração de matriz em consultas](#Iteration).

> [!NOTE]
> Ao utilizar o Explorador de consulta do portal do Azure, tenha em atenção que as consultas de agregação podem devolver os resultados parcialmente agregados através de uma página de consulta. Os SDKs produz um valor único cumulativo entre todas as páginas. 
> 
> Para executar consultas de agregação com o código, terá de SDK do .NET 1.12.0, .NET Core SDK 1.1.0 ou o SDK de Java 1.9.5 ou superior.    
>

## <a id="OrderByClause"></a>Cláusula ORDER BY
Como no ANSI-SQL, pode incluir uma cláusula Order By opcional ao consultar. A cláusula pode incluir um argumento ASC/DESC opcional para especificar a ordem na qual os resultados tem de ser obtidos.

Por exemplo, aqui está uma consulta que obtém famílias por ordem de nome da cidade residente.

**Consulta**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**Resultados**

    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"    
      }
    ]

E Eis uma consulta que obtém famílias de ordem da data de criação, que é armazenada como um número que representa a época de tempo, revertidos, tempo decorrido desde 1 de Janeiro de 1970 em segundos.

**Consulta**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**Resultados**

    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472    
      }
    ]

## <a id="Advanced"></a>Conceitos de base de dados avançadas e as consultas SQL

### <a id="Iteration"></a>Iteração
Foi adicionada uma construção novo através do **IN** palavra-chave no DocumentDB API SQL para fornecer suporte para iterating através de matrizes JSON. A origem FROM fornece suporte para iteração. Vamos começar com o exemplo seguinte:

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Agora vamos ver outra consulta efetua iteração através de elementos subordinados na coleção. Tenha em atenção a diferença na matriz da saída. Neste exemplo divide `children` e flattens os resultados para uma matriz única.  

**Consulta**

    SELECT * 
    FROM c IN Families.children

**Resultados**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Isto pode ser mais utilizado para filtrar cada entrada da matriz individuais conforme mostrado no exemplo seguinte:

**Consulta**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Resultados**  

    [{
      "givenName": "Lisa"
    }]

Também pode executar agregação sobre o resultado da iteração de matriz. Por exemplo, a seguinte consulta conta o número de elementos subordinados entre todas as famílias.

**Consulta**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Resultados**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Associações
Numa base de dados relacional, é importante a necessidade de associação em tabelas. É o corollary lógica para estruturar esquemas normalizadas. Contrária ao disposto, API de DocumentDB lida com o modelo de dados denormalized de documentos sem esquema. Este é o equivalente lógico de uma "associação automática".

A sintaxe que suporta o idioma é a União de associação < from_source2 > de < from_source1 >... ASSOCIAÇÃO < from_sourceN >. Em geral, esta ação devolve um conjunto de **N**- cadeias de identificação (cadeia de identificação com **N** valores). Cada cadeia de identificação tem valores produzidos pelo iterating todos os aliases de coleção ao longo do respetivos respetivos conjuntos. Por outras palavras, este é um produto cruzado completo dos conjuntos de participar na União.

Os exemplos seguintes mostram como funciona a cláusula JOIN. No exemplo seguinte, o resultado é vazio desde o produto cruzado de cada documento a partir da origem e um conjunto vazio está vazio.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Resultados**  

    [{
    }]


No exemplo seguinte, a associação é entre a raiz do documento e o `children` subroot. É um produto cruzado entre dois objetos JSON. O facto de que os elementos subordinados é uma matriz não tem efeito na União, uma vez que vamos são lidar com um único de raiz que está a matriz de elementos subordinados. Por conseguinte, o resultado contém apenas dois resultados, uma vez que o produto cruzado de cada documento com a matriz gera exatamente apenas um documento.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


O exemplo seguinte mostra uma associação mais convencional:

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



A primeira coisa a ter em atenção é que o `from_source` do **associar** cláusula é um iterator. Por isso, o fluxo neste caso, é o seguinte:  

* Expanda a cada elemento subordinado **c** na matriz.
* Aplicam-se de um produto cruzado com a raiz do documento **f** com cada elemento subordinado **c** que foi simplificado no primeiro passo.
* Por fim, o objeto de raiz do projeto **f** apenas a propriedade name. 

O primeiro documento (`AndersenFamily`) contém apenas um elemento subordinado, pelo que o conjunto de resultados contém apenas um único objeto correspondente a este documento. O segundo documento (`WakefieldFamily`) contém dois elementos subordinados. Por isso, o produto cruzado produz um objeto separado para cada elemento subordinado deste modo, resultando em dois objetos, um para cada subordinado correspondente a este documento. Os campos de raiz em ambos os documentos estes são os mesmos, tal como seria de esperar um produto cruzado.

O utilitário real da associação a um consiste em cadeias de identificação de forma do produto cruzado numa forma que caso contrário, é difícil de projeto. Além disso, como podemos ver no exemplo abaixo, pode filtrar a combinação de uma cadeia de identificação que permite que o utilizador optou por uma condição satisfeita pelas cadeias de identificação global.

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Resultados**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Este exemplo é uma extensão natural do exemplo anterior e efetua uma junção dupla. Por isso, o produto cruzado podem ser visualizado como o seguinte pseudo código:

    for-each(Family f in Families)
    {    
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`tem um subordinado que tenha um animal de estimação. Por isso, o produto cruzado gera uma linha (1\*1\*1) desta família. No entanto, WakefieldFamily tem dois subordinados, mas apenas um subordinado "Jesse" tem pets. Jesse tem dois pets apesar. Por conseguinte, o produto cruzado gera 1\*1\*2 = 2 linhas desta família.

No exemplo seguinte, há um filtro adicional na `pet`. Isto exclui todas as cadeias de identificação em que o nome de animal de estimação não é "Cópias sombra". Repare que estamos a conseguir criar cadeias de identificação de matrizes de filtro em qualquer um dos elementos de cadeia de identificação e qualquer combinação dos elementos do projeto. 

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Resultados**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>Integração de JavaScript
BD do Cosmos do Azure fornece um modelo de programação para executar a lógica de aplicação com base em JavaScript diretamente nas coleções em termos de acionadores e procedimentos armazenados. Isto permite ambas:

* Capacidade de efetuar as operações CRUD transacionais de elevado desempenho e consultas contra documentos numa coleção em virtude a integração profunda do tempo de execução do JavaScript diretamente dentro do motor de base de dados. 
* Uma modelação natural de fluxo de controlo, variável de âmbito, atribuição e integração de excepção a processar primitivos com transações da base de dados. Para obter mais detalhes sobre o suporte de base de dados do Azure Cosmos para integração de JavaScript, consulte a documentação de programação do lado do servidor para JavaScript.

### <a id="UserDefinedFunctions"></a>Funções definidas pelo utilizador (UDFs)
Juntamente com os tipos já definidos neste artigo, o SQL Server de API do DocumentDB fornece suporte para o funções de definido pelo utilizador (UDF). Em particular, escalares UDFs são suportadas em que os programadores podem passar zero ou vários argumentos e devolver um resultado único argumento novamente. Cada um destes argumentos está marcada para ser valores legais de JSON.  

A sintaxe de SQL de API do DocumentDB é expandida para suportar a lógica de aplicação personalizada utilizando estas funções definidas pelo utilizador. UDFs podem ser registados com o DocumentDB API e, em seguida, ser referenciadas como parte de uma consulta SQL. Na verdade, os UDFs exquisitely foram concebidos para ser invocado pelo consultas. Como um corollary para esta opção, UDFs não têm acesso ao objeto de contexto com os outros tipos de JavaScript (acionadores e procedimentos armazenados). Uma vez que executar consultas como só de leitura, podem executar no principal ou nas réplicas secundárias. Por conseguinte, UDFs são concebidos para serem executados em réplicas secundárias, ao contrário de outros tipos de JavaScript.

Abaixo está um exemplo de como um UDF pode ser registado na base de dados do Cosmos DB, especificamente sob uma coleção de documentos.

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

O exemplo anterior cria um UDF cujo nome é `REGEX_MATCH`. Aceita dois valores de cadeia JSON `input` e `pattern` e verifica se a primeira correspondências o padrão especificado no segundo utilizando a função de string.match() do JavaScript.

Agora podemos utilizar esta UDF numa consulta numa projecção. Tem de ser qualificados UDFs com o prefixo de maiúsculas e minúsculas de "udf." Quando chamado a partir de consultas. 

> [!NOTE]
> Antes de 3/17/2015 Cosmos DB suportado UDF chamadas sem "udf." prefixo, como SELECIONAR REGEX_MATCH(). Neste padrão de chamada foi preterido.  
> 
> 

**Consulta**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Resultados**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

UDF também pode ser utilizado dentro de um filtro conforme mostrado no exemplo abaixo, também qualificada com "udf." prefixo:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Resultados**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Essencialmente, UDFs são expressões escalares válidas e podem ser utilizados em projeções e os filtros. 

Para expandir num potência de UDFs, vamos ver outro exemplo com lógica condicional:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


Segue-se um exemplo que exercises UDF.

**Consulta**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**Resultados**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Como demonstram exemplos anteriores, UDFs integram potência de linguagem JavaScript com o SQL de API do DocumentDB a facultar uma interface avançada programável fazer lógica complexa de procedimento, condicional com a ajuda das capacidades de tempo de execução de JavaScript integradas.

SQL de API do DocumentDB fornece os argumentos para os UDFs para cada documento na origem de fase atual (cláusula WHERE ou cláusula SELECT) do processamento de UDF. O resultado é incorporado no pipeline de execução geral totalmente integrada. Se as propriedades referidas pela UDF parâmetros não estão disponíveis no valor de JSON, o parâmetro é considerado como não definida e, por conseguinte, a invocação de UDF inteiramente é ignorada. Da mesma forma se o resultado do UDF não for definido, não se encontra incluída nos resultados. 

Em resumo, UDFs são excelente ferramentas para efetuar a lógica de negócio complexos como parte da consulta.

### <a name="operator-evaluation"></a>Avaliação do operador
Cosmos DB, por virtue ser uma base de dados JSON, desenha parallels com operadores de JavaScript e a semântica de avaliação. Enquanto a base de dados do Cosmos tenta preservar a semântica de JavaScript em termos de suporte JSON, a avaliação da operação deviates em alguns casos.

No SQL Server de API do DocumentDB, ao contrário no SQL Server tradicional, os tipos de valores, muitas vezes, não sejam conhecidos até que os valores são obtidos a partir da base de dados. Para executar com eficiência as consultas, na maioria dos operadores têm requisitos de tipo restrito. 

SQL do DocumentDB API não efetuar conversões implícitas, ao contrário do JavaScript. Por exemplo, como uma consulta `SELECT * FROM Person p WHERE p.Age = 21` corresponde aos documentos que contenham uma propriedade de idade cujo valor é de 21. Qualquer outro documento cuja propriedade idade corresponde variações possivelmente infinita cadeia "21" ou outro como "021", "21.0", "0021", "00021", etc. não será correspondido. Este procedimento em contrapartida JavaScript onde os valores de cadeia são implicitamente casted para números (com base no operador, por ex: = =). Esta opção é fundamental para o índice eficiente correspondente no DocumentDB API SQL. 

## <a name="parameterized-sql-queries"></a>Consultas parametrizadas do SQL Server
BD do cosmos suporta consultas com os parâmetros expressados com familiar @ notação. SQL parametrizada fornece processamento robusto e escape de entrada, o utilizador a impedir a exposição acidental de dados através de injeção de SQL. 

Por exemplo, pode escrever uma consulta que aceita o apelido e o estado de endereço como parâmetros e, em seguida, execute-o para vários valores de apelido e o estado de endereço com base na entrada de utilizador.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Este pedido, em seguida, pode ser enviado para a BD do Cosmos como uma consulta JSON parametrizada, como mostrado abaixo.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

O argumento para a parte superior pode ser definido utilizando consultas parametrizadas, como mostrado abaixo.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Valores de parâmetro podem ser qualquer um JSON válido (cadeias, números, em booleanos, nulos, mesmo as matrizes ou aninhados JSON). Também uma vez que Cosmos base de dados sem esquema, os parâmetros não são validados em relação a qualquer tipo.

## <a id="BuiltinFunctions"></a>Funções incorporadas
BD do cosmos também suporta um número de funções incorporadas para operações comuns que podem ser utilizadas em consultas como as funções definidas pelo utilizador (UDFs).

| Grupo de função          | Operações                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Funções matemática  | ABS, limite, EXP, piso, registo, LOG10, energia, ROUND, início de sessão, SQRT, PARÊNTESES, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, graus, PI, radianos, único e TAN |
| A verificar as funções de tipo | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED e IS_PRIMITIVE                                                           |
| Funções de cadeia        | CONCAT, contém, ENDSWITH, INDEX_OF, à esquerda, comprimento, inferior, LTRIM, substituir, a replicação, inversa, direita, RTRIM, STARTSWITH, SUBCADEIA e superior       |
| Funções de matriz         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE                                                                                         |
| Funções geográficos       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID e ST_ISVALIDDETAILED                                                                           | 

Se está a utilizar uma função definida pelo utilizador (UDF) para o qual uma função incorporada está agora disponível, deve utilizar a função incorporada correspondente como vai ser mais rápida executar e a forma mais eficiente. 

### <a name="mathematical-functions"></a>Funções matemática
As funções matemática efetuar um cálculo, com base nos valores de entrada que são fornecidos como argumentos e devolvem um valor numérico. Eis uma tabela de funções de matemática incorporadas suportadas.


| Utilização | Descrição |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | Devolve o valor absoluto (positivo) da expressão numérica especificada. |
| [LIMITE (num_expr)](#bk_ceiling) | Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada. |
| [PISO (num_expr)](#bk_floor) | Devolve o maior número inteiro menor ou igual a expressão numérica especificada. |
| [EXP (num_expr)](#bk_exp) | Devolve o expoente da expressão numérica especificada. |
| [REGISTO (num_expr [, base])](#bk_log) | Devolve o logaritmo natural da expressão numérica especificada ou o logaritmo utilizando a base especificada |
| [Log10 (num_expr)](#bk_log10) | Devolve o valor logarítmica base 10 da expressão numérica especificada. |
| [ARREDONDAR (num_expr)](#bk_round) | Devolve um valor numérico, arredondado para o valor de número inteiro mais próximo. |
| [TRUNC (num_expr)](#bk_trunc) | Devolve um valor numérico, truncada de acordo com o valor de número inteiro mais próximo. |
| [SQRT (num_expr)](#bk_sqrt) | Devolve a raiz quadrada da expressão numérica especificada. |
| [PARÊNTESES (num_expr)](#bk_square) | Devolve o quadrado da expressão numérica especificado. |
| [ENERGIA (num_expr, num_expr)](#bk_power) | Devolve a energia da expressão numérica especificada para o valor especificado. |
| [Início de sessão (num_expr)](#bk_sign) | Devolve o valor de início de sessão (-1, 0, 1) da expressão numérica especificada. |
| [ACOS (num_expr)](#bk_acos) | Devolve o ângulo em radianos, cujo co-seno é a expressão numérica especificada; Também denominado o arco de co-seno. |
| [ASIN (num_expr)](#bk_asin) | Devolve o ângulo em radianos, cujo seno é a expressão numérica especificada. Isto também é denominado o arco de seno. |
| [ATAN (num_expr)](#bk_atan) | Devolve o ângulo em radianos, cuja tangente é a expressão numérica especificada. Isto também é denominado o arco de tangente. |
| [ATN2 (num_expr)](#bk_atn2) | Devolve o ângulo em radianos, entre o eixo x positivo e ray da origem para o ponto (y, x), onde x e y são os valores das duas expressões de vírgula flutuante especificado. |
| [COS (num_expr)](#bk_cos) | Devolve o co-seno do ângulo especificado, trigonometric em radianos, a expressão especificada. |
| [COT (num_expr)](#bk_cot) | Devolve a co-tangente do ângulo especificado, trigonometric em radianos, a expressão numérica especificado. |
| [GRAUS (num_expr)](#bk_degrees) | Devolve o ângulo correspondente em graus para um ângulo especificado em radianos. |
| [INSTALADOR DE PLATAFORMA ()](#bk_pi) | Devolve o valor de PI constante. |
| [RADIANOS (num_expr)](#bk_radians) | Devolve radianos quando uma expressão numérica, em graus, é introduzida. |
| [ÚNICO (num_expr)](#bk_sin) | Devolve o seno do ângulo especificado, trigonometric em radianos, a expressão especificada. |
| [TAN (num_expr)](#bk_tan) | Devolve a tangente da expressão de entrada, a expressão especificada. |

Por exemplo, pode agora executar consultas como o seguinte:

**Consulta**

    SELECT VALUE ABS(-4)

**Resultados**

    [4]

A principal diferença entre as funções do Cosmos DB em comparação comparada ANSI SQL é que foram concebidas para funcionar bem com os dados de esquema sem esquema e misto. Por exemplo, se tiver um documento em que a propriedade Size está em falta ou tem um valor não numéricos, como "desconhecido", em seguida, o documento é ignorado através de, em vez de devolver um erro.

### <a name="type-checking-functions"></a>A verificar as funções de tipo
As funções de verificação do tipo permitem-lhe verificar o tipo de uma expressão dentro de consultas SQL. Funções de verificação de tipo podem ser utilizadas para determinar o tipo de propriedades dentro de documentos no momento quando é desconhecido ou variável. Eis uma tabela de tipo incorporado suportado, a verificação de funções.

<table>
<tr>
  <td><strong>Utilização</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Devolve um valor boleano que indica se o tipo do valor é uma matriz.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Devolve um valor boleano que indica se o tipo do valor é um valor booleano.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Devolve um valor boleano que indica se o tipo do valor é nulo.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Devolve um valor boleano que indica se o tipo do valor é um número.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Devolve um valor boleano que indica se o tipo do valor é um objeto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Devolve um valor boleano que indica se o tipo do valor é uma cadeia.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Devolve um valor boleano que indica se a propriedade foi atribuída um valor.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Devolve um valor boleano que indica se o tipo do valor é uma cadeia, número, booleano ou nulo.</td>
</tr>

</table>

Utilizando estas funções, agora, pode executar consultas como o seguinte:

**Consulta**

    SELECT VALUE IS_NUMBER(-4)

**Resultados**

    [true]

### <a name="string-functions"></a>Funções de cadeia
As seguintes funções escalares efetuar uma operação num valor de cadeia de entrada e devolvem uma cadeia, o valor numérico ou booleano. Eis uma tabela de funções de cadeia incorporada:

| Utilização | Descrição |
| --- | --- |
| [COMPRIMENTO (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Devolve o número de carateres da expressão de cadeia especificada |
| [CONCAT (str_expr str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Devolve uma cadeia que é o resultado da concatenar duas ou mais valores de cadeia. |
| [SUBCADEIA (str_expr num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Devolve a parte de uma expressão de cadeia. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Devolve um booleano que indica se a primeira expressão de cadeia termina com o segundo |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Devolve um booleano que indica se a primeira expressão de cadeia termina com o segundo |
| [CONTÉM (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Devolve um booleano que indica se a primeira cadeia de expressão contém o segundo. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Devolve a posição inicial da primeira ocorrência da segunda cadeia de expressão na primeira expressão de cadeia especificada ou -1 se a cadeia não foi encontrada. |
| [À esquerda (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Devolve a parte esquerda de uma cadeia com o número especificado de carateres. |
| [À direita (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Devolve a parte direita de uma cadeia com o número especificado de carateres. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Devolve uma expressão de cadeia após remove espaços em branco à esquerda. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Devolve uma expressão de cadeia após truncar todos os espaços em branco de à direita. |
| [INFERIOR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Devolve uma expressão de cadeia após a conversão de dados do caráter em maiúsculas em minúsculas. |
| [MAIÚSCULA (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Devolve uma expressão de cadeia após a conversão de dados de carateres em minúsculas em maiúsculas. |
| [SUBSTITUA (str_expr str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Substitui todas as ocorrências de um valor de cadeia especificada com outro valor de cadeia. |
| [REPLICAR (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-reference#bk_replicate) |Repete-se um valor de cadeia um número de vezes especificado. |
| [INVERSA (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Devolve a ordem inversa de um valor de cadeia. |

Utilizando estas funções, pode agora executar consultas como o seguinte. Por exemplo, pode devolver o nome de família em maiúsculas da seguinte forma:

**Consulta**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Resultados**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Ou concatenar cadeias como neste exemplo:

**Consulta**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Resultados**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Funções de cadeia também podem ser utilizadas na cláusula WHERE para filtrar os resultados, como no exemplo seguinte:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Resultados**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funções de matriz
As seguintes funções escalares efetuar uma operação num valor de entrada da matriz e devolver um valor numérico, valor de cadeia Boolean ou matriz. Eis uma tabela de funções de matriz incorporada:

| Utilização | Descrição |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Devolve o número de elementos da expressão de matriz especificada. |
| [ARRAY_CONCAT (arr_expr arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Devolve uma matriz que é o resultado da concatenar duas ou mais valores de matriz. |
| [ARRAY_CONTAINS (arr_expr expr [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Devolve um valor boleano que indica se a matriz contém o valor especificado. Pode especificar se a correspondência total ou parcial. |
| [ARRAY_SLICE (arr_expr num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Devolve a parte de uma expressão de matriz. |

Funções de matriz podem ser utilizadas para manipular matrizes no JSON. Por exemplo, aqui está uma consulta que devolva todos os documentos em que é um dos principais "Round Robin Wakefield". 

**Consulta**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Pode especificar um fragmento parcial para efetuar a correspondência de elementos na matriz. A consulta seguinte localiza todos os elementos principais com o `givenName` de `Robin`.

**Consulta**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]


Segue-se outro exemplo que utiliza ARRAY_LENGTH para obter o número de elementos subordinados por família.

**Consulta**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Resultados**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Funções geográficos
BD do cosmos suporta as seguintes funções incorporadas abra Geoespacial Consortium (OGC) para consultar o geoespacial. 

<table>
<tr>
  <td><strong>Utilização</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Devolve a distância entre duas expressões de ponto de GeoJSON, polígono ou LineString.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Devolve uma expressão booleana que indica se o objeto GeoJSON primeiro (ponto, polígono ou LineString) é no objeto GeoJSON segundo (ponto, polígono ou LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Devolve uma expressão booleana que indica se os dois objetos de GeoJSON especificados (ponto, polígono ou LineString) intersect.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devolve um valor booleano que indica se a expressão de ponto de GeoJSON, polígono ou LineString especificada é válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devolve um valor JSON que contém um valor boleano valor se a expressão de ponto de GeoJSON, polígono ou LineString especificada é válida e se inválido, além do motivo como um valor de cadeia.</td>
</tr>
</table>

Funções geográficos podem ser utilizadas para executar consultas de proximidade contra dados geográficos. Por exemplo, aqui está uma consulta que devolva todos os documentos famílias que estejam dentro de 30 km da localização especificada utilizando a função incorporada ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Para obter mais detalhes sobre o suporte de geoespacial na base de dados do Cosmos, consulte [trabalhar com dados geoespacial do BD Azure Cosmos](geospatial.md). Que encapsula num wrapper funções geográficos e a sintaxe SQL para a base de dados do Cosmos. Agora vamos ver como funciona e como interage com a sintaxe de consulta do LINQ viu até ao momento.

## <a id="Linq"></a>LINQ to SQL de API do DocumentDB
LINQ é um modelo de programação do .NET que expresse precisa cálculo como consultas em fluxos de objetos. Cosmos DB fornece uma biblioteca do lado do cliente para a interface com LINQ pelo mesmo uma conversão entre objetos JSON e .NET e um mapeamento de um subconjunto de consultas LINQ para consultas de base de dados do Cosmos. 

A imagem abaixo mostra a arquitetura de suportar consultas LINQ com a base de dados do Cosmos.  Utilizando o cliente do Cosmos DB, os programadores podem criar um **IQueryable** objeto consulta diretamente o fornecedor de consulta do Cosmos DB, que, em seguida, traduz a consulta LINQ para uma consulta de base de dados do Cosmos. A consulta, em seguida, é passada para o servidor de base de dados do Cosmos para obter um conjunto de resultados no formato JSON. Os resultados apresentados são anular a serialização para um fluxo de objetos de .NET do lado do cliente.

![Arquitetura de suportar consultas LINQ com a API do DocumentDB - sintaxe SQL, idioma de consulta JSON, conceitos de base de dados e as consultas SQL][1]

### <a name="net-and-json-mapping"></a>Mapeamento de JSON e .NET
O mapeamento entre objetos .NET e documentos JSON é natural - cada campo de membro de dados está mapeado para um objeto JSON, onde o nome do campo está mapeado para a parte "chave" do objeto e a parte "valor" está em modo recursivo mapeado para a parte do valor do objeto. Considere o seguinte exemplo: objeto da família o criado está mapeado para o documento JSON, conforme mostrado abaixo. E vice-versa, o documento JSON está mapeado para um objeto de .NET.

**Classe de c#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ para tradução de SQL
O fornecedor de consulta de base de dados do Cosmos efetua um mapeamento de esforço melhor por uma consulta LINQ para uma consulta de SQL de BD do Cosmos. Na descrição do seguinte, partimos do pressuposto que o leitor tem uma familiaridade básica de LINQ.

Em primeiro lugar, para o sistema de tipo, suportamos todas as JSON tipos primitivos – null, string, Booleano e tipos numéricos. São suportados apenas estes tipos JSON. As expressões escalares seguintes são suportadas.

* Os valores constantes – incluem os valores constantes dos tipos de dados primitivos momento que é avaliada a consulta.
* Expressão de índice de matriz/propriedade – estas expressões consulte a propriedade de um objeto ou um elemento de matriz.
  
     família. ID;    Family.children[0].familyName;    Family.children[0].grade;    Family.children[n].grade; n é uma variável de int
* Expressões aritméticas - incluem expressões aritméticas comuns em valores numéricos e booleanos. Para obter a lista completa, consulte a especificação do SQL Server.
  
     2 * family.children[0].grade;    x + y;
* Expressão de comparação de cadeia - estes incluem a comparar um valor de cadeia para um valor de constante de cadeia.  
  
     mother.familyName = = "Santos";    child.givenName = = s; s é uma variável de cadeia
* Objeto/matriz expressão criação - estas expressões retorno um objeto do tipo de valor composta ou tipo anónimo ou uma matriz desses objetos. Estes valores podem ser aninhados.
  
     Principal novo {familyName = "Santos", givenName = "João"}; novo {primeiro = 1, segundo = 2}; um tipo anónimo com dois campos              
     novo int [] {child.grade 3, 5};

### <a id="SupportedLinqOperators"></a>Lista de operadores LINQ suportados
Eis uma lista de operadores LINQ suportadas no fornecedor LINQ incluídos com o SDK do .NET DocumentDB.

* **Selecione**: projeções traduzir para o SQL Server que SELECIONAR, incluindo a construção de objeto
* **Onde**: filtros convertidos para o SQL Server onde e suportar tradução entre & &, | | e! para os operadores do SQL Server
* **SelectMany**: permite unwinding das matrizes para a cláusula de associação do SQL Server. Pode ser utilizado para as expressões para filtrar em elementos de matriz de cadeia/aninhadas
* **OrderBy e OrderByDescending**: traduz-se por ordem ascendente/descendente
* **Contagem**, **soma**, **Min**, **máximo**, e **médio** operadores de agregação e os respetivos equivalentes de async  **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, e **AverageAsync**.
* **CompareTo**: traduz para comparações de intervalo. Normalmente utilizado para cadeias de uma vez que não estiverem comparáveis no .NET
* **Tirar**: traduz até ao topo de SQL para limitar os resultados de uma consulta
* **Funções de bibliotecas**: suporta a conversão do. Abs, Acos, Asin do NET, Atan, limite, Cos Exp, piso, registo, Log10, Pow, Round, início de sessão, único, Sqrt, Tan, Truncate as funções incorporadas do SQL Server equivalente.
* **As funções de cadeia**: suporta a conversão do. Concat, contém, EndsWith do NET, IndexOf, Count, ToLower, TrimStart, substituir, inversa, TrimEnd, StartsWith, subcadeia, ToUpper as funções incorporadas do SQL Server equivalente.
* **As funções de matriz**: suporta a conversão do. Do NET Concat, contém e contagem as funções incorporadas do SQL Server equivalente.
* **As funções da extensão Geoespacial**: suporta a conversão de métodos de stub distância, em IsValid e IsValidDetailed para as funções incorporadas do SQL Server equivalentes.
* **Definido pelo utilizador função de extensão de função**: suporta a conversão do método stub UserDefinedFunctionProvider.Invoke para a função definida pelo utilizador correspondente.
* **Diversas**: suporta a conversão do coalesce e operadores condicionais. Estes podem ser traduzidos contém a cadeia contém, ARRAY_CONTAINS ou o SQL Server está em dependendo do contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL
Seguem-se alguns exemplos que ilustram a forma como algumas dos operadores de consulta do LINQ padrão são convertidas para baixo para consultas de base de dados do Cosmos.

#### <a name="select-operator"></a>Selecione o operador
A sintaxe é `input.Select(x => f(x))`, onde `f` é uma expressão escalar.

**Expressão lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expressão lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expressão lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>SelectMany operador
A sintaxe é `input.SelectMany(x => f(x))`, onde `f` é uma expressão escalar que devolve um tipo de coleção.

**Expressão lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Onde operador
A sintaxe é `input.Where(x => f(x))`, onde `f` é uma expressão escalar, que devolve um valor booleano.

**Expressão lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expressão lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Consultas SQL compostas
Os operadores acima podem ser compostos para formar consultas mais poderosas. Uma vez que Cosmos DB suporta coleções aninhadas, composição pode ser concatenada ou aninhada.

#### <a name="concatenation"></a>Concatenação
A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Pode iniciar uma consulta concatenada com opcional `SelectMany` consulta seguido por vários `Select` ou `Where` operadores.

**Expressão lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expressão lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expressão lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expressão lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Aninhamento
A sintaxe é `input.SelectMany(x=>x.Q())` em que o p é um `Select`, `SelectMany`, ou `Where` operador.

Uma consulta aninhada, a consulta interna é aplicada a cada elemento da coleção externa. Uma funcionalidade importante é que a consulta interna pode referir-se os campos de elementos na colecção externa como associações automáticas.

**Expressão lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expressão lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expressão lambda LINQ**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>Execução de consultas SQL
BD do cosmos expõe recursos através de uma API REST que pode ser chamada por qualquer idioma com capacidade de efetuar pedidos HTTP/HTTPS. Além disso, a base de dados do Cosmos oferece bibliotecas de programação para vários idiomas populares, tais como .NET, Node.js, JavaScript e Python. A API REST e as bibliotecas de vários todos os suportam consultas através do SQL Server. O SDK .NET suporta LINQ consultar para além do SQL Server.

Os exemplos seguintes mostram como criar uma consulta e a submetê-las em relação a uma conta de base de dados de base de dados do Cosmos.

### <a id="RestAPI"></a>REST API
BD do cosmos oferece um modelo de programação RESTful aberto através de HTTP. Contas de base de dados podem ser aprovisionadas através de uma subscrição do Azure. O modelo de recursos do Cosmos DB é composta por um conjunto de recursos numa conta de base de dados, cada um dos quais é endereçável utilizando um URI lógico e estável. Um conjunto de recursos é referido como um feed neste documento. Uma conta de base de dados consiste num conjunto de bases de dados, cada uma contém várias coleções, cada um dos qual por sua vez conter documentos, UDFs e outros tipos de recursos.

O modelo de interação básico com estes recursos é efetuada através dos verbos HTTP GET, PUT, POST e DELETE com os respetivos interpretação padrão. O verbo POST é utilizado para a criação de um novo recurso, para executar um procedimento armazenado ou para emitir uma consulta de base de dados do Cosmos. As consultas são sempre operações só de leitura com nenhuma efeitos secundários.

Os exemplos seguintes mostram um pedido POST para uma consulta de API do DocumentDB efetuada em relação a uma coleção que contém dois documentos de exemplo que tiver revisto até ao momento. A consulta tem um filtro simple sobre a propriedade de nome JSON. Tenha em atenção a utilização do `x-ms-documentdb-isquery` e Content-Type: `application/query+json` cabeçalhos para indicar que a operação é uma consulta.

**Pedido**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


O segundo exemplo mostra uma consulta mais complexa que devolve resultados vários da associação a um.

**Pedido**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Se os resultados de uma consulta não é possível ajustar dentro de uma única página de resultados, em seguida, a API REST devolve um token de continuação através de `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginação resultados, incluindo o cabeçalho nos resultados subsequentes. O número de resultados por página também pode ser controlado através de `x-ms-max-item-count` cabeçalho número. Se a consulta especificada tem uma função de agregação, como `COUNT`, em seguida, a página de consulta poderá devolver um valor parcialmente agregado a página de resultados. Os clientes tem de efetuar uma agregação de segundo nível através destes resultados para produzir os resultados finais, por exemplo, soma através de contagens devolvidas nas páginas individuais para devolver a contagem total.

Para gerir a política de consistência de dados para consultas, utilize o `x-ms-consistency-level` cabeçalho, como todos os pedidos de REST API. Por razões de consistência de sessão, é necessário para a versão mais recente de eco também `x-ms-session-token` cabeçalho do Cookie do pedido de consulta. Política de indexação da coleção consultado também pode influenciar a consistência dos resultados da consulta. Com a predefinição de definições de política de indexação, para coleções o índice é sempre atual com o conteúdo de documentos e os resultados da consulta correspondem a consistência escolhida para dados. Se a política de indexação está flexibilizada para Lazy, as consultas podem devolver resultados obsoletos. Para obter mais informações, consulte [níveis de consistência de BD do Azure Cosmos][consistency-levels].

Se a política de indexação configurada na coleção não suporta a consulta especificada, o servidor de base de dados do Azure Cosmos devolve 400 "pedido incorreto". Este é devolvido para consultas de intervalo contra caminhos configurados para pesquisas de hash (igualdade) e para caminhos explicitamente excluídos da indexação. O `x-ms-documentdb-query-enable-scan` cabeçalho pode ser especificado para permitir que a consulta para efetuar uma análise quando um índice não está disponível.

Pode obter métricas detalhadas na execução de consultas através da definição `x-ms-documentdb-populatequerymetrics` cabeçalho para `True`. Para obter mais informações, consulte [métricas de consulta SQL para a API de DocumentDB do Azure Cosmos DB](documentdb-sql-query-metrics.md).

### <a id="DotNetSdk"></a>SDK DO C# (.NET)
O SDK .NET suporta LINQ e SQL Server a consultar. O exemplo seguinte mostra como efetuar a consulta de filtro simples introduzida anteriormente neste documento.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Este exemplo compara duas propriedades para igualdade dentro de cada documento e utiliza projeções anónimas. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


O exemplo seguinte mostra as associações, expressadas através de LINQ SelectMany.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



O cliente do .NET itera automaticamente através de todas as páginas dos resultados da consulta em blocos de foreach, conforme mostrado acima. As opções de consulta introduzidas na secção de REST API também estão disponíveis utilizando o .NET SDK do `FeedOptions` e `FeedResponse` classes no método CreateDocumentQuery. O número de páginas pode ser controlado através de `MaxItemCount` definição. 

Explicitamente também pode controlar a paginação criando `IDocumentQueryable` utilizando o `IQueryable` objeto, em seguida, ao ler o` ResponseContinuationToken` valores e passou-los fazer uma cópia como `RequestContinuationToken` no `FeedOptions`. `EnableScanInQuery`pode ser definido para permitir análises quando a consulta não é suportada pela política de indexação configurada. Para coleções particionadas, pode utilizar `PartitionKey` para executar a consulta em relação a uma única partição (embora Cosmos DB automaticamente pode extrair esta do texto de consulta), e `EnableCrossPartitionQuery` para executar consultas que poderão ter de ser executada relativamente a várias partições. 

Consulte [amostras de .NET de BD do Azure Cosmos](https://github.com/Azure/azure-documentdb-net) para obter mais exemplos que contém consultas. 

### <a id="JavaScriptServerSideApi"></a>API do JavaScript do lado do servidor
BD do cosmos fornece um modelo de programação para executar a lógica de aplicação com base em JavaScript diretamente nas coleções com acionadores e procedimentos armazenados. A lógica do JavaScript registada um nível de coleção, em seguida, pode emitir operações de base de dados nas operações em documentos de uma dada coleção. Estas operações são encapsuladas em transações ACID ambiente.

O exemplo seguinte mostra como utilizar o queryDocuments no servidor de JavaScript API para tornar as consultas do interior armazenados acionadores e procedimentos.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Referências
1. [Introdução ao Azure Cosmos DB][introduction]
2. [Especificação de Cosmos BD SQL do Azure](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Exemplos do Cosmos DB .NET do Azure](https://github.com/Azure/azure-documentdb-net)
4. [Níveis de consistência de BD do Cosmos do Azure][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. Especificação de JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Consultar as técnicas de avaliação para bases de dados grandes [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Consulta de processamento em sistemas de base de dados relacional paralelas, IEEE computador Society prima, 1994, feitas
11. Lu, Ooi, Tan, processamento de consultas no sistemas de base de dados relacional paralelas, IEEE computador Society prima, 1994, feitas.
12. Christopher Olston Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: um idioma não-para-externa para o processamento de dados, SIGMOD 2008.
13. G. Graefe. A arquitetura de Cascades de Otimização da consulta. IEEE dados Eng. Bull., 18(3): 1995.

[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
---
title: 'DocumentDB do Azure do Cosmos DB API: Sintaxe SQL | Microsoft Docs'
description: "Documentação de referência para o idioma de consulta de SQL de API do DocumentDB do Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: mimig
ms.openlocfilehash: 4907df15fddfb7d8d6128dc994b0920ca601f2c7
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="azure-cosmos-db-documentdb-api-sql-syntax-reference"></a>DocumentDB do Azure do Cosmos DB API: Referência de sintaxe SQL

A API de DocumentDB do Azure Cosmos DB suporta consulta de documentos através de um familiar SQL (Structured Query Language) como gramática através de documentos JSON hierárquicos sem necessidade de um esquema explícito ou criação de índices secundários. Este tópico fornece documentação de referência para o idioma de consulta de SQL de API do DocumentDB.

Para obter instruções sobre o idioma de consulta de SQL de API do DocumentDB, consulte [as consultas SQL para a API de DocumentDB do Azure Cosmos DB](documentdb-sql-query.md).  
  
Também Convidamo-lo para visitar o [Query Playground](http://www.documentdb.com/sql/demo) onde pode tentar BD do Cosmos do Azure e executar consultas SQL no nosso conjunto de dados.  
  
## <a name="select-query"></a>Consulta SELECT  
Obtém os documentos JSON da base de dados. Suporta a avaliação da expressão, projeções, filtragem e associa.  As convenções de utilizada para descrever as instruções SELECT são apresentadas na secção de convenções de sintaxe.  
  
**Sintaxe**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Observações**  
  
 Consulte as seguintes secções para obter detalhes sobre cada cláusula:  
  
-   [Cláusula SELECT](#bk_select_query)  
  
-   [A cláusula FROM](#bk_from_clause)  
  
-   [Cláusula WHERE](#bk_where_clause)  
  
-   [Cláusula ORDER BY](#bk_orderby_clause)  
  
As cláusulas na instrução SELECT tem de ser ordenadas conforme mostrado acima. Pode ser omitido a qualquer um das cláusulas opcionais. Mas, quando são utilizadas cláusulas opcionais, deve aparecer na ordem correta.  
  
**Ordem de processamento de mensagens em fila lógico de instrução SELECT**  
  
A ordem na qual são processadas cláusulas é:  

1.  [A cláusula FROM](#bk_from_clause)  
2.  [Cláusula WHERE](#bk_where_clause)  
3.  [Cláusula ORDER BY](#bk_orderby_clause)  
4.  [Cláusula SELECT](#bk_select_query)  

Tenha em atenção que isto é diferente da ordem em que aparecem na sintaxe. A ordenação é que todos os símbolos novo introduzidos por uma cláusula processada estão visíveis e podem ser utilizados nas cláusulas processadas mais tarde. Por exemplo, aliases declarados numa cláusula FROM estão acessíveis no onde e cláusulas SELECT.  

**Os carateres de espaço em branco e comentários**  

Todos os carateres de espaço em branco que não façam parte de uma cadeia delimitada por aspas ou entre aspas identificador não fazem parte da gramática de idioma e serão ignorados durante a análise.  

O idioma de consulta suporta comentários de estilo de T-SQL, como  

-   Instrução de SQL`-- comment text [newline]`  

Enquanto carateres de espaço em branco e comentários não têm qualquer significância a gramática, tem de ser utilizados para separar os tokens. Por exemplo: `-1e5` é o único tempo token, número`: – 1 e5` é um token minus seguido pelo número de 1 e e5 identificador.  

##  <a name="bk_select_query"></a>Cláusula SELECT  
As cláusulas na instrução SELECT tem de ser ordenadas conforme mostrado acima. Pode ser omitido a qualquer um das cláusulas opcionais. Mas, quando são utilizadas cláusulas opcionais, deve aparecer na ordem correta.  

**Sintaxe**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumentos**  
  
 `<select_specification>`  
  
 Propriedades ou valor para ser selecionado para o conjunto de resultados.  
  
 `'*'`  
  
Especifica que o valor deve ser obtido sem efetuar alterações. Especificamente, se o valor processado é um objeto, serão possível obter todas as propriedades.  
  
 `<object_property_list>`  
  
Especifica a lista de propriedades a recuperar. Cada valor devolvido será um objeto com as propriedades especificadas.  
  
`VALUE`  
  
Especifica que o valor JSON deve ser obtido em vez do objeto JSON completa. Isto, ao contrário `<property_list>` moldam o valor previsto um objeto.  
  
`<scalar_expression>`  
  
Expressão que representa o valor a ser calculado. Consulte [expressões escalares](#bk_scalar_expressions) secção para obter detalhes.  
  
**Observações**  
  
O `SELECT *` sintaxe só é válida se a cláusula FROM tem declaradas exatamente um alias. `SELECT *`Fornece uma projeção de identidade, o que pode ser útil se não for necessária nenhuma projecção. SELECIONE * só é válido se a cláusula FROM for especificado e apresentados apenas uma única origem de entrada.  
  
Tenha em atenção que `SELECT <select_list>` e `SELECT *` são "sugar diferenças sintáticas" e podem ser expressas em alternativa, utilizando instruções SELECT simples, conforme mostrado abaixo.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     é equivalente ao:  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     é equivalente ao:  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Consulte também**  
  
[Expressões escalares](#bk_scalar_expressions)  
[Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a>A cláusula FROM  
Especifica a origem ou origens associadas. A cláusula FROM é opcional. Se não for especificadas, outras cláusulas ainda serão executadas como se a cláusula FROM fornecido um único documento.  
  
**Sintaxe**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumentos**  
  
`<from_source>`  
  
Especifica uma origem de dados, com ou sem um alias. Se não for especificado o alias, irá ser inferido a partir de `<collection_expression>` utilizando os seguintes regras:  
  
-   Se a expressão for um collection_name, collection_name será utilizado como um alias.  
  
-   Se a expressão é `<collection_expression>`, em seguida, property_name, property_name será utilizado como um alias. Se a expressão for um collection_name, collection_name será utilizado como um alias.  
  
TAL COMO`input_alias`  
  
Especifica que o `input_alias` é um conjunto de valores devolvidos pela expressão de coleção subjacente.  
 
`input_alias`EM  
  
Especifica que o `input_alias` deve representam o conjunto de valores obtidos pelo iterating através de todos os elementos de matriz de cada matriz devolvida pela expressão de coleção subjacente. Qualquer valor devolvido pela expressão de coleção subjacente que não é uma matriz é ignorada.  
  
`<collection_expression>`  
  
Especifica a expressão de coleção para ser usada para recuperar os documentos.  
  
`ROOT`  
  
Especifica que esse documento deve ser obtido da predefinição, a coleção atualmente ligada.  
  
`collection_name`  
  
Especifica que esse documento deve ser obtido da coleção fornecida. O nome da coleção tem de corresponder ao nome da coleção atualmente ligado ao.  
  
`input_alias`  
  
Especifica que esse documento deve ser obtido a partir da origem definida pelo alias fornecido.  
  
`<collection_expression> '.' property_`  
  
Especifica esse documento deve ser obtido acedendo a `property_name` elemento de matriz de propriedade ou array_index para todos os documentos obtidos pelo especificada a expressão de coleção.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Especifica esse documento deve ser obtido acedendo a `property_name` elemento de matriz de propriedade ou array_index para todos os documentos obtidos pelo especificada a expressão de coleção.  
  
**Observações**  
  
Todos os aliases fornecido ou inferir no `<from_source>(`s) têm de ser exclusivos. A sintaxe `<collection_expression>.`property_name é o mesmo que `<collection_expression>' ['"property_name"']'`. No entanto, a sintaxe última pode ser utilizada se um nome de propriedade contém um identificador não carateres.  
  
**Falta de propriedades em falta elementos de matriz não definida valores processamento**  
  
Se uma expressão de coleção acede propriedades ou elementos de matriz e que não existe valor, esse valor será ignorado e não continuar a processar.  
  
**Âmbito de contexto de expressão de coleção**  
  
Uma expressão de coleção pode ser confinados por coleção ou no âmbito do documento:  
  
-   Uma expressão é coleção no âmbito, se a origem subjacente da expressão de coleção é a raiz ou `collection_name`. Este tipo uma expressão representa um conjunto de documentos obtida diretamente a coleção e não está dependente de processamento de outras expressões de coleção.  
  
-   Uma expressão é documento no âmbito, se a origem subjacente da expressão de coleção é `input_alias` introduzidas anteriormente na consulta. Este tipo uma expressão representa um conjunto de documentos obtido através da avaliação da expressão de coleção no âmbito de cada documento que pertencem ao conjunto de associados à coleção de um alias.  O conjunto resultante será uma União de conjuntos obtido através da avaliação da expressão de coleção para cada um dos documentos no conjunto de subjacente.  
  
**Associações**  
  
Na versão atual, a base de dados do Azure Cosmos suporta associações internas. Capacidades de associação adicionais são lançamento.

As associações internas resultam num produto cruzado completado dos conjuntos de participar na União. O resultado de uma associação de forma de N é um conjunto de cadeias de identificação de elemento N, onde cada valor na cadeia de identificação está associado o alias definir participar na União e pode ser acedido por referenciar esse alias nas outras cláusulas.  
  
A avaliação da associação a um depende do âmbito do contexto dos conjuntos de participantes:  
  
-  Uma associação entre um conjunto de coleções e âmbito de coleção de conjunto B, os resultados num produto cruzado de todos os elementos em conjuntos de A e B.
  
-   Uma associação entre o conjunto A e conjunto no âmbito do documento B, resulta numa união de todos os conjuntos de obtido através da avaliação no âmbito do documento conjunto B para cada documento de definir A.  
  
 Na versão atual, é suportado um máximo de uma expressão de coleção de âmbito, o processador de consultas.  
  
**Exemplos de associações:**  
  
Vamos observar a cláusula FROM seguintes:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Permitir que cada origem definir `input_alias1, input_alias2, …, input_aliasN`. Esta cláusula FROM devolve um conjunto de cadeias de identificação de N (cadeia de identificação com valores N). Cada cadeia de identificação tem valores produzidos pelo iterating todos os aliases de coleção ao longo do respetivos respetivos conjuntos.  
  
*Exemplo 1, com 2 origens de associação:*  
  
- Permitir que `<from_source1>` ser coleção no âmbito e representam conjunto {A, B, C}.  
  
- Permitir que `<from_source2>` ser confinada documento referenciar input_alias1 e conjuntos de representar:  
  
    {1, 2} para`input_alias1 = A,`  
  
    {3} para`input_alias1 = B,`  
  
    {4, 5} para`input_alias1 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2>` resultará nas cadeias de identificação seguintes:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*Exemplo 2, com 3 origens de associação:*  
  
- Permitir que `<from_source1>` ser coleção no âmbito e representam conjunto {A, B, C}.  
  
- Permitir que `<from_source2>` estar no âmbito do documento referenciar `input_alias1` e conjuntos de representar:  
  
    {1, 2} para`input_alias1 = A,`  
  
    {3} para`input_alias1 = B,`  
  
    {4, 5} para`input_alias1 = C,`  
  
- Permitir que `<from_source3>` estar no âmbito do documento referenciar `input_alias2` e conjuntos de representar:  
  
    {100, 200} para`input_alias2 = 1,`  
  
    {300} para`input_alias2 = 3,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas cadeias de identificação seguintes:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
> [!NOTE]
> Falta de cadeias de identificação para outros valores de `input_alias1`, `input_alias2`, para o qual o `<from_source3>` não devolveu quaisquer valores.  
  
*ASSOCIE exemplo 3, com 3 origens:*  
  
- Permita < from_source1 > ser confinada de coleção e representam conjunto {A, B, C}.  
  
- Permitir que `<from_source1>` ser coleção no âmbito e representam conjunto {A, B, C}.  
  
- Permitem < from_source2 > ser input_alias1 de referência no âmbito do documento e conjuntos de representar:  
  
    {1, 2} para`input_alias1 = A,`  
  
    {3} para`input_alias1 = B,`  
  
    {4, 5} para`input_alias1 = C,`  
  
- Permitir que `<from_source3>` incluído `input_alias1` e conjuntos de representar:  
  
    {100, 200} para`input_alias2 = A,`  
  
    {300} para`input_alias2 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas cadeias de identificação seguintes:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
> [!NOTE]
> Isto resultou num produto cruzado entre `<from_source2>` e `<from_source3>` porque ambos estão confinadas ao mesmo `<from_source1>`.  Isto resultou numa 4 (2 x 2) cadeias de identificação que tenham o valor A, cadeias de identificação 0 ter o valor B (1, 0) e (2 x 1) de 2 cadeias de identificação ter valor C.  
  
**Consulte também**  
  
 [Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a>Cláusula WHERE  
 Especifica a condição de pesquisa para os documentos devolvidos pela consulta.  
  
 **Sintaxe**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumentos**  
  
-   `<filter_condition>`  
  
     Especifica a condição para ser cumpridas para que os documentos a ser devolvido.  
  
-   `<scalar_expression>`  
  
     Expressão que representa o valor a ser calculado. Consulte o [expressões escalares](#bk_scalar_expressions) secção para obter detalhes.  
  
 **Observações**  
  
 Para que o documento a ser devolvida uma expressão especificada como filtro de condição tem de avaliar como verdadeiro. Apenas true valor booleano satisfaçam a condição, qualquer outro valor: indefinido, null, false, número, matriz nem um objeto não satisfaçam a condição.  
  
##  <a name="bk_orderby_clause"></a>Cláusula ORDER BY  
 Especifica a sequência de ordenação para resultados devolvidos pela consulta.  
  
 **Sintaxe**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumentos**  
  
-   `<sort_specification>`  
  
     Especifica uma propriedade ou expressão em que pretende ordenar o conjunto de resultados de consulta. Uma coluna de ordenação pode ser especificada como um alias de nome ou coluna.  
  
     Podem ser especificadas várias colunas de ordenação. Os nomes de coluna tem de ser exclusivos. A sequência das ordenar colunas na cláusula ORDER BY define a organização do conjunto de resultados ordenados. Ou seja, o conjunto de resultados é ordenado pela propriedade primeiro e, em seguida, essa lista ordenada está ordenada por propriedade segundo e assim sucessivamente.  
  
     Os nomes de coluna referenciados na cláusula ORDER BY têm de corresponder a uma coluna na lista de seleção ou uma coluna definida numa tabela especificada na cláusula FROM sem qualquer ambiguities.  
  
-   `<sort_expression>`  
  
     Especifica uma propriedade de único ou uma expressão em que pretende ordenar o conjunto de resultados de consulta.  
  
-   `<scalar_expression>`  
  
     Consulte o [expressões escalares](#bk_scalar_expressions) secção para obter detalhes.  
  
-   `ASC | DESC`  
  
     Especifica que os valores na coluna especificada devem ser ordenados na ordem ascendente ou descendente. Ordena ASC a partir do valor mais baixo para o valor mais alto. Ordena DESC do valor mais alto para o valor mais baixo. ASC é a sequência de ordenação de predefinição. Valores nulos são tratados como os valores possíveis mais baixos.  
  
 **Observações**  
  
 Enquanto a gramática de consulta suporta vários ordem pelas propriedades, o tempo de execução de consulta de base de dados do Azure Cosmos suporta a ordenação apenas em relação a uma única propriedade e apenas com os nomes de propriedades, ou seja, não contra propriedades calculadas. A ordenação também requer que a política de indexação inclui um índice de intervalo para a propriedade e o tipo especificado, com a precisão máxima. Consulte a documentação de política de indexação para obter mais detalhes.  
  
##  <a name="bk_scalar_expressions"></a>Expressões escalares  
 Uma expressão escalar é uma combinação de operadores que podem ser avaliados como para obter um valor único e símbolos. Expressões simples podem ser constantes, referências de propriedade, referências de elemento de matriz, referências de alias ou chamadas de função. As expressões simples podem ser combinadas nas expressões complexas utilizando operadores.  
  
 Para obter detalhes sobre qual expressão escalar poderá ter de valores, consulte [constantes](#bk_constants) secção.  
  
 **Sintaxe**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argumentos**  
  
-   `<constant>`  
  
     Representa um valor constante. Consulte [constantes](#bk_constants) secção para obter detalhes.  
  
-   `input_alias`  
  
     Representa um valor definido pelo `input_alias` introduzida no `FROM` cláusula.  
    Este valor é garantido a não ser **indefinido** –**indefinido** são ignorados valores na entrada.  
  
-   `<scalar_expression>.property_name`  
  
     Representa um valor da propriedade de um objeto. Se a propriedade não existe ou propriedade é referenciada num valor que não é um objeto, em seguida, avalia a expressão para **indefinido** valor.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Representa um valor de propriedade com o nome `property_name` ou elemento com índice de matriz `array_index` de uma objeto/matriz. Se o índice de matriz/propriedade não existe ou o índice de matriz/propriedade é referenciado num valor que não é uma objeto/matriz, em seguida, a expressão é avaliada como valor indefinido.  
  
-   `unary_operator <scalar_expression>`  
  
     Representa um operador que é aplicado a um valor único. Consulte [operadores](#bk_operators) secção para obter detalhes.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Representa um operador que é aplicado a dois valores. Consulte [operadores](#bk_operators) secção para obter detalhes.  
  
-   `<scalar_function_expression>`  
  
     Representa um valor definido por um resultado de uma chamada de função.  
  
-   `udf_scalar_function`  
  
     Escalar função definida pelo nome do utilizador.  
  
-   `builtin_scalar_function`  
  
     Nome da função incorporada escalar.  
  
-   `<create_object_expression>`  
  
     Representa um valor obtido através da criação de um novo objeto com propriedades especificadas e os respetivos valores.  
  
-   `<create_array_expression>`  
  
     Representa um valor obtido através da criação de uma matriz de novo com valores especificados como elementos  
  
-   `parameter_name`  
  
     Representa um valor de nome de parâmetro especificado. Os nomes dos parâmetros têm de ter um único @ como o primeiro caráter.  
  
 **Observações**  
  
 Quando chamar um utilizador ou incorporados definida uma função escalar todos os argumentos tem de ser definidos. Se qualquer um dos argumentos for definido, a função não será chamada e o resultado será indefinido.  
  
 Ao criar um objeto, qualquer propriedade que é atribuída o valor indefinido será ignorada e não incluída no objeto criado.  
  
 Quando a criação de uma matriz, qualquer valor de elemento que é atribuído **indefinido** valor será ignorado e não incluído no objeto criado. Isto fará com que o seguinte elemento definido tirar o seu lugar de forma a que a matriz criada não irá ter ignorada índices.  
  
##  <a name="bk_operators"></a>Operadores  
 Esta secção descreve os operadores suportados. Cada operador pode ser atribuído a exatamente uma categoria.  
  
 Consulte **categorias de operador** tabela abaixo, para obter detalhes sobre o processamento de **indefinido** valores, requisitos de tipo para os valores de entrada e de processamento de valores com os tipos não correspondentes.  
  
 **Categorias de operador:**  
  
|**Categoria**|**Detalhes**|  
|-|-|  
|**aritmética**|Operador espera input(s) ser Number(s). Saída também é um número. Se qualquer uma das entradas é **indefinido** ou um tipo diferente, em seguida, o resultado de número **indefinido**.|  
|**bit a bit**|Operador espera input(s) ser número inteiro com sinal de 32 bits Number(s). O resultado é também o número inteiro com sinal de 32 bits número.<br /><br /> Qualquer valor de não-inteiros será arredondado. Um valor positivo será arredondado para baixo, negativa valores arredondar por excesso.<br /><br /> Qualquer valor que está fora do intervalo de número inteiro de 32 bits será convertido, efetuando os últimos 32 bits da respetivo dois notação complemento.<br /><br /> Se qualquer uma das entradas é **indefinido** ou escreva outro número, em seguida, o resultado é **indefinido**.<br /><br /> **Nota:** o comportamento acima é compatível com o comportamento de operador bit a bit JavaScript.|  
|**lógica**|Operador espera input(s) ser Boolean(s). Saída também é um valor booleano.<br />Se qualquer uma das entradas é **indefinido** ou escreva exceto Boolean, em seguida, o resultado será **indefinido**.|  
|**comparação**|Operador espera input(s) para ter o mesmo tipo e não pode ser definido. O resultado é um valor booleano.<br /><br /> Se qualquer uma das entradas é **indefinido** ou entradas têm tipos diferentes, em seguida, o resultado é **indefinido**.<br /><br /> Consulte **ordenação dos valores de comparação** tabela para o valor de ordenação detalhes.|  
|**cadeia**|Operador espera input(s) ser cadeia. Saída também é uma cadeia.<br />Se qualquer uma das entradas é **indefinido** ou escreva à cadeia, em seguida, o resultado é **indefinido**.|  
  
 **Operadores unários:**  
  
|**Nome**|**Operador**|**Detalhes**|  
|-|-|-|  
|**aritmética**|+<br /><br /> -|Devolve o valor numérico.<br /><br /> Negação bit a bit. Devolve negated valor numérico.|  
|**bit a bit**|~|Daqueles complemento. Devolve um conjunto de um valor numérico.|  
|**Lógica**|**NÃO**|Negação. Devolve negated valor booleano.|  
  
 **Operadores binários:**  
  
|**Nome**|**Operador**|**Detalhes**|  
|-|-|-|  
|**aritmética**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Adição.<br /><br /> Subtração.<br /><br /> Multiplicação.<br /><br /> Divisão.<br /><br /> Reforçada.|  
|**bit a bit**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Bit a bit OR.<br /><br /> Bit a bit AND.<br /><br /> Operação XOR.<br /><br /> Shift esquerdo.<br /><br /> Shift à direita.<br /><br /> Shift à direita do preenchimento de zero.|  
|**lógica**|**E**<br /><br /> **OU**|Conjunção lógica. Devolve **verdadeiro** se ambos os argumentos forem **verdadeiro**, devolve **falso** caso contrário.<br /><br /> Conjunção lógica. Devolve **verdadeiro** se ambos os argumentos forem **verdadeiro**, devolve **falso** caso contrário.|  
|**comparação**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Igual a. Devolve **verdadeiro** se argumentos são iguais, devolve **falso** caso contrário.<br /><br /> Não é igual a. Devolve **verdadeiro** se argumentos não são iguais, devolve **falso** caso contrário.<br /><br /> Maior. Devolve **verdadeiro** se o primeiro argumento for maior do que o segundo, devolver **falso** caso contrário.<br /><br /> Maior ou igual a. Devolve **verdadeiro** se o primeiro argumento é maior que ou igual a segunda, devolver **falso** caso contrário.<br /><br /> Menor. Devolve **verdadeiro** se o primeiro argumento é menor que o um segundo, retorno **falso** caso contrário.<br /><br /> Menor ou igual a. Devolve **verdadeiro** se o primeiro argumento é menor ou igual a segunda, devolver **falso** caso contrário.<br /><br /> Unir. Devolve o segundo argumento, se o primeiro argumento é uma **indefinido** valor.|  
|**Cadeia**|**&#124;&#124;**|Concatenação. Devolve uma concatenação de ambos os argumentos.|  
  
 **Operadores ternary:**  
  
|Operador ternary|?|Devolve o segundo argumento, se o primeiro argumento for avaliada como **verdadeiro**; caso contrário a devolver o argumento da terceira.|  
|-|-|-|  
  
 **Ordenação de valores de comparação**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Indefinido**|Não é comparável.|  
|**Valor nulo**|Único valor: **nulo**|  
|**Número**|Número de real natural.<br /><br /> Valor de infinito negativo é menor do que qualquer outro valor de número.<br /><br /> Valor de infinito positivo é maior do que qualquer outro valor de número. **NaN** valor não é comparável. Comparar com **NaN** resultará na **indefinido** valor.|  
|**Cadeia**|Lexicographical ordem.|  
|**Matriz**|Não existem ordenação, mas equitable.|  
|**Objeto**|Não existem ordenação, mas equitable.|  
  
 **Observações**  
  
 Na base de dados do Azure Cosmos, os tipos de valores, muitas vezes, não sejam conhecidos até que, na verdade, são obtidos a partir da base de dados. Para suportar eficiente execução de consultas, na maioria dos operadores têm requisitos de tipo restrito. Também operadores por si mesmos não efetuar conversões implícitas.  
  
 Isto significa que, como uma consulta: SELECIONAR * da raiz r onde r.Age = 21 só irá devolver documentos com a propriedade idade igual ao número 21. Documentos com a propriedade idade igual para a cadeia "21" ou a cadeia "0021" não irão corresponder à, como a expressão "21" = 21 avalia a indefinido. Isto permite uma melhor utilização dos índices, porque a pesquisa de um valor específico (ou seja, número de 21) é mais rápida do que procurar um número indefinido de potenciais correspondências (ou seja, o número 21 ou cadeias "21", "021", "21.0"...). Isto é diferente do modo como o JavaScript avalia operadores nos valores de diferentes tipos.  
  
 **Comparação de igualdade de objetos e as matrizes e**  
  
 Comparação dos valores de matriz nem um objeto utilizando operadores de intervalo (>, > =, <, < =) irá resultar no não definida como há não definida no objeto ou a matriz de valores de ordem. No entanto, utilizar os operadores de igualdade/inequality (=,! = <>) é suportada e valores serão comparados estruturalmente.  
  
 As matrizes são iguais se ambas as matrizes têm o mesmo número de elementos e os elementos das posições de correspondência também são iguais. Se comparar qualquer par de resultados de elementos no não definida, o resultado de comparação de matriz não está definido.  
  
 Os objetos são iguais se ambos os objetos têm propriedades mesmas definidas e valores de propriedades de correspondência também são iguais. Se comparar qualquer par de resultados de valores de propriedade no não definida, o resultado de comparação de objeto não está definido.  
  
##  <a name="bk_constants"></a>Constantes  
 Uma constante, também conhecido como um literal ou um valor escalar, é um símbolo que representa um valor de dados específicos. O formato de uma constante depende do valor representa o tipo de dados.  
  
 **Tipos de dados escalar suportados:**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Indefinido**|Único valor: **indefinido**|  
|**Valor nulo**|Único valor: **nulo**|  
|**Valor booleano**|Valores: **falso**, **verdadeiro**.|  
|**Número**|Um dupla precisão número de vírgula flutuante, IEEE 754 padrão.|  
|**Cadeia**|Uma sequência de zero ou mais carateres Unicode. Cadeias de têm de estar entre aspas único ou duplo.|  
|**Matriz**|Uma sequência de zero ou mais elementos. Cada elemento pode ser um valor de qualquer tipo de dados escalar, exceto Undefined.|  
|**Objeto**|Um conjunto não ordenado de zero ou mais pares nome/valor. O nome é uma cadeia Unicode, o valor pode ser de qualquer tipo de dados escalar, exceto **Undefined**.|  
  
 **Sintaxe**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argumentos**  
  
1.  `<undefined_constant>; undefined`  
  
     Valor de representa não definida do tipo Undefined.  
  
2.  `<null_constant>; null`  
  
     Representa **nulo** valor do tipo **nulo**.  
  
3.  `<boolean_constant>`  
  
     Representa a constante de tipo Booleano.  
  
4.  `false`  
  
     Representa **falso** valor do tipo Booleano.  
  
5.  `true`  
  
     Representa **verdadeiro** valor do tipo Booleano.  
  
6.  `<number_constant>`  
  
     Representa uma constante.  
  
7.  `decimal_literal`  
  
     As literais decimais são os números de representado utilizando notação decimal ou notação científica.  
  
8.  `hexadecimal_literal`  
  
     As literais hexadecimal são os números de representado utilizando o prefixo '0x' seguido de um ou mais dígitos hexadecimais.  
  
9. `<string_constant>`  
  
     Representa uma constante de tipo cadeia.  
  
10. `string _literal`  
  
     As literais de cadeia são cadeias Unicode representadas por uma sequência de zero ou mais carateres Unicode ou sequências de escape. As literais de cadeia estão incluídas no plicas (apóstrofo: ') ou de aspas (aspas: ").  
  
 São permitidas seguintes sequências de escape:  
  
|**Sequência de escape**|**Descrição**|**Carácter Unicode**|  
|-|-|-|  
|\\'|apóstrofo (')|U + 0027|  
|\\"|aspas (")|U + 0022|  
|\\\|solidus inversa (\\)|U + 005C|  
|\\/|solidus (/)|U + 002F|  
|\b|RETROCESSO|U + 0008|  
|\f|feed do formulário|U + 000C|  
|\n|linha|U + 000A|  
|\r|avanço retorno|U + 000D|  
|\t|Separador|U + 0009|  
|\uXXXX|Um carácter Unicode definido pelo 4 dígitos hexadecimais.|U + XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a>Diretrizes de desempenho de consulta  
 Ordem de uma consulta ser executada de forma eficiente para uma coleção de grandes dimensões, deverá utilizar os filtros que podem ser servidos através de um ou mais índices.  
  
 Os seguintes filtros serão considerados para a pesquisa do índice:  
  
-   Utilize o operador de igualdade (=) com uma expressão de caminho do documento e uma constante.  
  
-   Utilizar os operadores de intervalo (<, \<=, >, > =) com uma expressão de caminho do documento e constantes de números.  
  
-   Expressão de caminho do documento representa uma expressão que identifica um caminho constante nos documentos da coleção da base de dados referenciada.  
  
 **Expressão de caminho do documento**  
  
 Expressões de caminho do documento são expressões que um caminho de propriedade ou matriz assessors indexador através de um documento proveniente de documentos de coleção da base de dados. Este caminho pode ser utilizado para identificar a localização dos valores referenciada num filtro diretamente nos documentos na coleção da base de dados.  
  
 Para uma expressão a ser considerada uma expressão de caminho do documento, deve:  
  
1.  A raiz de coleção de referenciar diretamente.  
  
2.  Indexador de matriz de propriedade ou constante referência de algumas expressão de caminho do documento  
  
3.  Um alias, que representa alguns expressão de caminho do documento de referência.  
  
     **Convenções de sintaxe**  
  
     A tabela seguinte descreve as convenções de utilizados para descrever a sintaxe de referência de linguagem de consulta do DocumentDB API.  
  
    |**Convenção**|**Utilizado para**|  
    |-|-|    
    |EM MAIÚSCULAS|Palavras-chave sensível.|  
    |minúsculas|Palavras-chave de maiúsculas e minúsculas.|  
    |\<nonterminal >|Nonterminal, definidas separadamente.|  
    |\<nonterminal >:: =|Definição de sintaxe do nonterminal.|  
    |other_terminal|Terminal (token), descrito detalhadamente nas palavras.|  
    |Identificador|Identificador. Permite que os seguintes carateres: caráter de _First. a-z A-Z 0-9 não pode ser um dígito.|  
    |"cadeia"|Cadeia delimitada por aspas. Permite que qualquer cadeia de caracteres válida. Ver descrição de um string_literal.|  
    |'símbolo'|Símbolo literal que faz parte da sintaxe.|  
    |&#124; (barra vertical)|Alternativas para itens de sintaxe. Pode utilizar apenas um dos itens especificados.|  
    |[] /(brackets)|Retos coloque um ou mais itens opcionais.|  
    |[ ,...n ]|Indica que o item anterior pode ser n repetida diversas vezes. As ocorrências são separadas por vírgulas.|  
    |[ ...n ]|Indica que o item anterior pode ser n repetida diversas vezes. As ocorrências são separadas por espaços em branco.|  
  
##  <a name="bk_built_in_functions"></a>Funções incorporadas  
 BD do Cosmos do Azure fornece várias funções incorporadas do SQL Server. As categorias de funções incorporadas são listadas abaixo.  
  
|Função|Descrição|  
|--------------|-----------------|  
|[Funções matemática](#bk_mathematical_functions)|As funções matemática efetuar um cálculo, normalmente, com base nos valores de entrada que são fornecidos como argumentos e devolvem um valor numérico.|  
|[A verificar as funções de tipo](#bk_type_checking_functions)|As funções de verificação do tipo permitem-lhe verificar o tipo de uma expressão dentro de consultas SQL.|  
|[Funções de cadeia](#bk_string_functions)|As funções de cadeia efetuar uma operação num valor de cadeia de entrada e devolvem uma cadeia, o valor numérico ou booleano.|  
|[Funções de matriz](#bk_array_functions)|As funções de matriz efetuar uma operação num valor de entrada da matriz e devolver um valor numérico, o valor de cadeia Boolean ou matriz.|  
|[Funções geográficos](#bk_spatial_functions)|As funções geográficos efetuar uma operação num valor de entrada do objeto espacial e devolvem um valor numérico ou booleano.|  
  
###  <a name="bk_mathematical_functions"></a>Funções matemática  
 As seguintes funções cada efetuar um cálculo, normalmente, com base nos valores de entrada que são fornecidos como argumentos e devolvem um valor numérico.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[LIMITE](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[GRAUS](#bk_degrees)|  
|[EXP](#bk_exp)|[PISO](#bk_floor)|[REGISTO](#bk_log)|  
|[LOG10](#bk_log10)|[INSTALADOR DE PLATAFORMA](#bk_pi)|[ENERGIA](#bk_power)|  
|[RADIANOS](#bk_radians)|[ARREDONDAR](#bk_round)|[ÚNICO](#bk_sin)|  
|[SQRT](#bk_sqrt)|[PARÊNTESES](#bk_square)|[INÍCIO DE SESSÃO](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a>ABS  
 Devolve o valor absoluto (positivo) da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra os resultados da utilizando a função ABS em três números diferentes.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a>ACOS  
 Devolve o ângulo em radianos, cujo co-seno é a expressão numérica especificada; Também denominado o arco de co-seno.  
  
 **Sintaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve ACOS de -1.  
  
```  
SELECT ACOS(-1)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a>ASIN  
 Devolve o ângulo em radianos, cujo seno é a expressão numérica especificada. Isto também é denominado o arco de seno.  
  
 **Sintaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve ASIN de -1.  
  
```  
SELECT ASIN(-1)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a>ATAN  
 Devolve o ângulo em radianos, cuja tangente é a expressão numérica especificada. Isto também é denominado o arco de tangente.  
  
 **Sintaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve ATAN do valor especificado.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a>ATN2  
 Devolve o valor principal do tangente arco de y / x, expressado em radianos.  
  
 **Sintaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula ATN2 para especificado x e y componentes.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a>LIMITE  
 Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra numérico positivo, negativo e zero valores com a função de limite.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a>COS  
 Devolve o co-seno do ângulo especificado, trigonometric em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula COS do ângulo especificado.  
  
```  
SELECT COS(14.78)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a>COT  
 Devolve a co-tangente do ângulo especificado, trigonometric em radianos, a expressão numérica especificado.  
  
 **Sintaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula COT do ângulo especificado.  
  
```  
SELECT COT(124.1332)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a>GRAUS  
 Devolve o ângulo correspondente em graus para um ângulo especificado em radianos.  
  
 **Sintaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o número de graus um ângulo de radianos PI/2.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a>PISO  
 Devolve o maior número inteiro menor ou igual a expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra numérico positivo, negativo e zero valores com a função de piso.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a>EXP  
 Devolve o valor da expressão especificada numérico exponencial.  
  
 **Sintaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Observações**  
  
 A constante **i** (2.718281...), seja a base da natural logarithms.  
  
 O expoente de um número é a constante **i** elevado à potência do número. Por exemplo EXP(1.0) = i ^ 1.0 = 2.71828182845905 e EXP(10) = i ^ 10 = 22026.4657948067.  
  
 Exponencial do logaritmo natural de um número, é o número próprio: EXP (registo (n)) = n. E o logaritmo natural de um número exponencial é o número próprio: registo (EXP (n)) = n.  
  
 **Exemplos**  
  
 O exemplo seguinte declara uma variável e devolve o valor exponencial de variável especificada (10).  
  
```  
SELECT EXP(10)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 O exemplo seguinte devolve o valor exponencial de natural logarithm de 20 e o logaritmo natural de exponencial de 20. Uma vez que estas funções são funções de inverso um do outro, o valor de retorno com arredondamento para contas de ponto flutuante em ambos os casos é 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a>REGISTO  
 Devolve o logaritmo natural da expressão numérica especificado.  
  
 **Sintaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
-   `base`  
  
     Argumento numérico opcional que define a base do logaritmo.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Observações**  
  
 Por predefinição, LOG() devolve o logaritmo natural. Pode alterar a base do logaritmo para outro valor utilizando o parâmetro opcional de base.  
  
 O logaritmo natural é o logaritmo para a base **i**, onde **i** é uma constante irrational aproximadamente igual ao 2.718281828.  
  
 O logaritmo natural de um número exponencial é o número próprio: registo (EXP (n)) = n. E exponencial do logaritmo natural de um número, é o número próprio: EXP (registo (n)) = n.  
  
 **Exemplos**  
  
 O exemplo seguinte declara uma variável e devolve o logaritmo de valor da variável especificada (10).  
  
```  
SELECT LOG(10)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 O exemplo seguinte calcula o registo para o expoente de um número.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a>LOG10  
 Devolve o logaritmo base 10 da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Observações**  
  
 As funções LOG10 e energia inversely relacionados com um do outro. Por exemplo, 10 ^ LOG10(n) = n.  
  
 **Exemplos**  
  
 O exemplo seguinte declara uma variável e devolve o valor de LOG10 da variável especificada (100).  
  
```  
SELECT LOG10(100)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a>INSTALADOR DE PLATAFORMA  
 Devolve o valor de PI constante.  
  
 **Sintaxe**  
  
```  
PI ()  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o valor de PI.  
  
```  
SELECT PI()  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a>ENERGIA  
 Devolve o valor da expressão especificada para a potência especificada.  
  
 **Sintaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
-   `y`  
  
     É a capacidade para o qual pretende aumentar `numeric_expression`.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte demonstra que gera um número à potência de 3 (cubo do número).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a>RADIANOS  
 Devolve radianos quando uma expressão numérica, em graus, é introduzida.  
  
 **Sintaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte utiliza os ângulos de alguns como entrada e devolve os valores de radian correspondentes.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a>ARREDONDAR  
 Devolve um valor numérico, arredondado para o valor de número inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte arredonda os números positivos e negativos seguintes para o número inteiro mais próximo.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a>INÍCIO DE SESSÃO  
 Devolve o positivo (+ 1), zero (0) ou negativo sessão (-1) da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve os valores de início de sessão de números entre -2 e 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a>ÚNICO  
 Devolve o seno do ângulo especificado, trigonometric em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula o único do ângulo especificado.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a>SQRT  
 Devolve a raiz quadrada do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve as raízes de quadrada dos números 1 a 3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a>PARÊNTESES  
 Devolve o quadrado do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve quadrados dos números 1 a 3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a>TAN  
 Devolve a tangente do ângulo especificado, em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula a tangente do (de) PI / 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a>TRUNC  
 Devolve um valor numérico, truncada de acordo com o valor de número inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte trunca os seguintes números positivos e negativos para o valor de número inteiro mais próximo.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a>A verificar as funções de tipo  
 As seguintes funções suportam o tipo de verificação contra os valores de entrada e cada devolver um valor booleano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a>IS_ARRAY  
 Devolve um valor booleano que indica se o tipo da expressão especificada é uma matriz.  
  
 **Sintaxe**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Se qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica objetos de JSON booleano número, cadeia, null, objeto, matriz e tipos indefinidos utilizando a função IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a>IS_BOOL  
 Devolve um valor booleano que indica se o tipo de expressão especificado é um valor booleano.  
  
 **Sintaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Se qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica objetos de JSON booleano número, cadeia, null, objeto, matriz e tipos indefinidos utilizando a função IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a>IS_DEFINED  
 Devolve um valor boleano que indica se a propriedade foi atribuída um valor.  
  
 **Sintaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Se qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica a presença de uma propriedade no documento JSON especificado. O primeiro devolve true, uma vez que "a" está presente, mas o segundo devolve false, uma vez que "b" está ausente.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a>IS_NULL  
 Devolve um valor booleano que indica se o tipo da expressão especificada é nulo.  
  
 **Sintaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Se qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica objetos de JSON booleano número, cadeia, null, objeto, matriz e tipos indefinidos utilizando a função IS_NULL.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a>IS_NUMBER  
 Devolve um valor booleano que indica se o tipo de expressão especificado é um número.  
  
 **Sintaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Se qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica objetos de JSON booleano número, cadeia, null, objeto, matriz e tipos indefinidos utilizando a função IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a>IS_OBJECT  
 Devolve um valor booleano que indica se o tipo de expressão especificado é um objeto JSON.  
  
 **Sintaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Se qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica objetos de JSON booleano número, cadeia, null, objeto, matriz e tipos indefinidos utilizando a função IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a>IS_PRIMITIVE  
 Devolve um valor booleano que indica se o tipo da expressão especificada é uma primitiva (string, Boolean, numérico ou null).  
  
 **Sintaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Se qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica objetos de JSON booleano número, cadeia, null, objeto, matriz e tipos indefinidos utilizando a função IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a>IS_STRING  
 Devolve um valor booleano que indica se o tipo da expressão especificada é uma cadeia.  
  
 **Sintaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     Se qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica objetos de JSON booleano número, cadeia, null, objeto, matriz e tipos indefinidos utilizando a função IS_STRING.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a>Funções de cadeia  
 As seguintes funções escalares efetuar uma operação num valor de cadeia de entrada e devolvem uma cadeia, o valor numérico ou booleano.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTÉM](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[À ESQUERDA](#bk_left)|[COMPRIMENTO](#bk_length)|  
|[INFERIOR](#bk_lower)|[LTRIM](#bk_ltrim)|[SUBSTITUIR](#bk_replace)|  
|[REPLICAR](#bk_replicate)|[INVERSA](#bk_reverse)|[À DIREITA](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBCADEIA](#bk_substring)|  
|[SUPERIOR](#bk_upper)|||  
  
####  <a name="bk_concat"></a>CONCAT  
 Devolve uma cadeia que é o resultado da concatenar duas ou mais valores de cadeia.  
  
 **Sintaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve a cadeia concatenada dos valores especificados.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a>CONTÉM  
 Devolve um booleano que indica se a primeira cadeia de expressão contém o segundo.  
  
 **Sintaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica se o "abc" contém "ab" e contém "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a>ENDSWITH  
 Devolve um booleano que indica se a primeira expressão de cadeia termina com o segundo.  
  
 **Sintaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve "abc" termina com "b" e "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a>INDEX_OF  
 Devolve a posição inicial da primeira ocorrência da segunda cadeia de expressão na primeira expressão de cadeia especificada ou -1 se a cadeia não foi encontrada.  
  
 **Sintaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o índice de subcadeias vários dentro "abc".  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a>À ESQUERDA  
 Devolve a parte esquerda de uma cadeia com o número especificado de carateres.  
  
 **Sintaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
-   `num_expr`  
  
     Se qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve a parte esquerda do "abc" para vários valores de comprimento.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a>COMPRIMENTO  
 Devolve o número de carateres da expressão de cadeia especificada.  
  
 **Sintaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o comprimento de uma cadeia.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a>INFERIOR  
 Devolve uma expressão de cadeia após a conversão de dados do caráter em maiúsculas em minúsculas.  
  
 **Sintaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar inferior numa consulta.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a>LTRIM  
 Devolve uma expressão de cadeia após remove espaços em branco à esquerda.  
  
 **Sintaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar LTRIM dentro de uma consulta.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a>SUBSTITUIR  
 Substitui todas as ocorrências de um valor de cadeia especificada com outro valor de cadeia.  
  
 **Sintaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar a substituição de uma consulta.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a>REPLICAR  
 Repete-se um valor de cadeia um número de vezes especificado.  
  
 **Sintaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
-   `num_expr`  
  
     Se qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar REPLICAR numa consulta.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a>INVERSA  
 Devolve a ordem inversa de um valor de cadeia.  
  
 **Sintaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar inversa numa consulta.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a>À DIREITA  
 Devolve a parte direita de uma cadeia com o número especificado de carateres.  
  
 **Sintaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
-   `num_expr`  
  
     Se qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve a parte direita de "abc" para vários valores de comprimento.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a>RTRIM  
 Devolve uma expressão de cadeia após remove espaços em branco à direita.  
  
 **Sintaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar RTRIM dentro de uma consulta.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a>STARTSWITH  
 Devolve um booleano que indica se a primeira expressão de cadeia começa com o segundo.  
  
 **Sintaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica se a cadeia "abc" começa com "b" e "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a>SUBCADEIA  
 Devolve a parte de uma expressão de cadeia a partir da posição carácter especificado baseado em zero e continua ao comprimento especificado ou para o fim da cadeia.  
  
 **Sintaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
-   `num_expr`  
  
     Se qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve a subcadeia de "abc" começando em 1 e durante um período de 1 caráter.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a>SUPERIOR  
 Devolve uma expressão de cadeia após a conversão de dados de carateres em minúsculas em maiúsculas.  
  
 **Sintaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É uma expressão de cadeia válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar o limite superior de uma consulta  
  
```  
SELECT UPPER("Abc")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a>Funções de matriz  
 As seguintes funções escalares efetuar uma operação num valor de entrada da matriz e devolver um valor numérico, valor de cadeia Boolean ou matriz  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a>ARRAY_CONCAT  
 Devolve uma matriz que é o resultado da concatenar duas ou mais valores de matriz.  
  
 **Sintaxe**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É uma expressão de matriz válido.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de matriz.  
  
 **Exemplos**  
  
 O exemplo seguinte como concatenar duas matrizes.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a>ARRAY_CONTAINS  
Devolve um valor boleano que indica se a matriz contém o valor especificado. Pode especificar se a correspondência total ou parcial. 

 **Sintaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É uma expressão de matriz válido.  
  
-   `expr`  
  
     Se qualquer expressão válida.  

-   `bool_expr`  
  
     É uma expressão booleana.       
  
 **Tipos de retorno**  
  
 Devolve um valor booleano.  
  
 **Exemplos**  
  
 O exemplo seguinte como verificar a existência de associação a uma matriz ARRAY_CONTAINS a utilizar.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": true, "$2": false}]  
```  

 O exemplo seguinte como procurar uma correspondência parcial de uma JSON numa matriz ARRAY_CONTAINS a utilizar.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 Eis o conjunto de resultados.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a>ARRAY_LENGTH  
 Devolve o número de elementos da expressão de matriz especificada.  
  
 **Sintaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É uma expressão de matriz válido.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte como obter o comprimento de uma matriz ARRAY_LENGTH a utilizar.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a>ARRAY_SLICE  
 Devolve a parte de uma expressão de matriz.
  
 **Sintaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É uma expressão de matriz válido.  
  
-   `num_expr`  
  
     Se qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Devolve um valor booleano.  
  
 **Exemplos**  
  
 O exemplo seguinte como obter uma parte de uma matriz ARRAY_SLICE a utilizar.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a>Funções geográficos  
 As seguintes funções escalares efetuar uma operação num valor de entrada do objeto espacial e devolvem um valor numérico ou booleano.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a>ST_DISTANCE  
 Devolve a distância entre duas expressões de ponto de GeoJSON, polígono ou LineString.  
  
 **Sintaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Se qualquer expressão de objeto GeoJSON ponto, polígono ou LineString válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica, que contém a distância. Este valor é expresso em medidores para o sistema de referência de predefinição.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como devolver todos os documentos famílias que estejam dentro de 30 km da localização especificada utilizando a função incorporada ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a>ST_WITHIN  
 Devolve uma expressão booleana que indica se o objeto GeoJSON (ponto, polígono ou LineString) especificado no primeiro argumento é dentro GeoJSON (ponto, polígono ou LineString) no segundo argumento.  
  
 **Sintaxe**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Se qualquer expressão de objeto GeoJSON ponto, polígono ou LineString válida.  
 
-   `spatial_expr`  
  
     Se qualquer expressão de objeto GeoJSON ponto, polígono ou LineString válida.  
  
 **Tipos de retorno**  
  
 Devolve um valor booleano.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como encontrar todos os documentos famílias dentro de um polígono com ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a>ST_INTERSECTS  
 Devolve uma expressão booleana que indica se o objeto GeoJSON (ponto, polígono ou LineString) especificado no primeiro argumento intersetar GeoJSON (ponto, polígono ou LineString) no segundo argumento.  
  
 **Sintaxe**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Se qualquer expressão de objeto GeoJSON ponto, polígono ou LineString válida.  
 
-   `spatial_expr`  
  
     Se qualquer expressão de objeto GeoJSON ponto, polígono ou LineString válida.  
  
 **Tipos de retorno**  
  
 Devolve um valor booleano.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como encontrar todas as áreas intersetar com polígono especificado.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a>ST_ISVALID  
 Devolve um valor booleano que indica se a expressão de ponto de GeoJSON, polígono ou LineString especificada é válida.  
  
 **Sintaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Se qualquer expressão GeoJSON ponto, polígono ou LineString válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como verificar se é válido utilizar ST_VALID um ponto.  
  
 Por exemplo, este ponto tem um valor de latitude que não se encontra no intervalo válido de valores [-90, 90], por isso, a consulta devolve false.  
  
 Para multilinestrings, a especificação de GeoJSON requer que o último par coordenado fornecido deve ser o mesmo que a primeira, para criar uma forma fechada. Pontos de dentro de um polígono tem de ser especificados por ordem counter-no sentido horário. Um polígono especificado na ordem no sentido horário representa o inverso da região dentro da mesma.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a>ST_ISVALIDDETAILED  
 Devolve um valor JSON que contém um valor boleano valor se a expressão de ponto de GeoJSON, polígono ou LineString especificada é válida e se inválido, além do motivo como um valor de cadeia.  
  
 **Sintaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     Se qualquer expressão de ponto ou polígono GeoJSON válida.  
  
 **Tipos de retorno**  
  
 Devolve um valor JSON que contém um valor boleano valor se a expressão de ponto ou polígono GeoJSON especificada é válida e se inválido, além do motivo como um valor de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte como verificar a validade (com detalhes) utilizando ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Eis o conjunto de resultados.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Passos seguintes  
 [Sintaxe SQL e consulta SQL para a base de dados do Azure Cosmos](documentdb-sql-query.md)   
 [Documentação do Cosmos BD do Azure](https://docs.microsoft.com/en-us/azure/cosmos-db/)  
  
  

---
title: Exemplos de consulta Lucene de Azure Search | Microsoft Docs
description: "Consulta lucene para pesquisa difusa, pesquisa de proximidade, os aumentos termo, pesquisa de expressão regular e pesquisa com carateres universais."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: Lucene query analyzer syntax
ms.assetid: 147f360d-a5ce-4d7b-a909-c8b65bfb748c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: liamca
ms.openlocfilehash: 1faed621039ecd04064cb074e6b9011418e6ec47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Exemplos de sintaxe de consulta Lucene para a criação de consultas na Azure Search
Quando construir consultas para a Azure Search, pode utilizar a predefinição é [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) ou a alternativa [Parser de consulta Lucene na Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). O Parser de consulta Lucene suporta construções de consulta mais complexas, como consultas de âmbito de campo, pesquisa difusa, pesquisa de proximidade, os aumentos termo e pesquisa de expressão regular.

Neste artigo, pode seguir exemplos demonstrar operações de consulta disponíveis quando utiliza a sintaxe completa.

## <a name="viewing-the-examples-in-jsfiddle"></a>Os exemplos de visualização no JSFiddle

Todos os exemplos neste artigo são executável consultas que são executadas em relação a um índice de pesquisa previamente carregado na [JSFiddle](https://jsfiddle.net), um editor de código online para testar o script e HTML. 

Para executá-los, faça duplo clique nos URLs de exemplo de consulta para abrir JSFiddle numa janela do browser separados.

> [!NOTE]
> Os exemplos seguintes tirar partido de um índice de pesquisa constituídas por tarefas disponíveis com base num conjunto de dados fornecido pelo [cidade de Nova Iorque OpenData](https://nycopendata.socrata.com/) iniciativa. Estes dados não devem ser considerados atual ou completos. O índice é um serviço de sandbox fornecida pela Microsoft. Não é necessário uma subscrição do Azure ou da Azure Search para experimentar estas consultas.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise completa de Lucene

Especifique todos os exemplos neste artigo o **queryType = completa** parâmetro, que indica que a sintaxe completa é processada pelo Parser de consulta Lucene de pesquisa. 

**Exemplo 1** -botão direito do rato o seguinte fragmento de consulta para abri-lo numa nova página de browser que carrega JSFiddle e executa a consulta:

* [& queryType = completa & Procurar = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Na nova janela do browser, a origem de JavaScript e HTML saída são apresentados lado. O script referencia uma consulta completa (não apenas o fragmento, conforme mostrado na ligação). A consulta completa é apresentada nos URL para cada exemplo. 

Esta consulta devolve documentos do nosso índice de tarefas de Nova Iorque Cidade (nycjobs, carregada num serviço sandbox). Para uma forma abreviada, a consulta especifica apenas negócio títulos são devolvidos. A consulta subjacente completa é o seguinte:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

O **searchFields** parâmetro restringe a pesquisa para apenas o campo de título do negócio. O **queryType** está definido como **completa**, que instrui a pesquisa do Azure para utilizar o Parser de consulta Lucene para esta consulta.

> [!NOTE]
> Para em segundo plano no processamento da consulta, consulte [como completa a pesquisa em texto funciona na Azure Search](search-lucene-query-architecture.md). Para obter mais informações sobre os parâmetros de pesquisa, consulte [documentos sobre pesquisa (API do REST de serviço de pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Operação de consulta fielded
Pode modificar os exemplos neste artigo, especificando um **fieldname:searchterm** construção para definir uma operação de consulta fielded, em que o campo é uma única palavra, e o termo de pesquisa é também uma única palavra ou frase de acesso, opcionalmente, com o Operadores booleanos. Alguns exemplos incluem o seguinte:

* business_title:(senior NOT junior)
* Estado: ("Nova Iorque" e "Novo Jersey")

Lembre-se de que colocar vários cadeias na aspas se pretender que ambas as cadeias para ser avaliada como uma única entidade, tal como neste caso, a procurar dois cidades diferentes no campo localização. Além disso, certifique-se o operador é capitalized como ver com não e AND.

O campo especificado no **fieldname:searchterm** tem de ser um campo pesquisável. Consulte [Create Index (serviço de API REST Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre a forma como os atributos de índice são utilizados nas definições de campo.

**Exemplo 2** -botão direito do rato o seguinte fragmento de consulta, esta consulta pesquisa de títulos de negócio com o sénior termo no-las, mas não junior:

* [& queryType = completa & Procurar = business_title:senior não junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Exemplo de pesquisa difusa
Uma pesquisa difusa localiza correspondências em termos que tenham uma construção composta por semelhantes. Por [Lucene documentação](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), pesquisa difusa baseiam-se no [Damerau Levenshtein distância](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Para fazer uma pesquisa difusa, acrescente o til "~" símbolo no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, "azul ~" ou "azul ~ 1" devolvam azul, blues e "glue".

**Exemplo 3** -botão direito do rato o seguinte fragmento de consulta. Esta consulta pesquisa para as tarefas com a associar termo (em que este está incorreto):

* [& queryType = completa & Procurar = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Consultas difusa não são [analisados](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), que pode ser surprising se esperar decorrentes ou lemmatization. Análise lexical só é executada em termos de licenciamento concluídos (uma consulta de termo ou a consulta de frase). Tipos de consulta com os termos de incompletos (consulta de prefixo, consulta de caráter universal, consulta regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando a fase de análise. A transformação apenas efetuada em termos de consulta incompleta é lowercasing.
>

## <a name="proximity-search-example"></a>Exemplo de pesquisa de proximidade
Pesquisa de proximidade é utilizada para localizar os termos que estão perto de si num documento. Inserir um til "~" símbolo no final de uma expressão de seguido pelo número de palavras que crie o limite de proximidade. Por exemplo, "airport de átrios" ~ 5 irá encontrar o átrios de termos e airport dentro de 5 palavras de si num documento.

**Exemplo 4** -botão direito do rato a consulta. Procure as tarefas com o termo "Analista Sénior" onde está separado por mais do que uma palavra:

* [& queryType = completa & Procurar = business_title: "Analista Sénior" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemplo 5** – experimente novamente remover as palavras entre o termo "Analista Sénior".

* [& queryType = completa & Procurar = business_title: "Analista Sénior" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Os aumentos exemplos de termo
Os aumentos prazo refere-se a classificação de um documento superior se contém o termo boosted, relativo à documentos que contenham o termo. Este padrão difere dos perfis de classificação em que os perfis de classificação melhorar determinados campos, em vez de termos específicos. O exemplo seguinte ajuda ilustrar as diferenças.

Considere corresponde a um perfil de classificação que boosts num determinado campo, tais como **género** no exemplo musicstoreindex. Os aumentos termo poderia ser utilizado para melhorar ainda mais determinada pesquisa superiores que outros termos. Por exemplo, "rock ^ 2 eletrónicas" irá melhorar documentos que contenham os termos de pesquisa no **género** superior a outros campos pesquisáveis no índice de campo. Além disso, os documentos que contenham o termo de pesquisa "rock" irão ser ordenados superior do que o outro termo de pesquisa "eletrónico" como resultado o valor de intensificação do termo (2).

Para melhorar um termo, utilize o acento circunflexo, "^", símbolo com um fator de aumento (um número) no fim do período da procura. Quanto maior for o fator de aumento, o mais relevante o termo será relativamente de outros termos de pesquisa. Por predefinição, o fator de aumento é 1. Embora o fator de intensificação tem de ser positivo, pode ser inferior a 1 (por exemplo, 0,2).

**Exemplo 6** -botão direito do rato a consulta. Procure as tarefas com o termo "analista de computador" onde, vemos existirem resultados com o computador de palavras e de analistas ainda as tarefas de analista estão na parte superior dos resultados.

* [& queryType = completa & Procurar = business_title:computer analista](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemplo 7** -tente de novo, este tempo os aumentos resulta com o computador do termo ao longo do analista termo se ambas as palavras que não existe.

* [& queryType = completa & Procurar = business_title:computer ^ analista 2](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Exemplo de expressão regular
Uma pesquisa de expressão regular localiza uma correspondência com base nos conteúdos entre barras "/", como documentado no [RegExp classe](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemplo 8** -botão direito do rato a consulta. Procure as tarefas com um termo sénior ou Junior.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

O URL para este exemplo não irá compor corretamente na página. Como solução, copie o URL mais abaixo e cole o endereço de URL do browser:`http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Exemplo de pesquisa com carateres universais
Pode utilizar a sintaxe geralmente reconhecido para vários (\*) ou únicas pesquisas de caráter universal (?) caráter. Tenha em atenção que o parser de consulta Lucene suporta a utilização destas símbolos com um termo único e não uma frase de acesso.

**Exemplo 9** -botão direito do rato a consulta. Procure as tarefas que contêm o prefixo 'prog', que inclui títulos de negócio com os termos de programação e programador no mesmo.

* [& queryType = completos do & $select = business_title & pesquisa = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

Não é possível utilizar um * ou? símbolo como o primeiro caráter de uma procura.

## <a name="next-steps"></a>Passos seguintes
Tente especificar o Parser de consulta Lucene no seu código. As hiperligações seguintes explicam como configurar a consultas de pesquisa para o .NET e a API REST. As ligações utilizam a sintaxe de simples de predefinido, por isso terá de aplicar o que aprendeu do artigo para especificar o **queryType**.

* [Consultar o índice da Azure Search utilizando o SDK .NET](search-query-dotnet.md)
* [Consultar o índice da Azure Search utilizando a API REST](search-query-rest-api.md)

## <a name="see-also"></a>Consultar também

 [Como completa a pesquisa em texto funciona na Azure Search](search-lucene-query-architecture.md)
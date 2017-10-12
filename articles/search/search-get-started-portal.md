---
title: "Tutorial: criar o seu primeiro índice do Azure Search no portal | Microsoft Docs"
description: "No portal do Azure, utilize dados de exemplo predefinidos para gerar um índice. Explore a pesquisa em texto completo, filtros, facetas, a pesquisa difusa, a pesquisa geográfica e muito mais."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 06/26/2017
ms.author: heidist
ms.openlocfilehash: c49989058fdd98d623c5517060f725e5f7e436d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-create-your-first-azure-search-index-in-the-portal"></a>Tutorial: criar o seu primeiro índice do Azure Search no portal

No portal do Azure, comece com um conjunto de dados de exemplo predefinido para gerar rapidamente um índice com o assistente para **Importar dados**. Explore a pesquisa em texto completo, filtros, facetas, a pesquisa difusa e a pesquisa geográfica com o **Explorador de pesquisa**.  

Nesta introdução isenta de código, vai começar com dados predefinidos, para que possa escrever consultas interessantes imediatamente. Embora as ferramentas do portal não substituam o código, são úteis para as tarefas seguintes:

+ Aprendizagem prática com esforço mínimo
+ Criar protótipos e índices antes de escrever código em **Importar dados**
+ Testar consultas e a sintaxe do analisador no **explorador de Pesquisa**
+ Ver um índice existente publicado no seu serviço e procurar os atributos do mesmo

**Estimativa de tempo:** cerca de 15 minutos, mas pode demorar mais, se também for necessário fazer a inscrição na conta ou no serviço. 

Em alternativa, otimize com a utilização de uma [introdução baseada em código à programação do Azure Search no .NET](search-howto-dotnet-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que existe uma [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e o [serviço Azure Search](search-create-service-portal.md). 

Se não pretender aprovisionar um serviço imediatamente, pode ver uma demonstração de 6 minutos dos passos deste tutorial a partir do terceiro minuto deste [vídeo de Descrição Geral do Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="find-your-service"></a>Encontrar o seu serviço
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Abra o dashboard de serviço do seu serviço da Azure Search. Se não tiver afixado o mosaico do serviço ao dashboard, pode encontrar o seu serviço da seguinte forma: 
   
   * Na barra de atalhos, clique em **Mais serviços**, na parte inferior do painel de navegação à esquerda.
   * Na caixa de pesquisa, escreva *pesquisa* para obter uma lista dos serviços de pesquisa da sua subscrição. O serviço deverá aparecer na lista. 

## <a name="check-for-space"></a>Verificar o espaço
Muitos clientes começam com o serviço gratuito. Esta versão está limitada a três índices, três origens de dados e três indexadores. Certifique-se de que tem espaço para itens adicionais antes de começar. Este tutorial cria um objeto de cada. 

> [!TIP] 
> Os mosaicos no dashboard do serviço mostram quantos índices, indexadores e origens de dados já tem. O mosaico Indexador mostra indicadores de êxito e falha. Clique no mosaico para ver a contagem de indexadores. 
>
> ![Mosaicos para indexadores e origens de dados][1]
>

## <a name="create-index"></a> Criar um índice e carregar dados
As consultas de pesquisas iteram sobre um *índice* que contém dados pesquisáveis, metadados e construções utilizadas para otimizar determinados comportamentos de pesquisa.

Para manter esta tarefa baseada no portal, utilizamos um conjunto de dados de exemplo incorporado que pode ser pesquisado com um indexador através do Assistente para **Importar Dados**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Passo 1: Iniciar o assistente para Importar dados
1. No seu dashboard do serviço Azure Search, clique em **Importar dados** na barra de comando para iniciar um assistente que cria e preenche um índice.
   
    ![Comando de importação de dados][2]

2. No assistente, clique em **Origem de Dados** > **Exemplos** > **realestate-us-sample**. Esta origem de dados está pré-configurada com um nome, um tipo e informações da ligação. Depois de criada, torna-se uma “origem de dados existente”, que pode ser reutilizada noutras operações de importação.

    ![Selecionar o conjunto de dados de exemplo][9]

3. Clique em **OK** para utilizá-la.

#### <a name="step-2-define-the-index"></a>Passo 2: Definir o índice
Geralmente, a tarefa de criação de um índice é manual e baseada em código, mas o assistente pode gerar um índice para qualquer origem de dados que consiga pesquisar. No mínimo, os índices precisam de um nome e de uma coleção de campos, com um campo marcado como a chave do documento para identificar exclusivamente cada documento.

Os campos têm tipos de dados e atributos. As caixas de verificação na parte superior são *atributos de índice* que controlam a forma como o campo é utilizado. 

* **Recuperável** significa que aparece na lista de resultados da pesquisa. Pode marcar campos individuais como inacessíveis para os resultados da pesquisa ao desmarcar esta caixa de verificação, por exemplo, quando os campos são utilizados apenas em expressões de filtro. 
* **Filtrável**, **Ordenável** e **Facetável** determinam se um campo pode ser utilizado num filtro, uma ordenação ou uma estrutura de navegação de faceta. 
* **Pesquisável** significa que um campo está incluído na pesquisa de texto completo. As cadeias são pesquisáveis. Os campos numéricos e booleanos são frequentemente marcados como não pesquisáveis. 

Por predefinição, o assistente verifica a origem de dados relativamente a identificadores exclusivos como a base do campo de chaves. As cadeias são atribuídas como recuperáveis e pesquisáveis. Os números inteiros são atribuídos como recuperáveis, filtráveis, ordenáveis e facetáveis.

  ![Índice de realestate gerado][3]

Clique em **OK** para criar o índice.

#### <a name="step-3-define-the-indexer"></a>Passo 3: Definir o indexador
Ainda no assistente para **Importar dados**, clique em **Indexador** > **Nome** e escreva um nome para o indexador. 

Este objeto define um processo executável. Pode colocá-lo na agenda recorrente, mas, por agora, utilize a opção predefinida para executar o indexador uma vez, de imediato, quando clicar em **OK**.  

  ![indexador de realestate][8]

## <a name="check-progress"></a>Verificar progresso
Para monitorizar a importação de dados, regresse ao dashboard do serviço, desloque-se para baixo e faça duplo clique no mosaico **Indexadores**, para abrir a lista de indexadores. Deverá ver o indexador que acabou de criar na lista, com o estado a indicar “em curso” ou “êxito”, juntamente com o número de documentos indexados.

   ![Mensagem de indexador em curso][4]

## <a name="query-index"></a> Consultar o índice
Tem agora um índice de pesquisa pronto para consulta. O **Explorador de pesquisa** é uma ferramenta de consulta incorporada no portal. Proporciona uma caixa de pesquisa, que lhe permite ver se os resultados da pesquisa são os esperados. 

> [!TIP]
> No [vídeo de Descrição Geral do Azure Search](https://channel9.msdn.com/Events/Connect/2016/138), os passos seguintes são demonstrados a partir dos 6m08 segundos.
>

1. Clique em **Explorador de pesquisa** na barra de comando.

   ![Comando do Explorador de pesquisa][5]

2. Clique em **Alterar índice**, na barra de comandos, para mudar para *realestate-us-sample*.

   ![Comandos de índice e de API][6]

3. Clique em **Definir versão da API**, na barra de comandos, para ver que APIs REST estão disponíveis. As APIs de pré-visualização dão-lhe acesso a funcionalidades novas que ainda não foram lançadas geralmente. Para as consultas abaixo, utilize a versão disponível geralmente (2016-09-01), salvo indicação em contrário. 

    > [!NOTE]
    > A [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) e a [Biblioteca .NET](search-howto-dotnet-sdk.md#core-scenarios) são totalmente equivalentes, mas o **Explorador de pesquisa** só está preparado para processar chamadas REST. Aceita sintaxes de [sintaxe de consultas simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) e [ analisador de consultas de Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), bem como todos os parâmetros de pesquisa disponíveis em operações de [Pesquisar no Documento](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 

4. Na barra de pesquisa, introduza as cadeias de consulta abaixo e clique em **Pesquisar**.

  ![Exemplo de consulta de pesquisa][7]

**`search=seattle`**

+ O parâmetro `search` é utilizado para introduzir uma pesquisa de palavras-chave para pesquisa em texto completo; neste caso, devolve listas em King County, no estado norte-americano de Washington, que contenham *Seattle* em qualquer campo pesquisável do documento. 

+ O **Explorador de pesquisa** devolve resultados em JSON, que é verboso e difícil de ler se os documentos tiverem uma estrutura densa. Consoante os seus documentos, poderá ter de escrever código que processe os resultados de pesquisa, de modo a extrair elementos importantes. 

+ Os documentos são compostos por todos os campos marcados como recuperáveis no índice. Para ver os atributos de índice no portal, clique em *realestate-us-sample* no mosaico **Índices**.

**`search=seattle&$count=true&$top=100`**

+ O símbolo `&` é utilizado para acrescentar os parâmetros da pesquisa, que podem ser especificados por qualquer ordem. 

+  O parâmetro `$count=true` devolve uma contagem para a soma de todos os documentos devolvidos. Pode verificar as consultas de filtro através da monitorização das alterações comunicadas por `$count=true`. 

+ O `$top=100` devolve os 100 documentos com a melhor classificação de entre o total. Por predefinição, o Azure Search devolve as 50 melhores correspondências. Pode aumentar ou diminuir a quantidade através de `$top`.

**`search=*&facet=city&$top=2`**

+ `search=*` é uma pesquisa em branco. As pesquisas em branco pesquisam em tudo. Uma das razões para submeter uma consulta em branco é para filtrar ou especificar facetas no conjunto completo de documentos. Por exemplo, pretende que uma estrutura de navegação por facetas seja constituída por todas as cidades no índice.

+  `facet` devolve uma estrutura de navegação que pode transmitir a um controlo de IU. Devolve categorias e uma contagem. Neste caso, as categorias baseiam-se no número de cidades. Não existe agregação no Azure Search, mas pode aproximar a agregação através de `facet`, que dá uma contagem de documentos em cada categoria.

+ `$top=2` devolve dois documentos, o que exemplifica que pode utilizar `top` para reduzir ou aumentar os resultados.

**`search=seattle&facet=beds`**

+ Esta consulta é a faceta para «beds», numa pesquisa de texto por *Seattle*. `"beds"` pode ser especificado como faceta, porque o campo está marcado como um campo recuperável, filtrável e facetável no índice e os valores nele contidos (numéricos, 1 a 5) são adequados para categorizar listas em grupos (listas com três quartos, quatro quartos). 

+ Apenas os campos filtráveis podem ser facetados. Apenas os campos recuperáveis podem ser devolvidos nos resultados.

**`search=seattle&$filter=beds gt 3`**

+ O parâmetro `filter` devolve resultados que correspondem aos critérios que indicou. Neste caso, quartos acima de 3. 

+ A sintaxe do filtro é uma construção OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

**`search=granite countertops&highlight=description`**

+ O detetor de ocorrências refere-se à formatação no texto que corresponde à palavra-chave, tendo em conta que as correspondências são encontradas num determinado campo. Se o termo da sua pesquisa estiver embrenhado numa descrição, pode adicionar o detetor de ocorrências para que seja mais fácil encontrá-lo. Neste caso, é mais fácil ver a expressão formatada `"granite countertops"` no campo da descrição.

**`search=mice&highlight=description`**

+ A pesquisa em texto completo localiza formas de palavras com uma semântica parecida. Neste caso, os resultados da pesquisa contêm texto realçado para “rato”, para casas que tenham uma praga de ratos, em resposta a uma pesquisa de palavras-chave sobre “rato”. Podem aparecer nos resultados diferentes formas da mesma palavra, devido à análise linguística. 

+ O Azure Search suporta 56 analisadores do Lucene e da Microsoft. Por predefinição, o Azure Search utiliza o analisador padrão do Lucene. 

**`search=samamish`**

+ Palavras com erros ortográficos, como “samamish”para o planalto Samammish, na área de Seattle, não conseguem devolver correspondências em pesquisas típicas. Para processar erros ortográficos, pode utilizar a pesquisa difusa, descrita no exemplo seguinte.

**`search=samamish~&queryType=full`**

+ A pesquisa difusa é ativada quando especifica o símbolo `~` e utiliza o analisador de consultas completas, que interpreta e analisa corretamente a sintaxe de `~`. 

+ A pesquisa difusa está disponível quando opta por utilizar o analisar de consultas completas, o que ocorre quando definir `queryType=full`. Para obter mais informações sobre os cenários de consultas que o analisador de consultas completo permite, veja [Lucene query syntax in Azure Search (Sintaxe de consultas do Lucene no Azure Search)](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

+ Quando `queryType` não é especificado, é utilizado o analisador de consultas simples predefinido. O analisador de consultas simples é mais rápido, mas se precisar de pesquisa difusa, de expressões regulares, da pesquisa de proximidade ou de outros tipos de consultas avançadas, será necessária a sintaxe completa. 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ A pesquisa geoespacial é suportada através do [tipo de dados edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) em campos que contenham coordenadas. A pesquisa geográfica é um tipo de filtros especificado em [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

+ A consulta de exemplo filtra todos os resultados relativamente a dados posicionais, em que os resultados estejam a menos de cinco quilómetros de distância de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar `$count`, pode ver quantos resultados são devolvidos quando alterar a distância ou as coordenadas. 

+ A pesquisa geoespacial é útil se a sua aplicação de pesquisa tiver uma funcionalidade “encontrar perto de mim” ou utilizar a navegação por mapa. Contudo, não é uma pesquisa em texto completo. Se os seus utilizadores tiverem de pesquisar por nome de cidade ou país, adicione campos que contenham nomes de cidades ou países, para além de coordenadas.

## <a name="next-steps"></a>Passos seguintes

+ Modifique qualquer um dos objetos que acabou de criar. Depois de executar o assistente uma vez, pode voltar atrás e ver ou modificar componentes individuais: índice, indexador ou origem de dados. Algumas edições, tal como a alteração do tipo de dados de campo, não são permitidas no índice, mas a maioria das propriedades e das definições são modificáveis.

  Para ver componentes individuais, clique nos mosaicos **Índice**, **Indexador** ou **Origens de Dados** no seu dashboard para apresentar uma lista de objetos existentes. Para saber mais sobre as edições a índices que não exigem uma recompilação, veja [Update Index (Azure Search REST API) (Atualizar Índice [API REST do Azure Search])](https://docs.microsoft.com/rest/api/searchservice/update-index).

+ Experimente as ferramentas e os passos com outras origens de dados. O conjunto de dados de exemplo, `realestate-us-sample`, é de uma Base de Dados SQL do Azure que o Azure Search consegue pesquisar. Para além da Base de Dados SQL do Azure, o Azure Search consegue pesquisar e inferior um índice a partir de estruturas de dados simples no Armazenamento de Tabelas do Azure, no Armazenamento de Blobs, no SQL Server em VMs do Azure e o Azure Cosmos DB. O assistente suporta todas estas origens de dados. No código, pode utilizar um *indexador* para preencher facilmente um índice.

+ Todas as outras origens de dados que não pertencem ao indexador são suportadas através de um modelo push, no qual o código envia conjuntos de linhas novos e alterados em JSON para o seu índice. Para obter mais informações, veja [Add, update, or delete documents in Azure Search (Adicionar, atualizar ou eliminar documentos no Azure Search)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Visite as ligações abaixo para saber mais sobre as outras funcionalidades mencionadas neste artigo:

* [Descrição geral dos indexadores](search-indexer-overview.md)
* [Criar Índice (inclui uma explicação detalhada dos atributos do índice)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Explorador de Procura](search-explorer.md)
* [Procurar nos Documentos (inclui exemplos da sintaxe de consulta)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
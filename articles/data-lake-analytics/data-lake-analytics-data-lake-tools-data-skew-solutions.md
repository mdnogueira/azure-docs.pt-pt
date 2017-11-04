---
title: Resolver problemas de desfasamento de dados utilizando ferramentas do Azure Data Lake para Visual Studio | Microsoft Docs
description: "Resolução de problemas possíveis soluções para problemas de desfasamento de dados utilizando ferramentas do Azure Data Lake para Visual Studio."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/16/2016
ms.author: yanacai
ms.openlocfilehash: 9b284ef33be4b935569fc368d81ddf040b2c2b7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Resolver problemas de desfasamento de dados utilizando ferramentas do Azure Data Lake para Visual Studio

## <a name="what-is-data-skew"></a>O que é dados desfasamento?

Brevemente indicado, o desfasamento de dados é um valor excessiva representado. Imagine que atribuiu 50 examiners dedução dos impostos para auditar dedução dos impostos devolve, um examiner para cada Estado de E.U.A.. Wyoming examiner, porque não existe a população é pequena, tem pouco fazer. Na Califórnia, no entanto, o examiner é mantida muito ocupado devido a população de grande o estado.
    ![Exemplo de problema de desfasamento de dados](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

No nosso cenário, os dados é unevenly distribuídos por todos os examiners de dedução dos impostos, que significa que alguns examiners tem de trabalhar mais do que outras pessoas. No seu próprio trabalho ocorrer com frequência situações semelhante ao exemplo dedução dos impostos examiner aqui. Em termos mais técnicos, um vértice muito mais dados ao respetivos elementos, recebe uma situação que torna o vertex eventualmente de trabalho mais do que outros e esse atrasar uma tarefa de toda. O que é um, a tarefa pode falhar, porque poderão ter vértices, por exemplo, uma limitação de tempo de execução de 5 horas e uma limitação de 6 GB de memória.

## <a name="resolving-data-skew-problems"></a>Resolver problemas de desfasamento de dados

Ferramentas do Azure Data Lake para Visual Studio podem ajudá-lo a detetar se a tarefa tem um problema de desfasamento de dados. Se existir um problema, pode resolver-tentando as soluções nesta secção.

## <a name="solution-1-improve-table-partitioning"></a>Solução 1: Melhorar a criação de partições de tabela

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opção 1: Filtrar o valor da chave distorcido antecipadamente

Se não afeta a lógica de negócio, pode filtrar os valores de frequência superior antecipadamente. Por exemplo, se existirem muitos 000 000 000 na coluna GUID, que poderá não pretende que o valor de agregação. Antes de agregação, pode escrever "WHERE GUID! ="000-000 000"" para filtrar o valor de alta frequência.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opção 2: Escolher uma chave de partição ou de distribuição diferente

No exemplo anterior, se pretender apenas verificar a carga de trabalho de auditoria dedução dos impostos em todo o país/região, pode melhorar a distribuição de dados selecionando o número de ID como a chave. Escolha uma partição diferente ou chave de distribuição pode, por vezes, distribuir os dados mais uniformemente, mas tem de certificar-se de que esta opção não afeta a lógica de negócio. Por exemplo, para calcular a soma de dedução dos impostos para cada Estado, poderá querer designar _estado_ como a chave de partição. Se continuar a ocorrer este problema, tente utilizar a opção 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opção 3: Adicionar mais chaves de partição ou de distribuição

Em vez de utilizar apenas _estado_ como uma chave de partição, pode utilizar mais do que uma chave para criação de partições. Por exemplo, considere adicionar _código postal_ como uma chave de partição adicionais para reduzir o tamanho de partição de dados e distribuir os dados mais uniformemente.

### <a name="option-4-use-round-robin-distribution"></a>Opção 4: Utilizar a distribuição de round robin

Se não é possível localizar uma chave adequada para a partição e a distribuição, pode tentar utilizar a distribuição de round robin. Distribuição de round robin processa todas as linhas igualmente e aleatoriamente coloca-os no registos correspondentes. Os dados obtém distribuídos uniformemente, mas perder informações localidade, uma desvantagem que também pode reduzir o desempenho de tarefa para algumas operações. Além disso, se estão a fazer a agregação para a chave distorcida mesmo assim, o problema de desfasamento de dados serão mantidas. Para saber mais sobre a distribuição de round robin, consulte a secção de U-SQL tabela distribuições em [CREATE TABLE (U-SQL): criar uma tabela com o esquema](https://msdn.microsoft.com/en-us/library/mt706196.aspx#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Solução 2: Melhorar o plano de consulta

### <a name="option-1-use-the-create-statistics-statement"></a>Opção 1: Utilizar a instrução CREATE STATISTICS

U-SQL fornece a instrução CREATE STATISTICS em tabelas. Esta declaração fornece mais informações para o otimizador de consultas sobre as características dos dados, como a distribuição de valor, que estão armazenados numa tabela. Para a maior parte das consultas, o otimizador de consultas já gera as estatísticas de necessárias para um plano de consulta de alta qualidade. Ocasionalmente, poderá ser necessário melhorar o desempenho das consultas, criar estatísticas adicionais com CREATE STATISTICS ou ao modificar a estrutura da consulta. Para obter mais informações, consulte o [CREATE STATISTICS (U-SQL)](https://msdn.microsoft.com/en-us/library/azure/mt771898.aspx) página.

Exemplo de código:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Informações de estatísticas não são atualizadas automaticamente. Se atualizar os dados numa tabela sem voltar a criar as estatísticas, poderá diminui o desempenho de consulta.

### <a name="option-2-use-skewfactor"></a>Opção 2: Utilizar SKEWFACTOR

Se pretender que a soma a dedução dos impostos para cada Estado, tem de utilizar o grupo por Estado, uma abordagem que não a evitar o problema de desfasamento de dados. No entanto, pode fornecer uma sugestão de dados na sua consulta para identificar o desfasamento de dados nas chaves para que o otimizador pode preparar um plano de execução.

Normalmente, pode definir o parâmetro como 0,5 e 1, com 0,5 que significa que não desfasamento de pesada significado dissimetrias e 1. Porque a sugestão afeta a otimização de plano de execução para a instrução atual e todas as instruções a jusante, lembre-se de que adicionar a sugestão antes do potencial skewed key-wise agregação.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Exemplo de código:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Opção 3: Utilizar contagem de linhas  
Para além de SKEWFACTOR, nos casos de associação de chave skewed específico, se sabe que o outro conjunto de linhas associado é pequeno, pode indicar o otimizador de adicionando uma sugestão de contagem de linhas na instrução U-SQL antes de associação. Desta forma, o otimizador pode escolher uma estratégia de difusão de associação para o ajudar a melhorar o desempenho. Lembre-se de que a contagem de linhas não resolver o problema de desfasamento de dados, mas pode oferecer ajuda adicional.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Exemplo de código:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Solução 3: Melhorar a reducer definido pelo utilizador e a combinação

Por vezes, pode escrever um operador definido pelo utilizador para lidar com lógica mais complicada processo e um reducer bem escrito e a combinação podem mitigar um problema de desfasamento de dados em alguns casos.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opção 1: Utilizar um reducer recursiva, se possível

Por predefinição, um reducer definido pelo utilizador é executado no modo não recursivo, que significa que reduzem o trabalho para uma chave é distribuída para um único vértice. Mas, se os seus dados é skewed, os conjuntos enormes de dados podem ser processados no vértice único e execute durante muito tempo.

Para melhorar o desempenho, pode adicionar um atributo no seu código para definir reducer sejam executadas em modo recursivo. Em seguida, os conjuntos enormes de dados pode ser distribuídos a vértices vários e executados em paralelo, que acelera a tarefa.

Para alterar um reducer não recursiva para recursiva, tem de certificar-se de que o algoritmo associative. Por exemplo, a soma seja associative, não sendo a mediana não. Terá também de certificar-se de que a entrada e saída para reducer manter o mesmo esquema.

Atributo de recursiva reducer:

    [SqlUserDefinedReducer(IsRecursive = true)]

Exemplo de código:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opção 2: Utilizar o modo de combinação ao nível da linha, se possível

Semelhante para a sugestão de contagem de linhas para casos de associação de chave skewed específico, o modo de combinação tenta distribuir conjuntos de valor de chave skewed enorme para vários vértices para que o trabalho pode ser executado em simultâneo. Modo de combinação não é possível resolver problemas de desfasamento de dados, mas pode oferecer ajuda adicional para conjuntos de valores de chave skewed grande.

Por predefinição, o modo de combinação é completo, o que significa que o conjunto de linhas esquerdo e o conjunto de linha à direita não podem ser separados. Definir o modo como direito/esquerda/interna permite que a associação ao nível da linha. O sistema separa os conjuntos de linha correspondente e distribui-las em vários vértices que são executadas em paralelo. No entanto, antes de configurar o modo de combinação, tenha cuidado garantir que os conjuntos de linhas correspondentes podem ser separados.

O exemplo que se segue mostra um conjunto de linhas esquerdo separados. Cada linha de saída depende de uma única linha de entrada da esquerda e, potencialmente depende todas as linhas da direita com o mesmo valor de chave. Se definir o modo de combinação como à esquerda, o sistema separa esquerda-linha enorme que definir para as pequenas e atribui-los a vários vértices.

![Ilustração do modo de combinação](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Se definir o modo de combinação incorreto, a combinação é menos eficiente e os resultados podem estar errados.

Atributos do modo de combinação de:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Cada linha de saída depende de uma única linha de entrada à esquerda (e, potencialmente, todas as linhas a partir da direita com o mesmo valor de chave).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): cada linha de saída depende de uma única linha de entrada à direita (e, potencialmente, todas as linhas da esquerda com o mesmo valor de chave).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Cada linha de saída depende de uma única linha de entrada à esquerda e à direita com o mesmo valor.

Exemplo de código:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }

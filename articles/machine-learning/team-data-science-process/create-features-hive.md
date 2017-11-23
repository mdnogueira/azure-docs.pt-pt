---
title: "Criar as funcionalidades para dados de um cluster de Hadoop através de consultas do Hive | Microsoft Docs"
description: Exemplos de consultas do Hive que geram funcionalidades em dados armazenados no cluster Azure HDInsight Hadoop.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 91ea23b732f520b02af7e9a9dd77ee62190a520c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Criar as funcionalidades de dados num cluster de Hadoop através de consultas do Hive
Este documento mostra como criar funcionalidades para os dados armazenados num cluster do Azure HDInsight Hadoop através de consultas do Hive. Estas consultas do Hive utilizam incorporados Hive User-Defined funções (UDFs), os scripts que são fornecidos.

As operações necessárias para criar as funcionalidades podem ser consomem muita memória. O desempenho das consultas do Hive se torne mais crítico nestes casos e pode ser melhorado determinados parâmetros de otimização. A otimização destes parâmetros mencionado na secção final.

Exemplos de consultas que são apresentados são específicos para o [NYC Taxi viagem dados](http://chriswhong.com/open-data/foil_nyc_taxi/) cenários também são fornecidos no [repositório do GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já tem o esquema de dados especificado e estão prontas para ser submetido para executar. Na secção final, os parâmetros que os utilizadores podem otimizar para que o desempenho das consultas do Hive pode ser melhorado também são debatidos.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Isto **menu** ligações para tópicos que descrevem como criar funcionalidades para os dados em vários ambientes. Esta tarefa é um passo de [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Aprovisionar um cluster de Hadoop personalizado com o serviço de HDInsight.  Se precisar de instruções, consulte [personalizar Azure HDInsight Clusters do Hadoop para análise avançada](customize-hadoop-cluster.md).
* Os dados terem sido carregados para as tabelas do Hive no Azure HDInsight Hadoop clusters. Se não tiver, siga [criar e carregar dados para as tabelas do Hive](move-hive-tables.md) carregar dados para as tabelas do Hive primeiro.
* Ativar o acesso remoto para o cluster. Se precisar de instruções, consulte [aceder a Head nó de Cluster do Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Geração de funcionalidade
São vários os exemplos das formas em que a funcionalidades podem gerar utilizar consultas do Hive descritos nesta secção. Depois de ter gerado funcionalidades adicionais, pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com as funcionalidades adicionais e a chave primária, o que, em seguida, pode ser associado com a tabela original. Seguem-se exemplos apresentados:

1. [Funcionalidade com base em frequência de geração](#hive-frequencyfeature)
2. [Riscos de variáveis Categórico na classificação binária](#hive-riskfeature)
3. [Extrair as funcionalidades do campo Datetime](#hive-datefeatures)
4. [Extrair as funcionalidades do campo de texto](#hive-textfeatures)
5. [Calcular a distância entre GPS coordenadas](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Geração de funcionalidade com base em frequência
Muitas vezes, é útil calcular as frequências dos níveis de uma variável categórico ou as frequências de determinados combinações de níveis de várias variáveis categórico. Os utilizadores podem utilizar o seguinte script para calcular estes frequências:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Riscos de variáveis categórico na classificação binária
Na classificação binária, variáveis categórico não numéricos tem de ser convertidas para funcionalidades numérico quando os modelos que está a ser utilizados apenas o funcionalidades numérico. Esta conversão é feito ao substituir a cada nível de não sejam numéricos por um risco de um valor numérico. Esta secção mostra algumas consultas do Hive genéricas que calcular os valores de risco (registo odds) de uma variável categórico.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

Neste exemplo, as variáveis `smooth_param1` e `smooth_param2` estão definidas como smooth os valores de risco calculados a partir dos dados. Riscos tem um intervalo entre -Inf e Inf. Um risco > 0 indica que a probabilidade de que o destino é igual a 1 é superior ao 0,5.

Depois do risco é calculado tabela, os utilizadores podem atribuir valores de risco a uma tabela ao associá-lo com a tabela de risco. O ramo de registo a associar a consulta foi fornecida na secção anterior.

### <a name="hive-datefeatures"></a>Extrair as funcionalidades dos campos datetime
Ramo de registo é fornecido com um conjunto de UDFs para processar os campos datetime. Ramo de registo, o formato de datetime predefinido é ' aaaa-MM-dd 00:00:00 ' ('1970-01-01-12:21:32 ' por exemplo). Esta secção mostra exemplos que extrair o dia de um mês, o mês de um campo datetime e outros exemplos para converter uma cadeia de datetime num formato que o formato predefinido para uma cadeia de datetime predefinidas no formato.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Esta consulta do Hive parte do princípio de que o *&#60; campo datetime >* está no formato datetime predefinido.

Se um campo datetime não se encontra no formato predefinição, tem de converter o campo de datetime primeiro carimbo de hora do Unix e, em seguida, converter o carimbo de hora de Unix para uma cadeia de datetime está no formato predefinição. Quando a datetime no predefinido é formato, os utilizadores podem aplicar a datetime incorporada UDFs para extrair as funcionalidades.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Esta consulta, se o *&#60; campo datetime >* tem o padrão como *26/03/2015 04:12:39*, a *' &#60; padrão do campo datetime >'* deve ser `'MM/dd/yyyy HH:mm:ss'`. Para testar, os utilizadores podem executar

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

O *hivesampletable* nesta consulta vem pré-instalada em todos os clusters do Hadoop do Azure HDInsight por predefinição quando os clusters são aprovisionados.

### <a name="hive-textfeatures"></a>Extrair as funcionalidades de campos de texto
Quando a tabela de Hive tem um campo de texto que contém uma cadeia de palavras são delimitados por espaços, a seguinte consulta extrai o comprimento da cadeia e o número de palavras na cadeia.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Calcular as distâncias entre conjuntos de coordenadas GPS
A consulta indicada nesta secção pode ser aplicada diretamente aos dados NYC Taxi viagem. O objetivo desta consulta é mostrar como aplicar uma função de matemática incorporada no ramo de registo para gerar funcionalidades.

Os campos que são utilizados nesta consulta são as coordenadas GPS das localizações de recolha e dropoff, com o nome *recolha\_longitude*, *recolha\_latitude*,  *dropoff\_longitude*, e *dropoff\_latitude*. As consultas que calcular a distância direta entre as coordenadas de recolha e dropoff são:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

As equações matemática que calcular a distância entre duas coordenadas GPS podem ser encontradas no <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable tipo Scripts</a> site, criada por Peter Lapisu. Neste Javascript, a função `toRad()` é apenas *lat_or_lon*pi/180 *, que converte graus em radianos. Aqui, *lat_or_lon* é a longitude ou latitude. Uma vez que o ramo de registo não fornece a função `atan2`, mas fornece a função `atan`, a `atan2` função é implementada por `atan` função na consulta do Hive acima, utilizando a definição fornecida no <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Criar área de trabalho](./media/create-features-hive/atan2new.png)

Uma lista completa de ramo de registo UDFs incorporados podem ser encontrados no **funções incorporadas** secção no <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a>Tópicos de avançadas: parâmetros de otimizar ramo de registo para melhorar a velocidade de consulta
Os parâmetros predefinidos de cluster do ramo de registo poderão não ser adequados para as consultas do Hive e os dados que são de processamento de consultas. Esta secção descreve alguns parâmetros que os utilizadores podem otimizar para melhorar o desempenho das consultas do Hive. Os utilizadores necessitam adicionar o parâmetro de otimização de consultas antes das consultas de processamento de dados.

1. **Espaço de área dinâmica para dados de Java**: para consultas que envolvem grandes conjuntos de dados de associação ou processar registos longos, **a ficar sem espaço de pilha** é um dos erros comuns. Este erro pode ser evitado, definir parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* para valores pretendidos. Segue-se um exemplo:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Este parâmetro atribui 4GB de memória para o espaço de área dinâmica para dados de Java e também facilita a ordenação mais eficiente ao alocar mais memória para o mesmo. É uma boa ideia para reproduzir com estas alocações se existirem todas as tarefas relacionadas com o espaço de área dinâmica para dados de erros de falha.

1. **Tamanho do bloco de DFS**: este parâmetro define a unidade de dados que armazena o sistema de ficheiros mais pequena. Por exemplo, se o tamanho do bloco DFS é de 128 MB, em seguida, todos os dados de tamanho menor e até 128 MB é armazenado num único bloco. Dados que são maiores do que 128 MB são atribuídos blocos adicionais. 
2. Escolher um tamanho de bloco pequeno faz com que grandes sobrecargas no Hadoop, uma vez que o nó nome tem de processar mais pedidos de muitos para localizar o bloco relevante relativas ao ficheiro. Uma definição recomendada quando lidar com gigabytes (ou superior) os dados são:

        set dfs.block.size=128m;

2. **Otimizar a operação de associação no ramo**: enquanto as operações de associação no mapa/reduza framework normalmente ocorrer na fase de reduza, por vezes, os ganhos de bastantes podem ser conseguidos agendando associações na fase de mapa (também denominada "mapjoins"). Para direcionar o ramo de registo para efetuar este procedimento sempre que possível, defina:
   
       set hive.auto.convert.join=true;

3. **Especifica o número de mappers ao ramo**: Hadoop enquanto permite que o utilizador definir o número de reducers, o número de mappers é normalmente não definido pelo utilizador. Um truque que permite que algumas grau de controlo deste número é escolher as variáveis de Hadoop *mapred.min.split.size* e *mapred.max.split.size* como o tamanho do mapa de cada tarefa é determinada por:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normalmente, o valor predefinido:
    
    - *mapred.min.split.size* for 0, do
    - *mapred.Max.split.size* é **Long.MAX** e do 
    - *DFS.Block.size* é 64 MB.

    Como é possível ver, dado o tamanho dos dados, estes parâmetros por "definição" de otimização-los permite-nos otimizar o número de mappers utilizado.

4. Seguem-se alguns outros mais **opções avançadas** para otimizar o desempenho do ramo de registo. Estas permitem definir a memória alocada para mapear e reduzir as tarefas e podem ser útil para ajustar o desempenho. Tenha em atenção que o *mapreduce.reduce.memory.mb* não pode ser maior que o tamanho de memória física de cada nó de trabalho no cluster de Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;


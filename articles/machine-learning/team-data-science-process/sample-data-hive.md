---
title: Dados em tabelas do Azure HDInsight Hive de exemplo | Microsoft Docs
description: Para baixo a amostragem de dados nas tabelas do Hive do Azure HDInsight (Hadopop)
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: d765c2adc8a3aa77d903490875c7f8ad622ef4d2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo em tabelas do Hive do HDInsight
Este artigo descreve como armazenados nas tabelas do Hive do Azure HDInsight através de consultas do Hive reduzir para um tamanho mais gerível para análise de dados de exemplo para baixo. Abrangem os três métodos de amostragem popularmente utilizado:

* Uniform amostragem aleatória
* Amostragem aleatória por grupos
* Amostragem stratified

O seguinte **menu** ligações para tópicos que descrevem como dados de vários ambientes de armazenamento de exemplo.

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Os dados de exemplo por que motivo?**
Se o conjunto de dados que pretende analisar for grande, normalmente, é uma boa ideia baixo-sample os dados para reduzir para um tamanho mais pequeno, mas representativo e mais fácil gerir. Baixo amostragem facilita a compreensão de dados, exploração e engenharia da funcionalidade. A função no processo de ciência de dados de equipa é permitir o rápido fazer o protótipo das funções de processamento de dados e modelos de machine learning.

Esta tarefa de amostragem é um passo de [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Como submeter consultas do Hive
Podem ser submetidas consultas do Hive a partir da consola da linha de comandos do Hadoop no nó principal do cluster de Hadoop. Para tal, inicie sessão no nó principal do cluster de Hadoop, abra a consola da linha de comandos do Hadoop e submeter consultas do Hive a partir daí. Para obter instruções sobre como submeter consultas do Hive na consola da linha de comandos do Hadoop, consulte [como submeter consultas do Hive](move-hive-tables.md#submit).

## <a name="uniform"></a>Uniform amostragem aleatória
A amostragem aleatória Uniform significa que cada linha no conjunto de dados tem hipóteses de que está a ser objeto de amostragem. Pode ser implementado, adicionando um campo adicional rand() para o conjunto de dados na consulta "selecionar" interna e na consulta "selecionar" externa essa condição nesse campo aleatório.

Eis um exemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Aqui, `<sample rate, 0-1>` Especifica a proporção de registos que pretendem que os utilizadores para a amostragem.

## <a name="group"></a>Amostragem aleatória por grupos
Quando dados categóricos de amostragem, poderá pretender incluir ou excluir todas as instâncias de um valor da variável categórico. Este tipo de amostragem é chamado "amostragem pelo grupo". Por exemplo, se tiver uma categórico variável "*estado*", que tem valores como NY, MA, AC, NJ e PA, se pretende que os registos de cada Estado para ser em conjunto, se são amostragem ou não.

Eis um exemplo de consulta que amostras por grupo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Amostragem stratified
Amostragem aleatória é stratified relativamente a uma variável categórico quando os exemplos obtidos têm valores categórico que estejam presentes no rácio do mesmo, tal como estavam da população de principal. Utilizar o mesmo exemplo acima indicadas, suponha que os seus dados têm as de observações seguintes por Estados: NJ tem as 100 observações, NY tem as 60 observações e WA tem as 300 observações. Se especificar a frequência de amostragem stratified ser 0,5, em seguida, o exemplo obtido deve ter aproximadamente 50, 30 e as 150 observações de NJ, NY e WA, respetivamente.

Eis um exemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obter informações sobre métodos de amostragem mais avançadas que estão disponíveis no ramo de registo, consulte [LanguageManual amostragem](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).


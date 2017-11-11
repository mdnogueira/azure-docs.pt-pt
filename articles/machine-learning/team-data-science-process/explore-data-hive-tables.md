---
title: Explorar dados em tabelas do Hive com consultas do Hive | Microsoft Docs
description: "Explore dados em tabelas do Hive através de consultas do Hive."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: 9cf205abcf9782ceac4d9ac5a920e136b69c57b6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar dados em tabelas do Hive com consultas do Hive
Este documento fornece scripts do Hive de exemplo que são utilizados para explorar dados em tabelas do Hive num cluster do HDInsight Hadoop.

O seguinte **menu** ligações para tópicos que descrevem como utilizar as ferramentas para explorar dados vários ambientes de armazenamento.

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Aprovisionar um cluster de Hadoop personalizado com o serviço de HDInsight. Se precisar de instruções, consulte [personalizar Azure HDInsight Clusters do Hadoop para análise avançada](customize-hadoop-cluster.md).
* Os dados terem sido carregados para as tabelas do Hive no Azure HDInsight Hadoop clusters. Se não tiver, siga as instruções em [criar e carregar dados para as tabelas do Hive](move-hive-tables.md) carregar dados para as tabelas do Hive primeiro.
* Ativar o acesso remoto para o cluster. Se precisar de instruções, consulte [aceder a Head nó de Cluster do Hadoop](customize-hadoop-cluster.md).
* Se precisar de obter instruções sobre como submeter consultas do Hive, consulte [como submeter consultas do Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Scripts de consulta de Hive de exemplo para a exploração de dados
1. Obter a contagem das observações por partição`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Obter a contagem das observações por dia`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Obter os níveis numa coluna categórico  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Obter o número de níveis numa combinação de duas colunas categórico`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Obter a distribuição para colunas numéricos  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrair os registos de associar as duas tabelas
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de consulta adicionais para cenários de dados do taxi viagem
Exemplos de consultas que são específicas para [NYC Taxi viagem dados](http://chriswhong.com/open-data/foil_nyc_taxi/) cenários também são fornecidos no [repositório do GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já tem o esquema de dados especificado e estão prontas para ser submetido para executar.


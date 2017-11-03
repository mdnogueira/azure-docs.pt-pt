---
title: Copiar dados de Blobs de armazenamento do Azure para o Data Lake Store | Microsoft Docs
description: Utilize a ferramenta de AdlCopy para copiar dados de Blobs de armazenamento do Azure para o Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: 2dd327f4e4abf19d41a54919c8b9c2e488d34d68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copy data from Azure Storage Blobs to Data Lake Store (Copiar dados dos Blobs de Armazenamento do Azure para o Data Lake Store)
> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

O Azure Data Lake Store fornece uma ferramenta de linha de comandos, [AdlCopy](http://aka.ms/downloadadlcopy), para copiar dados das seguintes origens:

* De Blobs de armazenamento do Azure para o arquivo Data Lake. Não é possível utilizar AdlCopy para copiar dados de Data Lake Store para blobs de armazenamento do Azure.
* Entre duas contas de Azure Data Lake Store.

Além disso, pode utilizar a ferramenta de AdlCopy em dois modos diferentes:

* **Autónomo**, onde a ferramenta utiliza recursos do Data Lake Store para efetuar a tarefa.
* **Utilizar uma conta do Data Lake Analytics**, onde as unidades atribuídas à sua conta do Data Lake Analytics são utilizadas para efetuar a operação de cópia. Pode querer utilizar esta opção quando pretender para executar as tarefas de cópia de forma previsível.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Os Blobs de armazenamento do Azure** contentor com alguns dados.
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **(Opcional) da conta do Azure Data Lake Analytics** -consulte [introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta de Data Lake Store.
* **Ferramenta de AdlCopy**. Instalar a ferramenta de AdlCopy do [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxe da ferramenta AdlCopy
Utilize a sintaxe seguinte para trabalhar com a ferramenta de AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern

Os parâmetros na sintaxe são descritos abaixo:

| Opção | Descrição |
| --- | --- |
| Origem |Especifica a localização da origem de dados no blob storage do Azure. A origem pode ser um contentor de blob, um blob ou outra conta de Data Lake Store. |
| Dest |Especifica o destino de Data Lake Store para copiar para. |
| SourceKey |Especifica a chave de acesso de armazenamento para a origem do blob storage do Azure. Isto é necessário apenas se a origem é um contentor do blob ou um blob. |
| Conta |**Opcional**. Utilize esta opção se pretender utilizar a conta do Azure Data Lake Analytics para executar a tarefa de cópia. Se utilizar a opção de /Account na sintaxe mas não especificar uma conta de Data Lake Analytics, AdlCopy utiliza uma conta predefinida para executar a tarefa. Além disso, se utilizar esta opção, tem de adicionar a origem (Blob de armazenamento do Azure) e de destino (Azure Data Lake Store) como origens de dados para a sua conta do Data Lake Analytics. |
| Unidades |Especifica o número de unidades do Data Lake Analytics que será utilizado para a tarefa de cópia. Esta opção é obrigatória se utilizar o **/conta** opção para especificar a conta de Data Lake Analytics. |
| Padrão |Especifica um padrão regex que indica que os blobs ou ficheiros para copiar. AdlCopy utiliza a correspondência de maiúsculas e minúsculas. O padrão predefinido utilizado quando não é especificado nenhum padrão é copiar todos os itens. Especificar vários padrões de ficheiro não é suportada. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Utilizar AdlCopy (autónomo) para copiar dados de um blob de armazenamento do Azure
1. Abra uma linha de comandos e navegue para o diretório onde está instalada a AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Execute o seguinte comando para copiar um blob específico a partir do contentor de origem para uma Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.NOTE] A sintaxe acima Especifica o ficheiro ser copiados para uma pasta na conta do Data Lake Store. Ferramenta de AdlCopy cria uma pasta, se o nome da pasta especificado não existe.

    Será solicitado para introduzir as credenciais para a subscrição do Azure na qual tem a conta de Data Lake Store. Irá ver um resultado semelhante ao seguinte:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Também pode copiar todos os blobs a partir de um contentor para a conta de Data Lake Store utilizando o seguinte comando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Por exemplo:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Considerações de desempenho

Se estiver a copiar a partir de uma conta do Blob Storage do Azure, pode limitadas durante a cópia do lado do armazenamento de Blobs. Isto irá degradar o desempenho da sua tarefa de cópia. Para saber mais sobre os limites do Blob Storage do Azure, consulte os limites de armazenamento do Azure em [subscrição do Azure e limites de serviço](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Utilizar AdlCopy (autónomo) para copiar dados de outra conta de Data Lake Store
Também pode utilizar AdlCopy para copiar dados entre duas contas de Data Lake Store.

1. Abra uma linha de comandos e navegue para o diretório onde está instalada a AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Execute o seguinte comando para copiar um ficheiro específico a partir de uma conta de Data Lake Store para outro.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Por exemplo:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > A sintaxe acima Especifica o ficheiro ser copiados para uma pasta no destino da conta do Data Lake Store. Ferramenta de AdlCopy cria uma pasta, se o nome da pasta especificado não existe.
   >
   >

    Será solicitado para introduzir as credenciais para a subscrição do Azure na qual tem a conta de Data Lake Store. Irá ver um resultado semelhante ao seguinte:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. O seguinte comando copia todos os ficheiros a partir de uma pasta específica da origem de conta do Data Lake Store para uma pasta no destino da conta do Data Lake Store.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Considerações de desempenho

Ao utilizar AdlCopy como uma ferramenta autónoma, a cópia é executada em partilhado, do Azure geridos recursos. O desempenho que poderá receber este ambiente depende da carga de sistema e de recursos disponíveis. Este modo melhor é utilizado para transferências pequenas numa base ad hoc. Não existem parâmetros têm de ser otimizados quando utilizar AdlCopy como uma ferramenta autónoma.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilizar AdlCopy (com a conta do Data Lake Analytics) para copiar dados
Também pode utilizar a sua conta do Data Lake Analytics para executar a tarefa de AdlCopy para copiar dados de blobs de armazenamento do Azure para o Data Lake Store. Normalmente utilizaria esta opção quando os dados sejam movidos estão no intervalo de gigabytes e terabytes e pretende que o débito previsível e melhor desempenho.

Para utilizar a sua conta do Data Lake Analytics com AdlCopy para copiar a partir de um Blob de armazenamento do Azure, a origem (Blob de armazenamento do Azure) tem de ser adicionada como uma origem de dados para a sua conta do Data Lake Analytics. Para obter instruções sobre como adicionar origens de dados adicionais à sua conta do Data Lake Analytics, consulte [origens de dados de contas de gerir a análise do Data Lake](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Se estiver a copiar de uma conta do Azure Data Lake Store como a origem com uma conta de Data Lake Analytics, não precisa de associar a conta de Data Lake Store com a conta de Data Lake Analytics. O requisito para associar o arquivo de origem com a conta de Data Lake Analytics é apenas quando a origem é uma conta de armazenamento do Azure.
>
>

Execute o seguinte comando para copiar a partir de um blob de armazenamento do Azure a uma conta de Data Lake Store utilizando a conta do Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Por exemplo:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Da mesma forma, execute o seguinte comando para copiar todos os ficheiros a partir de uma pasta específica da origem de conta do Data Lake Store para uma pasta na conta do Data Lake Store de destino utilizando a conta do Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Considerações de desempenho

Quando copiar dados no intervalo de terabytes, utilizar AdlCopy com a sua própria conta do Azure Data Lake Analytics proporciona um desempenho melhor e mais previsível. O parâmetro deve ser otimizado é o número de unidades do Azure Data Lake Analytics a utilizar para a tarefa de cópia. Aumento do número de unidades irá aumentar o desempenho da sua tarefa de cópia. Cada ficheiro a ser copiado, pode utilizar uma unidade máxima. Especificação de mais unidades que o número de ficheiros que está a ser copiados não irá aumentar o desempenho.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Utilizar AdlCopy para copiar dados utilizando a correspondência de padrão
Nesta secção, irá aprender a utilizar AdlCopy para copiar dados de uma origem (no nosso exemplo abaixo utilizamos Blob de armazenamento do Azure) a uma conta de Data Lake Store de destino utilizando a correspondência de padrões. Por exemplo, pode utilizar os passos abaixo para copiar todos os ficheiros com extensão. csv de blob de origem para o destino.

1. Abra uma linha de comandos e navegue para o diretório onde está instalada a AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Execute o seguinte comando para copiar todos os ficheiros com extensão *.csv de um blob específico do contentor de origem para um arquivo Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Faturação
* Se utilizar a ferramenta de AdlCopy como autónoma será faturado para os custos de saída para mover dados, se a conta de armazenamento do Azure de origem não está na mesma região que o Data Lake Store.
* Se utilizar a ferramenta de AdlCopy com o Data Lake Analytics conta, padrão [taxas de faturação do Data Lake Analytics](https://azure.microsoft.com/pricing/details/data-lake-analytics/) será aplicada.

## <a name="considerations-for-using-adlcopy"></a>Considerações sobre a utilização AdlCopy
* AdlCopy (para a versão 1.0.5), suporta a cópia de dados de origens que coletivamente tem mais do que milhares de ficheiros e pastas. No entanto, se ocorrerem problemas copiar um conjunto de dados grande, pode distribuir as ficheiros/pastas em subpastas diferentes e utilizar o caminho para esses subpastas como origem em vez disso.

## <a name="performance-considerations-for-using-adlcopy"></a>Considerações de desempenho para utilizar AdlCopy

AdlCopy suporta a cópia de dados que contém milhares de ficheiros e pastas. No entanto, se ocorrerem problemas copiar um conjunto de dados grande, pode distribuir as ficheiros/pastas em subpastas mais pequenas. AdlCopy foi concebido para cópias ad hoc. Se estiver a tentar copiar dados numa base periódica, deve considerar a utilização [do Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) que fornece gestão completa em torno as operações de cópia.

## <a name="release-notes"></a>Notas de versão
* 1.0.13 - se estiver a copiar dados para a mesma conta do Azure Data Lake Store através de vários comandos adlcopy, não terá de reintroduzir as suas credenciais para cada execução já. Adlcopy irão agora colocar em cache essa informação em várias execuções.

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)

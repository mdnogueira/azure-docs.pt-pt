---
title: "Copie o desempenho de atividade e o guia de otimização no Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre os principais fatores que afetam o desempenho do movimento de dados no Azure Data Factory quando utilizar a atividade de cópia."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: jingwang
ms.openlocfilehash: b0351e4c4dcf19f9e4b6ec11c59c4dd00f0013a2
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copie o desempenho de atividade e o guia de otimização
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-copy-activity-performance.md)
> * [Versão 2 - Pré-visualização](copy-activity-performance.md)


Atividade de cópia da fábrica de dados do Azure fornece um primeira classe dados segurados, fiáveis e elevado desempenho ao carregar a solução. Permite-lhe para copiar dezenas de terabytes de dados diariamente através de uma variedade de avançados de nuvem e no local arquivos de dados. Desempenho de carregamento de dados de incrivelmente rápida são importante para Certifique-se de que pode concentrar-se sobre o problema de "macrodados" núcleos: criar soluções de análise avançada e obter conhecimentos aprofundados sobre tudo o que os dados.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [copiar o desempenho de atividade de versão 1 do Data Factory](v1/data-factory-copy-activity-performance.md).

O Azure oferece um conjunto de nível empresarial soluções de armazém de dados e armazenamento de dados e a atividade de cópia oferece um experiência fácil de configurar e configurar do carregamento de dados altamente otimizados. Com apenas uma atividade de cópia única, pode conseguir:

* Carregar dados para **Azure SQL Data Warehouse** em **1,2 GBps**.
* Carregar dados para **Blob storage do Azure** em **1.0 GBps**
* Carregar dados para **Azure Data Lake Store** em **1.0 GBps**

Este artigo descreve:

* [Números de referência de desempenho](#performance-reference) suportam arquivos de dados de origem e dependente para ajudar a planear o projeto;
* As funcionalidades que podem melhorar o débito de cópia em cenários diferentes, incluindo [unidades de movimento de dados de nuvem](#cloud-data-movement-units), [paralela cópia](#parallel-copy), e [testado cópia](#staged-copy);
* [Orientações de otimização do desempenho](#performance-tuning-steps) sobre como otimizar o desempenho e os principais fatores que podem afetar o desempenho de cópia.

> [!NOTE]
> Se não estiver familiarizado com a atividade de cópia em geral, consulte o artigo [descrição geral da atividade de cópia](copy-activity-overview.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como uma referência, a tabela abaixo mostra o número de débito de cópia **em MBps** para os pares de origem e dependente fornecidos **numa atividade de cópia única executar** com base em testes internas. Para comparação, também demonstra como diferentes definições de [unidades de movimento de dados de nuvem](#cloud-data-movement-units) ou [escalabilidade de tempo de execução de integração de Self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime) (vários nós) podem ajudar no desempenho de cópia.

![Matriz de desempenho](./media/copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>No Azure Data Factory versão 2, quando a atividade de cópia é executada num tempo de execução de integração do Azure, as unidades de movimento de dados em nuvem permitidos mínima é dois. Se não for especificado, consulte unidades de movimento de dados predefinido a ser utilizadas numa [unidades de movimento de dados de nuvem](#cloud-data-movement-units).

Pontos a ter em atenção:

* Débito é calculado utilizando a fórmula seguinte: [tamanho dos dados ler a partir da origem] / [duração de execução da atividade cópia].
* Os números de referência de desempenho na tabela foram avaliados com [TPC-H](http://www.tpc.org/tpch/) conjunto de dados de execução da atividade única cópia.
* Nos arquivos de dados do Azure, a origem e dependente estão na mesma região do Azure.
* Para a cópia híbrida entre no local e nuvem arquivos de dados, cada nó de tempo de execução de integração de Self-hosted estava a ser executado numa máquina em que foi separado do arquivo de dados com abaixo especificação. Quando estava a ser executado numa única atividade, a operação de cópia consumido apenas uma pequena parte de CPU, memória ou largura de banda de rede da máquina de teste.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 núcleos 2.20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memória</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Rede</td>
        <td>Interface da Internet: 10 Gbps; interface de intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Pode obter um maior débito utilizando mais unidades de movimento de dados (DMUs) que não o predefinido permitido DMUs máximos, que são 32 para uma execução da atividade de cópia da nuvem para a nuvem. Por exemplo, com 100 DMUs, pode alcançar cópia de dados de Blobs do Azure para o Azure Data Lake Store em **1.0GBps**. Consulte o [unidades de movimento de dados de nuvem](#cloud-data-movement-units) secção para obter detalhes sobre esta funcionalidade e o cenário suportado. Contacte [suporte do Azure](https://azure.microsoft.com/support/) para pedir DMUs mais.

## <a name="cloud-data-movement-units"></a>Unidades de movimento de dados de nuvem

A **unidade de movimento de dados de nuvem (DMU)** é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na fábrica de dados. **DMU apenas se aplica a [Runtime de integração do Azure](concepts-integration-runtime.md#azure-integration-runtime)**, mas não [Self-hosted integração Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

**As unidades de movimento de dados de nuvem mínima atribuir a cópia de execução da atividade é dois.** Se não for especificado, a tabela seguinte lista os DMUs predefinido utilizados em cenários de cópia diferentes:

| Cenário de cópia | DMUs predefinido determinados pelo serviço |
|:--- |:--- |
| Copiar dados entre os arquivos de ficheiros | Entre 4 e 16 consoante o número e tamanho dos ficheiros. |
| Todos os outros cenários de cópia | 4 |

Para substituir esta predefinição, especifique um valor para o **cloudDataMovementUnits** propriedade da seguinte forma. O **valores permitidos** para o **cloudDataMovementUnits** propriedade são 2, 4, 8, 16, 32. O **número real de nuvem DMUs** que a operação de cópia utiliza em tempo de execução é igual ou inferior ao valor configurado, dependendo do seu padrão de dados. Para obter informações sobre o nível de ganhos de desempenho poderá obter quando configurar mais unidades para uma origem de cópia específico e o sink, consulte o [referência de desempenho](#performance-reference).

Pode ver as unidades de movimento de dados de nuvem, na verdade, utilizado para cada cópia executar a atividade de cópia ao monitorizar uma execução da atividade de saída. Saiba os detalhes da [copiar a monitorização da atividade](copy-activity-overview.md#monitoring).

> [!NOTE]
> Se precisar de mais nuvem DMUs para um maior débito, contacte [suporte do Azure](https://azure.microsoft.com/support/). Definição de 8 e superior funciona atualmente apenas quando é **copiar vários ficheiros de Blob storage/Data Lake Store/Amazon S3/nuvem FTP/nuvem SFTP para quaisquer outros arquivos de dados de nuvem.**.
>

**Exemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

### <a name="cloud-data-movement-units-billing-impact"></a>Unidades de movimento de dados de nuvem impacto de faturação

Tem **importante** lembrar-se de que são-lhe cobrados com base no tempo total da operação de cópia. A duração total que é-lhe faturado de movimento de dados é a soma de duração em DMUs. Se uma tarefa de cópia utilizados para demorar uma hora com duas unidades de nuvem e agora demora 15 minutos com oito unidades de nuvem, a fatura geral permanece quase o mesmo.

## <a name="parallel-copy"></a>Cópia paralela

Pode utilizar o **parallelCopies** propriedade para indicar o paralelismo que pretende que a atividade de cópia para utilizar. Pode considerar desta propriedade como o número máximo de threads dentro da atividade de cópia que podem ler a partir da sua origem de ou escrever os arquivos de dados de receptores em paralelo.

Para cada cópia de execução da atividade, o Data Factory determina o número de cópias paralelas a utilizar para copiar dados a partir da origem de dados armazenam e para os dados de destino arquivo. O número predefinido de cópias paralelas que utiliza depende do tipo de origem e dependente que está a utilizar:

| Cenário de cópia | Contagem de cópia paralelas predefinido determinada pelo serviço |
| --- | --- |
| Copiar dados entre os arquivos de ficheiros |Entre 1 e 32. Depende do tamanho dos ficheiros e o número de nuvem movimento unidades do data (DMUs) utilizados para copiar dados entre dois arquivos de dados de nuvem ou a configuração da máquina de tempo de execução de integração de Self-hosted física. |
| Copiar dados a partir de qualquer arquivo de dados de origem para o Table storage do Azure |4 |
| Todos os outros cenários de cópia |1 |

Normalmente, o comportamento predefinido deverá dar-lhe o melhor débito. No entanto controlar a carga em computadores que alojam os dados armazena ou para otimizar o desempenho de cópia, pode optar por substituir o valor predefinido e especifique um valor para o **parallelCopies** propriedade. O valor tem de ser um número inteiro maior que ou igual a 1. No momento de execução para o melhor desempenho, atividade de cópia utiliza um valor que é menor ou igual ao valor que definir.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

Pontos a ter em atenção:

* Quando copiar dados entre os arquivos de ficheiros, o **parallelCopies** determinar o paralelismo ao nível do ficheiro. Dentro de um único ficheiro de agrupamento aconteceria por baixo automática e transparente e foi concebido para utilizar o tamanho do segmento adequado melhor para um tipo de arquivo de dados de origem especificada para carregar os dados em paralelo e orthogonal para parallelCopies. O número real de cópias paralelas utiliza o serviço de movimento de dados para a operação de cópia em tempo de execução é não mais do que o número de ficheiros que tiver. Se o comportamento de cópia é **mergeFile**, atividade de cópia não é possível tirar partido de paralelismo de nível de ficheiro.
* Quando especificar um valor para o **parallelCopies** propriedade, considere o aumento de carga no seu arquivos de dados de origem e dependente e tempo de execução de integração de Self-Hosted se a atividade de cópia é dado o poder por-por exemplo, para cópia híbrida. Isto acontece, especialmente quando tem várias atividades ou em simultâneo executa as atividades de mesmo executar o mesmo arquivo de dados. Se reparar em que o arquivo de dados ou o tempo de execução de integração de Self-hosted é overwhelmed com a carga, diminuir o **parallelCopies** valor a relieve a carga.
* Quando copiar dados de arquivos de que não sejam baseados em ficheiros para os arquivos de que são baseados em ficheiros, o serviço de movimento de dados ignora a **parallelCopies** propriedade. Mesmo que o paralelismo for especificado, este não está aplicado neste caso.
* **parallelCopies** é orthogonal para **cloudDataMovementUnits**. A primeira é contabilizada em todas as nuvem dados movimento unidades.

## <a name="staged-copy"></a>Cópia faseada

Quando copiar dados de um arquivo de dados de origem para um arquivo de dados dependente, pode optar por utilizar o Blob storage como um arquivo intermédio de teste. Transição é especialmente útil nos seguintes casos:

- **Pretende para a ingestão de dados a partir de vários arquivos de dados para o SQL Data Warehouse através do PolyBase**. Armazém de dados do SQL Server utiliza o PolyBase como um mecanismo de alto débito para carregar uma grande quantidade de dados para o SQL Data Warehouse. No entanto, tem de ser a origem de dados no armazenamento de BLOBs ou do Azure Data Lake Store, e tem de cumprir critérios adicionais. Quando carregar dados a partir de um arquivo de dados diferente de armazenamento de BLOBs ou do Azure Data Lake Store, pode ativar dados copiar através do armazenamento de BLOBs de transição intermédio. Nesse caso, o Data Factory efetua as transformações de dados necessários para assegurar que cumpre os requisitos do PolyBase. Em seguida, utiliza o PolyBase para carregar dados para o armazém de dados do SQL Server de forma eficiente. Para obter mais informações, consulte [PolyBase de utilização para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Por vezes, demora tempo a executar um movimento de dados híbrida (ou seja, para copiar a partir de um local de arquivo de dados para um arquivo de dados na nuvem) através de uma ligação de rede lenta**. Para melhorar o desempenho, pode utilizar a cópia pré-configurada para comprimir os dados no local para que demora menos tempo para mover dados para o arquivo de dados de teste na nuvem, em seguida, descomprimir os dados no arquivo de teste antes de carregar para o arquivo de dados de destino.
- **Não pretende abrir as portas que não seja a porta 80 e a porta 443 na firewall, devido a políticas de TI empresarias**. Por exemplo, quando copiar dados de um arquivo de dados no local para um receptor de SQL Database do Azure ou um receptor de Azure SQL Data Warehouse, terá de ativar a comunicação de TCP de saída na porta 1433 para a firewall do Windows e a firewall da empresa. Neste cenário, cópia faseada pode tirar partido do tempo de execução de integração Self-hosted primeiro copiar dados para um instância de teste através de HTTP ou HTTPS na porta 443 de armazenamento de BLOBs, em seguida, carregar os dados na base de dados SQL ou SQL Data Warehouse do teste de armazenamento de Blobs. Neste fluxo, não precisa de ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Funciona como faseada de cópia

Ao ativar a funcionalidade de teste, primeiro os dados são copiados do arquivo de dados de origem para o armazenamento de BLOBs de transição (traga o seu próprio). Em seguida, os dados são copiados do arquivo de dados de teste para o arquivo de dados do sink. Fábrica de dados gere automaticamente o fluxo de duas etapas para si. Fábrica de dados limpa também dados temporários do armazenamento transição depois do movimento de dados está concluído.

![Cópia faseada](media/copy-activity-performance/staged-copy.png)

Quando ativar o movimento de dados através da utilização de um arquivo de teste, pode especificar se pretende que os dados para ser comprimido antes de mover dados do arquivo de dados de origem para um arquivo de dados provisória ou teste e, em seguida, descomprimir antes de mover dados de um provisório ou dados de teste Armazene o arquivo de dados do sink.

Atualmente, não é possível copiar dados entre dois arquivos de dados no local através da utilização de um arquivo de teste.

### <a name="configuration"></a>Configuração

Configurar o **enableStaging** definição na atividade de cópia para especificar se pretende que os dados a ser testada no Blob storage antes de carregar para um arquivo de dados de destino. Quando configurar **enableStaging** para `TRUE`, especifique as propriedades adicionais listadas na seguinte tabela. Se não tiver uma, também terá de criar um Storage do Azure ou o armazenamento partilhado serviço ligado de assinatura de acesso para transição.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| **enableStaging** |Especifique se pretende copiar os dados através de um provisório arquivo de teste. |Falso |Não |
| **linkedServiceName** |Especifique o nome de um [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) serviço, que se refere à instância de armazenamento que utilizar como um arquivo de transição provisória ligado. <br/><br/> Não é possível utilizar o armazenamento com uma assinatura de acesso partilhado para carregar dados para o SQL Data Warehouse através do PolyBase. Pode utilizá-lo em todos os outros cenários. |N/D |Sim, quando **enableStaging** está definido como verdadeiro |
| **caminho** |Especifique o caminho de armazenamento de BLOBs que pretende que contêm os dados de teste. Se não fornecer um caminho, o serviço cria um contentor para armazenar dados temporários. <br/><br/> Especifique um caminho de apenas se utilizar o armazenamento com uma assinatura de acesso partilhado ou exigir temporários de dados numa localização específica. |N/D |Não |
| **enableCompression** |Especifica se os dados devem ser comprimidos antes que seja copiado para o destino. Esta definição reduz o volume de dados a serem transferidos. |Falso |Não |

Segue-se uma definição de exemplo de atividade de cópia com as propriedades descritas na tabela anterior:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Teste cópia impacto de faturação

São-lhe cobrados com base em dois passos: copiar a duração e copie o tipo.

* Quando utiliza o teste durante uma cópia da nuvem (copiar dados de um arquivo de dados na nuvem para outro arquivo de dados de nuvem, ambas as fases dado o poder pelo Runtime de integração do Azure), são-lhe cobrados [soma da duração de cópia para o passo 1 e o passo 2] x [nuvem cópia unitário].
* Quando utiliza o teste durante uma cópia híbrida (copiar dados de um arquivo de dados no local para um arquivo de dados de nuvem, uma fase dado o poder pelo tempo de execução de integração de Self-hosted), são-lhe cobrados durante [híbrida cópia] x [híbrida cópia unitário] + [duração de cópia da nuvem] x [nuvem cópia unitário].

## <a name="performance-tuning-steps"></a>Passos de otimização de desempenho

Sugerimos que siga estes passos para otimizar o desempenho do seu serviço do Data Factory com atividade de cópia:

1. **Estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste o pipeline utilizando a atividade de cópia em relação a uma amostra de dados representativos. Recolher detalhes da execução e as características de desempenho seguintes [copiar a monitorização da atividade](copy-activity-overview.md#monitoring).

2. **Diagnosticar e otimizar o desempenho**. Se o desempenho que observar não cumprir as suas expectativas, tem de identificar constrangimentos de desempenho. Em seguida, Otimize o desempenho para remover ou minimizar o efeito de constrangimentos. Uma descrição completa de diagnóstico de desempenho ultrapassa o âmbito deste artigo, mas aqui estão algumas considerações comuns:

   * Funcionalidades de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de movimento de dados de nuvem](#cloud-data-movement-units)
     * [Cópia faseada](#staged-copy)
     * [Escalabilidade de tempo de execução de integração personalizada alojada](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Tempo de execução de integração personalizada alojada](#considerations-for-self-hosted-integration-runtime)
   * [Origem](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialização e a anulação da serialização](#considerations-for-serialization-and-deserialization)
   * [Compressão](#considerations-for-compression)
   * [Mapeamento de colunas](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)

3. **Expandir a configuração para o conjunto de dados completo**. Quando estiver satisfeito com os resultados de execução e o desempenho, pode expandir a definição e o pipeline de forma a cobrir o conjunto completo de dados.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considerações sobre a integração personalizada alojada tempo de execução

Se a atividade de cópia é executada num tempo de execução de integração Self-hosted, tenha em atenção o seguinte:

**A configuração**: Recomendamos que utilize uma máquina dedicada para o anfitrião de tempo de execução de integração. Consulte [considerações sobre a utilização de tempo de execução de integração de Self-hosted](concepts-integration-runtime.md).

**Aumentar horizontalmente**: um único lógica Self-hosted integração tempo de execução com um ou mais nós podem servir várias execuções de atividade de cópia em simultâneo em simultâneo. Se tiver necessidade pesada movimento de dados de híbrida com grande número de execuções de atividade de cópia em simultâneo ou com grande volume de dados para copiar, considere a [ampliação Self-hosted integração Runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability) , de modo a aprovisionar mais recursos para capacitar cópia.

## <a name="considerations-for-the-source"></a>Considerações para a origem

### <a name="general"></a>Geral

Lembre-se de que o arquivo de dados subjacente não é assoberbado com outras cargas de trabalho que estejam a executar no ou nele.

Para os arquivos de dados da Microsoft, consulte [monitorização e tópicos de otimização](#performance-reference) que são específicas para os arquivos de dados e ajuda a compreender dados armazenam as características de desempenho, minimizar os tempos de resposta e maximizem o débito.

* Se copiar dados **do armazenamento de BLOBs para o SQL Data Warehouse**, considere a utilização de **PolyBase** para melhorar o desempenho. Consulte [PolyBase de utilização para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter mais detalhes.
* Se copiar dados **do HDFS para Azure Blob/Azure Data Lake Store**, considere a utilização de **DistCp** para melhorar o desempenho. Consulte [DistCp de utilização para copiar dados de HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) para obter mais detalhes.
* Se copiar dados **de Redshift ao Azure SQL Data Warehouse/Azure BLob/Azure Data Lake Store**, considere a utilização de **descarregamento** para melhorar o desempenho. Consulte [descarregamento de utilização para copiar dados do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) para obter mais detalhes.

### <a name="file-based-data-stores"></a>Arquivos de dados baseada em ficheiros

* **Tempo médio de tamanho de ficheiro e a contagem de ficheiros**: um ficheiro de dados das transferências de atividade de cópia de cada vez. Com a mesma quantidade de dados para ser movida, o débito global é inferior se os dados constituem em muitos ficheiros pequenos em vez de alguns ficheiros grandes devido a fase de arranque de configuração para cada ficheiro. Por conseguinte, se possível, combine o pequenos ficheiros em ficheiros maiores para obter um maior débito.
* **Ficheiro de formato e compressão**: para obter mais formas de melhorar o desempenho, consulte o [considerações para serialização e a anulação da serialização](#considerations-for-serialization-and-deserialization) e [considerações para compressão](#considerations-for-compression) secções.

### <a name="relational-data-stores"></a>Arquivos de dados relacional

* **Padrão de dados**: O esquema de tabela afeta o débito de cópia. Um tamanho de linha grande dá-lhe um melhor desempenho do que o tamanho de linha pequenos, para copiar a mesma quantidade de dados. O motivo é que a base de dados de forma mais eficiente pode obter menos lotes de dados que contenham menos linhas.
* **Consulta ou o procedimento armazenado**: otimizar a lógica da consulta ou procedimento armazenado que especificar na origem de atividade de cópia para obter dados de forma mais eficiente.

## <a name="considerations-for-the-sink"></a>Considerações para o sink

### <a name="general"></a>Geral

Lembre-se de que o arquivo de dados subjacente não é assoberbado com outras cargas de trabalho que estejam a executar no ou nele.

Arquivos de dados da Microsoft, consulte [monitorização e tópicos de otimização](#performance-reference) que são específicas para os arquivos de dados. Estes tópicos podem ajudar a compreender as características de desempenho do arquivo de dados e como minimizar os tempos de resposta e maximizar o débito.

* Se copiar dados **do armazenamento de BLOBs para o SQL Data Warehouse**, considere a utilização de **PolyBase** para melhorar o desempenho. Consulte [PolyBase de utilização para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter mais detalhes.
* Se copiar dados **do HDFS para Azure Blob/Azure Data Lake Store**, considere a utilização de **DistCp** para melhorar o desempenho. Consulte [DistCp de utilização para copiar dados de HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) para obter mais detalhes.
* Se copiar dados **de Redshift ao Azure SQL Data Warehouse/Azure BLob/Azure Data Lake Store**, considere a utilização de **descarregamento** para melhorar o desempenho. Consulte [descarregamento de utilização para copiar dados do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) para obter mais detalhes.

### <a name="file-based-data-stores"></a>Arquivos de dados baseada em ficheiros

* **Copie o comportamento**: Se copiar dados de um arquivo de dados baseada em ficheiros diferente, a atividade de cópia tem três opções através de **copyBehavior** propriedade. Preserva a hierarquia, flattens hierarquia ou intercala ficheiros. Preservação ou pelo hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas a intercalação ficheiros faz com que a sobrecarga de desempenho aumentar.
* **Ficheiro de formato e compressão**: consulte a [considerações para serialização e a anulação da serialização](#considerations-for-serialization-and-deserialization) e [considerações para compressão](#considerations-for-compression) secções para obter mais maneiras melhorar o desempenho.

### <a name="relational-data-stores"></a>Arquivos de dados relacional

* **Copie o comportamento**: consoante as propriedades que tiver configurado para **sqlSink**, atividade de cópia escreve dados na base de dados de destino de formas diferentes.
  * Por predefinição, as utilizações de serviço de movimento de dados a API de cópia em massa ao inserir dados acrescente modo, que fornece o melhor desempenho.
  * Se configurar um procedimento armazenado no sink, a base de dados aplica-se a uma linha de dados num momento, em vez de como um carregamento em massa. Desempenho ignora significativamente. Se o conjunto de dados for grande, quando aplicável, considere mudar para utilizar o **preCopyScript** propriedade.
  * Se configurar o **preCopyScript** executar de propriedade para cada atividade de cópia, o serviço aciona o script e, em seguida, utilizar a API de cópia em massa para inserir os dados. Por exemplo, para substituir a tabela completa com os dados mais recentes, pode especificar um script de eliminar primeiro todos os registos antes dos novos dados da origem de carregamento em massa.
* **Tamanho de dados padrão e batch**:
  * O esquema de tabela afeta o débito de cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grande dá-lhe um melhor desempenho a um tamanho de linha pequeno porque a base de dados de forma mais eficiente pode consolidar menos lotes de dados.
  * Atividade de cópia insere dados de uma série de lotes. Pode definir o número de linhas num batch ao utilizar o **writeBatchSize** propriedade. Se os seus dados têm linhas pequenas, pode definir o **writeBatchSize** propriedade com um valor superior a beneficiar inferior sobrecarga de lote e o débito mais elevado. Se o tamanho de linha de dados for grande, tenha cuidado quando aumenta **writeBatchSize**. Um valor elevado pode originar uma falha de cópia causada por sobrecarregar a base de dados.

### <a name="nosql-stores"></a>Arquivos de NoSQL

* Para **tabela armazenamento**:
  * **Partição**: escrita de dados para partições intercaladas significativamente degrada o desempenho. Ordene os dados de origem, a chave de partição para que os dados são inseridos eficiente para uma partição após outro ou ajustar a lógica de escrever os dados para uma única partição.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações para serialização e a anulação da serialização

Serialização e a anulação da serialização podem ocorrer quando o conjunto de dados de entrada ou o conjunto de dados de saída é um ficheiro. Consulte [suportado os formatos de ficheiro e compressão](supported-file-formats-and-compression-codecs.md) com detalhes sobre os formatos de ficheiro suportados pela atividade de cópia.

**Copie o comportamento**:

* Copiar ficheiros entre os arquivos de dados de ficheiros:
  * Quando a entrada e de saída conjuntos de dados de ambos os têm o mesmo ou não existem definições do formato de ficheiro, o serviço de movimento de dados executa um **cópia binária** sem serialização ou anulação da serialização. Verá um débito mais elevado em comparação com o cenário em que as definições de formato de ficheiro de origem e dependente são diferentes entre si.
  * Quando a entrada e saída conjuntos de dados de ambos os estão no formato de texto e apenas a codificação de tipo é diferente, o serviço de movimento de dados não só a conversão de codificação. Não faz qualquer serialização e anulação da serialização, o que faz com que alguma sobrecarga comparado com uma cópia binária de desempenho.
  * Quando a entrada e de saída conjuntos de dados ambas têm formatos de ficheiro diferente ou configurações diferentes, como delimitadores, o serviço de movimento de dados deserializes dados de origem para transmitir, transformar e, em seguida, a serialização para o formato de saída que indicou. Esta operação resulta num desempenho muito mais significativo sobrecarga em comparação comparado outros cenários.
* Quando copia ficheiros para/de um arquivo de dados que não é baseado em ficheiros (por exemplo, a partir de um arquivo baseado em ficheiros num arquivo relacional), o passo de serialização ou anulação da serialização, é necessário. Este passo resulta em overhead de desempenho significativas.

**Formato de ficheiro**: escolher o formato de ficheiro pode afetar o desempenho de cópia. Por exemplo, Avro é um formato binário compact que armazena os metadados com dados. Tem suporte abrangente do ecossistema do Hadoop para processamento e a consultar. No entanto, Avro é mais dispendioso de serialização e a anulação da serialização, o que resulta num débito de cópia inferior em comparação comparado o formato de texto. Certifique-à sua escolha do formato de ficheiro em todo o fluxo de processamento de forma holística. Começar com os dados de que forma é armazenado nos arquivos de dados de origem ou a ser extraído de sistemas externos; o formato de armazenamento, processamento analítico e consultar; melhores e num formato de que os dados devem ser exportados em marts de dados para relatórios e visualização de ferramentas. Por vezes, um formato de ficheiro que é inferior ao ideal para ler e desempenho de escrita pode ser uma boa opção quando considerar o processo geral analítico.

## <a name="considerations-for-compression"></a>Considerações para compressão

Quando o conjunto de dados de entrada ou saído é um ficheiro, é possível definir a atividade de cópia para efetuar a compressão ou descompressão, tal como escreve dados para o destino. Quando escolher a compressão, efetua uma variação entre a entrada/saída (e/s) e da CPU. A compressão os custos de dados adicionais em recursos de computação. Mas, return, reduz e/s de rede e armazenamento. Consoante os dados, poderá ver um aumento no débito cópia global.

**Codec**: cada codec de compressão tem vantagens. Por exemplo, bzip2 tem o débito de cópia mais baixo, mas obter o melhor desempenho das consultas do Hive com bzip2 porque pode dividi-lo para processamento. GZip é a opção mais equilibrada e é utilizado mais frequentemente. Escolha o codec que melhor se adequa aos seus cenário ponto-a-ponto.

**Nível**: pode escolher entre duas opções para cada codec de compressão: fastest comprimido e forma ideal comprimido. A opção comprimida fastest comprime os dados mais rapidamente possível, mesmo que o ficheiro resultante não será comprimido forma ideal. A opção de forma ideal comprimida despende mais tempo no compressão e gera uma quantidade mínima de dados. Pode testar a ambas as opções para ver que fornece um melhor desempenho global no seu caso.

**Uma consideração**: para copiar uma grande quantidade de dados entre um arquivo no local e na nuvem, considere a utilização de [testado cópia](#staged-copy) com compressão ativada. Utilizar o armazenamento intermédio é útil quando a largura de banda de rede da sua empresa e os serviços do Azure é o fator de limitação e pretende que o conjunto de dados de entrada tanto os conjuntos de dados de saída estar no formato descomprimido.

## <a name="considerations-for-column-mapping"></a>Considerações para mapeamento de colunas

Pode definir o **columnMappings** propriedade na atividade de cópia para o mapa de todos os ou um subconjunto de colunas de entrada para as colunas de saída. Depois do serviço de movimento de dados lê os dados da origem, tem de efetuar o mapeamento de colunas nos dados antes de escreve os dados para o sink. Este processamento adicional reduz o débito de cópia.

Se o arquivo de dados de origem é consultável, por exemplo, se for um arquivo relacional como base de dados SQL ou SQL Server, ou se for um arquivo de NoSQL, como o armazenamento de tabelas ou base de dados do Azure Cosmos, considere emitir os filtros de coluna e reordenação lógica para o **consulta** propriedade em vez de utilizar o mapeamento de colunas. Desta forma, a projeção ocorre enquanto o serviço de movimento de dados lê dados a partir do arquivo de dados de origem, onde é muito mais eficiente.

Saiba mais de [mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Outras considerações

Se o tamanho dos dados que pretende copiar for grande, pode ajustar a lógica de negócio para ainda mais os dados de partição e agendar a atividade de cópia para ser executada com mais frequência para reduzir o tamanho dos dados para cada execução da atividade de cópia.

Tenha cuidado sobre o número de conjuntos de dados e atividades de cópia necessidade de fábrica de dados ligar para o mesmo arquivo de dados ao mesmo tempo. Várias tarefas de cópia em simultâneo poderão limitar um arquivo de dados e provocar a degradação do desempenho, repetições internas do tarefa de cópia e em alguns casos, falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: cópia de um servidor de SQL no local para o Blob storage

**Cenário**: é criado um pipeline para copiar dados de um servidor de SQL no local para o Blob storage num formato CSV. Para efetuar a tarefa de cópia mais rapidamente, os ficheiros CSV devem ser comprimidos para bzip2 formato.

**Análise e teste**: O débito de atividade de cópia é inferior a 2 MBps, que é muito mais lenta do que o benchmark de desempenho.

**Análise de desempenho e a otimização**: para resolver o problema de desempenho, vamos ver como os dados são processados e movidos.

1. **Ler dados**: tempo de execução de integração é aberta uma ligação ao SQL Server e envia a consulta. SQL Server responde ao enviar o fluxo de dados para o tempo de execução de integração através da intranet.
2. **Serializar e comprimir dados**: integração e tempo de execução serializa o fluxo de dados para o formato CSV, comprimir os dados para uma transmissão em fluxo bzip2.
3. **Escrever dados**: integração runtime carrega o fluxo de bzip2 para armazenamento de BLOBs através da Internet.

Como pode ver, os dados estão a ser processados e movidos de forma sequencial transmissão em fluxo: SQL Server > LAN > integração runtime > WAN > armazenamento de Blobs. **O desempenho global é gated pelo débito mínimo através do pipeline**.

![Fluxo de dados](./media/copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores podem fazer com que o valor de desempenho:

* **Origem**: próprio servidor de SQL tem débito baixo devido a cargas pesadas.
* **Autoalojado integração Runtime**:
  * **LAN**: tempo de execução de integração está localizado longe máquina do SQL Server e tem uma ligação de largura de banda baixa.
  * **Tempo de execução de integração**: integração runtime atingiu o respetivas limitações de carga para efetuar as seguintes operações:
    * **Serialização**: ao serializar o fluxo de dados para o formato CSV tem débito lento.
    * **Compressão**: escolher um codec de compressão lenta (por exemplo, bzip2, ou seja, 2,8 MBps com Core i7).
  * **WAN**: A largura de banda entre a rede empresarial e os serviços do Azure é baixa (por exemplo, T1 = 1,544 kbps; T2 = 6,312 kbps).
* **Sink**: armazenamento de BLOBs tem débito baixo. (Este cenário é pouco provável porque o respetivo SLA garante um mínimo de 60 MBps).

Neste caso, bzip2 compressão de dados poderá ser abrandamento do pipeline de todo. Mudar para um codec de compressão gzip poderá facilitar este congestionamento.

## <a name="reference"></a>Referência

Segue-se desempenho de monitorização e a otimização de referências para alguns dos arquivos de dados suportadas:

* Armazenamento do Azure (incluindo o armazenamento de BLOBs e de armazenamento de tabelas): [metas de escalabilidade do Storage do Azure](../storage/common/storage-scalability-targets.md) e [lista de verificação de armazenamento do Azure, desempenho e escalabilidade](../storage/common/storage-performance-checklist.md)
* Base de dados SQL do Azure: Pode [monitorizar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verifique a percentagem de unidade (DTU) de transação de base de dados
* O Azure SQL Data Warehouse: A capacidade é medida em unidades do data warehouse (DWUs); consulte [gerir computação power no Azure SQL Data Warehouse (descrição geral)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [níveis de desempenho na base de dados do Azure Cosmos](../cosmos-db/performance-levels.md)
* SQL Server no local: [monitorizar e otimizar para desempenho](https://msdn.microsoft.com/library/ms189081.aspx)
* Servidor de ficheiros no local: [otimização de desempenho para servidores de ficheiros](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md)
- [Copie a tolerância a falhas de atividade](copy-activity-fault-tolerance.md)

---
title: "Cenários de dados que envolvem Data Lake Store | Microsoft Docs"
description: "Compreender os diferentes cenários e ferramentas com que dados podem ingeridos, processados, transferidas e serão visualizados num Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 6428c6d9fcb577f18221ee48a61456c460bd8176
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Utilizar o Azure Data Lake Store para requisitos de macrodados
Existem quatro fases principais grande de processamento de dados:

* Ingerir grandes quantidades de dados para um arquivo de dados em tempo real ou em lotes
* Processar os dados
* Transferência de dados
* Visualizar os dados

Neste artigo, vamos ver estas fases no que respeita à Azure Data Lake Store para compreender as opções e as ferramentas disponíveis para satisfazer as necessidades de macrodados.

## <a name="ingest-data-into-data-lake-store"></a>Ingerir dados no Data Lake Store
Esta secção realça as diferentes origens de dados e as diferentes formas em que podem ser ingeridos esses dados para uma conta do Data Lake Store.

![Ingerir dados no Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "ingerir dados no Data Lake Store")

### <a name="ad-hoc-data"></a>Dados ad hoc
Representa os conjuntos de dados menores que são utilizados para fazer o protótipo uma aplicação de macrodados. Existem várias formas de dados ad hoc ingestão relacionadas, consoante a origem dos dados.

| Origem de Dados | -La através de inserção |
| --- | --- |
| Computador local |<ul> <li>[Portal do Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[CLI do Azure de várias plataformas 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[Utilizando as ferramentas do Data Lake para Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Blob de armazenamento do Azure |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp em execução no cluster do HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dados de transmissão em fluxo
Representa os dados que podem ser gerados por várias origens, como aplicações, dispositivos, sensores, etc. Estes dados podem ser ingeridos num Data Lake Store pelas ferramentas de várias. Estas ferramentas normalmente capturar e processar os dados numa base em eventos por eventos em tempo real e, em seguida, escrever os eventos em lotes no Data Lake Store para que estes podem ser processados.

Seguem-se as ferramentas que pode utilizar:

* [O Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -eventos ingeridos para os Event Hubs podem ser escritos para o Azure Data Lake utilizando uma saída de Azure Data Lake Store.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) -pode escrever dados diretamente para o Data Lake Store a partir do cluster do Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – pode receber eventos de Event Hubs e, em seguida, gravá-lo para o Data Lake Store utilizando o [SDK .NET do Data Lake Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dados relacionais
Também pode originar dados a partir de bases de dados relacionais. Durante um período de tempo, bases de dados relacionais recolhem quantidades enormes de dados que podem fornecer informações de chaves se processados através de um pipeline de macrodados. Pode utilizar as ferramentas seguintes para mover esses dados para o Data Lake Store.

* [O Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de registo do servidor Web (carregamento utilizando aplicações personalizadas)
Este tipo de conjunto de dados é designado sobretudo porque uma análise de dados de registo do servidor web é um caso de utilização comuns para aplicações de macrodados e necessita de grandes volumes de ficheiros de registo para ser carregado para o Data Lake Store. Pode utilizar qualquer uma das seguintes ferramentas para escrever os seus próprios scripts ou aplicações para carregar esses dados.

* [CLI do Azure de várias plataformas 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [SDK do .NET do Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Para carregar dados de registo do servidor web e também para carregar outros tipos de dados (por exemplo, dados de redes sociais sentiments), é uma boa abordagem para escrever as seus próprios scripts/aplicações personalizadas porque dá-lhe a flexibilidade para incluir os dados ao carregar o componente como parte da sua aplicação de macrodados maior. Em alguns casos, este código poderá demorar a forma de um script ou o utilitário de linha de comandos simples. Noutros casos, o código pode ser utilizado para integrar o processamento de dados de grande uma aplicação empresarial ou uma solução.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados a clusters do HDInsight do Azure
A maioria dos tipos de cluster de HDInsight (Hadoop, HBase, Storm) suportam Data Lake Store, como um repositório de armazenamento de dados. Clusters do HDInsight aceder aos dados de Blobs de armazenamento do Azure (WASB). Para um melhor desempenho, pode copiar os dados de WASB para uma conta do Data Lake Store associada com o cluster. Pode utilizar as ferramentas seguintes para copiar os dados.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Serviço de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Os dados armazenados no local ou IaaS Hadoop clusters
Grandes quantidades de dados podem ser armazenadas num existentes clusters do Hadoop, localmente em máquinas com HDFS. Os clusters do Hadoop podem estar numa implementação no local ou poderão estar dentro de um cluster de IaaS no Azure. Pode haver requisitos para copiar esses dados para o Azure Data Lake Store para uma abordagem pontuais ou de forma periódica. Existem várias opções que pode utilizar para conseguir isto. Segue-se uma lista de alternativas e os compromissos associados.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Utilização do Azure Data Factory (ADF) para copiar dados diretamente a partir de clusters do Hadoop para o Azure Data Lake Store |[ADF suporta HDFS como uma origem de dados](../data-factory/connector-hdfs.md) |ADF fornece suporte de out of box HDFS e primeiro class ponto-a-ponto gestão e monitorização |Requer o Data Management Gateway para ser implementado no local ou no IaaS do cluster |
| Exporte dados do Hadoop, como ficheiros. Em seguida, copie os ficheiros para o Azure Data Lake Store utilizando o mecanismo de adequado. |Pode copiar ficheiros para utilizar o Azure Data Lake Store: <ul><li>[O Azure PowerShell para o Windows SO](data-lake-store-get-started-powershell.md)</li><li>[CLI de plataforma do Azure 2.0 para não - SO Windows](data-lake-store-get-started-cli-2.0.md)</li><li>Aplicação personalizada utilizando qualquer SDK do Data Lake Store</li></ul> |Rápido começar a utilizar. Pode efetuar carregamentos personalizados |Processo de vários passo que envolve várias tecnologias. Gestão e monitorização irão aumentar a ser um desafio ao longo do tempo atribuído a natureza das ferramentas personalizada |
| Utilize o Distcp para copiar dados de Hadoop para o armazenamento do Azure. Em seguida, copie dados do armazenamento do Azure Data Lake Store utilizando o mecanismo de adequado. |Pode copiar dados de armazenamento do Azure Data Lake Store utilizando: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp Apache em execução em clusters do HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Pode utilizar ferramentas open source. |Processo de vários passo que envolve várias tecnologias |

### <a name="really-large-datasets"></a>Realmente grandes conjuntos de dados
Para carregar conjuntos de dados que variam em vários terabytes, utilizando os métodos descritos acima pode, por vezes, ser lenta e dispendioso. Nestes casos, pode utilizar as opções abaixo.

* **Com o ExpressRoute do Azure**. Azure ExpressRoute permite-lhe criar ligações privadas entre os datacenters do Azure e a infraestrutura no local. Isto fornece uma opção fiável para transferência de grandes quantidades de dados. Para obter mais informações, consulte [documentação do Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **Carregar "Offline" de dados**. Se utilizar o Azure ExpressRoute não é exequível por qualquer motivo, pode utilizar [serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md) para enviar unidades de disco rígido com os seus dados para um centro de dados do Azure. Os dados pela primeira vez são carregados para o Blobs Storage do Azure. Em seguida, pode utilizar [do Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) ou [AdlCopy ferramenta](data-lake-store-copy-data-azure-storage-blob.md) para copiar dados de Blobs de armazenamento do Azure para o Data Lake Store.

  > [!NOTE]
  > Ao utilizar o serviço de importação/exportação, os tamanhos de ficheiro nos discos de são enviados para o Centro de dados do Azure não deve ser superior a 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Processar os dados armazenados no Data Lake Store
Depois dos dados estão disponíveis no Data Lake Store pode executar análise em que os dados utilizando as aplicações de macrodados suportados. Atualmente, pode utilizar o Azure HDInsight e o Azure Data Lake Analytics para executar tarefas de análise de dados em dados armazenados no Data Lake Store.

![Analisar dados no Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "analisar os dados no Data Lake Store")

Pode examinar os exemplos seguintes.

* [Criar um cluster do HDInsight com o Data Lake Store, como armazenamento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Transferência de dados do Data Lake Store
Pode também querer transferir ou mover dados do Azure Data Lake Store para cenários tais como:

* Mova dados para outros repositórios a interface com os seus pipelines de processamento de dados existente. Por exemplo, pode querer mover dados do Data Lake Store para a SQL Database do Azure ou SQL Server no local.
* Transferir dados para o seu computador local para o processamento em ambientes de IDE durante a criação da prototypes de aplicação.

![Saída de dados do Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "saída de dados do Data Lake Store")

Nestes casos, pode utilizar qualquer uma das seguintes opções:

* [O Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Também pode utilizar os seguintes métodos para escrever o seu próprio script de aplicação para transferir dados do Data Lake Store.

* [CLI do Azure de várias plataformas 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [SDK do .NET do Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualizar dados no Data Lake Store
Pode utilizar uma combinação de serviços para criar representações visuais dos dados armazenados no Data Lake Store.

![Visualizar dados no Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "Visualize os dados no Data Lake Store")

* Pode iniciar utilizando [Azure Data Factory para mover dados do Data Lake Store para o Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Depois disso, pode [integrar o Power BI com o Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) para criar uma representação visual de dados.

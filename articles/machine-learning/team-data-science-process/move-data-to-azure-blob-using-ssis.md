---
title: Mover dados de ou para armazenamento de Blobs do Azure com conectores SSIS | Microsoft Docs
description: "Mova dados de ou para a utilização de conectores SSIS de Blob Storage do Azure."
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 96a1b5fb-34d1-4b9b-8d99-2bb8289e0398
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 24237173876f2b292141d9373b346721a489bc56
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Mover dados de ou para armazenamento de Blobs do Azure com conectores SSIS
O [SQL Server Integration Services Feature Pack para o Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornece componentes para ligar ao Azure, a transferência de dados entre origens de dados do Azure e no local e o processamento de dados armazenados no Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Assim que os clientes terem movidos dados no local para a nuvem, pode aceder-lhe a partir de qualquer serviço do Azure para tirar total partido do conjunto de tecnologias do Azure. Esta pode ser utilizada, por exemplo, no Azure Machine Learning ou num cluster do HDInsight.

Isto é, geralmente, ser o primeiro passo para a [SQL](sql-walkthrough.md) e [HDInsight](hive-walkthrough.md) instruções.

Para ver um debate canónicos cenários que utilizam SSIS para realizar as necessidades de negócio comuns em cenários de integração de dados de híbridos, consulte [fazê-lo mais com SQL Server Integration Services Feature Pack para o Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogue.

> [!NOTE]
> Para obter uma introdução completa para o armazenamento de Blobs do Azure, consulte [Noções básicas de Blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [serviço Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para efetuar as tarefas descritas neste artigo, tem de ter uma subscrição do Azure e configurar uma conta de armazenamento do Azure. Tem de conhecer a chave de conta e o nome conta de armazenamento do Azure para carregar ou transferir dados.

* Para configurar um **subscrição do Azure**, consulte [um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar um **conta de armazenamento** e para obter a conta e informações da chave, consulte [contas do storage do Azure sobre](../../storage/common/storage-create-storage-account.md).

Para utilizar o **conectores SSIS**, tem de transferir:

* **SQL Server 2014 ou padrão de 2016 (ou superior)**: instalação inclui o SQL Server Integration Services.
* **Microsoft SQL Server 2014 ou o pacote da funcionalidade de serviços de integração de 2016 para o Azure**: estes podem ser transferidos, respetivamente, do [serviços de integração do SQL Server 2014](http://www.microsoft.com/download/details.aspx?id=47366) e [integração do SQL Server 2016 Serviços](https://www.microsoft.com/download/details.aspx?id=49492) páginas.

> [!NOTE]
> SSIS é instalado com o SQL Server, mas não está incluído na versão rápida. Para obter informações sobre as aplicações estão incluídas nas várias edições do SQL Server, consulte [edições do SQL Server](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Para os materiais de formação em SSIS, consulte [às mãos na formação de SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Para obter informações sobre a obtenção de cópia de segurança e execução utilizar SISS para criar Consulte simple de extração, transformação e carregamento (ETL) de pacotes, [SSIS Tutorial: criar um pacote de ETL simples](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Transferir o conjunto de dados NYC Taxi
O exemplo descrito aqui utilizar um conjunto de dados publicamente disponível – o [NYC Taxi viagens](http://www.andresmh.com/nyctaxitrips/) conjunto de dados. O conjunto de dados consiste em cerca de 173 milhões taxi rides no NYC no ano 2013. Existem dois tipos de dados: viagem fornece detalhes sobre dados e fare dados. Como é não existe um ficheiro de cada mês, temos 24 ficheiros em todos os, cada um dos quais é aproximadamente 2GB descomprimido.

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para armazenamento de Blobs do Azure
Para mover dados através de SSIS funcionalidade pacote no local para o armazenamento de Blobs do Azure, utilizamos uma instância do [ **a tarefa de carregar Blob do Azure**](https://msdn.microsoft.com/library/mt146776.aspx), mostrados aqui:

![configurar dados-ciência-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Os parâmetros que utiliza as tarefas são descritos aqui:

| Campo | Descrição |
| --- | --- |
| **AzureStorageConnection** |Especifica um Gestor de ligações de armazenamento do Azure existente ou cria uma nova que se refere a uma conta de armazenamento do Azure que aponta para onde estão alojados os ficheiros de blob. |
| **BlobContainer** |Especifica o nome do contentor do blob que contêm os ficheiros carregados como blobs. |
| **BlobDirectory** |Especifica o diretório de blob armazenar o ficheiro carregado como um blob de bloco. O diretório de blob é uma estrutura hierárquica virtual. Se já existir um blob, it ia substituído. |
| **LocalDirectory** |Especifica o diretório local que contém os ficheiros para ser carregado. |
| **Nome de ficheiro** |Especifica um nome de filtro para selecionar os ficheiros com o padrão de nome especificado. Por exemplo, MySheet\*xls\* inclui ficheiros como MySheet001.xls e MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Especifica um filtro de intervalo de tempo. Ficheiros modificados após *TimeRangeFrom* e antes de *TimeRangeTo* estão incluídos. |

> [!NOTE]
> O **AzureStorageConnection** credenciais têm de ser correta e o **BlobContainer** tem de existir antes da transferência é tentada.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Transferência de dados do blob storage do Azure
Para transferir dados do blob storage do Azure para armazenamento no local com SSIS, utilize uma instância do [a tarefa de carregar Blob do Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Cenários mais avançados de SSIS-Azure
O pacote de funcionalidades SSIS permite fluxos mais complexos para ser processada por tarefas de empacotamento em conjunto. Por exemplo, o blob foi feed de dados diretamente para um cluster do HDInsight, cuja saída pode ser transferida para um blob e, em seguida, para o armazenamento no local. SSIS pode executar tarefas do Hive e do Pig num cluster do HDInsight utilizando conectores SSIS adicionais:

* Para executar um script de ramo de registo num cluster do Azure HDInsight com SSIS, utilize [tarefas de ramo de registo do Azure HDInsight](https://msdn.microsoft.com/library/mt146771.aspx).
* Para executar um script de Pig num cluster do Azure HDInsight com SSIS, utilize [Azure HDInsight Pig tarefas](https://msdn.microsoft.com/library/mt146781.aspx).


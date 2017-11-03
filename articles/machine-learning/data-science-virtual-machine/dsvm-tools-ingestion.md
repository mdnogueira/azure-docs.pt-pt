---
title: "Ferramentas de ingestão de dados de Máquina Virtual de ciência dados - Azure | Microsoft Docs"
description: "Ferramentas de ingestão de dados de Máquina Virtual de ciência dados"
keywords: "ferramentas de ciência de dados, a máquina de virtual de ciência de dados, as ferramentas de ciência de dados, ciência de dados do linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 564c06c5017a77431b7d6fed7b43c47141b12252
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados de Máquina Virtual de ciência dados

Um dos passos primeiro technical num projeto AI ou ciência de dados é identificar os conjuntos de dados a ser utilizada e colocá-los para o seu ambiente de análise. A Máquina Virtual de ciência de dados (DSVM) fornece ferramentas e bibliotecas para colocar em dados de diferentes origens para um armazenamento de dados analíticos localmente o DSVM ou numa plataforma de dados na nuvem ou no local. 

Seguem-se algumas ferramentas de movimento de dados que fornecemos-na DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados de blobs de armazenamento do Azure para o Azure Data Lake Store. -Também pode copiar dados entre duas contas de Azure Data Lake Store.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações comuns      | Importar vários blobs storage do Azure para o Azure Data Lake Store.      |
|  Como utilizar / executá-lo?    |   Abra uma linha de comandos, em seguida, escreva `adlcopy` para obter ajuda.    |
| Ligações para amostras      | Https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob [utilizando AdlCopy])      |
| Ferramentas relacionadas no DSVM      | AzCopy, linha de comandos do Azure     |

## <a name="azure-command-line"></a>Linha de comandos do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta de gestão do Azure. Também contém verbos de comando para mover dados de plataformas de dados do Azure, como o blobs storage do Azure, do armazenamento do Azure Data Lake     |
| Versões suportadas DSVM      | Windows, Linux     |
| Utilizações comuns      | Importar, exportar dados para e do armazenamento do Azure, o Azure Data Lake Store      |
|  Como utilizar / executá-lo?    |   Abra uma linha de comandos, em seguida, escreva `az` para obter ajuda.    |
| Ligações para amostras      | [Utilizar a CLI do Azure](https://docs.microsoft.com/cli/azure/?viee-cli-latest)     |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados e de ficheiros locais, blobs de armazenamento do Azure, ficheiros e tabelas.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações comuns      | Copiar ficheiros para o blob storage, copiar os blobs entre contas.      |
|  Como utilizar / executá-lo?    |   Abra uma linha de comandos, em seguida, escreva `azcopy` para obter ajuda.    |
| Ligações para amostras      | [AzCopy no Windows](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy)      |
| Ferramentas relacionadas no DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-documentdb-api-data-migration-tool"></a>Do Azure Cosmos DB: Ferramenta de migração de dados do DocumentDB API

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta para importar dados a partir de várias origens, incluindo ficheiros JSON, ficheiros CSV, SQL Server, MongoDB, armazenamento tabelas do Azure, Amazon DynamoDB e API de DocumentDB do Azure Cosmos DB coleções na base de dados do Azure Cosmos ou do Azure DocumentDB.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações comuns      | Importar ficheiros de uma VM para CosmosDB, importar dados do table storage do Azure para CosmosDB ou importar dados a partir de uma base de dados do SQL Server para CosmosDB.     |
|  Como utilizar / executá-lo?    |   Para utilizar a linha de comandos versão, abra uma linha de comandos, em seguida, escreva `dt`. Para utilizar a ferramenta de GUI, abra uma linha de comandos, em seguida, escreva `dtui`.    |
| Ligações para amostras      | [CosmosDB importar dados](https://docs.microsoft.com/en-us/azure/cosmos-db/import-data)      |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta de SQL Server para copiar dados entre o SQL Server e um ficheiro de dados.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações comuns      | Importar um ficheiro CSV para uma tabela do SQL Server, exportar uma tabela do SQL Server para um ficheiro.      |
|  Como utilizar / executá-lo?    |   Abra uma linha de comandos, em seguida, escreva `bcp` para obter ajuda.    |
| Ligações para amostras      | [Utilitário de cópia em massa](https://docs.microsoft.com/en-us/sql/tools/bcp-utility)      |
| Ferramentas relacionadas no DSVM      | SQL Server, sqlcmd      |


## <a name="microsoft-data-management-gateway"></a>Microsoft Data Management Gateway

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para estabelecer a ligação no local origens de dados para serviços de consumo em nuvem.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações comuns      | A ligar uma VM a uma origem de dados no local.      |
|  Como utilizar / executá-lo?    |   Inicie "Microsoft Data Management Gateway" no Menu Iniciar.    |
| Ligações para amostras      | [Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy, bcp    |

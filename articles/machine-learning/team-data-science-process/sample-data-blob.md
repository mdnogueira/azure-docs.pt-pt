---
title: Armazenamento de BLOBs de dados de exemplo no Azure | Microsoft Docs
description: Dados de exemplo no Blob Storage do Azure
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: 00444853cc7b0f8d8ea4dfa1e6cddad4479daec0
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="heading"></a>Armazenamento de BLOBs de dados de exemplo no Azure
Este documento inclui dados de amostragem armazenados no armazenamento de Blobs do Azure, transferindo-a através de programação e, em seguida, fazendo a amostragem-la através de procedimentos escritos no Python.

O seguinte **menu** ligações para tópicos que descrevem como dados de vários ambientes de armazenamento de exemplo. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Os dados de exemplo por que motivo?**
Se o conjunto de dados que pretende analisar for grande, normalmente, é uma boa ideia baixo-sample os dados para reduzir para um tamanho mais pequeno, mas representativo e mais fácil gerir. Isto facilita a compreensão de dados, exploração e engenharia da funcionalidade. A função no processo de análise do Cortana é permitir o rápido fazer o protótipo das funções de processamento de dados e modelos de machine learning.

Esta tarefa de amostragem é um passo de [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Transferir e dados de exemplo para baixo
1. Transferir os dados a partir do blob storage do Azure utilizando o serviço blob do código de Python do exemplo seguinte: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Ler dados para um intervalo de dados Pandas do ficheiro transferido acima.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Os dados através de exemplo de baixo o `numpy`do `random.choice` da seguinte forma:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Agora, pode trabalhar com o intervalo de dados acima com o exemplo de 1 por cento mais de exploração e geração de funcionalidade.

## <a name="heading"></a>Carregar dados e lê-lo no Azure Machine Learning
Pode utilizar o seguinte código de exemplo para os dados de exemplo para baixo e utilizá-lo diretamente no Azure Machine Learning:

1. Escrever o intervalo de dados para um ficheiro local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregar o ficheiro local para um blob do Azure utilizando o seguinte código de exemplo:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Ler os dados de Blobs do Azure utilizando o Azure Machine Learning [importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) conforme mostrado na imagem abaixo:

![blob de leitor](./media/sample-data-blob/reader_blob.png)


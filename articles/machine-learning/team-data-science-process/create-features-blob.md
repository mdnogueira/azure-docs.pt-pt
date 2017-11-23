---
title: Criar as funcionalidades para dados de armazenamento de Blobs do Azure utilizando Panda | Microsoft Docs
description: Como criar funcionalidades para os dados armazenados no contentor de blob do Azure com o pacote do Panda Python.
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev;garye
ms.openlocfilehash: 7a2e64927f4afca87642fb4829166c5ec60dbc09
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Criar funcionalidades para dados do Armazenamento de Blobs do Azure com o Pandas
Este documento mostra como criar funcionalidades para os dados armazenados no blob do Azure contentor utilizando o [Pandas](http://pandas.pydata.org/) pacote do Python. Após definido que estipule como carregar dados para um intervalo de dados Panda, mostra como gerar categórico funcionalidades com scripts do Python com valores de indicador e funcionalidades de discretização.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Isto **menu** ligações para tópicos que descrevem como criar funcionalidades para os dados em vários ambientes. Esta tarefa é um passo de [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já criou uma conta de armazenamento de Blobs do Azure e tem armazenados os dados não existe. Se precisar de instruções para configurar uma conta, consulte [criar uma conta de armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)

## <a name="load-the-data-into-a-pandas-data-frame"></a>Carregar os dados para um intervalo de dados Pandas
Para explorar e manipular um conjunto de dados transferidos-lo da origem do blob para um ficheiro local. Em seguida, carregue-o para um intervalo de dados Pandas. Eis os passos a seguir para este procedimento:

1. Transferência de dados do Azure blob com o seguinte código de Python de exemplo utilizando o serviço blob. Substitua os valores específicos a variável no seguinte código:
   
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
2. Ler os dados para um intervalo de dados Pandas do ficheiro transferido.
   
        #LOCALFILE is the file path
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Agora, está pronto para explorar os dados e gerar funcionalidades neste conjunto de dados.

## <a name="blob-featuregen"></a>Geração de funcionalidade
As duas secções seguintes explicam como gerar categórico com valores de indicador discretização e das funcionalidades e com scripts do Python.

### <a name="blob-countfeature"></a>Indicador baseado no valor funcionalidade geração
Funcionalidades categórico podem ser criadas da seguinte forma:

1. Inspecione a distribuição da coluna categórico:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Gerar valores de indicador para cada um dos valores de coluna
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Associar a coluna de indicador com o intervalo de dados original
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Remova a variável original próprio:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>A discretização geração de funcionalidade
Para gerar funcionalidades binned, avance da seguinte forma:

1. Adicionar uma sequência de colunas a bin uma coluna numérica
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Converter a discretização a uma sequência de variáveis booleanos
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Por fim, associar as variáveis fictícias novamente para o intervalo de dados original
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Escrever dados blob do Azure para consumi-lo no Azure Machine Learning
Para consumir dados no Azure Machine Learning tem explorou, amostragem ou featurized, carregar os dados para um blob do Azure. Funcionalidades adicionais podem ser criadas no Azure Machine Learning Studio bem. Os passos seguintes mostram como carregar os dados:

1. Escrever o intervalo de dados no ficheiro local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Carregar os dados ao blob do Azure da seguinte forma:
   
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
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Agora, os dados podem ser lidos a partir do blob a utilizar o Azure Machine Learning [importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) módulo, conforme mostrado na captura de ecrã seguinte:

![blob de leitor](./media/data-blob/reader_blob.png)


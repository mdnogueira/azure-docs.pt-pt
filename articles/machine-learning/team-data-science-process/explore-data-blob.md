---
title: Explore os dados no blob storage do Azure com Pandas | Microsoft Docs
description: Como explorar os dados armazenados no contentor de blob do Azure utilizando Pandas.
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: a46735dde28740087d201d7490f135349aad76f6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorar dados no Armazenamento de Blobs do Azure com o Pandas
Este documento abrange como explorar os dados armazenados no blob do Azure utilizando o contentor [Pandas](http://pandas.pydata.org/) pacote do Python.

O seguinte **menu** ligações para tópicos que descrevem como utilizar as ferramentas para explorar dados vários ambientes de armazenamento. Esta tarefa é um passo de [o processo de ciência de dados]().

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Armazenados os dados numa conta de armazenamento de Blobs do Azure. Se precisar de instruções, consulte [mover dados para e do armazenamento do Azure](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Carregar os dados para um DataFrame Pandas
Para explorar e manipular um conjunto de dados, esta tem primeiro de ser transferida da origem do blob para um ficheiro local, o que, em seguida, pode ser carregado num DataFrame Pandas. Eis os passos a seguir para este procedimento:

1. Transferência de dados do Azure blob com o seguinte exemplo de código de Python com o serviço blob. Substitua os valores específicos a variável no seguinte código: 
   
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

## <a name="blob-dataexploration"></a>Exemplos de exploração de dados utilizando Pandas
Seguem-se alguns exemplos de formas para explorar dados Pandas a utilizar:

1. Inspecione o **número de linhas e colunas** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Inspecione** as algumas primeira ou últimas **linhas** no conjunto de dados seguinte:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Verifique o **tipo de dados** cada coluna foi importada como utilizando o seguinte código de exemplo
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Verifique o **estatísticas básicas** para as colunas de dados definidas de forma
   
        dataframe_blobdata.describe()
5. Observe o número de entradas para cada valor de coluna da seguinte forma
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Contagem de valores em falta** versus o número real de entradas em cada coluna utilizando o seguinte código de exemplo
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Se tiver **valores em falta** para uma coluna específica nos dados, pode colocá-los da seguinte forma:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Outra forma de substituir os valores em falta é com a função de modo:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< nome_coluna >: .mode()[0]}) dataframe_blobdata ['< nome_coluna >']        
8. Criar um **histograma** desenhar utilizando variável número de intervalos binários para desenhar a distribuição de uma variável    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Observe **correlações** entre as variáveis a utilizar um scatterplot ou utilizando a função incorporada de correlação
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()


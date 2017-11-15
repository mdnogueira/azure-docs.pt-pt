---
title: "Processar os dados de Blobs do Azure com análises avançadas | Microsoft Docs"
description: Processamento de dados no Blob storage do Azure.
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8a59078-91d3-4440-b85c-430363c3f4d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: a31b77ca2d315b506c925a83a5050c0e70c54842
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="heading"></a>Processar os dados de Blobs do Azure com a análise avançada
Este documento abrange a explorar os dados e funcionalidades de geração de dados armazenados no Blob storage do Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Carregar os dados para um intervalo de dados Pandas
Para explorar e manipular um conjunto de dados, este tem de ser transferido a partir da origem do blob para um ficheiro local que, em seguida, pode ser carregado num intervalo de dados Pandas. Eis os passos a seguir para este procedimento:

1. Transferência de dados do Azure blob com o seguinte código de Python de exemplo utilizando o serviço blob. Substitua os valores específicos a variável com o código abaixo: 
   
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

## <a name="blob-dataexploration"></a>Exploração de dados
Seguem-se alguns exemplos de formas para explorar dados Pandas a utilizar:

1. Inspecione o número de linhas e colunas 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Inspecione as primeira ou últimas alguns as linhas no conjunto de dados, tal como indicado abaixo:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Verifique o tipo de dados que cada coluna foi importada como utilizando o seguinte código de exemplo
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Verifique as estatísticas básicas para as colunas no conjunto de dados de forma
   
        dataframe_blobdata.describe()
5. Observe o número de entradas para cada valor de coluna da seguinte forma
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Contagem de valores em falta em comparação com o número real de entradas em cada coluna utilizando o seguinte código de exemplo
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Se tiver os valores em falta para uma coluna específico nos dados, pode removê-los da seguinte forma:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Outra forma de substituir os valores em falta é com a função de modo:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< nome_coluna >: .mode()[0]}) dataframe_blobdata ['< nome_coluna >']        
8. Criar um desenho de histograma utilizando variável número de intervalos binários para desenhar a distribuição de uma variável    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Observe correlações entre as variáveis a utilizar um scatterplot ou utilizando a função incorporada de correlação
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Geração de funcionalidade
Iremos pode gerar funcionalidades com o Python da seguinte forma:

### <a name="blob-countfeature"></a>Valor do indicador baseado geração de funcionalidade
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
Para gerar binned funcionalidades, iremos continuar da seguinte forma:

1. Adicionar uma sequência de colunas a bin uma coluna numérica
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Converter a discretização a uma sequência de variáveis booleanos
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Por fim, associar as variáveis fictícias novamente para o intervalo de dados original
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Escrever dados blob do Azure e consumir no Azure Machine Learning
Depois de ter explorou os dados e criar as funcionalidades necessárias, pode carregar os dados (amostragem ou featurized) para um Azure blob e consumi-lo no Azure Machine Learning utilizando os seguintes passos: tenha em atenção que as funcionalidades adicionais podem ser criadas na máquina do Azure Learning Studio bem. 

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
3. Agora, os dados podem ser lidos a partir do blob a utilizar o Azure Machine Learning [importar dados] [ import-data] módulo, conforme mostrado no ecrã abaixo:

![blob de leitor][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/


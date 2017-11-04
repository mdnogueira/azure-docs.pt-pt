---
title: Fluxo de dados a partir do Stream Analytics para o Data Lake Store | Microsoft Docs
description: "Utiliza o Azure Stream Analytics para dados de sequência no Azure Data Lake Store"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/01/2017
ms.author: nitinme
ms.openlocfilehash: 35b737cf5b53f0ad0dbe4a50772fdcaa2e14ca5e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Stream data from Azure Storage Blob into Data Lake Store using Azure Stream Analytics (Transmitir dados do Blob de Armazenamento do Azure para o Data Lake Store com o Azure Stream Analytic)
Neste artigo, ficará a saber como utilizar o Azure Data Lake Store como resultado de uma tarefa do Azure Stream Analytics. Este artigo demonstra um cenário simples que lê dados de um blob de armazenamento do Azure (entrada) e escreve os dados ao Data Lake Store (saída).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta de Armazenamento do Azure**. Irá utilizar um contentor de BLOBs a partir desta conta para introduzir dados para uma tarefa de Stream Analytics. Para este tutorial partem do princípio de que tem uma conta de armazenamento denominada **storageforasa** e um contentor na conta denominada **storageforasacontainer**. Assim que tiver criado o contentor, carregar um ficheiro de dados de exemplo para a mesma. 
  
* **Conta do Azure Data Lake Store**. Siga as instruções em [Introdução ao Azure Data Lake Store com o Portal do Azure](data-lake-store-get-started-portal.md). Vamos assumir que tem uma conta de Data Lake Store chamada **asadatalakestore**. 

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics
Começar através da criação de uma tarefa de Stream Analytics que inclui uma origem de entrada e um destino de saída. Para este tutorial, a origem é um contentor de Blobs do Azure e o destino é o Data Lake Store.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **tarefas do Stream Analytics**e, em seguida, clique em **adicionar**.

    ![Criar uma tarefa do Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "criar uma tarefa de Stream Analytics")

    > [!NOTE]
    > Certifique-se criar a tarefa na mesma região que a conta de armazenamento ou irá implicar custos adicionais de mover dados entre regiões.
    >

## <a name="create-a-blob-input-for-the-job"></a>Criar um Blob de entrada para a tarefa

1. Clique do abrir a página para a tarefa de Stream Analytics, no painel esquerdo do **entradas** separador e, em seguida, clique em **adicionar**.

    ![Adicionar uma entrada para a tarefa](./media/data-lake-store-stream-analytics/create.input.1.png "adicionar uma entrada para a sua tarefa")

2. No **nova entrada** painel, forneça os valores seguintes.

    ![Adicionar uma entrada para a tarefa](./media/data-lake-store-stream-analytics/create.input.2.png "adicionar uma entrada para a sua tarefa")

    * Para **alias de entrada**, introduza um nome exclusivo para a tarefa de entrada.
    * Para **tipo de origem**, selecione **fluxo de dados**.
    * Para **origem**, selecione **armazenamento de BLOBs**.
    * Para **subscrição**, selecione **armazenamento de BLOBs de utilização da subscrição atual**.
    * Para **conta de armazenamento**, selecione a conta de armazenamento que criou como parte dos pré-requisitos. 
    * Para **contentor**, selecione o contentor que criou na conta de armazenamento selecionada.
    * Para **formato de serialização de eventos**, selecione **CSV**.
    * Para **delimitador**, selecione **separador**.
    * Para **codificação**, selecione **UTF-8**.

    Clique em **Criar**. Agora, o portal adiciona a entrada e testa a ligação ao mesmo.


## <a name="create-a-data-lake-store-output-for-the-job"></a>Criar uma saída de Data Lake Store para a tarefa

1. Clique de página para a tarefa de Stream Analytics, abra o **saídas** separador e, em seguida, clique em **adicionar**.

    ![Adicionar uma saída ao seu trabalho](./media/data-lake-store-stream-analytics/create.output.1.png "adicionar uma saída à sua tarefa")

2. No **nova saída** painel, forneça os valores seguintes.

    ![Adicionar uma saída ao seu trabalho](./media/data-lake-store-stream-analytics/create.output.2.png "adicionar uma saída à sua tarefa")

    * Para **alias de saída**, introduza um um nome exclusivo para o resultado da tarefa. Este é um nome amigável utilizado nas consultas para direcionar o resultado de consulta para este arquivo Data Lake.
    * Para **Sink**, selecione **Data Lake Store**.
    * Será solicitado para autorizar o acesso à conta do Data Lake Store. Clique em **autorizar**.

3. No **nova saída** painel, continuar a fornecer os seguintes valores.

    ![Adicionar uma saída ao seu trabalho](./media/data-lake-store-stream-analytics/create.output.3.png "adicionar uma saída à sua tarefa")

    * Para **nome da conta**, selecione de que já criou onde pretende que a tarefa de saída sejam enviados para a conta de Data Lake Store.
    * Para **padrão de prefixo do caminho**, introduza um caminho de ficheiro utilizado para escrever ficheiros dentro da conta de Data Lake Store especificado.
    * Para **formato de data**, se utilizou um token de data no caminho de prefixo, pode selecionar o formato da data em que os ficheiros estão organizados.
    * Para **formato de hora**, se utilizou um token de tempo no caminho do prefixo, especifique o formato de hora em que os ficheiros estão organizados.
    * Para **formato de serialização de eventos**, selecione **CSV**.
    * Para **delimitador**, selecione **separador**.
    * Para **codificação**, selecione **UTF-8**.
    
    Clique em **Criar**. Agora, o portal adiciona a saída e testa a ligação ao mesmo.
    
## <a name="run-the-stream-analytics-job"></a>Executar a tarefa de Stream Analytics

1. Para executar uma tarefa de Stream Analytics, tem de executar uma consulta do **consulta** separador. Para este tutorial, pode executar a consulta de exemplo, substituindo os marcadores de posição pela tarefa de entrada e saída aliases, conforme mostrado na captura de ecrã abaixo.

    ![Executar consulta](./media/data-lake-store-stream-analytics/run.query.png "executar consulta")

2. Clique em **guardar** da parte superior do ecrã e, em seguida, o **descrição geral** separador, clique em **iniciar**. Na caixa de diálogo, selecione **personalizada tempo**e, em seguida, defina a data e hora atuais.

    ![Definir a hora de tarefa](./media/data-lake-store-stream-analytics/run.query.2.png "definir a hora de tarefa")

    Clique em **iniciar** para iniciar a tarefa. -Pode demorar alguns minutos para iniciar a tarefa.

3. Para acionar a tarefa para escolher os dados de blob, copie um ficheiro de dados de exemplo para o contentor de blob. Pode obter um ficheiro de dados de exemplo do [repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Para este tutorial, vamos copie o ficheiro **vehicle1_09142014.csv**. Pode utilizar vários clientes, tais como [Explorador de armazenamento do Azure](http://storageexplorer.com/), carregar dados para um contentor do blob.

4. Do **descrição geral** separador em **monitorização**, consulte a forma como os dados foi processados.

    ![Tarefa de monitorização](./media/data-lake-store-stream-analytics/run.query.3.png "tarefa de monitorização")

5. Por fim, pode verificar que os dados de saída da tarefa estão disponíveis na conta do Data Lake Store. 

    ![Certifique-se a saída](./media/data-lake-store-stream-analytics/run.query.4.png "Certifique-se de saída")

    No painel do Explorador de dados, tenha em atenção que o resultado é escrito para um caminho de pasta conforme especificado no Data Lake Store saída definições (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Consultar também
* [Criar um cluster do HDInsight ao utilizar o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

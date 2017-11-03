---
title: "Integração do Azure Stream Analytics e Machine Learning | Microsoft Docs"
description: "Como utilizar uma função definida pelo utilizador e o Machine Learning uma tarefa de Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/06/2017
ms.author: samacha
ms.openlocfilehash: 243ee799d2cddb1baf5b8046eee6eaf182463d2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Efetuar análise de dados de sentimento utilizando o Azure Stream Analytics e o Azure Machine Learning
Este artigo descreve como configurar rapidamente a uma tarefa de Stream Analytics do Azure simples que integra o Azure Machine Learning. Utilizar um modelo de análise de dados de sentimento do Machine Learning a partir da galeria da Cortana Intelligence para analisar dados de texto de transmissão em fluxo e determinar a classificação de dados de sentimento em tempo real. Utilizar o Cortana Intelligence Suite permite-lhe realizar esta tarefa sem se preocupar intricacies da criação de um modelo de análise de dados de sentimento.

Pode aplicar o que aprender do artigo, para estes cenários:

* A analisar o sentimento em tempo real no Twitter dados de transmissão em fluxo.
* Analisar registos de cliente chats com a equipa de suporte.
* Comentários nos fóruns, blogues e vídeos de avaliação. 
* Muitos outros em tempo real, preditivos classificação cenários.

Um cenário do mundo real, teria de obter os dados diretamente a partir de um fluxo de dados do Twitter. Para simplificar o tutorial, iremos escritos-la para que a tarefa de análise de transmissão em fluxo obtém tweets de um ficheiro CSV no Blob storage do Azure. Pode criar o seu próprio ficheiro CSV, ou pode utilizar um ficheiro CSV de exemplo, conforme mostrado na imagem seguinte:

![exemplo de tweets num ficheiro CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

A tarefa de análise de transmissão em fluxo que criar aplica-se o modelo de análise de dados de sentimento como uma função definida pelo utilizador (UDF) nos dados de texto de exemplo do arquivo de blob. A saída (o resultado da análise de dados de sentimento) é escrita para o mesmo arquivo de blob num ficheiro CSV diferente. 

A figura seguinte demonstra esta configuração. Conforme indicado, num cenário mais realistas, pode substituir o armazenamento de Blobs com transmissão em fluxo de dados do Twitter de uma entrada de Event Hubs do Azure. Além disso, foi possível criar um [Microsoft Power BI](https://powerbi.microsoft.com/) visualização em tempo real de dados de sentimento agregado.    

![Descrição geral de integração do Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem o seguinte:

* Uma subscrição ativa do Azure.
* Um ficheiro CSV com alguns dados na mesma. Pode transferir o ficheiro apresentado anteriormente da [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), ou pode criar os seus próprios ficheiros. Para este artigo, partimos do pressuposto que está a utilizar o ficheiro a partir do GitHub.

Um nível elevado, para concluir as tarefas demonstradas neste artigo, efetue o seguinte:

1. Criar uma conta de armazenamento do Azure e um contentor de blob storage e carregar um ficheiro de entrada CSV formatado para o contentor.
3. Adicione um modelo de análise de dados de sentimento da galeria da Cortana Intelligence à sua área de trabalho do Azure Machine Learning e implementar este modelo como um serviço web na área de trabalho de Machine Learning.
5. Crie uma tarefa de Stream Analytics que chama este serviço web como uma função para determinar o sentimento para o texto de entrada.
6. Iniciar a tarefa de Stream Analytics e verificar o resultado.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Criar um contentor de armazenamento e carregar o ficheiro de entrada de CSV
Para este passo, pode utilizar qualquer ficheiro CSV, tal como o disponíveis a partir do GitHub.

1. No portal do Azure, clique em **novo** &gt; **armazenamento** &gt; **conta de armazenamento**.

   ![Criar nova conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-create-storage-account.png)

2. Forneça um nome (`samldemo` no exemplo). O nome pode utilizar apenas letras minúsculas e números, e tem de ser exclusivo em todo o Azure. 

3. Especifique um grupo de recursos existente e especifique uma localização. Para a localização, recomendamos que todos os recursos que criou neste tutorial, utilize a mesma localização.

    ![forneça detalhes da conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. No portal do Azure, selecione a conta de armazenamento. No painel de conta de armazenamento, clique em **contentores** e, em seguida, clique em  **+ &nbsp;contentor** criar armazenamento de Blobs.

    ![Criar contentor de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Forneça um nome para o contentor (`azuresamldemoblob` no exemplo) e certifique-se de que **aceder tipo** está definido como **Blob**. Quando tiver terminado, clique em **OK**.

    ![Especifique os detalhes do contentor de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. No **contentores** painel, selecione o novo contentor, que abre o painel para esse contentor.

7. Clique em **Carregar**.

    ![Botão 'Carregar' para um contentor](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. No **carregar blob** painel, especifique o ficheiro CSV que pretende utilizar para este tutorial. Para **Blob tipo**, selecione **BLOBs de blocos** e defina o tamanho do bloco para 4 MB, que é suficiente para este tutorial.

    ![carregar ficheiro de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

9. Clique em de **carregar** na parte inferior do painel.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicionar o modelo de análise de dados de sentimento da galeria da Cortana Intelligence

Agora que os dados de exemplo num blob, pode ativar o modelo de análise de dados de sentimento na galeria da Cortana Intelligence.

1. Vá para o [modelo de Análise Preditiva sentimento](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) página na galeria da Cortana Intelligence.  

2. Clique em **abrir no Studio**.  
   
   ![Stream Analytics Machine Learning, abra o Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. A iniciar sessão Ir para a área de trabalho. Selecione uma localização.

4. Clique em **executar** na parte inferior da página. O processo é executado, que demora um minuto.

   ![Execute experimentação no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Depois do processo foi executada com êxito, selecione **implementar serviço Web** na parte inferior da página.

   ![implementar experimentação no Machine Learning Studio como um serviço web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para validar que o modelo de análise de dados de sentimento está pronto a utilizar, clique em de **teste** botão. Forneça o texto de entrada, tais como "Posso adoram Microsoft". 

   ![teste de experimentação no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Se o teste funciona, verá um resultado semelhante ao seguinte exemplo:

   ![resultados do teste no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. No **aplicações** coluna, clique em de **Excel 2010 ou anterior livro** hiperligação para transferir um livro do Excel. O livro contém a chave de API e o URL que precisar mais tarde para configurar a tarefa de Stream Analytics.

    ![Stream Analytics Machine Learning, leitura rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Criar uma tarefa de Stream Analytics utiliza o modelo de Machine Learning

Agora, pode criar uma tarefa de Stream Analytics que lê os tweets de exemplo do ficheiro CSV no blob storage. 

### <a name="create-the-job"></a>Criar a tarefa

1. Aceda ao [Portal do Azure](https://portal.azure.com).  

2. Clique em **novo** > **Internet das coisas** > **tarefa do Stream Analytics**. 

   ![Caminho do portal do Azure para obter a uma nova tarefa de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-new-iot-sa-job.png)
   
3. A tarefa de nome `azure-sa-ml-demo`, especifique uma subscrição, especifique um grupo de recursos existente ou crie um novo e selecionar a localização para a tarefa.

   ![Especifique as definições para a nova tarefa de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Configurar a entrada da tarefa
A tarefa obtém a entrada do ficheiro CSV que carregou anteriormente para o blob storage.

1. Depois da tarefa foi criada, em **tarefa topologia** no painel de tarefas, clique o **entradas** caixa.  
   
   ![Caixa de 'Entradas' no painel de tarefas do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

2. No **entradas** painel, clique em **+ adicionar**.

   ![Botão "Adicionar" para adicionar uma entrada para a tarefa de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-button.png)  

3. Preencha o **nova entrada** painel com estes valores:

    * **Alias de entrada**: Utilize o nome `datainput`.
    * **Tipo de origem**: selecione **fluxo de dados**.
    * **Origem**: selecione **armazenamento de BLOBs**.
    * **Opção de importar**: selecione **armazenamento de BLOBs de utilização da subscrição atual**. 
    * **Conta de armazenamento**. Selecione a conta de armazenamento que criou anteriormente.
    * **Contentor**. Selecione o contentor que criou anteriormente (`azuresamldemoblob`).
    * **Formato de serialização de eventos**. Selecione **CSV**.

    ![Definições para a nova entrada de tarefa](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Clique em **Criar**.

### <a name="configure-the-job-output"></a>Configurar o resultado da tarefa
A tarefa envia resultados para o mesmo armazenamento de BLOBs onde obtém entrada. 

1. Em **tarefa topologia** no painel de tarefas, clique o **saídas** caixa.  
  
   ![Criar nova saída da tarefa de análise de transmissão em fluxo](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

2. No **saídas** painel, clique em **+ adicionar**e, em seguida, adicione uma saída com o alias `datamloutput`. 

3. Para **Sink**, selecione **armazenamento de BLOBs**. Em seguida, preencha o resto das definições de saída utilizando os mesmos valores que utilizou para o armazenamento de BLOBs para a entrada:

    * **Conta de armazenamento**. Selecione a conta de armazenamento que criou anteriormente.
    * **Contentor**. Selecione o contentor que criou anteriormente (`azuresamldemoblob`).
    * **Formato de serialização de eventos**. Selecione **CSV**.

   ![Definições do novo resultado da tarefa](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Clique em **Criar**.   


### <a name="add-the-machine-learning-function"></a>Adicione a função de Machine Learning 
Anteriormente publicado um modelo de Machine Learning para um serviço web. No nosso cenário, quando é executada a tarefa de análise de fluxo, envia cada tweet de exemplo de entrada para o serviço web para análise de dados de sentimento. O serviço web do Machine Learning devolve um sentimento (`positive`, `neutral`, ou `negative`) e uma probabilidade de tweet a ser positivo. 

Nesta secção do tutorial, é possível definir uma função na tarefa de análise de fluxo. A função pode ser invocada para enviar um tweet para o serviço web e voltar a resposta. 

1. Certifique-se de que a chave de API e o URL de serviço de web que transferiu anteriormente no livro do Excel.

2. Regresse ao painel de descrição geral de tarefa.

3. Em **definições**, selecione **funções** e, em seguida, clique em **+ adicionar**.

   ![Adicionar uma função para a tarefa de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-function1.png) 

4. Introduza `sentiment` como o alias da função e o preenchimento terminar o resto do painel utilizando estes valores:

    * **Tipo de função**: selecione **do Azure ML**.
    * **Opção de importar**: selecione **importação de uma subscrição diferente**. Isto dá-lhe a possibilidade de introduzir o URL e a chave.
    * **URL**: cole o URL do serviço web.
    * **Chave**: cole a chave de API.
  
    ![Definições para adicionar uma função de Machine Learning para a tarefa de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
5. Clique em **Criar**.

### <a name="create-a-query-to-transform-the-data"></a>Criar uma consulta para transformar os dados

Do Stream Analytics utiliza uma consulta declarativa, baseado em SQL para examinar a entrada e processá-la. Nesta secção, vai criar uma consulta que lê cada tweet da entrada e, em seguida, invoca a função de Machine Learning para executar uma análise de dados de sentimento. A consulta, em seguida, envia o resultado à saída de que definiu (armazenamento de BLOBs).

1. Regresse ao painel de descrição geral de tarefa.

2.  Em **tarefa topologia**, clique em de **consulta** caixa.

    ![Criar uma consulta para a tarefa de análise de transmissão em fluxo](./media/stream-analytics-machine-learning-integration-tutorial/create-query.png)  

3. Introduza a seguinte consulta:

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    A consulta invoca a função que criou anteriormente (`sentiment`) para executar uma análise de dados de sentimento em cada tweet na entrada. 

4. Clique em **guardar** para guardar a consulta.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa de Stream Analytics e a saída de verificação

É agora possível iniciar a tarefa de Stream Analytics.

### <a name="start-the-job"></a>Iniciar a tarefa
1. Regresse ao painel de descrição geral de tarefa.

2. Clique em **iniciar** na parte superior do painel.

    ![Criar uma consulta para a tarefa de análise de transmissão em fluxo](./media/stream-analytics-machine-learning-integration-tutorial/start-job.png)  

3. No **iniciar tarefa**, selecione **personalizada**e, em seguida, selecione um dia antes da quando o ficheiro CSV que carregou para o blob storage. Quando tiver terminado, clique em **iniciar**.  


### <a name="check-the-output"></a>Verifique a saída
1. Permitir que a tarefa executar alguns minutos até ver a atividade no **monitorização** caixa. 

2. Se tiver uma ferramenta que normalmente utiliza para examinar o conteúdo do armazenamento de BLOBs, utilize essa ferramenta para examinar o `azuresamldemoblob` contentor. Em alternativa, efetue os seguintes passos no portal do Azure:

    1. O portal, localize o `samldemo` armazenamento conta e, dentro da conta, localize o `azuresamldemoblob` contentor. Pode ver dois ficheiros no contentor: o ficheiro que contém os tweets de exemplo e um ficheiro CSV gerado pela tarefa de Stream Analytics.
    2. O ficheiro gerado com o botão direito e, em seguida, selecione **transferir**. 

   ![Transferir o resultado da tarefa CSV do armazenamento de BLOBs](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Abra o ficheiro CSV gerado. Verá algo semelhante ao seguinte exemplo:  
   
   ![Veja aprendizagem do Stream Analytics, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Veja as métricas
Também pode ver as métricas relacionadas com a função do Azure Machine Learning. As métricas relacionadas com a função seguintes são apresentadas no **monitorização** caixa no painel de tarefas:

* **Pedidos de função** indica o número de pedidos enviados para um serviço web do Machine Learning.  
* **Eventos de função** indica o número de eventos no pedido. Por predefinição, cada pedido a um serviço web do Machine Learning contém até 1000 eventos.  


## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integrar o REST API e Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




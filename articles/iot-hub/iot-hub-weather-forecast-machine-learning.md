---
title: "Meteorologia previsão a utilizar o Azure Machine Learning com dados a partir do IoT Hub | Microsoft Docs"
description: "Utilização do Azure Machine Learning para prever a possibilidade de rain com base nos dados relativos à temperatura e humidade que recolhe o seu IoT hub a partir de um sensor."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Meteorologia previsão de aprendizagem"
ms.assetid: 8ba7d9e7-699c-4448-b353-0f3e1429d198
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 50ae54b9476c49b80236e295c0bf244df8236cff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Meteorologia previsão a utilizar os dados de sensor do seu hub IoT no Azure Machine Learning

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning é uma técnica de ciência de dados que o ajuda a obter dados existentes para prever futuros comportamentos, resultados e tendências de computadores. O Azure Machine Learning é um serviço de análise preditiva baseado na cloud e que torna possível a criação e implementação rápidas de modelos preditivos como soluções de análise.

## <a name="what-you-learn"></a>O que irá aprender

Saiba como utilizar o Azure Machine Learning para meteorologia previsão (possibilidade de rain) utilizando os dados relativos à temperatura e humidade do seu hub IoT do Azure. A possibilidade de rain é o resultado de um modelo de previsão Meteorologia preparado. O modelo baseia-se após os dados históricos, para a possibilidade de rain com base no relativos à temperatura e humidade de previsão.

## <a name="what-you-do"></a>O que fazer

- Implemente o modelo de previsão de Meteorologia como um serviço web.
- Prepare o seu IoT hub para acesso a dados através da adição de um grupo de consumidores.
- Criar uma tarefa de Stream Analytics e configurar a tarefa para:
  - Ler dados relativos à temperatura e humidade a partir do seu IoT hub.
  - Chame o serviço web para obter a possibilidade de rain.
  - Guarde o resultado para um armazenamento de Blobs do Azure.
- Utilize o Explorador de armazenamento do Microsoft Azure para ver a previsão de Meteorologia.

## <a name="what-you-need"></a>Do que precisa

- Tutorial [configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluir que inclui os seguintes requisitos:
  - Uma subscrição ativa do Azure.
  - Um hub IoT do Azure na sua subscrição.
  - Uma aplicação de cliente que envia mensagens para o seu hub IoT do Azure.
- Uma conta do Azure Machine Learning Studio. ([Experimente gratuitamente o Machine Learning Studio](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Implementar o modelo de previsão Meteorologia como um serviço web

1. Vá para o [página de modelo de previsão Meteorologia](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Clique em **abrir no Studio** no Microsoft Azure Machine Learning Studio.
   ![Abrir a página de modelo de previsão de Meteorologia na galeria da Cortana Intelligence](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Clique em **executar** para validar os passos no modelo. Este passo pode demorar 2 minutos a concluir.
   ![Abra o modelo de previsão Meteorologia no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Clique em **configurar o serviço WEB** > **serviço Web preditiva**.
   ![Implementar o modelo de previsão Meteorologia no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. No diagrama, arraste o **Web service entrada** módulo algures quase a **Pontuar modelo** módulo.
1. Ligar o **Web service entrada** módulo para a **Pontuar modelo** módulo.
   ![Ligar dois módulos no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Clique em **executar** para validar os passos no modelo.
1. Clique em **implementar serviço WEB** para implementar o modelo como um serviço web.
1. No dashboard do modelo, transfira o **Excel 2010 ou anterior livro** para **pedido/resposta**.

   > [!Note]
   > Certifique-se de que transfira o **Excel 2010 ou anterior livro** , mesmo se estiver a executar uma versão posterior do Excel no seu computador.

   ![Transferir o Excel para o ponto final de resposta a um pedido](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Abra o livro do Excel, anote o **URL do serviço WEB** e **chave de acesso**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar uma tarefa de Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. No [portal do Azure](https://ms.portal.azure.com/), clique em **novo** > **Internet das coisas** > **tarefa do Stream Analytics**.
1. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: O nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Grupo de recursos**: utilizar o mesmo grupo de recursos que utiliza o seu IoT hub.

   **Localização**: utilizar a mesma localização como o grupo de recursos.

   **Afixar ao dashboard**: confirme esta opção para facilitar o acesso ao seu IoT hub a partir do dashboard.

   ![Criar uma tarefa de Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada para a tarefa de Stream Analytics

1. Abra a tarefa de Stream Analytics.
1. Em **tarefa topologia**, clique em **entradas**.
1. No **entradas** painel, clique em **adicionar**e, em seguida, introduza as seguintes informações:

   **Alias de entrada**: O alias exclusivo para a entrada.

   **Origem**: selecione **IoT hub**.

   **Grupo de consumidores**: selecione o grupo de consumidores que criou.

   ![Adicionar uma entrada para a tarefa de Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **tarefa topologia**, clique em **saídas**.
1. No **saídas** painel, clique em **adicionar**e, em seguida, introduza as seguintes informações:

   **O alias de saída**: O alias exclusivo para a saída.

   **Sink**: selecione **armazenamento de BLOBs**.

   **Conta de armazenamento**: A conta de armazenamento para o armazenamento de Blobs. Pode criar uma conta de armazenamento ou utilize uma já existente.

   **Contentor**: O contentor onde está guardado o blob. Pode criar um contentor ou utilize uma já existente.

   **Formato de serialização de eventos**: selecione **CSV**.

   ![Adicionar uma saída à tarefa do Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Adicionar uma função para a tarefa de Stream Analytics para chamar o serviço web que implementou

1. Em **tarefa topologia**, clique em **funções** > **adicionar**.
1. Introduza as seguintes informações:

   **Alias de função**: introduza `machinelearning`.

   **Tipo de função**: selecione **do Azure ML**.

   **Opção de importar**: selecione **importação de uma subscrição diferente**.

   **URL**: introduza o URL do serviço WEB que anotou para baixo do livro do Excel.

   **Chave**: introduza a chave de acesso que anotou para baixo de livro do Excel.

   ![Adicionar uma função para a tarefa de Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa de Stream Analytics

1. Em **tarefa topologia**, clique em **consulta**.
1. Substitua o código existente pelo código seguinte:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Substitua `[YourInputAlias]` com o alias de entrada da tarefa.

   Substitua `[YourOutputAlias]` com o alias de saída da tarefa.

1. Clique em **Guardar**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa de Stream Analytics

A tarefa de Stream Analytics, clique em **iniciar** > **agora** > **iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **parado** para **executar**.

![Executar a tarefa de Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Utilize o Explorador de armazenamento do Microsoft Azure para ver a previsão de Meteorologia

Execute a aplicação de cliente para iniciar a recolha e envio de dados de temperatura e humidade ao seu IoT hub. Para cada mensagem que recebe o seu IoT hub, a tarefa de Stream Analytics chama o serviço web de previsão Meteorologia para produzir a possibilidade de rain. O resultado é guardado, em seguida, para o armazenamento de Blobs do Azure. Explorador de armazenamento do Azure é uma ferramenta que pode utilizar para ver o resultado.

1. [Transfira e instale o Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/).
1. Abra o Explorador de armazenamento do Azure.
1. Inicie sessão sua conta do Azure.
1. Selecione a sua subscrição.
1. Clique em sua subscrição > **contas do Storage** > a conta do storage > **contentores de BLOBs** > o contentor.
1. Abra um ficheiro. csv para ver o resultado. A última coluna regista a possibilidade de rain.

   ![Obter Resultado de previsão Meteorologia com o Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Resumo

Utilizou com êxito do Azure Machine Learning para produzir a possibilidade de rain com base nos dados relativos à temperatura e humidade que recebe o seu IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
---
title: "Implementação do Azure Stream Analytics com o limite de IoT do Azure | Microsoft Docs"
description: "Implementar o Azure Stream Analytics como um módulo para um dispositivo de limite"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Implementar o Azure Stream Analytics como um módulo de limite de IoT – pré-visualização

Dispositivos de IoT podem produzir grandes quantidades de dados. Por vezes, estes dados tem de ser analisados ou processados antes de atingir a nuvem para reduzir o tamanho dos dados carregados ou para eliminar a latência reportadas round-trip das informações acionável sobre.

Limite de IoT tira partido dos módulos de limite de IoT do Azure Service previamente concebidos para uma implementação rápida e [Azure Stream Analytics] [ azure-stream] (ASA) é um desse módulo. Pode criar uma tarefa do ASA do seu portal e, regresse ao portal do IoT Hub para implementá-lo como um módulo de limite de IoT.  

O Azure Stream Analytics fornece uma sintaxe de consulta caixas estruturados para análise de dados na nuvem e no limite de IoT dispositivos. Para obter mais informações sobre ASA no limite do IoT, consulte [documentação ASA](../stream-analytics/stream-analytics-edge.md).

Este tutorial orienta-o através da criação de uma tarefa do Azure Stream Analytics e a respetiva implementação num dispositivo de limite de IoT para processar um fluxo de telemetria local diretamente no dispositivo e gerar alertas para uma ação imediata unidade no dispositivo.  Existem dois módulos envolvidos neste tutorial. Um módulo de sensor de temperatura simulada (tempSensor) gera dados de temperatura de 20 a 120 graus, aumentadas em 1 a cada cinco segundos. Um módulo de Stream Analytics repõe o tempSensor quando a média de 30 segundos atinge 70. Num ambiente de produção, esta funcionalidade pode ser utilizada para encerradas uma máquina ou tomar medidas preventivas quando a temperatura atinge níveis perigosos. 

Saiba como:

> [!div class="checklist"]
> * Criar uma tarefa do ASA para processar dados num limite
> * Ligar a nova tarefa de ASA com outros módulos de limite de IoT
> * Implementar a tarefa do ASA para um dispositivo de limite de IoT

## <a name="prerequisites"></a>Pré-requisitos

* Um IoT Hub 
* O dispositivo que criou e configurado no início rápido ou implementar o Azure IoT Edge om um dispositivo simulado no [Windows] [ lnk-tutorial1-win] e [Linux] [ lnk-tutorial1-lin]. É necessário saber a chave de ligação do dispositivo e o ID de dispositivo. 
* Docker em execução no seu dispositivo de limite de IoT
    * [Instalar Docker no Windows][lnk-docker-windows]
    * [Instalar Docker no Linux][lnk-docker-linux]
* Python 2.7.x no seu dispositivo de limite de IoT
    * [Instalar o Python 2.7 no Windows][lnk-python].
    * A maioria das distribuições de Linux, incluindo Ubuntu, já tem o Python 2.7 instalado.  Utilize o seguinte comando para se certificar de que está instalado o pip: `sudo apt-get install python-pip`.


## <a name="create-an-asa-job"></a>Criar uma tarefa do ASA

Nesta secção, vai criar uma tarefa do Azure Stream Analytics para colocar os dados a partir do seu IoT hub, consultar os dados de telemetria enviada do seu dispositivo e reencaminhar os resultados para um contentor de armazenamento do Azure (BLOB). Para obter mais informações, consulte o **descrição geral** secção o [documentação do Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

É necessária uma conta de armazenamento do Azure para fornecer um ponto final para ser utilizado como uma saída na sua tarefa do ASA. O exemplo abaixo utiliza o tipo de armazenamento de BLOBS.  Para obter mais informações, consulte o **Blobs** secção o [documentação do Storage do Azure][azure-storage].

1. No portal do Azure, navegue para **crie um recurso** e introduza `Storage account` na barra de procura. Selecione **conta de armazenamento - BLOBs, ficheiro, tabela, fila**.

2. Introduza um nome para a sua conta de armazenamento e selecione a mesma localização onde está localizado o seu IoT hub. Clique em **Criar**. Lembre-se o nome para utilizar mais tarde.

    ![nova conta de armazenamento][1]

3. Navegue para a conta de armazenamento que acabou de criar. Clique em **procurar blobs**. 
4. Crie um novo contentor para o módulo do ASA armazenar dados. Defina o acesso de nível para **contentor**. Clique em **OK**.

    ![definições de armazenamento][10]

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. No portal do Azure, navegue para **crie um recurso** > **Internet das coisas** e selecione **tarefa do Stream Analytics**.

2. Introduza um nome, escolha **Edge** como o ambiente de alojamento e utilizar os valores predefinidos restantes.  Clique em **Criar**.

    >[!NOTE]
    >Atualmente, as tarefas do ASA IoT Edge não são suportadas na região EUA oeste 2. 

3. Vá para a tarefa de criação. Selecione **entradas** , em seguida, clique em **adicionar**.

4. Para o alias de entrada introduza `temperature`, defina o tipo de origem para **fluxo de dados**e utilize as predefinições para os outros parâmetros. Clique em **Criar**.

   ![Entrada do ASA](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Selecione **saídas** , em seguida, clique em **adicionar**.

6. Para o alias de saída introduza `alert`e utilize as predefinições para os outros parâmetros. Clique em **Criar**.

   ![Saída do ASA](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. Selecione **consulta**.
8. Substitua o texto predefinido com a seguinte consulta:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```
9. Clique em **Guardar**.

## <a name="deploy-the-job"></a>Implementar a tarefa

Agora está pronto para implementar a tarefa do ASA no seu dispositivo de limite de IoT.

1. No portal do Azure, no seu IoT hub, navegue para **IoT Edge (pré-visualização)** e abra a página de detalhes para o seu dispositivo de limite de IoT.
1. Selecione **definir módulos**.
1. Se tiver implementado anteriormente o módulo de tempSensor neste dispositivo, poderá autopopulate. Caso contrário, utilize os seguintes passos para adicionar esse módulo:
   1. Clique em **Adicionar módulo IoT Edge**
   1. Introduza `tempSensor` como o nome, e `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` para o URI de imagem. 
   1. Deixe as outras definições inalteradas e clique em **guardar**.
1. Para adicionar a tarefa do ASA Edge, selecione **importação do Azure Stream Analytics IoT Edge módulo**.
1. Selecione a sua subscrição e a tarefa de limite de ASA que criou. 
1. Selecione a sua subscrição e a conta de armazenamento que criou. Clique em **Guardar**.

    ![módulo de conjunto][6]

1. Copie o nome que foi gerado automaticamente para o módulo do ASA. 

    ![módulo de temperatura][11]

1. Clique em **seguinte** configurar rotas.
1. Copie o seguinte procedimento para **rotas**.  Substitua _{moduleName}_ com o nome do módulo que copiou:

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

1. Clique em **Seguinte**.

1. No **rever modelo** passo, clique em **submeter**.

1. Regresse à página de detalhes do dispositivo e clique em **atualizar**.  Deverá ver o novo módulo do Stream Analytics executar juntamente com o **agente IoT Edge** módulo e a **hub IoT Edge**.

    ![saída do módulo][7]

## <a name="view-data"></a>Ver dados

Agora pode aceder ao seu dispositivo de limite de IoT para verificar a interação entre o módulo do ASA e o módulo de tempSensor.

Verifique se todos os módulos em execução no Docker:

   ```cmd/sh
   docker ps  
   ```

   ![saída de docker][8]

Ver todos os dados de métricas e registos de sistema. Utilize o nome do módulo do Stream Analytics:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Deverá conseguir ver a temperatura da máquina, aumentar gradualmente até atingir graus 70 durante 30 segundos. Em seguida, o módulo de Stream Analytics aciona uma reposição e a temperatura máquina novamente remove para 21. 

   ![registo de docker][9]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um contentor de armazenamento do Azure e uma tarefa de análise de transmissão em fluxo para analisar os dados a partir do seu dispositivo de limite de IoT.  Em seguida, carregar um módulo personalizado do ASA para mover dados do seu dispositivo, através da sequência de para um BLOB para transferência.  Pode continuar para outros tutoriais para ver mais como o limite de IoT do Azure pode criar soluções para a sua empresa.

> [!div class="nextstepaction"] 
> [Implementar um modelo do Azure Machine Learning como um módulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
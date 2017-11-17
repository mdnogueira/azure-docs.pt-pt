---
title: "Implementação do Azure Stream Analytics com o limite de IoT do Azure | Microsoft Docs"
description: "Implementar o Azure Stream Analytics como um módulo para um dispositivo de limite"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 88dc0860b4455d19b05b4f4f1766210e3f3c704e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Implementar o Azure Stream Analytics como um módulo de limite de IoT – pré-visualização

Dispositivos de IoT podem produzir grandes quantidades de dados. Por vezes, estes dados tem de ser analisados ou processados antes de atingir a nuvem para reduzir o tamanho dos dados carregados ou para eliminar a latência reportadas round-trip das informações acionável sobre.

[O Azure Stream Analytics] [ azure-stream] (ASA) fornece uma sintaxe de consulta caixas estruturados para análise de dados na nuvem e no limite de IoT dispositivos. Para obter mais informações sobre ASA no limite do IoT, consulte [documentação ASA](../stream-analytics/stream-analytics-edge.md).

Este tutorial orienta-o através da criação de uma tarefa do Azure Stream Analytics e a respetiva implementação num dispositivo de limite de IoT para processar um fluxo de telemetria local diretamente no dispositivo e gerar alertas para uma ação imediata unidade no dispositivo.  Existem dois módulos envolvidos neste tutorial. Um módulo de sensor de temperatura simulada (tempSensor) que gera dados de temperatura 20 a 120 graus, aumentadas em 1 a cada cinco segundos, e um módulo ASA, que filtra temperatures maiores que 100 graus. O módulo do ASA também repõe o tempSensor quando a média de 30 segundos atinge a 100.

Saiba como:

> [!div class="checklist"]
> * Criar uma tarefa do ASA para processar dados num limite
> * Ligar a nova tarefa de ASA com outros módulos de limite de IoT
> * Implementar a tarefa do ASA para um dispositivo de limite de IoT

## <a name="prerequisites"></a>Pré-requisitos

* Um IoT Hub 
* O dispositivo que criou e configurado no início rápido ou implementar o Azure IoT Edge om um dispositivo simulado no [Windows] [ lnk-tutorial1-win] e [Linux] [ lnk-tutorial1-lin].
* Docker no seu dispositivo de limite de IoT
    * [Instalar Docker no Windows] [ lnk-docker-windows] e certifique-se de que está a ser executado.
    * [Instalar Docker no Linux] [ lnk-docker-linux] e certifique-se de que está a ser executado.
* Python 2.7.x no seu dispositivo de limite de IoT
    * [Instalar o Python 2.7 no Windows][lnk-python].
    * A maioria das distribuições de Linux, incluindo Ubuntu, já tem o Python 2.7 instalado.  Utilize o seguinte comando para se certificar de que está instalado o pip: `sudo apt-get install python-pip`.

> [!NOTE]
> Tenha em atenção de que a cadeia de ligação do dispositivo e o ID de dispositivo de limite de IoT serão necessários para este tutorial.

Limite de IoT tira partido dos módulos de limite de IoT do Azure Service previamente concebidos para uma implementação rápida e do Azure Stream Analytics (ASA) é um desse módulo. Pode criar uma tarefa do ASA do seu portal e, regresse ao portal do IoT Hub para implementá-lo como um módulo de limite de IoT.  

Para obter mais informações sobre o Azure Stream Analytics, consulte o **descrição geral** secção o [documentação do Stream Analytics][azure-stream].

## <a name="create-an-asa-job"></a>Criar uma tarefa do ASA

Nesta secção, vai criar uma tarefa do Azure Stream Analytics para colocar os dados a partir do seu IoT hub, consultar os dados de telemetria enviada do seu dispositivo e reencaminhar os resultados para um contentor de armazenamento do Azure (BLOB). Para obter mais informações, consulte o **descrição geral** secção o [documentação do Stream Analytics][azure-stream]. 

> [!NOTE]
> É necessária uma conta de armazenamento do Azure para fornecer um ponto final para ser utilizado como uma saída na sua tarefa do ASA. O exemplo abaixo utiliza o tipo de armazenamento de BLOBS.  Para obter mais informações, consulte o **Blobs** secção o [documentação do Storage do Azure][azure-storage].

1. No portal do Azure, navegue para **criar um recurso -> armazenamento**, clique em **ver todos os**e clique em **conta de armazenamento - BLOBs, ficheiro, tabela, fila**.

2. Introduza um nome para a sua conta de armazenamento e selecione a mesma localização onde está armazenado o seu IoT Hub. Clique em **Criar**. Tome nota do nome de mais tarde.

    ![nova conta de armazenamento][1]

3. No portal do Azure, navegue para a conta de armazenamento que acabou de criar. Clique em **procurar blobs** em **serviço Blob**. 
4. Crie um novo contentor para o módulo do ASA armazenar dados. Defina o acesso de nível para _contentor_. Clique em **OK**.

    ![definições de armazenamento][10]

5. No portal do Azure, navegue para **crie um recurso** > **Internet das coisas** e selecione **tarefa do Stream Analytics**.

6. Introduza um nome, escolha **Edge** como o ambiente de alojamento e utilizar os valores predefinidos restantes.  Clique em **Criar**.

    >[!NOTE]
    >Atualmente, existem um número limitado de regiões que suportam as tarefas ASA no limite de IoT. Selecione um dos seguintes como a localização: EUA Central, Central EUA oeste, Norte Central dos EUA, EUA leste, Canadá leste, este do Japão, Leste asiático, Europa do Norte, RU Sul, leste da Austrália, sul do Brasil. 

    ![Criar ASA][5]

2. Vá para a tarefa criada, em **tarefa topologia**, selecione **entradas**, clique em **adicionar**.

3. Introduza o nome `temperature`, escolha **fluxo de dados** como o tipo de origem e utilizar as predefinições para os outros parâmetros. Clique em **Criar**.

    ![Entrada do ASA][2]

    > [!NOTE]
    > Entradas adicionais podem incluir pontos finais específicos do IoT Edge.

4. Em **tarefa topologia**, selecione **saídas**, clique em **adicionar**.

5. Introduza o nome `alert` e utilize as predefinições. Clique em **Criar**.

    ![Saída do ASA][3]

6. Em **tarefa topologia**, selecione **consulta**e introduza o seguinte:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>Implementar a tarefa

Agora está pronto para implementar a tarefa do ASA no seu dispositivo de limite de IoT.

1. No portal do Azure, no seu IoT Hub, navegue para **IoT Edge (pré-visualização)** e abra o *{"deviceId"}*do painel.

1. Selecione **definir módulos**, em seguida, selecione **módulo Edge IoT do serviço de importação do Azure**.

1. Selecione a subscrição e a tarefa de limite de ASA que criou. Em seguida, selecione a sua conta de armazenamento. Clique em **Guardar**.

    ![módulo de conjunto][6]

1. Clique em **Adicionar módulo de limite de IoT** para adicionar o módulo de sensor de temperatura. Introduza _tempSensor_ para nome, `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` para o URL da imagem. Deixe as outras definições inalteradas e clique em **guardar**.

    ![módulo de temperatura][11]

1. Copie o nome do módulo do ASA. Clique em **seguinte** configurar rotas.

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

1. Regresse à página de detalhes do dispositivo e clique em **atualizar**.  Deverá ver o novo _{moduleName}_ módulo em execução juntamente com o **agente IoT Edge** módulo e a **hub IoT Edge**.

    ![saída do módulo][7]

## <a name="view-data"></a>Ver dados

Agora pode aceder ao seu dispositivo de limite de IoT para verificar a interação entre o módulo do ASA e o módulo de tempSensor.

1. Numa linha de comandos, configure o tempo de execução com a cadeia de ligação do dispositivo de limite de IoT:

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. Execute o comando para iniciar o tempo de execução:

    ```cmd/sh
    iotedgectl start  
    ```

1. Execute o comando para ver os módulos em execução:

    ```cmd/sh
    docker ps  
    ```

    ![saída de docker][8]

1. Execute o comando para ver todos os registos de sistema e dados de métricas. Utilize o nome do módulo de acima:

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![registo de docker][9]

1. No portal do Azure, na sua conta de armazenamento, em **serviço Blob**, clique em **procurar blobs**, selecione o contentor e selecione o ficheiro de JSON recentemente criado.

1. Clique em **transferir** e ver os resultados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um contentor de armazenamento do Azure e uma tarefa de análise de transmissão em fluxo para analisar os dados a partir do seu dispositivo de limite de IoT.  Em seguida, carregar um módulo personalizado do ASA para mover dados do seu dispositivo, através da sequência de para um BLOB para transferência.  Pode continuar para outros tutoriais para ver mais como o limite de IoT do Azure pode criar soluções para a sua empresa.

> [!div class="nextstepaction"] 
> [Implementar um modelo do Azure Machine Learning como um módulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
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
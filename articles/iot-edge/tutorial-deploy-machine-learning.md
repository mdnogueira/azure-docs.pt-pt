---
title: Implementar o Azure Machine Learning com limite de IoT do Azure | Microsoft Docs
description: "Implementar o Azure Machine Learning como um módulo para um dispositivo de limite"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9b8475dcc51fb24fadd1faa4a2008b25a4464080
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Implementar o Azure Machine Learning como um módulo de limite de IoT – pré-visualização

Pode utilizar módulos de limite de IoT para implementar o código que implementa a lógica de negócio diretamente nos seus dispositivos de limite de IoT. Este tutorial explica-lhe implementar um módulo do Azure Machine Learning que prevê quando um dispositivo falha com base nos dados de sensores no dispositivo simulado contorno de IoT que criou na implementação do Azure IoT limite num dispositivo simulado no [Windows] [ lnk-tutorial1-win] ou [Linux] [ lnk-tutorial1-lin] tutoriais. Saiba como: 

> [!div class="checklist"]
> * Implementar um módulo do Azure Machine Learning para o seu dispositivo de limite de IoT
> * Dados de vista gerada

Quando pretender utilizar os seus próprios [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) modelo na sua solução irá [implementar um modelo](https://aka.ms/aml-iot-edge-doc) para limite de IoT e anfitrião-a no registo de um contentor como [registo de contentor do Azure](../container-registry/index.yml) ou Docker.

## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo de limite de IoT do Azure que criou no início rápido ou primeiro tutorial.
* A cadeia de ligação do IoT Hub para o IoT hub que liga o dispositivo de limite de IoT.
* O contentor do Azure ML

## <a name="create-the-azure-ml-container"></a>Criar o contentor do Azure ML
Para criar o contentor do Azure ML, siga as instruções de [toolkit de AI do Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).

## <a name="run-the-solution"></a>Executar a solução

1. No [portal do Azure](https://portal.azure.com), navegue até ao seu IoT hub.
1. Aceda a **IoT Edge (pré-visualização)** e selecione o seu dispositivo de limite de IoT.
1. Selecione **definir módulos**.
1. Selecione **módulo de limite de IoT adicionar**.
1. No **nome** campo, introduza `tempSensor`.
1. No **URI de imagem** campo, introduza `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Deixar as outras definições inalteradas e selecione **guardar**.
1. Ainda no **adicionar módulos** passo, selecione **módulo de limite de IoT adicionar** novamente.
1. No **nome** campo, introduza o nome do contentor que efetuou na secção anterior. Consulte o [toolkit de AI do Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection) para ajudar a localizar o nome.
1. No **imagem** campo, introduza a imagem de URI do contentor que efetuou na secção anterior. Consulte o [toolkit de AI do Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection) para ajudar a encontrar a imagem.
1. Clique em **Guardar**.
1. Volta a **adicionar módulos** passo, clique em **seguinte**.
1. Atualize as rotas para o módulo de:
1. No **especificar rotas** passo, copie o JSON abaixo na caixa de texto. Módulos publicar todas as mensagens para o tempo de execução do limite. As regras de declarativas em tempo de execução definem onde as mensagens de fluxo. Este tutorial precisa de dois rotas. A rota primeiro transportes mensagens do sensor de temperatura para o módulo de aprendizagem máquina através de ponto final "mlInput", que é o ponto final que utilizam todos os módulos do Azure Machine Learning. A segunda rota transportes mensagens do módulo machine learning ao IoT Hub. Esta rota, ' mlOutput ' é endput que todos os módulos do Azure Machine Learning utilizam para dados de saída e 'a montante ' é um destino de especial que diz ao Hub Edge para enviar mensagens para o IoT Hub. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Clique em **Seguinte**. 
1. No passo "Rever modelo", clique em "Submeter". 
1. Regresse à página de detalhes do dispositivo e clique em 'Atualizar '.  Deverá ver o novo "machinelearningmodule" em execução, juntamente com o módulo"tempSensor ' e 'IoT Edge tempo de execução'.

## <a name="view-generated-data"></a>Dados de vista gerada

 No VS Code, utilize o **vista | Comando paleta... | IoT: Mensagens de D2C monitorização início** comandos de menu para monitorizar os dados que chegam no IoT Hub. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um módulo de limite de IoT com tecnologia do Azure Machine Learning. Pode continuar a sessão em qualquer um dos outros tutoriais para saber mais sobre outras formas de limite de IoT do Azure pode ajudar a que ativar dados em informações empresariais no limite.

> [!div class="nextstepaction"]
> [Implementar uma função do Azure como um módulo](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
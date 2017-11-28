---
title: Implementar um modelo do Azure Machine Learning para um dispositivo de limite de IoT do Azure | Microsoft Docs
description: Este documento descreve como os modelos do Azure Machine Learning podem ser implementados em dispositivos de limite de IoT do Azure.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: 60db74535edd09841a1b660c2bf832af65fbfcad
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Implementar um modelo do Azure Machine Learning para um dispositivo de limite de IoT do Azure

Todos os modelos do Azure Machine Learning de como serviços web baseados em Docker também podem executar em dispositivos de limite de IoT do Azure. Instruções de scripts adicionais e podem ser encontradas no [Toolkit de AI do Azure IoT Edge](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operacionalizar o modelo
Operacionalizar o seu modelo, seguindo as instruções em [implementação de serviço ao Web do gestão de modelo do Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy) para criar uma imagem de Docker com o seu modelo.

## <a name="deploy-to-azure-iot-edge"></a>Implementar no Azure IoT Edge
Limite de IoT do Azure move a análise de nuvem e a lógica de negócio personalizadas para dispositivos. Todos os modelos de Machine Learning podem executar em dispositivos de limite de IoT. A documentação para configurar um dispositivo de limite de IoT e criar uma implementação pode ser encontrada em [aka.ms/azure-iot-limite-documento](https://aka.ms/azure-iot-edge-doc).

Seguem-se adicionais coisas a salientar.

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>Adicione as credenciais do registo para o tempo de execução do Edge no seu dispositivo de limite
No computador onde estiver a executar o limite de IoT, adicione as credenciais do seu registo para que o tempo de execução possa ter acesso ao contentor de extração.

Para o Windows, execute o seguinte comando:
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
Para Linux, execute o seguinte comando:
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>Localizar a localização de imagem de contentor do Machine Learning
Tem a localização da imagem de contentor do Machine Learning. Para localizar a localização de imagem do contentor:

1. Inicie sessão no [portal do Azure](http://portal.azure.com/).
2. No **registo de contentor do Azure**, selecione de registo que pretende verificar.
3. No registo, clique em **repositórios** para ver uma lista de todos os repositórios do e suas imagens.














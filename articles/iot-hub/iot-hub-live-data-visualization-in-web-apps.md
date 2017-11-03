---
title: "Visualização de dados em tempo real de dados de sensores do seu hub IoT do Azure – Web Apps | Microsoft Docs"
description: "Utilize a funcionalidade de Web Apps do App Service do Microsoft Azure para visualizar dados relativos à temperatura e humidade recolhidos a partir do sensor e enviados ao seu Iot hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "visualização de dados em tempo real, visualização de dados em direto, visualização de dados de sensor"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.openlocfilehash: e037f5c29cabf8e5d0d3e7ded187280a0652d5c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Visualizar dados de sensores em tempo real do seu hub IoT do Azure utilizando a funcionalidade Web Apps do App Service do Azure

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Neste tutorial, irá aprender a visualizar os dados de sensores em tempo real que o seu IoT hub recebe ao executar uma aplicação web que está alojada numa aplicação web. Se pretender experimentar visualizar os dados no seu IoT hub, utilizando o Power BI, consulte [utilize o Power BI para visualizar dados de sensores em tempo real do IoT Hub do Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que fazer

- Crie uma aplicação web no portal do Azure.
- Prepare o seu IoT hub para acesso a dados através da adição de um grupo de consumidores.
- Configure a aplicação web para ler dados de sensores a partir do seu IoT hub.
- Carregar uma aplicação web para ser alojada pela aplicação web.
- Abra a aplicação web para ver os dados relativos à temperatura e humidade em tempo real do seu IoT hub.

## <a name="what-you-need"></a>Do que precisa

- [Configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md), que inclui os seguintes requisitos:
  - Uma subscrição do Azure Active Directory
  - Um Iot hub na sua subscrição
  - Uma aplicação de cliente que envia mensagens ao seu Iot hub
- [Transferir o Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Criar uma aplicação Web

1. No [portal do Azure](https://ms.portal.azure.com/), clique em **novo** > **Web + móvel** > **aplicação Web**.
2. Introduza um nome de tarefa exclusivo, certifique-se a subscrição, especifique um grupo de recursos e uma localização, selecione **afixar ao dashboard**e, em seguida, clique em **criar**.

   Recomendamos que selecione a mesma localização que o grupo de recursos. Se o fizer, auxilia com velocidade de processamento e reduz os custos de transferência de dados.

   ![Criar uma aplicação Web](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configurar a aplicação web para ler dados a partir do seu IoT hub

1. Abra a aplicação web que apenas tenha aprovisionado.
2. Clique em **definições da aplicação**e, em **as definições de aplicação**, adicionar os pares chave/valor seguintes:

   | Chave                                   | Valor                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Obtidos a partir do Explorador do iothub                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | O nome do grupo de consumidores que adicionar ao seu IoT hub  |

   ![Adicionar definições à sua aplicação web com pares chave/valor](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

3. Clique em **definições da aplicação**, em **definições gerais**, ativar/desativar a **Web sockets** opção e, em seguida, clique em **guardar**.

   ![Ative a opção de sockets Web](media/iot-hub-live-data-visualization-in-web-apps/10_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Carregar uma aplicação web para ser alojada pela aplicação web

No GitHub, fizemos disponível uma aplicação web que apresenta dados de sensores em tempo real a partir do seu IoT hub. Tudo o que precisa de fazer é configurar a aplicação web para trabalhar com um repositório de Git, transfira a aplicação web a partir do GitHub e, em seguida, carregue-o no Azure para a aplicação web para o anfitrião.

1. Na aplicação web, clique em **opções de implementação** > **Escolher origem** > **repositório de Git Local**e, em seguida, clique em **OK**.

   ![Configurar a implementação de aplicação web para utilizar o repositório de Git local](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Clique em **as credenciais de implementação**, crie um nome de utilizador e palavra-passe para utilizar para ligar ao repositório de Git no Azure e, em seguida, clique em **guardar**.

3. Clique em **descrição geral**e anote o valor de **url de clone de Git**.

   ![Obter o URL de clone de Git da sua aplicação web](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

4. Abra uma janela de terminal no seu computador local ou um comando.

5. Transferir a aplicação web a partir do GitHub e carregue-o no Azure para a aplicação web para o anfitrião. Para tal, execute os seguintes comandos:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<URL de clone de Git\> é o URL do repositório de Git encontrado no **descrição geral** página da aplicação web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Abra a aplicação web para ver os dados relativos à temperatura e humidade em tempo real do seu IoT hub

No **descrição geral** página da sua aplicação web, clique no URL para abrir a aplicação web.

![Obter o URL da sua aplicação web](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Deverá ver os dados relativos à temperatura e humidade em tempo real do seu IoT hub.

![Página da aplicação Web em tempo real temperatura e humidade a mostrar](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Certifique-se de que a aplicação de exemplo está em execução no seu dispositivo. Se não, obterá um gráfico em branco, pode consultar os tutoriais em [configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Passos seguintes
Utilizou com êxito a aplicação web para visualizar dados de sensores em tempo real a partir do seu IoT hub.

Para uma maneira visualizar dados a partir do IoT Hub do Azure, consulte [utilize o Power BI para visualizar dados de sensores em tempo real do seu IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

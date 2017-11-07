---
title: Ligar um dispositivo utilizando C no Windows | Microsoft Docs
description: "Descreve como ligar um dispositivo à solução de monitorização remota do Azure IoT Suite pré-configurada utilizando uma aplicação de escrita no C em execução no Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Ligar o seu dispositivo à solução pré-configurada monitorização remota (Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Criar uma solução de exemplo C no Windows
Os passos seguintes mostram como criar uma aplicação cliente que comunica com a solução pré-configurada de monitorização remota. Esta aplicação é escrita no C incorporada e é executada no Windows.

Criar um projeto de arranque no Visual Studio 2015 ou Visual Studio 2017 e adicionar os pacotes de NuGet do cliente de dispositivo IoT Hub:

1. No Visual Studio, crie uma aplicação de consola C utilizando o Visual C++ **aplicação de consola Win32** modelo. Nomeie o projeto **RMDevice**.
2. No **definições da aplicação** página no **Assistente de aplicações de Win32**, certifique-se de que **aplicação de consola** está selecionada e desmarque **Precompiled cabeçalho** e **Security Development Lifecycle (SDL) verifica**.
3. No **Explorador de soluções**, eliminar os ficheiros stdafx.h, targetver.h e stdafx.cpp.
4. No **Explorador de soluções**, mude o nome de ficheiro RMDevice.cpp para RMDevice.c.
5. No **Explorador de soluções**, faça duplo clique no **RMDevice** projeto e, em seguida, clique em **pacotes NuGet gerir**. Clique em **procurar**, em seguida, procure e instale os seguintes pacotes de NuGet:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. No **Explorador de soluções**, faça duplo clique no **RMDevice** projeto e, em seguida, clique em **propriedades** para abrir o projeto **páginas de propriedades** caixa de diálogo. Para obter mais informações, consulte [propriedades da definição Visual C++ projeto][lnk-c-project-properties]. 
7. Clique no **Linker** pasta, em seguida, clique o **entrada** página de propriedades.
8. Adicionar **crypt32.lib** para o **dependências adicionais** propriedade. Clique em **OK** e, em seguida, **OK** novamente para guardar o projeto de valores de propriedade.

Adicionar a biblioteca de Parson JSON para o **RMDevice** projeto e adicione necessários `#include` instruções:

1. Numa pasta adequada no seu computador, clone o repositório do Parson GitHub utilizando o seguinte comando:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Copie os ficheiros de parson.h e parson.c a partir da cópia local do repositório Parson a sua **RMDevice** pasta do projeto.

1. No Visual Studio, clique com botão direito do **RMDevice** do projeto, clique em **adicionar**e, em seguida, clique em **Item existente**.

1. No **Adicionar Item existente** caixa de diálogo, selecione o parson.h e parson.c ficheiros no **RMDevice** pasta do projeto. Em seguida, clique em **adicionar** para adicionar estes dois ficheiros ao seu projeto.

1. No Visual Studio, abra o ficheiro RMDevice.c. Substituir a existente `#include` instruções com o seguinte código:
   
    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > Agora pode verificar se o seu projeto tem as dependências corretas configuradas por criá-lo.

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Compilar e executar o exemplo

Adicionar código para invocar a **remoto\_monitorização\_executar** funcionar e, em seguida, criar e executar a aplicação de dispositivo.

1. Substitua o **principal** função com o seguinte código ao invocar o **remoto\_monitorização\_executar** função:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Clique em **criar** e, em seguida, **compilar solução** para criar a aplicação de dispositivo.

1. No **Explorador de soluções**, faça duplo clique o **RMDevice** do projeto, clique em **depurar**e, em seguida, clique em **iniciar nova instância** para executar o exemplo. A consola apresenta as mensagens à medida que a aplicação envia a telemetria de exemplo para a solução pré-configurada, recebe os valores de propriedade pretendido definidos no dashboard de solução, responde aos métodos invocados a partir do dashboard de solução.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx

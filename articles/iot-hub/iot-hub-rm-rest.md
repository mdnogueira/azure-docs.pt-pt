---
title: Criar um hub IoT do Azure utilizando o fornecedor de recursos REST API | Microsoft Docs
description: Como utilizar o fornecedor de recursos REST API para criar um IoT Hub.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 52814ee5-bc10-4abe-9eb2-f8973096c2d8
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: d9372f8345257c45ae6b3b915383788f698a0e35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Criar um hub IoT utilizando o fornecedor de recursos REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pode utilizar o [fornecedor de recursos do IoT Hub REST API] [ lnk-rest-api] para criar e gerir os hubs IoT do Azure através de programação. Este tutorial mostra como utilizar o fornecedor de recursos do IoT Hub REST API para criar um hub IoT a partir de um programa c#.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo abrange utilizando o modelo de implementação Azure Resource Manager.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar o seu projeto de Visual Studio

1. No Visual Studio, crie um projeto do Visual c# Windows ambiente de trabalho clássico com a **aplicação de consola (.NET Framework)** modelo de projeto. Nomeie o projeto **CreateIoTHubREST**.

2. No Explorador de soluções, faça duplo clique no seu projeto e, em seguida, clique em **gerir pacotes NuGet**.

3. No Gestor de pacotes do NuGet, verifique **incluir pré-lançamento**e o **procurar** pesquisa de página para **Microsoft.Azure.Management.ResourceManager**. Selecione o pacote, clique em **instalar**, na **rever alterações** clique **OK**, em seguida, clique em **aceito** para aceitar as licenças.

4. No Gestor de pacotes do NuGet, procure **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **instalar**, na **rever alterações** clique **OK**, em seguida, clique em **aceito** para aceitar a licença.

5. Em Program.cs, substitua o existente **utilizando** instruções com o seguinte código:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Em Program.cs, adicione as seguintes variáveis estáticas, substituindo os valores de marcador de posição. Tomado nota do **ApplicationId**, **SubscriptionId**, **TenantId**, e **palavra-passe** anteriormente neste tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que utilizar quando criar o hub IoT. Pode utilizar pré-existente ou um novo grupo de recursos. **Nome do IoT Hub** é o nome do IoT Hub criar, tal como **MyIoTHub**. O nome do seu IoT hub deve ser globalmente exclusivo. **Nome da implementação** é um nome para a implementação, tais como **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Utilizar o fornecedor de recursos REST API para criar um hub IoT

Utilize o [fornecedor de recursos do IoT Hub REST API] [ lnk-rest-api] para criar um IoT hub no seu grupo de recursos. Também pode utilizar o REST API do fornecedor de recursos para efetuar alterações a um IoT hub.

1. Adicione o seguinte método para Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adicione o seguinte código para o **CreateIoTHub** método. Este código cria um **HttpClient** objeto com o token de autenticação nos cabeçalhos:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o seguinte código para o **CreateIoTHub** método. Este código descreve o IoT hub para criar e gera uma representação JSON. Para a atual lista de localizações que suportam o IoT Hub consulte [Azure estado][lnk-status]:

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Adicione o seguinte código para o **CreateIoTHub** método. Este código submete o pedido REST para o Azure. O código, em seguida, verifica a resposta e obtém o URL que pode utilizar para monitorizar o estado da tarefa de implementação:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Adicione o seguinte código ao fim do **CreateIoTHub** método. Este código utiliza o **asyncStatusUri** endereço obtido no passo anterior para aguardar a conclusão da implementação:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adicione o seguinte código ao fim do **CreateIoTHub** método. Este código obtém as chaves do IoT hub que criou e imprime-los para a consola:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Concluir e executar a aplicação

Agora pode concluir a aplicação ao chamar o **CreateIoTHub** método antes de criar e executá-la.

1. Adicione o seguinte código ao fim do **Main** método:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Clique em **criar** e, em seguida, **construir solução**. Corrigir os eventuais erros.

3. Clique em **depurar** e, em seguida, **iniciar depuração** para executar a aplicação. Pode demorar alguns minutos para que a implementação para execução.

4. Para verificar se a sua aplicação adicionado o novo IoT hub, visite o [portal do Azure] [ lnk-azure-portal] e ver a lista de recursos. Em alternativa, utilize o **Get-AzureRmResource** cmdlet do PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona uma S1 Standard IoT Hub para o qual é-lhe faturado. Quando tiver terminado, pode eliminar o hub IoT através de [portal do Azure] [ lnk-azure-portal] ou utilizando o **remover AzureRmResource** cmdlet do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Passos seguintes
Agora que implementou um IoT hub com o REST API do fornecedor de recursos, poderá querer explorar ainda mais:

* Leia sobre as capacidades do [fornecedor de recursos do IoT Hub REST API][lnk-rest-api].
* Leitura [descrição geral do Azure Resource Manager] [ lnk-azure-rm-overview] para saber mais sobre as funcionalidades do Gestor de recursos do Azure.

Para obter mais informações sobre como desenvolver para o IoT Hub, consulte os artigos seguintes:

* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs IoT do Azure][lnk-sdks]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

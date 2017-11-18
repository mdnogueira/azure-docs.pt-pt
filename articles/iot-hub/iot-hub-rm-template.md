---
title: "Criar um IoT Hub do Azure através de um modelo (.NET) | Microsoft Docs"
description: Como utilizar um modelo Azure Resource Manager para criar um IoT Hub com um programa c#.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a447b40c-c728-487e-875d-db554db5adc3
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 3f84ab26c43f33827479faed46db1de246920ae5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Criar um hub IoT utilizando o modelo Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pode utilizar o Azure Resource Manager para criar e gerir os hubs IoT do Azure através de programação. Este tutorial mostra como utilizar um modelo Azure Resource Manager para criar um hub IoT a partir de um programa c#.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo abrange utilizando o modelo de implementação Azure Resource Manager.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Um [conta de armazenamento do Azure] [ lnk-storage-account] onde pode armazenar os ficheiros de modelo Azure Resource Manager.
* [Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar o seu projeto de Visual Studio

1. No Visual Studio, crie um projeto do Visual c# Windows ambiente de trabalho clássico com a **aplicação de consola (.NET Framework)** modelo de projeto. Nomeie o projeto **CreateIoTHub**.

2. No Explorador de soluções, faça duplo clique no seu projeto e, em seguida, clique em **gerir pacotes NuGet**.

3. No Gestor de pacotes do NuGet, verifique **incluir pré-lançamento**e o **procurar** pesquisa de página para **Microsoft.Azure.Management.ResourceManager**. Selecione o pacote, clique em **instalar**, na **rever alterações** clique **OK**, em seguida, clique em **aceito** para aceitar as licenças.

4. No Gestor de pacotes do NuGet, procure **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **instalar**, na **rever alterações** clique **OK**, em seguida, clique em **aceito** para aceitar a licença.

5. Em Program.cs, substitua o existente **utilizando** instruções com o seguinte código:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Em Program.cs, adicione as seguintes variáveis estáticas, substituindo os valores de marcador de posição. Tomado nota do **ApplicationId**, **SubscriptionId**, **TenantId**, e **palavra-passe** anteriormente neste tutorial. **O nome da sua conta de armazenamento do Azure** é o nome da conta do Storage do Azure onde armazenar os ficheiros de modelo Azure Resource Manager. **Nome do grupo de recursos** é o nome do grupo de recursos que utilizar quando criar o hub IoT. O nome pode ter um grupo de recursos novo ou já existente. **Nome da implementação** é um nome para a implementação, tais como **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Submeter um modelo para criar um hub IoT

Utilize um ficheiro de modelo e o parâmetro JSON para criar um hub IoT no seu grupo de recursos. Também pode utilizar um modelo Azure Resource Manager para efetuar alterações a um IoT hub.

1. No Explorador de soluções, clique com botão direito no seu projeto, clique em **adicionar**e, em seguida, clique em **Novo Item**. Adicionar um ficheiro JSON chamado **Template** ao seu projeto.

2. Para adicionar um IoT hub padrão para o **EUA Leste** região, substitua os conteúdos de **Template** com a seguinte definição do recurso. Para a atual lista de regiões que suportam o IoT Hub consulte [Azure estado][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. No Explorador de soluções, clique com botão direito no seu projeto, clique em **adicionar**e, em seguida, clique em **Novo Item**. Adicionar um ficheiro JSON chamado **Parameters. JSON** ao seu projeto.

4. Substitua os conteúdos de **Parameters. JSON** com as seguintes informações de parâmetro que define um nome para o novo hub IoT, tais como **{suas iniciais} mynewiothub**. O nome do hub IoT deve ser globalmente exclusivo, pelo que deve incluir o nome ou iniciais:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. No **Explorador de servidores**, ligar à sua subscrição do Azure e na sua conta do Storage do Azure, criar um contentor denominado **modelos**. No **propriedades** painel, defina o **acesso de leitura público** permissões para o **modelos** contentor para **Blob**.

6. No **Explorador de servidores**, faça duplo clique no **modelos** contentor e, em seguida, clique em **contentor de BLOBs de vista**. Clique em de **carregar Blob** botão, selecione os dois ficheiros, **Parameters. JSON** e **templates.json**e, em seguida, clique em **abra** para carregar o Ficheiros JSON para o **modelos** contentor. Os URLs de blobs que contém os dados JSON são:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Adicione o seguinte método para Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Adicione o seguinte código para o **CreateIoTHub** método para submeter os ficheiros de parâmetros de modelo e o Gestor de recursos do Azure:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Adicione o seguinte código para o **CreateIoTHub** método que apresenta o estado e as chaves para o novo IoT hub:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Concluir e executar a aplicação

Agora pode concluir a aplicação ao chamar o **CreateIoTHub** método antes de criar e executá-la.

1. Adicione o seguinte código ao fim do **Main** método:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Clique em **criar** e, em seguida, **construir solução**. Corrigir os eventuais erros.

3. Clique em **depurar** e, em seguida, **iniciar depuração** para executar a aplicação. Pode demorar alguns minutos para que a implementação para execução.

4. Para verificar a sua aplicação adicionada novo IoT hub, visite o [portal do Azure] [ lnk-azure-portal] e ver a lista de recursos. Em alternativa, utilize o **Get-AzureRmResource** cmdlet do PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona uma S1 Standard IoT Hub para o qual é-lhe faturado. Pode eliminar o hub IoT através de [portal do Azure] [ lnk-azure-portal] ou utilizando o **remover AzureRmResource** cmdlet do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Passos seguintes
Agora que implementou um IoT hub utilizando um modelo Azure Resource Manager com um programa c#, pretende explorar ainda mais:

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
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

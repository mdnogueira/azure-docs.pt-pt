---
title: Implementar uma VM com c# e um modelo do Resource Manager | Microsoft Docs
description: Saiba como como utilizar c# e um modelo do Resource Manager para implementar uma VM do Azure.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: davidmu
ms.openlocfilehash: bd1c860db026f948202cd1f3aa763b4547c597b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implementar uma máquina de Virtual do Azure com c# e um modelo do Resource Manager
Este artigo mostra como implementar um modelo de Gestor de recursos do Azure com c#. O modelo que criou implementa uma única máquina virtual com o Windows Server numa rede virtual novo com uma única sub-rede.

Para obter uma descrição detalhada do recurso de máquina virtual, consulte [máquinas virtuais num modelo Azure Resource Manager](template-description.md). Para obter mais informações sobre todos os recursos num modelo, consulte [instruções do modelo Azure Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Demora cerca de 10 minutos para executar estes passos.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Neste passo, certifique-se de que o Visual Studio está instalado e criar uma aplicação de consola utilizada para implementar o modelo.

1. Se ainda não o fez, instale [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **desenvolvimento de ambiente de trabalho .NET** na página de cargas de trabalho e, em seguida, clique em **instalar**. Em resumo, pode ver que **ferramentas de desenvolvimento do .NET Framework 4 4.6** é selecionado automaticamente para si. Se já tiver instalado o Visual Studio, pode adicionar a carga de trabalho do .NET utilizando o Iniciador do Studio Visual.
2. No Visual Studio, clique em **ficheiro** > **novo** > **projeto**.
3. No **modelos** > **Visual c#**, selecione **aplicação de consola (.NET Framework)**, introduza *myDotnetProject* para o nome do projeto, selecione a localização do projeto e, em seguida, clique em **OK**.

## <a name="install-the-packages"></a>Instalar os pacotes

Os pacotes de NuGet são a forma mais fácil de instalar as bibliotecas que precisa para concluir estes passos. Para obter as bibliotecas que necessita no Visual Studio, execute estes passos:

1. Clique em **ferramentas** > **Gestor de pacotes Nuget**e, em seguida, clique em **consola do Gestor de pacotes**.
2. Introduza os seguintes comandos na consola:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Criar os ficheiros

Neste passo, vai criar um ficheiro de modelo que implementa os recursos e um ficheiro de parâmetros que forneça valores de parâmetro para o modelo. Também é criar um ficheiro de autorização que é utilizado para executar operações do Azure Resource Manager.

### <a name="create-the-template-file"></a>Criar o ficheiro de modelo

1. No Explorador de soluções, faça duplo clique *myDotnetProject* > **adicionar** > **Novo Item**e, em seguida, selecione **doficheirodetexto** no *Visual c# itens*. Nome de ficheiro *CreateVMTemplate.json*e, em seguida, clique em **adicionar**.
2. Adicione este código JSON para o ficheiro que criou:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Guarde o ficheiro CreateVMTemplate.json.

### <a name="create-the-parameters-file"></a>Criar o ficheiro de parâmetros

Para especificar valores para os parâmetros de recursos que estão definidos no modelo, crie um ficheiro de parâmetros que contém os valores.

1. No Explorador de soluções, faça duplo clique *myDotnetProject* > **adicionar** > **Novo Item**e, em seguida, selecione **doficheirodetexto** no *Visual c# itens*. Nome de ficheiro *Parameters. JSON*e, em seguida, clique em **adicionar**.
2. Adicione este código JSON para o ficheiro que criou:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Guarde o ficheiro Parameters. JSON.

### <a name="create-the-authorization-file"></a>Criar o ficheiro de autorização

Antes de poder implementar um modelo, certifique-se de que tem acesso a um [principal de serviço do Active Directory](../../resource-group-authenticate-service-principal.md). A partir de principal de serviço, adquirir um token para autenticar pedidos para o Azure Resource Manager. Também deve registar o ID da aplicação, a chave de autenticação e o ID do inquilino que necessita no ficheiro de autorização.

1. No Explorador de soluções, faça duplo clique *myDotnetProject* > **adicionar** > **Novo Item**e, em seguida, selecione **doficheirodetexto** no *Visual c# itens*. Nome de ficheiro *azureauth.properties*e, em seguida, clique em **adicionar**.
2. Adicione estas propriedades de autorização:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Substitua  **&lt;id de subscrição&gt;**  com o identificador de subscrição,  **&lt;id da aplicação&gt;**  com a aplicação do Active Directory Identificador,  **&lt;chave de autenticação&gt;**  com a chave de aplicação, e  **&lt;id do inquilino&gt;**  com o identificador de inquilino.

3. Guarde o ficheiro azureauth.properties.
4. Defina uma variável de ambiente do Windows com o nome AZURE_AUTH_LOCATION com o caminho completo do ficheiro de autorização que criou, por exemplo do PowerShell seguinte comando pode ser utilizado:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>Criar o cliente de gestão

1. Abra o ficheiro Program.cs para o projeto que criou e, em seguida, adicione-os através de instruções para as instruções existentes na parte superior do ficheiro:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Para criar o cliente de gestão, adicione este código ao método principal:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para especificar valores para a aplicação, adicione o código para o método principal:

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O modelo e os parâmetros sejam implementados a partir de uma conta de armazenamento no Azure. Neste passo, pode criar a conta e carregar os ficheiros. 

Para criar a conta, adicione este código ao método principal:

```
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFile("..\\..\\CreateVMTemplate.json");

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFile("..\\..\\Parameters.json");
```

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo e os parâmetros da conta do storage que foi criada. 

Para implementar o modelo, adicione este código ao método principal:

```
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Eliminar os recursos

Porque os lhe é cobrados os recursos utilizados no Azure, é sempre boa prática para eliminar os recursos que já não são necessárias. Não terá de eliminar cada recurso separadamente a partir de um grupo de recursos. Elimine o grupo de recursos e todos os respetivos recursos são eliminados automaticamente. 

Para eliminar o grupo de recursos, adicione este código ao método principal:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar a aplicação

Deve demorar cerca de cinco minutos para esta aplicação de consola executar totalmente do início seja concluída. 

1. Para executar a aplicação de consola, clique em **iniciar**.

2. Antes de premir **Enter** para iniciar a eliminação de recursos, pode demorar alguns minutos para verificar a criação dos recursos no portal do Azure. Clique no estado de implementação para ver informações sobre a implementação.

## <a name="next-steps"></a>Passos seguintes
* Se ocorreram problemas com a implementação, um passo seguinte será consultar [resolver erros comuns de implementação do Azure com o Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Saiba como implementar uma máquina virtual e os respetivos recursos de suporte, revendo [implementar uma Azure Virtual Machine utilizar c#](csharp.md).

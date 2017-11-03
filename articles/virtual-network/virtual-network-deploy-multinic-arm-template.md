---
title: "Criar uma VM com vários NICs - modelo Azure Resource Manager | Microsoft Docs"
description: "Crie uma VM com vários NICs com um modelo Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>Criar uma VM com vários NICs com um modelo
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md).  Este artigo explica como utilizar o modelo de implementação Resource Manager, o que é recomendado pela Microsoft para a maioria das novas implementações em vez do [modelo de implementação clássica](virtual-network-deploy-multinic-classic-ps.md).
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Os seguintes passos utilizam um grupo de recursos denominado *IaaSStory* para os servidores WEB e um grupo de recursos denominado *IaaSStory-back-end* para os servidores de base de dados.

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder criar os servidores de base de dados, terá de criar o *IaaSStory* grupo de recursos com todos os recursos necessários para este cenário. Para criar estes recursos, execute os seguintes passos:

1. Navegue para [na página do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Na página do modelo, à direita do **grupo de recursos de principal**, clique em **implementar no Azure**.
3. Se for necessário, altere os valores de parâmetros, em seguida, siga os passos no portal de pré-visualização do Azure para implementar o grupo de recursos.

> [!IMPORTANT]
> Certifique-se de que os nomes de conta de armazenamento são exclusivos. Não podem ter nomes de conta de armazenamento duplicado no Azure.
> 

## <a name="understand-the-deployment-template"></a>Compreender o modelo de implementação
Antes de implementar o modelo fornecido com esta documentação, certifique-se de que compreende o que faz. Os passos seguintes fornecem uma boa descrição geral do modelo:

1. Navegue para [na página do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Clique em **azuredeploy. JSON** para abrir o ficheiro de modelo.
3. Tenha em atenção o *osType* parâmetro listado abaixo. Este parâmetro é utilizado para selecionar as imagens VM para utilizar o servidor de base de dados, juntamente com o sistema de operativo várias as definições relacionadas.

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. Desloque para baixo para a lista de variáveis e verifique a definição para o **dbVMSetting** variáveis, listadas abaixo. Recebe um dos elementos contidos na matriz de **dbVMSettings** variável. Se estiver familiarizado com terminologia de desenvolvimento de software, pode ver o **dbVMSettings** variável como uma tabela hash ou um dicionário.

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. Suponha que decidir implementar VMs do Windows a executar o SQL Server no back-end. Em seguida, o valor para **osType** seria *Windows*e o **dbVMSetting** variável iria conter o elemento listado abaixo, que representa o primeiro valor no **dbVMSettings** variável.

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. Tenha em atenção o **vmSize** contém o valor *Standard_DS3*. Permitir que apenas determinados tamanhos de VM para a utilização de vários NICs. Pode verificar os tamanhos de VM suportam vários NICs ao ler o [tamanhos de Windows VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [tamanhos de VM com Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigos.

7. Desloque para baixo até **recursos** e repare o primeiro elemento. Descreve uma conta de armazenamento. Esta conta de armazenamento será utilizada para manter os discos de dados utilizados por cada VM de base de dados. Neste cenário, cada base de dados de VM tem um disco de SO armazenados no armazenamento normal e dois discos de dados armazenados no armazenamento SSD (premium).

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. Desloque para baixo para o recurso do seguinte, conforme listado abaixo. Este recurso representa a NIC utilizada para a base de dados acesso em cada base de dados de VM. Tenha em atenção a utilização do **cópia** funcionar neste recurso. O modelo permite-lhe implementar como várias VMs como quiser, com base no **dbCount** parâmetro. Por conseguinte, terá de criar a mesma quantidade de NICs para acesso de base de dados, uma para cada VM.

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. Desloque para baixo para o recurso do seguinte, conforme listado abaixo. Este recurso representa a NIC utilizada para a gestão de cada base de dados de VM. Novamente, precisa de um destes NICs para cada base de dados de VM. Tenha em atenção o **networkSecurityGroup** elemento, associar um NSG que permite o acesso a RDP/SSH a esta NIC apenas.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. Desloque para baixo para o recurso do seguinte, conforme listado abaixo. Este recurso representa um conjunto de disponibilidade para ser partilhado por todas as VMs de base de dados. Dessa forma, garante que sempre existirá uma VM no conjunto de execução durante a manutenção.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. Desloque para baixo para o recurso seguinte. Este recurso representa a base de dados VMs, conforme visto na primeira algumas linhas listadas abaixo. Tenha em atenção a utilização do **cópia** funcionar novamente, garantindo que várias VMs são criadas com base no **dbCount** parâmetro. Tenha também em atenção o **dependsOn** coleção. Lista dois NICs a ser necessárias para ser criado antes da implementação da VM, juntamente com o conjunto de disponibilidade e a conta de armazenamento.

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. Desloque para baixo no recurso de VM para o **networkProfile** elemento, conforme listado abaixo. Tenha em atenção que existem dois NICs a referência para cada VM. Quando cria vários NICs para uma VM, tem de definir o **primário** propriedade de um de NICs para *verdadeiro*e o restante para *falso*.

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implementar o modelo ARM com a função clique para implementar

> [!IMPORTANT]
> Certifique-se de que segue o [pré-requisitos](#Pre-requisites) passos antes de seguir as instruções abaixo.
> 

O modelo de exemplo disponível no repositório público utiliza um ficheiro de parâmetros com os valores predefinidos utilizados para gerar o cenário descrito acima. Para implementar este modelo utilizando, clique em implementar, siga [esta ligação](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), à direita do **grupo de recursos de back-end (consulte a documentação)** clique **implementar no Azure**, substitua o valores de parâmetro, se necessário predefinidos e siga as instruções no portal.

A figura abaixo mostra os conteúdos do novo grupo de recursos, após a implementação.

![Grupo de recursos de back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Implementar o modelo com o PowerShell
Para implementar o modelo que transferiu com o PowerShell, instalar e configurar o PowerShell, efetuando os passos a [instalar e configurar o PowerShell](/powershell/azure/overview) artigo e, em seguida, conclua os seguintes passos:

Execute o  **`New-AzureRmResourceGroup`**  cmdlet para criar um grupo de recursos utilizando o modelo.

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

Resultado esperado:

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar o modelo com a CLI do Azure
Para implementar o modelo com a CLI do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../cli-install-nodejs.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **`azure config mode`** para mudar para o modo Resource Manager, como mostrado abaixo.

    ```azurecli
    azure config mode arm
    ```

    O resultado esperado segue:

        info:    New mode is arm

3. Abra o [ficheiro de parâmetros](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), selecione o seu conteúdo e guardá-lo para um ficheiro no seu computador. Para este exemplo, guardámos o ficheiro de parâmetros em *parameters.json*.
4. Execute o cmdlet **`azure group deployment create`** para implementar a nova VNet com o modelo e os ficheiros de parâmetros que transferiu e alterou acima. A lista apresentada depois do resultado explica os parâmetros utilizados.

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    Resultado esperado:
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK


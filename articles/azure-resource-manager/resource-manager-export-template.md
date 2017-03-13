---
title: Exportar um modelo do Azure Resource Manager | Microsoft Docs
description: Utilize o Azure Resource Manager para exportar um modelo a partir de um grupo de recursos existente.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 5f5ca940-eef8-4125-b6a0-f44ba04ab5ab
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: f8512229ee30fee6315d8ba167f1716e40f79b3e
ms.lasthandoff: 03/06/2017


---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Exportar um modelo do Azure Resource Manager a partir de recursos existentes
O Gestor de Recursos permite-lhe exportar um modelo do Gestor de Recursos a partir de recursos existentes na sua subscrição. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe do modelo ou para automatizar a reimplementação da sua solução conforme necessário.

É importante ter em conta que existem duas formas diferentes de exportar um modelo:

* Pode exportar o modelo que foi utilizado para uma implementação. O modelo exportado inclui todos os parâmetros e variáveis exatamente como apareciam no modelo original. Esta abordagem é útil se tiver implementado recursos através do portal. Agora, pretende ver como construir o modelo para criar esses recursos.
* Pode exportar um modelo que representa o estado atual do grupo de recursos. O modelo exportado não é baseado em qualquer modelo utilizado para implementação. Em vez disso, cria um modelo que é um instantâneo do grupo de recursos. O modelo exportado tem muitos valores codificados e provavelmente não tantos parâmetros como normalmente seriam definidos. Esta abordagem é útil se tiver modificado o grupo de recursos através do portal ou de scripts. Agora, tem de capturar o grupo de recursos como modelo.

Este tópico mostra ambas as abordagens.

Neste tutorial, irá iniciar sessão no portal do Azure, criar uma conta de armazenamento e exportar o modelo para essa conta de armazenamento. Vai adicionar uma rede virtual para modificar o grupo de recursos. Por fim, vai exportar um novo modelo que representa o estado atual. Embora este artigo se concentre numa infraestrutura simplificada, pode utilizar estes mesmos passos para exportar um modelo para uma solução mais complexa.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
1. No [portal do Azure](https://portal.azure.com), selecione **Novo** > **Armazenamento** > **Conta de armazenamento**.
   
      ![criar armazenamento](./media/resource-manager-export-template/create-storage.png)
2. Crie uma conta do Storage com o nome **storage**, as suas iniciais e a data. O nome da conta do Storage tem de ser exclusivo em todo o Azure. Se o nome já estiver a ser utilizado, verá uma mensagem de erro a indicar isso. Tente uma variação. No grupo de recursos, selecione **Criar novo** e atribua-lhe o nome **ExportGroup**. Pode utilizar os valores predefinidos para as outras propriedades. Selecione **Criar**.
   
      ![fornecer valores para o armazenamento](./media/resource-manager-export-template/provide-storage-values.png)

A implementação pode demorar um minuto. Após a conclusão da implementação, a sua subscrição contém a conta do Storage.

## <a name="view-a-template-from-deployment-history"></a>Ver um modelo no histórico de implementações
1. Aceda ao painel do grupo de recursos para o novo grupo de recursos. Verifique que o painel mostra o resultado da última implementação. Selecione essa ligação.
   
      ![painel do grupo de recursos](./media/resource-manager-export-template/resource-group-blade.png)
2. Será apresentado um histórico das implementações do grupo. No seu caso, o painel mostra provavelmente mostra apenas uma implementação. Selecione essa implementação.
   
     ![última implementação](./media/resource-manager-export-template/last-deployment.png)
3. O painel apresenta um resumo da implementação. O resumo inclui o estado da implementação e as respetivas operações, bem como os valores que forneceu para os parâmetros. Para ver o modelo que utilizou para a implementação, selecione **Ver modelo**.
   
     ![ver resumo da implementação](./media/resource-manager-export-template/deployment-summary.png)
4. O Resource Manager obtém os seguintes seis ficheiros para si:
   
   1. **Modelo** - O modelo que define a infraestrutura para a sua solução. Quando criou a conta do Storage através do portal, o Resource Manager utilizou um modelo para a implementar e guardou esse modelo para consulta futura.
   2. **Parâmetros** - Um ficheiro de parâmetros que pode utilizar para transmitir valores durante a implementação. Contém os valores que indicou durante a primeira implementação, mas pode alterar qualquer um destes valores quando implementar novamente o modelo.
   3. **CLI** - Um ficheiro de script da interface de linha de comandos do Azure (CLI) que pode utilizar para implementar o modelo.
   3. **CLI 2.0** - Um ficheiro de script da interface de linha de comandos (CLI) do Azure que pode utilizar para implementar o modelo.
   4. **PowerShell** - Um ficheiro de script do Azure PowerShell que pode utilizar para implementar o modelo.
   5. **.NET** - Uma classe .NET que pode utilizar para implementar o modelo.
   6. **.NET** - Uma classe Ruby que pode utilizar para implementar o modelo.
      
      Os ficheiros estão disponíveis através de ligações no painel. Por predefinição, o painel mostra o modelo.
      
       ![ver modelo](./media/resource-manager-export-template/view-template.png)
      
      Vamos observar o modelo com atenção. O modelo deverá ter um aspeto semelhante ao seguinte:
      
      ```json
      {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "name": {
            "type": "String"
          },
          "accountType": {
            "type": "String"
          },
          "location": {
            "type": "String"
          },
          "encryptionEnabled": {
            "defaultValue": false,
            "type": "Bool"
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
              "name": "[parameters('accountType')]"
            },
            "kind": "Storage",
            "name": "[parameters('name')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
              "encryption": {
                "services": {
                  "blob": {
                    "enabled": "[parameters('encryptionEnabled')]"
                  }
                },
                "keySource": "Microsoft.Storage"
              }
            }
          }
        ]
      }
      ```

Este é o modelo utilizado para criar a conta de armazenamento. Repare que contém os parâmetros que permitem-lhe implementar os diferentes tipos de contas de armazenamento. Para saber mais sobre a estrutura de um modelo, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md). Para obter a lista completa das funções que pode utilizar num modelo, veja [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).

## <a name="add-a-virtual-network"></a>Adicionar uma rede virtual
O modelo que transferiu na secção anterior representava a infraestrutura dessa implementação original. No entanto, não irá ter em conta quaisquer alterações que efetuar após a implementação.
Para ilustrar este problema, vamos modificar o grupo de recursos ao adicionar uma rede virtual através do portal.

1. No painel do grupo de recursos, selecione **Adicionar**.
   
      ![adicionar recurso](./media/resource-manager-export-template/add-resource.png)
2. Selecione **Rede virtual** dos recursos disponíveis.
   
      ![selecionar uma rede virtual](./media/resource-manager-export-template/select-vnet.png)
3. Atribua à rede virtual o nome **VNET** e utilize os valores predefinidos para as outras propriedades. Selecione **Criar**.
   
      ![alerta de definição](./media/resource-manager-export-template/create-vnet.png)
4. Depois de a rede virtual ser implementada com êxito para o grupo de recursos, observe novamente o histórico de implementações. Agora verá duas implementações. Se não vir a segunda implementação, terá de fechar o painel do grupo de recursos e voltar a abri-lo. Selecione a implementação mais recente.
   
      ![histórico de implementações](./media/resource-manager-export-template/deployment-history.png)
5. Veja o modelo dessa implementação. Repare que define apenas a rede virtual. Não inclui a conta de armazenamento que implementou anteriormente. Já não tem um modelo que representa todos os recursos no seu grupo de recursos.

## <a name="export-the-template-from-resource-group"></a>Exportar o modelo a partir do grupo de recursos
Para obter o estado atual do seu grupo de recursos, exporte um modelo que mostra um instantâneo do grupo de recursos.  

> [!NOTE]
> Não pode exportar um modelo para um grupo de recursos que tenha mais de 200 recursos.
> 
> 

1. Para ver o modelo para um grupo de recursos, selecione **Script de automatização**.
   
      ![exportar grupo de recursos](./media/resource-manager-export-template/export-resource-group.png)
   
     Nem todos os tipos de recursos suportam a função de modelo de exportação. Se o grupo de recursos incluir apenas a conta de armazenamento e a rede virtual referidas neste artigo, não será apresentado nenhum erro. No entanto, se tiver criado outros tipos de recursos, pode aparecer um erro a indicar que existe um problema com a exportação. Pode saber como lidar com esses problemas na secção [Corrigir problemas de exportação](#fix-export-issues).
2. Verá novamente os seis ficheiros que pode utilizar para voltar a implementar a solução, mas, desta vez, o modelo é ligeiramente diferente. Este modelo tem apenas dois parâmetros: um para o nome da conta do Storage e outro para o nome da rede virtual.

  ```json
  "parameters": {
    "virtualNetworks_VNET_name": {
      "defaultValue": "VNET",
      "type": "String"
    },
    "storageAccounts_storagetf05092016_name": {
      "defaultValue": "storagetf05092016",
      "type": "String"
    }
  },
  ```
   
     O Resource Manager não obteve os modelos que utilizou durante a implementação. Em vez disso, gerou um novo modelo baseado na configuração atual dos recursos. Por exemplo, o modelo define a localização da conta de armazenamento e o valor da replicação para:

  ```json 
  "location": "northeurope",
  "tags": {},
  "properties": {
    "accountType": "Standard_RAGRS"
  },
  ```
3. Tem algumas opções para continuar a trabalhar com este modelo. Pode transferir o modelo e trabalhar no mesmo localmente com um editor JSON. Em alternativa, pode guardar o modelo na sua biblioteca e trabalhar no mesmo através do portal.
   
     Se estiver familiarizado com um editor JSON, como [Código VS](resource-manager-vs-code.md) ou [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), pode preferir transferir o modelo localmente e utilizar esse editor. Se não tiver um editor JSON, poderá preferir editar o modelo através do portal. O resto deste tópico parte do princípio de que guardou o modelo na sua biblioteca no portal. No entanto, realiza as mesmas alterações de sintaxe no modelo quer trabalhe localmente com um editor JSON quer trabalhe através do portal.
   
     Para trabalhar localmente, selecione **Transferir**.
   
      ![transferir modelo](./media/resource-manager-export-template/download-template.png)
   
     Para trabalhar através do portal, selecione **Adicionar à biblioteca**.
   
      ![adicionar à biblioteca](./media/resource-manager-export-template/add-to-library.png)
   
     Quando adicionar um modelo à biblioteca, atribua um nome e uma descrição ao modelo. Em seguida, selecione **Guardar**.
   
     ![definir os valores do modelo](./media/resource-manager-export-template/set-template-values.png)
4. Para ver um modelo guardado na biblioteca, selecione **Mais serviços**, escreva **Modelos** para filtrar os resultados e selecione **Modelos**.
   
      ![localizar modelos](./media/resource-manager-export-template/find-templates.png)
5. Selecione o modelo com o nome que guardou.
   
      ![selecionar modelo](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>Personalizar o modelo
O modelo exportado funcionará bem se pretender criar a mesma conta de armazenamento e rede virtual para cada implementação. No entanto, o Resource Manager fornece opções para que possa implementar modelos com uma flexibilidade muito maior. Por exemplo, durante a implementação, pode pretender especificar o tipo de conta do Storage a criar ou os valores a utilizar para o prefixo do endereço de rede virtual e o prefixo de sub-rede.

Nesta secção, adiciona parâmetros ao modelo exportado para que possa reutilizar o modelo ao implementar estes recursos noutros ambientes. Também adiciona algumas funcionalidades ao modelo para diminuir a probabilidade de encontrar um erro ao implementar o seu modelo. Já não tem de procurar um nome exclusivo para a sua conta de armazenamento. Em vez disso, o modelo cria um nome exclusivo. Restrinja os valores que podem ser especificados para o tipo de conta de armazenamento para apenas opções válidas.

1. Para personalizar o modelo, selecione **Editar**.
   
     ![mostrar modelo](./media/resource-manager-export-template/show-template.png)
2. Selecione o modelo.
   
     ![editar modelo](./media/resource-manager-export-template/edit-template.png)
3. Para poder passar os valores que pode pretender especificar durante a implementação, substitua a secção **parâmetro** por novas definições de parâmetros. Repare nos valores de **allowedValues** para **storageAccount_accountType**. Se fornecer acidentalmente um valor inválido, esse erro é reconhecido antes do início da implementação. Além disso, tenha em atenção que está a fornecer apenas um prefixo para o nome da conta do Storage e o prefixo está limitado a 11 carateres. Ao limitar o prefixo a 11 carateres, assegura que o nome completo não excede o número máximo de carateres para uma conta de armazenamento. O prefixo permite aplicar uma convenção de nomenclatura às contas do Storage. Poderá ver como criar um nome exclusivo no próximo passo.

  ```json
  "parameters": {
    "storageAccount_prefix": {
      "type": "string",
      "maxLength": 11
    },
    "storageAccount_accountType": {
      "defaultValue": "Standard_RAGRS",
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ]
    },
    "virtualNetwork_name": {
      "type": "string"
    },
    "addressPrefix": {
      "defaultValue": "10.0.0.0/16",
      "type": "string"
    },
    "subnetName": {
      "defaultValue": "subnet-1",
      "type": "string"
    },
    "subnetAddressPrefix": {
      "defaultValue": "10.0.0.0/24",
      "type": "string"
    }
  },
  ```

4. A secção **variables** do seu modelo está atualmente vazia. Na secção **variáveis**, pode criar valores que simplifiquem a sintaxe para o resto do seu modelo. Substitua esta secção por uma nova definição de variável. A variável **storageAccount_name** concatena o prefixo do parâmetro a uma cadeia exclusiva que é gerada com base no identificador do grupo de recursos. Já não tem de procurar um nome exclusivo quando fornecer um valor de parâmetro.

  ```json
  "variables": {
    "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
  },
  ```

5. Para utilizar os parâmetros e a variável nas definições de recursos, substitua a secção **recursos** por novas definições. Repare que pouco mudou nas definições de recursos, além do valor que é atribuído à propriedade de recursos. As propriedades são as mesmas do modelo exportado. Está simplesmente a atribuir propriedades a valores de parâmetros em vez de valores impostos. A localização dos recursos está definida para utilizar a mesma localização que o grupo de recursos através da expressão **resourceGroup().location**. A variável que criou para o nome da conta do Storage é referenciada através da expressão **variables**.

  ```json
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[parameters('virtualNetwork_name')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('subnetName')]",
            "properties": {
              "addressPrefix": "[parameters('subnetAddressPrefix')]"
            }
          }
        ]
      },
      "dependsOn": []
    },
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccount_name')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "accountType": "[parameters('storageAccount_accountType')]"
      },
      "dependsOn": []
    }
  ]
  ```

6. Selecione **OK** quando terminar de editar o modelo.
7. Selecione **Guardar** para guardar as alterações feitas ao modelo.
   
     ![guardar modelo](./media/resource-manager-export-template/save-template.png)
8. Para implementar o modelo atualizado, selecione **Implementar**.
   
     ![implementar modelo](./media/resource-manager-export-template/deploy-template.png)
9. Forneça os valores de parâmetros e selecione um novo grupo de recursos para implementar os recursos.

## <a name="update-the-downloaded-parameters-file"></a>Atualizar o ficheiro de parâmetros transferido
Se estiver a trabalhar com os ficheiros transferidos (em vez da biblioteca do portal), terá de atualizar o ficheiro de parâmetros transferido. Esse ficheiro já não corresponde aos parâmetros do seu modelo. Não é obrigatório utilizar um ficheiro de parâmetros, mas tal pode simplificar o processo quando volta a implementar um ambiente. Utilize os valores predefinidos que estão definidos no modelo para muitos dos parâmetros, de modo a que o ficheiro de parâmetros apenas necessite de dois valores.

Substitua o conteúdo do ficheiro parameters.json por:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

O ficheiro de parâmetros atualizado fornece valores apenas para os parâmetros que não tenham um valor predefinido. Pode fornecer valores para os outros parâmetros quando pretender utilizar um valor que seja diferente do valor predefinido.

## <a name="fix-export-issues"></a>Corrigir problemas de exportação
Nem todos os tipos de recursos suportam a função de modelo de exportação. O Resource Manager não exporta especificamente alguns tipos de recursos para impedir a exposição a dados confidenciais. Por exemplo, se tiver uma cadeia de ligação na sua configuração de site, provavelmente não pretende que seja apresentada explicitamente num modelo exportado. Para resolver este problema, adicione manualmente os recursos em falta no seu modelo.

> [!NOTE]
> Só encontra problemas de exportação se exportar a partir de um grupo de recursos em vez do seu histórico de implementação. Se a última implementação representar com precisão o estado atual do grupo de recursos, deve exportar o modelo do histórico de implementação em vez do grupo de recursos. Só deve exportar de um grupo de recursos depois de efetuar alterações ao grupo de recursos que não esteja definido num único modelo.
> 
> 

Por exemplo, se exportar um modelo para um grupo de recursos que contenha uma aplicação Web, Base de Dados SQL e uma cadeia de ligação na configuração do site, verá a seguinte mensagem:

![mostrar erro](./media/resource-manager-export-template/show-error.png)

Selecionar a mensagem irá mostrar-lhe exatamente que tipos de recursos não foram exportados. 

![mostrar erro](./media/resource-manager-export-template/show-error-details.png)

Este tópico mostra as correções comuns.

### <a name="connection-string"></a>Cadeia de ligação
No recurso de sites, adicione uma definição para a cadeia de ligação à base de dados:

```json
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>Extensão do site
No recurso de sites, adicione uma definição para o código para instalar:

```json
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>Extensão de máquina virtual
Para obter exemplos de extensões de máquina virtual, veja o artigo [Amostras de Configuração de Extensão do Azure Windows VM](../virtual-machines/virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="virtual-network-gateway"></a>Gateway de rede virtual
Adicione um tipo de recurso de gateway de rede virtual.

```json
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>Gateway de rede local
Adicione um tipo de recurso de gateway de rede local.

```json
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>Ligação
Adicione um tipo de recurso de ligação.

```json
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>Passos seguintes
Parabéns! Aprendeu a exportar um modelo a partir dos recursos que criou no portal.

* Pode implementar um modelo através do [PowerShell](resource-group-template-deploy.md), do [CLI do Azure](resource-group-template-deploy-cli.md) ou da [API REST](resource-group-template-deploy-rest.md).
* Para ver como exportar um modelo através do PowerShell, consulte [Utilizar o Azure PowerShell com o Azure Resource Manager](powershell-azure-resource-manager.md).
* Para ver como exportar um modelo através da CLI do Azure, consulte [Utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](xplat-cli-azure-resource-manager.md).



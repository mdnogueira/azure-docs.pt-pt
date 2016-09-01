<properties
    pageTitle="Exportar um modelo do Azure Resource Manager | Microsoft Azure"
    description="Utilize o Azure Resource Manager para exportar um modelo a partir de um grupo de recursos existente."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/03/2016"
    ms.author="tomfitz"/>

# Exportar um modelo do Azure Resource Manager a partir de recursos existentes

O Gestor de Recursos permite-lhe exportar um modelo do Gestor de Recursos a partir de recursos existentes na sua subscrição. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe do modelo ou para automatizar a reimplementação da sua solução conforme necessário.

É importante ter em conta que existem duas formas diferentes de exportar um modelo:

- Pode exportar o modelo que foi utilizado para uma implementação. O modelo exportado inclui todos os parâmetros e variáveis exatamente como apareciam no modelo original. Esta abordagem é útil se tiver implementado recursos através do portal. Agora, pretende ver como construir o modelo para criar esses recursos.
- Pode exportar um modelo que representa o estado atual do grupo de recursos. O modelo exportado não é baseado em qualquer modelo utilizado para implementação. Em vez disso, cria um modelo que é um instantâneo do grupo de recursos. O modelo exportado tem muitos valores codificados e provavelmente não tantos parâmetros como normalmente seriam definidos. Esta abordagem é útil se tiver modificado o grupo de recursos através do portal ou de scripts. Agora, tem de capturar o grupo de recursos como modelo.

Este tópico mostra ambas as abordagens. No artigo [Personalizar um modelo exportado do Azure Resource Manager](resource-manager-customize-template.md), verá como utilizar um modelo que gerou a partir do estado atual do grupo de recursos e torná-lo mais útil para voltar a implementar a solução.

Neste tutorial, irá iniciar sessão no portal do Azure, criar uma conta de armazenamento e exportar o modelo para essa conta de armazenamento. Vai adicionar uma rede virtual para modificar o grupo de recursos. Por fim, vai exportar um novo modelo que representa o estado atual. Embora este artigo se concentre numa infraestrutura simplificada, pode utilizar estes mesmos passos para exportar um modelo para uma solução mais complexa.

## Criar uma conta do Storage

1. No [Portal do Azure](https://portal.azure.com), selecione **Novo** > **Dados + Armazenamento** > **Conta do Storage**.

      ![criar armazenamento](./media/resource-manager-export-template/create-storage.png)

2. Crie uma conta do Storage com o nome **storage**, as suas iniciais e a data. O nome da conta do Storage tem de ser exclusivo em todo o Azure. Se tentar inicialmente um nome que já está a ser utilizado, experimente utilizar uma variação. Para o grupo de recursos, utilize **ExportGroup**. Pode utilizar os valores predefinidos para as outras propriedades. Selecione **Criar**.

      ![fornecer valores para o armazenamento](./media/resource-manager-export-template/provide-storage-values.png)

Após a conclusão da implementação, a sua subscrição contém a conta do Storage.

## Exportar o modelo a partir do histórico de implementação

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
   4. **PowerShell** - Um ficheiro de script do Azure PowerShell que pode utilizar para implementar o modelo.
   5. **.NET** - Uma classe .NET que pode utilizar para implementar o modelo.
   6. **.NET** - Uma classe Ruby que pode utilizar para implementar o modelo.

     Os ficheiros estão disponíveis através de ligações no painel. Por predefinição, o painel mostra o modelo.

       ![ver modelo](./media/resource-manager-export-template/view-template.png)

     Vamos observar o modelo com atenção. O modelo deverá ter um aspeto semelhante ao seguinte:

        {     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",     "contentVersion": "1.0.0.0",     "parameters": {       "name": {         "type": "String"       },       "accountType": {         "type": "String"       },       "location": {         "type": "String"       },       "encryptionEnabled": {         "defaultValue": false,         "type": "Bool"       }     },     "resources": [       {         "type": "Microsoft.Storage/storageAccounts",         "sku": {           "name": "[parameters('accountType')]"         },         "kind": "Storage",         "name": "[parameters('name')]",         "apiVersion": "2016-01-01",         "location": "[parameters('location')]",         "properties": {           "encryption": {             "services": {               "blob": {                 "enabled": "[parameters('encryptionEnabled')]"               }             },             "keySource": "Microsoft.Storage"           }         }       }     ]   }
 
Este é o modelo utilizado para criar a conta de armazenamento. Repare que contém os parâmetros que permitem-lhe implementar os diferentes tipos de contas de armazenamento. Para saber mais sobre a estrutura de um modelo, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md). Para obter a lista completa das funções que pode utilizar num modelo, veja [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).


## Adicionar uma rede virtual

O modelo que transferiu na secção anterior representava a infraestrutura dessa implementação original. No entanto, não irá ter em conta quaisquer alterações que efetuar após a implementação.
Para ilustrar este problema, vamos modificar o grupo de recursos ao adicionar uma rede virtual através do portal.

1. No painel do grupo de recursos, selecione **Adicionar**.

      ![adicionar recurso](./media/resource-manager-export-template/add-resource.png)

2. Selecione **Rede virtual** dos recursos disponíveis.

      ![selecionar uma rede virtual](./media/resource-manager-export-template/select-vnet.png)

2. Atribua à rede virtual o nome **VNET** e utilize os valores predefinidos para as outras propriedades. Selecione **Criar**.

      ![alerta de definição](./media/resource-manager-export-template/create-vnet.png)

3. Depois de a rede virtual ser implementada com êxito para o grupo de recursos, observe novamente o histórico de implementações. Agora verá duas implementações. Se não vir a segunda implementação, terá de fechar o painel do grupo de recursos e voltar a abri-lo. Selecione a implementação mais recente.

      ![histórico de implementações](./media/resource-manager-export-template/deployment-history.png)

4. Observe o modelo dessa implementação. Repare que este define apenas as alterações que efetuou para adicionar a rede virtual.

Normalmente, é melhor trabalhar com um modelo que implementa toda a infraestrutura para a sua solução numa única operação. Esta abordagem é mais fiável do que recordar muitos modelos diferentes para implementar.


## Exportar o modelo a partir do grupo de recursos

Embora cada implementação mostre apenas as alterações que efetuou ao seu grupo de recursos, pode exportar um modelo em qualquer altura para mostrar os atributos de todo o grupo de recursos.  

1. Para ver o modelo para um grupo de recursos, selecione **Script de automatização**.

      ![exportar grupo de recursos](./media/resource-manager-export-template/export-resource-group.png)

     Nem todos os tipos de recursos suportam a função de modelo de exportação. Se o grupo de recursos incluir apenas a conta de armazenamento e a rede virtual referidas neste artigo, não verá um erro. No entanto, se tiver criado outros tipos de recursos, pode aparecer um erro a indicar que existe um problema com a exportação. Pode saber como lidar com esses problemas na secção [Corrigir problemas de exportação](#fix-export-issues).

      

2. Verá novamente os seis ficheiros que pode utilizar para voltar a implementar a solução, mas, desta vez, o modelo é ligeiramente diferente. Este modelo tem apenas dois parâmetros: um para o nome da conta do Storage e outro para o nome da rede virtual.

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

     O Resource Manager não obteve os modelos que utilizou durante a implementação. Em vez disso, gerou um novo modelo baseado na configuração atual dos recursos. Por exemplo, o modelo define a localização da conta de armazenamento e o valor da replicação para:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Transfira o modelo para que possa trabalhar no mesmo localmente.

      ![transferir modelo](./media/resource-manager-export-template/download-template.png)

4. Localize o ficheiro .zip que transferiu e extraia os conteúdos. Pode utilizar este modelo transferido para voltar a implementar a infraestrutura.

## Corrigir problemas de exportação

Nem todos os tipos de recursos suportam a função de modelo de exportação. O Resource Manager não exporta especificamente alguns tipos de recursos para impedir a exposição a dados confidenciais. Por exemplo, se tiver uma cadeia de ligação na sua configuração de site, provavelmente não pretende que seja apresentada explicitamente num modelo exportado. Pode contornar este problema adicionando manualmente os recursos em falta no seu modelo.

> [AZURE.NOTE] Só encontra problemas de exportação se exportar a partir de um grupo de recursos em vez do seu histórico de implementação. Se a última implementação representar com precisão o estado atual do grupo de recursos, deve exportar o modelo do histórico de implementação em vez do grupo de recursos. Só deve exportar de um grupo de recursos depois de efetuar alterações ao grupo de recursos que não esteja definido num único modelo.

Por exemplo, se exportar um modelo para um grupo de recursos que contenha uma aplicação Web, Base de Dados do SQL Server e uma cadeia de ligação na configuração do site, verá a seguinte mensagem.

![mostrar erro](./media/resource-manager-export-template/show-error.png)

Selecionar a mensagem irá mostrar-lhe exatamente que tipos de recursos não foram exportados. 
     
![mostrar erro](./media/resource-manager-export-template/show-error-details.png)

Este tópico mostra as seguintes correções comuns. Para implementar estes recursos, terá de adicionar parâmetros ao modelo. Para obter mais informações, veja [Personalizar e voltar a implementar um modelo exportado](resource-manager-customize-template.md).

### Cadeia de ligação

No recurso de sites, adicione uma definição para a cadeia de ligação à base de dados:

```
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

### Extensão do site

No recurso de sites, adicione uma definição para o código para instalar:

```
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

### Extensão de máquina virtual

Para obter exemplos de extensões de máquina virtual, veja o artigo [Amostras de Configuração de Extensão do Azure Windows VM](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### Gateway de rede virtual

Adicione um tipo de recurso de gateway de rede virtual.

```
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

### Gateway de rede local

Adicione um tipo de recurso de gateway de rede local.

```
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

### Ligação

Adicione um tipo de recurso de ligação.

```
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


## Passos seguintes

Parabéns! Aprendeu a exportar um modelo a partir dos recursos que criou no portal.

- Na segunda parte deste tutorial personaliza o modelo que transferiu, ao adicionar mais parâmetros e voltar a implementá-lo através de um script. Veja [Personalizar e voltar a implementar um modelo exportado](resource-manager-customize-template.md).
- Para ver como exportar um modelo através do PowerShell, consulte [Utilizar o Azure PowerShell com o Azure Resource Manager](powershell-azure-resource-manager.md).
- Para ver como exportar um modelo através da CLI do Azure, consulte [Utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](xplat-cli-azure-resource-manager.md).



<!--HONumber=ago16_HO4-->



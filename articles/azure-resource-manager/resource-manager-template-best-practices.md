---
title: "Melhores práticas para a criação de modelos do Resource Manager | Microsoft Docs"
description: Diretrizes para simplificar os modelos Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.openlocfilehash: a23301ba88279af3f7bf4d353ae808e9eeb0900d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Melhores práticas para a criação de modelos Azure Resource Manager
Estas diretrizes podem ajudar a criar os modelos Azure Resource Manager que são fiáveis e fáceis de utilizar. As diretrizes são apenas de sugestões. Não são os requisitos, exceto se encontra onde indicado. O cenário poderá necessitar de uma variação de uma das seguintes abordagens ou exemplos.

## <a name="resource-names"></a>Nomes de recursos
Geralmente, trabalha com três tipos de nomes de recursos no Gestor de recursos:

* Nomes de recursos que têm de ser exclusivos.
* Os nomes de recursos que não têm de ser exclusivo, mas optar por fornecer um nome que pode ajudar a identificar um recurso com base no contexto.
* Nomes de recursos que podem ser genéricos.

 Para obter informações sobre as restrições de nome de recurso, consulte [recomendado convenções de nomenclatura para recursos do Azure](../guidance/guidance-naming-conventions.md).

### <a name="unique-resource-names"></a>Nomes exclusivos de recursos
Tem de fornecer um nome exclusivo do recurso para qualquer tipo de recurso que tenha um ponto final de acesso de dados. Alguns tipos de recursos comuns que necessitam de um nome exclusivo incluem:

* Armazenamento do Azure<sup>1</sup> 
* Funcionalidade de Aplicações Web no Serviço de Aplicações do Azure
* SQL Server
* Azure Key Vault
* Cache de Redis do Azure
* Azure Batch
* Traffic Manager do Azure
* Azure Search
* O Azure HDInsight

<sup>1</sup> os nomes das contas de armazenamento também tem de ser em minúsculas, 24 carateres ou menos e não têm qualquer hífenes.

Se fornecer um parâmetro para um nome de recurso, tem de fornecer um nome exclusivo ao implementar o recurso. Opcionalmente, pode criar uma variável que utiliza o [uniqueString()](resource-group-template-functions-string.md#uniquestring) função para gerar um nome. 

Também poderá pretender adicionar um prefixo ou sufixo para o **uniqueString** resultado. Modificar o nome exclusivo pode ajudá-lo mais fácil identificar o tipo de recurso do nome. Por exemplo, pode gerar um nome exclusivo para uma conta de armazenamento utilizando a seguinte variável:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nomes de recursos para identificação
Alguns tipos de recursos, que pode querer nome, mas os respetivos nomes tem de ser exclusivo. Estes tipos de recurso, pode fornecer um nome que identifique o contexto de recursos e o tipo de recurso. Forneça um nome descritivo que o ajuda a identificar o recurso de uma lista de recursos. Se precisar de utilizar um nome de recurso diferente para diferentes implementações, pode utilizar um parâmetro para o nome:

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

Se não pretender passar num nome durante a implementação, pode utilizar uma variável: 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

Também pode utilizar um valor hard-coded:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>Nomes de recursos genérico
Tipos de recurso que principalmente aceder através de um recurso diferente, pode utilizar um nome genérico que é "hard-coded" no modelo. Por exemplo, pode definir um nome genérico padrão das regras de firewall num SQL server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>Parâmetros
As seguintes informações podem ser úteis quando trabalha com parâmetros:

* Estará a minimizar a utilização de parâmetros. Sempre que possível, utilize uma variável ou um valor literal. Utilize parâmetros apenas para estes cenários:
   
   * Definições que pretende utilizar variações de acordo com o ambiente (SKU, tamanho, capacidade).
   * Nomes de recursos que pretende especificar para fins de identificação fácil.
   * Valores que utilizar com frequência para realizar outras tarefas (por exemplo, um nome de utilizador de admin).
   * Segredos (tal como as palavras-passe).
   * O número ou a matriz de valores a utilizar quando criar várias instâncias de um tipo de recurso.
* Utilize camel case para os nomes dos parâmetros.
* Forneça uma descrição de cada parâmetro nos metadados:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Defina os valores predefinidos para os parâmetros (exceto para palavras-passe e chaves SSH):
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Utilize **SecureString** para todas as palavras-passe e segredos: 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Sempre que possível, não utilize um parâmetro para especificar a localização. Em alternativa, utilize o **localização** propriedade do grupo de recursos. Utilizando o **resourceGroup (). location** expressão para todos os recursos, recursos do modelo são implementados na mesma localização que o grupo de recursos:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Se um tipo de recurso é suportado em apenas um número limitado de localizações, pode pretender especificar uma localização válida diretamente no modelo. Se tiver de utilizar um **localização** parâmetro, partilham esse valor de parâmetro quanto possível com os recursos que são provável que esteja na mesma localização. Isto minimiza o número de vezes que os utilizadores são-lhe pedidos para fornecer informações de localização.
* Evite utilizar um parâmetro ou variável para a versão da API para um tipo de recurso. Propriedades de recursos e os valores podem variar pelo número de versão. O IntelliSense num editor de código não é possível determinar o esquema correto quando a versão de API está definida como um parâmetro ou variável. Em vez disso, versão de codificar a API no modelo.

## <a name="variables"></a>Variáveis
As seguintes informações podem ser úteis quando trabalha com variáveis:

* Utilize as variáveis para valores que tem de utilizar mais do que uma vez num modelo. Se for utilizado um valor apenas uma vez, um valor hard-coded faz com que o seu modelo mais fáceis de ler.
* Não é possível utilizar o [referência](resource-group-template-functions-resource.md#reference) funcionar o **variáveis** secção do modelo. O **referência** deriva de função de valor de estado de runtime do recurso. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construção valores que tem o **referência** funcionarem diretamente no **recursos** ou **produz** secção do modelo.
* Incluir variáveis para nomes de recursos que têm de ser exclusivos, conforme descrito em [os nomes de recursos](#resource-names).
* Pode agrupar variáveis em objetos complexos. Utilize o **variable.subentry** formato para fazer referência a um valor de um objeto complexo. As variáveis de agrupamento pode ajudar a controlar variáveis relacionadas. Melhora também a legibilidade do modelo. Eis um exemplo:
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > Um objeto complexo não pode conter uma expressão que referencia um valor de um objeto complexo. Defina uma variável separada para esta finalidade.
   > 
   > 
   
     Para obter exemplos avançados da utilização de objetos complexos como variáveis, consulte [partilhar Estado em modelos do Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="resources"></a>Recursos
As seguintes informações podem ser úteis quando trabalhar com recursos:

* Para ajudar outros contribuintes compreender a finalidade do recurso, especifique **comentários** para cada recurso no modelo:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Pode utilizar etiquetas para adicionar metadados a recursos. Utilize os metadados para adicionar informações sobre os seus recursos. Por exemplo, pode adicionar metadados para registar os detalhes de faturação para um recurso. Para obter mais informações, consulte [utilizar etiquetas para organizar os recursos do Azure](resource-group-using-tags.md).
* Se utilizar um *ponto final público* no seu modelo (por exemplo, um Blob do Azure armazenamento ponto final público), *efetue não codificar* o espaço de nomes. Utilize o **referência** função dinamicamente obter o espaço de nomes. Pode utilizar esta abordagem para implementar o modelo para ambientes de espaço de nomes pública diferente, sem alterar manualmente o ponto final no modelo. Defina a versão da API para a mesma versão que está a utilizar para a conta de armazenamento no seu modelo:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se a conta de armazenamento é implementada no mesmo modelo que está a criar, não terá de especificar o espaço de nomes do fornecedor ao referenciam o recurso. Esta é a sintaxe simplificada:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se tiver outros valores no modelo que estão configurados para utilizar um espaço de nomes público, alterar estes valores para refletir o mesmo **referência** função. Por exemplo, pode definir o **storageUri** propriedade do perfil de diagnóstico de máquina virtual:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Também pode referenciar uma conta de armazenamento existente que esteja no grupo de recursos diferente:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Atribua endereços IP públicos para uma máquina virtual, apenas quando uma aplicação exige. Para ligar a uma máquina virtual (VM) de depuração, ou para fins administrativos ou de gestão, utilize regras NAT de entrada, um gateway de rede virtual ou um jumpbox.
   
     Para obter mais informações sobre como ligar a máquinas virtuais, consulte:
   
   * [Executar as VMs para uma arquitetura de N camadas no Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurar o acesso de WinRM para VMs no Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Permitir o acesso externo à VM através do portal do Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Permitir o acesso externo à sua VM utilizando o PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Permitir o acesso externo para a VM com Linux utilizando a CLI do Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* O **domainNameLabel** propriedade para endereços IP públicos têm de ser exclusiva. O **domainNameLabel** valor tem de ter entre 3 e 63 carateres de comprimento e siga as regras especificadas por esta expressão regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Porque o **uniqueString** função gera uma cadeia que é de 13 carateres de comprimento, a **dnsPrefixString** parâmetro está limitado a 50 carateres:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Quando adiciona uma palavra-passe para uma extensão de script personalizado, utilize o **commandToExecute** propriedade no **protectedSettings** propriedade:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Para garantir que os segredos são encriptados quando estes são transmitidos como parâmetros para VMs e as extensões, utilize o **protectedSettings** propriedade das extensões relevantes.
   > 
   > 

## <a name="outputs"></a>saídas
Se utilizar um modelo para criar os endereços IP públicos, incluir uma **produz** secção que devolve detalhes do endereço IP e o nome de domínio completamente qualificado (FQDN). Pode utilizar valores de saída para obter detalhes sobre o público endereços IP e FQDNs facilmente após a implementação. Quando referenciar o recurso, utilize a versão de API que utilizou para criá-la: 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>Modelo único vs modelos aninhados
Para implementar a solução, pode utilizar um único modelo ou um modelo de principal com vários modelos aninhados. Modelos aninhados são comuns para cenários mais avançados. Utilizar um modelo aninhado dá-lhe as seguintes vantagens:

* Pode dividir uma solução para componentes de destino.
* Pode reutilizar aninhados modelos com modelos principais diferentes.

Se optar por utilizar modelos aninhados, as diretrizes seguintes podem ajudá-lo normalizar a estrutura do modelo. Estas diretrizes baseiam-se no [padrões de conceção de modelos Azure Resource Manager para](best-practices-resource-manager-design-templates.md). Recomendamos uma estrutura que tem os seguintes modelos:

* **Modelo principal** (azuredeploy. JSON). Utilize para os parâmetros de entrada.
* **Modelo de recursos partilhados**. Utilizar para implementar recursos partilhados que utilizam todos os outros recursos (por exemplo, os conjuntos virtuais de rede e disponibilidade). Utilize o **dependsOn** expressão para se certificar de que este modelo é implementado antes de outros modelos.
* **Modelo de recursos opcional**. Utilize para condicionalmente implementar recursos com base num parâmetro (por exemplo, um jumpbox).
* **Modelo de recursos do membro**. Cada tipo de instância dentro de uma camada de aplicação tem a respetiva configuração. Dentro de uma camada, pode definir os tipos de instância diferente. (Por exemplo, a primeira instância cria um cluster, e instâncias adicionais são adicionadas ao cluster existente.) Cada tipo de instância tem o seu próprio modelo de implementação.
* **Scripts**. Scripts reutilizáveis amplamente são aplicáveis a cada tipo de instância (por exemplo, inicializar e formatar adicionais discos). Scripts personalizados que criar para um objetivo de personalização específicas são diferentes, consoante o tipo de instância.

![Modelo aninhado](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Para obter mais informações, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-templates"></a>Condicionalmente ligação aos modelos aninhadas
Pode utilizar um parâmetro condicionalmente ligar aos modelos aninhados. O parâmetro passa a fazer parte do URI para o modelo:

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>Formato de modelo
É uma boa prática para passar o modelo através de uma validação JSON. Um validador pode ajudar a remover vírgulas supérfluas, parênteses e parênteses Retos que poderão originar um erro durante a implementação. Tente [JSONLint](http://jsonlint.com/) ou edição de um pacote de linter para favorito do seu ambiente (Visual Studio Code, Atom, Sublime Text, Visual Studio).

Também é uma boa ideia para formatar o seu JSON para legibilidade melhor. Pode utilizar um pacote de formatador JSON para o seu editor local. No Visual Studio, para formatar o documento, prima **Ctrl + K, Ctrl + D**. No Visual Studio Code, prima **Alt + Shift + F**. Se o seu editor local não formatar o documento, pode utilizar um [formatador online](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Passos seguintes
* Para obter orientações sobre a arquitetura da sua solução de máquinas virtuais, consulte [executar uma VM do Windows no Azure](../guidance/guidance-compute-single-vm.md) e [executar uma VM com Linux no Azure](../guidance/guidance-compute-single-vm-linux.md).
* Para obter orientações sobre como configurar uma conta de armazenamento, consulte [lista de verificação de armazenamento do Azure, desempenho e escalabilidade](../storage/common/storage-performance-checklist.md).
* Para saber mais sobre como uma empresa pode utilizar o Gestor de recursos para gerir subscrições de forma eficaz, consulte [andaime enterprise do Azure: governação de subscrição prescritiva](resource-manager-subscription-governance.md).


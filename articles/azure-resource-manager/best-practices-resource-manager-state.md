---
title: Passar valores complexos entre os modelos do Azure | Microsoft Docs
description: Mostra recomendado abordagens para partilhar os dados de estado com modelos Azure Resource Manager e modelos ligados utilizando os objetos complexos.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fd2f5e2d-d56d-4e01-a57d-34f3eaead4a9
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: tomfitz
ms.openlocfilehash: e163f3c2e9a78b057dc2a7a42924c59d0aac3fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="share-state-to-and-from-azure-resource-manager-templates"></a>Estado de partilha e para os modelos Azure Resource Manager
Este tópico mostra as melhores práticas para gerir e partilhar Estado nos modelos. Os parâmetros e variáveis apresentadas neste tópico são exemplos dos tipos de objetos que pode definir para comodamente organizar os seus requisitos de implementação. Destes exemplos, pode implementar os seus próprios objetos com valores de propriedade que fazem sentido para o seu ambiente.

Este tópico faz parte de um documento maior. Para ler o documento completo, transfira [mundo classe Resource Manager modelos considerações e práticas comprovada](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## <a name="provide-standard-configuration-settings"></a>Forneça definições de configuração padrão
Em vez de oferecer um modelo que proporciona flexibilidade total e variações countless, um padrão comum consiste em fornecer uma seleção de configurações conhecidas. Em vigor, os utilizadores podem selecionar tamanhos de t-shirt padrão, tais como sandbox, pequena, média e grande. Outros exemplos de tamanhos de t-shirt são ofertas de produtos, tais como a Comunidade edition ou enterprise edition. Noutros casos, poderá ser específico da carga de trabalho configurações uma tecnologia de – como reduzir o mapa ou sem SQL Server.

Com os objetos complexos, pode criar variáveis que contêm as recolhas de dados, por vezes conhecidas como "matrizes de propriedades" e utilizar esses dados para orientar a declaração de recursos no seu modelo. Esta abordagem fornece uma boas, conhecidas configurações de diferentes tamanhos que estão pré-configuradas para clientes. Sem configurações conhecidas, os utilizadores do modelo tem de determinar cluster Dimensionar por si próprios, fator restrições de recursos de plataforma e fazer bibliotecas para identificar a criação de partições resultante de contas de armazenamento e outros recursos (devido a restrições de tamanho e de recursos de cluster). Para além de fazer uma melhor experiência do cliente, algumas configurações de conhecidos são mais fáceis de suporte e pode ajudar a fornecer um nível mais elevado de densidade.

O exemplo seguinte mostra como definir as variáveis que contêm objetos complexos para que representa as recolhas de dados. As coleções definem valores que são utilizados para o tamanho da máquina virtual, as definições de rede, definições do sistema operativo e definições de disponibilidade.

```json
"variables": {
  "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
  "tshirtSizeSmall": {
    "vmSize": "Standard_A1",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
    "vmCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 1,
      "pool": "db",
      "map": [0,0],
      "jumpbox": 0
    }
  },
  "tshirtSizeMedium": {
    "vmSize": "Standard_A3",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
    "vmCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 2,
      "pool": "db",
      "map": [0,1],
      "jumpbox": 0
    }
  },
  "tshirtSizeLarge": {
    "vmSize": "Standard_A4",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
    "vmCount": 3,
    "slaveCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 2,
      "pool": "db",
      "map": [0,1,1],
      "jumpbox": 0
    }
  },
  "osSettings": {
    "scripts": [
      "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
    ],
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer",
      "sku": "14.04.2-LTS",
      "version": "latest"
    }
  },
  "networkSettings": {
    "vnetName": "[parameters('virtualNetworkName')]",
    "addressPrefix": "10.0.0.0/16",
    "subnets": {
      "dmz": {
        "name": "dmz",
        "prefix": "10.0.0.0/24",
        "vnet": "[parameters('virtualNetworkName')]"
      },
      "data": {
        "name": "data",
        "prefix": "10.0.1.0/24",
        "vnet": "[parameters('virtualNetworkName')]"
      }
    }
  },
  "availabilitySetSettings": {
    "name": "pgsqlAvailabilitySet",
    "fdCount": 3,
    "udCount": 5
  }
}
```

Tenha em atenção que o **tshirtSize** variável concatena o tamanho de t-shirt fornecido através de um parâmetro (**pequeno**, **média**, **grande**) para o texto **tshirtSize**. Utilize esta variável para obter a variável de associou o objeto complexo para esse tamanho t-shirt.

Em seguida, pode referenciar estas variáveis mais tarde no modelo. A capacidade para referenciar variáveis com o nome e as respetivas propriedades simplifica a sintaxe do modelo e torna mais fácil de compreender contexto. O exemplo seguinte define um recurso implementar utilizando os objetos apresentados anteriormente para definir os valores. Por exemplo, o tamanho da VM é definido por obter o valor para `variables('tshirtSize').vmSize` enquanto o valor para o tamanho do disco é obtido `variables('tshirtSize').diskSize`. Além disso, o URI para um modelo ligado está definido com o valor para `variables('tshirtSize').vmTemplate`.

```json
"name": "master-node",
"type": "Microsoft.Resources/deployments",
"apiVersion": "2015-01-01",
"dependsOn": [
    "[concat('Microsoft.Resources/deployments/', 'shared')]"
],
"properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[variables('tshirtSize').vmTemplate]",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "value": "[parameters('adminPassword')]"
      },
      "replicatorPassword": {
        "value": "[parameters('replicatorPassword')]"
      },
      "osSettings": {
        "value": "[variables('osSettings')]"
      },
      "subnet": {
        "value": "[variables('networkSettings').subnets.data]"
      },
      "commonSettings": {
        "value": {
          "region": "[parameters('region')]",
          "adminUsername": "[parameters('adminUsername')]",
          "namespace": "ms"
        }
      },
      "storageSettings": {
        "value":"[variables('tshirtSize').storage]"
      },
      "machineSettings": {
        "value": {
          "vmSize": "[variables('tshirtSize').vmSize]",
          "diskSize": "[variables('tshirtSize').diskSize]",
          "vmCount": 1,
          "availabilitySet": "[variables('availabilitySetSettings').name]"
        }
      },
      "masterIpAddress": {
        "value": "0"
      },
      "dbType": {
        "value": "MASTER"
      }
    }
  }
}
```

## <a name="pass-state-to-a-template"></a>Estado de passagem para um modelo
Partilhar o estado a um modelo através de parâmetros que fornecem diretamente durante a implementação.

A tabela seguinte lista os parâmetros frequentemente utilizados em modelos.

| Nome | Valor | Descrição |
| --- | --- | --- |
| localização |Cadeia de uma lista de regiões do Azure restrita |A localização onde os recursos são implementados. |
| storageAccountNamePrefix |Cadeia |Nome DNS exclusivo para a conta do Storage onde os discos desta VM são colocados |
| domainName |Cadeia |Nome de domínio do jumpbox acessível publicamente VM no formato: **{domainName}. { location}.cloudapp.com** por exemplo: **mydomainname.westus.cloudapp.azure.com** |
| adminUsername |Cadeia |Nome de utilizador para as máquinas virtuais |
| adminPassword |Cadeia |Palavra-passe para as máquinas virtuais |
| tshirtSize |Cadeia de uma lista de tamanhos de t-shirt oferecido restrita |O tamanho da unidade de escala com nome para aprovisionar. Por exemplo, "Pequeno", "médio", "Grande" |
| virtualNetworkName |Cadeia |Nome da rede virtual que o consumidor que pretende utilizar. |
| enableJumpbox |Cadeia de uma lista restrita (ativado/desativado) |Parâmetro que identifica se pretende ativar uma jumpbox para o ambiente. Valores: "activado", "desativado" |

O **tshirtSize** parâmetro utilizado na secção anterior está definido como:

```json
"parameters": {
  "tshirtSize": {
    "type": "string",
    "defaultValue": "Small",
    "allowedValues": [
      "Small",
      "Medium",
      "Large"
    ],
    "metadata": {
      "Description": "T-shirt size of the MongoDB deployment"
    }
  }
}
```

## <a name="pass-state-to-linked-templates"></a>Estado de passar para modelos ligados
Ao ligar a modelos ligados, muitas vezes, utilizar uma combinação de estática e gerou variáveis.

### <a name="static-variables"></a>Variáveis estáticas
Estáticas variáveis são frequentemente utilizadas para fornecer valores base, tais como URLs, o que são utilizados ao longo de um modelo.

No seguinte excerpt de modelo, `templateBaseUrl` Especifica a localização de raiz para o modelo no GitHub. A linha seguinte cria uma nova variável `sharedTemplateUrl` que concatena o URL de base com o nome do modelo de recursos partilhados conhecido. Abaixo dessa linha, uma variável de objeto complexo é utilizada para armazenar um tamanho de t-shirt, em que o URL de base é concatenado para a localização do modelo de configuração conhecido e armazenado no `vmTemplate` propriedade.

A vantagem desta abordagem é que se alterar a localização do modelo, apenas precisar de alterar a variável estática num único local, passa-a em todos os modelos ligados.

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "tshirtSizeSmall": {
    "vmSize": "Standard_A1",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
    "vmCount": 2,
    "slaveCount": 1,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 1,
      "pool": "db",
      "map": [0,0],
      "jumpbox": 0
    }
  }
}
```

### <a name="generated-variables"></a>Variáveis geradas
Para além das variáveis estáticas, várias variáveis são geradas dinamicamente. Esta secção identifica alguns dos tipos comuns de variáveis gerados.

#### <a name="tshirtsize"></a>tshirtSize
Está familiarizado com esta variável gerado dos exemplos acima.

#### <a name="networksettings"></a>networkSettings
Capacidade, capacidade ou modelo de solução ponto-a-ponto de âmbito, os modelos ligados normalmente criam recursos de que existem numa rede. Uma abordagem simples é utilizar um objeto complexo para armazenar definições de rede e transmita-los para modelos ligados.

Um exemplo de comunicar as definições de rede pode ser visto abaixo.

```json
"networkSettings": {
  "vnetName": "[parameters('virtualNetworkName')]",
  "addressPrefix": "10.0.0.0/16",
  "subnets": {
    "dmz": {
      "name": "dmz",
      "prefix": "10.0.0.0/24",
      "vnet": "[parameters('virtualNetworkName')]"
    },
    "data": {
      "name": "data",
      "prefix": "10.0.1.0/24",
      "vnet": "[parameters('virtualNetworkName')]"
    }
  }
}
```

#### <a name="availabilitysettings"></a>availabilitySettings
Recursos criados no modelos ligados, muitas vezes, são colocados num conjunto de disponibilidade. No exemplo seguinte, é especificado o nome do conjunto de disponibilidade e também o domínio de falhas e o domínio de atualização de contagem de utilizar.

```json
"availabilitySetSettings": {
  "name": "pgsqlAvailabilitySet",
  "fdCount": 3,
  "udCount": 5
}
```

Se precisar de vários conjuntos de disponibilidade (por exemplo, um de nós principais) e outra para nós de dados, pode utilizar um nome como um prefixo, especifique vários conjuntos de disponibilidade ou, siga o modelo apresentado anteriormente para a criação de uma variável para um tamanho de t-shirt específico.

#### <a name="storagesettings"></a>storageSettings
Detalhes de armazenamento, muitas vezes, são partilhados com modelos ligados. No exemplo abaixo, um *storageSettings* objeto fornece detalhes sobre os nomes de conta e contentor de armazenamento.

```json
"storageSettings": {
    "vhdStorageAccountName": "[parameters('storageAccountName')]",
    "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
    "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
}
```

#### <a name="ossettings"></a>osSettings
Com modelos ligados, poderá ter passar definições do sistema operativo para vários tipos de nós em tipos conhecidos de configuração diferente. Um objeto complexo é uma forma fácil para armazenar e partilhar informações do sistema operativo e também torna mais fácil de suportar várias opções do sistema operativo para a implementação.

O exemplo seguinte mostra um objeto para *osSettings*:

```json
"osSettings": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "14.04.2-LTS",
    "version": "latest"
  }
}
```

#### <a name="machinesettings"></a>machineSettings
Uma variável gerada, *machineSettings* é um objeto complexo que contém uma mistura de variáveis de núcleos para criar uma VM. As variáveis de incluem nome de utilizador de administrador e a palavra-passe, um prefixo para os nomes VM e uma referência de imagem do sistema operativo.

```json
"machineSettings": {
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "machineNamePrefix": "mongodb-",
    "osImageReference": {
        "publisher": "[variables('osFamilySpec').imagePublisher]",
        "offer": "[variables('osFamilySpec').imageOffer]",
        "sku": "[variables('osFamilySpec').imageSKU]",
        "version": "latest"
    }
},
```

Tenha em atenção que *osImageReference* obtém os valores do *osSettings* variável definida no modelo principal. Isto significa que pode alterar facilmente o sistema operativo para uma VM — inteiramente ou com base nas preferências de um consumidor de modelo.

#### <a name="vmscripts"></a>vmScripts
O *vmScripts* objeto contém os detalhes sobre os scripts para transferir e executar uma instância VM, incluindo dentro e fora de referências. Fora referências incluem a infraestrutura.
Referências de interior incluem a instalação de software instalado e a configuração.

Utilizar o *scriptsToDownload* propriedade para listar os scripts para transferir para a VM. Este objeto também contém referências a argumentos da linha de comandos para diferentes tipos de ações. Estas ações incluem a executar a instalação predefinida para cada nó individual, uma instalação que é executada após todos os nós são implementados e scripts adicionais que podem ser específicas para um modelo especificado.

Neste exemplo é a partir de um modelo utilizado para implementar o MongoDB, que requer um arbiter fornecer elevada disponibilidade. O *arbiterNodeInstallCommand* foi adicionado ao *vmScripts* para instalar o arbiter.

A secção de variáveis é onde encontrar as variáveis que definem o texto especificado ao executar o script com os valores adequados.

```json
"vmScripts": {
    "scriptsToDownload": [
        "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
        "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],
    "regularNodeInstallCommand": "[variables('installCommand')]",
    "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
    "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
},
```

## <a name="return-state-from-a-template"></a>Devolver o estado a partir de um modelo
Não só pode passar dados a um modelo, também pode partilhar dados de volta para o modelo de chamada. No **produz** secção de um modelo de ligado, pode fornecer pares chave/valor que podem ser utilizadas pelo modelo de origem.

O exemplo seguinte mostra como passar o endereço IP privado gerado num modelo ligado.

```json
"outputs": {
    "masterip": {
        "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
        "type": "string"
     }
}
```

No modelo principal, pode utilizar esses dados com a seguinte sintaxe:

    "[reference('master-node').outputs.masterip.value]"

Pode utilizar esta expressão na secção de saídas ou a secção de recursos do modelo principal. Não é possível utilizar a expressão na secção de variáveis porque baseia-se no estado do tempo de execução. Para este valor de retorno do modelo de principal, utilize:

```json
"outputs": {
  "masterIpAddress": {
    "value": "[reference('master-node').outputs.masterip.value]",
    "type": "string"
  }
```

Para obter um exemplo de utilização a secção de saídas de um modelo de ligado para devolver os discos de dados para uma máquina virtual, consulte [criar vários discos de dados para uma Máquina Virtual](resource-group-create-multiple.md).

## <a name="define-authentication-settings-for-virtual-machine"></a>Definir definições de autenticação para a máquina virtual
Pode utilizar o mesmo padrão mostrado anteriormente para as definições de configuração para especificar as definições de autenticação para uma máquina virtual. Criar um parâmetro para transmitir o tipo de autenticação.

```json
"parameters": {
  "authenticationType": {
    "allowedValues": [
      "password",
      "sshPublicKey"
    ],
    "defaultValue": "password",
    "metadata": {
      "description": "Authentication type"
    },
    "type": "string"
  }
}
```

Adicione as variáveis para os tipos de autenticação diferentes e uma variável para armazenar o tipo é utilizada para esta implementação baseada no valor do parâmetro.

```json
"variables": {
  "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
  "osProfilepassword": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "notused",
    "computerName": "[parameters('vmName')]",
    "customData": "[base64(variables('customData'))]"
  },
  "osProfilesshPublicKey": {
    "adminUsername": "notused",
    "computerName": "[parameters('vmName')]",
    "customData": "[base64(variables('customData'))]",
    "linuxConfiguration": {
      "disablePasswordAuthentication": "true",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "[parameters('sshPublicKey')]",
            "path": "/home/notused/.ssh/authorized_keys"
          }
        ]
      }
    }
  }
}
```

Ao definir a máquina virtual, defina o **osProfile** para a variável que criou.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  ...
  "osProfile": "[variables('osProfile')]"
}
```

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre as secções do modelo, consulte [criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md)
* Para ver as funções que estão disponíveis dentro de um modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md)

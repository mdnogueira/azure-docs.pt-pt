---
title: "Extensões de máquina virtual e funcionalidades do Linux | Microsoft Docs"
description: "Saiba que extensões estão disponíveis para máquinas virtuais do Azure, agrupadas por que forneça ou melhorar."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 8a5b39351f665c51ae7d83f755329e54ff3cf786
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Extensões de máquina virtual e funcionalidades para Linux

Extensões de máquina virtual do Azure são aplicações de pequenas a indicar tarefas de configuração e a automatização de pós-implementação em máquinas virtuais do Azure. Por exemplo, se uma máquina virtual requer a instalação de software, a proteção de software antivírus ou a configuração de Docker, uma extensão da VM pode ser utilizada para concluir estas tarefas. Extensões VM do Azure podem ser executadas utilizando a CLI do Azure, PowerShell, os modelos Azure Resource Manager e o portal do Azure. Extensões podem ser incluídas com uma nova implementação da máquina virtual ou executar qualquer sistema existente.

Este documento fornece uma descrição geral das extensões de VM, pré-requisitos para utilizar extensões de VM do Azure, e orientações sobre como detetar, gerir e remover as extensões de VM. Este documento fornece informações generalizadas porque há várias extensões VM, cada um com uma configuração potencialmente exclusiva. Extensão específicos podem ser encontrados detalhes cada documento específicos para a extensão individuais.

## <a name="use-cases-and-samples"></a>Casos de utilização e amostras

Estão disponíveis várias extensões de VM do Azure diferentes, cada um com específico utilizar maiúsculas e minúsculas. Alguns exemplos incluem:

- Aplica as configurações de PowerShell Desired estado para uma máquina virtual utilizando a extensão de DSC para Linux. Para obter mais informações, consulte [extensão de configuração do Azure Desired estado](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Configure a monitorização de uma máquina virtual com a extensão de VM de agente de monitorização da Microsoft. Para obter mais informações, consulte [como monitorizar uma VM com Linux](tutorial-monitoring.md).
- Configure a monitorização da infraestrutura do Azure com a extensão de Datadog. Para obter mais informações, consulte o [Datadog blogue](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configure um anfitrião do Docker numa máquina virtual do Azure com a extensão de VM de Docker. Para obter mais informações, consulte [extensão da VM do Docker](dockerextension.md).

Para além das extensões de específicos de processos, uma extensão de Script personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de Script personalizado para Linux permite que os scripts de Bash ser executado numa máquina virtual. Scripts personalizados são úteis para estruturar implementações do Azure que necessitam de configuração para além dos quais as nativa ferramentas do Azure podem fornecer. Para obter mais informações, consulte [a extensão de Script de personalizada de VM Linux](extensions-customscript.md).


## <a name="prerequisites"></a>Pré-requisitos

Cada extensão da máquina virtual poderá ter o seu próprio conjunto de pré-requisitos. Por exemplo, a extensão de VM de Docker tem um pré-requisito de uma distribuição Linux suportado. Requisitos de extensões individuais são detalhados na documentação do específicos de extensão.

### <a name="azure-vm-agent"></a>Agente da VM do Azure

O agente da VM do Azure gere as interações entre uma máquina virtual do Azure e o controlador de recursos de infraestrutura do Azure. O agente da VM é responsável por muitos aspetos funcionais de implementar e gerir máquinas virtuais do Azure, incluindo a executar as extensões VM. O agente da VM do Azure está pré-instalado imagens do Azure Marketplace e pode ser instalado manualmente nos sistemas operativos suportados.

Para obter informações sobre sistemas operativos suportados e instruções de instalação, consulte [agente da máquina virtual do Azure](../windows/classic/agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Detetar as extensões de VM

Várias extensões VM diferentes estão disponíveis para utilização com máquinas virtuais do Azure. Para ver uma lista completa, execute o seguinte comando com a CLI do Azure, substituir a localização de exemplo com a localização da sua preferência.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>Executar as extensões VM

Extensões de máquina virtual do Azure podem ser executadas em máquinas virtuais existentes, que são úteis quando tem de efetuar alterações de configuração ou recuperar conectividade numa VM já implementada. Extensões VM também podem ser incluídas com implementações de modelos Azure Resource Manager. Ao utilizar extensões com modelos do Resource Manager, máquinas virtuais do Azure pode ser implementadas e configuradas sem a intervenção de pós-implementação.

Os métodos seguintes podem ser utilizados para executar uma extensão na máquina virtual existente.

### <a name="azure-cli"></a>CLI do Azure

Extensões de máquina virtual do Azure podem ser executadas relativamente a uma máquina virtual existente utilizando o `az vm extension set` comando. Neste exemplo é executada a extensão de script personalizado relativamente a uma máquina virtual.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

O script produz o resultado semelhante para o seguinte texto:

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portal do Azure

Extensões VM podem ser aplicadas a uma máquina virtual existente através do portal do Azure. Para tal, selecione a máquina virtual, escolha **extensões**e clique em **adicionar**. Selecione a extensão que pretende na lista de extensões disponíveis e siga as instruções no assistente.

A imagem seguinte mostra a instalação da extensão de Script personalizado de Linux do portal do Azure.

![Instalar a extensão de script personalizado](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Extensões VM podem ser adicionadas a um modelo Azure Resource Manager e executadas com a implementação do modelo. Quando implementa uma extensão com um modelo, pode criar totalmente configuradas implementações do Azure. Por exemplo, o seguinte JSON é obtido a partir de um modelo do Resource Manager. O modelo implementa um conjunto de máquinas de virtuais com balanceamento de carga e uma base de dados SQL do Azure e, em seguida, instala uma aplicação .NET Core em cada VM. A extensão da VM encarrega-se de que a instalação de software.

Para obter mais informações, consulte o completo [modelo do Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Para obter mais informações, consulte [modelos Authoring Azure Resource Manager](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions).

## <a name="secure-vm-extension-data"></a>Proteger os dados de extensão VM

Quando estiver a executar uma extensão VM, poderá ser necessário incluir informações confidenciais, tais como as credenciais, os nomes das contas de armazenamento e chaves de acesso de conta de armazenamento. Várias extensões VM incluem uma configuração protegida que encripta os dados e apenas desencripta a mesma dentro da máquina virtual de destino. Cada extensão com um esquema de configuração protegido específica, e cada está detalhada na documentação de extensão específica.

O exemplo seguinte mostra uma instância da extensão de Script personalizado para o Linux. Tenha em atenção de que o comando a executar inclui um conjunto de credenciais. Neste exemplo, o comando a executar não será encriptado.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Mover o **comando a executar** propriedade para o **protegidos** configuração protege a cadeia de execução.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>Resolver problemas de extensões VM

Cada extensão VM poderá ter passos específicos para a extensão de resolução de problemas. Por exemplo, quando estiver a utilizar a extensão de Script personalizado, detalhes de execução do script podem ser encontrados localmente na máquina virtual em que a extensão foi executada. Quaisquer passos de resolução de problemas específicos da extensão estão descritos na documentação de extensão específica.

Os passos de resolução de problemas seguintes aplicam-se a todas as extensões de máquina virtual.

### <a name="view-extension-status"></a>Ver o estado de extensão

Depois de uma máquina virtual extensão tiver sido executada em relação a uma máquina virtual, utilize o seguinte comando da CLI do Azure para devolver o estado da extensão. Substitua os nomes dos parâmetros de exemplo com os seus próprios valores.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída aparente ser o seguinte texto:

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

Também é possível encontrar o estado de execução da extensão no portal do Azure. Para ver o estado de uma extensão, selecione a máquina virtual, escolha **extensões**e selecione a extensão pretendida.

### <a name="rerun-a-vm-extension"></a>Executar novamente uma extensão VM

Pode haver casos em que uma extensão da máquina virtual tem de ser executada novamente. Pode executar novamente uma extensão, removendo-a e, em seguida, execute novamente a extensão com um método de execução da sua preferência. Para remover uma extensão, execute o seguinte comando com a CLI do Azure. Substitua os nomes dos parâmetros de exemplo com os seus próprios valores.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Pode remover uma extensão, utilizando os seguintes passos no portal do Azure:

1. Selecione uma máquina virtual.
2. Escolha **extensões**.
3. Selecione a extensão pretendida.
4. Escolha **desinstalar**.

## <a name="common-vm-extension-reference"></a>Referência de extensão VM comuns
| Nome de extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de Script personalizada para Linux |Executar scripts em relação a uma máquina virtual do Azure |[Extensão de Script personalizada para Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Extensão de docker |Instale o daemon de Docker para suportar os comandos remotos do Docker. |[Extensão de VM do Docker](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Extensão de acesso da VM |Recuperar o acesso a uma máquina virtual do Azure |[Extensão de acesso da VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extensão de diagnóstico do Azure |Gerir o diagnóstico do Azure |[Extensão de diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso de VM do Azure |Gerir utilizadores e as credenciais |[Extensão de acesso de VM para Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

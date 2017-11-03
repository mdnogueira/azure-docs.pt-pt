---
title: "Implementar uma aplicação para um conjunto de dimensionamento da máquina virtual do Azure | Microsoft Docs"
description: "Saiba como implementar aplicações para Linux e Windows instâncias de máquina virtual num conjunto de dimensionamento"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 7e03d5e2bbdb1b3b206fa7fa455f7dce7951f02b
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implementar a sua aplicação em conjuntos de dimensionamento de máquina virtual
Para executar as aplicações em instâncias de máquina virtual (VM) num conjunto de dimensionamento, terá primeiro de instalar os componentes da aplicação e os ficheiros necessários. Este artigo apresenta formas para criar uma imagem VM personalizada para instâncias na escala definido ou executam automaticamente os scripts de instalação em instâncias de VM existentes. Também irá aprender a gerir aplicações ou atualizações do SO através de um conjunto de dimensionamento.


## <a name="build-a-custom-vm-image"></a>Criar uma imagem VM personalizada
Quando utilizar uma das imagens da plataforma do Azure para criar as instâncias do conjunto de dimensionamento, o software adicional não está instalado ou configurado. Pode automatizar a instalação destes componentes, no entanto que adiciona ao tempo que demora a aprovisionar instâncias VM para os conjuntos de dimensionamento. Se aplicar demasiadas alterações de configuração para as instâncias de VM, há gestão sobrecarga com os scripts de configuração e tarefas.

Para reduzir a gestão de configuração e a hora para Aprovisionar uma VM, pode criar uma imagem VM personalizada que está pronta para executar a sua aplicação, assim que uma instância é aprovisionada no conjunto de dimensionamento. O processo geral para criar uma imagem VM personalizada para escala instâncias do conjunto são os seguintes:

1. Para criar uma imagem VM personalizada para a escala de instâncias do conjunto, pode criar e iniciar sessão a uma VM, em seguida, instala e configurar a aplicação. Pode utilizar Packer para definir e criar um [Linux](../virtual-machines/linux/build-image-with-packer.md) ou [Windows](../virtual-machines/windows/build-image-with-packer.md) imagem de VM. Em alternativa, pode criar manualmente e configurar a VM:

    - Criar uma VM com Linux com o [Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md), [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md), ou o [portal](../virtual-machines/linux/quick-create-portal.md).
    - Criar uma VM do Windows com o [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md), a [Azure CLI 2.0](../virtual-machines/windows/quick-create-cli.md), ou o [portal](../virtual-machines/windows/quick-create-portal.md).
    - Inicie sessão num [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) ou [Windows](../virtual-machines/windows/connect-logon.md) VM.
    - Instalar e configurar as aplicações e as ferramentas necessárias. Se precisar de versões específicas de uma biblioteca ou tempo de execução, uma imagem VM personalizada permite-lhe definir uma versão e 

2. Capturar a VM com o [CLI do Azure 2.0](../virtual-machines/linux/capture-image.md) ou [o Azure PowerShell](../virtual-machines/windows/capture-image.md). Este passo cria a imagem VM personalizada que é utilizada para, em seguida, implementar instâncias num conjunto de dimensionamento.

3. [Criar um conjunto de dimensionamento](virtual-machine-scale-sets-create.md) e especifique a imagem VM personalizada criada nos passos anteriores.


## <a name="already-provisioned"></a>Instalar uma aplicação com a extensão de Script personalizado
A extensão de Script personalizado transfere e executa os scripts em VMs do Azure. Esta extensão é útil para configuração de implementação de post, instalação de software ou qualquer outra configuração / tarefas de gestão. Scripts podem ser transferidos a partir do armazenamento do Azure ou o GitHub ou fornecidos para o portal do Azure em tempo de execução de extensão.

A extensão de Script personalizado se integra com modelos Azure Resource Manager e também pode ser executada utilizando a CLI do Azure, o PowerShell, o portal do Azure ou a API de REST de Máquina Virtual do Azure. 

Para obter mais informações, consulte o [descrição geral de extensão de Script personalizado](../virtual-machines/windows/extensions-customscript.md).


### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
PowerShell utiliza uma tabela hash para armazenar o ficheiro para transferir e o comando a executar. O exemplo seguinte:

- Indica as instâncias de VM para transferir um script a partir do GitHub - *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- Define a extensão para executar um script de instalação-`powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- Obtém informações sobre um conjunto com de dimensionamento [Get AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Aplica-se a extensão para as instâncias de VM com [AzureRmVmss de atualização](/powershell/module/azurerm.compute/update-azurermvmss)

É aplicada a extensão de Script personalizado para o *myScaleSet* instâncias de VM no grupo de recursos com o nome *myResourceGroup*. Introduza os seus próprios nomes da seguinte forma:

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Se a política de atualização no seu conjunto de dimensionamento é *manual*, atualizar as instâncias de VM com [atualização AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Este cmdlet aplica-se a configuração de conjunto de dimensionamento atualizados para as instâncias de VM e instala a aplicação.


### <a name="use-azure-cli-20"></a>Utilizar a CLI do Azure 2.0
Para utilizar a extensão de Script personalizado com a CLI do Azure, crie um ficheiro JSON que define quais os ficheiros para obter e comandos para executar. Estas definições JSON podem ser reutilizadas em implementações de conjunto de dimensionamento para aplicar a instalação de aplicação consistente.

Na sua shell atual, crie um ficheiro denominado *customConfig.json* e cole a seguinte configuração. Por exemplo, crie o ficheiro na Shell na nuvem não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloudConfig.json` para criar o ficheiro e ver uma lista de editores disponíveis.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Aplicar a configuração de extensão de Script personalizado para as instâncias de VM no seu dimensionamento definida com [az vmss extensão conjunto](/cli/azure/vmss/extension#set). O exemplo seguinte aplica-se a *customConfig.json* configuração para o *myScaleSet* instâncias de VM no grupo de recursos com o nome *myResourceGroup*. Introduza os seus próprios nomes da seguinte forma:

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

Se a política de atualização no seu conjunto de dimensionamento é *manual*, atualizar as instâncias de VM com [az vmss update-instâncias](/cli/azure/vmss#update-instances). Este cmdlet aplica-se a configuração de conjunto de dimensionamento atualizados para as instâncias de VM e instala a aplicação.


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalar uma aplicação para uma VM com o PowerShell DSC do Windows
[Configuração de estado pretendido ' (DSC) da PowerShell](https://msdn.microsoft.com/en-us/powershell/dsc/overview) é uma plataforma de gestão para definir a configuração das máquinas de destino. Configurações de DSC definem o que instalar num computador e como configurar o anfitrião. Um motor de Gestor de configuração Local (MMC) é executado em cada nó de destino que processa o pedido ações com base nas configurações premidas.

A extensão de DSC do PowerShell permite-lhe personalizar a instâncias de VM um conjunto com o PowerShell de dimensionamento. O exemplo seguinte:

- Indica as instâncias de VM para transferir um pacote de DSC a partir do GitHub - *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- Define a extensão para executar um script de instalação-`configure-http.ps1`
- Obtém informações sobre um conjunto com de dimensionamento [Get AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Aplica-se a extensão para as instâncias de VM com [AzureRmVmss de atualização](/powershell/module/azurerm.compute/update-azurermvmss)

A extensão de DSC é aplicada à *myScaleSet* instâncias de VM no grupo de recursos com o nome *myResourceGroup*. Introduza os seus próprios nomes da seguinte forma:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Se a política de atualização no seu conjunto de dimensionamento é *manual*, atualizar as instâncias de VM com [atualização AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Este cmdlet aplica-se a configuração de conjunto de dimensionamento atualizados para as instâncias de VM e instala a aplicação.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalar uma aplicação para uma VM com Linux com init de nuvem
[Nuvem init](https://cloudinit.readthedocs.io/latest/) é uma abordagem amplamente utilizada para personalizar uma VM com Linux como efetua o arranque pela primeira vez. Pode utilizar a cloud init para instalar pacotes e escrever em ficheiros, ou para configurar utilizadores e de segurança. Como é executado na nuvem init durante o processo de arranque inicial, existem não existem passos adicionais ou agentes necessários para aplicar a configuração.

Nuvem init também funciona em distribuições. Por exemplo, não utilize **apt get instalação** ou **yum instalar** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes para instalar. Nuvem init utiliza automaticamente a ferramenta de gestão do pacote nativo para distro que selecionar.

Para obter mais informações, incluindo um exemplo *nuvem init.txt* de ficheiros, consulte [utilizar nuvem init para personalizar as VMs do Azure](../virtual-machines/linux/using-cloud-init.md).

Para criar um conjunto de dimensionamento e utilizar um ficheiro de nuvem init, adicione o `--custom-data` parâmetro para o [az vmss criar](/cli/azure/vmss#create) comando e especifique o nome de um ficheiro de nuvem init. O exemplo seguinte cria um conjunto nomeado de dimensionamento *myScaleSet* no *myResourceGroup* e configura as instâncias VM com um ficheiro denominado *nuvem init.txt*. Introduza os seus próprios nomes da seguinte forma:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="install-applications-as-a-set-scales-out"></a>Instalar aplicações como um conjunto aumenta horizontalmente de forma
Conjuntos de dimensionamento permitem-lhe aumentar o número de instâncias VM que executar a sua aplicação. Ampliação este processo pode ser iniciada manualmente ou automaticamente com base nas métricas, como a utilização de CPU ou memória.

Se aplicou uma extensão de Script personalizado para o conjunto de dimensionamento, a aplicação está instalada para cada nova instância VM. Se o conjunto de dimensionamento é com base na imagem personalizada com aplicações pré-instaladas, cada nova instância VM é implementada num estado utilizável. 

Se as instâncias de VM de conjunto de dimensionamento anfitriões de contentor, pode utilizar a extensão de Script personalizado para solicitar e execute a necessidade de imagens do contentor. A extensão de Script personalizado também foi possível registar a nova instância VM um Orchestrator, tais como o serviço de contentor do Azure.


## <a name="deploy-application-updates"></a>Implementar atualizações da aplicação
Se atualizar o código da aplicação, bibliotecas ou pacotes, pode emitir mais recente estado da aplicação para instâncias de VM num conjunto de dimensionamento. Se utilizar a extensão de Script personalizado, as atualizações à sua aplicação e automaticamente implementada. Altere a configuração de Script personalizado, tal como para apontar para um script de instalação que tem um nome de versão atualizada. Um exemplo anterior, a extensão de Script personalizado utiliza um script com o nome *automate_nginx.sh* da seguinte forma:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Quaisquer atualizações efetuadas à sua aplicação não são expostas a extensão de Script personalizado, a menos que instale as alterações de scripts. É uma abordagem para incluir um número de versão que versões de incrementos com a sua aplicação. A extensão de Script personalizado foi agora referenciar *automate_nginx_v2.sh* da seguinte forma:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

A extensão de Script personalizado é agora executada contra as instâncias de VM para aplicar as atualizações mais recentes da aplicação.


### <a name="install-applications-with-os-updates"></a>Instalar aplicações com as atualizações do SO
Quando estiverem disponíveis novas versões de SO, pode utilizar ou criar uma nova imagem personalizada e [implementar atualizações do SO](virtual-machine-scale-sets-upgrade-scale-set.md) para uma escala definido. Cada instância VM é atualizada para a imagem mais recente que especificar. Pode utilizar uma imagem personalizada com a aplicação previamente instalada, a extensão de Script personalizado ou PowerShell DSC ter a sua aplicação automaticamente disponível como efetuar a atualização. Terá de planear para manutenção de aplicação, como efetuar este processo para se certificar de que não existem nenhuma versão de problemas de compatibilidade.

Se utilizar uma imagem VM personalizada com a aplicação previamente instalada, pode integrar as atualizações de aplicação com um pipeline de implementação para as novas imagens de criar e implementar atualizações do SO entre o conjunto de dimensionamento. Esta abordagem permite que o pipeline recolher as compilações de aplicação mais recentes, criar e validar uma imagem de VM, em seguida, atualizar as instâncias de VM no conjunto de dimensionamento. Para executar um pipeline de implementação que cria e implementa atualizações de aplicações nas imagens VM personalizadas, pode utilizar [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Spinnaker](https://www.spinnaker.io/), ou [Jenkins](https://jenkins.io/) .


## <a name="next-steps"></a>Passos seguintes
Como criar e implementar aplicações para os conjuntos de dimensionamento, pode rever o [descrição geral do Design definir dimensionamento](virtual-machine-scale-sets-design-overview.md). Para obter mais informações sobre como gerir o seu conjunto de dimensionamento, consulte [utilize o PowerShell para gerir o seu conjunto de dimensionamento](virtual-machine-scale-sets-windows-manage.md).

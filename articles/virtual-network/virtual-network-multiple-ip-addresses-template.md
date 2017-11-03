---
title: "Vários endereços IP para máquinas virtuais do Azure - modelo | Microsoft Docs"
description: "Saiba como atribuir vários endereços IP a uma máquina virtual utilizando um modelo Azure Resource Manager."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Atribuir vários endereços IP para máquinas virtuais utilizando um modelo Azure Resource Manager

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação Azure Resource Manager com um modelo do Resource Manager. Não não possível atribuir vários endereços IP públicos e privados para o mesmo NIC ao implementar uma VM através do modelo de implementação clássica. Para saber mais sobre modelos de implementação do Azure, leia o [compreender os modelos de implementação](../resource-manager-deployment-model.md) artigo.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Descrição do modelo

Implementar um modelo permite-lhe rápida e consistentemente criar recursos do Azure com valores de configuração diferente. Leia o [instruções do modelo do Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo se não estiver familiarizado com modelos Azure Resource Manager. O [implementar uma VM com vários endereços IP](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) modelo é utilizado neste artigo.

<a name="resources"></a>Implementar o modelo cria os seguintes recursos:

|Recurso|Nome|Descrição|
|---|---|---|
|Interface de rede|*myNic1*|As três configurações de IP descritas na secção deste artigo cenário são criadas e atribuídas a esta NIC.|
|Recurso de endereço IP público|são criados 2: *myPublicIP* e *myPublicIP2*|Estes recursos são atribuídos a endereços IP públicos estáticos e são atribuídos para o *IPConfig-1* e *IPConfig 2* descritas no cenário de configurações de IP.|
|VM|*myVM1*|Um padrão DS3 VM.|
|Rede virtual|*myVNet1*|Uma rede virtual com uma sub-rede designada *mySubnet*.|
|Conta de armazenamento|Exclusivo para a implementação|Uma conta de armazenamento.|

<a name="parameters"></a>Quando implementar o modelo, tem de especificar valores para os seguintes parâmetros:

|Nome|Descrição|
|---|---|
|adminUsername|Nome de utilizador de Admin. O nome de utilizador tem de cumprir [requisitos de nome de utilizador do Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Palavra-passe de administrador a palavra-passe tem de cumprir [requisitos de palavra-passe do Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|Nome DNS para PublicIPAddressName1. Irá resolver o nome DNS para um dos endereços IP públicos atribuídos à VM. O nome tem de ser exclusivo dentro da região do Azure (localização) criar a VM.|
|dnsLabelPrefix1|Nome DNS para PublicIPAddressName2. Irá resolver o nome DNS para um dos endereços IP públicos atribuídos à VM. O nome tem de ser exclusivo dentro da região do Azure (localização) criar a VM.|
|OSVersion|A versão do Windows/Linux para a VM. O sistema operativo é uma imagem totalmente patched a versão do Windows/Linux determinado selecionado.|
|imagePublisher|O publicador de imagem do Windows/Linux para a VM selecionada.|
|imageOffer|A imagem do Windows/Linux para a VM selecionada.|

Cada um dos recursos implementados pelo modelo está configurada com várias predefinições. Pode ver estas definições através de um dos seguintes métodos:

- **Ver o modelo no GitHub:** se estiver familiarizado com modelos, pode ver as definições dentro de [modelo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Ver as definições depois de implementar:** se não estiver familiarizado com modelos, pode implementar o modelo utilizando os passos de uma das seguintes secções e, em seguida, ver as definições após a implementação.

Pode utilizar o portal do Azure, PowerShell ou a interface de linha de comandos (CLI) do Azure para implementar o modelo. Todos os métodos produzem o mesmo resultado. Para implementar o modelo, conclua os passos de uma das seguintes secções:

## <a name="deploy-using-the-azure-portal"></a>Implementar no portal do Azure

Para implementar o modelo utilizando o portal do Azure, execute os seguintes passos:

1. Modifique o modelo, se assim o desejar. O modelo de implementa os recursos e definições listado no [recursos](#resources) secção deste artigo. Para saber mais sobre modelos e sobre como criá-los, leia o [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)artigo.
2. Implemente o modelo com um dos seguintes métodos:
    - **Selecione o modelo no portal:** concluir os passos a [implementar recursos do modelo personalizado](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) artigo. Escolha o modelo já existente com o nome *101-vm-múltiplo-ipconfig*.
    - **Diretamente:** clique no botão seguinte para abrir o modelo diretamente no portal:<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Independentemente do método que escolher, terá de fornecer valores para o [parâmetros](#parameters) listados anteriormente neste artigo. Após a implementação da VM, ligar à VM e adicionar os endereços IP privados para o sistema operativo é implementada, efetuando os passos a [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo. Adicione os endereços IP públicos para o sistema operativo.

## <a name="deploy-using-powershell"></a>Implementar utilizando o PowerShell

Para implementar o modelo com o PowerShell, execute os seguintes passos:

1. Implementar o modelo, efetuando os passos a [implementar um modelo com o PowerShell](../azure-resource-manager/resource-group-template-deploy-cli.md) artigo. O artigo descreve várias opções para implementar um modelo. Se optar por implementar utilizando o `-TemplateUri parameter`, o URI para este modelo é *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Se optar por implementar utilizando o `-TemplateFile` parâmetro, copie o conteúdo a [ficheiro de modelo](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) a partir do GitHub para um novo ficheiro no seu computador. Modificar o conteúdo do modelo, se assim o desejar. O modelo de implementa os recursos e definições listado no [recursos](#resources) secção deste artigo. Para saber mais sobre modelos e sobre como criá-los, leia o [modelos Authoring Azure Resource Manager ](../azure-resource-manager/resource-group-authoring-templates.md)artigo.

    Independentemente da opção que escolher para implementar o modelo com, tem de fornecer valores para os valores de parâmetros apresentados no [parâmetros](#parameters) secção deste artigo. Se optar por fornecer os parâmetros que utilizam um ficheiro de parâmetros, copie o conteúdo a [ficheiro de parâmetros](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) a partir do GitHub para um novo ficheiro no seu computador. Modifique os valores no ficheiro. Utilize o ficheiro que criou como o valor para o `-TemplateParameterFile` parâmetro.

    Para determinar os valores válidos para o OSVersion, ImagePublisher e imageOffer parâmetros, concluir os passos a [navegar e selecionar artigo de imagens de VM do Windows](../virtual-machines/windows/cli-ps-findimage.md) artigo.

    >[!TIP]
    >Se não tiver a certeza se está disponível um dnslabelprefix, introduza o `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` comando para descobrir. Se estiver disponível, o comando devolverá `True`.

2. Após a implementação da VM, ligar à VM e adicionar os endereços IP privados para o sistema operativo é implementada, efetuando os passos a [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo. Adicione os endereços IP públicos para o sistema operativo.

## <a name="deploy-using-the-azure-cli"></a>Implementar utilizando a CLI do Azure

Para implementar o modelo utilizando a CLI do Azure 1.0, conclua os seguintes passos:

1. Implementar o modelo, efetuando os passos a [implementar um modelo com a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) artigo. O artigo descreve várias opções para implementar o modelo. Se optar por implementar utilizando o `--template-uri` (-f), o URI para este modelo é *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Se optar por implementar utilizando o `--template-file` (-f) parâmetro, copie o conteúdo a [ficheiro de modelo](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) a partir do GitHub para um novo ficheiro no seu computador. Modificar o conteúdo do modelo, se assim o desejar. O modelo de implementa os recursos e definições listado no [recursos](#resources) secção deste artigo. Para saber mais sobre modelos e sobre como criá-los, leia o [modelos Authoring Azure Resource Manager ](../azure-resource-manager/resource-group-authoring-templates.md)artigo.

    Independentemente da opção que escolher para implementar o modelo com, tem de fornecer valores para os valores de parâmetros apresentados no [parâmetros](#parameters) secção deste artigo. Se optar por fornecer os parâmetros que utilizam um ficheiro de parâmetros, copie o conteúdo a [ficheiro de parâmetros](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) a partir do GitHub para um novo ficheiro no seu computador. Modifique os valores no ficheiro. Utilize o ficheiro que criou como o valor para o `--parameters-file` (-i) parâmetro.

    Para determinar os valores válidos para o OSVersion, ImagePublisher e imageOffer parâmetros, concluir os passos a [navegar e selecionar artigo de imagens de VM do Windows](../virtual-machines/windows/cli-ps-findimage.md) artigo.

2. Após a implementação da VM, ligar à VM e adicionar os endereços IP privados para o sistema operativo é implementada, efetuando os passos a [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo. Adicione os endereços IP públicos para o sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

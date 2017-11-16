---
title: "Guia de introdução - criar o primeiro contentor de instâncias de contentor do Azure com o PowerShell"
description: "Comece com instâncias de contentor do Azure através da criação de uma instância de contentor do Windows com o PowerShell."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: ca10274fc6a23d7f5e7436dbaf72a6e7a918f275
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o seu primeiro contentor no Azure Container Instances

Instâncias de contentor do Azure torna mais fácil criar e gerir contentores de Docker no Azure, sem ter de aprovisionar as máquinas virtuais ou que adotar um serviço de nível mais elevado.

Este guia de introdução, criar um contentor do Windows no Azure e expô-la à internet com um endereço IP público. Esta operação é concluída com um único comando. Dentro de alguns instantes, pode ver a aplicação em execução o seu browser:

![Aplicação implementada com o Azure Container Instances vista no browser][qs-powershell-01]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o módulo do Azure PowerShell versão 3.6 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure.

## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos do Azure com [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Criar um contentor

Pode criar um contentor, fornecendo um nome, uma imagem de Docker e um grupo de recursos do Azure para o [New-AzureRmContainerGroup] [ New-AzureRmContainerGroup] cmdlet. Opcionalmente, pode expor o contentor na Internet com um endereço IP público. Neste caso, iremos utilizar um contentor de Windows Nano servidor executar serviços de informação Internet (IIS).

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Dentro de alguns segundos, irá obter uma resposta ao seu pedido. Inicialmente, o contentor está no **criar** Estado, mas deve iniciar dentro de um ou dois minutos. Pode verificar o estado com o [Get-AzureRmContainerGroup] [ Get-AzureRmContainerGroup] cmdlet:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Estado de aprovisionamento e o endereço IP do contentor são apresentados no resultado do cmdlet:

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

Uma vez o contentor **ProvisioningState** move para `Succeeded`, pode chegá-lo no seu browser utilizando o endereço IP fornecido.

![IIS implementada utilizando as instâncias de contentor do Azure visualizadas no browser][qs-powershell-01]

## <a name="delete-the-container"></a>Eliminar o contentor

Quando tiver terminado com o contentor, pode removê-la utilizando o [remover AzureRmContainerGroup] [ Remove-AzureRmContainerGroup] cmdlet:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução que iniciou o um contentor de Windows pré-criadas em instâncias de contentor do Azure. Se gostaria de tentar criar um contentor por si e implementar a política para instâncias de contentor do Azure com o registo de contentor do Azure, avance para o tutorial de instâncias de contentor do Azure.

> [!div class="nextstepaction"]
> [Tutorial de instâncias de contentor do Azure](./container-instances-tutorial-prepare-app.md)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png
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
ms.date: 09/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: fbd1bee04c5180beda23c04607b313eec9edcab4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o seu primeiro contentor no Azure Container Instances

Instâncias de contentor do Azure torna mais fácil criar e gerir contentores de Docker no Azure, sem ter de aprovisionar as máquinas virtuais ou que adotar um serviço de nível mais elevado.

Este guia de introdução, criar um contentor do Windows no Azure e expô-la à internet com um endereço IP público. Esta operação é concluída com um único comando. Dentro de alguns instantes, verá isto no seu browser:

![Aplicação implementada com o Azure Container Instances vista no browser][qs-powershell-01]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este guia de introdução requer o Azure PowerShell versão do módulo 4.4 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos do Azure com [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Criar um contentor

Pode criar um contentor, fornecendo um nome, uma imagem de Docker e um grupo de recursos do Azure para o [New-AzureRmContainerGroup] [ New-AzureRmContainerGroup] cmdlet. Opcionalmente, pode expor o contentor na Internet com um endereço IP público. Neste caso, iremos utilizar um contentor de Windows Nano servidor executar serviços de informação Internet (IIS).

```powershell
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Dentro de alguns segundos, obterá uma resposta ao seu pedido. Inicialmente, o contentor estarão disponíveis num **criar** Estado, mas deve iniciar dentro de um ou dois minutos. Pode verificar o estado com o [Get-AzureRmContainerGroup] [ Get-AzureRmContainerGroup] cmdlet:

```powershell
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

```powershell
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução que iniciou o um contentor de Windows pré-criadas em instâncias de contentor do Azure. Se gostaria de tentar criar um contentor por si e implementar a política para instâncias de contentor do Azure com o registo de contentor do Azure, avance para o tutorial de instâncias de contentor do Azure.

> [!div class="nextstepaction"]
> [Azure Container Instances tutorials](./container-instances-tutorial-prepare-app.md) (Tutoriais do Azure Container Instances)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png
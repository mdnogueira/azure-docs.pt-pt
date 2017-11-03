---
title: "Guia de introdução - criar um registo de Docker privado no Azure com o PowerShell"
description: Saiba mais rapidamente criar um registo de contentor do Docker privado com o PowerShell.
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2017
ms.author: nepeters
ms.openlocfilehash: b58b10e644e934cc38a6e0512ba7642ab8bf27c4
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Criar um registo de contentor do Azure com o PowerShell

O Azure Container Registry é um serviço de registo do contentor Docker gerido, utilizado para armazenar imagens de contentor do Docker privadas. Este detalhes guia criar uma instância de registo de contentor do Azure com o PowerShell.

Este guia de introdução requer o Azure PowerShell versão do módulo 3,6 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

Também tem de ter Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Criar uma instância ACR utilizando o [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) comando.

O nome do registo **têm de ser exclusivos**. No exemplo seguinte *myContainerRegistry007* é utilizado. Atualize esta para um valor exclusivo.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Inicie sessão no ACR

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. Em primeiro lugar, utilize o [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) comando para obter as credenciais de administrador para a instância ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Em seguida, utilize o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) comando para iniciar sessão para a instância ACR.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

O comando devolve uma mensagem “Início de sessão com êxito” depois de concluir.

## <a name="push-image-to-acr"></a>Imagem de push para o ACR

Para enviar uma imagem para um registo de contentor do Azure, primeiro tem de ter uma imagem. Se for necessário, execute o seguinte comando para extrair uma imagem pré-criadas do Hub de Docker.

```bash
docker pull microsoft/aci-helloworld
```

A imagem têm de ser etiquetada com o nome de servidor de início de sessão ACR. Execute o [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) comando para devolver o nome do servidor de início de sessão da instância ACR.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Marcar a imagem a utilizar o [tag de docker](https://docs.docker.com/engine/reference/commandline/tag/) comando. Substitua *acrLoginServer* com o nome do servidor de início de sessão da sua instância ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Por último, utilize [docker push](https://docs.docker.com/engine/reference/commandline/push/) para emitir as imagens para a instância ACR. Substitua *acrLoginServer* com o nome do servidor de início de sessão da sua instância ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode utilizar o [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) comando para remover o grupo de recursos, instância ACR e todas as imagens de contentor.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, criou um registo de contentor do Azure com a CLI do Azure. Se gostaria de utilizar o registo de contentor do Azure com instâncias de contentor do Azure, continue para o tutorial de instâncias de contentor do Azure.

> [!div class="nextstepaction"]
> [Tutorial de instâncias de contentor do Azure](../container-instances/container-instances-tutorial-prepare-app.md)
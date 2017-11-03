---
title: "Criar uma máquina virtual do Windows na pilha do Azure utilizando a CLI do Azure | Microsoft Docs"
description: Saiba como criar uma VM do Windows na pilha do Azure utilizando a CLI do Azure
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 196bf4351ebd2bf977102571de385edae6f9612b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Criar uma máquina virtual do Windows na pilha do Azure utilizando a CLI do Azure

CLI do Azure é utilizado para criar e gerir recursos de pilha do Azure a partir da linha de comandos. Este detalhes de guia ao utilizar a CLI do Azure para criar uma máquina virtual do Windows Server 2016 na pilha do Azure. Assim que a máquina virtual é criada, que irá estabelecer ligação com o ambiente de trabalho remoto, instalar o IIS, e ver o Web site predefinido. 

## <a name="prerequisites"></a>Pré-requisitos 

* Certifique-se de que o operador de pilha do Azure adicionou a imagem "Windows Server 2016" para o mercado de pilha do Azure.  

* Pilha do Azure requer uma versão específica do CLI do Azure para criar e gerir os recursos. Se não tiver a CLI do Azure configurada para a pilha do Azure, siga os passos para [instalar e configurar a CLI do Azure](azure-stack-connect-cli.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico na qual pilha do Azure recursos são implementados e geridos. O kit de desenvolvimento ou a pilha do Azure integrado no sistema, execute o [criar grupo az](/cli/azure/group#create) comando para criar um grupo de recursos. Iremos atribuiu valores para todas as variáveis neste documento, pode utilizá-los tal como está ou atribuir um valor diferente. O exemplo seguinte cria um grupo de recursos denominado myResourceGroup na localização local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma VM utilizando o [az vm criar](/cli/azure/vm#create) comando. O exemplo seguinte cria uma VM com o nome myVM. Este exemplo utiliza Demouser para um nome de utilizador administrativo e Demouser@123 como a palavra-passe. Atualize estes valores para algo adequado ao ambiente. Estes valores são necessários quando ligar à máquina virtual.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Quando é criada a VM, anote o *PublicIPAddress* parâmetro que é de saída, que irá utilizar para aceder a VM.
 
## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web

Por predefinição, apenas nas ligações RDP estão autorizadas a máquina virtual do Windows implementada na pilha do Azure. Se esta VM vier a ser um servidor Web, tem de abrir a porta 80 a partir da Internet. Utilize o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta pretendida.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP com o endereço IP público da máquina virtual. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a máquina virtual.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalar o IIS com o PowerShell

Agora que iniciou sessão na VM do Azure, pode utilizar uma única linha do PowerShell para instalar o IIS e ativar a regra de firewall local para permitir o tráfego da Web. Abra uma janela do PowerShell e execute o comando seguinte:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Ver a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do IIS predefinida. Certifique-se de que utiliza o endereço IP público que documentou acima para visitar a página predefinida. 

![Site predefinido do IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#delete) para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, implementou uma máquina virtual simple do Windows. Para saber mais sobre as máquinas virtuais de pilha do Azure, avance para [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).

---
title: "Criar uma máquina virtual Linux utilizando a CLI do Azure na pilha do Azure | Microsoft Docs"
description: "Crie uma máquina virtual Linux com a CLI na pilha do Azure."
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
ms.openlocfilehash: de2ff697c083493b43ab0d1b5bcde532c28684e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Criar uma máquina virtual Linux utilizando a CLI do Azure na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas*

CLI do Azure é utilizado para criar e gerir recursos de pilha do Azure a partir da linha de comandos. Este detalhes de início rápido, utilizando a CLI do Azure para criar uma máquina virtual Linux na pilha do Azure.  Uma vez criada a VM, um servidor web está instalado e porta 80 está aberta para permitir o tráfego da web.

## <a name="prerequisites"></a>Pré-requisitos 

* Certifique-se de que o operador de pilha do Azure adicionou a imagem "Ubuntu Server 16.04 LTS" para o mercado de pilha do Azure. 

* Pilha do Azure requer uma versão específica do CLI do Azure para criar e gerir os recursos. Se não tiver a CLI do Azure configurada para a pilha do Azure, inicie sessão no [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou um baseados em Windows externo cliente se [ligado através de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e siga os passos para [instalar e configurar a CLI do Azure](azure-stack-connect-cli.md).

* Uma chave SSH pública com o nome id_rsa.pub deverá ser criada no diretório. SSH do seu perfil de utilizador do Windows. Para obter informações detalhadas sobre a criação de chaves SSH, consulte [criar SSH chaves no Windows](../../virtual-machines/linux/ssh-from-windows.md). 

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
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Depois de concluído, o comando será saída parâmetros para a máquina virtual.  Anote o *PublicIPAddress*, desde que utilize esta opção para ligar e gerir a sua máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web

Por predefinição, só são permitidas ligações SSH para máquinas virtuais do Linux implementadas no Azure. Se esta VM vier a ser um servidor Web, tem de abrir a porta 80 a partir da Internet. Utilize o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta pretendida.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

A partir de um sistema com SSH instalado, utilize o seguinte comando para ligar à máquina virtual. Se trabalhar no Windows, o [Putty](http://www.putty.org/) pode ser utilizado para criar a ligação. Certifique-se de que substitui com o endereço IP público correto da sua máquina virtual. No nosso exemplo acima, o endereço IP foi 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Instalar o NGINX

Utilize o script de bash seguinte para atualizar as origens de pacotes e instalar o pacote NGINX mais recente. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do NGINX predefinida. Certifique-se de que utiliza o *publicIpAddress* que documentou acima para visitar a página predefinida. 

![Site predefinido do NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#delete) para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, implementou uma máquina virtual do Linux simple. Para saber mais sobre as máquinas virtuais de pilha do Azure, avance para [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).


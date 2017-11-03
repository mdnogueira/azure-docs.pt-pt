---
title: "Guia de Introdução do Azure - Criar Portal da VM | Microsoft Docs"
description: "Guia de Introdução do Azure - Criar Portal da VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 15d4f81f8a7a835ea61a9056d83d8337d529c7cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual Linux com o portal do Azure

As máquinas virtuais podem ser criadas através do portal do Azure. Este método fornece uma interface de utilizador baseada no browser para criar e configurar máquinas virtuais e todos os recursos relacionados. Este Guia de Introdução explica como criar uma máquina virtual e como instalar um servidor Web na VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Precisa de um par de chaves SSH para concluir este guia de introdução. Se tiver um par de chaves SSH existente, este passo pode ser ignorado.

A partir de uma shell Bash, execute este comando e siga as direções no ecrã. A saída do comando inclui o nome de ficheiro do ficheiro de chave pública. Copie o conteúdo do ficheiro de chave pública para a área de transferência.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**. 

3. Introduza as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública de SSH**. Ao colar na sua chave pública de SSH, remova quaisquer espaços em branco à esquerda ou à direita. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/quick-create-portal/create-vm-portal-basic-blade.png)

4. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. 

    ![Captura de ecrã que mostra os tamanhos de VM](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. Em **Definições**, mantenha as predefinições e clique em **OK**.

6. Na página de resumo, clique em **Ok** para iniciar a implementação da máquina virtual.

7. A VM será afixada ao dashboard do portal do Azure. Depois de concluída a implementação, o resumo da VM abre-se automaticamente.


## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Crie uma ligação SSH com a máquina virtual.

1. Clique no botão **Ligar** nas propriedades da máquina virtual. O botão de ligação apresenta uma cadeia de ligação SSH que pode ser utilizada para ligar à máquina virtual.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Execute o seguinte comando para criar uma sessão SSH. Substitua a cadeia de ligação que copiou a partir do portal do Azure.

```bash 
ssh azureuser@40.112.21.50
```

## <a name="install-nginx"></a>Instalar o NGINX

Utilize o script de bash seguinte para atualizar as origens de pacotes e instalar o pacote NGINX mais recente. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar, saia da sessão SSH e volte às propriedades da VM no portal do Azure.


## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web 

Um Grupo de segurança de rede (NSG) protege os tráfegos de entrada e de saída. Quando cria uma VM a partir do portal do Azure, é criada uma regra de entrada na porta 22 para ligações SSH. Como esta VM aloja um servidor Web, tem de ser criada uma regra NSG para a porta 80.

1. Na máquina virtual, clique no nome do **Grupo de recursos**.
2. Selecione o **grupo de segurança de rede**. O NSG pode ser identificado através da coluna **Tipo**. 
3. No menu do lado esquerdo, em definições, clique em **Regras de segurança de entrada**.
4. Clique em **Adicionar**.
5. Em **Nome**, escreva **http**. Certifique-se de que o **Intervalo da porta** está definido como 80 e a **Ação** está definida como **Permitir**. 
6. Clique em **OK**.


## <a name="view-the-nginx-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 aberta para a VM, torna-se possível aceder ao servidor Web a partir da Internet. Abra um browser e introduza o endereço IP público da VM. O endereço IP público pode ser encontrado no propriedades da VM no portal do Azure.

![Site predefinido do NGINX](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual e clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, implementou uma máquina virtual simples, uma regra de grupo de segurança de rede e instalou um servidor Web. Para saber mais sobre as máquinas virtuais do Azure, continue para o tutorial das VMs do Linux.

> [!div class="nextstepaction"]
> [Tutoriais das máquinas virtuais do Linux do Azure](./tutorial-manage-vm.md)

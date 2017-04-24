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
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: ab29f01980bc7c3a8f12aaa55ff35baa3bf3f9fb
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual Linux com o portal do Azure

As máquinas virtuais podem ser criadas através do portal do Azure. Este método fornece uma interface de utilizador baseada no browser para criar e configurar máquinas virtuais e todos os recursos relacionados. Estes passos do Guia de Introdução explicam a criação de uma máquina virtual com o portal do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Precisa de um par de chaves SSH para concluir este guia de introdução. Se tiver um par de chaves SSH existente, este passo pode ser ignorado. Se estiver a utilizar um computador Windows, siga as instruções presentes [aqui](ssh-from-windows.md). 

A partir de uma shell Bash, execute este comando e siga as direções no ecrã. A saída do comando inclui o nome de ficheiro do ficheiro de chave pública. O conteúdo deste ficheiro é necessário ao criar a máquina virtual.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Computação** a partir do painel **Novo**, selecione **Ubuntu Server 16.04 LTS** a partir do painel **Computação** e, em seguida, clique no botão **Criar**.

3. Preencha o formulário **Noções básicas** da máquina virtual. Para **Tipo de autenticação**, selecione **SSH**. Ao colar na sua **chave pública SSH**, tenha atenção para remover quaisquer espaços em branco à esquerda ou à direita. Para o **Grupo de recursos** crie um novo. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são criados e geridos coletivamente. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/quick-create-portal/create-vm-portal-basic-blade.png)  

4. Escolha um tamanho para a VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. 

    ![Captura de ecrã que mostra os tamanhos de VM](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. No painel definições, selecione **Sim** em **Utilizar discos geridos**, mantenha as predefinições para o resto das definições e clique em **OK**.

6. Na página de resumo, clique em **Ok** para iniciar a implementação da máquina virtual.

7. Para monitorizar o estado de implementação, clique na máquina virtual. A VM pode ser encontrada no dashboard do portal do Azure ou ao selecionar **Máquinas Virtuais** a partir do menu do lado esquerdo. Quando a VM tiver sido criada, o estado será alterado de **Em implementação** para **Em execução**.


## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web 

Por predefinição, só são permitidas ligações SSH para máquinas virtuais do Linux implementadas no Azure. Se esta VM vier a ser um servidor Web, tem de abrir a porta 80 ao tráfego da Web. Este passo orienta-o ao longo da criação de uma regra de grupo de segurança de rede (NSG) para permitir ligações de entrada na porta 80.

1. No painel da máquina virtual, na secção **Essenciais**, clique no nome do **Grupo de recursos**.
2. No painel do grupo de recursos, clique no **Grupo de segurança de rede**, na lista de recursos. O nome do NSG deve ser o nome da VM com -nsg acrescentado ao fim.
3. Clique no cabeçalho **Regra de Segurança de Entrada** para abrir a lista de regras de entrada. Deverá ver uma regra para RDP já na lista.
4. Clique em **+ Adicionar** para abrir o painel **Adicionar regra de segurança de entrada**.
5. Em **Nome**, escreva **nginx**. Certifique-se de que o **Intervalo da porta** está definido como 80 e a **Ação** está definida como **Permitir**. Clique em **OK**.


## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Depois de a implementação estar concluída, crie uma ligação de SSH com a máquina virtual.

1. Clique no botão **Ligar** no painel da máquina virtual. O botão de ligação apresenta uma cadeia de ligação SSH que pode ser utilizada para ligar à máquina virtual.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Execute o seguinte comando para criar uma sessão SSH. Substitua a cadeia de ligação que copiou a partir do portal do Azure.

```bash 
ssh <replace with IP address>
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

## <a name="view-the-ngix-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do NGINX predefinida. Certifique-se de que utiliza o `publicIpAddress` que documentou para visitar a página predefinida. 

![Site predefinido do NGINX](./media/quick-create-cli/nginx.png) 
## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para fazê-lo, selecione o grupo de recursos a partir do painel da máquina virtual e clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre a criação de máquinas virtuais altamente disponíveis](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explorar amostras de CLI de implementação de VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


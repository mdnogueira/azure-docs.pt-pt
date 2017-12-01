---
title: "Azure início rápido da pilha - criar o Portal VM"
description: "Rápido do Azure de pilha iniciar - criar uma VM com Linux através do portal"
services: azure-stack
cloud: azure-stack
author: vhorne
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f986c060b26489e412f6230665ec76a4730293f1
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>Criar uma máquina virtual Linux com o portal de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Máquinas virtuais de pilha do Azure podem ser criadas através do portal do Azure pilha. Este método fornece uma interface de utilizador baseada no browser para criar e configurar uma máquina virtual e todos os recursos relacionados. Este guia de introdução mostra como criar uma máquina virtual Linux e instalar um servidor web no mesmo rapidamente.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no marketplace pilha do Azure**

   O marketplace de pilha do Azure não contém uma imagem de Linux por predefinição. Por isso, antes de poder criar uma máquina virtual Linux, certifique-se de que transferiu o operador de pilha do Azure a **Ubuntu Server 16.04 LTS** imagem utilizando os passos descritos no [transferir itens do marketplace do Azure para o Azure Pilha](../azure-stack-download-azure-marketplace-item.md) tópico.

* **Acesso a um cliente SSH**

   Se estiver a utilizar o Kit de desenvolvimento de pilha do Azure (ASDK), pode não ter acesso a um cliente SSH no seu ambiente. Se for este o caso, pode escolher entre vários pacotes que incluem um cliente SSH. Por exemplo, pode instalar PuTTY que inclui um cliente SSH e o gerador de chaves SSH (puttygen.exe). Para obter mais informações sobre as possíveis opções, consulte os seguintes relacionados com o artigo do Azure: [como chaves de utilizar o SSH com o Windows no Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Este guia de introdução utiliza o PuTTY para gerar as chaves SSH e ligar à máquina virtual Linux. Para transferir e instalar PuTTY, aceda a [http://www.putty.org/](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Precisa de um par de chaves SSH para concluir este guia de introdução. Se tiver um par de chaves SSH existente, este passo pode ser ignorado.

1. Navegue para a pasta de instalação PuTTY (a localização predefinida é ```C:\Program Files\PuTTY```) e executar ```puttygen.exe```.
2. Na janela do gerador de chave PuTTY, certifique-se a **tipo de chave a gerar** está definido como **RSA**e o **número de bits de uma chave gerada** está definido como **2048**. Quando estiver pronto, clique em **gerar**.

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Para concluir o processo de geração de chaves, mova o cursor do rato na janela do gerador de chave PuTTY.
4. Quando tiver concluído a geração de chaves, clique em **Guardar chave pública** e **Guardar chave privada** para guardar as suas chaves públicas e privadas aos ficheiros.

   ![Chaves puTTY](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>Inicie sessão no portal do Azure pilha

Inicie sessão no portal do Azure pilha. O endereço do portal do Azure pilha depende o produto de pilha do Azure ao qual está a ligar:

* Para o Kit de desenvolvimento de pilha do Azure (ASDK), aceda a: https://portal.local.azurestack.external.
* Para um sistema de pilha do Azure integrado, avance para o URL que o operador de pilha do Azure fornecido.

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. Clique em de **novo** botão encontrado no canto superior esquerdo do portal do Azure pilha.

2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Clique em **Criar**.

4. Escreva as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública de SSH**. Quando colar no seu a chave pública SSH (que guardou para um ficheiro anteriormente), asseguramos para remover qualquer espaço em branco à esquerda nem à direita. Quando terminar, clique em **OK**.

   ![Noções básicas de máquina virtual](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selecione **D1_V2** para a máquina virtual.

   ![Tamanho da máquina](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. No **definições** página, mantenha as predefinições e clique em **OK**.

7. No **resumo** página, clique em **OK** para iniciar a implementação da máquina virtual.


## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

1. Clique em **Connect** na página de máquina virtual. Esta ação apresenta uma cadeia de ligação de SSH que pode ser utilizada para ligar à máquina virtual.

   ![Ligar a máquina virtual](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Abra o PuTTY.
3. No **configuração do PuTTY** ecrã em **categoria**, expanda **SSH** e, em seguida, clique em **Auth**. Clique em **procurar** e selecione o ficheiro de chave privada que guardou anteriormente.

   ![Chave privada puTTY](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. Em **categoria**, desloque o ecrã para cima e clique em **sessão**.
5. No **nome de anfitrião (ou endereço IP)** caixa, cole a cadeia de ligação do portal do Azure pilha que vimos anteriormente. Neste exemplo, a cadeia é ```asadmin@192.168.102.34```.
 
   ![Sessão puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. Clique em **abrir** para abrir uma sessão para a máquina virtual.

   ![Linus sessão](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>Instalar o NGINX

Utilize o seguinte script de deteção para atualizar as origens de pacote e instalar o pacote NGINX mais recente na máquina virtual. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar, saia da sessão SSH e voltar a página de descrição geral de máquina virtual no portal do Azure pilha.


## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web 

Um Grupo de segurança de rede (NSG) protege os tráfegos de entrada e de saída. Quando uma máquina virtual é criada a partir do portal do Azure pilha, é criada uma regra de entrada na porta 22 para ligações SSH. Porque esta máquina virtual aloja um servidor web, uma regra NSG tem de ser criado para a porta 80.

1. Na máquina virtual **descrição geral** página, clique no nome do **grupo de recursos**.
2. Selecione o **grupo de segurança de rede** para a máquina virtual. O NSG pode ser identificado através da coluna **Tipo**. 
3. No menu da esquerda, em **definições**, clique em **regras de segurança de entrada**.
4. Clique em **Adicionar**.
5. Em **Nome**, escreva **http**. Certifique-se de que o **Intervalo da porta** está definido como 80 e a **Ação** está definida como **Permitir**. 
6. Clique em **OK**.


## <a name="view-the-nginx-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com NGINX instalado e a porta 80 aberto na sua máquina virtual, o servidor web agora pode ser acedido em endereço IP público da máquina virtual. O endereço IP público pode ser encontrado na página de descrição geral da máquina virtual no portal do Azure pilha.

Abra um browser e navegue para ```http://<public IP address>```.

![Site predefinido do NGINX](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos a partir da página da máquina virtual e clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes

Este início rápido, a tiver implementado uma máquina de virtual com Linux simple, uma regra de grupo de segurança de rede e o instalado num servidor web. Para saber mais sobre as máquinas virtuais de pilha do Azure, avance para [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).


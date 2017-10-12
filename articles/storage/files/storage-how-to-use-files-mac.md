---
title: "Montar uma partilha de Ficheiros do Azure através de SMB com macOS | Microsoft Docs"
description: "Saiba como montar uma partilha de Ficheiros do Azure através de SMB com macOS"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 6e71a13f99160fdd310be1e9a59717c9fecbf35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montar uma partilha de Ficheiros do Azure através de SMB com macOS
[Ficheiros do Azure](storage-files-introduction.md) é o serviço da Microsoft que lhe permite criar e utilizar partilhas de ficheiros de rede no Azure com a norma da indústria. As partilhas de Ficheiros do Azure podem ser montadas no macOS Sierra (10.12) e El Capitan (10.11). Este artigo mostra duas formas diferentes de montar uma artilha de Ficheiros do Azure em macOS com a IU do Finder e o Terminal.

> [!Note]  
> Antes de montar uma partilha de Ficheiros do Azure através de SMB, recomendamos desativar a assinatura de pacotes SMB. Mantê-la ativada pode originar um mau desempenho ao aceder à partilha a partir de macOS. A ligação SMB será encriptada, pelo que a segurança da sua ligação não é afetada. No terminal, os comandos seguintes desativam a assinatura de pacotes SMB, conforme descrito neste [artigo de suporte da Apple relativo à desativação da assinatura de pacotes SMB](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Pré-requisitos para montar uma partilha de Ficheiros do Azure em macOS
* **Nome da conta de armazenamento**: para montar uma partilha de Ficheiros do Azure, precisa do nome da conta de armazenamento.

* **Chave da conta de armazenamento**: para montar uma partilha de Ficheiros do Azure, precisa da chave de armazenamento primária (ou secundária). Atualmente, não são suportadas chaves SAS para a montagem.

* **Confirme que a porta 445 está aberta**: o SMB comunica através da porta TCP 445. No computador cliente (o Mac), verifique se a firewall não está a bloquear a porta TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montar uma partilha de Ficheiros do Azure através do Finder
1. **Abra o Finder**: o Finder é aberto no macOS por predefinição, mas pode confirmar que é a aplicação que está selecionada ao clicar no “ícone de cara do macOS” no dock:  
    ![O ícone de cara do macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selecione "Connect to Server" ("Ligar ao servidor") a partir do Menu “Go” ("Ir")**: utilizando o caminho UNC dos [pré-requisitos](#preq), converta a barra invertida duplo de início (`\\`) para `smb://` e todas as outras barras invertidas (`\`) para barras (`/`). A ligação deverá ter um aspeto semelhante ao seguinte: ![caixa de diálogo "Connect to Server"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Utilize o nome da partilha e a chave da conta de armazenamento quando lhe for pedido o nome de utilizador e a palavra-passe**: quando clica em “Connect” (”Ligar”) na caixa de diálogo “Connect to Server” (“Ligar ao Servidor”), é-lhe pedido o nome de utilizador e a palavra-passe (que são preenchidos automaticamente com o seu nome de utilizador de macOS). Tem a opção de armazenar o nome da partilha/chave da conta de armazenamento no porta-chaves do macOS.

4. **Utilize a partilha de Ficheiros de Azure conforme pretendido**: depois de substituir o nome da partilha e a chave da conta de armazenamento pelo nome de utilizador e a palavra-passe, a partilha é montada. Pode utilizá-la tal como se fosse uma partilha pasta/ficheiro local normal, incluindo arrastar e largar ficheiros na partilha:

    ![Instantâneo de uma partilha de Ficheiros do Azure montada](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montar uma partilha de Ficheiros do Azure através do Terminal
1. Substitua `<storage-account-name>` pelo nome de sua conta de armazenamento. Indique a Chave da Conta de Armazenamento como a palavra-passe, quando lhe for pedido. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Utilize a partilha de Ficheiros do Azure conforme pretendido**: a partilha do Ficheiro do Azure é montada no ponto de montagem especificado pelo comando anterior.  

    ![Instantâneo da partilha de Ficheiros do Azure montada](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Passos seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

* [Artigo de Suporte da Apple - Como efetuar a ligação à Partilha de ficheiros no Mac](https://support.apple.com/HT204445)
* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Resolução de Problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)    
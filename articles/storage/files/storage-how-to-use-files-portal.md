---
title: Como gerir os Ficheiros do Azure a partir do portal do Azure | Microsoft Docs
description: Aprenda a utilizar o portal do Azure para gerir os Ficheiros do Azure.
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
ms.openlocfilehash: e56f8bf1057a8bc2cfcde841f69022104bafff27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-files-from-the-azure-portal"></a>Como utilizar os Ficheiros do Azure a partir do Portal do Azure
O [portal do Azure](https://portal.azure.com) fornece uma interface de utilizador para a gestão dos Ficheiros do Azure. Pode efetuar as seguintes ações a partir do seu navegador:

* Criar Partilhas de Ficheiros
* Carregar e transferir ficheiros de e para a partilha de ficheiros.
* Monitorizar a utilização real de cada partilha de ficheiros.
* Ajustar a quota de tamanho da partilha de ficheiros.
* Copiar os comandos de montagem a utilizar para montar a partilha de ficheiros a partir de um cliente Windows ou Linux.

## <a name="create-file-share"></a>Criar a partilha de ficheiros
1. Inicie sessão no Portal do Azure.
2. No menu de navegação, clique em **Contas de armazenamento** ou **Contas de armazenamento (clássicas)**.
    
    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. Escolha a sua conta de armazenamento.

    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. Escolha o serviço “Ficheiros”.

    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. Clique em “Partilhas de ficheiros” e siga a ligação para criar a sua primeira partilha de ficheiros.

    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. Preencha o nome da partilha de ficheiros e o tamanho da partilha de ficheiros (até 5120 GB) para criar a primeira partilha de ficheiros. Quando a partilha de ficheiros tiver sido criada, é possível montá-la a partir de qualquer sistema de ficheiros que suporte o SMB 2.1 ou SMB 3.0. Pode clicar em **Quota** na partilha de ficheiros para alterar o tamanho do ficheiro até 5120 GB. Veja a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/) para calcular os custos de armazenamento da utilização dos Ficheiros do Azure.

    ![Captura de ecrã que mostra como criar a partilha de ficheiros no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>Carregar e transferir os ficheiros
1. Escolha uma partilha de ficheiros que já criou.

    ![Captura de ecrã que mostra como carregar e transferir os ficheiros do portal](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. Clique em **Carregar** para abrir a interface de utilizador para o carregamento de ficheiros.

    ![Captura de ecrã que mostra como carregar ficheiros do portal](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>Ligar à partilha de ficheiros
-  Clique em **Ligar** para obter a linha de comandos para montar a partilha de ficheiros a partir do Windows e Linux. Os utilizadores do Linux também podem ver [Como utilizar os Ficheiros do Azure com o Linux](../storage-how-to-use-files-linux.md) para obter mais instruções de montagem para outras distribuições do Linux.

    ![Captura de ecrã que mostra como montar a partilha de ficheiros](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  Pode copiar os comandos para a montagem de partilha de ficheiros no Windows ou Linux e executá-los a partir da sua VM do Azure ou na máquina no local.

    ![Captura de ecrã que mostra os comandos de montagem para o Windows e Linux](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**Sugestão:**  
Para localizar a chave de acesso da conta de armazenamento para a montagem, clique em **Visualizar as chaves de acesso desta conta de armazenamento** na parte inferior da página de ligação.

## <a name="see-also"></a>Consultar também
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Resolução de Problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)    

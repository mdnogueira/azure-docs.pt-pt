---
title: Mover dados para e do armazenamento de Blobs com o Explorador de armazenamento do Azure | Microsoft Docs
description: Mover dados de e para o Armazenamento de Blobs do Azure com o Explorador do Armazenamento do Azure
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 3db06eed16191ef906f2c565d78fd0063cdb2f7b
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover dados para e do armazenamento de Blobs do Azure através do Explorador de armazenamento do Azure
Explorador de armazenamento do Azure é uma ferramenta gratuita da Microsoft que lhe permite trabalhar com dados de armazenamento do Azure no Windows, macOS e Linux. Este tópico descreve como utilizá-la para carregar e transferir os dados do armazenamento de Blobs do Azure. A ferramenta pode ser transferida a partir [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se estiver a utilizar VM que foi configurado com os scripts fornecidos pelo [máquinas virtuais de ciência de dados no Azure](virtual-machines.md), em seguida, o Explorador de armazenamento do Azure já está instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa para o armazenamento de Blobs do Azure, consulte [Noções básicas de Blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [serviço Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento parte do princípio de que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/transferência de dados, tem de conhecer a chave de conta e o nome da conta de armazenamento do Azure. 

* Para configurar uma subscrição do Azure, consulte [um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e para obter a conta e informações da chave, consulte [contas do storage do Azure sobre](../../storage/common/storage-create-storage-account.md). Tome nota da chave de acesso para a sua conta de armazenamento, conforme necessário esta chave para ligar à conta de com a ferramenta Explorador de armazenamento do Azure.
* A ferramenta Explorador de armazenamento do Azure pode ser transferida do [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/). Aceite as predefinições durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Utilize o Explorador de armazenamento do Azure
Os seguintes passos documentar como carregamento/transferir dados através do Explorador de armazenamento do Azure. 

1. Inicie o Explorador de armazenamento do Microsoft Azure.
2. Para fazer aparecer o **iniciar sessão na sua conta...**  assistente, selecione **as definições da conta do Azure** ícone, em seguida, **adicionar uma conta** e introduzir as credenciais. ![Adicionar uma conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para fazer aparecer o **ligar ao armazenamento de Azure** assistente, selecione o **ligar ao armazenamento do Azure** ícone. ![Ligar ao armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Introduza a chave de acesso da sua conta de armazenamento do Azure **ligar ao armazenamento de Azure** assistente e, em seguida, **seguinte**. ![Ligar ao armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Introduza o nome de conta de armazenamento na **nome da conta** caixa e, em seguida, selecione **seguinte**. ![Anexar armazenamento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Agora deverá estar listada a conta de armazenamento adicionada. Para criar um contentor de BLOBs numa conta do storage, clique com botão direito do **contentores de BLOBs** nó nessa conta, selecione **criar contentor de Blob**e introduza um nome.
7. Carregar dados para um contentor, selecione o contentor de destino e clique em de **carregar** botão.![ Contas de armazenamento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique em de **...**  à direita do **ficheiros** caixa, selecione um ou vários ficheiros para carregar a partir do sistema de ficheiros e clique em **carregar** para iniciar o carregamento de ficheiros.![ Carregar ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para transferir dados, selecionando os BLOBs no contentor correspondente para transferir e clique em **transferir**. ![Transferir ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)


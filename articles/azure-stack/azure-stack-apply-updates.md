---
title: "Aplicar atualizações na pilha do Azure | Microsoft Docs"
description: "Saiba como importar e instalar pacotes de atualização da Microsoft para um sistema de pilha do Azure integrado."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: b00bd606faaffaad30ff6cea3bcf47dc85282f69
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="apply-updates-in-azure-stack"></a>Aplicar atualizações na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas*

Como um operador de pilha do Azure, pode aplicar pacotes para a pilha do Azure utilizando a atualização na peça de mosaico do portal do administrador do Microsoft update. Tem de transferir o pacote de atualização da Microsoft, importar os ficheiros do pacote para a pilha do Azure e, em seguida, instale o pacote de atualização. 

## <a name="download-the-update-package"></a>Transferir o pacote de atualização

Quando estiver disponível um pacote de atualização da Microsoft para a pilha do Azure, transfira o pacote para uma localização que esteja acessível a partir de pilha do Azure e rever o conteúdo do pacote. Um pacote de atualização normalmente consiste dos seguintes ficheiros:

- Extração automática *PackageName*.exe ficheiro. Este ficheiro contém o payload para a atualização, por exemplo a atualização cumulativa mais recente para o Windows Server.   
- Correspondente *PackageName*. bin ficheiros. Estes ficheiros fornecem a compressão do payload que se encontra associado a *PackageName*.exe ficheiro. 
- Um ficheiro Metadata.xml. Este ficheiro contém informações essenciais sobre a atualização, por exemplo o publicador, nome, pré-requisito, tamanho e o URL de caminho de suporte.

## <a name="import-and-install-updates"></a>Importar e instalar atualizações

O procedimento seguinte mostra como importar e instalar pacotes de atualização no portal do administrador.

> [!IMPORTANT]
> Recomendamos vivamente que notifique os utilizadores de quaisquer operações de manutenção e agendar a janelas de manutenção normal durante horas quanto possíveis. Operações de manutenção podem afetar as cargas de trabalho de utilizador e operações portais.

1. No portal do administrador, selecione **mais serviços**. Em seguida, sob o **dados + armazenamento** categoria, selecione **contas do Storage**. (Ou, na caixa do filtro, comece a escrever **contas do storage**e selecione-o.)

    ![Mostra onde encontrar contas do storage no portal](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Na caixa do filtro, escreva **atualizar**e selecione o **updateadminaccount** conta de armazenamento.

    ![Mostra como procurar updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. O armazenamento conta em obter mais detalhes, **serviços**, selecione **Blobs**.
 
    ![Mostra como obter para Blobs para a conta de armazenamento](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. Em **serviço Blob**, selecione **+ contentor** para criar um contentor. Introduza um nome (por exemplo *atualização 1709*) e, em seguida, selecione **OK**.
 
     ![Mostra como adicionar um contentor na conta de armazenamento](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Depois do contentor é criado, clique no nome de contentor e, em seguida, clique em **carregar** para carregar os ficheiros do pacote para o contentor.
 
    ![Mostra como carregar os ficheiros do pacote](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Em **carregar blob**, clique no ícone de pasta, procure .exe ficheiro o pacote de atualização e, em seguida, clique em **abra** na janela do Explorador de ficheiros.
  
7. Em **carregar blob**, clique em **carregar**. 
 
    ![Mostra onde pretende carregar cada ficheiro de pacote](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Repita os passos 6 e 7 para o *PackageName*. bin e ficheiros de Metadata.xml. 
9. Quando terminar, pode rever as notificações (ícone de campainha no canto superior direito do portal). As notificações devem indicar que o carregamento foi concluída. 
10. Navegue de volta para o mosaico de atualização no dashboard. O mosaico deve indicar que está disponível uma atualização. Clique no mosaico para rever o pacote de atualização adicionados recentemente.
11. Para instalar a atualização, selecione o pacote que está marcado como **pronto** e um pacote com o botão direito e selecione **atualizar agora**, ou clique em de **atualizar agora** ação perto da parte superior .
12. Ao clicar em Instalar pacote de atualização, pode ver o estado de **detalhes da execução da atualização** área. Aqui, também pode clicar em **transferir os registos completos** para transferir os ficheiros de registo.
13. Quando a atualização estiver concluída, o mosaico de atualização mostra a versão atualizada da pilha do Azure.

## <a name="next-steps"></a>Passos seguintes

- [Gerir as atualizações na descrição geral de pilha do Azure](azure-stack-updates.md)
- [Pilha do Azure de política de manutenção](azure-stack-servicing-policy.md)

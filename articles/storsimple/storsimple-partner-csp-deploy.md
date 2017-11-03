---
title: "Descrição geral de programa do Microsoft Azure StorSimple e fornecedor de soluções em nuvem | Microsoft Docs"
description: "Uma descrição geral sobre o StorSimple e o CSP para parceiros do StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: c8cb51093142146fc7d43b51a62d949f6cc38988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Implementar matriz Virtual StorSimple para o programa de fornecedor de solução em nuvem

## <a name="overview"></a>Descrição geral

Matriz Virtual StorSimple pode ser implementado por parceiros do fornecedor de solução em nuvem (CSP) para os seus clientes. Um parceiro CSP pode criar um serviço do Gestor de dispositivos do StorSimple. Este serviço, em seguida, pode ser utilizado para implementar e gerir matriz Virtual StorSimple e as partilhas associadas, volumes e cópias de segurança.

Este artigo descreve como um parceiro CSP pode adicionar uma nova subscrição ou o cliente para um cliente existente e, em seguida, criar um serviço para implementar uma matriz de Virtual StorSimple no CSP.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

- Inscritos com o programa CSP.
- Tiver válido [Centro de parceiros](http://partnercenter.microsoft.com/) credenciais de início de sessão. As credenciais permitem-lhe iniciar sessão no portal de parceiros para adicionar novos clientes, procurar clientes ou navegue para uma conta de cliente a partir do dashboard de parceiro. O CSP pode funcionar como um administrador do StorSimple em nome do cliente no portal do Azure.
                             
## <a name="add-a-customer"></a>Adicionar um cliente

Se adicionar um cliente, é automaticamente criada uma subscrição. Para adicionar um cliente (e criar automaticamente uma subscrição), execute os seguintes passos no portal de parceiros.

1. Vá para o [Centro de parceiros](http://partnercenter.microsoft.com/) e inicie sessão com as suas credenciais CSP. Clique em **Dashboard**.

     ![Dashboard do Centro de parceiros](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **clientes**. No painel direito, clique em **adicionar clientes**. Introduza os detalhes do cliente. Clique em **seguinte: subscrições** para criar uma subscrição do cliente.

    ![Adicionar cliente](./media/storsimple-partner-csp-deploy/image2.png)

3.  Selecione **Microsoft Azure** oferecem. Desloque-se na parte inferior da página e clique em **revisão**.

    ![Reveja as informações de subscrição](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Reveja as informações e clique em **submeter**.

    ![Submeter subscrição](./media/storsimple-partner-csp-deploy/image4.png)

5. Guarde as informações de confirmação para consulta futura.

    ![Guardar confirmação](./media/storsimple-partner-csp-deploy/image5.png)

6. Localizar ou navegue para o cliente que acabou de ser adicionado. Clique em de **nome da empresa** para desagregar os detalhes.

    ![Pesquisa para o cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel esquerdo, selecione **gestão de serviço**. No painel direito, em **administrar serviços**, clique em **Microsoft Azure Management Portal** para iniciar sessão como um administrador do Azure para o seu cliente.

    ![Inicie sessão no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um Gestor de dispositivos do StorSimple, clique em **+ novo** e procure ou navegue até ao **série de dispositivo Virtual StorSimple**. Para obter mais informações, aceda a [implementar um serviço do Gestor de dispositivos do StorSimple](storsimple-virtual-array-manage-service.md).

    ![Criar o serviço do Gestor de dispositivos do StorSimple](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Adicionar uma subscrição

Em alguns casos, poderá ter um cliente existente e tem de adicionar uma subscrição. Para adicionar uma subscrição a um cliente existente, execute os seguintes passos no portal de parceiros.

1. Vá para o [Centro de parceiros](http://partnercenter.microsoft.com/) e inicie sessão com as suas credenciais CSP. Clique em **Dashboard**.

     ![Dashboard do Centro de parceiros](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **clientes**. Localizar ou navegue para o cliente que pretende adicionar uma subscrição. Clique em de ![ícone de verificação de expansão](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ícone para expandir a linha para o nome da empresa para o seu cliente. Nos detalhes, clique em **adicionar subscrições**.

    ![Clientes](./media/storsimple-partner-csp-deploy/image10.png)

3. Verifique **Microsoft Azure** para o **principais ofertas** na subscrição e clique em **submeter**. Esta ação cria uma nova subscrição.

    ![Adicionar nova subscrição](./media/storsimple-partner-csp-deploy/image11.png)

6. Depois de criar uma nova subscrição, clique em **< – clientes** no painel esquerdo para regressar ao **clientes** página. Pesquisa para o cliente para o qual acabou de criar uma subscrição. Clique em de **nome da empresa** para desagregar os detalhes.

    ![Pesquisa para o cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel esquerdo, selecione **gestão de serviço**. No painel direito, em **administrar serviços**, clique em **Microsoft Azure Management Portal** para iniciar sessão como um administrador do Azure para o seu cliente.

    ![Inicie sessão no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um Gestor de dispositivos do StorSimple, clique em **+ novo** e procure ou navegue até ao **série de dispositivo Virtual StorSimple**. Para obter mais informações, aceda a [implementar um serviço do Gestor de dispositivos do StorSimple](storsimple-virtual-array-manage-service.md).

    ![Criar o serviço do Gestor de dispositivos do StorSimple](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Passos seguintes

- Se tiver mais perguntas sobre o StorSimple no CSP, aceda a [StorSimple no CSP: Perguntas mais frequentes](storsimple-partner-csp-faq.md).
- Se estiver pronto para implementar a sua StorSimple, aceda a [implementar a sua StorSimple no CSP](storsimple-partner-csp-deploy.md).

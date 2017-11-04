---
title: "Implementar o serviço do Gestor de dispositivos do StorSimple | Microsoft Docs"
description: "Explica como criar e eliminar o dispositivo do serviço StorSimple Manager no portal do Azure e descreve como gerir a chave de registo do serviço."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Implementar o serviço StorSimple Manager de dispositivo para a matriz Virtual StorSimple
## <a name="overview"></a>Descrição geral

O serviço do Gestor de dispositivos do StorSimple é executado no Microsoft Azure e se liga a diversos dispositivos StorSimple. Depois de criar o serviço, pode utilizá-lo para gerir os dispositivos a partir do portal do Microsoft Azure em execução num browser. Isto permite-lhe monitorizar todos os dispositivos que estão ligados ao serviço do Gestor de dispositivos do StorSimple a partir de uma única localização central, deste modo, minimizando o fardo administrativo.

As tarefas comuns relacionadas com um serviço StorSimple Manager de dispositivos são:

* Criar um serviço
* Eliminar um serviço
* Obter a chave de registo do serviço
* Regenerar a chave de registo do serviço

Este tutorial descreve como efetuar cada uma das tarefas anteriores. As informações contidas neste artigo são aplicáveis apenas a matrizes Virtual StorSimple. Para obter mais informações sobre série 8000 do StorSimple, aceda a [implementar um serviço StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Criar um serviço

Para criar um serviço, tem de ter:

* Uma subscrição com um Enterprise Agreement
* Uma conta de armazenamento do Microsoft Azure Active Directory
* As informações de faturação que são utilizadas para gestão de acesso

Também pode optar por gerar uma conta de armazenamento ao criar o serviço.

Um único serviço pode gerir vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço funcione com diferentes subscrições, as organizações ou até mesmo as localizações de implementação.

> [!NOTE]
> Terá de instâncias separadas do serviço do Gestor de dispositivos do StorSimple para gerir dispositivos de série 8000 do StorSimple e matrizes Virtual StorSimple.


Execute os seguintes passos para criar um serviço.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminar um serviço

Antes de eliminar um serviço, certifique-se de que não existem dispositivos ligados a estiver a utilizar. Se o serviço está em utilização, desative os dispositivos ligados. A operação de desativar irá sever a ligação entre o dispositivo e o serviço, mas manter os dados de dispositivo na nuvem.

> [!IMPORTANT]
> Depois de um serviço é eliminado, não é possível reverter a operação. Qualquer dispositivo que estava a utilizar o serviço terá de ser antes de poder ser utilizada com outro serviço de reposição de fábrica. Neste cenário, os dados locais no dispositivo, bem como a configuração, serão perdidos.
 

Execute os seguintes passos para eliminar um serviço.

#### <a name="to-delete-a-service"></a>Para eliminar um serviço

1. Aceda a **todos os recursos**. Pesquisa para o seu serviço do Gestor de dispositivos do StorSimple. Selecione o serviço que pretende eliminar.
   
    ![Selecionar serviço eliminar](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Aceda ao seu dashboard do serviço para garantir que não existem nenhum dispositivo ligado ao serviço. Se não houver nenhuma dispositivos registados com este serviço, também verá uma mensagem de faixa para o efeito. Clique em **Eliminar**.
   
    ![Eliminar o serviço](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Quando lhe for pedido para confirmação, clique em **Sim** na notificação de confirmação. 
   
    ![Confirmar eliminação do serviço](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Pode demorar alguns minutos para que o serviço será eliminado. Depois do serviço é eliminado com êxito, será notificado.
   
    ![Eliminação do serviço concluída com êxito](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

A lista de serviços será atualizada.

 ![Lista atualizada de serviços](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço
Depois de ter criado com êxito um serviço, terá de registar o dispositivo StorSimple com o serviço. Para registar o primeiro dispositivo StorSimple, terá da chave de registo do serviço. Para registar dispositivos adicionais com um serviço StorSimple, terá da chave de registo e a chave de encriptação de dados do serviço (o que é gerado no primeiro dispositivo durante o registo). Para mais informações sobre a chave de encriptação de dados do serviço, consulte [segurança do StorSimple](storsimple-security.md). Pode obter a chave de registo acedendo a **chaves** painel para o seu serviço.

Execute os passos seguintes para obter a chave de registo do serviço.

#### <a name="to-get-the-service-registration-key"></a>Para obter a chave de registo do serviço
1. No **Gestor de dispositivos do StorSimple** painel, aceda a **gestão &gt;**  **chaves**.
   
   ![Painel de chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. No **chaves** aparece do painel, uma chave de registo do serviço. Copie a chave de registo utilizando o ícone de cópia. 

Manter a chave de registo do serviço numa localização segura. Terá esta chave, bem como a chave de encriptação de dados de serviço, para registar dispositivos adicionais este serviço. Depois de obter a chave de registo do serviço, terá de configurar o dispositivo através do Windows PowerShell para StorSimple interface.

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registo do serviço
Terá de voltar a gerar uma chave de registo do serviço, se for necessário para executar a rotação da chave ou se a lista de administradores de serviço foi alterada. Quando voltar a gerar a chave, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos que foram registados já não são afetados por este processo.

Execute os seguintes passos para voltar a gerar uma chave de registo do serviço.

#### <a name="to-regenerate-the-service-registration-key"></a>A regenerar a chave de registo do serviço
1. No **Gestor de dispositivos do StorSimple** painel, aceda a **gestão &gt;**  **chaves**.
   
   ![Painel de chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. No **chaves** painel, clique em **regenerar**.
   
   ![Clique em voltar a gerar](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. No **chave de registo do serviço de voltar a gerar** painel, reveja a ação é necessária quando as chaves são regeneradas. Todos os dispositivos posteriores que são registados com este serviço irão utilizar a nova chave de registo. Clique em **regenerar** para confirmar. Será notificado depois de concluir o registo.
   
   ![Confirme a regeneração da chave](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Será apresentada uma nova chave de registo do serviço.
   
    ![Confirme a regeneração da chave](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Copie esta chave e guarde-a para registar os novos dispositivos com este serviço.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [começar](storsimple-virtual-array-deploy1-portal-prep.md) com uma matriz de Virtual StorSimple.
* Saiba como [administrar o dispositivo StorSimple](storsimple-ova-web-ui-admin.md).


---
title: Gerir credenciais de conta de armazenamento de matriz Virtual StorSimple | Microsoft Docs
description: "Explica como pode utilizar a página de configuração do Gestor de dispositivos do StorSimple para adicionar, editar, eliminar ou rodar as chaves de segurança de credenciais de conta de armazenamento associadas a matriz de Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Utilize o Gestor de dispositivo StorSimple para gerir as credenciais da conta de armazenamento para a matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral
O **configuração** secção do painel de serviço do Gestor de dispositivos do StorSimple da sua matriz de Virtual StorSimple apresenta os parâmetros de serviço global que podem ser criados no serviço StorSimple Manager. Estes parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço e incluem:

* Credenciais da conta de armazenamento
* Registos de controlo de acesso
  
  ![Dashboard de serviço do Gestor de dispositivos](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Este tutorial explica como pode adicionar, editar ou eliminar as credenciais da conta de armazenamento para a matriz de Virtual StorSimple. As informações neste tutorial aplica-se apenas à matriz Virtual StorSimple. Para obter informações sobre como gerir contas de armazenamento na 8000 série, consulte [utilizar o serviço StorSimple Manager para gerir a sua conta de armazenamento](storsimple-manage-storage-accounts.md).

As credenciais da conta de armazenamento contém as credenciais que o dispositivo utiliza para aceder à sua conta de armazenamento com o fornecedor de serviço em nuvem. Para contas de armazenamento do Microsoft Azure, estas são as credenciais, tais como o nome da conta e a chave de acesso primária.

No **as credenciais da conta de armazenamento** painel, todas as credenciais de conta de armazenamento criados para a subscrição de faturação são apresentadas em formato tabular que contém as seguintes informações:

* **Nome** – o nome exclusivo atribuído à conta quando foi criado.
* **SSL ativado** – SSL o se estiver ativada e a comunicação do dispositivo para a nuvem é através do canal seguro.
  
  ![Secção de configuração](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

As tarefas mais comuns relacionadas com as credenciais da conta de armazenamento podem ser executadas no **as credenciais da conta de armazenamento** painel são:

* Adicionar uma credencial da conta de armazenamento
* Editar uma credencial de conta de armazenamento
* Eliminar uma credencial de conta de armazenamento

## <a name="types-of-storage-account-credentials"></a>Tipos de credenciais de conta de armazenamento
Existem três tipos de credenciais de conta de armazenamento que podem ser utilizados com o dispositivo StorSimple.

* **As credenciais da conta de armazenamento gerado automaticamente** – como o nome sugere, este tipo de credencial de conta de armazenamento é gerado automaticamente quando o serviço é criado pela primeira vez. Para obter mais informações sobre como esta credencial de conta de armazenamento é criado, consulte o artigo [criar um novo serviço](storsimple-virtual-array-manage-service.md#create-a-service).
* **as credenciais da conta de armazenamento na subscrição do serviço** – estas são as credenciais de conta de armazenamento do Azure que estão associadas a mesma subscrição do que o serviço. Para mais informações sobre a forma como estas armazenamento são criadas as credenciais da conta, consulte [sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* **as credenciais da conta de armazenamento fora da subscrição do serviço** – estas são as credenciais de conta de armazenamento do Azure que não estão associadas ao seu serviço e provável que existiam antes do serviço foi criado.

## <a name="add-a-storage-account-credential"></a>Adicionar uma credencial da conta de armazenamento
Pode adicionar uma credencial de conta de armazenamento para a configuração de serviço do Gestor de dispositivos do StorSimple, fornecendo um nome amigável exclusivo e credenciais de acesso que estejam ligadas à conta de armazenamento. Também tem a opção de ativar o modo do secure sockets layer (SSL) para criar um canal seguro para a comunicação de rede entre o dispositivo e a nuvem.

Pode criar várias contas para um fornecedor de serviços de nuvem especificada. Enquanto está a ser guardada a credencial da conta de armazenamento, o serviço tenta comunicar com o fornecedor de serviço em nuvem. As credenciais e o material de acesso que forneceu são autenticados neste momento. Uma credencial de conta de armazenamento é criada apenas se a autenticação é concluída com êxito. Se falhar a autenticação, em seguida, é apresentada uma mensagem de erro apropriada.

Utilize os procedimentos seguintes para adicionar as credenciais da conta de armazenamento do Azure:

* Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição do Azure como o serviço do Gestor de dispositivos
* Para adicionar uma credencial de conta de armazenamento do Azure que está fora da subscrição do serviço do Gestor de dispositivos

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição do Azure como o serviço do Gestor de dispositivos

1. Navegue até ao seu serviço do Gestor de dispositivos, selecione e faça duplo clique. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro de **configuração** secção.
3. Clique em **Adicionar**.
4. No **adicionar uma conta de armazenamento** painel, efetue o seguinte procedimento:
   
    1. Para **subscrição**, selecione **atual**.
    2. Forneça o nome da sua conta de armazenamento do Azure.
    3. Selecione **ativar** para criar um canal seguro para a comunicação de rede entre o dispositivo StorSimple e a nuvem. Selecione **desativar** apenas se estiver a utilizar uma nuvem privada.
    4. Clique em **Adicionar**. Será notificado depois da conta de armazenamento é criada com êxito.<br></br>
   
        ![Adicionar uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento do Azure que está fora da subscrição do serviço do Gestor de dispositivos

1. Navegue até ao seu serviço do Gestor de dispositivos, selecione e faça duplo clique. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro de **configuração** secção. Isto apresenta uma lista as credenciais de conta de armazenamento existente associadas ao serviço StorSimple Manager do dispositivo.
3. Clique em **Adicionar**.
4. No **adicionar uma conta de armazenamento** painel, efetue o seguinte procedimento:
   
    1. Para **subscrição**, selecione **outros**.
   
    2. Forneça o nome da credencial de conta do storage do Azure.
   
    3. No **chave de acesso de conta do Storage** texto caixa, forneça a chave de acesso primária para a credencial da conta de armazenamento do Azure. Para obter esta chave, aceda ao serviço de armazenamento do Azure, selecione a credencial da conta de armazenamento e clique em **gerir chaves de conta**. Agora pode copiar a chave de acesso primária.
   
    4. Para ativar o SSL, clique o **ativar** botão para criar um canal seguro para a comunicação de rede entre o serviço do Gestor de dispositivos do StorSimple e a nuvem. Clique em de **desativar** botão apenas se estiver a utilizar uma nuvem privada.
   
    5. Clique em **Adicionar**. Será notificado depois de criado com êxito a credencial da conta de armazenamento.

5. A credencial da conta de armazenamento acabada de criar é apresentada no painel de serviço do StorSimple configurar Gestor de dispositivos em **as credenciais da conta de armazenamento**.
   
    ![Adicionar uma credencial de conta de armazenamento fora a subscrição de serviço do Gestor de dispositivos](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Editar uma credencial de conta de armazenamento
Pode editar uma credencial de conta de armazenamento utilizada pelo seu dispositivo. Se editar uma credencial de conta de armazenamento que está atualmente em utilização, são os campos disponíveis para modificar a chave de acesso e o modo SSL para a credencial da conta de armazenamento. Pode fornecer a chave de acesso de armazenamento novo ou modificar o **modo SSL ativar** seleção e guardar as definições atualizadas.

#### <a name="to-edit-a-storage-account-credential"></a>Para editar uma credencial de conta de armazenamento
1. Navegue até ao seu serviço do Gestor de dispositivos, selecione e faça duplo clique. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro de **configuração** secção. Isto apresenta uma lista as credenciais de conta de armazenamento existente associadas ao serviço StorSimple Manager do dispositivo.
3. Na lista de tabela das credenciais de conta de armazenamento, selecione e faça duplo clique em conta que pretende modificar.
4. Na credencial da conta de armazenamento **propriedades** painel, efetue o seguinte procedimento:
   
   1. Se necessário, pode modificar o **ativar SSL** seleção de modo.
   2. Pode optar por voltar a gerar as chaves de acesso de credencial de conta de armazenamento. Para obter mais informações, consulte [regenerar as chaves de conta de armazenamento](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys). Forneça a nova chave de credencial de conta de armazenamento. Para uma conta de armazenamento do Azure, esta é a chave de acesso primária.
   3. Clique em **guardar** na parte superior do **propriedades** painel para guardar as definições. As definições são atualizadas no **as credenciais da conta de armazenamento** painel.
      
      ![Editar uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Eliminar uma credencial de conta de armazenamento
> [!IMPORTANT]
> Pode eliminar uma credencial de conta de armazenamento apenas se não está em utilização. Se uma credencial de conta de armazenamento está a ser utilizado, será notificado.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Para eliminar uma credencial de conta de armazenamento
1. Navegue até ao seu serviço do Gestor de dispositivos, selecione e faça duplo clique. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro de **configuração** secção. Isto apresenta uma lista as credenciais de conta de armazenamento existente associadas ao serviço StorSimple Manager do dispositivo.
3. Na lista de tabela das credenciais de conta de armazenamento, selecione e faça duplo clique em conta que pretende eliminar.
4. Na credencial da conta de armazenamento **propriedades** painel, efetue o seguinte procedimento:
   
   1. Clique em **eliminar** para eliminar as credenciais.
   2. Quando lhe for pedido para confirmação, clique em **Sim** para continuar com a eliminação. A listagem de tabela é atualizada para refletir as alterações.
      
      ![Eliminar uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Sincronizar as chaves de credencial de conta de armazenamento
Por motivos de segurança, rotação da chave é frequentemente um requisito nos centros de dados. Um administrador do Microsoft Azure pode voltar a gerar ou alterar a chave primária ou secundária ao aceder diretamente a credencial da conta de armazenamento (através do serviço de armazenamento do Microsoft Azure). O serviço do Gestor de dispositivos do StorSimple não ver automaticamente esta alteração.

Para informar o serviço do Gestor de dispositivos do StorSimple da alteração, terá de aceder ao serviço do Gestor de dispositivos do StorSimple, aceder a credencial da conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo do que foi alterado). O serviço, em seguida, obtém a chave mais recente, encripta as chaves e envia a chave encriptada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Para sincronizar as chaves de credenciais da conta do storage na mesma subscrição que o serviço (apenas para o Azure)
1. No painel de destino de serviço, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, no **configuração** secção, clique em **as credenciais da conta de armazenamento**.
2. No **as credenciais da conta de armazenamento** painel, na lista de armazenamento as credenciais da conta, selecionadas a conta de armazenamento de credenciais cujas chaves que pretende sincronizar.
3. No **propriedades** painel para a credencial da conta de armazenamento selecionada, efetue o seguinte procedimento:
   
    1. Clique em **mais**e, em seguida, clique em **chave de acesso de sincronização**.
   
    2. Quando lhe for pedido para confirmação, clique em **chave sincronização** para concluir a sincronização.
    
4. O serviço do Gestor de dispositivos do StorSimple, terá de atualizar a chave que anteriormente foi alterada no serviço de armazenamento do Microsoft Azure. No **sincronizar a chave de conta do storage** painel, se a chave de acesso primária foi alterada (regenerada), clique em principal e, em seguida, clique em **sincronização chave**. Se a chave secundária foi alterada, clique em **secundário**e, em seguida, clique em **sincronização chave**.
   
    ![Chave de acesso de sincronização](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).


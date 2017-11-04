---
title: "Gerir as suas credenciais de conta de armazenamento StorSimple para dispositivos de série 8000 do StorSimple do Microsoft Azure | Microsoft Docs"
description: "Explica como pode utilizar a página de configuração do Gestor de dispositivos do StorSimple para adicionar, editar, eliminar ou rodar as chaves de segurança para uma conta de armazenamento."
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 36058ad69ea670998b50cf9038741c294a5b79ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Utilizar o serviço StorSimple Manager de dispositivos para gerir as credenciais da conta de armazenamento

## <a name="overview"></a>Descrição geral

O **configuração** secção no painel de serviço do Gestor de dispositivos do StorSimple apresenta todos os parâmetros de serviço global que podem ser criados no serviço do Gestor de dispositivos do StorSimple. Estes parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço e incluem:

* Credenciais da conta de armazenamento
* Modelos de largura de banda 
* Registos de controlo de acesso 

Este tutorial explica como adicionar, editar, eliminar as credenciais da conta de armazenamento ou rodar as chaves de segurança para uma conta de armazenamento.

 ![Lista das credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

As contas do Storage contém as credenciais que o dispositivo StorSimple utiliza para aceder à sua conta de armazenamento com o fornecedor de serviço em nuvem. Para contas de armazenamento do Microsoft Azure, estas são as credenciais, tais como o nome da conta e a chave de acesso primária. 

No **as credenciais da conta de armazenamento** painel, todas as contas de armazenamento criados para a subscrição de faturação são apresentadas em formato tabular que contém as seguintes informações:

* **Nome** – o nome exclusivo atribuído à conta quando foi criado.
* **SSL ativado** – SSL o se estiver ativada e a comunicação do dispositivo para a nuvem é através do canal seguro.
* **Utilizado por** – o número de volumes utilizando a conta de armazenamento.

As tarefas mais comuns relacionadas com contas do storage que podem ser efetuadas são:

* Adicionar uma conta de armazenamento 
* Editar uma conta de armazenamento 
* Eliminar uma conta do Storage 
* Rotação da chave de contas do storage 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Existem três tipos de contas do storage que podem ser utilizados com o dispositivo StorSimple.

* **As contas de armazenamento gerado automaticamente** – como o nome sugere, este tipo de conta de armazenamento é gerado automaticamente quando o serviço é criado pela primeira vez. Para obter mais informações sobre como esta conta de armazenamento é criada, consulte o artigo [passo 1: criar um novo serviço](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) no [implementar o dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md). 
* **As contas de armazenamento na subscrição do serviço** – estas são as contas de armazenamento do Azure que estão associadas a mesma subscrição do que o serviço. Para mais informações sobre a forma como estas armazenamento são criadas contas, consulte [sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md). 
* **As contas do Storage fora da subscrição do serviço** – estas são as contas de armazenamento do Azure que não estão associadas ao seu serviço e provável que existiam antes do serviço foi criado.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento

Pode adicionar uma conta de armazenamento, fornecendo um nome amigável exclusivo e credenciais de acesso que estejam ligadas à conta de armazenamento (com o fornecedor de serviços de nuvem especificada). Também tem a opção de ativar o modo do secure sockets layer (SSL) para criar um canal seguro para a comunicação de rede entre o dispositivo e a nuvem.

Pode criar várias contas para um fornecedor de serviços de nuvem especificada. Tenha em atenção, no entanto, depois de criar uma conta de armazenamento, não é possível alterar o fornecedor de serviço em nuvem.

Enquanto a conta de armazenamento está a ser guardada, o serviço tenta comunicar com o fornecedor de serviço em nuvem. As credenciais e o material de acesso que especificou serão autenticados neste momento. Apenas se a autenticação é concluída com êxito, é criada uma conta de armazenamento. Se falhar a autenticação, irá ser apresentada uma mensagem de erro apropriada.

Utilize os procedimentos seguintes para adicionar as credenciais da conta de armazenamento do Azure:

* Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição do Azure como o serviço do Gestor de dispositivos
* Para adicionar uma credencial de conta de armazenamento do Azure que está fora da subscrição do serviço do Gestor de dispositivos

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento do Azure fora a subscrição de serviço do Gestor de dispositivos do StorSimple

1. Navegue para o dispositivo do serviço StorSimple Manager, selecione e faça duplo clique. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro de **configuração** secção. Isto apresenta uma lista as credenciais de conta de armazenamento existente associadas ao serviço StorSimple Manager do dispositivo.
3. Clique em **Adicionar**.
4. No **adicionar uma credencial de conta de armazenamento** painel, efetue o seguinte procedimento:
   
    1. Para **subscrição**, selecione **outros**.
   
    2. Forneça o nome da credencial de conta do storage do Azure.
   
    3. No **chave de acesso de conta do Storage** texto caixa, forneça a chave de acesso primária para a credencial da conta de armazenamento do Azure. Para obter esta chave, aceda ao serviço de armazenamento do Azure, selecione a credencial da conta de armazenamento e clique em **gerir chaves de conta**. Agora pode copiar a chave de acesso primária.
   
    4. Para ativar o SSL, clique o **ativar** botão para criar um canal seguro para a comunicação de rede entre o serviço do Gestor de dispositivos do StorSimple e a nuvem. Clique em de **desativar** botão apenas se estiver a utilizar uma nuvem privada.
   
    5. Clique em **Adicionar**. Será notificado depois de criado com êxito a credencial da conta de armazenamento.

5. A credencial da conta de armazenamento acabada de criar é apresentada no painel de serviço do StorSimple configurar Gestor de dispositivos em **as credenciais da conta de armazenamento**.
   


## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Pode editar uma conta de armazenamento que é utilizada por um contentor de volume. Se editar uma conta de armazenamento que está a ser utilizada, o campo apenas disponível para modificar é a chave de acesso para a conta de armazenamento. Pode fornecer a nova chave de acesso de armazenamento e guardar as definições atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

    ![Credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. No **as credenciais da conta de armazenamento** painel, na lista de credenciais da conta de armazenamento, selecione e clique naquele que pretende editar. 

3. Pode modificar o **ativar SSL** seleção. Também pode clicar em **mais...**  e, em seguida, selecione **chave de acesso de sincronização para rodar** as chaves de acesso da conta de armazenamento. Aceda a [rotação de contas de armazenamento de chaves](#key-rotation-of-storage-accounts) para obter mais informações sobre como efetuar a rotação da chave. Depois de modificar as definições, clique em **guardar**. 

    ![Guarde as credenciais da conta de armazenamento editado](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Quando lhe for pedida a confirmação, clique em **Sim**. 

    ![Confirmar alterações](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

As definições serão atualizadas e guardadas para a sua conta de armazenamento. 

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

> [!IMPORTANT]
> Pode eliminar uma conta do storage apenas se não está a ser utilizado por um contentor de volume. Se uma conta de armazenamento está a ser utilizada por um contentor de volume, primeiro de eliminar o contentor de volume e, em seguida, elimine a conta de armazenamento associadas.

#### <a name="to-delete-a-storage-account"></a>Para eliminar uma conta de armazenamento

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

2. Na lista de tabela de contas de armazenamento, coloque o cursor sobre a conta que pretende eliminar. Contexto para invocar o menu de contexto e clique em **eliminar**.

    ![Eliminar as credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Quando lhe for pedido para confirmação, clique em **Sim** para continuar com a eliminação. A listagem de tabela será atualizada para refletir as alterações.

    ![Confirmar eliminação](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotação da chave de contas do storage

Por motivos de segurança, rotação da chave é frequentemente um requisito nos centros de dados. Cada subscrição do Microsoft Azure pode ter uma ou mais contas de armazenamento associadas. O acesso a estas contas é controlado pelas chaves de acesso e de subscrição para cada conta de armazenamento. 

Quando cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits que são utilizadas para autenticação quando a conta do storage é acedida. Ter duas chaves de acesso de armazenamento permite-lhe gerar as chaves sem interrupções ao seu serviço de armazenamento ou de acesso a esse serviço. A chave que está atualmente em utilização é o *primário* chave e a chave de cópia de segurança é referido como o *secundário* chave. Um destas duas chaves tem de ser fornecido quando o dispositivo Microsoft Azure StorSimple acede ao seu fornecedor de serviços de armazenamento na nuvem.

## <a name="what-is-key-rotation"></a>O que é a rotação da chave?

Normalmente, as aplicações utilizar apenas uma das chaves de aceder aos seus dados. Após um determinado período de tempo, pode fazer com as suas aplicações de comutador para a segunda chave a utilizar. Depois de ter de mudar as suas aplicações para a chave secundária, pode extinguir a primeira chave e, em seguida, gerar uma nova chave. Utilizar as duas chaves desta forma permite o acesso de aplicações para os dados sem incorrer em qualquer período de inatividade.

As chaves de conta de armazenamento são sempre armazenadas no serviço de um formato encriptado. No entanto, estes podem ser repostos através do serviço do Gestor de dispositivos do StorSimple. O serviço pode obter a chave primária e chave secundária para todas as contas de armazenamento na mesma subscrição, incluindo as contas criadas no serviço de armazenamento, bem como as contas do storage predefinida geradas quando o serviço de serviço do Gestor de dispositivos do StorSimple criado pela primeira vez. O serviço StorSimple Manager de dispositivo será sempre obter estas chaves do portal clássico do Azure e, em seguida, armazená-las de forma encriptada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação

Um administrador do Microsoft Azure pode voltar a gerar ou alterar a chave primária ou secundária ao aceder diretamente a conta de armazenamento (através do serviço de armazenamento do Microsoft Azure). O serviço do Gestor de dispositivos do StorSimple não ver automaticamente esta alteração.

Para informar o serviço do Gestor de dispositivos do StorSimple da alteração, que será necessário para aceder ao serviço do Gestor de dispositivos do StorSimple, aceder à conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo do que foi alterado). O serviço, em seguida, obtém a chave mais recente, encripta as chaves e envia a chave encriptada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Para sincronizar as chaves de contas do storage na mesma subscrição que o serviço 
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.
2. Na listagem de tabela de contas do storage, clique em que pretende modificar. 

    ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Clique em **... Mais** e, em seguida, selecione **chave de acesso de sincronização para rodar**.   

    ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. O serviço do Gestor de dispositivos do StorSimple, terá de atualizar a chave que anteriormente foi alterada no serviço de armazenamento do Microsoft Azure. Se a chave de acesso primária foi alterada (regenerada), selecione **primário** chave. Se a chave secundária tiver sido alterada, selecione **secundário** chave. Clique em **chave sincronização**.
      
      ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Será notificado depois da chave com êxito sycnhronized.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar as chaves de contas do storage fora a subscrição de serviço
1. No **serviços** página, clique em de **configurar** separador.
2. Clique em **adicionar/editar as contas do Storage**.
3. Na caixa de diálogo, efetue o seguinte:
   
   1. Selecione a conta de armazenamento com a chave de acesso que pretende atualizar.
   2. Terá de atualizar a chave de acesso de armazenamento no serviço do Gestor de dispositivos do StorSimple. Neste caso, pode ver a chave de acesso de armazenamento. Introduza a nova chave no **chave de acesso de conta do Storage** caixa. 
   3. Guarde as alterações. Agora deve ser atualizado a sua chave de acesso da conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [segurança do StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [utilizando o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


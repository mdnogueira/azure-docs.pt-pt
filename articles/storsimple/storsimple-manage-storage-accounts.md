---
title: Gerir a sua conta de armazenamento StorSimple | Microsoft Docs
description: "Explica como pode utilizar a página Configurar StorSimple Manager para adicionar, editar, eliminar ou rodar as chaves de segurança para uma conta de armazenamento."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 93207c40-e0eb-489e-8724-59fb94907081
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 4bf8b397d81e12bc48fe3d0ce16d5fff705cedbc
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Utilizar o serviço StorSimple Manager para gerir a sua conta de armazenamento
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [utilizar o serviço StorSimple Manager para gerir a sua conta de armazenamento](storsimple-8000-manage-storage-accounts.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
O **configurar** página apresenta todos os parâmetros de serviço global que podem ser criados no serviço StorSimple Manager. Estes parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço e incluem:

* Contas de armazenamento 
* Modelos de largura de banda 
* Registos de controlo de acesso 

Este tutorial explica como pode utilizar o **configurar** página para adicionar, editar ou eliminar as contas de armazenamento ou rodar as chaves de segurança para uma conta de armazenamento.

 ![Configurar página](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

As contas do Storage contém as credenciais que o dispositivo utiliza para aceder à sua conta de armazenamento com o fornecedor de serviço em nuvem. Para contas de armazenamento do Microsoft Azure, estas são as credenciais, tais como o nome da conta e a chave de acesso primária. 

No **configurar** página, todas as contas de armazenamento criados para a subscrição de faturação são apresentadas em formato tabular que contém as seguintes informações:

* **Nome** – o nome exclusivo atribuído à conta quando foi criado.
* **SSL ativado** – SSL o se estiver ativada e a comunicação do dispositivo para a nuvem é através do canal seguro.
* **Utilizado por** – o número de volumes utilizando a conta de armazenamento.

As tarefas mais comuns relacionadas com contas de armazenamento que podem ser efetuadas no **configurar** página são:

* Adicionar uma conta de armazenamento 
* Editar uma conta de armazenamento 
* Eliminar uma conta do Storage 
* Rotação da chave de contas do storage 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento
Existem três tipos de contas do storage que podem ser utilizados com o dispositivo StorSimple.

* **As contas de armazenamento gerado automaticamente** – como o nome sugere, este tipo de conta de armazenamento é gerado automaticamente quando o serviço é criado pela primeira vez. Para obter mais informações sobre como esta conta de armazenamento é criada, consulte o artigo [passo 1: criar um novo serviço](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) no [implementar o dispositivo StorSimple no local](storsimple-deployment-walkthrough.md). 
* **As contas de armazenamento na subscrição do serviço** – estas são as contas de armazenamento do Azure que estão associadas a mesma subscrição do que o serviço. Para mais informações sobre a forma como estas armazenamento são criadas contas, consulte [sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md). 
* **As contas do Storage fora da subscrição do serviço** – estas são as contas de armazenamento do Azure que não estão associadas ao seu serviço e provável que existiam antes do serviço foi criado.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento
Pode adicionar uma conta de armazenamento, fornecendo um nome amigável exclusivo e credenciais de acesso que estejam ligadas à conta de armazenamento (com o fornecedor de serviços de nuvem especificada). Também tem a opção de ativar o modo do secure sockets layer (SSL) para criar um canal seguro para a comunicação de rede entre o dispositivo e a nuvem.

Pode criar várias contas para um fornecedor de serviços de nuvem especificada. Tenha em atenção, no entanto, depois de criar uma conta de armazenamento, não é possível alterar o fornecedor de serviço em nuvem.

Enquanto a conta de armazenamento está a ser guardada, o serviço tenta comunicar com o fornecedor de serviço em nuvem. As credenciais e o material de acesso que especificou serão autenticados neste momento. Apenas se a autenticação é concluída com êxito, é criada uma conta de armazenamento. Se falhar a autenticação, irá ser apresentada uma mensagem de erro apropriada.

Contas de armazenamento de Gestor de recursos criadas no portal do Azure também são suportadas com StorSimple. As contas de armazenamento do Resource Manager não irão aparecer na lista pendente para seleção quando tentar criar um contentor de volume, apenas as contas de armazenamento criadas no portal clássico do Azure será apresentado. As contas de armazenamento do Resource Manager terá de ser adicionada utilizando o procedimento para adicionar uma conta de armazenamento descrita abaixo.

> [!NOTE]
> O procedimento para adicionar uma conta de armazenamento difere consoante a versão de software do StorSimple que está a utilizar. Lembre-se de que siga o procedimento correto para a sua versão do StorSimple.
> 
> 

[!INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[!INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento
Pode editar uma conta de armazenamento que é utilizada por um contentor de volume. Se editar uma conta de armazenamento que está a ser utilizada, o campo apenas disponível para modificar é a chave de acesso para a conta de armazenamento. Pode fornecer a nova chave de acesso de armazenamento e guardar as definições atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento
1. Na página serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, clique em **configurar**.
2. Clique em **adicionar/editar as contas do Storage**.
3. No **contas de armazenamento de adicionar/editar** caixa de diálogo:
   
   1. Na lista pendente de **contas do Storage**, escolha uma conta existente que pretende modificar. Isto também pode incluir as contas de armazenamento que foram geradas automaticamente quando o serviço foi criado pela primeira vez.
   2. Se necessário, pode modificar o **ativar modo SSL** seleção.
   3. Pode escolher rodar as chaves de acesso da conta de armazenamento. Consulte [rotação de contas de armazenamento de chaves](#key-rotation-of-storage-accounts) para obter mais informações sobre como efetuar a rotação da chave.
   4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) para guardar as definições. As definições serão atualizadas a **configurar** página. Clique em **guardar** para guardar as definições recentemente atualizadas.
      
      ![Editar uma conta de armazenamento](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage
> [!IMPORTANT]
> Pode eliminar uma conta do storage apenas se não está a ser utilizado por um contentor de volume. Se uma conta de armazenamento está a ser utilizada por um contentor de volume, primeiro de eliminar o contentor de volume e, em seguida, elimine a conta de armazenamento associadas.
> 
> 

#### <a name="to-delete-a-storage-account"></a>Para eliminar uma conta de armazenamento
1. Na página de destino do serviço do StorSimple Manager, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, clique em **configurar**.
2. Na lista de tabela de contas de armazenamento, coloque o cursor sobre a conta que pretende eliminar.
3. Um ícone Eliminar (**x**) será apresentado na coluna da direita Alpine para essa conta de armazenamento. Clique em de **x** ícone Eliminar as credenciais.
4. Quando lhe for pedido para confirmação, clique em **Sim** para continuar com a eliminação. A listagem de tabela será atualizada para refletir as alterações.

## <a name="key-rotation-of-storage-accounts"></a>Rotação da chave de contas do storage
Por motivos de segurança, rotação da chave é frequentemente um requisito nos centros de dados. 

> [!NOTE]
> As seguintes informações de rotação da chave e o procedimento de rotação aplicam-se apenas a contas de armazenamento Microsoft Azure. Se estiver a utilizar outro fornecedor de serviço em nuvem, pode gerir chaves de conta de armazenamento através do dashboard esse fornecedor.
> 
> 

Cada subscrição do Microsoft Azure pode ter uma ou mais contas de armazenamento associadas. O acesso a estas contas é controlado pelas chaves de acesso e de subscrição para cada conta de armazenamento. 

Quando cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits que são utilizadas para autenticação quando a conta do storage é acedida. Ter duas chaves de acesso de armazenamento permite-lhe gerar as chaves sem interrupções ao seu serviço de armazenamento ou de acesso a esse serviço. A chave que está atualmente em utilização é o *primário* chave e a chave de cópia de segurança é referido como o *secundário* chave. Um destas duas chaves tem de ser fornecido quando o dispositivo Microsoft Azure StorSimple acede ao seu fornecedor de serviços de armazenamento na nuvem.

## <a name="what-is-key-rotation"></a>O que é a rotação da chave?
Normalmente, as aplicações utilizar apenas uma das chaves de aceder aos seus dados. Após um determinado período de tempo, pode fazer com as suas aplicações de comutador para a segunda chave a utilizar. Depois de ter de mudar as suas aplicações para a chave secundária, pode extinguir a primeira chave e, em seguida, gerar uma nova chave. Utilizar as duas chaves desta forma permite o acesso de aplicações para os dados sem incorrer em qualquer período de inatividade.

As chaves de conta de armazenamento são sempre armazenadas no serviço de um formato encriptado. No entanto, estes podem ser repostos através do serviço StorSimple Manager. O serviço pode obter a chave primária e chave secundária para todas as contas de armazenamento na mesma subscrição, incluindo as contas criadas no serviço de armazenamento, bem como as contas do storage predefinida geradas quando o serviço StorSimple Manager foi inicialmente criar. O serviço StorSimple Manager irá obter sempre estas chaves do portal clássico do Azure e, em seguida, armazená-las de forma encriptada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação
Um administrador do Microsoft Azure pode voltar a gerar ou alterar a chave primária ou secundária ao aceder diretamente a conta de armazenamento (através do serviço de armazenamento do Microsoft Azure). O serviço StorSimple Manager não consegue ver esta alteração automaticamente.

Para informar o serviço StorSimple Manager da alteração, que será necessário para aceder ao serviço StorSimple Manager, aceder à conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo do que foi alterado). O serviço, em seguida, obtém a chave mais recente, encripta as chaves e envia a chave encriptada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Para sincronizar as chaves de contas do storage na mesma subscrição que o serviço (apenas para o Azure)
1. No **serviços** página, clique em de **configurar** separador.
2. Clique em **adicionar/editar as contas do Storage**.
3. Na caixa de diálogo, efetue o seguinte:
   
   1. Selecione a conta de armazenamento com a chave de que pretende sincronizar. As chaves de conta de armazenamento são encriptadas quando são apresentadas.
   2. O serviço StorSimple Manager, terá de atualizar a chave que anteriormente foi alterada no serviço de armazenamento do Microsoft Azure. Se a chave de acesso primária foi alterada (regenerada), clique em **sincronizar a chave primária**. Se a chave secundária foi alterada, clique em **sincronizar a chave secundária**.
      
      ![sincronizar chaves](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar as chaves de contas do storage fora a subscrição de serviço
1. No **serviços** página, clique em de **configurar** separador.
2. Clique em **adicionar/editar as contas do Storage**.
3. Na caixa de diálogo, efetue o seguinte:
   
   1. Selecione a conta de armazenamento com a chave de acesso que pretende atualizar.
   2. Terá de atualizar a chave de acesso de armazenamento no serviço StorSimple Manager. Neste caso, pode ver a chave de acesso de armazenamento. Introduza a nova chave no **chave de acesso de conta do Storage**y caixa. 
   3. Guarde as alterações. Agora deve ser atualizado a sua chave de acesso da conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [segurança do StorSimple](storsimple-security.md).
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).


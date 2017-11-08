---
title: "Implementar o serviço StorSimple Manager | Microsoft Docs"
description: "Explica como criar e eliminar o serviço StorSimple Manager no portal clássico do Azure e descreve como gerir a chave de registo do serviço."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bc1d5650-275c-42ed-bc77-cdb596f85943
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee22a31e9c0ec23d9b042dc894cafe0fc346e742
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-the-storsimple-manager-service-in-the-azure-classic-portal"></a>Implementar o serviço StorSimple Manager no portal clássico do Azure
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [implementar o serviço StorSimple Manager no portal clássico do Azure](storsimple-8000-manage-service.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Descrição geral
O serviço StorSimple Manager é executado no Microsoft Azure e se liga a diversos dispositivos StorSimple. Depois de criar o serviço, pode utilizá-lo para gerir os dispositivos do portal clássico do Microsoft Azure em execução num browser. Isto permite-lhe monitorizar todos os dispositivos que estão ligados ao serviço StorSimple Manager a partir de uma única localização central, deste modo, minimizando o fardo administrativo.

A página de destino do StorSimple Manager apresenta uma lista de todos os serviços do StorSimple Manager que pode utilizar para gerir os dispositivos de armazenamento StorSimple. Para cada serviço StorSimple Manager, as seguintes informações são apresentadas na página do StorSimple Manager:

* **Nome** – o nome que foi atribuído ao serviço StorSimple Manager quando foi criado. **Não é possível alterar o nome do serviço após o serviço é criado. Isto também se aplica para outras entidades, tais como os dispositivos, volumes, contentores de volume e políticas de cópia de segurança que não não possível mudar o nome no portal clássico do Azure.**
* **Estado** – o estado do serviço, que pode ser **Active Directory**, **criar**, ou **Online**.
* **Localização** – a localização geográfica na qual o dispositivo StorSimple será implementado.
* **Subscrição** – a subscrição de faturação que está associada ao seu serviço.

As tarefas comuns que podem ser efetuadas através de página do StorSimple Manager são:

* Criar um serviço
* Eliminar um serviço
* Obter a chave de registo do serviço
* Regenerar a chave de registo do serviço

Este tutorial descreve como efetuar cada uma destas tarefas.

## <a name="create-a-service"></a>Criar um serviço
Utilize o **criação rápida** opção para criar um serviço StorSimple Manager se pretender implementar o dispositivo StorSimple. Para criar um serviço, tem de ter:

* Uma subscrição com um Enterprise Agreement
* Uma conta de armazenamento do Microsoft Azure Active Directory
* As informações de faturação que são utilizadas para gestão de acesso

Também pode optar por gerar uma conta do storage predefinida quando criar o serviço.

Um único serviço pode gerir vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço funcione com diferentes subscrições, as organizações ou até mesmo as localizações de implementação. Tenha em atenção que precisar de separar as instâncias do serviço StorSimple Manager para gerir dispositivos de série 8000 do StorSimple e matrizes Virtual StorSimple.

> [!IMPORTANT] 
> Se tiver um serviço não utilizado criado (nenhum dispositivo de operações que foram efetuadas neste recurso) antes de Agosto de 2016, não podem ser gerido através do portal do Azure ou o portal clássico do Azure. Recomendamos que crie um novo serviço no portal do Azure.

Execute os seguintes passos para criar um serviço.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminar um serviço
Antes de eliminar um serviço, certifique-se de que não existem dispositivos ligados a estiver a utilizar. Se o serviço está em utilização, desative os dispositivos ligados. A operação de desativar irá sever a ligação entre o dispositivo e o serviço, mas manter os dados de dispositivo na nuvem.

> [!IMPORTANT] 
> Depois de um serviço é eliminado, não é possível reverter a operação. Qualquer dispositivo que estava a utilizar o serviço terá de ser antes de poder ser utilizada com outro serviço de reposição de fábrica. Neste cenário, os dados locais no dispositivo, bem como a configuração, serão perdidos.

Execute os seguintes passos para eliminar um serviço.

### <a name="to-delete-a-service"></a>Para eliminar um serviço
1. No **serviço StorSimple Manager** página, selecione o serviço que pretende eliminar.
2. Clique em **eliminar** na parte inferior da página.
3. Clique em **Sim** na notificação de confirmação. Pode demorar alguns minutos para que o serviço será eliminado.

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço
Depois de ter criado com êxito um serviço, terá de registar o dispositivo StorSimple com o serviço. Para registar o primeiro dispositivo StorSimple, terá da chave de registo do serviço. Para registar dispositivos adicionais com um serviço StorSimple, terá da chave de registo e a chave de encriptação de dados do serviço (o que é gerado no primeiro dispositivo durante o registo). Para mais informações sobre a chave de encriptação de dados do serviço, consulte [segurança do StorSimple](storsimple-security.md). Pode obter a chave de registo acedendo **chave de registo** no **serviços** página.

Execute os passos seguintes para obter a chave de registo do serviço.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

Manter a chave de registo do serviço numa localização segura. Terá esta chave, bem como a chave de encriptação de dados de serviço, para registar dispositivos adicionais este serviço. Depois de obter a chave de registo do serviço, terá de configurar o dispositivo através do Windows PowerShell para StorSimple interface.

Para obter mais informações sobre como utilizar esta chave de registo, consulte [passo 3: configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registo do serviço
Terá de voltar a gerar uma chave de registo do serviço, se for necessário para executar a rotação da chave ou se a lista de administradores de serviço foi alterada. Quando voltar a gerar a chave, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos que foram registados já não são afetados por este processo.

Execute os seguintes passos para voltar a gerar uma chave de registo do serviço.

### <a name="to-regenerate-the-service-registration-key"></a>A regenerar a chave de registo do serviço
1. No **serviço StorSimple Manager** página, clique em **chave de registo**.
2. No **chave de registo do serviço** caixa de diálogo, clique em **regenerar**.
3. Verá uma mensagem de confirmação. Clique em **OK** para continuar com a regeneração.
4. Será apresentada uma nova chave de registo do serviço.
5. Copie esta chave e guarde-a para registar os novos dispositivos com este serviço.
6. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-service/HCS_CheckIcon.png) para fechar esta caixa de diálogo.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [processo de implementação do StorSimple](storsimple-deployment-walkthrough-u2.md).
* Saiba mais sobre [gerir a conta de armazenamento StorSimple](storsimple-manage-storage-accounts.md).
* Saiba mais sobre como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

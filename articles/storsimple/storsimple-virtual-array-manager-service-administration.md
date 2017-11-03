---
title: "Administração do Microsoft Azure StorSimple Manager Virtual matriz | Microsoft Docs"
description: "Saiba como gerir a matriz de Virtual do StorSimple no local utilizando o serviço StorSimple Manager de dispositivos no portal do Azure."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: a74a160eae88a2d03460a1346479c333d8f9d524
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para administrar a matriz de Virtual StorSimple
![fluxo de processo de configuração](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Descrição geral
Este artigo descreve a interface de serviço do Gestor de dispositivos do StorSimple, incluindo como ligar a e as várias opções disponíveis e fornece ligações para os fluxos de trabalho específicas que podem ser efetuadas através deste IU.

Depois de ler este artigo, ficará a saber como:

* Ligar ao serviço do Gestor de dispositivos do StorSimple
* Navegue até o Gestor de dispositivos do StorSimple da IU
* Administrar a matriz de Virtual StorSimple através do serviço do Gestor de dispositivos do StorSimple

> [!NOTE]
> Para ver as opções de gestão disponíveis para o dispositivo de série 8000 do StorSimple, aceda a [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Ligar ao serviço do Gestor de dispositivos do StorSimple
O serviço do Gestor de dispositivos do StorSimple é executado no Microsoft Azure e liga-se a várias matrizes de Virtual StorSimple. Utilizar um portal central do Microsoft Azure em execução num browser para gerir estes dispositivos. Para ligar ao serviço do Gestor de dispositivos do StorSimple, efetue o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Aceda a [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Utilizar as credenciais da conta Microsoft, inicie sessão no portal do Microsoft Azure (localizado na parte superior direita do painel).
3. Navegar até procurar--> 'De filtro' sobre gestores de dispositivo StorSimple para ver todos os seus gestores de dispositivo numa determinada subscrição.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para efetuar tarefas de gestão
A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados no painel de resumo do serviço Gestor de dispositivos do StorSimple. Estas tarefas são organizadas com base nos painéis em que são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique o procedimento adequado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do Gestor de dispositivos do StorSimple
| Se pretender efetuar este procedimento... | Utilize este procedimento |
| --- | --- | --- |
| Criar um serviço</br>Eliminar um serviço</br>Obter a chave de registo do serviço</br>Regenerar a chave de registo do serviço |[Implementar o serviço do Gestor de dispositivos do StorSimple](storsimple-virtual-array-manage-service.md) |
| Consulte os registos de atividade |[Utilize o resumo de serviço do StorSimple](storsimple-virtual-array-service-summary.md) |
| Desativar uma matriz de Virtual</br>Eliminar uma matriz de Virtual |[Desative ou elimine uma matriz de virtual](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Ativação pós-falha de dispositivo e de recuperação de desastre</br>Pré-requisitos de ativação pós-falha</br>Recuperação de desastre de continuidade de negócio (BCDR)</br>Erros durante a recuperação após desastre |[Ativação pós-falha de dispositivo e de recuperação após desastre para a matriz de Virtual StorSimple](storsimple-virtual-array-failover-dr.md) |
| Cópia de segurança de partilhas e os volumes</br>Efetuar uma cópia de segurança manual</br>Alterar a agenda de cópia de segurança</br>Ver as cópias de segurança existentes |[Criar cópias de segurança a matriz de Virtual StorSimple](storsimple-virtual-array-backup.md) |
| Partilhas de clone a partir de um conjunto de cópia de segurança</br>Volumes de clone de um conjunto de cópia de segurança</br>Recuperação ao nível do item (apenas servidor de ficheiros) |[Clonar a partir de uma cópia de segurança da sua matriz de Virtual StorSimple](storsimple-virtual-array-clone.md) |
| Sobre as contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage |[Gerir contas de armazenamento para a matriz de Virtual StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Sobre os registos de controlo de acesso</br>Adicionar ou modificar um registo de controlo de acesso </br>Elimine um registo de controlo de acesso |[Gerir registos de controlo de acesso para a matriz de Virtual StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Ver detalhes da tarefa |[Gerir tarefas de matriz Virtual StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Configurar definições de alerta</br>Receber notificações de alerta</br>Gerir alertas</br>Rever os alertas |[Ver e gerir alertas para a matriz de Virtual StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Modifique a palavra-passe de administrador do dispositivo |[Alterar a palavra-passe de administrador do dispositivo de matriz Virtual StorSimple](storsimple-virtual-array-change-device-admin-password.md) |
| Instalar atualizações de software |[Atualizar a matriz de Virtual](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Tem de utilizar o [IU da web do local](storsimple-ova-web-ui-admin.md) para as seguintes tarefas:
> 
> * [Obter a chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Criar um pacote de suporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Parar e reiniciar uma matriz de Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para informações sobre a IU da web e como utilizá-lo, aceda a [utilizar a IU da web do StorSimple para administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).


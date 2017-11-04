---
title: "Migrar contas do storage, subscrições para o seu serviço do Gestor de dispositivos do StorSimple | Microsoft Docs"
description: "Saiba como migrar as subscrições, contas do storage para o Gestor de dispositivos do StorSimple service8000."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: alkohli
ms.openlocfilehash: edac2635086106f83b30316ab190bead5245f195
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrar subscrições e contas de armazenamento associadas ao serviço do Gestor de dispositivos do StorSimple

Poderá ter de mover o serviço do StorSimple para uma novo inscrição ou para uma nova subscrição. Estes cenários de migração são as alterações de conta ou alterações de centro de dados. Utilize a tabela seguinte para compreender qual estes cenários são suportadas, incluindo os passos detalhados para mover.

## <a name="account-changes"></a>Alterações de conta

| Pode mover...| Suportado| Período de indisponibilidade| Processo de suporte do Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Uma subscrição completa (inclui as contas de armazenamento e de serviço do StorSimple) para inscrição outra? | Sim       | Não       | **Transferência de inscrição**<br>Utilização:<li>Quando comprar um novo compromisso do Azure com um novo contrato.</li><li>Quer migrar todas as subscrições e contas da inscrição antiga para o novo. Isto inclui todos os serviços do Azure sob a subscrição antigo.</li> | **Passo 1: Abra um pedido de suporte de operação do Azure Enterprise.**<li>Aceda a [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li> Selecione **inscrição administração** e, em seguida, selecione **transferir da inscrição de um para uma novo inscrição**.<br>**Passo 2: Fornecer as informações pedidas**<br>Incluem:<li>número de inscrição de origem</li><li> número de inscrição de destino</li><li>Data efetiva de transferência|
| StorSimple serviço a partir de uma conta existente para um novo inscrição?    | Sim       | Não       | **Transferência de conta**<br>Utilização:<li>Quando não pretender que uma transferência completa de inscrição.</li><li>Apenas pretender mover contas específicas para uma inscrição de novo.</li>| **Passo 1: Abra um pedido de suporte de operação do Azure Enterprise.**<li>Aceda a [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li>Selecione **inscrição administração** e, em seguida, selecione **transferir uma conta de EA para uma novo inscrição**.<br>**Passo 2: Fornecer as informações pedidas**<br>Incluem:<li>número de inscrição de origem</li><li> número de inscrição de destino</li><li>Data efetiva de transferência|
| Serviço de StorSimple a partir de uma subscrição a outra subscrição?      | Não        |    Sim         | Nenhum processo manual|<li>Migre dados desativar o dispositivo StorSimple.</li><li>Efetuar uma reposição de fábrica do dispositivo, este elimina quaisquer dados locais no dispositivo.</li><li>Registe o dispositivo com a nova subscrição para um serviço do Gestor de dispositivos do StorSimple.</li><li>Migre os dados para o dispositivo.|
| Dispositivo StorSimple a partir de um serviço do Gestor de dispositivos do StorSimple para outro serviço numa região diferente?      | Não        | Sim            | Nenhum processo manual |Igual acima.|
| Conta de armazenamento para uma nova subscrição ou grupo de recursos?     | Sim        | Não             |Mover a conta de armazenamento para outro subscrição ou grupo de recursos |Após a mudança, se forem atualizadas as chaves de acesso da conta de armazenamento, o utilizador terá de configurar as chaves de acesso manualmente para a conta de armazenamento migrados através do serviço do Gestor de dispositivos do StorSimple.|
| Conta de armazenamento clássico para uma conta do storage do Azure Resource Manager      | Sim        | Não             |Migrar do clássico para o Azure Resource Manager |<li>Para obter instruções detalhadas sobre como migrar uma conta de armazenamento do clássico para o Azure Resource Manager, aceda a [migrar uma conta de armazenamento clássicas](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Se as chaves de acesso da conta de armazenamento forem atualizadas após a migração, o utilizador terá de sincronizar as chaves de acesso para a conta de armazenamento migrados através do serviço do Gestor de dispositivos do StorSimple. Isto é para garantir que os dispositivos StorSimple continuam a funcionar normalmente e são capazes de camada de dados principal/cópia de segurança no Azure. Para obter instruções detalhadas sobre a sincronização de chaves de acesso, aceda a [fluxo de trabalho de rotação](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> No caso de uma aplicação de nuvem do StorSimple, se a conta de armazenamento clássico é migrada, mas a máquina virtual subjacente ainda permanece no clássica, o dispositivo de deve funcionar corretamente. Se a máquina virtual subjacente para a aplicação de nuvem é migrada, em seguida, a funcionalidade de desativar e eliminar não funcionará.</li><li> Tem de criar um novo aparelhos de nuvem do StorSimple no portal do Azure e, em seguida, efetuar a ativação pós-falha de aplicações de nuvem mais antigas. Não é possível criar uma aplicação de nuvem do StorSimple no portal do Azure através de uma conta de armazenamento clássicas, têm de ter uma conta de armazenamento do Azure Resource Manager. Para obter mais informações, aceda a [implementar e gerir um dispositivo de nuvem do StorSimple](storsimple-8000-cloud-appliance-u2.md).</li>|Igual acima.|

## <a name="datacenter-changes"></a>Alterações do Centro de dados

| Pode mover...| Suportado|Período de indisponibilidade| Processo de suporte do Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Um serviço StorSimple a partir de um datacenter do Azure para outro? | Não | Sim |Nenhum processo manual  |<li>Migre dados desativar o dispositivo StorSimple.</li><li>Efetuar uma reposição de fábrica do dispositivo, este elimina quaisquer dados locais no dispositivo.</li><li>Registe o dispositivo com a nova subscrição para um novo serviço do Gestor de dispositivos do StorSimple.</li><li>Migre os dados para o dispositivo.|
| Uma conta de armazenamento de um datacenter do Azure para outro? | Não |Sim  |Nenhum processo manual  | Igual acima.|

## <a name="next-steps"></a>Passos seguintes

* [Implementar o serviço do Gestor de dispositivos do StorSimple](storsimple-8000-manage-service.md)
* [Implementar o dispositivo de série 8000 do StorSimple no portal do Azure](storsimple-8000-deployment-walkthrough-u2.md)

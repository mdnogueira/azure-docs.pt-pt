---
title: "Mover do clássico para FAQ do portal do Azure | Microsoft Docs"
description: "Fornece respostas a perguntas mais frequentes sobre como mover dispositivos StorSimple do clássico para o portal do Azure."
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
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 06e8d99aa2ad4eb11e594a729c6dab39d5cd1eb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Mover o serviço StorSimple Manager de dispositivo do clássico para o portal do Azure: Perguntas mais frequentes sobre (FAQ)

## <a name="overview"></a>Descrição geral

Seguem-se perguntas e respostas que possam ter quando mover o serviço StorSimple Manager de dispositivos com o portal clássico do Azure para o portal do Azure.

Perguntas e respostas são dispostas nas seguintes categorias:

* Mover o serviço do Gestor de dispositivos do StorSimple
* Mover as contas de armazenamento
* Com o Azure Resource Manager cmdlets baseados no
* Utilizar o serviço StorSimple Manager de dados
* Diversos

## <a name="moving-storsimple-device-manager-service"></a>Mover o serviço do Gestor de dispositivos do StorSimple

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Uma vez posso ter movida para o portal do Azure, pode ainda criar um serviço StorSimple Manager no portal clássico?

Não. Depois de ter migrado do serviço StorSimple Manager para o portal do Azure, não é possível criar um novo serviço no portal clássico. Além disso, não é possível gerir o seu dispositivo através do portal clássico. Para obter mais informações, aceda a [mover o seu serviço para o portal do Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>Posso ter vários gestores de StorSimple em execução no portal clássico. Pode escolher aqueles mover para o portal do Azure?

Não. Não é possível escolher os gestores de StorSimple para mover para o portal do Azure. São movidos todos os gestores de StorSimple na sua subscrição.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>Iniciar a migração do meu serviço para o novo portal do Azure. Deve eliminar o StorSimple Manager no portal clássico? 

Não. Não tente eliminar qualquer serviço que foram movidas do portal clássico. Aguarde a migração concluir, depois de todos os gestores de StorSimple movido para o novo portal, não terá de eliminar todos os serviços do portal clássico. Eventualmente o portal clássico foi preterido.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Pode mudar o nome do meu dispositivo do serviço StorSimple Manager no portal do Azure?

Não. Não é possível alterar o nome do serviço após o serviço é criado no portal do Azure. O mesmo comportamento também se verifica para outras entidades, tais como os dispositivos, volumes, contentores de volume e políticas de cópia de segurança.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Pode criar os dispositivos de nuvem do 8010/8020 StorSimple com o modelo de implementação Azure Resource Manager?

Sim. Pode criar novas aplicações de nuvem de StorSimple 8010/8020 no modelo de implementação Azure Resource Manager. As VMs para estas aplicações em nuvem subjacentes também são no modelo de implementação Resource Manager.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Quando iremos migrar subscrições no portal do Azure, serão as VMs subjacentes para os dispositivos de nuvem do StorSimple também migrar para o modelo de implementação de gestão de recursos do Azure?

A movimentação de serviço do StorSimple é independente da gestão das VMs para os dispositivos de nuvem do StorSimple. Pode totalmente gerir as VMs de aparelhos de nuvem do StorSimple no clássica e o portal do Azure hoje mesmo.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Pode gerir existente clássico 8010/8020 StorSimple nuvem aplicação do portal do Azure?

Sim. As VMs associadas com os dispositivos 8010/8020 nuvem existentes podem ser geridas do portal do Azure.

Se tiver criado aparelhos de nuvem do StorSimple modelo 8010/8020 com atualização 3.0 e superior, não são afetados pelo seu serviço móvel para o novo portal do Azure. Deverá conseguir gerir totalmente os dispositivos de nuvem sem quaisquer problemas. 

Se tiver aplicações em nuvem com versões anteriores ao 3.0 de atualização no portal clássico, em seguida, apenas tiver limitados funcionalidade disponível. Para obter mais informações, visite o [lista de operações não suportadas para dispositivos que executam versões anteriores ao Update 3](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

Não pode atualizar uma aplicação da cloud. Utilize a versão mais recente do software para criar um novo dispositivo de nuvem e, em seguida, efetuar a ativação pós-falha os contentores de volume existente para o novo dispositivo de nuvem criados. Para obter mais informações, aceda a [efetuar a ativação pós-falha para a aplicação de nuvem](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Os meus dispositivos de série 8000 do StorSimple está em execução de atualização 2.0. Posso migradas meu serviço para o novo portal do Azure. O meu dispositivo ligado com êxito, mas parece que não consigo gerir totalmente o meu dispositivo. Como resolver o este comportamento?

O novo portal do Azure é suportada apenas para dispositivos StorSimple com atualização 3.0 e superior. Se o dispositivo tiver o Update 2.0, apenas tem limitada funcionalidades disponíveis para este dispositivo. Para obter mais informações, visite o [lista de operações não suportadas para dispositivos que executam versões anteriores ao Update 3](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

Para gerir totalmente o seu dispositivo, instale a atualização mais recente no seu dispositivo. Para obter mais informações, aceda a [instalar Update 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Posso mover apenas o meu serviço StorSimple Manager para o portal do Azure. Estou a ver alguns alertas relacionados com o meu dispositivo. Este comportamento relacionadas com a mudança?

Não. A movimentação propriamente dito não deve resultar em erros ou alertas. Siga as recomendações de alerta para resolver os alertas.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>Estou a utilizar um dispositivo de série do StorSimple 5000/7000. Estes também são suportados no portal do Azure?

Não. Os dispositivos das séries 5000/7000 do StorSimple não são suportados no portal do Azure.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>Estou a planeia migrar dados de dispositivos de série do StorSimple 5000/7000 para um dispositivo de série 8000 do StorSimple. Como mover um serviço para o portal do Azure afeta os meus migração de dados? 

Pode migrar dados do seu dispositivo de série do StorSimple 5000/7000 num dispositivo da série 8000 do StorSimple em execução no portal do Azure. Para ativar a migração de dados da série de 5000/7000 8000 série, terá [iniciar um pedido de suporte com a Microsoft Support](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Mover as subscrições, as contas do storage, grupos de recursos

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>Posso mover Gestor de dispositivos do StorSimple de uma subscrição para outra subscrição no portal do Azure?

Não. Mover o serviço StorSimple Manager de dispositivos de uma subscrição para outra não é suportada. Pode efetuar um processo manual que consiste dos seguintes passos:

* Migre dados desativar o dispositivo StorSimple.
* Efetuar uma reposição de fábrica do dispositivo, este elimina quaisquer dados locais no dispositivo.
* Registe o dispositivo com a nova subscrição para um serviço do Gestor de dispositivos do StorSimple.
* Migre os dados para o dispositivo.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>É necessário que migrar a conta de armazenamento para o modelo de implementação Azure Resource Manager?

Não. As contas de armazenamento clássicas podem ser totalmente geridas do portal do Azure. Ao mover o serviço do StorSimple no portal do Azure, a conta de armazenamento continua a funcionar como anteriormente.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>O que acontece para a conta do storage depois de migrar o serviço para o portal do Azure?

A mudança do serviço não está relacionado com a gestão de conta de armazenamento. Clássica e contas de armazenamento do Azure Resource Manager podem ser totalmente geridas dentro do portal do Azure. Depois de mover o seu serviço para o portal do Azure, o dispositivo deve continuar a executar com esta conta de armazenamento e não deverá haver nenhum impacto aos seus dados.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>Posso migrar Gestor de dispositivos do StorSimple de um grupo de recursos para outro?

Não. Não é possível mover um Gestor de dispositivos do StorSimple criado com um grupo de recursos para outro grupo de recursos.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Com o Azure Resource Manager cmdlets baseados no

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Posso mover para o novo portal do Azure. Agora os meus scripts com base nos cmdlets do PowerShell de modelo de implementação clássico do Azure não estiverem a funcionar? O que é necessário fazer?

Os cmdlets do PowerShell do modelo de implementação clássico do Azure existentes não são suportados no portal do Azure. Atualize os scripts para gerir os seus dispositivos através do Gestor de recursos do Azure. Para obter mais informações sobre como atualizar os scripts, aceda a [amostras para o novo portal do Azure](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>Posso apenas movê-la para o portal do Azure e necessários para o rollover da chave de encriptação de dados do serviço. Onde está esta opção no portal do Azure?

A opção de rollover de chave de encriptação de dados do serviço não está no portal do Azure. Para obter mais informações sobre como efetuar esta rollover no portal do Azure, aceda a [alterar a chave de encriptação de dados do serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Estiver a utilizar o Windows PowerShell para StorSimple cmdlets no dispositivo StorSimple para executar operações essa extrair um pacote de suporte. Estes cmdlets afetados quando posso mover para o novo portal do Azure?

Não. Com o serviço de mover para o novo portal do Azure, não deverá haver nenhum impacto sobre o Windows cmdlets do PowerShell para StorSimple associados ao dispositivo do StorSimple no local (que propriamente dito não é afetado pela movimentação). Pode continuar a utilizar estes cmdlets para criar um pacote de suporte sem quaisquer problemas mesmo no portal do Azure. Apenas os cmdlets do PowerShell do Azure de implementação clássica modelo são afetados por este mover.

## <a name="moving-storsimple-data-manager-service"></a>Mover o serviço StorSimple Manager de dados

### <a name="i-am-using-storsimple-data-manager-service-how-should-i-proceed-with-this-move"></a>Estou a utilizar o serviço StorSimple Manager de dados. Como deve continuar com esta mudança?

Se estiver a utilizar o serviço StorSimple Manager de dados, tem de mover os gestores de dispositivo do StorSimple primeiro ao portal do Azure. Depois de concluída a mover, crie os gestores de dados do novo StorSimple no portal do Azure. Gestores de dados do StorSimple criados antes da movimentação não funcionam.

Para obter mais informações sobre a migração de serviço do Gestor de dispositivos do StorSimple, aceda a [mover o seu serviço para o portal do Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal). Para obter mais informações sobre a criação do Gestor de dados do StorSimple, aceda a [criar um serviço StorSimple Manager de dados](storsimple-data-manager-ui.md).

## <a name="miscellaneous"></a>Diversos

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>Estou a ser executado Snapshot Manager do StorSimple para os meus dispositivos de série 8000. Existe qualquer impacto no Snapshot Manager do StorSimple quando posso mover para o portal do Azure?

Não. Não há nenhum impacto para Snapshot Manager do StorSimple ao mover o serviço para o portal do Azure. O dispositivo e o Snapshot Manager do StorSimple continuam a funcionar como anteriormente.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>Pode mudar o nome do meu dispositivo StorSimple, contentores de volume ou volumes?

Não. Não é possível mudar o nome de dispositivos, volumes, contentores de volume ou políticas de cópia de segurança no portal do Azure.

## <a name="next-steps"></a>Passos seguintes

Saiba como passo a passo [mover o serviço StorSimple Manager de dispositivo ao portal do Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).




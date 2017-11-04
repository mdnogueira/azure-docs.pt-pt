---
title: Desativar e eliminar um dispositivo StorSimple | Microsoft Docs
description: "Descreve como remover o dispositivo StorSimple do serviço ao desativar-o primeiro e, em seguida, eliminá-lo."
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 155cda38-c5ae-45dc-b7e8-6444494afc9e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c000a642aa088ac80cc7077453b87e9a47f96900
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-8000-series-device-via-storsimple-manager-service"></a>Desativar e eliminar um dispositivo da série 8000 do StorSimple através do serviço StorSimple Manager
## <a name="overview"></a>Descrição geral
Poderá pretender assumir um dispositivo StorSimple fora de serviço (por exemplo, se estiver a substituir ou atualizar o seu dispositivo ou se já não estiver a utilizar StorSimple). Se for este o caso, terá de desativar o dispositivo antes de poder eliminá-la. Desativar servidores a ligação entre o dispositivo e o serviço StorSimple Manager correspondente. Este tutorial explica como remover um dispositivo StorSimple do serviço ao primeiro o desativar e, em seguida, eliminá-lo. 

Quando desativar um dispositivo, todos os dados que foram armazenados localmente no dispositivo deixarão de estar acessíveis. Apenas os dados associados ao dispositivo que foram armazenado na nuvem podem ser recuperados.  

> [!WARNING]
> A desativação de uma operação permanente e não pode ser anulada. Não é possível registar um dispositivo desativado com o serviço StorSimple Manager, a menos que o primeiro é repor as predefinições de fábrica. 
> 
> O reposição de fábrica processo elimina todos os dados que foram armazenados localmente no seu dispositivo. Por conseguinte, é essencial que tirar um instantâneo de nuvem de todos os seus dados antes de desativar um dispositivo. Isto permitirá recuperar todos os dados de uma fase posterior.
> 
> 

Este tutorial explica como:

* Desativar um dispositivo e eliminar os dados
* Desativar um dispositivo e manter os dados

Também explica como a desativação e a eliminação funciona num dispositivo virtual StorSimple.

> [!NOTE]
> Antes de desativar um dispositivo StorSimple de físico ou virtual, certifique-se para ou elimina os clientes e os anfitriões que dependem desse dispositivo.
> 
> 

## <a name="deactivate-and-delete-data"></a>Desativar e eliminação de dados
Se estiver interessado em eliminar completamente o dispositivo e não pretende manter os dados no dispositivo, em seguida, conclua os seguintes passos.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e eliminar os dados
1. Antes de desativar um dispositivo, tem de eliminar o volume de contentores (e os volumes) associados ao dispositivo. Pode eliminar contentores de volume apenas depois de ter eliminado as cópias de segurança associadas.
2. Desative o dispositivo da seguinte forma:
   
   1. O serviço StorSimple Manager **dispositivos** página, selecione o dispositivo que pretende desativar e, na parte inferior da página, clique em **desativar**.
   2. Será apresentada uma mensagem de confirmação. Clique em **Sim** para continuar. O processo de desativar irá iniciar e demorar alguns minutos a concluir.
3. Após a desativação, pode eliminar completamente o dispositivo. Eliminar um dispositivo remove-o da lista de dispositivos ligados ao serviço. O serviço, em seguida, pode já não está a gerir o dispositivo foi eliminado. Utilize os seguintes passos para eliminar o dispositivo:
   
   1. O serviço StorSimple Manager **dispositivos** página, selecione um dispositivo desativado que pretende eliminar.
   2. Na parte inferior da página, clique em **eliminar**.
   3. Será solicitado para confirmação. Clique em **Sim** para continuar.
      
      Pode demorar alguns minutos para que o dispositivo ser eliminado.

## <a name="deactivate-and-retain-data"></a>Desativar e manter os dados
Se tiver está interessadas nas eliminar o dispositivo, mas pretende manter os dados, em seguida, conclua os seguintes passos.

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e manter os dados
1. Desative o dispositivo. Irão permanecer todos os contentores de volume e dos instantâneos do dispositivo.
   
   1. O serviço StorSimple Manager **dispositivos** página, selecione o dispositivo que pretende desativar e, na parte inferior da página, clique em **desativar**.
   2. Será apresentada uma mensagem de confirmação. Clique em **Sim** para continuar. O processo de desativar irá iniciar e demorar alguns minutos a concluir.
2. Agora pode efetuar a ativação pós-falha os contentores de volume e instantâneos associados. Para obter os procedimentos, vá para [ativação pós-falha e recuperação após desastre para o dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).
3. Após a desativação e a ativação pós-falha, pode eliminar completamente o dispositivo. Eliminar um dispositivo remove-o da lista de dispositivos ligados ao serviço. O serviço, em seguida, pode já não está a gerir o dispositivo foi eliminado. Conclua os seguintes passos para eliminar o dispositivo:
   
   1. O serviço StorSimple Manager **dispositivos** página, selecione um dispositivo desativado que pretende eliminar.
   2. Na parte inferior da página, clique em **eliminar**.
   3. Será solicitado para confirmação. Clique em **Sim** para continuar.
      
      Pode demorar alguns minutos para que o dispositivo ser eliminado.

## <a name="deactivate-and-delete-a-virtual-device"></a>Desativar e eliminar um dispositivo virtual
Para um dispositivo virtual StorSimple, à desativação deallocates a máquina virtual. Em seguida, pode eliminar a máquina virtual e os recursos criados no aprovisionamento. Depois de o dispositivo virtual ter sido desativado, não é possível restaurar para o estado anterior. 

Resultados de Desativação nas seguintes ações:

* O dispositivo virtual StorSimple é removido.
* O OSDisk e os discos de dados criada para o dispositivo virtual StorSimple são removidos.
* O serviço alojado e a rede Virtual que foram criados durante o aprovisionamento são retidos. Se não estiver a utilizar estas entidades, deverá eliminá-los manualmente.
* Instantâneos de nuvem criados pelo dispositivo virtual StorSimple são retidos.

## <a name="next-steps"></a>Passos seguintes
* Para restaurar o dispositivo desativado para as predefinições de fábrica, aceda a [repor o dispositivo para as predefinições de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Para obter assistência técnica, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md).
* Para obter mais informações sobre como utilizar o serviço StorSimple Manager, aceda a [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md). 


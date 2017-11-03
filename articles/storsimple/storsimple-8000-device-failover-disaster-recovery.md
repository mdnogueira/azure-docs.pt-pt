---
title: "Ativação pós-falha de StorSimple, recuperação após desastre para os dispositivos de 8000 série | Microsoft Docs"
description: "Saiba como efetuar a ativação pós-falha do dispositivo StorSimple para si próprio, outro dispositivo físico ou uma aplicação de nuvem."
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 5dc4a98bf889d38c62c76364289c2d58c14d771e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Ativação pós-falha e recuperação após desastre para o seu dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve a funcionalidade de ativação pós-falha do dispositivo para os dispositivos de série 8000 do StorSimple e como esta funcionalidade pode ser utilizada para recuperar dispositivos StorSimple, se ocorrer um desastre. StorSimple utiliza ativação pós-falha do dispositivo para migrar os dados a partir de um dispositivo de origem no Centro de dados para outro dispositivo de destino. As orientações neste artigo aplica-se a dispositivos físicos de série 8000 do StorSimple e aplicações em nuvem com versões do software Update 3 e posterior.

StorSimple utiliza o **dispositivos** painel para iniciar a funcionalidade de ativação pós-falha do dispositivo em caso de desastre. Este painel apresenta uma lista de todos os dispositivos StorSimple ligados ao seu serviço do Gestor de dispositivos do StorSimple.

![Painel de dispositivos](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperação após desastre (DR) e ativação pós-falha do dispositivo

Num cenário de recuperação (DR) após desastre, o dispositivo primário deixa de funcionar. StorSimple utiliza o dispositivo primário como _origem_ e move os dados na nuvem associadas a outro _destino_ dispositivo. Este processo é denominado o *ativação pós-falha*. O gráfico seguinte ilustra o processo de ativação pós-falha.

![O que acontece na ativação pós-falha do dispositivo?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

O dispositivo de destino para uma ativação pós-falha pode ser um dispositivo físico ou mesmo uma aplicação de nuvem. O dispositivo de destino pode estar localizado no mesmo ou numa localização geográfica do dispositivo de origem diferente.

Durante a ativação pós-falha, pode selecionar contentores de volume para migração. StorSimple, em seguida, altera a propriedade destes contentores de volume do dispositivo de origem para o dispositivo de destino. Depois dos contentores de volume alterar a propriedade, StorSimple elimina esses contentores do dispositivo de origem. Após a eliminação completa, pode efetuar novamente o dispositivo de destino. _Reativação pós-falha_ transfere a propriedade para o dispositivo de origem original.

### <a name="cloud-snapshot-used-during-device-failover"></a>Instantâneo de nuvem utilizado durante a ativação pós-falha do dispositivo

Os seguintes um DR, a mais recente cópia de segurança de nuvem é utilizada para restaurar os dados para o dispositivo de destino. Para obter mais informações sobre instantâneos de nuvem, consulte [utilizar o serviço do Gestor de dispositivos do StorSimple para efetuar uma cópia de segurança manual](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Numa série 8000 do StorSimple, políticas de cópia de segurança associados a cópias de segurança. Se existirem várias políticas de cópia de segurança para o mesmo volume, StorSimple seleciona a política de cópia de segurança com maior número de volumes. StorSimple, em seguida, utiliza a cópia de segurança mais recente da política de cópia de segurança selecionada para restaurar os dados no dispositivo de destino.

Suponha que existem duas políticas de cópia de segurança, *defaultPol* e *customPol*:

* *defaultPol*: um volume, *vol1*, é executada diariamente, começando as 22:30:00.
* *customPol*: quatro volumes, *vol1*, *vol2*, *vol3*, *vol4*, é executada diariamente, começando 10:00 PM.

Neste caso, StorSimple dá prioridade para consistência com falhas e utiliza *customPol* porque tem mais volumes. A cópia de segurança mais recente desta política é utilizada para restaurar dados. Para obter mais informações sobre como criar e gerir políticas de cópia de segurança, aceda a [utilizar o serviço do Gestor de dispositivos do StorSimple para gerir políticas de cópia de segurança](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Considerações comuns para ativação pós-falha do dispositivo

Antes da ativação pós-falha de um dispositivo, reveja as seguintes informações:

* Antes de começa a uma ativação pós-falha do dispositivo, todos os volumes dentro os contentores de volume tem de ser offline. Uma ativação pós-falha não planeada, StotSimple volumes serão automaticamente fique offline. Mas, se estiver a efetuar uma ativação pós-falha planeada (para testar DR), tem de colocar todos os volumes offline.
* Apenas os contentores de volume que tem um associados instantâneo na nuvem são listadas para DR. Tem de existir pelo menos um contentor de volume com um instantâneo na nuvem associadas para recuperar dados.
* Se existirem instantâneos de nuvem que abrangem através de vários contentores de volume, StorSimple a ativação pós-falha estes contentores de volume como um conjunto. Uma instância de raro, se existirem instantâneos locais que abrangem através de vários contentores de volume, mas instantâneos de nuvem associado não o fizer, StorSimple falhar os instantâneos locais e os dados locais são perdidos depois de DR.
* Os dispositivos de destino disponíveis para DR são dispositivos que têm espaço suficiente para acomodar os contentores de volume selecionado. Todos os dispositivos que não têm espaço suficiente, não estão listadas como dispositivos de destino.
* Após um DR (durante um período limitado), o desempenho de acesso de dados pode ser afetado significativamente, como o dispositivo tem de aceder aos dados da nuvem e armazene-o localmente.

#### <a name="device-failover-across-software-versions"></a>Ativação pós-falha de dispositivo nas versões de software

Um dispositivo do serviço StorSimple Manager numa implementação pode ter vários dispositivos, ambos os física e na nuvem, todas as versões de software diferentes em execução.

Utilize a tabela seguinte para determinar se pode efetuar a ativação pós-falha ou falhar ao executar uma versão de software diferentes e a forma como os tipos de volume comportar-se durante a DR de outro dispositivo.

#### <a name="failover-and-failback-across-software-versions"></a>Ativação pós-falha e a reativação pós-falha entre versões de software

| Efetuar a ativação pós-falha / falhar novamente a partir do | Dispositivo físico | Aplicação da cloud |
| --- | --- | --- |
| Atualizar 3 para atualização 4 |Volumes em camadas falharem mais camadas. <br></br>Volumes localmente afixados a ativação pós-falha como localmente afixado. <br></br> Após uma ativação pós-falha quando tirar um instantâneo no dispositivo, atualização 4 o [controlo baseado em heatmap](storsimple-update4-release-notes.md#whats-new-in-update-4) inicia. |Afixado localmente volumes de ativação pós-falha como em camadas. |
| Atualização 4 para a atualização 3 |Volumes em camadas falharem mais camadas. <br></br>Volumes localmente afixados a ativação pós-falha como localmente afixado. <br></br> As cópias de segurança utilizadas para restaurar reter os metadados de heatmap. <br></br>Controlo baseado em Heatmap não está disponível na atualização 3 seguir uma reativação pós-falha. |Afixado localmente volumes de ativação pós-falha como em camadas. |


## <a name="device-failover-scenarios"></a>Cenários de ativação pós-falha do dispositivo

Se ocorrer um desastre, pode optar por efetuar a ativação pós-falha do dispositivo StorSimple:

* [Para um dispositivo físico](storsimple-8000-device-failover-physical-device.md).
* [A próprio](storsimple-8000-device-failover-same-device.md).
* [Para uma aplicação de nuvem](storsimple-8000-device-failover-cloud-appliance.md).

Os artigos anteriores fornecem passos detalhados para cada um dos cenários de ativação pós-falha acima.


## <a name="failback"></a>Reativação pós-falha

Para Update 3 e versões posteriores, StorSimple também suporta a reativação pós-falha. Reativação pós-falha é apenas a reversão da ativação pós-falha, o destino torna-se a origem e o dispositivo de origem original durante a ativação pós-falha agora torna-se o dispositivo de destino. 

Durante a reativação pós-falha, StorSimple novamente sincroniza os dados de volta para a localização principal, o forem a e/s e a atividade de aplicação e transições de volta para a localização original.

Após uma ativação pós-falha estiver concluída, StorSimple efetua as seguintes ações:

* StorSimple cleans os contentores de volume que estão a ativação pós-falha do dispositivo de origem.
* StorSimple inicia uma tarefa em segundo plano por contentor de volume (a ativação pós-falha) no dispositivo de origem. Se tentar falhar enquanto a tarefa está em curso, receberá uma notificação para o efeito. Aguarde até que a tarefa estiver concluída, para iniciar a reativação pós-falha.
* O tempo necessário para concluir a eliminação de contentores de volume depende de vários fatores, tais como a quantidade de dados, a idade dos dados, o número de cópias de segurança e a largura de banda de rede disponível para a operação.

Se estiver a planear as ativações pós-falha de teste ou testar reativações, recomendamos que teste contentores de volume com menos dados (Gbs). Normalmente, é possível iniciar a reativação pós-falha 24 horas depois de concluída a ativação pós-falha.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Q. **O que acontece se a DR falha ou tiver sucesso parcial?**

A. Se falhar a DR, recomendamos que tentar novamente. A segunda tarefa de ativação pós-falha de dispositivo está ciente do progresso da tarefa de primeiro e começa a partir desse ponto.

Q. **Posso eliminar um dispositivo enquanto a ativação pós-falha de dispositivo está em curso?**

A. Não é possível eliminar um dispositivo enquanto um DR está em curso. Só é possível eliminar o dispositivo após a conclusão do DR. Pode monitorizar o progresso de tarefa de ativação pós-falha de dispositivo no **tarefas** painel.

Q. **Quando a recolha de lixo iniciar o dispositivo de origem para que os dados locais no dispositivo de origem são eliminados?**

A. Recolha de lixo está ativada no dispositivo de origem apenas depois do dispositivo é completamente limpos. A limpeza inclui a limpeza de objetos que falharam ao longo do dispositivo de origem, tais como volumes, objetos de cópia de segurança (não dados), os contentores de volume e políticas.

Q. **O que acontece se falhar a tarefa de eliminação associada os contentores de volume no dispositivo de origem?**

A.  Se a tarefa de eliminação falhar, em seguida, pode eliminar manualmente os contentores de volume. No **dispositivos** painel, selecione o dispositivo de origem e clique em **contentores de Volume**. Selecione os contentores de volume que falharam a ativação pós-falha e na parte inferior do painel, clique em **eliminar**. Depois de ter eliminado todas as falhas através de contentores de volume no dispositivo de origem, pode iniciar a reativação pós-falha. Para obter mais informações, aceda a [eliminar um contentor de volume](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastre de continuidade de negócio (BCDR)

Um cenário de recuperação (BCDR) de desastre de continuidade do negócio ocorre quando o todo o datacenter do Azure deixa de funcionar. Este cenário pode afetar o serviço StorSimple Manager de dispositivo e os dispositivos associados do StorSimple.

Se um dispositivo StorSimple foi registado imediatamente antes de um desastre ocorreu, em seguida, este dispositivo poderá ter de sofrer uma reposição de fábrica. Após desastre, o dispositivo StorSimple aparece no portal do Azure como offline. Este dispositivo tem de ser eliminado a partir do portal. Repor o dispositivo para as predefinições de fábrica e registá-lo com o serviço.

## <a name="next-steps"></a>Passos seguintes

Se estiver pronto para executar uma ativação pós-falha do dispositivo, escolha um dos seguintes cenários para obter instruções detalhadas:

- [Ativação pós-falha para outro dispositivo físico](storsimple-8000-device-failover-physical-device.md)
- [Ativação pós-falha para o mesmo dispositivo](storsimple-8000-device-failover-same-device.md)
- [Ativação pós-falha para o dispositivo StorSimple nuvem](storsimple-8000-device-failover-cloud-appliance.md)

Se tiverem falhado ao longo do seu dispositivo, selecione uma das seguintes opções:

* [Desative ou elimine o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


---
title: "StorSimple ativação pós-falha e recuperação após desastre | Microsoft Docs"
description: "Saiba como efetuar a ativação pós-falha do dispositivo StorSimple para si próprio, outro dispositivo físico ou um dispositivo virtual."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5751598e-49c8-42b3-8121-fea5857a7d83
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: alkohli
ms.openlocfilehash: bf92ffdb16b86c4033cc96ae2abb060d90f9505e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-device"></a>Ativação pós-falha e recuperação após desastre para o dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial descreve os passos necessários para efetuar a ativação pós-falha de um dispositivo StorSimple no caso de desastre. Uma ativação pós-falha permitirá migrar os dados a partir de um dispositivo de origem no Centro de dados para outra físico ou mesmo um dispositivo virtual localizado no mesmo ou outra localização geográfica. 

Recuperação após desastre (DR) é orquestrada através da funcionalidade de ativação pós-falha do dispositivo e é iniciada a partir de **dispositivos** página. Esta página tabulates todos os dispositivos StorSimple ligados ao serviço StorSimple Manager. Para cada dispositivo, o nome amigável, o estado, aprovisionada e máxima de capacidade, tipo e modelo são apresentados.

![Página de dispositivos](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

As orientações neste tutorial aplica-se a dispositivos físicos e virtuais do StorSimple em todas as versões de software.

## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperação após desastre (DR) e ativação pós-falha do dispositivo
Num cenário de recuperação (DR) após desastre, o dispositivo primário deixa de funcionar. Nesta situação, pode mover os dados na nuvem associados ao dispositivo falha noutro dispositivo utilizando o dispositivo primário como a *origem* e especificando outro dispositivo, como o *destino*. Pode selecionar um ou mais contentores de volume para migrar para o dispositivo de destino. Este processo é denominado o *ativação pós-falha*. 

Durante a ativação pós-falha, os contentores de volume do dispositivo de origem alterar a propriedade e são transferidos para o dispositivo de destino. Depois dos contentores de volume alterar a propriedade, estes são eliminados do dispositivo de origem. Após a eliminação completa, o dispositivo de destino, em seguida, pode voltar falhou.

Normalmente, seguindo uma DR, a cópia de segurança mais recente é utilizada para restaurar os dados para o dispositivo de destino. No entanto, se existirem várias políticas de cópia de segurança para o mesmo volume, em seguida, a política de cópia de segurança com maior número de volumes obtém selecionada e a cópia de segurança mais recente da política de que é utilizada para restaurar os dados no dispositivo de destino.

Por exemplo, se existirem duas políticas de cópia de segurança (um predefinido e um personalizada) *defaultPol*, *customPol* com os seguintes detalhes:

* *defaultPol* : um volume, *vol1*, é executada diariamente, começando as 22:30:00.
* *customPol* : quatro volumes, *vol1*, *vol2*, *vol3*, *vol4*, é executada diariamente, começando 10:00 PM.

Neste caso, *customPol* será utilizado como tem mais volumes e iremos definir prioridades para consistência com falhas. A cópia de segurança mais recente desta política é utilizada para restaurar dados.

## <a name="considerations-for-device-failover"></a>Considerações sobre a ativação pós-falha do dispositivo
Em caso de desastre, pode optar por efetuar a ativação pós-falha do dispositivo StorSimple:

* Para um dispositivo físico 
* A próprio
* Para um dispositivo virtual

Para qualquer dispositivo de ativação pós-falha, tenha em atenção o seguinte:

* Os pré-requisitos para a DR são que todos os volumes dentro os contentores de volume estão offline e os contentores de volume possui uma associado instantâneo na nuvem. 
* Os dispositivos de destino disponíveis para DR são dispositivos que têm espaço suficiente para acomodar os contentores de volume selecionado. 
* Os dispositivos que estão ligados ao seu serviço, mas que não cumprem os critérios de espaço suficiente não estarão disponíveis como dispositivos de destino.
* Os seguintes um DR, durante um período de tempo limitado, o desempenho de acesso de dados pode ser afetado significativamente, como o dispositivo terá de aceder aos dados da nuvem e armazená-la localmente.

#### <a name="device-failover-across-software-versions"></a>Ativação pós-falha de dispositivo nas versões de software
Um serviço StorSimple Manager numa implementação pode ter vários dispositivos físicos e virtuais, todas as versões de software diferentes em execução. Consoante a versão do software, os tipos de volume nos dispositivos também podem ser diferentes. Por exemplo, um dispositivo com o Update 2 ou posterior tem localmente afixado e volumes em camadas (com a ser um subconjunto de arquivo em camadas). Um dispositivo de pré-atualização 2 por outro lado poderá ter em camadas e volumes de arquivo. 

Utilize a tabela seguinte para determinar se que pode efetuar a ativação pós-falha noutro dispositivo que executa uma versão de software diferentes e o comportamento dos tipos de volume durante a DR.

| Ativação pós-falha do | Permitido para o dispositivo físico | Permitido para o dispositivo virtual |
| --- | --- | --- |
| Atualização 2 para a pré-atualização 1 (versão, 0.1, 0,2, 0,3) |Não |Não |
| Atualização 2 para a atualização 1 (1, 1.1, 1.2) |Sim <br></br>Se utilizar localmente afixado ou camadas volumes ou uma combinação dos dois, os volumes são sempre a ativação pós-falha como em camadas. |Sim<br></br>Se utilizar localmente afixado volumes, estes são falha mais camadas. |
| Atualização 2 para a atualização 2 (versão posterior) |Sim<br></br>Se utilizar volumes localmente afixados ou em camadas ou de uma combinação dos dois, os volumes são sempre a ativação pós-falha como o tipo de volume partida; em camadas como em camadas e afixado localmente afixado como localmente. |Sim<br></br>Se utilizar localmente afixado volumes, estes são falha mais camadas. |

#### <a name="partial-failover-across-software-versions"></a>Ativação pós-falha parcial nas versões de software
Se pretender efetuar uma ativação pós-falha parcial de utilizar um dispositivo de origem do StorSimple a executar o anterior à atualização 1 para um destino executar a atualização 1 ou posterior, siga essa orientação. 

| A ativação pós-falha parcial do | Permitido para o dispositivo físico | Permitido para o dispositivo virtual |
| --- | --- | --- |
| Pré-atualização 1 (versão, 0.1, 0,2, 0,3) para atualização 1 ou posterior |Sim, veja a seguir a melhor sugestão de prática. |Sim, veja a seguir a melhor sugestão de prática. |

> [!TIP]
> Ocorreu uma alteração de formato de nuvem metadados e dados, na atualização 1 e versões posteriores. Por conseguinte, não se recomenda uma ativação pós-falha parcial da pré-atualização 1 Update 1 ou versões posteriores. Se precisar de efetuar uma ativação pós-falha parcial, recomendamos que primeiro aplicar Update 1 ou posterior na ambos os dispositivos (origem e destino) e, em seguida, prossiga com a ativação pós-falha. 
> 
> 

## <a name="fail-over-to-another-physical-device"></a>Ativação pós-falha para outro dispositivo físico
Execute os seguintes passos para restaurar o seu dispositivo para um dispositivo físico de destino.

1. Certifique-se de que o contentor de volume que pretende efetuar a ativação pós-falha associou instantâneos de nuvem.
2. No **dispositivos** página, clique em de **contentores de Volume** separador.
3. Selecione um contentor de volume que pretende efetuar a ativação pós-falha para outro dispositivo. Clique no contentor de volume para apresentar a lista de volumes dentro deste contentor. Selecione um volume e clique em **Colocar Offline** para colocar offline o volume. Repita este processo para todos os volumes no contentor de volume.
4. Repita o passo anterior para todos os contentores de volume que pretende efetuar a ativação pós-falha para outro dispositivo.
5. No **dispositivos** página, clique em **ativação pós-falha**.
6. No Assistente de que se abre, em **escolha o contentor de volume para a ativação pós-falha**:
   
   1. Na lista de contentores de volume, selecione os contentores de volume que pretende efetuar a ativação pós-falha.
      **Apenas os contentores de volume com instantâneos de nuvem associados e offline volumes são apresentados.**
   2. Em **escolha um dispositivo de destino** para os volumes nos contentores selecionados, selecione um dispositivo de destino na lista pendente de dispositivos disponíveis. Apenas os dispositivos que tenham capacidade disponível são apresentados na lista pendente.
   3. Por fim, consultar todas as definições de ativação pós-falha em **confirmar ativação pós-falha**. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
7. É criada uma tarefa de ativação pós-falha que podem ser monitorizados através do **tarefas** página. Se o contentor de volume, ativação pós-falha tem volumes locais, em seguida, verá as tarefas de restauro individuais para cada volume local (e não para volumes em camadas) no contentor. Estes restauro tarefas poderão demorar bastante algum tempo a concluir. É provável que a tarefa de ativação pós-falha pode ser anterior. Tenha em atenção que estes volumes terão garantias locais apenas depois das tarefas de restauro estejam concluídas. Uma vez concluída a ativação pós-falha, vá para o **dispositivos** página.                                            
   
   1. Selecione o dispositivo que foi utilizado como o dispositivo de destino para o processo de ativação pós-falha.
   2. Vá para o **contentores de Volume** página. Todos os contentores volume, juntamente com os volumes do dispositivo antigo, deverá estar listados.

## <a name="failover-using-a-single-device"></a>Ativação pós-falha utilizando um único dispositivo
Se apenas um único dispositivo e tem de efetuar uma ativação pós-falha, execute os seguintes passos.

1. Criar instantâneos de nuvem de todos os volumes no seu dispositivo.
2. Repor o seu dispositivo para as predefinições de fábrica. Siga as instruções detalhadas no [como repor um dispositivo StorSimple para as predefinições de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Configurar o dispositivo e registá-lo novamente no serviço StorSimple Manager.
4. No **dispositivos** página, o dispositivo antigo deve mostrar como **Offline**. O dispositivo recém-registado deve mostrar como **Online**.
5. Para o novo dispositivo, conclua a configuração mínima do dispositivo pela primeira vez. 
   
   > [!IMPORTANT]
   > **Se a configuração mínima não seja concluída primeiro, a DR falhará em resultado de erros na implementação atual. Este comportamento será corrigido numa versão posterior.**
   > 
   > 
6. Selecione o dispositivo antigo (estado offline) e clique em **ativação pós-falha**. No Assistente de que é apresentado, efetuar a ativação pós-falha este dispositivo e especificar o dispositivo de destino como o dispositivo recentemente registado. Para obter instruções detalhadas, consulte [ativação pós-falha para outro dispositivo físico](#fail-over-to-another-physical-device).
7. Será criada uma tarefa de restauro de dispositivo que pode monitorizar a partir do **tarefas** página.
8. Depois da tarefa foi concluída com êxito, o novo dispositivo de acesso e navegue para o **contentores de Volume** página. Todos os contentores de volume do dispositivo antigo agora devem ser migrados para o novo dispositivo.

## <a name="fail-over-to-a-storsimple-virtual-device"></a>Ativação pós-falha para um dispositivo virtual StorSimple
Tem de ter um StorSimple dispositivo virtual criada e configurada antes de executar este procedimento. Se executar a atualização 2, considere utilizar um dispositivo virtual 8020 para DR que tenha 64 TB e utiliza o armazenamento Premium. 

Execute os seguintes passos para restaurar o dispositivo para um destino do dispositivo virtual StorSimple.

1. Certifique-se de que o contentor de volume que pretende efetuar a ativação pós-falha associou instantâneos de nuvem.
2. No **dispositivos** página, clique em de **contentores de Volume** separador.
3. Selecione um contentor de volume que pretende efetuar a ativação pós-falha para outro dispositivo. Clique no contentor de volume para apresentar a lista de volumes dentro deste contentor. Selecione um volume e clique em **Colocar Offline** para colocar offline o volume. Repita este processo para todos os volumes no contentor de volume.
4. Repita o passo anterior para todos os contentores de volume que pretende efetuar a ativação pós-falha para outro dispositivo.
5. No **dispositivos** página, clique em **ativação pós-falha**.
6. No Assistente de que se abre, em **escolha o contentor de volume para ativação pós-falha**, concluir os seguintes procedimentos:
   
    a. Na lista de contentores de volume, selecione os contentores de volume que pretende efetuar a ativação pós-falha.
   
    **Apenas os contentores de volume com instantâneos de nuvem associados e offline volumes são apresentados.**
   
    b. Em **escolha um dispositivo de destino para os volumes nos contentores selecionados**, selecione o dispositivo virtual StorSimple da lista pendente de dispositivos disponíveis. **Apenas os dispositivos que tem capacidade suficiente são apresentados na lista pendente.**  
7. Por fim, consultar todas as definições de ativação pós-falha em **confirmar ativação pós-falha**. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
8. Uma vez concluída a ativação pós-falha, vá para o **dispositivos** página.
   
    a. Selecione o dispositivo virtual StorSimple que foi utilizado como o dispositivo de destino para o processo de ativação pós-falha.
   
    b. Vá para o **contentores de Volume** página. Agora deverá estar listados todos os contentores volume, juntamente com os volumes do dispositivo antigo.

![Vídeo disponível](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como pode restaurar uma falha ao longo do dispositivo físico para um dispositivo virtual na nuvem, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## <a name="failback"></a>Reativação pós-falha
Para Update 3 e versões posteriores, StorSimple também suporta a reativação pós-falha. Depois de concluída a ativação pós-falha, as ações seguintes ocorrem:

* Os contentores de volume que estão a ativação pós-falha são limpos do dispositivo de origem.
* É iniciada uma tarefa em segundo plano por contentor de volume (a ativação pós-falha) no dispositivo de origem. Se tentar a reativação pós-falha enquanto a tarefa está em curso, receberá uma notificação para o efeito. Terá de aguardar até que a tarefa estiver concluída, para iniciar a reativação pós-falha. 
  
    O tempo para concluir a eliminação de contentores de volume é depende de vários fatores, tais como a quantidade de dados, a idade dos dados, o número de cópias de segurança e a largura de banda de rede disponível para a operação. Se estiver a planear as ativações pós-falha de teste/reativações, recomendamos que teste contentores de volume com menos dados (Gbs). Na maioria dos casos, pode iniciar a reativação pós-falha 24 horas depois de concluída a ativação pós-falha. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
Q. **O que acontece se a DR falha ou tiver sucesso parcial?**

A. Se falhar a DR, recomendamos que tentar novamente. Na segunda vez aproximadamente, DR sabe o que tudo foi feito e quando o processo parado pela primeira vez. O processo de DR é iniciado a partir desse ponto em diante. 

Q. **Posso eliminar um dispositivo enquanto a ativação pós-falha de dispositivo está em curso?**

A. Não é possível eliminar um dispositivo enquanto um DR está em curso. Só é possível eliminar o dispositivo após a conclusão do DR.

Q.    **Quando a recolha de lixo iniciar o dispositivo de origem para que os dados locais no dispositivo de origem são eliminados?**

A. Recolha de lixo será ativada no dispositivo de origem apenas depois do dispositivo é completamente limpa. A limpeza inclui a limpeza de objetos que falharam ao longo do dispositivo de origem, tais como volumes, objetos de cópia de segurança (não dados), os contentores de volume e políticas.

Q. **O que acontece se falhar a tarefa de eliminação associada os contentores de volume no dispositivo de origem?**

A.  Se a tarefa de eliminação falhar, terá de ativar manualmente a eliminação dos contentores de volume. No **dispositivos** página, selecione o seu dispositivo de origem e clique em **contentores de Volume**. Selecione os contentores de volume que falharam a ativação pós-falha e na parte inferior da página, clique em **eliminar**. Depois de ter eliminado todas as falhas através de contentores de volume no dispositivo de origem, pode iniciar a reativação pós-falha.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastre de continuidade de negócio (BCDR)
Um cenário de recuperação (BCDR) de desastre de continuidade do negócio ocorre quando o todo o datacenter do Azure deixa de funcionar. Isto pode afetar o serviço StorSimple Manager e os dispositivos associados do StorSimple.

Se existirem dispositivos StorSimple que foram registados antes de um desastre ocorreu, em seguida, estes dispositivos StorSimple poderão ter de sofrer uma reposição de fábrica. Após desastre, o dispositivo StorSimple irá ser mostrado como offline. O dispositivo StorSimple tem de ser eliminado do portal e, uma reposição de fábrica deve ser efetuada, seguido de um novo registo.

## <a name="next-steps"></a>Passos seguintes
* Depois de ter de efetuar uma ativação pós-falha, poderá ser necessário [desative ou elimine o dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).
* Para informações sobre como utilizar o serviço StorSimple Manager, aceda a [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).


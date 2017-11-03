---
title: StorSimple localmente afixado volumes FAQ | Microsoft Docs
description: Fornece respostas a perguntas mais frequentes sobre volumes do StorSimple afixado localmente.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 857647e89b7ac5d9bc72bc4b2ace0ea7279353f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple localmente afixado volumes: Perguntas mais frequentes sobre (FAQ)
## <a name="overview"></a>Descrição geral
Seguem-se perguntas e respostas que possa ter quando criar um volume StorSimple afixado localmente, converter um volume em camadas para um volume localmente afixado (e vice-versa), ou cópia de segurança e restaurar um volume afixado localmente.

Perguntas e respostas são dispostas nas seguintes categorias

* Criar um volume localmente afixado
* Cópia de segurança de um afixado localmente
* A conversão de um volume em camadas para um volume localmente afixado
* Restaurar um volume localmente afixado
* Falha ao longo de um volume localmente afixado

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Perguntas sobre como criar um volume localmente afixado
**P.** O que é o tamanho máximo de um volume localmente afixado que pode criar nos dispositivos 8000 série?

**A** em dispositivos com StorSimple 8000 série atualização 3.0, pode aprovisionar volumes localmente afixados até 8.5 TB ou volumes em camadas até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB.

**P.** Posso atualizado recentemente o meu dispositivo 8100 para atualização 3.0 e ao tentar criar um volume localmente afixado, o tamanho máximo disponível é apenas 6 TB e não 8.5 TB. Por que motivo não é possível a criar um volume 8.5 TB?

**A** se o dispositivo estiver em execução de atualização 3.0, pode aprovisionar volumes localmente afixados até 8.5 TB ou volumes em camadas até 200 TB no dispositivo 8100. Se o dispositivo já tem volumes em camadas, o espaço disponível para a criação de um volume localmente afixado será proporcionalmente inferior a este limite máximo. Por exemplo, se já tiverem sido aprovisionada aproximadamente 106 TB de volumes em camadas no dispositivo 8100 (que é metade da capacidade em camadas), em seguida, o tamanho máximo de um volume local que pode criar no dispositivo 8100 será proporcionalmente reduzido para 4 TB (aproximadamente metade máximo localmente afixado capacidade do volume).

Como algum espaço local no dispositivo é utilizado para alojar o conjunto de trabalho de volumes em camadas, o espaço disponível para a criação de um volume localmente afixado é reduzido se o dispositivo tem volumes em camadas. Por outro lado, a criação de um volume localmente afixado proporcionalmente reduz o espaço disponível para volumes em camadas. As tabelas seguintes resume a capacidade disponível em camadas nos dispositivos 8100 e 8600 quando são criados volumes localmente afixados.

#### <a name="update-30"></a>Atualizar 3.0 
| Capacidade de aprovisionamento de volumes localmente afixados | Capacidade disponível para ser aprovisionada para volumes em camadas - 8100 | Capacidade disponível para ser aprovisionada para volumes em camadas - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |ND |277.8 TB |
| 15 TB |ND |166.7 TB |
| 22.5 TB |ND |0 TB |

**P.** Por que motivo é criação de volumes localmente afixados uma operação demorada que?

**R.** Volumes localmente afixados são fortemente aprovisionados. Para criar espaço nas camadas locais do dispositivo, alguns dados a partir de volumes em camadas existentes poderão ser enviadas por push para a nuvem durante o processo de aprovisionamento. E, uma vez que isto depende do tamanho do volume que está a ser aprovisionado, os dados existentes no seu dispositivo e a largura de banda disponível na nuvem, o tempo necessário para criar um volume local poderá ser várias horas.

**P.** Quanto tempo demora para criar um volume localmente afixado?

**R.** Porque volumes localmente afixados são fortemente aprovisionados, alguns dados existentes a partir de volumes em camadas podem ser enviados para a nuvem durante o processo de aprovisionamento. Por conseguinte, o tempo necessário para criar um volume localmente afixado depende múltiplos fatores, incluindo o tamanho do volume, os dados no seu dispositivo e a largura de banda disponível. Num dispositivo instalado de raiz que não tenha nenhum volumes, o tempo para criar um volume localmente afixado é cerca de 10 minutos por com vários terabytes de dados. No entanto, a criação de volumes locais poderá demorar várias horas com base nos fatores explicados anteriormente num dispositivo que está a ser utilizado.

**P.** Pretende criar um volume afixado localmente. Existem as melhores práticas que necessário ter em consideração?

**R.** Volumes localmente afixados são adequados para cargas de trabalho que necessitam de garantias locais dos dados de todas as vezes são confidenciais para as latências da nuvem. Ao considerar a utilização de volumes locais para qualquer uma das suas cargas de trabalho,. tenha em atenção o seguinte:

* Volumes localmente afixados são fortemente aprovisionados e criar volumes locais tem impacto sobre o espaço disponível para volumes em camadas. Por conseguinte, sugerimos que comece com volumes de tamanho mais pequeno e dimensione à medida que aumenta de requisitos do armazenamento.
* O aprovisionamento de volumes locais é uma operação de execução longa que poderão envolver a enviar dados existentes de volumes em camadas para a nuvem. Como resultado, pode deparar-se um desempenho nestes volumes.
* Aprovisionamento de volumes locais é uma operação demorada. A hora real envolvidos depende de vários fatores: o tamanho do volume que está a ser aprovisionados, dados no seu dispositivo e a largura de banda disponível. Se não tiver feita os volumes existentes para a nuvem, criação de volumes é mais lenta. Sugerimos que criar instantâneos de nuvem dos seus volumes existentes antes de aprovisionar um volume local.
* Pode converter os volumes em camadas existentes volumes localmente afixados e esta conversão envolve o aprovisionamento de espaço no dispositivo para o volume localmente afixado resultante (para além de desativar a dados em camadas, se existir, da nuvem). Novamente, esta é uma operação de execução longa que depende de fatores que discutimos acima. Sugerimos que copie em segurança os volumes existentes antes da conversão como o processo será ainda mais lento se os volumes existentes não são guardados em cópia de segurança. O dispositivo também pode ocorrer reduzido desempenho durante este processo.

Obter mais informações sobre como [criar um volume localmente afixado](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**P.** Pode criar vários volumes localmente afixados ao mesmo tempo?

**R.** Sim, mas as tarefas de criação e a expansão de volume localmente afixado são processados sequencialmente.

Volumes localmente afixados são fortemente aprovisionados e Isto exige que a criação de espaço local no dispositivo (o que poderá resultar numa dados existentes a partir de volumes em camadas para ser enviado para a nuvem durante o processo de aprovisionamento). Por conseguinte, se uma tarefa de aprovisionamento está em curso, outras tarefas de criação do local volume irão ser colocado em fila até essa tarefa estiver concluída.

Da mesma forma, se um volume local existente está a ser expandido ou um volume em camadas está a ser convertido para um volume localmente afixado, em seguida, a criação de um novo volume localmente afixado é colocada na fila até que a tarefa anterior é concluída. Expandir o tamanho de um volume localmente afixado envolve a expansão do espaço local existente para esse volume. A conversão de um em camadas para afixado localmente volume também envolve a criação de espaço local para resultante localmente afixado volume. Em ambas estas operações, a criação ou a expansão de espaço local é uma longa executar tarefa.

Pode ver estas tarefas no **tarefas** painel do serviço Gestor de dispositivos do StorSimple. A tarefa que está a ser processada ativamente continuamente é atualizada para refletir o progresso do aprovisionamento de espaço. As tarefas restantes do volume localmente afixado estão marcadas como em execução, mas os respetivos progresso está parado e estes serão escolhidos pela ordem que foram colocados em fila.

**P.** Posso eliminar um volume afixado localmente. Por que motivo não vejo o espaço reclaimed refletido no espaço disponível ao tentar criar um novo volume?

**R.** Se eliminar um volume localmente afixado, o espaço disponível para novos volumes não pode ser atualizado imediatamente. O serviço do Gestor de dispositivos do StorSimple atualiza o espaço local disponível aproximadamente a cada hora. Sugerimos que aguarde uma hora antes de tentar criar o novo volume.

**P.** São suportados volumes localmente afixados no dispositivo de nuvem?

**R.** Volumes localmente afixados não são suportadas no dispositivo de nuvem (8010 e 8020 dispositivos referidos anteriormente como o dispositivo virtual StorSimple).

**P.** Pode utilizar os cmdlets do PowerShell do Azure para criar e gerir volumes localmente afixados?

**R.** Não, não é possível criar volumes localmente afixados através de cmdlets do Azure PowerShell (qualquer volume que criar através do PowerShell do Azure está em camadas). Podemos também sugerir que não utilize os cmdlets do PowerShell do Azure para modificar quaisquer propriedades de um volume localmente afixado, como o efeito indesejado de modificar o tipo de volume pode ter a em camadas.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Perguntas sobre a cópia de segurança de um volume localmente afixado
**P.** São instantâneos locais dos volumes localmente afixados suportados?

**R.** Sim, pode tirar instantâneos locais dos seus volumes localmente afixados. No entanto, sugerimos vivamente que regularmente uma cópia de segurança sua volumes localmente afixados com instantâneos de nuvem para garantir que os dados estão protegidos em eventuality de um desastre.

Tenha em atenção que os instantâneos locais dos volumes localmente afixados podem camada também para a nuvem e não são garantidos que para se manter no escalão local do dispositivo.

**P.** Existem as diretrizes para gerir instantâneos locais para volumes afixados localmente?

**R.** Instantâneos locais frequentes juntamente com uma taxa elevada de fluxo de dados no volume localmente afixado podem causar espaço local no dispositivo para ser consumido rapidamente e resultar em dados a partir de volumes em camadas a ser enviadas para a nuvem. Sugerimos, por conseguinte, que estará a minimizar o número de instantâneos locais.

**P.** Recebi um alerta a indicar que poderão ser invalidou o meu instantâneos locais dos volumes localmente afixados. Quando pode isto acontecer?

**R.** Instantâneos locais frequentes juntamente com uma taxa elevada de fluxo de dados no volume localmente afixado podem causar espaço local no dispositivo a ser consumidos rapidamente. Se as camadas locais do dispositivo descontos elevados forem utilizadas, uma falha de expandida nuvem poderá resultar no dispositivo cheios e entradas escritas no volume poderão resultar em invalidação instantâneos (uma vez que não existe espaço existe para atualizar os instantâneos de consultar os blocos de dados que foram substituídos anteriores). Uma situação deste tipo de escritas no volume irão continuar a ser apresentada, mas os instantâneos locais poderão ser inválidos. Não há nenhum impacto sobre a sua instantâneos de nuvem existente.

É o aviso de alerta para notificá-lo que uma situação deste tipo pode surgir e certifique-se de que mesmo endereço de uma forma atempada por rever as agendas de instantâneos locais para criar instantâneos locais menos frequentes ou eliminar instantâneos locais mais antigos que já não são necessários.

Se os instantâneos locais são também invalidados, receberá um alerta de informação notificam os instantâneos locais para a política de cópia de segurança específico foram invalidados juntamente com a lista de carimbos instantâneos locais que foram invalidados. Estes instantâneos serão eliminados automaticamente e já não poderá visualizá-las no **catálogos de cópia de segurança** painel no portal do Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Perguntas sobre a conversão de um volume em camadas para um volume localmente afixado
**P.** Posso estou observar algumas slowness no dispositivo durante a conversão de um volume em camadas para um volume afixado localmente. Por que motivo é tal acontecer?

**R.** O processo de conversão envolve dois passos:

1. Aprovisionamento de espaço no dispositivo para o em breve-para--convertido localmente afixado volume.
2. Garante que transferir quaisquer dados em camadas da nuvem para garantir local.

Estes passos são muito a executar as operações que dependem do tamanho do volume que está a ser convertido, os dados no dispositivo e a largura de banda disponível. Como alguns dados a partir de volumes em camadas existentes poderão transbordam para a nuvem, como parte do processo de aprovisionamento, o seu dispositivo pode ocorrer reduzido desempenho durante este período. Além disso, o processo de conversão pode ser mais lento se:

* Os volumes existentes não foram efetuadas cópias de para a nuvem; para que sugerimos que os volumes antes de iniciar uma conversão de cópia de segurança.
* Foram aplicadas políticas de limitação de largura de banda, que pode restringir a largura de banda disponível para a nuvem; Por conseguinte, recomendamos que tiver uma Mbps 40 dedicado ou a ligação mais para a nuvem.
* O processo de conversão poderá demorar várias horas devido as vários fatores explicados acima; Por conseguinte, sugerimos que executar esta operação em situações não picos ou um fim de semana para evitar o impacto nos consumidores de fim.

Obter mais informações sobre como [converter um volume em camadas para um volume localmente afixado](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**P.** Pode cancelar a operação de conversão de volume?

**R.** Não, não é possível a cancelar a operação de conversão foi iniciada uma vez. Tal como explicado à pergunta anterior,. tenha em atenção os potenciais problemas de desempenho que possam ocorrer durante o processo e siga as melhores práticas listadas acima, quando planeia a conversão.

**P.** O que acontece ao meu volume se falhar a operação de conversão?

**R.** Conversão do volume pode falhar devido a problemas de conectividade de nuvem. O dispositivo, eventualmente, poderá interromper o processo de conversão após uma série de tentativas sem para colocar para baixo de dados em camadas a partir da nuvem. Neste cenário, o tipo de volume irá continuar a ser do tipo de volume de origem antes de conversão, e:

* Será desencadeado um alerta crítico para notificá-lo da falha na conversão do volume. Obter mais informações sobre [alertas relacionados com volumes localmente afixados](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Se estiver a converter um em camadas para um volume localmente afixado, o volume continuará apresentem um propriedades de um volume em camadas, como dados ainda poderão residem na nuvem. Sugerimos que resolva os problemas de conectividade e, em seguida, repita a operação de conversão.
* Da mesma forma, quando a conversão de um afixado localmente para um volume em camadas falha, embora o volume será marcado como um volume localmente afixado, funcionará como um volume em camadas (porque foi ter spilled dados para a nuvem). No entanto, continua a ocupam espaço nas camadas locais do dispositivo. Este espaço não estará disponível para outros volumes localmente afixados. Sugerimos que repita esta operação para se certificar de que a conversão do volume está concluída e o espaço local no dispositivo pode ser recuperado.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Perguntas sobre o restauro de um volume localmente afixado
**P.** São volumes localmente afixados restauradas de forma instantânea?

**R.** Sim, os volumes localmente afixados estão restauradas instantaneamente. Assim que as informações de metadados para o volume são retiradas da nuvem como parte da operação de restauro, o volume seja colocado online e pode ser acedido pelo anfitrião. No entanto, garantias locais para o volume de dados não estará presentes até que todos os dados tenha sido transferido a partir da nuvem e pode deparar-se um desempenho reduzido nestes volumes durante o restauro.

**P.** Quanto tempo demora para restaurar um volume localmente afixado?

**R.** Volumes localmente afixados são restaurados instantaneamente e colocados online, assim como as informações de metadados de volume são obtidas a partir da nuvem, enquanto os dados do volume continuam a ser transferido em segundo plano. Esta última parte da operação de restauro – voltar a obtê de garantias locais para os dados do volume – é uma operação demorada que e poderá demorar várias horas para todos os dados para ficarem local novamente. O tempo necessário para concluir a mesma depende de vários fatores, tais como o tamanho do volume a ser restaurado e a largura de banda disponível. Se o volume original que está a ser restaurado foi eliminado, será conduzido tempo adicional para criar o espaço local no dispositivo como parte da operação de restauro.

**P.** Tem de restaurar os meus volume localmente afixado existente para um instantâneo mais antigo (tomado quando o volume foi em camadas). O volume vai ser restaurado conforme camadas neste caso?

**R.** Não, o volume será restaurado como um volume afixado localmente. Embora as datas de instantâneo para a hora quando o volume foi camado, enquanto restaurava volumes existentes, StorSimple utiliza sempre o tipo de volume no disco tal como existe atualmente.

**P.** Posso expandido meu volume localmente afixado recentemente, mas agora necessário restaurar os dados para uma hora quando o volume era inferior de tamanho. Restauro redimensionará o volume atual e será necessário expandir o tamanho do volume depois do restauro está concluído?

**R.** Sim, o restauro será redimensionar o volume e precisa de expandir o tamanho do volume depois do restauro está concluído.

**P.** Pode alterar o tipo de um volume durante o restauro?

**Recomeço**não, não é possível alterar o tipo de volume durante o restauro.

* Volumes que tenham sido eliminadas são restaurados durante o tipo armazenado no instantâneo.
* Os volumes existentes são restaurados com base no respetivo tipo atual, independentemente do tipo armazenado no instantâneo (consulte as dois perguntas anteriores).

**P.** Necessário restaurar os meus volume localmente afixado, mas posso selecionado um ponto de incorreto no instantâneo de tempo. Pode cancelar a operação de restauro atual?

**R.** Sim, pode cancelar uma operação de restauro em curso. O estado do volume será revertido para o estado no início do restauro. No entanto, quaisquer gravações que foram efetuadas para o volume enquanto o restauro estava em curso serão perdidas.

**P.** Posso foi iniciada uma operação de restauro de um dos meus volumes localmente afixados e agora vejo um instantâneo no meu catálogo de registo de segurança que posso não recollect criar. Esta finalidade do?

**R.** Este é o instantâneo temporário que é criado antes da operação de restauro e é utilizado para reversão no caso do restauro foi cancelado ou falha. Não eliminar este instantâneo; será possível eliminá-lo automaticamente quando o restauro foi concluído. Este comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, este comportamento não ocorrerá.

**P.** Pode clonar um volume localmente afixado?

**R.** Sim, pode. No entanto, o volume localmente afixado será clonado como um volume em camadas por predefinição. Obter mais informações sobre como [clonar um volume localmente afixado](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Perguntas sobre a falha através de um volume localmente afixado
**P.** Tem de efetuar a ativação pós-falha do meu dispositivo a outro dispositivo físico. Meu volumes localmente afixados irão ser falha mais como afixado localmente ou camadas?

**R.** Os volumes localmente afixados são a ativação pós-falha como localmente afixado se o dispositivo de destino está em execução atualização de série 8000 do StorSimple 3 ou superior.

Obter mais informações sobre [ativação pós-falha e DR de afixado localmente volumes entre versões](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**P.** Volumes localmente afixados são restauradas instantaneamente durante a recuperação após desastre (DR)?

**R.** Sim, os volumes localmente afixados estão restauradas instantaneamente durante a ativação pós-falha. Assim que as informações de metadados para o volume são retiradas da nuvem como parte da operação de ativação pós-falha, o volume é colocado online no dispositivo de destino e pode ser acedido pelo anfitrião. Entretanto, os dados do volume vai continuar a transferir em segundo plano e pode deparar-se um desempenho nestes volumes durante a ativação pós-falha.

**P.** Posso ver a tarefa de ativação pós-falha concluída, como posso acompanhar o progresso do volume localmente afixado que está a ser restaurada no dispositivo de destino?

**R.** Durante uma operação de ativação pós-falha, a tarefa de ativação pós-falha está marcada como uma vez concluir todos os volumes no conjunto de ativação pós-falha tenham sido instantaneamente restaurados e colocados online no dispositivo de destino. Isto inclui quaisquer volumes localmente afixados que poderão ter sido efetuadas a ativação pós-falha; No entanto, garantias locais dos dados só estará disponíveis quando todos os dados para o volume tenha sido transferido. Pode monitorizar esta progresso para cada volume localmente afixado que foi ativada com pós-falha pelo monitorização as tarefas de restauro correspondente que são criadas como parte da ativação pós-falha. Estas tarefas de restauro individuais só serão criadas para volumes afixados localmente.

**P.** Pode alterar o tipo de um volume durante a ativação pós-falha?

**R.** Não, não é possível alterar o tipo de volume durante uma ativação pós-falha. Se estão a falhar através de outro dispositivo físico que está a executar 8000 do StorSimple série de atualização 3, os volumes são a ativação pós-falha com base no tipo de volume armazenado no instantâneo.

**P.** Pode falhar ao longo de um contentor de volume com volumes localmente afixados para a aplicação na nuvem?

**R.** Sim, pode. Os volumes localmente afixados irão a ativação pós-falha como volumes em camadas. Obter mais informações sobre [ativação pós-falha e DR de afixado localmente volumes entre versões](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)


---
title: "Configurar o agendamento para clusters do HDInsight baseado em Linux - Azure da aplicação de patches de SO | Microsoft Docs"
description: "Saiba como configurar o agendamento para clusters do HDInsight baseado em Linux a aplicação de patches de SO."
services: hdinsight
documentationcenter: 
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: af3c5a19ae8e2e606e4b0506f9f6dddb41192e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="os-patching-for-hdinsight"></a>Para o HDInsight a aplicação de patches de SO 
Como um serviço gerido do Hadoop, HDInsight encarrega-se de aplicação de patches de SO das VMs subjacentes utilizados pelos clusters do HDInsight. A partir de 1 de Agosto de 2016, vamos foram alterados a política de aplicação de patches de SO convidado para os clusters do HDInsight baseado em Linux (versão 3.4 ou superior). O objetivo da nova política é reduzir significativamente o número de reinícios devido a aplicação de patches. A nova política irá continuar a correção máquinas de virtuais (VMs) em clusters do Linux cada segunda-feira ou quinta começando UTC 12: 00, de forma escalonada em nós em qualquer cluster especificado. No entanto, qualquer VM especificada só será reiniciado no máximo uma vez a cada 30 dias devido a aplicação de patches de SO do convidado. Além disso, o primeiro reinício para um cluster recém-criado não acontece mais cedo do que 30 dias a contar da data de criação do cluster. Correções de erros serão aplicadas depois das VMs são reiniciadas.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Como configurar o agendamento para clusters do HDInsight baseado em Linux a aplicação de patches de SO
As máquinas virtuais no cluster do HDInsight tem de ser reiniciado, ocasionalmente, para que podem ser instaladas patches de segurança importantes. A partir de 1 de Agosto de 2016, os clusters do HDInsight baseado em Linux novo (versão 3.4 ou superior,) são reiniciados, utilizando a seguinte agenda:

1. Uma máquina virtual do cluster pode apenas reiniciado para patches no máximo, uma vez num período de 30 dias.
2. O reinício ocorre começando em 12: 00 UTC.
3. O processo de reinício é escalonado em máquinas virtuais no cluster, para que o cluster ainda está disponível durante o processo de reinício.
4. O primeiro reinício para um cluster recém-criado não acontecerá mais cedo do que 30 dias após a data de criação do cluster.

Utilizando a ação de script descrita neste artigo, pode modificar o SO a aplicação de patches agenda da seguinte forma:
1. Ativar ou desativar reinícios automáticos
2. Conjunto a frequência é reiniciado (dias entre reinícios)
3. Definir o dia da semana quando ocorre uma reinicialização

> [!NOTE]
> Esta ação de script apenas irá trabalhar com clusters do HDInsight baseado em Linux criados após 1 de Agosto de 2016. Correções de erros serão aplicadas apenas quando são reiniciadas, VMs. 
>

## <a name="how-to-use-the-script"></a>Como utilizar o script 

Quando utilizar este script requer as seguintes informações:
1. A localização do script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  Para localizar e executar o script em todas as máquinas virtuais no cluster, o HDInsight utiliza este URI.
  
2. Os tipos de nó de cluster que o script é aplicado: headnode, workernode, zookeeper. Este script tem de ser aplicado a todos os tipos de nó do cluster. Se não se aplica a um tipo de nó, as máquinas virtuais para esse tipo de nó continuará a utilizar a agenda de aplicação de patches anterior.


3.  Parâmetro: Este script aceita três parâmetros numérico:

    | Parâmetro | Definição |
    | --- | --- |
    | Ativar/desativar reinícios automáticos |0 ou 1. Um valor de 0 desativa a reinícios automáticos enquanto 1 permite reinícios automáticos. |
    | Frequência |7 a 90 (inclusive). O número de dias a aguardar antes de iniciar as máquinas virtuais para patches que necessitem de um reinício. |
    | Dia da semana |1 a 7 (inclusive). Um valor de 1 indica o reinício deve ocorrer numa segunda-feira e 7 indica um exemplo de Sunday.For, utilizando os parâmetros de 1 60 2 resulta em automático é reiniciado a cada 60 dias (no máximo) na Terça-feira. |
    | Persistência |Ao aplicar uma ação de script para um cluster existente, pode marcar o script como persistente. Scripts persistentes são aplicadas quando workernodes novas são adicionadas ao cluster através do dimensionamento operações. |

> [!NOTE]
> Tem de marcar este script como persistente ao aplicar a um cluster existente. Caso contrário, os novos nós criados através de operações de dimensionamento utilizará a predefinição de agenda de aplicação de patches.
Se aplicar o script como parte do processo de criação de cluster, este é continuada automaticamente.
>

## <a name="next-steps"></a>Passos seguintes

Para obter passos específicos sobre como utilizar a ação de script, consulte as secções seguintes o [clusters do HDInsight baseado em Personalizar Linuz através da ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Utilizar uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script para um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)

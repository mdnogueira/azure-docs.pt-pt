---
title: Compreender e resolver erros de WebHCat no HDInsight - Azure | Microsoft Docs
description: "Saiba como a sobre erros comuns devolvidos pelo WebHCat no HDInsight e como resolvê-los."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1b3d94b1-207d-4550-aece-21dc45485549
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: 1e4d72540a44f3b1838b6ed4dfad47dbe84489dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Compreender e resolver erros recebidos de WebHCat no HDInsight

Saiba mais sobre os erros recebidos quando através de WebHCat com o HDInsight e como resolvê-los. WebHCat é utilizada internamente do lado do cliente de ferramentas, como o Azure PowerShell e as ferramentas do Data Lake para Visual Studio.

## <a name="what-is-webhcat"></a>O que é WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST para [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), uma tabela e a camada de gestão de armazenamento para o Hadoop. WebHCat está ativada por predefinição nos clusters do HDInsight e é utilizado por várias ferramentas para submeter tarefas, obter o estado da tarefa, etc. sem iniciar sessão para o cluster.

## <a name="modifying-configuration"></a>Modificar configuração

> [!IMPORTANT]
> Muitos dos erros listados neste documento ocorrerem porque foi excedido um máximo configurado. Quando o passo de resolução menciona suportadas que podem alterar um valor, tem de utilizar um dos seguintes procedimentos para efetuar a alteração:

* Para **Windows** clusters: utilizar uma ação de script para configurar o valor durante a criação do cluster. Para obter mais informações, consulte [desenvolver ações de script](hdinsight-hadoop-script-actions.md).

* Para **Linux** clusters: utilizar Ambari (web ou a REST API) para modificar o valor. Para obter mais informações, consulte [HDInsight gerir com o Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

### <a name="default-configuration"></a>Configuração predefinida

Se os valores predefinidos seguintes for excedidos, pode degradar o desempenho de WebHCat ou causar erros:

| Definição | O que faz | Valor predefinido |
| --- | --- | --- |
| [yarn.Scheduler.CAPACITY.Maximum-aplicações][maximum-applications] |O número máximo de tarefas que podem estar ativas em simultâneo (pendentes ou em execução) |10,000 |
| [templeton.exec.Max procs][max-procs] |O número máximo de pedidos que podem ser servidos em simultâneo |20 |
| [mapreduce.jobhistory.Max-idade-ms][max-age-ms] |O número de dias que histórico da tarefa é retido |7 dias |

## <a name="too-many-requests"></a>Demasiados pedidos

**Código de estado HTTP**: 429

| Causa | Resolução |
| --- | --- |
| Excedeu o máximo de pedidos simultâneo servido pelo WebHCat por minuto (predefinição 20) |Reduza a carga de trabalho para se certificar de que não submeta mais do que o número máximo de pedidos simultâneos ou aumentar o limite do pedido simultâneo modificando `templeton.exec.max-procs`. Para obter mais informações, consulte [Modifying configuração](#modifying-configuration) |

## <a name="server-unavailable"></a>Server indisponível

**Código de estado HTTP**: 503

| Causa | Resolução |
| --- | --- |
| Este código de estado ocorre normalmente durante a ativação pós-falha entre a primária e secundária HeadNode para o cluster |Aguardar dois minutos e repita a operação |

## <a name="bad-request-content-could-not-find-job"></a>Pedido incorreto conteúdo: não foi possível localizar a tarefa

**Código de estado HTTP**: 400

| Causa | Resolução |
| --- | --- |
| Detalhes da tarefa foram limpos no histórico da tarefa de limpeza |O período de retenção predefinido para o histórico de tarefas é de 7 dias. O período de retenção predefinido pode ser alterado modificando `mapreduce.jobhistory.max-age-ms`. Para obter mais informações, consulte [Modifying configuração](#modifying-configuration) |
| Tarefa tem foi cancelada devido a uma ativação pós-falha |Repita a submissão da tarefa de dois minutos |
| Foi utilizado um id de tarefa inválida |Verifique se o id da tarefa está correto |

## <a name="bad-gateway"></a>Gateway incorreto

**Código de estado HTTP**: 502

| Causa | Resolução |
| --- | --- |
| Libertação de memória interna está a ocorrer dentro do processo de WebHCat |Aguardar a recolha de lixo concluir ou reinicie o serviço de WebHCat |
| Tempo limite a aguardar uma resposta do serviço ResourceManager. Este erro pode ocorrer quando o número de aplicações do Active Directory passa o máximo configurado (predefinição 10 000) |Aguarde tarefas para concluir ou aumentar o limite de trabalho em simultâneo através da modificação atualmente em execução `yarn.scheduler.capacity.maximum-applications`. Para obter mais informações, consulte o [Modifying configuração](#modifying-configuration) secção. |
| Tentar obter todas as tarefas através do [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) chamada ao `Fields` está definido como`*` |Não obtêm *todos os* detalhes da tarefa. Em vez disso, utilize `jobid` para obter os detalhes para as tarefas de maiores apenas a determinados id da tarefa. Ou, não utilize`Fields` |
| O serviço de WebHCat está inativo durante a ativação pós-falha de HeadNode |Aguardar dois minutos e repita a operação |
| Existem mais do que 500 tarefas pendentes submetidas através de WebHCat |Aguarde pela conclusão das tarefas actualmente a aguardar antes de submeter mais tarefas |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

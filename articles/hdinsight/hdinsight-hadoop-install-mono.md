---
title: Instalar ou atualizar Mono no HDInsight - Azure | Microsoft Docs
description: "Saiba como utilizar uma versão específica do Mono com o cluster do HDInsight. Mono é utilizada para executar aplicações de .NET nos clusters do HDInsight baseado em Linux."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: a6e5712026dc2da3627c07e9b411c81fb9a7e1fb
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="install-or-update-mono-on-hdinsight"></a>Instalar ou atualizar Mono no HDInsight

Saiba como instalar uma versão específica do [Mono](https://www.mono-project.com) no HDInsight 3.4 ou superior.

Mono está instalado no HDInsight 3.4 e superior e é utilizada para executar aplicações de .NET. Para obter informações sobre a versão do Mono incluído com cada versão do HDInsight, consulte [controlo de versões do HDInsight componente](hdinsight-component-versioning.md). Para instalar uma versão diferente no seu cluster, utilize a ação de script neste documento. 

## <a name="how-it-works"></a>Como funciona

Este script aceita o parâmetro seguinte:

* __Número de versão mono__: A versão do Mono para instalar. A versão tem de estar disponível na [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

O script instala os pacotes Mono seguintes:

* __concluir o mono__

* __mono de certificados de AC__

## <a name="the-script"></a>O script

__Localização do script__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Requisitos__:

* O script tem de ser aplicado a __nós principais__ e __nós de trabalho__.

## <a name="to-use-the-script"></a>Para utilizar o script

Para obter informações sobre como utilizar este script com o HDInsight, consulte o [clusters do HDInsight baseado em Linux personalizar através da ação de script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) documento. Pode utilizar o script através do portal do Azure, Azure PowerShell ou a CLI do Azure.

Ao seguir o documento de ação de script, utilize o URI seguinte:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Para especificar a versão Mono que está instalada, utilize o número de versão no __parâmetros__ campo. Por exemplo, introduza `5.4` instalar Mono 5.4.

> [!NOTE]
> Quando configurar HDInsight com este script, marcar o script como __persistentes__. Esta definição permite HDInsight aplicar o script para nós de trabalho adicionados através do dimensionamento operações.

## <a name="next-steps"></a>Passos seguintes

Aprendeu como atualizar ou instalar uma versão específica do Mono no HDInsight. Para obter mais informações sobre a utilização de aplicações .NET com Mono no HDInsight, consulte os seguintes documentos:

* [Utilize o .NET para transmissão em fluxo MapReduce no HDInsight](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Utilize o .NET do Hive e do Pig no HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Desenvolver soluções de c# com o Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Migrar soluções .NET para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Para obter mais informações sobre como utilizar as ações de script, consulte [clusters do HDInsight baseado em Linux personalizar através da ação de script](hdinsight-hadoop-customize-cluster-linux.md)
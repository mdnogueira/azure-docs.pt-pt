---
title: Utilize o .NET com MapReduce de Hadoop no HDInsight baseado em Linux - Azure | Microsoft Docs
description: "Saiba como utilizar aplicações .NET para transmissão em fluxo MapReduce no HDInsight baseado em Linux."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: 978606aa5f16842f8198ee67a65b476b4f560ab7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrar soluções .NET para o HDInsight baseado em Windows para o HDInsight baseado em Linux

Utilização de clusters do HDInsight baseado em Linux [Mono (https://mono-project.com)](https://mono-project.com) para executar aplicações de .NET. Mono permite-lhe utilizar componentes de .NET, tais como aplicações de MapReduce com o HDInsight baseado em Linux. Neste documento, saiba como migrar as soluções de .NET criadas para clusters do HDInsight baseados em Windows trabalhar com Mono no HDInsight baseado em Linux.

## <a name="mono-compatibility-with-net"></a>Compatibilidade mono com .NET

Versão mono 4.2.1 está incluído com o HDInsight versão 3.5. Para obter mais informações sobre a versão do Mono incluído com o HDInsight, consulte [as versões de componentes do HDInsight](hdinsight-component-versioning.md). Para instalar uma versão específica do Mono, consulte o [instalar ou atualizar Mono](hdinsight-hadoop-install-mono.md) documento.

Para obter mais informações sobre a compatibilidade entre Mono e .NET, consulte o [compatibilidade Mono (http://www.mono-project.com/docs/about-mono/compatibility/)](http://www.mono-project.com/docs/about-mono/compatibility/) documento.

> [!IMPORTANT]
> A estrutura SCP.NET é compatível com Mono. Para obter mais informações sobre a utilização SCP.NET com Mono, consulte [utilize o Visual Studio para desenvolver topologias c# para Apache Storm no HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Análise de portabilidade automatizada

O [.NET portabilidade analisador](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) pode ser utilizado para gerar um relatório de incompatibilidades entre a sua aplicação e Mono. Utilize os seguintes passos para configurar o analisador para verificar a sua aplicação para portabilidade Mono:

1. Instalar o [.NET portabilidade analisador](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Durante a instalação, selecione a versão do Visual Studio para utilizar.

2. A partir do Visual Studio 2015, selecione __analisar__ > __portabilidade analisador definições__e certifique-se de que __4.5__ é verificado o __Mono__ secção.

    ![4.5 marcada na secção Mono para as definições de analisador](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Selecione __OK__ para guardar a configuração.

3. Selecione __analisar__ > __analisar portabilidade de assemblagem__. Selecione a assemblagem que contém a sua solução e, em seguida, selecione __abra__ para iniciar uma análise.

4. Assim que a análise estiver concluída, selecione __analisar__ > __ver relatórios de análise__. No __resultados de análise portabilidade__, selecione __abrir relatório__ para abrir um relatório.

    ![Caixa de diálogo de resultados de analisador de portabilidade](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> O analisador de não é possível detetar cada problema com a sua solução. Por exemplo, um caminho de ficheiro de `c:\temp\file.txt` é considerado OK se Mono está em execução no Windows. O mesmo caminho não é válido numa plataforma Linux.

## <a name="manual-portability-analysis"></a>Análise de portabilidade manual

Efetuar uma auditoria manual do seu código utilizando as informações de [portabilidade de aplicação (http://www.mono-project.com/docs/getting-started/application-portability/)](http://www.mono-project.com/docs/getting-started/application-portability/) documento.

## <a name="modify-and-build"></a>Modificar e criar

Pode continuar a utilizar o Visual Studio para criar as suas soluções de .NET para o HDInsight. No entanto, tem de se certificar de que o projeto está configurado para utilizar o .NET Framework 4.5.

## <a name="deploy-and-test"></a>Implementar e testar

Depois de modificar a sua solução com as recomendações do analisador de portabilidade .NET ou a partir de uma análise manual, terá de a testar com o HDInsight. Testar a solução num cluster do HDInsight baseado em Linux pode revelar problemas subtis que precisam de ser corrigido. Recomendamos que ative o registo adicional na sua aplicação ao testá-lo.

Para obter mais informações sobre aceder a registos, consulte os seguintes documentos:

* [Access YARN application logs on Linux-based HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md) (Aceder a registos de aplicações do YARN no HDInsight baseado no Linux)

## <a name="next-steps"></a>Passos seguintes

* [Utilizar c# com o MapReduce no HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Utilizar funções definidas pelo utilizador c# com o Hive e do Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Desenvolver topologias c# para Storm no HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
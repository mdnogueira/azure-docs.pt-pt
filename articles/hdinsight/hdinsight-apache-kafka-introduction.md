---
title: "Uma introdução ao Apache Kafka no HDInsight | Microsoft Docs"
description: "Saiba mais sobre o Apache Kafka no HDInsight. O que é, o que faz e onde encontrar exemplos e obter informações de introdução."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ecc141c9afa46f23d31de4356068ef4f98a92aa
ms.openlocfilehash: a47a911967a50102f62d471d2e73cf86bdeda3b1
ms.lasthandoff: 02/10/2017

---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>Introdução ao Apache Kafka no HDInsight (pré-visualização)

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de transmissão em fluxo distribuída de código aberto que pode ser utilizada para criar aplicações e pipelines de dados de transmissão em fluxo em tempo real. O Kafka também fornece a funcionalidade de mediador de mensagem semelhante a uma fila de mensagens, onde pode publicar e subscrever fluxos de dados nomeados. O Kafka no HDInsight fornece um serviço gerido, altamente escalável e disponível na cloud do Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>Porquê utilizar o Kaftka no HDInsight?

O Kafka oferece as seguintes funcionalidades:

* Padrão de mensagem de publicação-subscrição: o Kafka fornece uma API de Produtor para publicar registos num tópico do Kafka. A API de Consumidor é utilizada ao subscrever um tópico.

* Processamento de fluxo: o Kafka é muitas vezes utilizado com o Apache Storm ou Spark para processamento de fluxo em tempo real. O Kafka 0.10.0.0 (HDInsight versão 3.5) introduziu uma API de transmissão em fluxo que lhe permite criar soluções de transmissão em fluxo, sem precisar do Storm ou do Spark.

* Escala horizontal: transmissões em fluxo de partições do Kafka nos nós no cluster do HDInsight. Os processos de consumidor podem estar associados a partições individuais para fornecer balanceamento de carga ao consumir registos.

* Entrega por ordem: em cada partição, os registos são armazenados na transmissão em fluxo pela ordem em que foram recebidos. Ao associar um processo de consumidor por partição, pode garantir que os registos são processados por ordem.

* Com tolerância a falhas: as partições podem ser replicadas entre nós para fornecer tolerância a falhas.

## <a name="use-cases"></a>Casos de utilização

* **Mensagens**: uma vez que suporta o padrão de mensagem de publicação-subscrição, o Kafka é frequentemente utilizado como um mediador de mensagem.

* **Controlo de atividades**: uma vez que o Kafka fornece os registos por ordem, pode ser utilizado para controlar e voltar a criar atividades. Por exemplo, ações do utilizador num site ou numa aplicação.

* **Agregação**: com o processamento de transmissão em fluxo, pode agregar informações a partir de várias transmissões em fluxo para combinar e centralizar as informações nos dados operacionais.

* **Transformação**: com o processamento de transmissão em fluxo, pode combinar e enriquecer os dados a partir de vários tópicos de entrada em um ou mais tópicos de saída.

## <a name="where-do-i-start"></a>Por onde devo começar?

Consulte [Get started with Kafka on HDInsight (Introdução ao Kafka no HDInsight)](hdinsight-apache-kafka-get-started.md), para ver os passos sobre a criação de um cluster do Kafka e utilização do Kafka, incluindo exemplos baseados em Java exemplos sobre como utilizar o produtor, consumidor e API de transmissão em fluxo

## <a name="next-steps"></a>Passos seguintes

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Introdução ao Kafka no HDInsight](hdinsight-apache-kafka-get-started.md)

* [Utilizar MirrorMaker para criar uma réplica de Kafka no HDInsight](hdinsight-apache-kafka-mirroring.md)

* [Utilizar o Apache Storm com Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)

* [Utilizar o Apache Spark com Kafka no HDInsight](hdinsight-apache-spark-with-kafka.md)



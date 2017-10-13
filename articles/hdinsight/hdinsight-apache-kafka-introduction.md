---
title: "Uma introdução ao Apache Kafka no HDInsight – Azure | Microsoft Docs"
description: "Saiba mais sobre o Apache Kafka no HDInsight: o que é, o que faz e onde encontrar exemplos e obter informações sobre como começar."
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
ms.date: 09/07/2017
ms.author: larryfr
ms.openlocfilehash: 39234ca792983178cfd4304e001271ea30e28ae6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
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

* Integração com os Azure Managed Disks: os discos geridos permitem uma maior escala e taxa de débito para os discos utilizados pelas máquinas virtuais no cluster do HDInsight.

    Os discos geridos estão ativados por predefinição no Kafka no HDInsight. O número de discos utilizados por nó pode ser configurado durante a criação do HDInsight. Para obter mais informações sobre os discos geridos, consulte [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md).

    Para obter mais informações sobre como configurar os discos geridos com o Kafka no HDInsight, consulte [Increase scalability of Kafka on HDInsight](hdinsight-apache-kafka-scalability.md) (Aumentar a escalabilidade do Kafka no HDInsight).

## <a name="use-cases"></a>Casos de utilização

* **Mensagens**: uma vez que suporta o padrão de mensagem de publicação-subscrição, o Kafka é frequentemente utilizado como um mediador de mensagem.

* **Controlo de atividades**: uma vez que o Kafka fornece os registos por ordem, pode ser utilizado para controlar e voltar a criar atividades. Por exemplo, ações do utilizador num site ou numa aplicação.

* **Agregação**: com o processamento de transmissão em fluxo, pode agregar informações a partir de várias transmissões em fluxo para combinar e centralizar as informações nos dados operacionais.

* **Transformação**: com o processamento de transmissão em fluxo, pode combinar e enriquecer os dados a partir de vários tópicos de entrada em um ou mais tópicos de saída.

## <a name="architecture"></a>Arquitetura

![Configuração do cluster do Kafka](./media/hdinsight-apache-kafka-introduction/kafka-cluster.png)

Este diagrama mostra uma configuração do Kafka comum que utiliza grupos de consumidores, particionamento e replicação para oferecer leitura paralela de eventos com tolerância a falhas. O Apache ZooKeeper foi concebido para transações simultâneas, resilientes e de baixa latência, pois gere o estado do cluster do Kafka. O Kafka armazena registos em *tópicos*. Os registos são produzidos por *produtores* e consumidos por *consumidores*. Os produtores obtêm registos de *mediadores* Kafka. Cada nó de trabalho no cluster HDInsight é um mediador Kafka. É criada uma partição para cada consumidor, permitindo o processamento paralelo dos dados de transmissão em fluxo. A replicação é utilizada para espalhar as partições pelos nós, protegendo contra interrupções do nó (mediador). A partição assinalada com *(L)* é a partição líder dessa partição específica. O tráfico do produtor é encaminhado para o líder de cada nó mediante a utilização do estado gerido pelo ZooKeeper.

> [!IMPORTANT]
> O Kafka não tem conhecimento do hardware subjacente (bastidor) no datacenter do Azure. Para garantir o correto equilíbrio das partições no hardware subjacente, veja [configure high availability of data (Kafka)](hdinsight-apache-kafka-high-availability.md) [Configurar a elevada disponibilidade dos dados (Kafka)]

## <a name="next-steps"></a>Passos seguintes

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Introdução ao Kafka no HDInsight](hdinsight-apache-kafka-get-started.md)

* [Utilizar MirrorMaker para criar uma réplica de Kafka no HDInsight](hdinsight-apache-kafka-mirroring.md)

* [Utilizar o Apache Storm com Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)

* [Utilizar o Apache Spark com Kafka no HDInsight](hdinsight-apache-spark-with-kafka.md)

* [Ligar ao Kafka através de uma Rede Virtual do Azure](hdinsight-apache-kafka-connect-vpn-gateway.md)
---
title: "Elevada disponibilidade com o Apache Kafka – Azure HDInsight | Microsoft Docs"
description: "Saiba como garantir a elevada disponibilidade com o Apache Kafka no Azure HDInsight. Saiba como reequilibrar réplicas da partição no Kafka para que fiquem localizadas em domínios de falhas diferentes dentro da região do Azure que contém o HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f8164d1c3483b28e5f2abcc8035da78880daec1e
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017

---
<a id="high-availability-of-your-data-with-apache-kafka-preview-on-hdinsight" class="xliff"></a>

# Elevada disponibilidade dos seus dados com o Apache Kafka (pré-visualização) no HDInsight

Saiba como configurar réplicas da partição para que os tópicos do Kafka tirem partido da configuração de rack do hardware subjacente. Esta configuração garante a disponibilidade dos dados armazenados no Apache Kafka no HDInsight.

<a id="fault-and-update-domains-with-kafka" class="xliff"></a>

## Domínios de falha e atualização com o Kafka

Um domínio de falha é um agrupamento lógico de hardware subjacente num centro de dados do Azure. Cada domínio de falha partilha um comutador de rede e uma fonte de alimentação. As máquinas virtuais e os discos geridos que implementam os nós num cluster HDInsight são distribuídos por esses domínios de falha. Esta arquitetura limita o possível impacto de falhas físicas de hardware.

Cada região do Azure tem um número específico de domínios de falha. Para obter uma lista de domínios e o número de domínios de falha que contêm, consulte a documentação [Availability Sets](../virtual-machines/linux/regions-and-availability.md#availability-sets) (Conjuntos de disponibilidade).

> [!IMPORTANT]
> O Kafka não está ciente dos domínios de falha. Quando cria um tópico no Kafka, pode armazenar todas as réplicas da partição no mesmo domínio de falha. Para resolver este problema, disponibilizamos a [ferramenta de reequilíbrio de partições do Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

<a id="when-to-rebalance-partition-replicas" class="xliff"></a>

## Quando deve reequilibrar as réplicas de partições

Para garantir a maior disponibilidade dos seus dados do Kafka, deve reequilibrar as réplicas de partições do seu tópico nos seguintes momentos:

* Quando é criado um novo tópico ou partição

* Quando expandir um cluster

<a id="replication-factor" class="xliff"></a>

## Fator de replicação

> [!IMPORTANT]
> É recomendável utilizar uma região do Azure que contenha três domínios de falha e um fator de replicação de 3.

Se tiver de utilizar uma região que contenha apenas dois domínios de falha, utilize um fator de replicação de 4 para distribuir as réplicas uniformemente entre os dois domínios de falha.

Para um exemplo de criação de tópico e definição do fator de replicação, consulte o documento [Começar com o Kafka no HDInsight](hdinsight-apache-kafka-get-started.md).

<a id="how-to-rebalance-partition-replicas" class="xliff"></a>

## Como reequilibrar as réplicas de partições

Utilize a [ferramenta de reequilíbrio de partições do Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) para reequilibrar os tópicos selecionados. Esta ferramenta deve ser executada numa sessão SSH para o nó principal do cluster do Kafka.

Para obter mais informações sobre a ligação ao HDInsight através do SSH, consulte o documento [Utilizar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

<a id="next-steps" class="xliff"></a>

## Passos seguintes

* [Escalabilidade do Kafka no HDInsight](hdinsight-apache-kafka-scalability.md)
* [Espelhamento com o Kafka no HDInsight](hdinsight-apache-kafka-mirroring.md)

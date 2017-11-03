---
title: No Azure HDInsight o planeamento de capacidade de cluster | Microsoft Docs
description: Como especificar um cluster do HDInsight para a capacidade e o desempenho.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: b4bdf3339e585a7b22a1945871f802854020fb94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planeamento da capacidade para clusters do HDInsight

Antes de implementar um cluster do HDInsight, planear a capacidade de cluster pretendido por determinar o desempenho necessário e a escala. Isto ajuda a planeamento otimizar os custos e a facilidade de utilização. Algumas decisões de capacidade de cluster não podem ser alteradas após a implementação. Se alterar os parâmetros de desempenho, um cluster pode ser dismantled e voltar a criar sem perder dados armazenados.

As chaves questões a colocar para planeamento de capacidade são:

* Em cada região geográfica deve implementar o cluster?
* Quantidade de armazenamento precisa?
* Que tipo de cluster deve implementar?
* O tamanho e tipo de máquina virtual (VM) devem os nós do cluster utilizar?
* O número de nós de trabalho deve ter o cluster

## <a name="choose-an-azure-region"></a>Selecione uma região do Azure

A região do Azure determina onde o cluster seja aprovisionado fisicamente. Para minimizar a latência de leituras e escritas, o cluster deve estar perto os dados.

HDInsight está disponível em várias regiões do Azure. Para localizar a região mais próxima, consulte o *HDInsight Linux* entrada em *dados + análise* no [Azure produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Escolha a localização de armazenamento e o tamanho

### <a name="location-of-default-storage"></a>Localização de armazenamento predefinido

O armazenamento de predefinido, uma conta de armazenamento do Azure ou o Azure Data Lake Store, tem de ser na mesma localização do seu cluster. Armazenamento do Azure está disponível em todas as localizações. Data Lake Store está disponível em algumas regiões - consulte a disponibilidade de Data Lake Store atual em *armazenamento* no [Azure produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Localização dos dados existentes

Se já tiver uma conta de armazenamento ou a Data Lake Store que contém os dados e pretende utilizar este tipo de armazenamento como armazenamento de predefinido do cluster, tem de implementar o cluster nessa mesma localização.

### <a name="storage-size"></a>Tamanho de armazenamento

Depois de ter implementado num cluster do HDInsight, pode anexar mais contas de armazenamento do Azure ou aceder a outros arquivos do Data Lake. Todas as suas contas do storage têm de residir na mesma localização do seu cluster. Uma Data Lake Store pode ser numa localização diferente, apesar de esta configuração poderá dar origem alguma latência de leitura/escrita de dados.

O armazenamento do Azure tem algumas [os limites de capacidade](../azure-subscription-service-limits.md#storage-limits), enquanto o Data Lake Store é praticamente ilimitado.

Um cluster pode aceder a uma combinação de contas de armazenamento diferente. Exemplos típicos incluem:

* Quando a quantidade de dados é provável que exceder a capacidade de armazenamento de um contentor de armazenamento de blob único.
* Quando a taxa de acesso para o contentor de blob pode exceder o limiar em que ocorre a limitação.
* Quando pretender de dados, já tenha carregado um contentor de blob disponível para o cluster.
* Em que pretende isolar as diferentes partes de armazenamento por motivos de segurança ou para simplificar a administração.

Para um cluster de nó de 48 recomendamos as contas de armazenamento de 4 a 8. Apesar de já pode ser armazenamento total suficiente, cada conta do storage fornece mais largura de banda de rede para os nós de computação. Quando tiver várias contas de armazenamento, utilize um nome aleatório para cada conta de armazenamento, sem um prefixo. O objetivo de nomenclatura aleatórias é reduzir a possibilidade de congestionamentos de armazenamento (otimização) ou falhas de modo de comuns em todas as contas. Para um melhor desempenho, utilize apenas um contentor por conta de armazenamento.

## <a name="choose-a-cluster-type"></a>Escolher um tipo de cluster

O tipo de cluster determina a carga de trabalho que do cluster do HDInsight está configurado para executar, tais como Hadoop, Storm, Kafka ou Spark. 
<!-- For a detailed description of the available cluster types, see [HDInsight Architecture](hdinsight-architecture.md). -->
Cada tipo de cluster tem uma topologia de implementação específica que inclui os requisitos para o tamanho e número de nós.

## <a name="choose-the-vm-size-and-type"></a>Escolha o tamanho da VM e o tipo

Cada tipo de cluster tem um conjunto de tipos de nó e cada tipo de nó tem opções específicas para o respetivo tamanho da VM e o tipo.

Para determinar o tamanho do cluster ideal para a sua aplicação, pode avaliar a capacidade de cluster e aumentar o tamanho, conforme indicado. Por exemplo, pode utilizar uma carga de trabalho simulada, ou um *consulta canary*. Com uma carga de trabalho simulada, executa as cargas de trabalho esperadas em clusters de tamanho diferente, aumentar gradualmente o tamanho até que o desempenho pretendido for atingido. Uma consulta canary pode ser inserida periodicamente, entre outras consultas de produção para mostrar ou não o cluster tem recursos suficientes.

O tamanho da VM e o tipo é determinado por power, tamanho da RAM e latência de rede de processamento da CPU:

* CPU: O tamanho da VM determinam o número de núcleos. Os mais núcleos, maior grau de computação paralela pode alcançar a cada nó. Além disso, alguns tipos VM tem núcleos mais rápidos.

* RAM: O tamanho da VM dita também a quantidade de RAM disponível na VM. Para cargas de trabalho que armazenam os dados para processamento, em vez de leitura a partir do disco, certifique-se os nós de trabalho tem memória suficiente para se ajustar os dados.

* Rede: Para a maioria dos tipos de cluster, os dados processados pelo cluster são não num disco local, mas em vez de um serviço de armazenamento externo, como o Data Lake Store ou armazenamento do Azure. Considere a largura de banda de rede e o débito entre o nó de VM e o serviço de armazenamento. Normalmente, aumenta a largura de banda de rede disponível para uma VM com o tamanho maior. Para obter mais informações, consulte [descrição geral de tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Escolha a escala de cluster

Dimensionamento de um cluster é determinado pela quantidade dos respetivos nós VM. Para todos os tipos de cluster, existem tipos de nó com uma escala específica e tipos de nó que suporta Escalamento horizontal. Por exemplo, um cluster pode requerer exatamente três nós de ZooKeeper ou dois nós Head. Nós de trabalho que efetuar o processamento de dados de forma distribuída podem beneficiar do aumentar horizontalmente, ao adicionar nós de trabalho adicionais.

Consoante o tipo de cluster, o aumento do número de nós de trabalho adiciona a capacidade de cálculo adicional (por exemplo, mais núcleos), mas também pode adicionar para a quantidade total de memória necessária para todo o cluster suportar dentro da memória armazenamento de dados a ser processados. Tal como acontece com a opção de tamanho da VM e o tipo, selecionar o dimensionamento do cluster à direita é normalmente contactado empirically, através de cargas de trabalho simuladas ou canary consultas.

Pode ampliar o cluster para satisfazer pico de carga pedidos, em seguida, dimensionar volta a para baixo, quando esses nós adicionais já não são necessárias.
<!-- - see [Scaling - best practices](hdinsight-scaling-best-practices.md). -->

### <a name="cluster-lifecycle"></a>Ciclo de vida do cluster

São-lhe cobrados para duração de um cluster. Se existirem apenas horas específicas que precisa do cluster de cópia de segurança e em execução, pode criar clusters a pedido utilizando o Azure Data Factory.
<!-- [create on-demand clusters using Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). -->
Pode também criar scripts do PowerShell que aprovisionar e elimina o cluster e, em seguida, agendar essas scripts com [da automatização do Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> Quando é eliminado um cluster, o respetivo metastore do Hive predefinida também é eliminado. Para manter o metastore para a criação de nova cluster seguinte, utilize um armazenamento de metadados externo, como a base de dados do Azure ou Oozie.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolar os erros de tarefas de cluster

Por vezes, os erros podem ocorrer devido à execução paralela de mapa de vários e reduzir componentes num cluster de vários nós. Para ajudar a isolar o problema, tente testar distribuídas através da execução simultânea várias tarefas num cluster de nó único, em seguida, expanda esta abordagem para executar várias tarefas em simultâneo em clusters que contêm mais do que um nó. Para criar um cluster de nó único do HDInsight no Azure, utilize o *avançadas* opção.

Também pode instalar o ambiente de desenvolvimento de nó único no seu computador local e testar a solução não existe. Hortonworks fornece um ambiente de desenvolvimento local de nó único para as soluções baseadas no Hadoop que é útil para desenvolvimento inicial, prova de conceito, teste. Para obter mais informações, consulte [Hortonworks Sandbox](http://hortonworks.com/products/hortonworks-sandbox/).

Para identificar o problema num cluster de nó único local pode voltar a executar as tarefas que falharam e ajustar os dados de entrada ou utilizar conjuntos de dados mais pequenos. Como executar essas tarefas depende da plataforma e o tipo de aplicação.

## <a name="quotas"></a>Quotas

Após determinar o tamanho de VM de cluster de destino, a escala e o tipo, verifique os limites de capacidade de quota atual da sua subscrição. Quando chegar um limite de quota, poderá não conseguir implementar novos clusters ou ampliar clusters existentes adicionando mais nós de trabalho. Foi atingido o limite quota mais comuns é a quota de núcleos de CPU que existe na subscrição, região e, níveis de série VM. Por exemplo, a subscrição pode ter um limite de total de 200 núcleos, com um limite de 30 núcleos na sua região e um limite de 30 de núcleos de instâncias de VM. Pode [contacte o suporte para pedir um aumento de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

No entanto, existem alguns limites de quota fixos, por exemplo uma única subscrição do Azure pode ter no máximo 10 000 núcleos. Para obter detalhes sobre estes limites, consulte [subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>Passos seguintes

* [Configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md): Saiba como configurar clusters no HDInsight com o Hadoop, Spark, Kafka, interativa de ramo de registo, HBase, servidor R ou Storm.
* [Monitorizar o desempenho do cluster](hdinsight-key-scenarios-to-monitor.md): saber mais sobre os cenários principais para monitorizar o cluster do HDInsight que pode afetar a capacidade do seu cluster.

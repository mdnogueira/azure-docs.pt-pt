---
title: "Hadoop componentes e as versões - Azure HDInsight | Microsoft Docs"
description: "Saiba os componentes do Hadoop e versões no HDInsight e os níveis de serviço disponíveis esta distribuição de nuvem da plataforma de dados do Hortonworks."
keywords: "versões de hadoop, componentes do ecossistema do hadoop, componentes do hadoop, como verificar a versão de hadoop"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: bprakash
ms.openlocfilehash: d1098ea7f7ab7765f9769dd2e398eb3b62c5557b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Quais são os componentes do Hadoop e versões disponíveis com o HDInsight?

Saiba mais sobre os componentes do ecossistema do Apache Hadoop e versões no Microsoft Azure HDInsight, bem como os níveis de serviço Standard e Premium. Além disso, saiba como verificar as versões de componentes do Hadoop no HDInsight. 

Cada versão do HDInsight é uma distribuição de nuvem de uma versão do Hortonworks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Componentes do Hadoop disponíveis com diferentes versões do HDInsight
O Azure HDInsight suporta várias versões de cluster de Hadoop que podem ser implementadas em qualquer altura. Cada opção de versão cria uma versão específica da distribuição HDP e um conjunto de componentes que estão contidas dentro dessa distribuição. A partir de 17 de Fevereiro de 2017, a versão de cluster predefinido utilizada pelo Azure HDInsight é 3.5 e baseia-se no HDP 2.5.

As versões de componente associadas as versões de cluster do HDInsight estão listadas na seguinte tabela. 

> [!NOTE]
> Poderá alterar a versão predefinida para o serviço de HDInsight sem aviso prévio. Se tiver uma dependência de versão, especifique a versão do HDInsight ao criar os clusters com o SDK .NET da Azure PowerShell e da CLI do Azure.

| Componente | HDInsight 3.6 (predefinição) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | 3.2 do HDInsight | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| O Apache Hadoop e YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| O Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| O Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive e HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| O Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| O Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| O Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| O Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| O Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| O Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (apenas Linux) |1.6.2 + 2.0 (apenas Linux) |1.6.0 (apenas Linux) |1.5.2 (Linux experimental compilação apenas) |1.3.1 (apenas Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Verifique a existência de informações de versão do componente de Hadoop atuais

Versões de componentes do ecossistema do Hadoop associadas com as versões de cluster do HDInsight podem alterar com atualizações para o HDInsight. Para verificar os componentes do Hadoop e certifique-se de que versões estão a ser utilizadas para um cluster, utilize a API de REST do Ambari. O **GetComponentInformation** comando obtém informações sobre os componentes de serviço. Para obter mais informações, consulte o [Ambari documentação][ambari-docs].

Para os clusters do Windows, outra forma de verificar a versão do componente é para início de sessão para um cluster utilizando o ambiente de trabalho remoto e examinar o conteúdo do diretório C:\apps\dist\.

> [!IMPORTANT]
> Linux é o único sistema operativo utilizado no HDInsight versão 3.4 ou posterior. Para obter mais informações, consulte [extinção do Windows no HDInsight](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Notas de versão

Consulte [notas de versão do HDInsight](hdinsight-release-notes.md) para notas de versão adicionais nas versões mais recentes do HDInsight.

## <a name="supported-hdinsight-versions"></a>Versões suportadas do HDInsight
A tabela seguinte lista as versões do HDInsight que estão atualmente disponíveis no portal do Azure. As versões HDP que correspondem a cada versão do HDInsight são listadas juntamente com as datas de versão do produto. As datas de expiração e extinção de suporte também são fornecidas quando se estiver a conhecido.

> [!NOTE]
> Depois do suporte para uma versão tiver expirado, poderá não ser disponível através do portal clássico do Microsoft Azure. No entanto, as versões de cluster continuem a estar a utilizar disponível o `Version` parâmetro no Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) comando e o SDK .NET até que a data de retirada da versão.
> 
> Elevada clusters com dois nós principais são implementadas por predefinição para o HDInsight versão 2.1 e posterior. Não estão disponíveis para os clusters do HDInsight versão 1.6.

| Versão do HDInsight | Versão HDP | SO DE VM | Elevada disponibilidade | Data da versão | Disponibilidade no portal do Azure | Data de expiração de suporte | Data de retirada |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16 |Sim |4 de Abril de 2017 |Sim | | |
| HDInsight 3.5 |HDP 2,5 |Ubuntu 16 |Sim |30 de Setembro de 2016 |Sim |5 de Setembro de 2017 |31 de Maio de 2018 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Sim |29 de Março de 2016 |Sim |29 de Dezembro de 2016 |9 de Janeiro de 2018 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |Sim |2 de Dezembro de 2015 |Sim |27 de Junho de 2016 |31 de Julho de 2018 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |Sim |2 de Dezembro de 2015 |Sim |27 de Junho de 2016 |31 de Julho de 2017 |
| 3.2 do HDInsight |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012 R2 |Sim |18 de Fevereiro de 2015 |Não |1 de Março de 2016 |1 de Abril de 2017 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |Sim |24 de Junho de 2014 |Não |18 de Maio de 2015 |30 de Junho de 2016 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |Sim |11 de Fevereiro de 2014 |Não |17 de Setembro de 2014 |30 de Junho de 2015 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |Sim |28 de Outubro de 2013 |Não |12 de Maio de 2014 |31 de Maio de 2015 |
| HDInsight 1.6 |HDP 1.1 | |Não |28 de Outubro de 2013 |Não |26 de Abril de 2014 |31 de Maio de 2015 |

## <a name="hdinsight-windows-retirement"></a>Extinção do Windows do HDInsight
Microsoft Azure HDInsight versão 3.3 foi a última versão do HDInsight no Windows. A data de retirada para o HDInsight no Windows é de 31 de Julho de 2018. Se tiver quaisquer clusters do HDInsight no Windows 3.3 ou anteriores, tem de migrar para o HDInsight no Linux (HDInsight versão 3.5 ou posterior) antes de 31 de Julho de 2018. Migrar para o SO Linux permite-lhe manter a capacidade de criar ou redimensionar seus clusters do HDInsight. Suporte para o HDInsight versão 3.3 no Windows expirou 27 de Junho de 2016.

A partir do HDInsight versão 3.4, Microsoft lançou HDInsight apenas no SO Linux. Como resultado, alguns dos componentes de dentro do HDInsight estão disponíveis para Linux apenas. Estes incluem Apache Ranger, Kafka, consulta interativas, aplicações do HDInsight Spark e o Azure Data Lake Store, como o sistema de ficheiros primário. Em versões futuras do HDInsight só estão disponíveis no SO Linux. Não haverá nenhuma versões futuras do HDInsight no Windows. 

## <a name="faqs"></a>Perguntas mais frequentes

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>O que é a linha cronológica para extinguir o HDInsight no Windows?
De 2018, 31 de Julho, é a data de retirada para o HDInsight no Windows. Se a data de retirada planeada é diferente para a região, será notificado separadamente. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>O que é o impacto da extinção do HDInsight no Windows para clientes existentes?
Depois de é extinguido HDInsight no Windows, não é possível criar um novo cluster de HDInsight Windows ou redimensionar um cluster de HDInsight Windows existente. Suporte para o HDInsight versão 3.3 expirou 27 de Junho de 2016. Por conseguinte, não é qualquer suporte ou correções de erros para o HDInsight 3.3 ou versões anteriores. Em versões futuras do HDInsight só estão disponíveis no SO Linux. Não haverá nenhuma versões futuras do HDInsight no Windows.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Quais as versões do HDInsight no Windows são afetadas?
O Azure HDInsight versão 3.3 é a última versão do HDInsight para o Windows. É retirado HDInsight no Windows, versão de clusters, todos os HDInsight Windows 3.3 ou anteriores têm de ser migradas para o HDInsight no Linux versão 3.5 ou posterior. Migrar clusters para o HDInsight no Linux permite-lhe manter a capacidade de criar novos clusters ou redimensionar clusters existentes. 

### <a name="what-do-i-need-to-do"></a>O que é necessário fazer?
Migre os clusters do HDInsight Windows para um cluster do HDInsight com Linux suportadas antes de 31 de Julho de 2018. Saiba mais no [documento de migração de HDInsight](hdinsight-migrate-from-windows-to-linux.md). Para obter detalhes sobre as versões do Azure HDInsight, consulte a lista de [versões](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Onde posso encontrar o tipo de SO do cluster?
No portal do Azure, aceda à página de descrição geral do Cluster do HDInsight e localizar **tipo de Cluster** em **Essentials**. Os tipos de SO do cluster estão listados nessa página. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Posso não pode migrar para um cluster do HDInsight com Linux por 31 de Julho de 2018. O que é o impacto para os meus cluster HDInsight Windows?
O cluster do HDInsight Windows é executado como-é, mas não é possível criar um novo cluster de HDInsight Windows ou redimensionar um cluster de HDInsight Windows existente. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>A minha cluster tem uma dependência de .NET. Como resolver o esta dependência no Linux
Pode resolver a dependência de cluster do Linux, utilizando o [projeto Mono](http://www.mono-project.com/). Esta implementação de código aberto do .NET está disponível para clusters do HDInsight com Linux. Saiba mais no [documento de migração de HDInsight](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Sou um novo cliente para o HDInsight no Windows. Como posso criar um cluster do HDInsight Windows?
A partir de 3 de Julho de 2017, apenas clientes existentes do Windows do HDInsight podem criar novas janelas de HDInsight clusters. Novos clientes não é possível criar um cluster do HDInsight Windows no portal do Azure utilizando o PowerShell ou o SDK. Recomendamos que novos clientes, crie um cluster do HDInsight do Linux. Os clientes existentes podem criar as novas janelas de HDInsight clusters até o HDInsight no Windows data de retirada. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Existe um impacto de preços associado a mover do HDInsight no Windows para o HDInsight no Linux?
Não, o preço é o mesmo para o HDInsight no SO. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Quais são as vantagens de cliente associadas a mudança para utilizar apenas o HDInsight no Linux?
* Mais rápido tempo mercado para tecnologias de macrodados open source para através do serviço do HDInsight
* Um grande Comunidade e ecossistema de suporte
* Capacidade para desenvolvimento de Active Directory pela Comunidade de código aberto para o Hadoop e outras tecnologias de macrodados

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>HDInsight no Linux fornece funcionalidades adicionais além da que está disponível no HDInsight no Windows?
A partir do HDInsight versão 3.4, Microsoft lançou HDInsight apenas no SO Linux. Como resultado, alguns dos componentes de dentro do HDInsight estão disponíveis para Linux apenas. Estes incluem Apache Ranger, Kafka, consulta interativas, aplicações do HDInsight Spark e o Azure Data Lake Store, como o sistema de ficheiros primário. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrato de nível de serviço para as versões de cluster do HDInsight
O contrato de nível de serviço (SLA) está definido em termos de um _suporte janela_. A janela de suporte é o período de tempo que um clusters do HDInsight versão é suportada pela Microsoft e atendimento ao cliente. Se a versão tem um _suportar data de expiração_ que foi efectuada com êxito, o cluster do HDInsight é fora da janela de suporte. Para obter mais informações sobre as versões suportadas, consulte a lista de [versões de cluster do HDInsight](hdinsight-migrate-from-windows-to-linux.md). A data de expiração de suporte para um especificado HDInsight versão X (após está disponível uma versão mais recente do X + 1) é calculada como o posterior de:  

* Fórmula 1: Adicione 180 dias até à data quando os clusters do HDInsight versão X foi libertado.
* Fórmula 2: Adicione 90 dias até à data quando os clusters do HDInsight versão X + 1 é disponibilizada no portal do Azure.

O _data de retirada_ -se da data após o qual não é possível criar a versão do cluster HDInsight. Iniciar a 31 de Julho de 2017, não pode redimensionar um cluster do HDInsight após a respetiva data de retirada. 

> [!NOTE]
> Clusters do HDInsight Windows (incluindo versões 2.1, 3.0, 3.1, 3.2 e 3.3) executam família de SO convidado do Azure versão 4, que utiliza a versão de 64 bits do Windows Server 2012 R2. Família de SO convidado do Azure versão 4 suporta as versões do .NET Framework 4.0, 4.5, 4.5.1 e 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks as notas associadas com as versões do HDInsight

A secção fornece ligações para notas de versão das distribuições de plataforma de dados do Hortonworks e os componentes de Apache que são utilizados com o HDInsight.
* Clusters do HDInsight versão 3.6 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Clusters do HDInsight versão 3.5 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Clusters do HDInsight versão 3.5 está a _predefinido_ cluster de Hadoop, que é criado no portal do Azure.
* Clusters do HDInsight versão 3.4 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Clusters do HDInsight versão 3.3 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Notas de versão do Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) estão disponíveis no Web site do Apache.
  * [Notas de versão Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) estão disponíveis no Web site do Apache.
* Clusters do HDInsight versão 3.2 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 2.2][hdp-2-2].

  * Notas de versão para componentes Apache específicos estão disponíveis da seguinte forma: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [comuns](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), e [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Clusters do HDInsight versão 3.1 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Clusters do HDInsight 3.1 criados antes 7 de Novembro,, de 2014, baseiam-se no [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Clusters do HDInsight versão 3.0 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 2.0][hdp-2-0-8].
* Clusters do HDInsight versão 2.1 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 1.3][hdp-1-3-0].
* Clusters do HDInsight versão 1.6 utiliza a distribuição Hadoop baseado em [Hortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium

O Azure HDInsight fornece ofertas de nuvem de macrodados em duas categorias: _padrão_ e _Premium_. A tabela seguinte apresenta uma lista de funcionalidades que estão disponíveis _apenas_ no HDInsight Premium. Funcionalidades que não estão explicitamente descritas na tabela estão disponíveis no HDInsight Standard e Premium.

> [!NOTE]
> O HDInsight Premium oferta está atualmente em pré-visualização e está disponível apenas para os clusters do Linux.

| Funcionalidade do HDInsight Premium | Descrição |
| --- | --- |
| Clusters do HDInsight associados a um domínio |Associação a clusters do HDInsight para domínios do Azure Active Directory (Azure AD) para segurança ao nível da empresa. No HDInsight Premium, pode configurar uma lista de funcionários da sua empresa que pode autenticar através do Azure AD para iniciar sessão para um cluster do HDInsight. O administrador de empresa pode configurar o controlo de acesso baseado em funções de segurança de ramo de registo utilizando [Apache Ranger](http://hortonworks.com/apache/ranger/) e restringir o acesso a dados a utilizar apenas como necessário. Por fim, o administrador pode auditar dados aos quais os funcionários e as alterações para políticas de controlo, aumentando assim alcançar um elevado grau de governação de recursos empresariais de acesso. Para obter mais informações, consulte [configurar clusters do HDInsight associados a um domínio](./domain-joined/apache-domain-joined-configure.md). |

### <a name="cluster-types-supported-in-hdinsight-premium"></a>Tipos de cluster suportados no HDInsight Premium
A tabela seguinte lista os tipos de cluster que são suportados no HDInsight Premium.

| Tipo de cluster | Standard | Premium (pré-visualização) |
| --- | --- | --- |
| Hadoop |Sim |Sim (apenas no HDInsight 3.6) |
| Spark |Sim |Não |
| HBase |Sim |Não |
| Storm |Sim |Não |
| R Server |Sim |Não |
| Consulta interativa |Sim |Não |
| Kafka (pré-visualização) |Sim |Não | 

### <a name="support-for-azure-data-lake-store-in-hdinsight-premium"></a>Suporte para o Azure Data Lake Store no HDInsight Premium

Clusters do HDInsight Premium não suportam a utilização do Azure Data Lake Store como armazenamento principal. No entanto, pode utilizar o Azure Data Lake Store como armazenamento suplementar com clusters do HDInsight Premium.

### <a name="pricing-and-sla"></a>Preços e SLA
Para informações sobre preços e o SLA para o HDInsight Premium, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Predefinição configuração e a máquina virtual tamanhos de nó para clusters
As tabelas seguintes listam os tamanhos de máquina virtual (VM) predefinido de clusters do HDInsight.

> [!IMPORTANT]
> Se precisar de mais do que 32 nós de trabalho num cluster, tem de selecionar um tamanho de nó principal pelo menos 8 núcleos e 14 GB de RAM.
> 
> 

* Regiões todos suportadas exceto sul do Brasil e oeste do Japão:

  | Tipo de cluster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | HEAD: tamanho da VM predefinido |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | HEAD: recomendado tamanhos de VM |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Trabalho: tamanho VM de predefinido |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Trabalho: tamanhos de VM de recomendada |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: tamanho da VM predefinido | |A3 |A2 | | |
  | ZooKeeper: recomendado tamanhos de VM | |A3, A4, A5 |A2, A3, A4 | | |
  | Limite: tamanho da VM predefinição | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Limite: recomendado tamanho da VM | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Sul do Brasil e apenas oeste do Japão (tamanhos de v2):

  | Tipo de cluster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | HEAD: tamanho da VM predefinido |D3 |D3 |A3 |D12 |D12 |
  | HEAD: recomendado tamanhos de VM |D12 D3, D4, |D12 D3, D4, |A3, A4, A5 |D14 D12, D13, |D14 D12, D13, |
  | Trabalho: tamanho VM de predefinido |D3 |D3 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Trabalho: tamanhos de VM de recomendada |D12 D3, D4, |D12 D3, D4, |D12 D3, D4, |Windows: D12, D13, D14; Linux: D4, D14 D12, D13, |Windows: D12, D13, D14; Linux: D4, D14 D12, D13, |
  | ZooKeeper: tamanho da VM predefinido | |A2 |A2 | | |
  | ZooKeeper: recomendado tamanhos de VM | |A2, A3, A4 |A2, A3, A4 | | |
  | Limite: tamanhos de VM de predefinição | | | | |Windows: D12; Linux: D4 |
  | Limite: recomendado tamanhos de VM | | | | |Windows: D12, D13, D14; Linux: D4, D14 D12, D13, |

> [!NOTE]
> - HEAD é conhecido como *Nimbus* o Storm para o tipo de cluster.
> - Trabalho é conhecido como *Supervisor* o Storm para o tipo de cluster.
> - Trabalho é conhecido como *região* para o HBase tipo de cluster.

## <a name="next-steps"></a>Passos seguintes
- [A configuração de cluster de Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Hadoop no HDInsight a partir do Windows PC de trabalho](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

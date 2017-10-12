---
title: Utilizar o Data Lake Store com o Hadoop no Azure HDInsight | Microsoft Docs
description: "Saiba como consultar dados do Azure Data Lake Store e armazenar os resultados da sua análise."
keywords: "armazenamento de blobs, hdfs, dados estruturados, dados não estruturados, data lake store"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: jgao
ms.openlocfilehash: 28a836aff65636ef0031ac63f633d746436d7e4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Utilizar o Data Lake Store com clusters do Azure HDInsight

Para analisar dados num cluster do HDInsight, pode armazenar os dados no [Armazenamento do Azure](../storage/common/storage-introduction.md), no [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) ou em ambos. Ambas opções de armazenamento permitem eliminar em segurança os clusters do HDInsight que são utilizados para o cálculo sem que haja perda de dados do utilizador.

Neste artigo, ficará a saber como o Data Lake Store funciona com clusters do HDInsight. Para saber como o Armazenamento do Azure funciona com clusters do HDInsight, veja [Utilizar o Armazenamento do Azure com clusters do Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter mais informações sobre a criação de um cluster do HDInsight, veja [Create Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Criar clusters do Hadoop no HDInsight).

> [!NOTE]
> O Data Lake Store é sempre acedido através de um canal seguro, pelo que não existe nenhum nome de esquema de sistema de ficheiros `adls`. Utiliza sempre `adl`.
> 
> 

## <a name="availabilities-for-hdinsight-clusters"></a>Disponibilidades para clusters do HDInsight

O Hadoop suporta uma noção do sistema de ficheiros predefinido. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação do cluster do HDInsight, pode especificar um contentor de blobs no Armazenamento do Azure como o sistema de ficheiros predefinido ou, com o HDInsight 3.5 e versões mais recentes, pode selecionar o Armazenamento do Azure ou o Azure Data Lake Store como o sistema de ficheiros predefinido, com algumas exceções. 

Os clusters do HDInsight podem utilizar o Data Lake Store de duas formas:

* Como armazenamento predefinido
* Como armazenamento adicional, com o Azure Storage Blob como armazenamento predefinido.

A partir de agora, apenas alguns tipos/versões de clusters do HDInsight suportam a utilização do Data Lake Store como armazenamento predefinido e contas de armazenamento adicionais:

| Tipo de cluster do HDInsight | Data Lake Store como armazenamento predefinido | Data Lake Store como armazenamento adicional| Notas |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versão 3.6 | Sim | Sim | |
| HDInsight versão 3.5 | Sim | Sim | Com exceção do HBase|
| HDInsight versão 3.4 | Não | Sim | |
| HDInsight versão 3.3 | Não | Não | |
| HDInsight versão 3.2 | Não | Sim | |
| HDInsight Premium (escalão)| Não | Não | |
| Storm | | |Pode utilizar o Data Lake Store para escrever dados de uma topologia Storm. Também pode utilizar o Data Lake Store para dados de referência que podem então ser lidos por uma topologia Storm.|

A utilização do Data Lake Store como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de leitura ou escrita no armazenamento do Azure a partir do cluster.


## <a name="use-data-lake-store-as-default-storage"></a>Utilizar o Data Lake Store como armazenamento predefinido

Quando o HDInsight é implementado com o Data Lake Store como armazenamento predefinido, os ficheiros relacionados com o cluster são armazenados no Data Lake Store, na seguinte localização:

    adl://mydatalakestore/<cluster_root_path>/

em que `<cluster_root_path>` é o nome de uma pasta que cria no Data Lake Store. Ao especificar um caminho de raiz para cada cluster, pode utilizar a mesma conta do Data Lake Store para mais de um cluster. Por isso, pode ter uma configuração em que:

* Cluster1 pode utilizar o caminho `adl://mydatalakestore/cluster1storage`
* Cluster2 pode utilizar o caminho `adl://mydatalakestore/cluster2storage`

Tenha em atenção que ambos os clusters utilizam a mesma conta do Data Lake Store **mydatalakestore**. Cada cluster tem acesso ao seu próprio sistema de ficheiros de raiz no Data Lake Store. A experiência de implementação do portal do Azure em particular pede-lhe que utilize um nome de pasta como **/clusters/\<clustername>** para o caminho da raiz.

Para poder utilizar o Data Lake Store como armazenamento predefinido, tem de conceder ao principal de serviço acesso aos seguintes caminhos:

- A raiz da conta do Data Lake Store.  Por exemplo: adl://mydatalakestore/.
- A pasta com todas as pastas de clusters.  Por exemplo: adl://mydatalakestore/clusters.
- A pasta do cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para obter mais informações sobre como criar o principal de serviço e conceder acesso, veja [Configurar o acesso ao arquivo do Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Utilizar o Data Lake Store como armazenamento adicional

Também pode utilizar o Data Lake Store como armazenamento adicional para o cluster. Nestes casos, o armazenamento predefinido do cluster pode ser o Azure Storage Blob ou uma conta do Data Lake Store. Se estiver a executar tarefas do HDInsight contra aos dados armazenados no Data Lake Store como armazenamento adicional, tem de utilizar o caminho completamente qualificado para os ficheiros. Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Tenha em atenção que agora não existe nenhum **cluster_root_path** no URL. Isso acontece porque, neste caso, o Data Lake Store não é um armazenamento predefinido, pelo que só precisa de fornecer o caminho para os ficheiros.

Para conseguir utilizar o Data Lake Store como armazenamento adicional, só tem de conceder ao principal de serviço acesso aos caminhos onde os ficheiros estão armazenados.  Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para obter mais informações sobre como criar o principal de serviço e conceder acesso, veja [Configurar o acesso ao arquivo do Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Utilizar mais de uma conta do Data Lake Store

A adição de uma conta do Data Lake Store como adicional e a adição de mais do que uma conta do Azure Data Lake Store são efetuadas ao dar ao cluster do HDInsight permissão sobre os dados em mais do que uma conta do Data Lake Store. Veja [Configurar o acesso ao Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configurar o acesso ao arquivo do Data Lake Store

Para configurar o acesso ao arquivo do Data Lake Store a partir do cluster do HDInsight, tem de um ter um principal de serviço do diretório do Azure Active Directory (Azure AD) Apenas um administrador do Azure AD pode criar um principal de serviço. O principal de serviço tem de ser criado com um certificado. Para obter mais informações, veja [Configurar o acesso ao Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#configure-data-lake-store-access) e [Criar um principal de serviço com certificado autoassinado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Se pretende utilizar o Azure Data Lake Store como armazenamento adicional do cluster do HDInsight, é altamente recomendável que o faça ao criar o cluster, conforme descrito neste artigo. A adição do Azure Data Lake Store como armazenamento adicional a um cluster existente do HDInsight é um processo complicado e propenso a erros.
>

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Existem várias formas de aceder aos ficheiros no Data Lake Store a partir de um cluster do HDInsight.

* **Utilizar o nome completamente qualificado**. Com esta abordagem, fornece o caminho completo para o ficheiro ao qual pretende aceder.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Utilizando o formato de caminho abreviado**. Com esta abordagem, substitui o caminho até à raiz do cluster por adl:///. Por isso, no exemplo acima, pode substituir `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` por `adl:///`.

        adl:///<file path>

* **Utilizar o caminho relativo**. Com esta abordagem, fornece apenas o caminho relativo para o ficheiro ao qual pretende aceder. Por exemplo, se o caminho completo para o ficheiro for:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Pode aceder ao mesmo ficheiro sample.log utilizando, em alternativa, este caminho relativo.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Criar clusters do HDInsight com acesso ao Data Lake Store

Utilize as hiperligações seguintes para obter instruções detalhadas sobre como criar clusters do HDInsight com acesso ao Data Lake Store.

* [Utilizar o Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [Utilizar o PowerShell (com o Data Lake Store como armazenamento predefinido)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Utilizar o PowerShell (com o Data Lake Store como armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Utilizar modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar o Azure Data Lake Store compatível com HDFS, através do HDInsight. Isto permite-lhe criar soluções de aquisição de dados para arquivo de longo prazo dimensionáveis e utilizar o HDInsight para aceder às informações contidas nos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]
* [Introdução ao Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Use Hive with HDInsight (Utilizar o Hive com o HDInsight)][hdinsight-use-hive]
* [Use Pig with HDInsight (Utilizar o Pig com o HDInsight)][hdinsight-use-pig]
* [Utilizar Assinaturas de Acesso Partilhado do Storage do Azure para restringir o acesso aos dados com o HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  

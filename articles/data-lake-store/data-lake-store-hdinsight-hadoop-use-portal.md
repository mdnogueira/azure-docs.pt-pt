---
title: Utilizar o portal do Azure para criar clusters do HDInsight de Azure Data Lake Store | Microsoft Docs
description: Utilizar o portal do Azure para criar e utilizar clusters do HDInsight com o Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 4a27ae1402717c91029eda9d635db124f8bb6b8d
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Criar clusters do HDInsight com o Data Lake Store utilizando o portal do Azure
> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilize o PowerShell (para armazenamento de predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilize o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilize o Gestor de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como utilizar o portal do Azure para criar um cluster do HDInsight com uma conta do Azure Data Lake Store, como o armazenamento de predefinido ou um armazenamento adicional. Apesar de armazenamento adicional é opcional para um cluster do HDInsight, recomenda-se para armazenar dados da sua empresa nas contas de armazenamento adicional.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, certifique-se de que cumpriu os seguintes requisitos:

* **Uma subscrição do Azure**. Aceda a [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store**. Siga as instruções de [introdução ao Azure Data Lake Store utilizando o portal do Azure](data-lake-store-get-started-portal.md). Também tem de criar uma pasta raiz na conta.  Neste tutorial, uma pasta de raiz denominada __/clusters__ é utilizado.
* **Um principal de serviço do Azure Active Directory**. Este tutorial fornece instruções sobre como criar um serviço principal no Azure Active Directory (Azure AD). No entanto, para criar um principal de serviço, tem de ser um administrador do Azure AD. Se for um administrador, pode ignorar este pré-requisito e continue com o tutorial.

    >[!NOTE]
    >Pode criar um serviço principal, apenas se for um administrador do Azure AD. O administrador do Azure AD tem de criar um serviço principal antes de poder criar um cluster do HDInsight com o Data Lake Store. Além disso, o principal de serviço tem de ser criado com um certificado, conforme descrito em [criar um principal de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Criar um cluster do HDInsight

Nesta secção, criará um cluster do HDInsight com contas de Data Lake Store, como a predefinição ou o armazenamento adicional. Este artigo foca-se apenas a parte da configuração de contas do Data Lake Store.  Para as informações de criação do cluster geral e procedimentos, consulte [clusters do Hadoop criar no HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Criar um cluster com o Data Lake Store, como armazenamento de predefinido

**Para criar um cluster do HDInsight com uma Data Lake Store, como a conta de armazenamento predefinida**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Siga [criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para as informações gerais sobre a criação de clusters do HDInsight.
3. No **armazenamento** painel, em **tipo de armazenamento primário**, selecione **Data Lake Store**e, em seguida, introduza as seguintes informações:

    ![Adicionar o principal de serviço ao HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "principal de serviço de adicionar ao cluster do HDInsight")

    - **Conta Selecione Data Lake Store**: selecione uma conta existente do Data Lake Store. É necessária uma conta de Data Lake Store existente.  Consulte [pré-requisitos](#prereuisites).
    - **Caminho da raiz**: introduza um caminho de onde os ficheiros de específicas do cluster estão a ser armazenados. Na captura de ecrã, é __/clusters myhdiadlcluster/__, na qual o __/clusters__ pasta tem de existir e o Portal cria *myhdicluster* pasta.  O *myhdicluster* é o nome do cluster.
    - **Acesso de data Lake Store**: configurar o acesso entre a conta do Data Lake Store e o cluster do HDInsight. Para obter instruções, consulte [acesso de configurar o Data Lake Store](#configure-data-lake-store-access).
    - **Contas de armazenamento adicional**: contas de adicionar contas de armazenamento do Azure como armazenamento adicional para o cluster. Adicionar arquivos do Data Lake, é necessário fornecer as permissões de cluster nos dados de mais contas de Data Lake Store ao configurar uma conta de Data Lake Store, como o tipo de armazenamento primário. Veja [Configurar o acesso ao Data Lake Store](#configure-data-lake-store-access).

4. No **acesso de Data Lake Store**, clique em **selecione**e, em seguida, continuar com a criação do cluster, conforme descrito em [clusters do Hadoop criar no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Criar um cluster com o Data Lake Store, como armazenamento adicional

As seguintes instruções criar um cluster do HDInsight com uma conta de armazenamento do Azure como armazenamento de predefinido e uma conta de Data Lake Store, como um armazenamento adicional.
**Para criar um cluster do HDInsight com uma Data Lake Store, como a conta de armazenamento predefinida**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Siga [criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para as informações gerais sobre a criação de clusters do HDInsight.
3. No **armazenamento** painel, em **tipo de armazenamento primário**, selecione **Storage do Azure**e, em seguida, introduza as seguintes informações:

    ![Adicionar o principal de serviço ao HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "principal de serviço de adicionar ao cluster do HDInsight")

    - **Método de seleção**: Utilize uma das seguintes opções:

        * Para especificar uma conta de armazenamento que faz parte da sua subscrição do Azure, selecione **minhas subscrições**e, em seguida, selecione a conta de armazenamento.
        * Para especificar uma conta de armazenamento que está fora da sua subscrição do Azure, selecione **chave de acesso**e, em seguida, forneça as informações da conta do storage externo.

    - **Contentor predefinido**: utilizar o valor predefinido ou especifique o nome da sua própria.

    - Contas de armazenamento adicionais: adicione mais contas de armazenamento do Azure como o armazenamento adicional.
    - Acesso de data Lake Store: configurar o acesso entre a conta do Data Lake Store e o cluster do HDInsight. Para obter instruções, consulte [acesso de configurar o Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configurar o acesso do Data Lake Store 

Nesta secção, configure o acesso de Data Lake Store de clusters do HDInsight através de um principal de serviço do Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Especifique um principal de serviço

No portal do Azure, pode utilizar um principal de serviço existente ou crie um novo.

**Para criar um principal de serviço a partir do portal do Azure**

1. Clique em **acesso de Data Lake Store** a partir do painel de arquivo.
2. No **acesso de Data Lake Store** painel, clique em **criar nova**.
3. Clique em **Principal de serviço**e, em seguida, siga as instruções para criar um principal de serviço.
4. Transfira o certificado, se optar por utilizá-lo novamente no futuro. Transferir o certificado é útil se pretender utilizar o mesmo principal de serviço, quando criar clusters do HDInsight adicionais.

    ![Adicionar o principal de serviço ao HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "principal de serviço de adicionar ao cluster do HDInsight")

4. Clique em **acesso** para configurar o acesso da pasta.  Consulte [configurar permissões de ficheiro](#configure-file-permissions).


**Para utilizar um principal de serviço existente do portal do Azure**

1. Clique em **acesso de Data Lake Store**.
1. No **acesso de Data Lake Store** painel, clique em **utilizar existente**.
2. Clique em **Principal de serviço**e, em seguida, selecione um principal de serviço. 
3. Carregue o certificado (. pfx) que está associada a sua principal de serviço selecionado e, em seguida, introduza a palavra-passe do certificado.

    ![Adicionar o principal de serviço ao HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "principal de serviço de adicionar ao cluster do HDInsight")

4. Clique em **acesso** para configurar o acesso da pasta.  Consulte [configurar permissões de ficheiro](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Configurar permissões de ficheiro

O configura são diferentes dependendo se a conta é utilizada como o armazenamento de predefinido ou uma conta de armazenamento adicional:

- Utilizado como armazenamento de predefinido

    - permissão ao nível da raiz da conta do Data Lake Store
    - permissão ao nível da raiz de armazenamento de cluster de HDInsight. Por exemplo, o __/clusters__ pasta utilizada anteriormente no tutorial.
- Utilizar como um armazenamento adicional

    - Permissão em pastas onde precisa de ficheiro acesso.

**Para atribuir permissão ao nível de raiz da conta do Data Lake Store**

1. No **acesso de Data Lake Store** painel, clique em **acesso**. O **Selecione as permissões do ficheiro** é aberto o painel. Lista todas as contas de Data Lake Store na sua subscrição.
2. Paire o rato (não clique) o rato sobre o nome da conta do Data Lake Store para tornar a caixa de verificação visíveis, em seguida, selecione a caixa de verificação.

    ![Adicionar o principal de serviço ao HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "principal de serviço de adicionar ao cluster do HDInsight")

  Por predefinição, __ler__, __escrever__, AND __executar__ são todos os selecionados.

3. Clique em **selecione** na parte inferior da página.
4. Clique em **executar** para atribuir permissão.
5. Clique em **Concluído**.

**Para atribuir permissão a nível de raiz do cluster de HDInsight**

1. No **acesso de Data Lake Store** painel, clique em **acesso**. O **Selecione as permissões do ficheiro** é aberto o painel. Lista todas as contas de Data Lake Store na sua subscrição.
1. Do **Selecione as permissões do ficheiro** painel, clique no nome do Data Lake Store para mostrar o respetivo conteúdo.
2. Selecione a raiz de armazenamento de cluster do HDInsight, selecionando a caixa de verificação à esquerda da pasta. De acordo com a captura de ecrã anterior, raiz de armazenamento de cluster é __/clusters__ pasta que especificou ao selecionar o Data Lake Store, como armazenamento de predefinido.
3. Defina as permissões na pasta.  Por predefinição, ler, escrever e executar são todos os selecionados.
4. Clique em **selecione** na parte inferior da página.
5. Clique em **Executar**.
6. Clique em **Concluído**.

Se estiver a utilizar o Data Lake Store como armazenamento adicional, tem de atribuir permissão apenas para as pastas que pretende aceder a partir do cluster do HDInsight. Por exemplo, a captura de ecrã abaixo, deve fornecer acesso apenas aos **hdiaddonstorage** pasta numa conta do Data Lake Store.

![Atribua permissões de principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "atribuir permissões de principal de serviço para o cluster do HDInsight")


## <a name="verify-cluster-set-up"></a>Verifique a configuração de cluster

Depois de concluída a configuração de cluster, no painel do cluster, certifique-se os seus resultados efetuando um ou ambos os seguintes passos:

* Para verificar que o armazenamento associado para o cluster é a conta de Data Lake Store que especificou, clique em **contas do Storage** no painel esquerdo.

    ![Adicionar o principal de serviço ao HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "principal de serviço de adicionar ao cluster do HDInsight")

* Para verificar se o principal de serviço está corretamente associado ao cluster do HDInsight, clique em **acesso de Data Lake Store** no painel esquerdo.

    ![Adicionar o principal de serviço ao HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "principal de serviço de adicionar ao cluster do HDInsight")


## <a name="examples"></a>Exemplos

Depois de configurou o armazenamento do cluster com o Data Lake Store, consulte estes exemplos de como utilizar o cluster do HDInsight para analisar os dados armazenados no Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Executar uma consulta do Hive contra dados num arquivo Data Lake (como armazenamento primário)

Para executar uma consulta do Hive, utilize a interface de vistas do Hive no portal do Ambari. Para obter instruções sobre como utilizar as vistas Ambari Hive, consulte [utilizar a vista do Hive com o Hadoop no HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Quando trabalha com dados de uma Data Lake Store, existem alguns cadeias para alterar.

Se utilizar, por exemplo, o cluster que criou com o Data Lake Store, como armazenamento primário, o caminho para os dados é: *adl: / / < data_lake_store_account_name > /azuredatalakestore.net/path/to/file*. Uma consulta do Hive para criar uma tabela a partir dos dados de exemplo que estão armazenados na conta do Data Lake Store aspeto a seguinte instrução:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrições:
* `adl://hdiadlstorage.azuredatalakestore.net/`é a raiz da conta do Data Lake Store.
* `/clusters/myhdiadlcluster`é a raiz dos dados de cluster que especificou ao criar o cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`é a localização do ficheiro de exemplo que utilizou na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Executar uma consulta do Hive contra dados num arquivo Data Lake (como armazenamento adicional)

Se o cluster que criou utiliza armazenamento de BLOBs, como armazenamento de predefinido, os dados de exemplo não estão contidos na conta do Azure Data Lake Store que é utilizada como armazenamento adicional. Nesse caso, transferir primeiro os dados de armazenamento de BLOBs para o Data Lake Store e, em seguida, execute as consultas, conforme mostrado no exemplo anterior.

Para obter informações sobre como copiar dados de armazenamento de BLOBs para uma Data Lake Store, consulte os artigos seguintes:

* [Utilizar o Distcp para copiar dados entre o blobs Storage do Azure e a Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Utilizar AdlCopy para copiar dados de blobs de armazenamento do Azure para o Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Utilizar o Data Lake Store com um cluster do Spark
Pode utilizar um cluster do Spark para executar tarefas de Spark em dados armazenados num Data Lake Store. Para obter mais informações, consulte [cluster utilizar o Spark do HDInsight para analisar os dados no Data Lake Store](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Utilizar o Data Lake Store numa topologia de Storm
Pode utilizar o Data Lake Store para escrever dados a partir de uma topologia do Storm. Para obter instruções sobre como atingir este cenário, consulte [utilização do Azure Data Lake Store com Apache Storm com o HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Consultar também
* [Utilizar o Data Lake Store com clusters do HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Criar um cluster do HDInsight ao utilizar o Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

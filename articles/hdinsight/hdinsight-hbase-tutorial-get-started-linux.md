---
title: "Introdução ao HBase no Azure HDInsight | Microsoft Docs"
description: "Siga este tutorial de HBase para uma introdução ao HBase do Apache com Hadoop no HDInsight. Criar tabelas a partir da shell de HBase e fazer consultas utilizando o Hive."
keywords: apache hbase,hbase,hbase shell,tutorial de hbase
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 21d8dff230e045607b70013f4eabf1bfe8ec3993
ms.lasthandoff: 03/25/2017


---
# <a name="hbase-tutorial-get-started-using-apache-hbase-in-hdinsight"></a>Tutorial de HBase: introdução ao Apache HBase no HDInsight

Saiba como criar um cluster de HBase no HDInsight, criar tabelas de HBase e consultar tabelas utilizando o Hive. Para obter informações gerais do HBase, consulte o artigo [Descrição geral do HBase do HDInsight][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial do HBase, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Requisitos do controlo de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Criar cluster HBase
O procedimento seguinte utiliza um modelo do Azure Resource Manager para criar um cluster HBase baseado em Linux versão 3.4 e a conta do Azure Storage dependente predefinida. Para compreender os parâmetros utilizados no procedimento e outros métodos de criação do cluster, consulte o artigo [Criar clusters do Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir o modelo no portal do Azure. O modelo está localizado num contentor de blob público. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. No painel **Implementação personalizada**, introduza o seguinte:
   
   * **Subscrição**: selecione a sua subscrição do Azure que será utilizada para criar o cluster.
   * **Grupo de recursos**: crie um novo grupo do Azure Resource Management ou utilize um já existente.
   * **Localização**: especifique a localização do grupo de recursos. 
   * **Nome de cluster**: introduza um nome para o cluster HBase que vai criar.
   * **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é **admin**.
   * **Nome de utilizador e palavra-passe SSH**: o nome de utilizador predefinido é **sshuser**.  Pode alterá-lo.
     
     Os outros parâmetros são opcionais.  
     
     Cada cluster tem uma dependência de conta do Armazenamento do Azure. Depois de eliminar um cluster, os dados são mantidos na conta do Storage. O nome da conta do Storage predefinida do cluster é o nome do cluster com "store" anexado. É codificado na secção de variáveis de modelo.
3. Selecione **Concordo com os termos e condições indicados acima** e, em seguida, clique em **Comprar**. A criação de um cluster demora cerca de 20 minutos.

> [!NOTE]
> Depois de eliminar um cluster HBase, pode criar outro cluster HBase utilizando o mesmo contentor de blob predefinido. O novo cluster selecionará as tabelas de HBase criadas por si no cluster original. Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster.
> 
> 

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados
Pode utilizar o SSH para ligar a clusters HBase e, em seguida, utilizar o Shell do HBase para criar tabelas de HBase, inserir dados e consultar dados. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![HDInsight HBase tabular data][img-hbase-sample-data-tabular]

No HBase que é uma implementação do BigTable, os mesmos dados tem este aspeto:

![HDInsight HBase BigTable data][img-hbase-sample-data-bigtable]

Depois de concluir o procedimento a seguir, isto fará mais sentido.  

**Para utilizar a shell de HBase**

1. A partir do SSH, execute o seguinte comando:
   
        hbase shell
2. Crie um HBase com duas famílias de coluna:
   
        create 'Contacts', 'Personal', 'Office'
        list
3. Insira alguns dados:
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![HDInsight Hadoop HBase shell][img-hbase-shell]
4. Obter uma única linha
   
        get 'Contacts', '1000'
   
    Verá os mesmos resultados como quando utiliza o comando de análise porque existe apenas uma linha.
   
    Para mais informações sobre o esquema da tabela HBase, consulte o artigo [Introdução ao Design do Esquema HBase][hbase-schema]. Para obter mais comandos HBase, consulte o artigo [Guia de referência Apache HBase][hbase-quick-start].
5. Sair da shell
   
        exit

**Para efetuar o carregamento em massa de dados para a tabela de contactos HBase**

O HBase inclui vários métodos de carregamento dos dados em tabelas.  Para obter mais informações, consulte o artigo [Carregamento em massa](http://hbase.apache.org/book.html#arch.bulk.load).

Um ficheiro de dados de exemplo foi carregado para um contentor de blobs público, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  O conteúdo do ficheiro de dados é:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Pode criar um ficheiro de texto e carregar o ficheiro para a sua própria conta o Storage, se o desejar. Para instruções, consulte [Carregar dados para trabalhos de Hadoop no HDInsight][hdinsight-upload-data].

> [!NOTE]
> Este procedimento utiliza a tabela de contactos HBase que criou no último procedimento.
> 
> 

1. Do SSH, execute o seguinte comando para transformar o ficheiro de dados em StoreFiles e armazená-lo num caminho relativo especificado por Dimporttsv.bulk.output:.  Se estiver na Shell de HBase, utilize o comando exit para sair.
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
2. Execute o seguinte comando para carregar os dados do /example/data/storeDataFileOutput para a tabela HBase:
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
3. Pode abrir a shell do HBase e utilizar o comando de análise para listar o conteúdo da tabela.

## <a name="use-hive-to-query-hbase"></a>Utilizar Hive para consultar o HBase
Pode consultar dados nas tabelas HBase através do Hive. Esta secção cria uma tabela de Hive que mapeia para a tabela HBase e utiliza-a para consultar os dados na tabela HBase.

> [!NOTE]
> Se o Hive e o HBase estiverem em diferentes clusters no mesmo VNet, deve passar o quórum zookeeper ao invocar a Hive shell:
>
>       hive --hiveconf hbase.zookeeper.quorum=zk0-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk1-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk2-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net --hiveconf zookeeper.znode.parent=/hbase-unsecure  
>
>

1. Abrir **PuTTY** e ligar ao cluster.  Consulte as instruções no procedimento anterior.
2. Abra a shell do Hive.
   
       hive
       
3. Execute o seguinte script de HiveQL para criar uma tabela de Hive que mapeia para a tabela HBase. Certifique-se de que criou a tabela de exemplo referida anteriormente neste tutorial, utilizando a shell de HBase antes de executar esta instrução.
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
4. Execute o seguinte script de HiveQL para consultar os dados na tabela HBase:
   
         SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Utilizar APIs REST de HBase utilizando Curl
> [!NOTE]
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do identificador URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor.
> 
> Para os comandos nesta secção, substitua **USERNAME** pelo utilizador para autenticar-se o cluster e substitua **PASSWORD** pela palavra-passe da conta de utilizador. Substitua **CLUSTERNAME** pelo nome do cluster.
> 
> A API de REST está protegida por [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.
> 
> 

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
   
    Deverá receber uma resposta semelhante ao seguinte:
   
        {"status":"ok","version":"v1"}
   
    Os parâmetros utilizados neste comando são os seguintes:
   
   * **-u** -o nome de utilizador e palavra-passe utilizada para autenticar o pedido.
   * **-G** -indica que se trata de um pedido GET.
2. Utilize o seguinte comando para listar as tabelas HBase existentes:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/
3. Utilize o seguinte comando para criar uma nova tabela HBase com duas famílias de coluna:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v
   
    O esquema é fornecido no formato JSon.
4. Utilize o seguinte comando para inserir alguns dados:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
        -v
   
    Terá de codificar em base64 os valores especificados no comutador -d.  No exemplo:
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: João Dinis
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) permite-lhe inserir múltiplos valores (em lote).
5. Utilize o seguinte comando para obter uma linha:
   
        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Para mais informações sobre o HBase Rest, veja [Guia de Referência do HBase Apache](https://hbase.apache.org/book.html#_rest).

>
> [!NOTE]
> O thrift não é suportado pelo HBase no HDInsight.
>

## <a name="check-cluster-status"></a>Verificar o estado do cluster
O HBase em HDInsight é fornecido com uma interface de utilizador da Web para monitorização de clusters. Utilizando a interface de utilizador da Web, pode pedir estatísticas ou informações sobre regiões.

**Para aceder à IU Principal do HBase**

1. Abrir a IU da Web Ambari em https://&lt;Clustername>.azurehdinsight.net.
2. Clique em **HBase** no menu à esquerda.
3. Clique em **Ligações rápidas** no topo da página, aponte para a ligação de nó ativa do Zookeeper e, em seguida, clique em **IU Principal do HBase**.  A IU é aberta noutro separador do browser:

  ![IU HDInsight HBase HMaster](./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

  A IU Principal do HBase contém as seguintes secções:

  - servidores de região
  - principais cópias de segurança
  - tabelas
  - tarefas
  - atributos de software

## <a name="delete-the-cluster"></a>Eliminar o cluster
Para evitar inconsistências, recomendamos que desative as tabelas do HBase antes de eliminar o cluster.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial do HBase para o HDInsight, aprendeu como criar um cluster HBase e como criar tabelas e ver os dados nessas tabelas a partir da shell HBase. Também aprendeu como utilizar uma consulta Hive nos dados de tabelas de HBase e como utilizar as APIs REST C# para criar uma tabela de HBase e recuperar os dados da tabela.

Para saber mais, consulte:

* [Descrição geral do HBase do HDInsight ][hdinsight-hbase-overview]: O HBase é uma base de dados NoSQL open source do Apache, baseada no Hadoop, que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semi-estruturados.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png


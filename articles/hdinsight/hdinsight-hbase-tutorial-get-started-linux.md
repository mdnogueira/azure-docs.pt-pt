<properties
    pageTitle="Tutorial do HBase: Introdução aos clusters de HBase no Hadoop baseado em Linux | Microsoft Azure"
    description="Siga este tutorial de HBase para uma introdução ao HBase do Apache com Hadoop no HDInsight. Criar tabelas a partir da shell de HBase e fazer consultas utilizando o Hive."
    keywords="apache hbase,hbase,hbase shell,hbase tutorial"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/04/2016"
    ms.author="jgao"/>



# Tutorial de HBase: Introdução ao HBase do Apache com Hadoop baseado em Linux no HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Saiba como criar um cluster de HBase no HDInsight, criar tabelas de HBase e consultar tabelas utilizando o Hive. Para obter informações gerais do HBase, consulte o artigo [Descrição geral do HBase do HDInsight][hdinsight-hbase-overview].

As informações neste documento são específicas para clusters do HDInsight baseados em Linux. Para obter informações sobre clusters baseados no Windows, utilize o seletor de separador no topo da página para alternar.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

###Pré-requisitos

Antes de começar este tutorial do HBase, tem de ter o seguinte:

- **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell(SSU)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [curl](http://curl.haxx.se/download.html).

## Criar cluster HBase

O procedimento a seguir utiliza um modelo de ARM do Azure para criar um cluster HBase. Para compreender os parâmetros utilizados no procedimento e outros métodos de criação do cluster, consulte o artigo [Criar clusters do Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir um modelo ARM no Portal do Azure. O modelo ARM está localizado num contentor de blob público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. No painel **Parâmetros**, introduza o seguinte:

    - **Nome de cluster**: introduza um nome para o cluster HBase que vai criar.
    - **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é **admin**.
    - **Nome de utilizador e palavra-passe SSH**: o nome de utilizador predefinido é **sshuser**.  Pode alterá-lo.
     
    Os outros parâmetros são opcionais.  
    
    Cada cluster tem uma dependência de conta do Blob Storage do Azure. Depois de eliminar um cluster, os dados são mantidos na conta do Storage. O nome da conta do Storage predefinida do cluster é o nome do cluster com "store" anexado. É codificado na secção de variáveis de modelo.
        
3. Clique em **OK** para guardar os parâmetros.
4. No painel **Implementação personalizada**, clique na caixa pendente **Grupo de recursos** e depois clique em **Novo** para criar um novo grupo de recursos.  O grupo de recursos é um contentor que agrupa o cluster, a conta do Storage dependente e outros recursos associados.
5. Clique em **Termos legais** e em **Criar**.
6. Clique em **Criar**. A criação de um cluster demora cerca de 20 minutos.


>[AZURE.NOTE] Depois de eliminar um cluster HBase, pode criar outro cluster HBase utilizando o mesmo contentor de blob predefinido. O novo cluster selecionará as tabelas de HBase criadas por si no cluster original.

## Criar tabelas e inserir dados

Pode utilizar o SSH para ligar a clusters HBase e utilizar o Shell do HBase para criar tabelas de HBase, inserir dados e consultar dados. Para informações sobre como utilizar o SSH do Linux, o Unix, o OS X e o Windows, consulte o artigo [Utilizar o SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) e [Utilizar o SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![hdinsight hbase tabular data][img-hbase-sample-data-tabular]

No HBase que é uma implementação do BigTable, os mesmos dados tem este aspeto:

![hdinsight hbase bigtable data][img-hbase-sample-data-bigtable]

Depois de concluir o procedimento a seguir, isto fará mais sentido.  


**Para utilizar a shell de HBase**

1. A partir do SSH, execute o seguinte comando:

        hbase shell

4. Crie um HBase com duas famílias de coluna:

        create 'Contacts', 'Personal', 'Office'
        list
5. Insira alguns dados:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Obter uma única linha

        get 'Contacts', '1000'

    Verá os mesmos resultados como quando utiliza o comando de análise porque existe apenas uma linha.

    Para mais informações sobre o esquema da tabela HBase, consulte o artigo [Introdução ao Design do Esquema HBase][hbase-schema]. Para obter mais comandos HBase, consulte o artigo [Guia de referência Apache HBase][hbase-quick-start].

6. Sair da shell

        exit

**Para efetuar o carregamento em massa de dados para a tabela de contactos HBase**

O HBase inclui vários métodos de carregamento dos dados em tabelas.  Para obter mais informações, consulte o artigo [Carregamento em massa](http://hbase.apache.org/book.html#arch.bulk.load).


Um ficheiro de dados de exemplo foi carregado para um contentor do blob público, *wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  O conteúdo do ficheiro de dados é:

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

Pode criar um ficheiro de texto e carregar o ficheiro para a sua própria conta o Storage, se o desejar. Para instruções, consulte [Carregar dados para trabalhos de Hadoop no HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Este procedimento utiliza a tabela de contactos HBase que criou no último procedimento.

1. Do SSH, execute o seguinte comando para transformar o ficheiro de dados em StoreFiles e armazená-lo num caminho relativo especificado por Dimporttsv.bulk.output:.  Se estiver na Shell de HBase, utilize o comando exit para sair.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Execute o seguinte comando para carregar os dados do /example/data/storeDataFileOutput para a tabela HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Pode abrir a shell do HBase e utilizar o comando de análise para listar o conteúdo da tabela.



## Utilizar Hive para consultar o HBase

Pode consultar dados nas tabelas HBase através do Hive. Esta secção cria uma tabela de Hive que mapeia para a tabela HBase e utiliza-a para consultar os dados na tabela HBase.

1. Abrir **PuTTY** e ligar ao cluster.  Consulte as instruções no procedimento anterior.
2. Abra a shell do Hive.

       hive
3. Execute o seguinte script de HiveQL para criar uma tabela de Hive que mapeia para a tabela HBase. Certifique-se de que criou a tabela de exemplo referida anteriormente neste tutorial, utilizando a shell de HBase antes de executar esta instrução.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Execute o seguinte script de HiveQL. A consulta de Hive consulta os dados na tabela do HBase:

        SELECT count(*) FROM hbasecontacts;

## Utilizar APIs REST de HBase utilizando Curl

> [AZURE.NOTE] Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do identificador URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor.
>
> Para os comandos nesta secção, substitua **USERNAME** pelo utilizador para autenticar-se o cluster e substitua **PASSWORD** pela palavra-passe da conta de utilizador. Substitua **CLUSTERNAME** pelo nome do cluster.
>
> A API de REST está protegida por [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

        curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Deverá receber uma resposta semelhante ao seguinte:

    {"estado": "ok", "versão": "v1"}

  Os parâmetros utilizados neste comando são os seguintes:

    * **-u** -o nome de utilizador e palavra-passe utilizada para autenticar o pedido.
    * **-G** -indica que se trata de um pedido GET.

2. Utilize o seguinte comando para listar as tabelas HBase existentes:

        curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Utilize o seguinte comando para criar uma nova tabela HBase com duas famílias de coluna:

        curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{\"@name\":\"test\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}"

    O esquema é fornecido no formato JSon.

4. Utilize o seguinte comando para inserir alguns dados:

        curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{\"Row\":{\"key\":\"1000\",\"Cell\":{\"column\":\"Personal:Name\", \"$\":\"John Dole\"}}}"

5. Utilize o seguinte comando para obter uma linha:

        curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## Verificar o estado do cluster

O HBase em HDInsight é fornecido com uma interface de utilizador da Web para monitorização de clusters. Utilizando a interface de utilizador da Web, pode pedir estatísticas ou informações sobre regiões.

É possível utilizar o SSH para criar um túnel entre os pedidos locais, como os pedidos Web, e o cluster do HDInsight. O pedido será encaminhado para o recurso solicitado como se tivesse tido origem no nó principal do cluster do HDInsight. Para obter mais informações, consulte o artigo [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Estabelecer um túnel de sessão SSH**

1. Abra o **PuTTY**.  
2. Se forneceu uma chave SSH quando criou a conta de utilizador durante o processo de criação, tem de executar o passo seguinte para selecionar a chave privada a utilizar durante a autenticação no cluster:

    Em **Categoria**, expanda **Ligação**, expanda **SSH** e selecione **Autenticação**. Por último, clique em **Procurar** e selecione o ficheiro .ppk que contém a chave privada.

3. Em **Categoria**, clique em **Sessão**.
4. Das opções básicas para o ecrã de sessão PuTTY, introduza os seguintes valores:

    - **Nome do Anfitrião**: o endereço SSH do servidor HDInsight no campo nome de anfitrião (ou endereço IP). O endereço SSH é o nome de cluster, em seguida, **-ssh.azurehdinsight.net**. Por exemplo, *mycluster-ssh.azurehdinsight.net*.
    - **Porta**: 22. A porta ssh no nó principal 0 é 22.  
5. Na secção**Categoria** à esquerda da caixa de diálogo, expanda **Ligação**, expanda **SSH** e em seguida, clique em **Túneis**.
6. Forneça as seguintes informações no formulário de Opções de controlo de reencaminhamento da porta SSH:

    - **Porta de origem** - A porta no cliente que pretende reencaminhar. Por exemplo, 9876.
    - **Dinâmico** - Ativa encaminhamento proxy de SOCKS dinâmico.
7. Clique em **Adicionar** para adicionar as definições.
8. Clique em **Abrir** na parte inferior da caixa de diálogo para abrir uma ligação SSH.
9. Quando lhe for solicitado, inicie sessão no servidor com uma conta SSH. Esta ação estabelecerá uma sessão SSH e ativará o túnel.

**Localizar o FQDN do zookeepers utilizando Ambari**

1. Navegue para https://<ClusterName>.azurehdinsight.net/.
2. Introduza as credenciais de conta de utilizador do cluster duas vezes.
3. No menu à esquerda, clique em **zookeeper**.
4. Clique em uma das três ligações de **Servidor ZooKeeper** na lista de Resumo.
5. Copie **Hostname**. Por exemplo, zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Configurar um programa de cliente (Firefox) e verificar o estado do cluster**

1. Abra o Firefox.
2. Clique no botão **Abrir Menu**.
3. Clique em **Opções**.
4. Clique em **Avançado**, clique em **Rede** e em seguida, clique em **Definições**.
5. Selecione **Configuração manual do proxy**.
6. Introduza os seguintes valores:

    - **Host de Socks**: localhost
    - **Porta**: Utilize a mesma porta configurada no túnel SSH do Putty.  Por exemplo, 9876.
    - **SOCKS v5**: (selecionado)
    - **DNS remoto**: (selecionado)
7. Clique em **OK** para guardar as alterações.
8. Navegar para http://<TheFQDN of a ZooKeeper>:60010/master-status

Num cluster de elevada disponibilidade, encontrará uma ligação para o nó mestre HBase atualmente ativo que está a alojar a interface de utilizador na Web.

##Eliminar o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Passos seguintes?
Neste tutorial do HBase para o HDInsight, aprendeu como criar um cluster HBase e como criar tabelas e ver os dados nessas tabelas a partir da shell HBase. Também aprendeu como utilizar uma consulta Hive nos dados de tabelas de HBase e como utilizar as APIs REST C# para criar uma tabela de HBase e recuperar os dados da tabela.

Para saber mais, consulte:

- [Descrição geral do HBase do HDInsight ][hdinsight-hbase-overview]: O HBase é uma base de dados NoSQL open source do Apache, baseada no Hadoop, que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semi-estruturados.


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



<!--HONumber=Jun16_HO2-->



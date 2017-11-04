---
title: Restringir o acesso com assinaturas de acesso partilhado - Azure HDInsight | Microsoft Docs
description: Saiba como utilizar assinaturas de acesso partilhado para restringir o acesso do HDInsight para dados armazenados em blobs de armazenamento do Azure.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2017
ms.author: larryfr
ms.openlocfilehash: 92ad526d034591b8f463ef6b01e115101b74e1ae
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Utilizar assinaturas de acesso partilhado do Azure armazenamento para restringir o acesso aos dados no HDInsight

HDInsight tem acesso total aos dados nas contas de armazenamento do Azure associadas com o cluster. Pode utilizar assinaturas de acesso partilhado no contentor do blob para restringir o acesso aos dados. Por exemplo, para fornecer acesso só de leitura aos dados. Assinaturas de acesso partilhado (SAS) são uma funcionalidade de contas do storage do Azure que permite-lhe limitar o acesso a dados. Por exemplo, que fornece acesso só de leitura aos dados.

> [!IMPORTANT]
> Como uma solução com Apache Ranger, considere utilizar o HDInsight associados a um domínio. Para obter mais informações, consulte o [configurar associados a um domínio HDInsight](./domain-joined/apache-domain-joined-configure.md) documento.

> [!WARNING]
> HDInsight tem de ter acesso total ao armazenamento de predefinido para o cluster.

## <a name="requirements"></a>Requisitos

* Uma subscrição do Azure.
* C# ou Python. Código de exemplo do c# é fornecido como uma solução do Visual Studio.

  * Visual Studio tem de ser versão 2013, 2015 ou 2017
  * Python tem de ser versão 2.7 ou superior

* Um cluster do HDInsight baseado em Linux ou [Azure PowerShell] [ powershell] -se tiver um cluster existente baseado em Linux, pode utilizar Ambari para adicionar uma assinatura de acesso partilhado ao cluster. Caso contrário, pode utilizar o Azure PowerShell para criar um cluster e adicione uma assinatura de acesso partilhado durante a criação do cluster.

    > [!IMPORTANT]
    > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Ficheiros de exemplo da [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Este repositório inclui os seguintes itens:

  * Um projeto do Visual Studio que pode criar um contentor de armazenamento, a política armazenadas e a SAS para utilização com o HDInsight
  * Um script de Python que pode criar um contentor de armazenamento, a política armazenadas e a SAS para utilização com o HDInsight
  * Um script do PowerShell que pode criar um cluster do HDInsight e configurá-lo para utilizar a SAS.

## <a name="shared-access-signatures"></a>Assinaturas de acesso partilhado

Existem duas formas de assinaturas de acesso partilhado:

* Ad hoc: A hora de início, hora de expiração e as permissões para a SAS são todos especificadas no URI de SAS.

* Armazenados a política de acesso: uma política de acesso armazenada está definida no contentor de recursos, tais como um contentor do blob. Uma política pode ser utilizada para gerir restrições para um ou mais assinaturas de acesso partilhado. Quando associa um SAS com uma política de acesso armazenada, a SAS herda as restrições - a hora de início, hora de expiração e permissões - definidas para a política de acesso armazenada.

A diferença entre os dois formulários é importante para um cenário de chave: revogação. Uma SAS é um URL, pelo que qualquer pessoa que obtém a SAS pode utilizá-lo, independentemente de quem o pedido lugar. Se uma SAS é publicada publicamente, pode ser utilizada por qualquer pessoa do mundo. Uma SAS que é distribuída é válida até uma das quatro ações acontece:

1. A hora de expiração especificada na SAS foi atingida.

2. A hora de expiração especificada na política de acesso armazenada referenciada pela SAS foi atingida. Os seguintes cenários de fazer com que a hora de expiração a ser atingido:

    * O intervalo de tempo decorrido.
    * A política de acesso armazenada é modificada para ter um período de tempo de expiração no passado. Alterar a hora de expiração é uma forma de revogar a SAS.

3. A política de acesso armazenada referenciada através da SAS é eliminada, que é outra forma de revogar a SAS. Se recrie a política de acesso armazenada com o mesmo nome, todos os tokens SAS para a política anterior são válidos (se a hora de expiração a SAS não passou). Se pretender revogar a SAS, lembre-se de que utilize um nome diferente se recrie a política de acesso com uma data de expiração futura.

4. A chave de conta que utilizou para criar a SAS é novamente gerada. A regenerar a chave faz com que todas as aplicações que utilizam a chave anterior para falha na autenticação. Atualize todos os componentes para a nova chave.

> [!IMPORTANT]
> Uma assinatura de acesso partilhado URI está associada a chave de conta utilizada para criar a assinatura e o associados armazenados política de acesso (se aplicável). Não se for especificada nenhuma política de acesso armazenada, é a única forma de revogar uma assinatura de acesso partilhado alterar a chave de conta.

Recomendamos que utilize sempre a políticas de acesso armazenada. Ao utilizar políticas armazenadas, pode revogar assinaturas ou expandir a data de expiração, conforme necessário. Os passos nesta utilização de documentos armazenados políticas de acesso para gerar a SAS.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Criar uma política armazenadas e SAS utilizando C\#

1. Abra a solução no Visual Studio.

2. No Explorador de soluções, clique com botão direito no **SASToken** projeto e selecione **propriedades**.

3. Selecione **definições** e adicione valores para as seguintes entradas:

   * StorageConnectionString: A cadeia de ligação para a conta de armazenamento que pretende criar uma política armazenadas e um SAS para. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` onde `myaccount` é o nome da sua conta do storage e `mykey` é a chave para a conta de armazenamento.

   * ContainerName: O contentor na conta de armazenamento que pretende restringir o acesso.

   * SASPolicyName: O nome a utilizar para a política armazenada para criar.

   * FileToUpload: O caminho para um ficheiro que é carregado para o contentor.

4. Execute o projeto. Depois da SAS foi gerada, são apresentadas informações semelhantes para o seguinte texto:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Guarde o token de política SAS, o nome da conta de armazenamento e o nome de contentor. Estes valores são utilizados quando associar a conta de armazenamento de cluster do HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Criar uma política armazenadas e um SAS com o Python

1. Abra o ficheiro de SASToken.py e alterar os valores seguintes:

   * política\_nome: O nome a utilizar para a política armazenada para criar.

   * armazenamento\_conta\_nome: O nome da sua conta de armazenamento.

   * armazenamento\_conta\_chave: A chave da conta do storage.

   * armazenamento\_contentor\_nome: O contentor na conta de armazenamento que pretende restringir o acesso.

   * exemplo\_ficheiro\_caminho: O caminho para um ficheiro que é carregado para o contentor.

2. Execute o script. O token SAS semelhante para o seguinte texto é apresentado quando a conclusão do script:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Guarde o token de política SAS, o nome da conta de armazenamento e o nome de contentor. Estes valores são utilizados quando associar a conta de armazenamento de cluster do HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Utilizar o SAS com o HDInsight

Quando criar um cluster do HDInsight, tem de especificar uma conta de armazenamento primário e, opcionalmente, pode especificar contas de armazenamento adicional. Ambos estes métodos de adição de armazenamento requerem acesso total para as contas de armazenamento e contentores que são utilizados.

Para utilizar uma assinatura de acesso partilhado para limitar o acesso a um contentor, adicione uma entrada de personalizado para o **core-site** configuração para o cluster.

* Para **baseados em Windows** ou **baseado em Linux** clusters do HDInsight, pode adicionar a entrada durante a criação do cluster com o PowerShell.
* Para **baseado em Linux** clusters do HDInsight, alterar a configuração após a criação do cluster com o Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Criar um cluster que utiliza a SAS

Um exemplo de como criar um cluster do HDInsight que utiliza a SAS está incluído no `CreateCluster` diretório do repositório. Para utilizá-lo, utilize os seguintes passos:

1. Abra o `CreateCluster\HDInsightSAS.ps1` ficheiro num editor de texto e modificar os seguintes valores no início do documento.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Por exemplo, altere `'mycluster'` com o nome do cluster que pretende criar. Os valores SAS devem correspondem aos valores dos passos anteriores, quando criar uma conta de armazenamento e um SAS token.

    Assim que tiver alterado os valores, guarde o ficheiro.

2. Abra uma linha nova do PowerShell do Azure. Se estiver familiarizado com o Azure PowerShell, ou não tiver instalado, consulte [instalar e configurar o Azure PowerShell][powershell].

1. A partir da linha de comandos, utilize o seguinte comando para autenticar a sua subscrição do Azure:

    ```powershell
    Login-AzureRmAccount
    ```

    Quando lhe for solicitado, inicie sessão com a conta para a sua subscrição do Azure.

    Se a sua conta está associada a várias subscrições do Azure, poderá ter de utilizar `Select-AzureRmSubscription` para selecionar a subscrição que pretende utilizar.

4. Na linha de comandos, altere os diretórios para o `CreateCluster` diretório que contém o ficheiro HDInsightSAS.ps1. Em seguida, utilize o seguinte comando para executar o script

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Como o script é executado, regista saída para a linha de comandos do PowerShell à medida que cria o recurso de contas de armazenamento e de grupo. Lhe for pedido que introduza o utilizador HTTP para o cluster do HDInsight. Esta conta é utilizada para proteger o acesso HTTP/s para o cluster.

    Se estiver a criar um cluster baseado em Linux, lhe for pedido um nome de conta de utilizador SSH e a palavra-passe. Esta conta é utilizada para iniciar sessão remotamente no cluster.

   > [!IMPORTANT]
   > Quando lhe for pedido para HTTP/s ou SSH nome de utilizador e palavra-passe, tem de fornecer uma palavra-passe que cumpra os seguintes critérios:
   >
   > * Tem de ser no mínimo 10 carateres de comprimento
   > * Tem de conter pelo menos um dígito
   > * Tem de conter pelo menos um caráter não alfanumérico
   > * Tem de conter, pelo menos, um em maiúsculas ou minúsculas

Demora tempo para este script concluir, normalmente, cerca de 15 minutos. O script estiver concluído sem erros, o cluster foi criado.

### <a name="use-the-sas-with-an-existing-cluster"></a>Utilizar o SAS com um cluster existente

Se tiver um cluster existente baseado em Linux, pode adicionar a SAS para o **core-site** configuração utilizando os seguintes passos:

1. Abra o web do Ambari da IU para o cluster. O endereço para esta página é https://YOURCLUSTERNAME.azurehdinsight.net. Quando lhe for pedido, autenticar para o cluster utilizando o nome de administrador (administrador) e palavra-passe que utilizou quando criar o cluster.

2. No lado esquerdo da IU da web do Ambari, selecione **HDFS** e, em seguida, selecione o **folhas** separador no meio da página.

3. Selecione o **avançadas** separador e, em seguida, desloque-se até encontrar o **site core personalizado** secção.

4. Expanda o **site core personalizado** secção, em seguida, desloque-se para o fim e selecione o **Adicionar propriedade de...**  ligação. Utilize os seguintes valores para o **chave** e **valor** campos:

   * **Chave**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Valor**: O SAS devolvido pela aplicação c# ou Python que executou anteriormente

     Substitua **CONTAINERNAME** com o nome do contentor utilizado com a aplicação c# ou SAS. Substitua **STORAGEACCOUNTNAME** com o nome de conta de armazenamento que utilizou.

5. Clique em de **adicionar** botão Guardar esta chave e valor, em seguida, clique em de **guardar** botão para guardar as alterações de configuração. Quando lhe for pedido, adicione uma descrição da alteração ("adicionar acesso de armazenamento SAS" por exemplo) e, em seguida, clique em **guardar**.

    Clique em **OK** quando as alterações foram concluídas.

   > [!IMPORTANT]
   > Tem de reiniciar vários serviços antes da alteração surta efeito.

6. No Ambari IU da web, selecione **HDFS** na lista à esquerda e, em seguida, selecione **todas reinicie as** do **ações de serviço** lista à direita pendente. Quando lhe for pedido, selecione **ativar o modo de manutenção** e, em seguida, selecione __Conform todas reinicie ".

    Repita este processo para MapReduce2 e YARN.

7. Assim que os serviços de ser reiniciado, selecione cada um deles e desativar o modo de manutenção desde o **serviço ações** pendente.

## <a name="test-restricted-access"></a>Testar o acesso restrito

Para verificar que tenham acesso restrito, utilize os seguintes métodos:

* Para **baseados em Windows** clusters do HDInsight, utilize o ambiente de trabalho remoto para ligar ao cluster. Para obter mais informações, consulte [ligar ao HDInsight através de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Assim que estiver ligado, utilize o **Hadoop da linha de comandos** ícone no ambiente de trabalho para abrir uma linha de comandos.

* Para **baseado em Linux** clusters do HDInsight, utilizar o SSH para ligar ao cluster. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Assim que estiver ligado ao cluster, utilize os seguintes passos para verificar que apenas leitura e lista os itens na conta de armazenamento SAS, pode:

1. Para listar o conteúdo do contentor, utilize o comando seguinte a partir da linha de comandos: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Substitua **SASCONTAINER** com o nome do contentor criado para a conta de armazenamento SAS. Substitua **SASACCOUNTNAME** com o nome da conta de armazenamento utilizado para a SAS.

    A lista inclui o ficheiro carregado quando o contentor e SAS foram criado.

2. Utilize o seguinte comando para verificar que podem ler o conteúdo do ficheiro. Substitua o **SASCONTAINER** e **SASACCOUNTNAME** como no passo anterior. Substitua **FILENAME** com o nome do ficheiro apresentado no comando anterior:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Este comando lista os conteúdos do ficheiro.

3. Utilize o seguinte comando para transferir o ficheiro para o sistema de ficheiros locais:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Este comando transfere o ficheiro para um ficheiro local com o nome **testfile.txt**.

4. Utilize o seguinte comando para carregar o ficheiro local para um novo ficheiro designado **testupload.txt** no armazenamento SAS:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Receberá uma mensagem semelhante ao seguinte texto:

        put: java.io.IOException

    Este erro ocorre porque a localização de armazenamento é leitura + lista apenas. Utilize o seguinte comando para colocar os dados no armazenamento de predefinido para o cluster, o que é gravável:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Neste momento, a operação deve ser concluído com êxito.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="a-task-was-canceled"></a>Uma tarefa foi cancelada

**Sintomas**: ao criar um cluster utilizando o script do PowerShell, poderá receber a seguinte mensagem de erro:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Causa**: Este erro pode ocorrer se utilizar uma palavra-passe para o utilizador de admin/HTTP para o cluster ou (para clusters baseados em Linux) o utilizador SSH.

**Resolução**: utilizar uma palavra-passe que cumpra os seguintes critérios:

* Tem de ser no mínimo 10 carateres de comprimento
* Tem de conter pelo menos um dígito
* Tem de conter pelo menos um caráter não alfanumérico
* Tem de conter, pelo menos, um em maiúsculas ou minúsculas

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como adicionar armazenamento de acesso limitado ao cluster do HDInsight, saiba outras formas de trabalhar com dados no seu cluster:

* [Utilizar o Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs

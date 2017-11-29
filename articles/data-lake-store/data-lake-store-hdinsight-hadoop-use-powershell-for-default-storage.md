---
title: Criar clusters do HDInsight com o Data Lake Store como armazenamento de predefinido utilizando o PowerShell | Microsoft Docs
description: Utilizar o Azure PowerShell para criar e utilizar clusters do HDInsight com o Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 2f1793c2de2b68a8b155ada73044c6bc36882612
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Criar clusters do HDInsight com o Data Lake Store como armazenamento de predefinido utilizando o PowerShell
> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilize o PowerShell (para armazenamento de predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilize o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilize o Gestor de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Saiba como utilizar o Azure PowerShell para configurar o Azure HDInsight clusters com o Azure Data Lake Store, como armazenamento de predefinido. Para obter instruções sobre como criar um cluster do HDInsight com o Data Lake Store, como armazenamento adicional, consulte [criar um cluster do HDInsight com o Data Lake Store, como armazenamento adicional](data-lake-store-hdinsight-hadoop-use-powershell.md).

Seguem-se algumas considerações importantes para utilizar o HDInsight com o Data Lake Store:

* A opção para criar clusters do HDInsight com acesso ao Data Lake Store, como armazenamento de predefinição está disponível para o HDInsight versão 3.5 e 3.6.

* A opção para criar HDInsight clusters com acesso ao Data Lake Store, como o armazenamento de predefinido é *não está disponível* para clusters do HDInsight Premium.

Para configurar o HDInsight para trabalhar com o Data Lake Store utilizando o PowerShell, siga as instruções nas secções junto cinco.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, certifique-se de que cumpre os seguintes requisitos:

* **Uma subscrição do Azure**: aceda a [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
* **O Azure PowerShell 1.0 ou superior**: consulte [como instalar e configurar o PowerShell](/powershell/azure/overview).
* **Software Development Kit (SDK) do Windows**: para instalar o Windows SDK, visite [transfere e ferramentas para Windows 10](https://dev.windows.com/en-us/downloads). O SDK é utilizado para criar um certificado de segurança.
* **Principal de serviço do Azure Active Directory**: Este tutorial descreve como criar um principal de serviço no Azure Active Directory (Azure AD). No entanto, para criar um principal de serviço, tem de ser um administrador do Azure AD. Se for um administrador, pode ignorar este pré-requisito e continue com o tutorial.

    >[!NOTE]
    >Pode criar um serviço principal, apenas se for um administrador do Azure AD. O administrador do Azure AD tem de criar um serviço principal antes de poder criar um cluster do HDInsight com o Data Lake Store. O principal de serviço tem de ser criado com um certificado, conforme descrito em [criar um principal de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Criar uma conta do Data Lake Store
Para criar uma conta de Data Lake Store, efetue o seguinte:

1. A partir do ambiente de trabalho, abra uma janela do PowerShell e, em seguida, introduza os fragmentos abaixo. Quando lhe for pedido para iniciar sessão, inicie sessão como um dos administradores da subscrição ou proprietários. 

        # Sign in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Se registar o fornecedor de recursos do Data Lake Store e receber um erro semelhante a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, a subscrição poderá não estar na lista de permissões para o Data Lake Store. Para ativar a sua subscrição do Azure para a pré-visualização pública do Data Lake Store, siga as instruções em [introdução ao Azure Data Lake Store utilizando o portal do Azure](data-lake-store-get-started-portal.md).
    >

2. Conta do Data Lake Store está associada um grupo de recursos do Azure. Comece por criar um grupo de recursos.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Deverá ver um resultado como esta:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Crie uma conta de Data Lake Store. O nome da conta que especificar tem de conter apenas letras minúsculas e números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    Deve ver um resultado como o seguinte:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

4. Utilizar o Data Lake Store como armazenamento de predefinido requer que especifique um caminho de raiz ao qual os ficheiros de cluster específicos são copiados durante a criação do cluster. Para criar um caminho de raiz, o que é **/clusters/hdiadlcluster** no fragmento, utilize os seguintes cmdlets:

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurar a autenticação de acesso baseado em funções para o Data Lake Store
Cada subscrição do Azure está associada uma entidade do Azure AD. Utilizadores e serviços que acedem aos recursos de subscrição utilizando o portal do Azure ou a API do Azure Resource Manager, devem primeiro autenticar com o Azure AD. É concedido acesso aos serviços e subscrições do Azure ao lhes atribuir a função adequada num recurso do Azure. Para os serviços, um principal de serviço identifica o serviço no Azure AD.

Esta secção ilustra como conceder a um serviço de aplicações, tais como o HDInsight, acesso a um recurso do Azure (a conta de Data Lake Store que criou anteriormente). Fazê-lo através da criação de um serviço principal para as aplicações e funções de atribuição ao mesmo através do PowerShell.

Para configurar a autenticação do Active Directory para o Azure Data Lake, execute as tarefas em duas secções seguintes.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Certifique-se de que tem [Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de continuar com os passos nesta secção. Tem de ter também criou um diretório, tais como *C:\mycertdir*, onde criar o certificado.

1. Na janela do PowerShell, vá para a localização onde instalou o SDK do Windows (normalmente, *C:\Program Files (x86) \Windows Kits\10\bin\x86*) e utilizar o [MakeCert] [ makecert] utilitário para criar um certificado autoassinado e uma chave privada. Utilize os seguintes comandos:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Será solicitado para introduzir a palavra-passe da chave privada. Depois do comando for executado com êxito, deverá ver **CertFile.cer** e **mykey.pvk** no diretório de certificado que especificou.
2. Utilize o [Pvk2Pfx] [ pvk2pfx] utilitário para converter os ficheiros de .pvk e. cer que MakeCert criado para um ficheiro. pfx. Execute o seguinte comando:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando lhe for pedido, introduza a chave palavra-passe privada que especificou anteriormente. O valor especificado para o **-po** parâmetro é a palavra-passe que foi associada ao ficheiro. pfx. Depois do comando foi concluído com êxito, também deverá ver uma **CertFile.pfx** no diretório de certificado que especificou.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Criar um Azure AD e um principal de serviço
Nesta secção, criar um principal de serviço para uma aplicação do Azure AD, atribuir uma função para o principal de serviço e efetue a autenticação como o principal de serviço ao fornecer um certificado. Para criar uma aplicação no Azure AD, execute os seguintes comandos:

1. Cole os cmdlets seguintes na janela de consola do PowerShell. Certifique-se de que o valor especificado para o **- DisplayName** propriedade é exclusiva. Os valores para **- home page** e **- IdentiferUris** são valores de marcador de posição e não são verificadas.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Criar um principal de serviço com o ID da aplicação.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Conceda o acesso de principal de serviço para a raiz do Data Lake Store e todas as pastas no caminho da raiz que especificou anteriormente. Utilize os seguintes cmdlets:

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Criar um cluster do HDInsight com Linux com o Data Lake Store, como o armazenamento de predefinido

Nesta secção, vai criar um cluster do HDInsight Hadoop Linux com o Data Lake Store como armazenamento de predefinido. Nesta versão, o cluster do HDInsight e a Data Lake Store tem de ser na mesma localização.

1. Obter o ID de inquilino da subscrição e armazene-a para utilizar mais tarde.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Crie o cluster do HDInsight utilizando os seguintes cmdlets:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    O cmdlet tenha sido concluída com êxito, deverá ver um resultado que lista os detalhes do cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Executar tarefas de teste no cluster do HDInsight ao utilizar o Data Lake Store
Depois de configurar um cluster do HDInsight, pode executar tarefas de teste no mesmo para se certificar de que se pode aceder ao Data Lake Store. Para tal, execute uma tarefa do Hive de exemplo para criar uma tabela que utiliza os dados de exemplo que já se encontra disponíveis no Data Lake Store em  *<cluster root>/example/data/sample.log*.

Nesta secção, efetuar uma ligação Secure Shell (SSH) para o cluster do HDInsight com Linux que criou e, em seguida, executar uma consulta do Hive de exemplo.

* Se estiver a utilizar um cliente Windows ao estabelecer uma ligação SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente Linux para estabelecer uma ligação SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Depois de efetuar a ligação, inicie a interface de linha de comandos (CLI) de ramo de registo utilizando o seguinte comando:

        hive
2. Utilizar a CLI para introduzir as seguintes instruções para criar uma nova tabela com o nome **veículos** utilizando os dados de exemplo no Data Lake Store:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Deverá ver o resultado de consulta na consola do SSH.

    >[!NOTE]
    >O caminho para os dados de exemplo no comando CREATE TABLE anterior é `adl:///example/data/`, onde `adl:///` é a raiz do cluster. O exemplo do cluster de raiz que é especificado neste tutorial, é o comando a seguir `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Pode utilizar a alternativa mais curta ou forneça o caminho completo para a raiz do cluster.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Aceder ao Data Lake Store utilizando comandos HDFS
Depois de ter configurado o cluster do HDInsight ao utilizar o Data Lake Store, pode utilizar comandos da shell distribuídas ficheiro sistema Hadoop (HDFS) para aceder ao arquivo.

Nesta secção, efetuar uma ligação SSH para o cluster do HDInsight com Linux que criou e, em seguida, executar os comandos HDFS.

* Se estiver a utilizar um cliente Windows ao estabelecer uma ligação SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente Linux para estabelecer uma ligação SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de tiver tomado a ligação, liste os ficheiros no Data Lake Store utilizando o seguinte comando do sistema de ficheiros do HDFS.

    hdfs dfs -ls adl:///

Também pode utilizar o `hdfs dfs -put` comando para carregar alguns ficheiros para o Data Lake Store e, em seguida, utilize `hdfs dfs -ls` para verificar se os ficheiros foram carregados com êxito.

## <a name="see-also"></a>Consultar também
* [Utilizar o Data Lake Store com clusters do HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal do Azure: criar um cluster do HDInsight ao utilizar o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

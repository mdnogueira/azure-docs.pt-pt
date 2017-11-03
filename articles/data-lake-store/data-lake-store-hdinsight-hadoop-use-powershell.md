---
title: 'PowerShell: Cluster do HDInsight do Azure como armazenamento suplementar do Data Lake Store | Microsoft Docs'
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 0e2e0d02677013252b0806558535fe970b5c50b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-additional-storage"></a>Utilizar o Azure PowerShell para criar um cluster do HDInsight com o Data Lake Store (como armazenamento adicional)
> [!div class="op_single_selector"]
> * [Utilizar o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Através do PowerShell (para armazenamento de predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilizar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Com o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como utilizar o Azure PowerShell para configurar um cluster do HDInsight com o Azure Data Lake Store, **como armazenamento adicional**. Para obter instruções sobre como criar um cluster do HDInsight com o Azure Data Lake Store, como armazenamento de predefinido, consulte [criar um cluster do HDInsight com o Data Lake Store, como armazenamento de predefinido](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Se pretende utilizar o Azure Data Lake Store como armazenamento adicional do cluster do HDInsight, é altamente recomendável que o faça ao criar o cluster, conforme descrito neste artigo. A adição do Azure Data Lake Store como armazenamento adicional a um cluster existente do HDInsight é um processo complicado e propenso a erros.
>

Para tipos de cluster suportadas, a Data Lake Store pode ser utilizado como um armazenamento de predefinido ou uma conta de armazenamento adicional. Quando o Data Lake Store é utilizado como armazenamento adicional, a conta do storage predefinida para os clusters continuarão a estar Blobs de armazenamento do Azure (WASB) e os ficheiros relacionados com o cluster (por exemplo, os registos, etc.) ainda são escritos para o armazenamento de predefinido, enquanto os dados que pretende processar podem ser armazenados numa conta do Data Lake Store. Utilizar o Data Lake Store como uma conta de armazenamento adicional não impacto no desempenho ou a capacidade de leitura/escrita para o armazenamento do cluster.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Utilizar o Data Lake Store para armazenamento de cluster do HDInsight

Seguem-se algumas considerações importantes para utilizar o HDInsight com o Data Lake Store:

* Opção para criar clusters do HDInsight com acesso ao Data Lake Store, como é disponível para as versões do HDInsight 3.2, 3.4, 3.5 e 3.6 armazenamento adicional.

Configurar o HDInsight para trabalhar com o Data Lake Store através do PowerShell envolvem os seguintes passos:

* Criar um Azure Data Lake Store
* Configurar a autenticação de acesso baseado em funções para o Data Lake Store
* Criar cluster do HDInsight com a autenticação para o Data Lake Store
* Executar uma tarefa de teste em cluster

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* **O Windows SDK**. Pode instalar a [aqui](https://dev.windows.com/en-us/downloads). Utilize esta para criar um certificado de segurança.
* **Principal de serviço de diretório do Azure Active Directory**. Os passos neste tutorial fornecem instruções sobre como criar um principal de serviço no Azure AD. No entanto, tem de ser um administrador do Azure AD para poder criar um principal de serviço. Se for um administrador do Azure AD, pode ignorar este pré-requisito e continue com o tutorial.

    **Se não for um administrador do Azure AD**, não será capaz de executar os passos necessários para criar um principal de serviço. Nesse caso, o administrador do Azure AD tem primeiro de criar um principal de serviço antes de poder criar um cluster do HDInsight com o Data Lake Store. Além disso, o principal de serviço tem de ser criado utilizando um certificado, conforme descrito em [criar um principal de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-azure-data-lake-store"></a>Criar um Azure Data Lake Store
Siga estes passos para criar um Data Lake Store.

1. A partir do ambiente de trabalho, abra uma nova janela do PowerShell do Azure e, introduza o fragmento seguinte. Quando lhe for pedido para iniciar sessão, certifique-se de que inicie sessão como um do administrador/proprietário da subscrição:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Se receber um erro semelhante `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` ao registar o fornecedor de recursos do Data Lake Store, é possível que a sua subscrição não está na lista de permissões para o Azure Data Lake Store. Certifique-se de ativar a sua subscrição do Azure para pré-visualização pública do Data Lake Store, seguindo estes [instruções](data-lake-store-get-started-portal.md).
   >
   >
2. Uma conta do Azure Data Lake Store está associada a um Grupo de Recursos do Azure. Comece por criar um Grupo de Recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Deverá ver um resultado como esta:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Crie uma conta do Azure Data Lake Store. O nome da conta que especificar só pode conter letras minúsculas e números.

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

5. Carregar alguns dados de exemplo para o Azure Data Lake. Utilizaremos esta neste artigo para verificar se os dados estão acessíveis a partir de um cluster do HDInsight. Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurar a autenticação de acesso baseado em funções para o Data Lake Store
Cada subscrição do Azure está associada um Azure Active Directory. Utilizadores e serviços que acedem aos recursos da subscrição utilizando o Portal clássico do Azure ou a API do Azure Resource Manager, devem primeiro autenticar com o diretório do Azure Active Directory. É concedido acesso aos serviços e subscrições do Azure ao lhes atribuir a função adequada num recurso do Azure.  Para os serviços, um principal de serviço identifica o serviço no Azure Active Directory (AAD). Esta secção ilustra como conceder a um serviço de aplicações, como o HDInsight, acesso a um recurso do Azure (a conta do Azure Data Lake Store que criou anteriormente), criando um principal de serviço para a aplicação e atribuir funções a que através do PowerShell do Azure.

Para configurar a autenticação do Active Directory para o Azure Data Lake, tem de efetuar as seguintes tarefas.

* Criar um certificado autoassinado
* Criar uma aplicação no Azure Active Directory e um Principal de serviço

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Certifique-se de que tem [Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de continuar com os passos nesta secção. Tem de ter também criou um diretório, tais como **C:\mycertdir**, onde será criado o certificado.

1. A janela do PowerShell, navegue para a localização onde instalou o SDK do Windows (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86` e utilizar o [MakeCert] [ makecert] utilitário para criar um certificado autoassinado e uma chave privada. Utilize os seguintes comandos.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Será solicitado para introduzir a palavra-passe da chave privada. Depois do comando executado com êxito, deverá ver uma **CertFile.cer** e **mykey.pvk** no diretório de certificado que especificou.
2. Utilize o [Pvk2Pfx] [ pvk2pfx] utilitário para converter os ficheiros de .pvk e. cer que MakeCert criado para um ficheiro. pfx. Execute o seguinte comando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando lhe for pedido introduza a palavra-passe chave privada que especificou anteriormente. O valor especificado para o **-po** parâmetro é a palavra-passe que estão associada com o ficheiro. pfx. Depois do comando for concluído com êxito, deve também verá um CertFile.pfx no diretório de certificado que especificou.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Criar um Azure Active Directory e um principal de serviço
Nesta secção, execute os passos para criar um serviço principal para uma aplicação do Azure Active Directory, atribuir uma função para o principal de serviço e efetue a autenticação como o principal de serviço ao fornecer um certificado. Execute os seguintes comandos para criar uma aplicação no Azure Active Directory.

1. Cole os cmdlets seguintes na janela de consola do PowerShell. Certifique-se de que o valor especificado para o **- DisplayName** propriedade é exclusiva. Além disso, os valores para **- home page** e **- IdentiferUris** são valores de marcador de posição e não são verificadas.

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
3. Conceda o acesso de principal de serviço para a pasta do Data Lake Store e o ficheiro que terão acesso a partir do cluster do HDInsight. O fragmento abaixo fornece acesso para a raiz da conta do Data Lake Store (onde copiou o ficheiro de dados de exemplo) e o próprio ficheiro.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-additional-storage"></a>Criar um cluster do HDInsight com Linux com o Data Lake Store, como armazenamento adicional

Nesta secção, vamos criar um cluster do HDInsight Hadoop Linux com o Data Lake Store como armazenamento adicional. Nesta versão, o cluster do HDInsight e a Data Lake Store tem de ser na mesma localização.

1. Começar a utilizar ao obter o ID de inquilino da subscrição. Irá precisar que mais tarde.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. Nesta versão, para um cluster de Hadoop, arquivo Data Lake só pode ser utilizado como um armazenamento adicional para o cluster. Armazenamento de predefinido continuarão a estar os blobs de armazenamento do Azure (WASB). Por isso, vamos primeiro criar a conta de armazenamento e contentores de armazenamento necessários para o cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext
3. Crie o cluster do HDInsight. Utilize os seguintes cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Depois do cmdlet concluir com êxito, deverá ver uma saída listar os detalhes do cluster.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Executar tarefas de teste no cluster do HDInsight ao utilizar o Data Lake Store
Depois de configurar um cluster do HDInsight, pode executar tarefas de teste no cluster para testar que o cluster do HDInsight pode aceder ao Data Lake Store. Para tal, iremos executar uma tarefa do Hive de exemplo que cria uma tabela utilizando os dados de exemplo que carregou anteriormente para o Data Lake Store.

Nesta secção, irá SSH para o cluster do HDInsight com Linux criado e execute a uma consulta do Hive de exemplo.

* Se estiver a utilizar um cliente Windows SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente Linux SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Assim que estiver ligado, inicie a CLI de ramo de registo utilizando o seguinte comando:

        hive
2. Com a CLI do AZURE, introduza as seguintes instruções para criar uma nova tabela com o nome **veículos** utilizando os dados de exemplo no Data Lake Store:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Deverá ver um resultado semelhante ao seguinte:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

## <a name="access-data-lake-store-using-hdfs-commands"></a>Acesso Data Lake Store utilizando comandos HDFS
Assim que tiver configurado o cluster do HDInsight ao utilizar o Data Lake Store, pode utilizar os comandos da shell HDFS para aceder ao arquivo.

Nesta secção, irá SSH para o cluster do HDInsight com Linux criado e execute os comandos HDFS.

* Se estiver a utilizar um cliente Windows SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente Linux SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Assim que estiver ligado, utilize o seguinte comando do sistema de ficheiros HDFS para listar os ficheiros no Data Lake Store.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Isto deve listar o ficheiro que carregou anteriormente para o Data Lake Store.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Também pode utilizar o `hdfs dfs -put` comando para carregar alguns ficheiros para o Data Lake Store e, em seguida, utilize `hdfs dfs -ls` para verificar se os ficheiros foram carregados com êxito.

## <a name="see-also"></a>Veja Também
* [Portal: Criar um cluster do HDInsight ao utilizar o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

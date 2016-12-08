---
title: "Começar a utilizar o Azure Data Lake Analytics com Python| Microsoft Docs"
description: 'Saiba como utilizar Python para criar uma conta do Data Lake Store e submeter trabalhos. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>Tutorial: introdução ao Azure Data Lake Analytics com Python
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar Python para criar contas do Azure Data Lake Analytics, definir os trabalhos do Data Lake Analytics em [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter os trabalhos para contas do Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

Neste tutorial, vai desenvolver um trabalho que lê um ficheiro de valores separados por tabulações (TSV) e converte-o num ficheiro de valores separados por vírgulas (CSV). Para seguir o mesmo tutorial, utilizando outras ferramentas suportadas, clique nos separadores na parte superior desta secção.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

- **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Uma Aplicação do Azure Active Directory**. A aplicação do Azure AD é utilizada para autenticar a aplicação do Data Lake Store no Azure AD. Existem abordagens diferentes para a autenticação no Azure AD, que são a autenticação do utilizador final ou a autenticação de serviço para serviço. Para obter instruções e mais informações sobre a forma como autenticar, veja [Authenticate with Data Lake Store using Azure Active Directory (Autenticar no Data Lake Store com o Azure Active Directory)](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).
- **[Python](https://www.python.org/downloads/)**. Tem de utilizar a versão de 64 bits. Este artigo utiliza a versão 3.5.2 de Python


## <a name="install-azure-python-sdk"></a>Instalar o SDK de Python para o Azure

Para trabalhar no Data Lake Store com Python, tem de instalar três módulos.

O módulo “azure-mgmt-datalake-store” inclui as operações de gestão de conta do Azure Data Lake Store. O módulo azure-mgmt-resource inclui outros módulos Azure para o Active Directory, etc. O módulo “azure-datalake-store” inclui as operações do sistema de ficheiros do Azure Data Lake Store. O módulo “azure-datalake-analytics” inclui as operações do Azure Data Lake Analytics. Utilize os comandos seguintes para instalar os módulos.

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>Criar uma aplicação Python

1. Utilize o IDE à sua escolha para criar uma aplicação Python nova, como, por exemplo, mysample.py.

2. Adicione as linhas seguintes para importar os módulos necessários.

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store.account import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.account.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Guarde as alterações no ficheiro de aplicação Python.

## <a name="authentication"></a>Autenticação

Utilize um dos seguintes métodos para autenticar:

### <a name="end-user-authentication-for-account-management"></a>Autenticação de utilizador final para gestão de conta

Utilize este método para autenticar no Azure AD para operações de gestão de conta (criar/eliminar conta do Data Lake Store, etc.). Tem de indicar o nome de utilizador e a palavra-passe de um utilizador do Azure AD. Não é possível configurar a conta de utilizador para a autenticação multifator e aquela não pode ser uma conta Microsoft / Live ID, como, por exemplo, @outlook.com, e @live.com.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticação serviço para serviço com segredo do cliente para gestão da conta

Utilize este método para autenticar no Azure AD para operações de gestão de conta (criar/eliminar conta do Data Lake Store, etc.). O fragmento seguinte pode ser utilizado para autenticar a aplicação de forma não interativa com o segredo do cliente de uma aplicação/principal de serviço. Utilize esta opção com uma ”Aplicação Web“ do Azure AD existente.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>Criar grupo de Gestão de Recursos

Utilize o fragmento de código seguinte para criar um Grupo de Recursos do Azure:

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Criar uma conta do Data Lake Store

Cada conta do Data Lake Analytics requer uma conta do Data Lake Store. Para obter instruções, veja [Criar uma conta do Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).



## <a name="create-data-lake-analytics-account"></a>Criar conta do Data Lake Analytics 

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Submeter tarefas de Data Lake Analytics

As tarefas de Data Lake Analytics são escritas em linguagem U-SQL. Para saber mais sobre U-SQL, consulte [Introdução à linguagem U SQL](data-lake-analytics-u-sql-get-started.md) e [Referência de linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>Passos seguintes

- Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
- Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicações U-SQL, consulte [Desenvolver scripts SQL-U, utilizando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, veja [Manage Azure Data Lake Analytics using Azure portal (Gerir o Azure Data Lake Analytics com o Portal do Azure)](data-lake-analytics-manage-use-portal.md).
- Para uma descrição geral da Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).




<!--HONumber=Nov16_HO4-->



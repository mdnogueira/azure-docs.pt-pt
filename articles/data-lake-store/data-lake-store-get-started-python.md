---
title: "Introdução ao Azure Data Lake Store com Python | Microsoft Docs"
description: Saiba como utilizar o Python SDK para trabalhar com contas e com o sistema de ficheiros do Data Lake Store.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: d8faeafc6d4c73c4c71d0bc1554b04302dffdc55
ms.openlocfilehash: 72186e03a1dc47f67b8f4cdcac55208a61c8e147


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>Introdução ao Azure Data Lake Store com Python

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI do Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [python](data-lake-store-get-started-python.md)
>
>

Saiba como utilizar o Python SDK para o Azure e o Azure Data Lake Store para fazer operações básicas, como criar pastas, carregar e transferir ficheiros de dados, etc. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.5.2

* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma Aplicação do Azure Active Directory**. A aplicação do Azure AD é utilizada para autenticar a aplicação do Data Lake Store no Azure AD. Existem abordagens diferentes para a autenticação no Azure AD, que são **autenticação do utilizador final** ou **autenticação de serviço para serviço**. Para obter instruções e mais informações sobre a forma como autenticar, veja [Authenticate with Data Lake Store using Azure Active Directory (Autenticar no Data Lake Store com o Azure Active Directory)](data-lake-store-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar no Data Lake Store com Python, tem de instalar três módulos.

* O módulo `azure-mgmt-resource`. Inclui módulos do Azure para o Active Directory, etc...
* O módulo `azure-mgmt-datalake-store`. Inclui operações de gestão de contas do Azure Data Lake Store. Para obter mais informações sobre este módulo, veja [Azure Data Lake Store Management module reference (Referência ao módulo de Gestão do Azure Data Lake Store)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* O módulo `azure-datalake-store`. Inclui as operações do sistema de ficheiros do Azure Data Lake Store. Para obter mais informações sobre este módulo, veja [Azure Data Lake Store Filesystem module reference (Referência ao módulo de Sistema de Ficheiros do Azure Data Lake Store)](http://azure-datalake-store.readthedocs.io/en/latest/).

Utilize os comandos seguintes para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. No IDE à sua escolha, crie uma aplicação Python nova, como, por exemplo, **mysample.py**.

2. Adicione as linhas seguintes para importar os módulos necessários.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Guarde as alterações a mysample.py.

## <a name="authentication"></a>Autenticação

Nesta secção, vamos falar sobre as diferentes formas de autenticar com o Azure AD. As opções disponíveis são:

* Autenticação de utilizador final
* Autenticação serviço a serviço
* Multi-Factor Authentication

Tem de utilizar estas opções de autenticação para os módulos de gestão de contas e de gestão do sistema de ficheiros.

### <a name="end-user-authentication-for-account-management"></a>Autenticação de utilizador final para gestão de conta

Utilize este método para autenticar no Azure AD para operações de gestão de contas (criar/eliminar conta do Data Lake Store, etc.). Tem de indicar o nome de utilizador e a palavra-passe de um utilizador do Azure AD. Tenha em conta que o utilizador não deve ser configurado para a autenticação multifator.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>Autenticação do utilizador final para operações do sistema de ficheiros

Utilize este método para autenticar no Azure AD para operações do sistema de ficheiros (criar pastas, carregar ficheiros, etc.). Utilize-o com uma aplicação de **cliente nativa** do Azure AD existente. O utilizador do Azure AD cujas credenciais indicar não deve ser configurado para a autenticação mulitfator.

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticação serviço para serviço com segredo do cliente para gestão da conta

Utilize este método para autenticar no Azure AD para operações de gestão de contas (criar/eliminar conta do Data Lake Store, etc.). O fragmento seguinte pode ser utilizado para autenticar a aplicação de forma não interativa com o segredo do cliente de uma aplicação/principal de serviço. Utilize esta opção com uma ”Aplicação Web“ do Azure AD existente.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Autenticação de serviço para serviço com segredo do cliente para operações de sistema de ficheiros

Utilize este método para autenticar no Azure AD para operações do sistema de ficheiros (criar pastas, carregar ficheiros, etc.). O fragmento seguinte pode ser utilizado para autenticar a aplicação de forma não interativa com o segredo do cliente de uma aplicação/principal de serviço. Utilize esta opção com uma ”Aplicação Web“ do Azure AD existente.

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

### <a name="multi-factor-authentication-for-account-management"></a>Autenticação mulftifator para gestão de conta

Utilize este método para autenticar no Azure AD para operações de gestão de contas (criar/eliminar conta do Data Lake Store, etc.). O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Utilize esta opção com uma ”Aplicação Web“ do Azure AD existente.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="multi-factor-authentication-for-filesystem-management"></a>Autenticação multifator para gestão do sistema de ficheiros

Utilize este método para autenticar no Azure AD para operações do sistema de ficheiros (criar pastas, carregar ficheiros, etc.). O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Utilize esta opção com uma ”Aplicação Web“ do Azure AD existente.

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>Criar um Grupo de Recursos do Azure

Utilize o fragmento de código seguinte para criar um Grupo de Recursos do Azure:

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-clients-and-data-lake-store-account"></a>Criar clientes e a conta do Data Lake Store

O fragmento que se segue cria, primeiro, o cliente de conta do Data Lake Store. Utiliza o objeto de cliente para criar uma conta do Data Lake Store. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>Listar as contas do Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="create-a-directory"></a>Criar um diretório

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Carregar um ficheiro


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Transferir um ficheiro

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Eliminar um diretório

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="see-also"></a>Consultar também

- [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
- [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Referência do SDK .NET do Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx)
- [Referência do REST do Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx)



<!--HONumber=Jan17_HO2-->



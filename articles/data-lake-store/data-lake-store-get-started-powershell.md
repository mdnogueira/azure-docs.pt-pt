---
title: "Utilizar o PowerShell para começar a utilizar o Azure Data Lake Store | Microsoft Docs"
description: "Utilizar o Azure PowerShell para criar uma conta do Data Lake Store e executar operações básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 3428c3852f156df850f4b9e8833d15f4ae32729d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introdução ao Azure Data Lake Store utilizando o Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)
>
>

Aprenda a utilizar o Azure PowerShell para criar uma conta do Azure Data Lake Store e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre o Data Lake Store, veja [Descrição geral do Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Autenticação
Este artigo utiliza uma abordagem de autenticação mais simples com o Data Lake Store, em que lhe é pedido que introduza as credenciais da sua conta do Azure. O nível de acesso à conta do Data Lake Store e ao sistema de ficheiros é, então, governado pelo nível de acesso do utilizador que tem sessão iniciada. Contudo, existem outras abordagens para autenticar com o Data Lake Store, que são a **autenticação do utilizador final** ou a **autenticação de serviço a serviço**. Para obter instruções e obter mais informações sobre como autenticar, veja [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticação de utilizador final) ou [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Autenticação de serviço a serviço).

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Azure Data Lake Store
1. No ambiente de trabalho, abra uma nova janela do Windows PowerShell. Introduza o fragmento seguinte para iniciar sessão na sua conta do Azure, definir a subscrição e registar o fornecedor do Data Lake Store. Quando lhe for pedido para iniciar sessão, certifique-se de que o faz como um dos admininistradores/proprietário da subscrição:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Uma conta do Azure Data Lake Store está associada a um Grupo de Recursos do Azure. Comece por criar um Grupo de Recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Criar um Grupo de Recursos do Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Criar um Grupo de Recursos do Azure")
3. Crie uma conta do Azure Data Lake Store. O nome que especificar só pode conter minúsculas e números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Criar uma conta do Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta do Azure Data Lake Store")
4. Certifique-se de que a conta foi criada com êxito.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    O resultado do cmdlet deve ser **Verdadeiro**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Criar estruturas de diretório no Azure Data Lake Store
Pode criar diretórios na sua conta do Azure Data Lake Store para gerir e armazenar dados.

1. Especifique um diretório de raiz.

        $myrootdir = "/"
2. Crie um novo diretório denominado **mynewdirectory** na raiz especificada.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Certifique-se de que o novo diretório foi criado com êxito.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Deve ser apresentado como uma saída, conforme mostrado na captura de ecrã seguinte:

    ![Verificar Diretório](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificar Diretório")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Carregar dados para o Azure Data Lake Store
Pode carregar os dados para o Data Lake Store diretamente no nível de raiz ou para um diretório que criou na conta. Os fragmentos nesta secção demonstram como carregar alguns dados de exemplo para o diretório (**mynewdirectory**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Mudar o nome, transferir e eliminar dados do Data Lake Store
Para mudar o nome de um ficheiro, utilize o seguinte comando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para transferir um ficheiro, utilize o seguinte comando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para eliminar um ficheiro, utilize o seguinte comando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando lhe for pedido, introduza **S** para eliminar o item. Se tiver mais de um ficheiro a eliminar, pode fornecer todos os caminhos separados por vírgula.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Eliminar a conta do Azure Data Lake Store
Utilize o seguinte comando para eliminar a conta do Data Lake Store.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quando lhe for pedido, introduza **S** para eliminar a conta.

## <a name="next-steps"></a>Passos seguintes
* [Guia de sintonização de desempenho para o PowerShell com o Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
* [Utilizar o Azure Data Lake Store para requisitos de macrodados](data-lake-store-data-scenarios.md) 
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)
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
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: ebe8724d73769eb55e40b8af2056880a5a4007ce


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introdução ao Azure Data Lake Store utilizando o Azure PowerShell
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

Aprenda a utilizar o Azure PowerShell para criar uma conta do Azure Data Lake Store e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre o Data Lake Store, veja [Descrição geral do Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="authentication"></a>Autenticação
Este artigo utiliza uma abordagem de autenticação mais simples com o Data Lake Store, em que lhe é pedido que introduza as credenciais da sua conta do Azure. O nível de acesso à conta do Data Lake Store e ao sistema de ficheiros é, então, governado pelo nível de acesso do utilizador que tem sessão iniciada. Contudo, existem outras abordagens para autenticar com o Data Lake Store, que são a **autenticação do utilizador final** ou a **autenticação de serviço a serviço**. Para obter instruções e mais informações sobre a forma como autenticar, veja [Authenticate with Data Lake Store using Azure Active Directory (Autenticar no Data Lake Store com o Azure Active Directory)](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Azure Data Lake Store
1. A partir do ambiente de trabalho, abra uma nova janela do Windows PowerShell e introduza o fragmento seguinte para iniciar sessão na sua conta do Azure, definir a subscrição e registar o fornecedor do Data Lake Store. Quando lhe for pedido para iniciar sessão, certifique-se de que o faz como um dos admininistradores/proprietário da subscrição:

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

    O resultado deve ser **Verdadeiro**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Criar estruturas de diretório no Azure Data Lake Store
Pode criar diretórios na sua conta do Azure Data Lake Store para gerir e armazenar dados.

1. Especifique um diretório de raiz.

        $myrootdir = "/"
2. Crie um novo diretório denominado **mynewdirectory** na raiz especificada.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Certifique-se de que o novo diretório foi criado com êxito.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Deve agora mostrar um resultado como o seguinte:

    ![Verificar Diretório](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificar Diretório")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Carregar dados para o Azure Data Lake Store
Pode carregar os dados para o Data Lake Store diretamente no nível de raiz ou para um diretório que criou na conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para o diretório (**mynewdirectory**) que criou na secção anterior.

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

## <a name="performance-guidance-while-using-powershell"></a>Orientação de desempenho ao utilizar o PowerShell

Seguem-se as definições mais importantes que podem ser otimizadas para obter o melhor desempenho ao utilizar o PowerShell para trabalhar com o Data Lake Store:

| Propriedade            | Predefinição | Descrição |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Este parâmetro permite-lhe escolher o número de threads paralelos para carregar ou transferir cada ficheiro. Este número representa o máximo de threads que podem ser alocados por ficheiro, mas poderá obter menos threads dependendo do seu cenário (por exemplo, se estiver a carregar um ficheiro de 1 KB, obterá um thread, mesmo que solicite 20 threads).  |
| ConcurrentFileCount | 10      | Este parâmetro destina-se especificamente ao carregamento ou transferência de pastas. Este parâmetro determina o número de ficheiros simultâneos que podem ser carregados ou transferidos. Este número representa o número máximo de ficheiros simultâneos que podem ser carregados ou transferidos de uma só vez, mas poderá obter menos simultaneidade dependendo do seu cenário (por exemplo, se estiver a carregar dois ficheiros, irá obter dois carregamentos de ficheiros simultâneos, mesmo que solicite 15). |

**Exemplo**

Este comando transfere ficheiros do Azure Data Lake Store para a unidade local do utilizador, com 20 threads por ficheiro e 100 ficheiros simultâneos.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>Como posso determinar o valor a definir para estes parâmetros?

Eis algumas orientações que poderá utilizar.

* **Passo 1: Determine a contagem total de threads** - Deve começar por calcular a contagem total de threads a utilizar. Como orientação geral, deve utilizar 6 threads para cada núcleo físico.

        Total thread count = total physical cores * 6

    **Exemplo**

    Partindo do princípio de que está a executar os comandos do PowerShell a partir de uma VM D14 que tem 16 núcleos

        Total thread count = 16 cores * 6 = 96 threads


* **Passo 2: Calcular a PerFileThreadCount** - Calculamos a nossa PerFileThreadCount com base no tamanho dos ficheiros. Para ficheiros com tamanho inferior a 2,5 GB, não é necessário alterar este parâmetro, uma vez que a predefinição de 10 é suficiente. Para ficheiros com mais de 2,5 GB, deve utilizar 10 threads como base para os primeiros 2,5 GB e adicionar 1 thread por cada aumento de 256 MB adicionais no tamanho do ficheiro. Se estiver a copiar uma pasta com uma grande variedade de tamanhos de ficheiros, considere agrupá-los em tamanhos de ficheiro semelhantes. Ter tamanhos de ficheiro diferentes pode causar um desempenho não ideal. Se não for possível agrupar tamanhos de ficheiros semelhantes, deve definir PerFileThreadCount com base no tamanho de ficheiro maior.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **Exemplo**

    Partindo do princípio de que tem 100 ficheiros com tamanhos que variam entre 1 GB e 10 GB, utilizamos os 10 GB como o tamanho de ficheiro maior para a equação, que teria a seguinte formulação.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **Passo 3: Calcular a ConcurrentFilecount** - Utilize a contagem total de threads e a PerFileThreadCount para calcular a ConcurrentFileCount com base na seguinte equação.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Exemplo**

    Com base nos valores de exemplo que temos estado a utilizar

        96 = 40 * ConcurrentFileCount

    Por isso, **ConcurrentFileCount** é **2,4**, que podemos arredondar para **2**.

### <a name="further-tuning"></a>Otimização adicional

Poderá necessitar de otimização adicional porque existe uma grande variedade de tamanhos de ficheiro com os quais trabalhar. O cálculo acima funciona bem se a totalidade ou a maior parte dos ficheiros tiver um tamanho superior e próximo do intervalo de 10 GB. Se, em vez disso, existirem vários tamanhos de ficheiro diferentes com muitos ficheiros mais pequenos, poderia reduzir a PerFileThreadCount. Ao reduzir a PerFileThreadCount, podemos aumentar a ConcurrentFileCount. Por isso, se partirmos do princípio de que a maioria dos ficheiros são mais pequenos no intervalo de 5 GB, podemos refazer o nosso cálculo:

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

Por isso, **ConcurrentFileCount** será agora 96/20, que corresponde a 4,8, arredondado para **4**.

Pode continuar a otimizar estas definições, alterando a **PerFileThreadCount** para cima e para baixo consoante a distribuição dos tamanhos de ficheiro.

### <a name="limitation"></a>Limitação

* **O número de ficheiros é menor que ConcurrentFileCount**: se o número de ficheiros que está a carregar for menor que a **ConcurrentFileCount** que calculou, deve reduzir a **ConcurrentFileCount** para ser igual ao número de ficheiros. Pode utilizar quaisquer threads restantes para aumentar a **PerFileThreadCount**.

* **Existem demasiados threads**: se aumentar demasiado a contagem de threads sem aumentar o tamanho do cluster, corre o risco de degradar o desempenho. Podem existir problemas de contenção ao alternar de contexto na CPU.

* **Simultaneidade insuficiente**: se a simultaneidade não for suficiente, significa que o cluster pode ser demasiado pequeno. Pode aumentar o número de nós no cluster, o que lhe dará mais simultaneidade.

* **Erros de limitação**: poderá ver erros de limitação se a simultaneidade for demasiado elevada. Se vir erros de limitação, deverá reduzir a simultaneidade ou contactar a Microsoft.

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Jan17_HO4-->



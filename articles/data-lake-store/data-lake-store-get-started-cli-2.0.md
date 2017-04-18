---
title: "Utilizar a interface de linha de comandos 2.0 do Azure para começar a utilizar o Azure Data Lake Store | Microsoft Docs"
description: "Utilizar a linha de comandos entre plataformas 2.0 para criar uma conta de Data Lake Store e executar operações básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: c9d5fdc2ff27454b2492751034b43658ee9d46c5
ms.lasthandoff: 04/06/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20-preview"></a>Introdução ao Azure Data Lake Store utilizando a CLI 2.0 do Azure (Pré-visualização)
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI do Azure](data-lake-store-get-started-cli.md)
> * [CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [python](data-lake-store-get-started-python.md)
>
>

Aprenda a utilizar a CLI 2.0 do Azure para criar uma conta do Azure Data Lake Store e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre o Data Lake Store, veja [Descrição geral do Data Lake Store](data-lake-store-overview.md).

A CLI 2.0 do Azure é nova experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode ser utilizada no macOS, no Linux e no Windows. Para obter mais informações, consulte [Overview of Azure CLI 2.0 (Descrição geral da CLI 2.0 do Azure)](https://docs.microsoft.com/cli/azure/overview). Também pode ver a [Azure Data Lake Store CLI 2.0 reference (referência da CLI 2.0 do Azure Data Lake Store)](https://docs.microsoft.com/cli/azure/dls) para obter uma lista completa de comandos e a sintaxe.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **CLI 2.0 do Azure** - Consulte [Install Azure CLI 2.0 (Instalar a 2.0 CLI do Azure)](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter instruções.

## <a name="authentication"></a>Autenticação

Este artigo utiliza uma abordagem de autenticação mais simples com o Data Lake Store, em que inicia sessão como utilizador final. O nível de acesso à conta do Data Lake Store e ao sistema de ficheiros é, então, governado pelo nível de acesso do utilizador que tem sessão iniciada. Contudo, existem outras abordagens para autenticar com o Data Lake Store, que são a **autenticação do utilizador final** ou a **autenticação de serviço a serviço**. Para obter instruções e mais informações sobre a forma como autenticar, veja [Authenticate with Data Lake Store using Azure Active Directory (Autenticar no Data Lake Store com o Azure Active Directory)](data-lake-store-authenticate-using-active-directory.md).

## <a name="enable-data-lake-store-preview-in-azure-cli-20"></a>Ativar Data Lake Store (Pré-visualização) na CLI 2.0 do Azure

A CLI 2.0 do Data Lake Store está atualmente em pré-visualização e não fica ativada por predefinição ao instalar a CLI 2.0 do Azure. Execute o seguinte comando para ativar uma CLI 2.0 do Data Lake Store.

```azurecli
az component update --add dls
```


## <a name="log-in-to-your-azure-subscription"></a>Inicie sessão na subscrição do Azure

1. Inicie sessão na subscrição do Azure.

    ```azurecli
    az login
    ```

    Obtenha um código para utilizar no passo seguinte. Utilize um browser para abrir a página https://aka.ms/devicelogin e introduza o código para autenticar. É-lhe pedido para iniciar sessão com as suas credenciais.

2. Depois de iniciar sessão, a janela lista todas as subscrições do Azure que estão associadas à sua conta. Utilize o seguinte comando para utilizar uma subscrição específica.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Azure Data Lake Store

1. Crie um novo grupo de recursos. No seguinte comando, forneça os valores de parâmetros que pretende utilizar. Se o nome da localização contiver espaços, coloque-o entre aspas. Por exemplo, "EUA Leste 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Crie uma conta do Data Lake Store.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Criar pastas numa conta do Data Lake Store

Pode criar pastas na sua conta do Azure Data Lake Store para gerir e armazenar dados. Utilize o seguinte comando para criar uma pasta denominada **mynewfolder** na raiz do Data Lake Store.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> O parâmetro `--folder` assegura que o comando cria uma pasta. Se este parâmetro não estiver presente, o comando cria um ficheiro vazio designado mynewfolder na raiz da conta do Data Lake Store.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Carregar dados para uma conta do Data Lake Store

Pode carregar os dados para o Data Lake Store diretamente no nível de raiz ou para uma pasta que criou na conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para a pasta (**mynewfolder**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Para o destino, tem de especificar o caminho completo, incluindo o nome do ficheiro.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Listar ficheiros numa conta do Data Lake Store

Utilize o seguinte comando para listar os ficheiros numa conta do Data Lake Store.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

O resultado deve ser semelhante ao seguinte:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Mudar o nome, transferir e eliminar dados de uma conta do Data Lake Store 

* **Para mudar o nome de um ficheiro**, utilize o seguinte comando:
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Para transferir um ficheiro**, utilize o seguinte comando: Certifique-se de que o caminho de destino especificado já existe.
  
    ```azurecli        
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > O comando cria a pasta de destino se não existir.
    > 
    >

* **Para eliminar um ficheiro**, utilize o seguinte comando:
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Se pretender eliminar a pasta **mynewfolder** e o ficheiro **vehicle1_09142014_copy.csv** em conjunto num comando, utilize o --parâmetro de recurso

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Trabalhe com as permissões e ACLs de uma conta do Data Lake Store

Nesta secção, saiba mais sobre como gerir ACLs e permissões com a CLI 2.0 do Azure. Para um debate detalhado sobre como as ACLs estão implementadas no Azure Data Lake Store, consulte [Access control in Azure Data Lake Store (Controlo de acesso do Azure Data Lake Store)](data-lake-store-access-control.md).

* **Para atualizar o proprietário de um ficheiro/pasta**, utilize o seguinte comando:

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Para atualizar as permissões de um ficheiro/pasta**, utilize o seguinte comando:

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Para obter as ACLs de um determinado caminho**, utilize o seguinte comando:

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    O resultado deve ser semelhante ao seguinte:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Para definir uma entrada de uma ACL**, utilize o seguinte comando:

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Para remover uma entrada de uma ACL**, utilize o seguinte comando:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Para remover uma ACL predefinida completa**, utilize o seguinte comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **Para remover uma ACL não-predefinida completa**, utilize o seguinte comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Eliminar uma conta do Data Lake Store
Utilize o seguinte comando para eliminar uma conta do Data Lake Store.

```azurecli
az dls account delete --account mydatalakestore
```

Quando lhe for pedido, introduza **S** para eliminar a conta.

## <a name="next-steps"></a>Passos seguintes

* [Referência da CLI 2.0 do Azure Data Lake Store](https://docs.microsoft.com/cli/azure/dls)
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md


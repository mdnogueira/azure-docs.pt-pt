---
title: "API REST: operações do sistema de ficheiros no Azure Data Lake Store | Microsoft Docs"
description: "Utilizar APIs REST WebHDFS para efetuar operações do sistema de ficheiros no Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 62cfc713c3b7ce8f7c0a7ff4dd5e80802f1843b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Operações do sistema de ficheiros no Azure Data Lake Store com a API REST
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, aprenderá a utilizar APIs REST WebHDFS e APIs REST do Data Lake Store para efetuar operações do sistema de ficheiros no Azure Data Lake Store. Para obter instruções sobre como efetuar operações de gestão de contas no Data Lake Store com a API REST, veja [Operações de gestão de contas no Data Lake Store com a API REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure Data Lake Store**. Siga as instruções em [Get started with Azure Data Lake Store using the Azure portal (Introdução ao Azure Data Lake Store com o portal do Azure)](data-lake-store-get-started-portal.md).

* **[cURL](http://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas da API REST em relação a uma conta do Data Lake Store.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

* Para a autenticação de utilizador final na sua aplicação (interativa), veja [Autenticação de utilizador final com o Data Lake Store com o SDK .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para a autenticação serviço a serviço na sua aplicação (não interativa), veja [Autenticação serviço a serviço com o Data Lake Store com o SDK .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Criar pastas
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilize o seguinte comando cURL. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. Este comando cria um diretório denominado **mytempdir** na pasta raiz da conta do Data Lake Store.

Se a operação for concluída com êxito, deverá ver uma resposta como o seguinte fragmento:

    {"boolean":true}

## <a name="list-folders"></a>Listar pastas
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilize o seguinte comando cURL. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente.

Se a operação for concluída com êxito, deverá ver uma resposta como o seguinte fragmento:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Carregar dados
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Utilize o seguinte comando cURL. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

No parâmetro da sintaxe anterior **-T** encontra-se a localização do ficheiro que está a carregar.

O resultado é semelhante ao seguinte fragmento:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Ler dados
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Ler dados de uma conta do Data Lake Store é um processo de dois passos.

* Primeiro, submeta um pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esta chamada devolve uma localização para submeter o pedido GET seguinte.
* Em seguida, submeta o pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Esta chamada apresenta o conteúdo do ficheiro.

No entanto, uma vez que não existe nenhuma diferença nos parâmetros de entrada entre o primeiro e o segundo passo, pode utilizar o parâmetro `-L` para submeter o primeiro pedido. A opção `-L` combina essencialmente dois pedidos num único e faz com que o cURL refaça o pedido na nova localização. Por fim, é apresentado o resultado de todas as chamadas do pedido, como mostrado no fragmento seguinte. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Deverá ver um resultado semelhante ao seguinte fragmento:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilize o seguinte comando cURL para mudar o nome de um ficheiro. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Deverá ver um resultado semelhante ao seguinte fragmento:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Eliminar um ficheiro
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilize o seguinte comando cURL para eliminar um ficheiro. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Deve ver um resultado como o seguinte:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Passos seguintes
* [Operações de gestão de contas no Data Lake Store com a API REST](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Consultar também
* [Referência à API REST do Azure Data Lake Store](https://docs.microsoft.com/rest/api/datalakestore/)
* [Open Source Big Data applications compatible with Azure Data Lake Store (Aplicações de Macrodados Open Source compatíveis com o Azure Data Lake Store)](data-lake-store-compatible-oss-other-applications.md)


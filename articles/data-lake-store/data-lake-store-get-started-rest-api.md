---
title: Introdução ao Data Lake Store utilizando a API REST | Microsoft Docs
description: Utilizar APIs REST WebHDFS para executar operações no Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: nitinme

---
# Introdução ao Azure Data Lake Store utilizando APIs REST
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI do Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Neste artigo, irá aprender a utilizar APIs REST WebHDFS e APIs REST do Data Lake Store para efetuar a gestão de contas, bem como operações do sistema de ficheiros no Azure Data Lake Store. O Azure Data Lake Store expõe as suas próprias APIs REST para as operações de gestão de contas. No entanto, uma vez que o Data Lake Store é compatível com o ecossistema HDFS e Hadoop, suporta a utilização de APIs REST WebHDFS para operações do sistema de ficheiros.

> [!NOTE]
> Para obter informações detalhadas sobre o suporte de APIs REST do Data Lake Store, veja [Referência da API REST do Azure Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx).
> 
> 

## Pré-requisitos
* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Criar uma Aplicação do Azure Active Directory**. Existem duas formas para se autenticar com o Azure Active Directory - **interativa** e **não interativa**. Existem pré-requisitos diferentes com base na forma como se pretende autenticar.
  
  * **Para a autenticação interativa** (utilizada neste artigo) - No Azure Active Directory, tem de criar uma **aplicação de Cliente Nativo**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
    
    * Obtenha o **ID de cliente** e o **URI de redirecionamento** para a aplicação
    * Definir permissões delegadas
  * **Para a autenticação não interativa** - No Azure Active Directory, tem de criar uma **aplicação Web**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
    
    * Obtenha o **ID de cliente**, o **segredo de cliente** e o **URI de redirecionamento** para a aplicação
    * Definir permissões delegadas
    * Atribua a aplicação do Azure Active Directory a uma função. A função pode estar ao nível do âmbito a que pretende dar permissão à aplicação do Azure Active Directory. Por exemplo, pode atribuir a aplicação ao nível da subscrição ou ao nível de um grupo de recursos. Para obter instruções, veja [Atribuir a aplicação a uma função](../resource-group-create-service-principal-portal.md#assign-application-to-role). 
    
    Veja [Criar aplicação do Active Directory e um principal de serviço com o portal](../resource-group-create-service-principal-portal.md) para obter instruções sobre como obter estes valores, definir as permissões e atribuir funções.
* [cURL](http://curl.haxx.se/). Este artigo utiliza o cURL para demonstrar como fazer chamadas da API REST em relação a uma conta do Data Lake Store.

## Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

### Interativa (autenticação do utilizador)
Neste cenário, a aplicação pede ao utilizador para iniciar sessão e todas as operações são efetuadas no contexto do utilizador. Execute os passos seguintes para a autenticação interativa.

1. Através da sua aplicação, redirecione o utilizador para o seguinte URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI> tem de estar codificado para utilização num URL. Assim, https://localhost, utilize `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    Para o objetivo deste tutorial, pode substituir os valores de marcador de posição no URL acima e colá-lo na barra de endereço do browser. Será redirecionado para a autenticação utilizando o seu início de sessão do Azure. Depois de iniciar sessão com êxito, a resposta é apresentada na barra de endereço do browser. A resposta estará no seguinte formato:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. Capture o código de autorização na resposta. Para este tutorial, pode copiar o código de autorização na barra de endereço do browser e transmiti-lo no pedido POST ao ponto final do token, conforme mostrado abaixo:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > Neste caso, \<REDIRECT-URI> não tem de ser codificado.
   > 
   > 
3. A resposta é um objeto JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"`) e um token de atualização (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"`). A aplicação utiliza o token de acesso ao aceder ao Azure Data Lake Store e o token de atualização para obter outro token de acesso quando um token de acesso expira.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. Quando o token de acesso expira, pode pedir um novo token de acesso utilizando o token de atualização, conforme mostrado abaixo:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
           -F grant_type=refresh_token \
           -F resource=https://management.core.windows.net/ \
           -F client_id=<CLIENT-ID> \
           -F refresh_token=<REFRESH-TOKEN>

Para obter mais informações sobre a autenticação de utilizador interativa, veja [Fluxo de concessão de códigos de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### Não interativo
Neste cenário, a aplicação fornece as suas próprias credenciais para efetuar as operações. Para tal, tem de emitir um pedido POST, como o mostrado abaixo. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

O resultado deste pedido irá incluir um token de autorização (denotado por `access-token` no resultado abaixo) que irá transmitir subsequentemente com as chamadas da API REST. Guarde este token de autenticação num ficheiro de texto; irá precisar dele mais tarde neste artigo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo utiliza a abordagem **não interativa**. Para obter mais informações sobre a abordagem não interativa (chamadas serviço a serviço), veja [Chamadas serviço a serviço utilizando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## Criar uma conta do Data Lake Store
Esta operação baseia-se na chamada da API REST definida [aqui](https://msdn.microsoft.com/library/mt694078.aspx).

Utilize o seguinte comando cURL. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

No comando acima, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. O payload do pedido para este comando está contido no ficheiro **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do ficheiro input.json assemelha-se ao seguinte:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## Criar pastas numa conta do Data Lake Store
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilize o seguinte comando cURL. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

No comando acima, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. Este comando cria um diretório denominado **mytempdir** na pasta raiz da conta do Data Lake Store.

Deverá ver uma resposta como esta se a operação for concluída com êxito:

    {"boolean":true}

## Listar pastas numa conta do Data Lake Store
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilize o seguinte comando cURL. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

No comando acima, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente.

Deverá ver uma resposta como esta se a operação for concluída com êxito:

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
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## Carregar dados para uma conta do Data Lake Store
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Carregar dados utilizando a API REST WebHDFS é um processo de dois passos, conforme explicado abaixo.

1. Submeta um pedido HTTP PUT sem enviar os dados do ficheiro a ser carregado. No comando seguinte, substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.
   
        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE
   
    O resultado para este comando irá conter um URL de redirecionamento temporário, como o mostrado abaixo.
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...
2. Agora, tem de submeter outro pedido HTTP PUT relativamente ao URL listado para a propriedade **Localização** na resposta. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.
   
        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true
   
    O resultado será semelhante ao seguinte:
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 201 Created
        ...
        ...

## Ler dados de uma conta do Data Lake Store
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Ler dados de uma conta do Data Lake Store é um processo de dois passos.

* Primeiro, submeta um pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Isto irá devolver uma localização para submeter o pedido GET seguinte.
* Em seguida, submeta o pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Esta ação apresentará o conteúdo do ficheiro.

No entanto, uma vez que não existe nenhuma diferença nos parâmetros de entrada entre o primeiro e o segundo passo, pode utilizar o parâmetro `-L` para submeter o primeiro pedido. `-L` A opção combina essencialmente dois pedidos num único e fará com que o cURL refaça o pedido na nova localização. Por fim, é apresentado o resultado de todas as chamadas do pedido, como mostrado abaixo. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Deverá ver um resultado semelhante ao seguinte:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## Mudar o nome de um ficheiro numa conta do Data Lake Store
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilize o seguinte comando cURL para mudar o nome de um ficheiro. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Deverá ver um resultado semelhante ao seguinte:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## Eliminar um ficheiro de uma conta do Data Lake Store
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilize o seguinte comando cURL para eliminar um ficheiro. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Deve ver um resultado como o seguinte:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## Eliminar uma conta do Data Lake Store
Esta operação baseia-se na chamada da API REST definida [aqui](https://msdn.microsoft.com/library/mt694075.aspx).

Utilize o seguinte comando cURL para eliminar uma conta do Data Lake Store. Substitua **\<nomedoarquivo>** pelo nome do Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Deve ver um resultado como o seguinte:

    HTTP/1.1 200 OK
    ...
    ...

## Consultar também
* [Aplicações de Macrodados Open Source compatíveis com o Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

<!--HONumber=Sep16_HO3-->



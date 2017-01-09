---
title: "Utilizar o SDK Java do Data Lake Store para desenvolver aplicações | Microsoft Docs"
description: "Utilizar o SDK Java do Azure Data Lake Store para desenvolver aplicações"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/23/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: c157da7bf53e2d0762624e8e71e56e956db04a24
ms.openlocfilehash: a80da95328a6f3c47edf6e9be9e786437a8c316e


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Introdução ao Azure Data Lake Store com Java
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

Saiba como utilizar o SDK Java do Azure Data Lake Store para executar operações básicas, como criar pastas, carregar e transferir ficheiros de dados, etc. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

Pode aceder aos documentos da API do SDK Java do Azure Data Lake Store nos [documentos da API do SDK Java do Azure Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Pré-requisitos
* Kit de Desenvolvimento do Java (JDK 7 ou superior, com Java versão 1.7 ou superior)
* Conta do Azure Data Lake Store. Siga as instruções em [Introdução ao Azure Data Lake Store com o Portal do Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível compilar sem utilizar um sistema de compilação como Maven ou Gradle, estes sistemas facilitam muito a gestão das dependências.
* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Neste tutorial, utilizamos um segredo do cliente da aplicação Azure AD para obter um token do Azure Active Directory (autenticação do serviço a serviço). Este token é utilizado para criar um objeto de cliente do Data Lake Store para realizar operações de ficheiros e de diretórios. Para obter instruções sobre como autenticar com o Azure Data Lake Store através do segredo do cliente, realizamos os seguintes passos de alto nível:

1. Criar uma aplicação Web do Azure AD.
2. Obter o ID de cliente, o segredo do cliente e o ponto final do token da aplicação Web do Azure AD.
3. Configurar o acesso à aplicação Web do Azure AD no ficheiro/pasta do Data Lake Store que pretende aceder a partir da aplicação Java que está a criar.

Para obter instruções sobre como realizar estes passos, veja [Create an Active Directory application (Criar uma aplicação do Active Directory)](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

O Azure Active Directory fornece também outras opções para obter um token. Pode escolher entre vários mecanismos de autenticação diferentes de acordo com o seu cenário, por exemplo, uma aplicação em execução num browser, uma aplicação distribuída como uma aplicação de ambiente de trabalho ou uma aplicação de servidor em execução no local ou numa máquina virtual do Azure. Também pode escolher entre diferentes tipos de credenciais, como palavras-passe, certificados, autenticação de 2 fatores, etc. Além disso, o Azure Active Directory permite-lhe sincronizar os seus utilizadores do Active Directory no local com a nuvem. Para obter detalhes, veja [Authentication Scenarios for Azure Active Directory (Cenários de Autenticação do Azure Active Directory)](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Criar uma aplicação Java
O exemplo de código disponível no [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) explica-lhe o processo de criação de ficheiros no arquivo, de concatenação de ficheiros, de transferência de um ficheiro e de eliminação de alguns ficheiros do arquivo. Esta secção do artigo explica-lhe as partes principais do código.

1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 
2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte fragmento de texto entre a etiqueta **\</version >** e a etiqueta **\</project >**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.1</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    A primeira dependência é para utilizar o SDK do Data Lake Store (`azure-datalake-store`) do repositório maven. A segunda dependência (`slf4j-nop`) é para especificar a arquitetura de registo a utilizar para esta aplicação. O SDK do Data Lake Store utiliza uma frente de registo [slf4j](http://www.slf4j.org/), que permite escolher a partir de várias arquiteturas de registo mais populares, como log4j, registo Java, logback, etc., ou nenhum registo. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Adicionar o código da aplicação
Existem três partes principais para o código.

1. Obter o token do Azure Active Directory.
2. Utilizar o token para criar um cliente do Data Lake Store.
3. Utilizar o cliente do Data Lake Store para realizar operações.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Passo 1: obter um token do Azure Active Directory
O SDK do Data Lake Store fornece métodos convenientes que lhe permitem obter os tokens de segurança necessários para comunicar com a conta do Data Lake Store. No entanto, o SDK não impõe que sejam utilizados apenas estes métodos. Pode utilizar também qualquer outro meio para obter um token, como utilizar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou o seu próprio código personalizado.

Para utilizar o SDK do Data Lake Store para obter um token para a aplicação Web do Active Directory que criou anteriormente, utilize os métodos estáticos na classe `AzureADAuthenticator`. Substitua **FILL-IN-HERE** pelos valores reais da aplicação Web do Azure Active Directory.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Passo 2: criar um objeto de cliente (ADLStoreClient) do Azure Data Lake Store
Criar um objeto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) requer que especifique o nome da conta do Data Lake Store e o token do Azure Active Directory gerados no último passo. Tenha em atenção que o nome da conta do Data Lake Store tem de ser um nome de domínio completamente qualificado. Por exemplo, substitua **FILL-IN-HERE** por algo como **omeudatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Passo 3: utilizar o ADLStoreClient para realizar as operações de ficheiros e de diretórios
O código abaixo contém fragmentos de exemplo de algumas operações comuns. Pode ver os [documentos da API do SDK Java do Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) completos do objeto **ADLStoreClient** para ver outras operações.

Tenha em atenção que os ficheiros são lidos e escritos com fluxos de Java padrão. Tal significa que pode colocar qualquer um dos fluxos de Java na parte superior dos fluxos do Data Lake Store para beneficiar da funcionalidade Java padrão (por exemplo, imprimir fluxos de saída formatada ou qualquer um dos fluxos de compressão e encriptação para uma funcionalidade adicional na parte superior, etc.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Passo 4: compilar e executar a aplicação
1. Para executar a partir de um IDE, localize e prima o botão **Executar**. Para executar a partir do Maven, utilize [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Para produzir um jar autónomo que pode executar a partir da linha de comandos, compile um jar com todas as dependências incluídas com o [plug-in de montagem do Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). O pom.xml no [código de origem de exemplo no github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tem um exemplo sobre como realizar esta ação.

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO4-->



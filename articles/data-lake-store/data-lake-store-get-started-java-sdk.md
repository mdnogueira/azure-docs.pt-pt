---
title: "SDK Java: operações do sistema de ficheiros no Azure Data Lake Store | Microsoft Docs"
description: "Utilize o SDK Java do Azure Data Lake Store para executar operações do sistema de ficheiros no Data Lake Store, como criar pastas, etc."
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
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: e8c7b788061b3eb18b3e6c282339a03d93ab8b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-data-lake-store-using-java-sdk"></a>Operações do sistema de ficheiros no Data Lake Store com o SDK Java
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [python](data-lake-store-data-operations-python.md)
>
> 

Saiba como utilizar o SDK Java do Azure Data Lake Store para executar operações básicas, como criar pastas, carregar e transferir ficheiros de dados, etc. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

Pode aceder aos documentos da API do SDK Java do Azure Data Lake Store nos [documentos da API do SDK Java do Azure Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Pré-requisitos
* Kit de Desenvolvimento do Java (JDK 7 ou superior, com Java versão 1.7 ou superior)
* Conta do Azure Data Lake Store. Siga as instruções em [Get started with Azure Data Lake Store using the Azure portal (Introdução ao Azure Data Lake Store com o portal do Azure)](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível compilar sem utilizar um sistema de compilação como Maven ou Gradle, estes sistemas facilitam muito a gestão das dependências.
* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="create-a-java-application"></a>Criar uma aplicação Java
O exemplo de código disponível no [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) explica-lhe o processo de criação de ficheiros no arquivo, de concatenação de ficheiros, de transferência de um ficheiro e de eliminação de alguns ficheiros do arquivo. Esta secção do artigo explica-lhe as partes principais do código.

1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte fragmento antes da etiqueta **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    A primeira dependência é para utilizar o SDK do Data Lake Store (`azure-data-lake-store-sdk`) do repositório maven. A segunda dependência é para especificar a arquitetura de registo (`slf4j-nop`) a utilizar para esta aplicação. O SDK do Data Lake Store utiliza uma frente de registo [slf4j](http://www.slf4j.org/), que permite escolher a partir de várias arquiteturas de registo mais populares, como log4j, registo Java, logback, etc., ou nenhum registo. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](http://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes declarações de importação à aplicação.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Autenticação

* Para a autenticação de utilizador final na sua aplicação, veja [Autenticação de utilizador final com o Data Lake Store através de Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Para a autenticação serviço a serviço na sua aplicação, veja [Autenticação serviço a serviço com o Data Lake Store através de Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-an-azure-data-lake-store-client"></a>Criar um cliente do Azure Data Lake Store
Criar um objeto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) requer que especifique o nome da conta do Data Lake Store e o fornecedor do token que gerou durante a autenticação com o Data Lake Store (veja a secção [Autenticação](#authentication)). O nome da conta do Data Lake Store tem de ser um nome de domínio completamente qualificado. Por exemplo, substitua **FILL-IN-HERE** por algo como **omeudatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Os fragmentos de código nas secções seguintes contêm exemplos de algumas operações comuns do sistema de ficheiros. Pode ver os [documentos da API do SDK Java do Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) completos do objeto **ADLStoreClient** para ver outras operações.

## <a name="create-a-directory"></a>Criar um diretório

O fragmento seguinte cria uma estrutura de diretórios na raiz da conta do Data Lake Store que especificou.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Criar um ficheiro

O fragmento seguinte cria um ficheiro (c.txt) na estrutura de diretórios e escreve alguns dados no ficheiro.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Também pode criar um ficheiro (d.txt) através de matrizes de bytes.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

A definição da função `getSampleContent` utilizada no fragmento anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Acrescentar a um ficheiro

O fragmento seguinte acrescenta conteúdo a um ficheiro existente.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

A definição da função `getSampleContent` utilizada no fragmento anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Ler um ficheiro

O fragmento seguinte lê o conteúdo de um ficheiro na conta do Data Lake Store.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Concatenar ficheiros

O fragmento seguinte concatena dois ficheiros na conta do Data Lake Store. Se for efetuado com êxito, o ficheiro concatenado substitui os dois ficheiros existentes.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro

O fragmento seguinte muda o nome de um ficheiro na conta do Data Lake Store.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Obter metadados para um ficheiro

O fragmento seguinte obtém os metadados para um ficheiro na conta do Data Lake Store.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Definir permissões num ficheiro

O fragmento seguinte define as permissões do ficheiro que criou na secção anterior.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

O fragmento seguinte lista o conteúdo de um diretório, em modo recursivo.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

A definição da função `printDirectoryInfo` utilizada no fragmento anterior está disponível como parte do exemplo [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Eliminar ficheiros e pastas

O fragmento seguinte elimina os ficheiros e pastas especificados numa conta do Data Lake Store, em modo recursivo.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação
1. Para executar a partir de um IDE, localize e prima o botão **Executar**. Para executar a partir do Maven, utilize [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Para produzir um jar autónomo que pode executar a partir da linha de comandos, compile um jar com todas as dependências incluídas com o [plug-in de montagem do Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). O pom.xml no [código de origem de exemplo no github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tem um exemplo.

## <a name="next-steps"></a>Passos seguintes
* [Explorar JavaDoc para o SDK Java](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)



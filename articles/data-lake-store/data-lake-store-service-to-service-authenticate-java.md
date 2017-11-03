---
title: "Autenticação do serviço de serviço: Java com o Data Lake Store utilizando o Azure Active Directory | Microsoft Docs"
description: "Aprenda a alcançar a autenticação de serviço a serviço utilizando o Azure Active Directory com o Java do Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: f68239b2f4f7a2ba0617023d9397184c483a4d99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-java"></a>Autenticação do serviço de serviço com o Data Lake Store com Java
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Neste artigo, pode saber mais sobre como utilizar o SDK Java para fazer a autenticação de serviços do Azure Data Lake Store. Não é suportada a autenticação de utilizador final com o Data Lake Store utilizando o SDK de Java.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Web" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação do serviço de serviço com o Data Lake Store utilizando o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível compilar sem utilizar um sistema de compilação como Maven ou Gradle, estes sistemas facilitam muito a gestão das dependências.

* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço
1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte fragmento antes da etiqueta **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
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

4. Utilize o seguinte fragmento na sua aplicação Java para obter o token para a aplicação Web do Active Directory que criou anteriormente com uma das subclasses de `AccessTokenProvider` (o exemplo seguinte utiliza `ClientCredsTokenProvider`). O fornecedor do token guarda as credenciais utilizadas para obter o token na memória e renova-o automaticamente se estiver prestes a expirar. É possível criar as seus próprios as subclasses de `AccessTokenProvider` para tokens são obtidos pelo seu código de cliente. Por agora, vamos apenas utilizar fornecido no SDK.

    Substitua **FILL-IN-HERE** pelos valores reais da aplicação Web do Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

O SDK do Data Lake Store disponibiliza métodos convenientes que lhe permitem gerir os tokens de segurança necessários para comunicar com a conta do Data Lake Store. No entanto, o SDK não impõe que sejam utilizados apenas estes métodos. Pode utilizar também qualquer outro meio para obter um token, como utilizar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou o seu próprio código personalizado.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a autenticação de utilizador final para autenticar com o Azure Data Lake Store utilizando o SDK de Java. Agora pode ver os seguintes artigos falar sobre como utilizar o SDK Java para trabalhar com o Azure Data Lake Store.

* [Operações de dados no Data Lake Store com Java SDK](data-lake-store-get-started-java-sdk.md)



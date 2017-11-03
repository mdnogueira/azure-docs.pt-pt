---
title: "Linha de comandos do Azure do AD Java introdução | Microsoft Docs"
description: "Como criar uma aplicação de linha de comandos do Java que assina os utilizadores para aceder a uma API."
services: active-directory
documentationcenter: java
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 91e4a7b2ac454465d5cce4948a4d5f0b542d2b55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Utilizar a aplicação de linha de comandos de Java para aceder a uma API com o Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure AD torna simples e fácil de outsource gestão de identidade da aplicação web, fornecendo único início de sessão e fim de sessão com apenas alguns linhas de código.  Nas aplicações web de Java, isto é possível utilizar a implementação do ADAL4J Comunidade da Microsoft.

  Aqui vamos utilizar ADAL4J para:

* Inicie a sessão de utilizador para a aplicação utilizar o Azure AD como o fornecedor de identidade.
* Apresente algumas informações sobre o utilizador.
* O utilizador fora da aplicação de início de sessão.

Para tal, tem de:

1. Registar uma aplicação com o Azure AD
2. Configure a sua aplicação para utilizar a biblioteca de ADAL4J.
3. Utilize a biblioteca de ADAL4J para emitir pedidos de início de sessão e fim de sessão para o Azure AD.
4. Imprima os dados sobre o utilizador.

Para começar, [transferir a estrutura de aplicação](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) ou [transferir o exemplo concluído](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip).  Também terá um inquilino do Azure AD na qual pode registar a sua aplicação.  Se ainda não tiver uma, [saber como obter um](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1.  Registar uma aplicação com o Azure AD
Para ativar a sua aplicação autenticar os utilizadores, primeiro terá de registar uma nova aplicação no seu inquilino.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta e, no **diretório** lista, escolha o inquilino do Active Directory onde pretende registar a sua aplicação.
3. Clique em **mais serviços** na navegação esquerda e escolha **do Azure Active Directory**.
4. Clique em **registos de aplicação** e escolha **adicionar**.
5. Siga as instruções e crie um novo **aplicação Web e/ou End WebAPI**.
  * O **nome** da aplicação irá descrever a aplicação aos utilizadores finais
  * O **URL de início de sessão** é o URL de base da sua aplicação.  Predefinição a estrutura é `http://localhost:8080/adal4jsample/`.
6. Depois de concluir o registo, AAD atribuirá a aplicação um ID de aplicação único.  Irá precisar deste valor nas secções seguintes, por isso, copie-o de separador da aplicação.
7. Do **definições** -> **propriedades** página para a sua aplicação, atualize o URI de ID de aplicação. O **URI de ID de aplicação** é um identificador exclusivo para a sua aplicação.  A Convenção de consiste em utilizar `https://<tenant-domain>/<app-name>`, por exemplo, `http://localhost:8080/adal4jsample/`.

Uma vez no portal para a sua aplicação criar um **chave** do **definições** página para a sua aplicação e copie-a para baixo.  Precisará dele em breve.

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. Configurar a aplicação utilizar bibliotecas ADAL4J e pré-requisitos com o Maven
Aqui, iremos irá configurar ADAL4J para utilizar o protocolo de autenticação OpenID Connect.  ADAL4J será utilizado para emitir pedidos de início de sessão e fim de sessão, gerir a sessão do utilizador e obter informações sobre o utilizador, entre outras coisas.

* No diretório de raiz do projeto, abrir/criar `pom.xml` e localize o `// TODO: provide dependencies for Maven` e substitua o seguinte:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. Criar o ficheiro de Java PublicClient
Conforme indicado acima, vamos utilizar a Graph API para obter dados sobre o utilizador com sessão iniciada. Para que isto seja mais fácil para nos deve criar tanto um ficheiro para representar um **objeto de diretório** e um ficheiro individual para representar o **utilizador** para que possa ser utilizado o padrão OO de Java.

* Crie um ficheiro chamado `DirectoryObject.java` que iremos utilizar para armazenar dados básicos sobre qualquer DirectoryObject (que pode à vontade para utilizá-lo mais tarde para outras consultas gráfico, pode fazê-lo). -Pode cortar/colar isto partir do abaixo:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>Compilar e executar o exemplo
Altere enviados de volta para o diretório de raiz e execute o seguinte comando para criar a amostra apenas a colocar em conjunto com `maven`. Este procedimento irá utilizar o `pom.xml` que escreveu para dependências de ficheiro.

`$ mvn package`

Agora, deve ter um `adal4jsample.war` de ficheiros no seu `/targets` diretório. Pode implementar que no contentor de Tomcat e aceda ao URL 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> É muito fácil de implementar um WAR com os servidores de Tomcat mais recentes. Basta navegar para `http://localhost:8080/manager/` e siga as instruções no carregamento do ' adal4jsample.war' ficheiros. Irá autodeploy por si com o ponto final correto.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Parabéns! Agora tem uma aplicação de Java que tenha a capacidade para autenticar utilizadores, de forma segura chamar APIs da Web através de OAuth 2.0 e obter informações básicas sobre o utilizador.  Se ainda não o fez, agora é o tempo para preencher o seu inquilino com alguns utilizadores.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), ou pode cloná-la a partir do GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```


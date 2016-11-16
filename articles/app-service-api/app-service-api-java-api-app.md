---
title: "Criar e implementar uma aplicação API Java no App Service do Azure"
description: "Saiba como criar um pacote de API Apps Java e implementá-lo no App Service do Azure."
services: app-service\api
documentationcenter: java
author: rmcmurray
manager: erikre
editor: tdykstra
ms.assetid: 8d21ba5f-fc57-4269-bc8f-2fcab936ec22
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: get-started-article
ms.date: 10/19/2016
ms.author: rachelap;robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b073958fd41fec05b473594b23b586d561e996f3


---
# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Criar e implementar uma aplicação API Java no App Service do Azure
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial mostra como criar uma aplicação Java e como implementá-la nas API Apps do App Service do Azure utilizando o [Git]. As instruções deste tutorial podem ser seguidas em qualquer sistema operativo capaz de executar Java. O código neste tutorial é criado com o [Maven]. O [Jax-RS] é utilizado para criar o serviço RESTful e é gerado com base na especificação dos metadados [Swagger] utilizando o [Swagger Editor].

## <a name="prerequisites"></a>Pré-requisitos
1. [Kit de Programação Java 8] \(ou posterior)
2. [Maven] instalado no seu computador de desenvolvimento
3. [Git] instalado no seu computador de desenvolvimento
4. Uma subscrição paga ou [avaliação gratuita] do [Microsoft Azure]
5. Um aplicação de teste HTTP como o [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Estruturar a API utilizando o Swagger.IO
Utilizando o editor online swagger.io, pode introduzir código Swagger JSON ou YAML que represente a estrutura da sua API. Assim que tiver a área de superfície da API concebida, pode exportar código para uma variedade de plataformas e estruturas. Na secção seguinte, o código estruturado será modificado para que inclua a funcionalidade mock. 

Esta demonstração irá começar com um corpo Swagger JSON irá colar no editor swagger.io, que em seguida será utilizado para gerar código utilizando o JAX RS para aceder a um ponto final de API REST. Em seguida, irá editar o código estruturado para devolver dados mock, simulando uma API REST incorporada visível num mecanismo de persistência de dados.  

1. Copie o seguinte código de Swagger JSON para a sua área de transferência:
   
        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }
2. Navegue para o [Online Swagger Editor]. Uma vez, clique no item de menu **Ficheiro -> Colar JSON**.
   
    ![Colar item do menu JSON][paste-json]
3. Cole no Swagger JSON da API da Lista de Contactos que copiou anteriormente. 
   
    ![Colar o código JSON no Swagger][pasted-swagger]
4. Ver as páginas de documentação e o resumo de API apresentados no editor. 
   
    ![Ver Documentos Gerados no Swagger][view-swagger-generated-docs]
5. Selecione a opção do menu **Gerar Servidor -> JAX RS** para estruturar o código do lado do servidor que irá editar mais tarde para adicionar a implementação de mock. 
   
    ![Item do Menu Gerar Código][generate-code-menu-item]
   
    Depois de o código ser gerado, receberá um ficheiro ZIP para transferir. Este ficheiro contém o código estruturado pelo gerador de código de Swagger e todos os scripts incorporados associados. Deszipe a biblioteca na totalidade para um diretório na sua estação de trabalho de desenvolvimento. 

## <a name="edit-the-code-to-add-api-implementation"></a>Editar o Código para adicionar a Implementação de API
Nesta secção, irá substituir a implementação do lado do servidor do código gerado pelo Swagger pelo código personalizado. O novo código irá devolver uma ArrayList de entidades de contacto para o cliente que efetua a chamada. 

1. Abra o ficheiro do modelo *Contact.java*, que está localizado na pasta *src/gen/java/io/swagger/model*, utilizando o [Visual Studio Code] ou o seu editor de texto preferido. 
   
    ![Abrir Ficheiro de Modelo de Contacto][open-contact-model-file]
2. Adicione o construtor seguinte à classe **Contacto**. 
   
        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }
3. Abra o ficheiro de implementação do serviço *ContactsApiServiceImpl.java*, que está localizado na pasta *src/main/java/io/swagger/api/impl*, utilizando o [Visual Studio Code] ou o seu editor de texto preferido.
   
    ![Abrir Ficheiro de Código do Serviço de Contacto][open-contact-service-code-file]
4. Substitua o código no ficheiro por este novo código para adicionar uma implementação mock ao código do serviço. 
   
        package io.swagger.api.impl;
   
        import io.swagger.api.*;
        import io.swagger.model.*;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;
   
        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
   
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
   
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
   
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
   
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }
5. Abra uma linha de comandos e altere o diretório para a pasta raiz da sua aplicação.
6. Execute o seguinte comando Maven para criar o código e executá-lo utilizando o servidor da aplicação Jetty localmente. 
   
        mvn package jetty:run
7. Deverá ver a janela de comandos refletir que a aplicação Jetty iniciou o código na porta 8080. 
   
    ![Abrir Ficheiro de Código do Serviço de Contacto][run-jetty-war]
8. Utilize o [Postman] para fazer um pedido ao método API "obter todos os contactos" em http://localhost:8080/api/contacts.
   
    ![Chamar a API Contactos][calling-contacts-api]
9. Utilize o [Postman] para fazer um pedido ao método API "obter contacto específico" em http://localhost:8080/api/contacts/2.
   
    ![Chamar a API Contactos][calling-specific-contact-api]
10. Por fim, crie o ficheiro de Java WAR (arquivo Web) executando o seguinte comando Maven na sua consola. 
    
         mvn package war:war
11. Depois de o ficheiro WAR ser criado, será colocado na pasta de **destino**. Navegue para a pasta de **destino** e mude o nome do ficheiro WAR para **ROOT.war**. (Certifique-se do que o uso de maiúsculas/minúsculas corresponde a este formato).
    
          rename swagger-jaxrs-server-1.0.0.war ROOT.war
12. Por fim, execute os seguintes comandos a partir da pasta raiz da sua aplicação para criar uma pasta **implementar** a utilizar para implementar o ficheiro WAR no Azure. 
    
          mkdir deploy
          mkdir deploy\webapps
          copy target\ROOT.war deploy\webapps
          cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publicar o resultado no App Service do Azure
Nesta secção irá aprender como criar uma nova aplicação API através do Portal do Azure, preparar essa aplicação API para alojar aplicações Java e implementar o ficheiro WAR recentemente criado no App Service do Azure para executar a nova Aplicação. 

1. Crie uma nova Aplicação API no [Portal do Azure], clicando no item de menu **Novo -> Web + Mobile -> Aplicação API**, introduzindo os detalhes da aplicação e, em seguida, clicando em **Criar**.
   
    ![Criar uma nova Aplicação API][create-api-app]
2. Quando tiver sido criada a sua aplicação API, abra o painel **Definições** da sua aplicação e, em seguida, clique no item de menu **Definições da aplicação**. Selecione as versões mais recentes do Java a partir das opções disponíveis, em seguida, selecione o Tomcat mais recente a partir do menu **Contentor Web** e, em seguida, clique em **Guardar**.
   
    ![Configurar o Java no painel Aplicação API][set-up-java]
3. Clique no item de menu de definições **Credenciais de implementação** e forneça um nome de utilizador e uma palavra-passe que pretenda utilizar para publicar os ficheiros na aplicação API. 
   
    ![Definir credenciais de implementação][deployment-credentials]
4. Clique no menu de definições **Origem da implementação**. Aqui, clique no botão **Escolher origem**, selecione a opção **Repositório de Git Local** e, em seguida, clique em **OK**. Esta ação cria um repositório de Git em execução no Azure, que tem uma associação à sua Aplicação API. Sempre que consolidar o código para o ramo *mestre* do repositório de Git, o código será publicado na sua instância da aplicação API em execução em direto. 
   
    ![Configurar um novo repositório de Git no local][select-git-repo]
5. Copie o URL do novo repositório de Git para a sua área de transferência. Guarde-o pois vai ser importante num determinado momento. 
   
    ![Configurar um novo repositório de Git para a sua aplicação][copy-git-repo-url]
6. Emita consolidações de Git do ficheiro WAR para o repositório online. Para tal, navegue para a pasta **implementar** que criou anteriormente, de modo a que possa facilmente consolidar o código para o repositório em execução no seu App Service. Assim que estiver na janela da consola e navegar para a pasta onde está localizada a pasta webapps, emita os seguintes comandos de Git para iniciar o processo e desativar uma implementação. 
   
         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master
   
    Assim que emite o pedido de **emissão**, ser-lhe-á pedida a palavra-passe que criou anteriormente para a credencial de implementação. Depois de introduzir as suas credenciais, deverá ver o seu portal a informar que a atualização foi implementada.
7. Se utilizar novamente o Postman para aceder à aplicação API recentemente implementada em execução no App Service do Azure, verá que o comportamento é consistente e que agora está a devolver dados de contacto conforme esperado e a utilizar as alterações de código simples para o código de Java estruturado Swagger.io. 
   
    ![Utilizar a API REST dos Contactos do Java em direto no Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Passos seguintes
Neste artigo, conseguiu iniciar com um ficheiro Swagger JSON e algum código Java estruturado obtido a partir do editor Swagger.io. A partir daí, as suas alterações simples e um processo de implementação de Git resultou numa aplicação API funcional escrita em Java. O próximo tutorial mostra como [consumir API Apps a partir de clientes JavaScript, utilizando a CORS][CORS da API do App Service]. Tutoriais posteriores na série mostram como implementar a autenticação e a autorização.

Para criar esta amostra, pode saber mais sobre o [SDK de Armazenamento para Java] para blobs JSON. Ou, pode utilizar o [Document DB Java SDK] para guardar os seus dados de contacto no Azure Document DB. 

<a name="see-also"></a>

## <a name="see-also"></a>Veja também
Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores do Java do Azure].

<!-- URL List -->

[CORS da API do App Service]: app-service-api-cors-consume-javascript.md
[Portal do Azure]: https://portal.azure.com/
[Document DB Java SDK]: ../documentdb/documentdb-java-application.md
[avaliação gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Centro de Programadores Java do Azure]: /develop/java/
[Kit de Programação Java 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Online Swagger Editor]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[SDK de Armazenamento para Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Swagger Editor]: http://editor.swagger.io/
[Visual Studio Code]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png



<!--HONumber=Nov16_HO2-->



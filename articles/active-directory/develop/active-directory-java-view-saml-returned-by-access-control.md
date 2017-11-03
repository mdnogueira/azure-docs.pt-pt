---
title: "Vista de SAML devolvido pelo serviço de controlo de acesso (Java)"
description: "Saiba como ver SAML devolvido pelo serviço de controlo de acesso em aplicações de Java alojadas no Azure."
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: 1552e624a4703138ab82f7133ceaec3dbd04e1db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Como ver SAML devolvido pelo serviço de controlo de acesso do Azure
Este guia irá mostrar como visualizar o subjacente Security Assertion Markup Language (SAML) devolvida para a aplicação por acesso Control Service (ACS) do Azure. O guia baseia-se a [como autenticar utilizadores Web com o Eclipse de através de serviço de controlo de acesso do Azure](active-directory-java-authenticate-users-access-control-eclipse.md) tópico, fornecendo o código que apresenta as informações de SAML. A aplicação concluída terá um aspeto semelhante ao seguinte.

![Exemplo de saída SAML][saml_output]

Para obter mais informações sobre ACS, consulte o [passos](#next_steps) secção.

> [!NOTE]
> O filtro de controlo de serviços de acesso do Azure é uma versão de pré-visualização de tecnologia de Comunidade. Como o software de pré-lançamento, não é formally suportado pela Microsoft.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir as tarefas neste guia, execute o exemplo em [como autenticar utilizadores Web com o Eclipse de através de serviço de controlo de acesso do Azure](active-directory-java-authenticate-users-access-control-eclipse.md) e utilizá-lo como o ponto de partida para este tutorial.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Adicionar a biblioteca de JspWriter para a assemblagem de caminho e implementação de compilação
Adicionar a biblioteca que contém o **javax.servlet.jsp.JspWriter** classe para a assemblagem de compilação e implementação de caminho. Se estiver a utilizar o Tomcat, a biblioteca está **jsp api.jar**, que está localizado no Apache **lib** pasta.

1. No Explorador de projeto do Eclipse, clique com botão direito **MyACSHelloWorld**, clique em **criar caminho**, clique em **configurar criar caminho**, clique em de **bibliotecas**separador e, em seguida, clique em **adicionar JARs externos**.
2. No **JAR seleção** caixa de diálogo, navegue para o JAR necessária, selecione-o e, em seguida, clique em **abra**.
3. Com o **propriedades MyACSHelloWorld** clique ainda aberta caixa de diálogo **implementação assemblagem**.
4. No **assemblagem de implementação Web** caixa de diálogo, clique em **adicionar**.
5. No **novo diretiva de assemblagem** caixa de diálogo, clique em **entradas de caminho de compilação de Java** e, em seguida, clique em **seguinte**.
6. Selecione a biblioteca adequada e clique em **concluir**.
7. Clique em **OK** para fechar o **propriedades MyACSHelloWorld** caixa de diálogo.

## <a name="modify-the-jsp-file-to-display-saml"></a>Modificar o ficheiro JSP para apresentar SAML
Modificar **index.jsp** para utilizar o seguinte código.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>Executar a aplicação
1. Execute a sua aplicação no emulador do computador ou implementar no Azure, utilizando os passos documentados no [como autenticar utilizadores Web com o Eclipse de através de serviço de controlo de acesso do Azure](active-directory-java-authenticate-users-access-control-eclipse.md).
2. Iniciar um browser e abra a aplicação web. Depois de iniciar sessão sua aplicação, irá ver informações de SAML, incluindo a asserção de segurança fornecida pelo fornecedor de identidade.

## <a name="next-steps"></a>Passos seguintes
Para obter mais explorar a funcionalidade do ACS e para testar cenários mais sofisticados, consulte [2.0 de serviço de controlo de acesso][Access Control Service 2.0].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png

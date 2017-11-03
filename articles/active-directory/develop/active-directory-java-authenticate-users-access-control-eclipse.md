---
title: Como utilizar o controlo de acesso (Java) | Microsoft Docs
description: Saiba como desenvolver e utilizar o controlo de acesso com o Java no Azure.
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: 698403d181e1fee09bb4692290c92203ded97ba4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Como autenticar os utilizadores do Web com o serviço de controlo de acesso do Azure utilizando o Eclipse
Este guia irá mostrar como utilizar o serviço de controlo de acesso (ACS) do Azure dentro do Toolkit do Azure para o Eclipse. Para obter mais informações sobre ACS, consulte o [passos](#next_steps) secção.

> [!NOTE]
> O filtro de controlo de serviços de acesso do Azure é uma versão de pré-visualização de tecnologia de Comunidade. Como o software de pré-lançamento, não é formally suportado pela Microsoft.
> 
> 

## <a name="what-is-acs"></a>O que é ACS?
A maioria dos programadores não são especialistas de identidade e, geralmente, não pretendem gastam mecanismos de autenticação e autorização desenvolvimento no tempo para os serviços e aplicações. ACS é um serviço Azure que fornece uma forma fácil de autenticar utilizadores que necessitam de aceder aos seus serviços e aplicações web sem ter de lógica complexa de autenticação de fator para o seu código.

As seguintes funcionalidades estão disponíveis em ACS:

* Integração com o Windows Identity Foundation (WIF).
* Suporte para fornecedores de identidade de web populares (IPs), incluindo Windows Live ID, Google, Yahoo! e o Facebook.
* Suporte para serviços de Federação do Active Directory (AD FS) 2.0.
* Um Open Data Protocol (OData)-com base em serviço de gestão que fornece acesso programático às definições de ACS.
* Um Portal de gestão que permite o acesso administrativo às definições de ACS.

Para mais informações sobre ACS, consulte [2.0 de serviço de controlo de acesso][Access Control Service 2.0].

## <a name="concepts"></a>Conceitos
Azure ACS baseia-se os princípios de identidade baseada em afirmações - uma abordagem consistente para criar os mecanismos de autenticação para aplicações em execução no local ou na nuvem. Identidade baseada em afirmações fornece uma forma comum de aplicações e serviços para adquirir as informações de identidade têm sobre os utilizadores dentro da respetiva organização, em outras organizações e na Internet.

Para concluir as tarefas neste guia, deve compreender os seguintes conceitos:

**Cliente** -no contexto deste guia de procedimentos, este é um browser que está a tentar obter acesso à sua aplicação web.

**Aplicação de terceiros (RP) entidade confiadora** -aplicação RP um é um Web site ou serviço que outsources autenticação para uma autoridade de externa. Jargon de identidade, podemos dizer o que o RP confia nessa autoridade. Este guia explica como configurar a sua aplicação confiança dos ACS.

**Token** -um token é uma coleção de dados de segurança que, normalmente, são emitidos após a autenticação com êxito de um utilizador. Contém um conjunto de *afirmações*, atributos de utilizador autenticado. Uma afirmação pode representar o nome de utilizador, um identificador para uma função de um utilizador pertence a um utilizador idade e assim sucessivamente. Um token está assinado digitalmente normalmente, que significa que pode sempre ser obtido para o respetivo emissor e respetivo conteúdo não pode ser adulterado. Um utilizador obtém acesso a uma aplicação RP através da apresentação de um token válido emitido por uma autoridade que confie na aplicação RP.

**Fornecedor de identidade (IP)** -um IP é uma autoridade de que efetua a autenticação de identidades de utilizador e emite tokens de segurança. O trabalho real de emissão de tokens é implementado, apesar de um serviço de especial denominado serviço de Token de segurança (STS). Exemplos típicos de IPs incluem Windows Live ID, Facebook, repositórios de utilizador de negócio (como o Active Directory) e assim sucessivamente.
Quando o ACS estiver configurado para confiar um IP, o sistema irá aceitar e validar os tokens emitidos por essa IP. ACS pode confiar vários IPs de uma só vez, que significa que quando a aplicação confianças ACS, pode instantaneamente oferecer a aplicação para todos os utilizadores autenticados todos os IPs confianças de ACS em seu nome.

**Fornecedor de Federação (FP)** -IPs têm conhecimento direto de utilizadores e autenticá-los utilizando as suas credenciais e emitir as afirmações sobre o que já estão habituados sobre os mesmos. Um fornecedor de Federação (FP) é um tipo diferente da autoridade: em vez de autenticar os utilizadores diretamente, atua como um intermediário e os corretores a autenticação entre um RP e um ou mais IPs. IPs e FPs emitem tokens de segurança, por conseguinte, podem ambos utilizar serviços de Token de segurança (STS). ACS é um FP.

**O motor de regra de ACS** -a lógica utilizada para transformar tokens recebidos a partir de IPs fidedignos para tokens se destinam a ser consumidos por no RP é codified no formulário de regras de transformação de afirmações simples. ACS funcionalidades um motor de regra encarrega-se aplicar a qualquer lógica de transformação especificada para a sua RP.

**Espaço de nomes do ACS** -um espaço de nomes é uma partição de nível superior do ACS que utilizar para organizar as suas definições. Um espaço de nomes contém uma lista de IPs confiança, as aplicações de RP pretende servir, as regras que espera que a regra de motor para processar os tokens recebidos com e assim sucessivamente. Um espaço de nomes expõe vários pontos finais que serão utilizados pela aplicação e o programador para obter ACS efetue a respetiva função.

A figura seguinte mostra como funciona a autenticação de ACS com uma aplicação web:

![Diagrama de fluxo de ACS][acs_flow]

1. O cliente (neste caso um browser) pede uma página no RP.
2. Uma vez que o pedido não é ainda autenticado, o RP redireciona o utilizador para a autoridade que confia, que é dos ACS. O ACS apresenta o utilizador com a opção de IPs que foram especificados para esta RP. O utilizador seleciona o IP adequado.
3. O cliente procura para a página de autenticação de IP e pede ao utilizador para iniciar sessão.
4. Depois do cliente é autenticado (por exemplo, a identidade as credenciais são introduzidas), o IP emite um token de segurança.
5. Depois de emitir um token de segurança, o IP redireciona o cliente para ACS e o cliente envia o token de segurança emitido por IP para ACS.
6. ACS valida o token de segurança emitido por IP, entradas a identidade de afirmações neste token para o motor de regras de ACS, calcula as afirmações de identidade de saída e emite um novo token de segurança que contenha estas afirmações de saída.
7. ACS redireciona o cliente para o RP. O cliente envia o novo token de segurança emitido por ACS no RP. O RP valida a assinatura no token de segurança emitido por ACS, valida as afirmações neste token e devolve a página que foram originalmente pedida.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir as tarefas neste guia, irá precisar do seguinte:

* Um Java Developer Kit (JDK), v 1.6 ou posterior.
* IDE Eclipse para programadores de Java EE, Indigo ou posterior. Isto pode ser transferido a partir do <http://www.eclipse.org/downloads/>. 
* Distribuição de um servidor web com base em Java ou o servidor de aplicações, tais como o Apache Tomcat, GlassFish, servidor de aplicações JBoss ou Jetty.
* uma subscrição do Azure, o que é possível adquirir de <http://www.microsoft.com/windowsazure/offers/>.
* A versão de Abril de 2014 do Toolkit do Azure para o Eclipse, ou posterior. Para obter mais informações, consulte [instalar o Toolkit do Azure para o Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Um certificado x. 509 para utilizar com a sua aplicação. Terá deste certificado no certificado público (. cer) e Personal Information Exchange (. Formato PFX). (Opções para criar este certificado serão descritas mais tarde neste tutorial).
* Familiaridade com o Azure computação emulador e implementação técnicas abordadas em [criação de uma aplicação de Olá mundo do Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Criar um espaço de nomes do ACS
Para começar a utilizar o serviço de controlo de acesso (ACS) no Azure, tem de criar um espaço de nomes do ACS. O espaço de nomes fornece um âmbito único para endereçamento recursos ACS a partir de dentro da sua aplicação.

1. Inicie sessão no [Portal de gestão do Azure][Azure Management Portal].
2. Clique em **do Active Directory**. 
3. Para criar um novo espaço de nomes do controlo de acesso, clique em **novo**, clique em **serviços aplicacionais**, clique em **controlo de acesso**e, em seguida, clique em **criação rápida**. 
4. Introduza um nome para o espaço de nomes. Azure verifica se o nome é exclusivo.
5. Selecione a região em que é utilizado o espaço de nomes. Para obter o melhor desempenho, utilize a região na qual está a implementar a aplicação.
6. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar para o espaço de nomes do ACS.
7. Clique em **Criar**.

O Azure cria e ativa o espaço de nomes. Aguarde até que o estado do novo espaço de nomes é **Active Directory** antes de continuar. 

## <a name="add-identity-providers"></a>Adicionar fornecedores de identidade
Nesta tarefa, adicione IPs a utilizar com a sua aplicação RP para autenticação. Para fins de demonstração, esta tarefa mostra como adicionar o Windows Live como um IP, mas pode utilizar qualquer um dos IPs listado no Portal de gestão dos ACS.

1. No [Azure Management Portal][Azure Management Portal], clique em **do Active Directory**, selecione um espaço de nomes do controlo de acesso e, em seguida, clique em **gerir**. Abre o Portal de gestão dos ACS.
2. No painel de navegação esquerdo do Portal de gestão dos ACS, clique em **fornecedores de identidade**.
3. Windows Live ID está ativada por predefinição e não pode ser eliminada. Para efeitos deste tutorial, é utilizada apenas o Windows Live ID. No entanto, este ecrã é onde pode adicionar outros IPs, clicando a **adicionar** botão.

Windows Live ID está agora ativado como um IP para o espaço de nomes do ACS. Em seguida, especificar a aplicação web de Java (para ser criado posteriormente) como um RP.

## <a name="add-a-relying-party-application"></a>Adicionar uma aplicação da entidade confiadora
Nesta tarefa, pode configurar ACS reconheça a aplicação web de Java como uma aplicação RP válida.

1. No Portal de gestão dos ACS, clique em **aplicações de fornecedores de entidade Confiadora**.
2. No **aplicações de terceiros de entidade Confiadora** página, clique em **adicionar**.
3. No **Adicionar aplicação da entidade Confiadora** página, efetue o seguinte:
   
   1. No **nome**, escreva o nome no RP. Para efeitos deste tutorial, escreva **aplicação Web do Azure**.
   2. No **modo**, selecione **introduzir as definições manualmente**.
   3. No **Realm**, escreva o URI aos quais se aplica o token de segurança emitido por ACS. Para esta tarefa, escreva **http://localhost:8080 /**.
      ![Realm de entidade confiadora para utilização no emulador de computação][relying_party_realm_emulator]
   4. No **devolver URL,** escreva o URL ao qual os ACS devolve o token de segurança. Para esta tarefa, escreva **http://localhost:8080/MyACSHelloWorld/index.jsp**
      ![URL de retorno de terceiros de entidade Confiadora para utilização no emulador de computação][relying_party_return_url_emulator]
   5. Aceite os valores predefinidos o resto dos campos.
4. Clique em **Guardar**.

Agora configurou com êxito a aplicação web de Java quando for executada no emulador de computação do Azure (no http://localhost:8080 /) para ser um RP no seu espaço de nomes do ACS. Em seguida, crie as regras que ACS utiliza para processar afirmações para o RP.

## <a name="create-rules"></a>Criar regras
Nesta tarefa, pode definir as regras que unidade como afirmações são transmitidas IPs para o RP. Com o objetivo deste guia, iremos irá configurar simplesmente ACS para copiar o tipos de afirmação de entrada e valores diretamente no token de saída, sem filtragem ou modifiquem.

1. Na página principal do Portal de gestão de ACS, clique em **regra grupos**.
2. No **regra grupos** página, clique em **grupo de regras predefinidas para a aplicação Web do Azure**.
3. No **Editar grupo de regras** página, clique em **gerar**.
4. No **gerar regras: grupo de regras predefinidas para a aplicação Web do Azure** página, certifique-se de que é verificado Windows Live ID e, em seguida, clique em **gerar**.    
5. No **Editar grupo de regras** página, clique em **guardar**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Carregue um certificado para o seu espaço de nomes do ACS
Nesta tarefa, carregar um. Certificado PFX que será utilizado para assinar pedidos de token criados pelo seu espaço de nomes do ACS.

1. Na página principal do Portal de gestão de ACS, clique em **certificados e chaves**.
2. No **certificados e chaves** página, clique em **adicionar** acima **assinatura de tokens**.
3. No **adicionar Token de assinatura de certificado ou chave** página:
   1. No **utilizado para** secção, clique em **aplicações de terceiros de entidade Confiadora** e selecione **aplicação Web do Azure** (que anteriormente definido como o nome da sua aplicação da entidade confiadora).
   2. No **tipo** secção, selecione **certificado x. 509**.
   3. No **certificado** secção, clique no botão Procurar e navegue para o ficheiro de certificado x. 509 que pretende utilizar. Este será um. Ficheiro PFX. Selecione o ficheiro, clique em **abra**e, em seguida, introduza a palavra-passe do certificado no **palavra-passe** caixa de texto. Tenha em atenção que, para fins de teste, pode utilizar um self-signed-certificado. Para criar um certificado autoassinado, utilize o **novo** clique no botão no **biblioteca de filtro de ACS** diálogo (descrito mais tarde) ou utilize o **encutil.exe** utilitário do [Web site do projeto] [ project website] do Kit de arranque do Azure para Java.
   4. Certifique-se de que **tornar primário** está marcada. O **adicionar Token de assinatura de certificado ou chave** página deve ter um aspeto semelhante ao seguinte.
       ![Adicionar o certificado de assinatura de tokens][add_token_signing_cert]
   5. Clique em **guardar** para guardar as definições e fechar o **adicionar Token de assinatura de certificado ou chave** página.

Em seguida, reveja as informações na página de integração de aplicação e copie o URI que terá de configurar a sua aplicação web de Java para utilizar ACS.

## <a name="review-the-application-integration-page"></a>Reveja a página de integração de aplicações
Para encontrar todas as informações e o código necessário para configurar a sua aplicação web de Java (a aplicação RP) para trabalhar com ACS na página de integração de aplicações do Portal de gestão dos ACS. Estas informações serão necessárias quando configurar a sua aplicação web de Java para a autenticação federada.

1. No Portal de gestão dos ACS, clique em **integração de aplicações**.  
2. No **integração de aplicações** página, clique em **páginas de início de sessão**.
3. No **integração da página de início de sessão** página, clique em **aplicação Web do Azure**.

No **integração da página de início de sessão: aplicação Web do Azure** página, ao URL listado no **opção 1: ligação para uma página de início de sessão alojadas em ACS** vão ser utilizados na sua aplicação web de Java. Este valor é necessário quando adicionar a biblioteca de filtro de serviços de controlo de acesso do Azure para a aplicação de Java.

## <a name="create-a-java-web-application"></a>Criar uma aplicação web de Java
1. No Eclipse, no menu, clique **ficheiro**, clique em **novo**e, em seguida, clique em **Dynamic Web Project**. (Se não vir **Dynamic Web Project** listado como um projeto disponível depois de clicar em **ficheiro**, **novo**, em seguida, efetue o seguinte: clique em **ficheiro**, clique em **novo**, clique em **projeto**, expanda **Web**, clique em **Dynamic Web Project**e clique em **seguinte**.) Para efeitos deste tutorial, nome de projeto **MyACSHelloWorld**. (Certifique-se de utilizar este nome, os passos subsequentes no tutorial esperam o ficheiro WAR para ter o nome MyACSHelloWorld). O ecrã do seu aparecerá semelhante ao seguinte:
   
    ![Criar um projeto de Olá, mundo para ACS exampple][create_acs_hello_world]
   
    Clique em **Concluir**.
2. Na vista do Explorador de projeto do Eclipse, expanda **MyACSHelloWorld**. Com o botão direito, faça um clique em **WebContent**, clique em **Novo**, e, em seguida, clique em **Ficheiro JSP**.
3. No **novo ficheiro JSP** caixa de diálogo, o nome de ficheiro **index.jsp**. Mantenha a pasta principal MyACSHelloWorld/WebContent, conforme mostrado no seguinte:
   
    ![Adicione um ficheiro JSP, por exemplo de ACS][add_jsp_file_acs]
   
    Clique em **Seguinte**.
4. No **selecionar modelo de JSP** caixa de diálogo, selecione **novo ficheiro JSP (html)** e clique em **concluir**.
5. Quando abre o ficheiro index.jsp no Eclipse, adicione o texto a apresentar **Olá ACS, mundo!** dentro do elemento `<body>` existente. O atualizado `<body>` conteúdo deverá aparecer da seguinte forma:
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    Guarde o index.jsp.

## <a name="add-the-acs-filter-library-to-your-application"></a>Adicionar a biblioteca de filtro de ACS à sua aplicação
1. No Explorador de projeto do Eclipse, clique com botão direito **MyACSHelloWorld**, clique em **criar caminho**e, em seguida, clique em **configurar criar caminho**.
2. No **criar caminho Java** caixa de diálogo, clique em de **bibliotecas** separador.
3. Clique em **Adicionar biblioteca**.
4. Clique em **filtrar serviços de controlo de acesso do Azure (por técnico de abrir MS)** e, em seguida, clique em **seguinte**. O **filtrar de serviços de controlo de acesso do Azure** é apresentada a caixa de diálogo.  (O **localização** campo pode ter um caminho diferente, dependendo de onde instalou o Eclipse, e o número de versão pode ser diferente, consoante as atualizações de software.)
   
    ![Adicionar a biblioteca de filtro de ACS][add_acs_filter_lib]
5. Utilizando um browser aberta para o **integração da página de início de sessão** página do Portal de gestão, copiar o URL listado no **opção 1: ligação para uma página de início de sessão alojadas em ACS** campo e cole-o para o **ponto final de autenticação de ACS** campo da caixa de diálogo Eclipse.
6. Utilizando um browser aberta para o **editar aplicação da entidade Confiadora** página do Portal de gestão, copiar o URL listado no **Realm** campo e cole-o para o **Realm de entidade Confiadora** campo da caixa de diálogo Eclipse.
7. Dentro do **segurança** secção da caixa de diálogo Eclipse, se pretender utilizar um certificado existente, clique em **procurar**, navegue para o certificado que pretende utilizar, selecione-o e clique em **abra**. Ou, se pretender criar um novo certificado, clique em **novo** para apresentar o **novo certificado** caixa de diálogo, em seguida, especifique a palavra-passe, o nome do ficheiro. cer e o nome do ficheiro. pfx para o novo certificado.
8. Verifique **incorporar o certificado com o ficheiro WAR**. Ao incorporar o certificado desta forma inclui-lo na sua implementação sem necessidade de adicionar manualmente como um componente. (Se em vez disso, tem de armazenar o certificado externamente do ficheiro WAR, pode adicionar o certificado como um componente de função e desmarque **incorporar o certificado com o ficheiro WAR**.)
9. [Opcional] Manter **ligações HTTPS requerem** marcada. Se definir esta opção, terá de aceder à aplicação utilizando o protocolo HTTPS. Se não quiser que necessite de ligações de HTTPS, desmarque esta opção.
10. Para uma implementação para o emulador de computação, o **Azure ACS filtro** definições terá um aspeto semelhantes ao seguinte.
    
    ![Definições de filtro de ACS do Azure para uma implementação para o emulador de computação][add_acs_filter_lib_emulator]
11. Clique em **Concluir**.
12. Clique em **Sim** quando apresentado com uma caixa de diálogo a indicar que um ficheiro web.xml será criado.
13. Clique em **OK** para fechar o **criar caminho Java** caixa de diálogo.

## <a name="deploy-to-the-compute-emulator"></a>Implementar o emulador de computação
1. No Explorador de projeto do Eclipse, clique com botão direito **MyACSHelloWorld**, clique em **Azure**e, em seguida, clique em **pacote para o Azure**.
2. Para **nome do projeto**, tipo **MyAzureACSProject** e clique em **seguinte**.
3. Selecione um JDK e servidor de aplicação. (Estes passos descritos detalhadamente no [criação de uma aplicação de Olá mundo do Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) tutorial).
4. Clique em **Concluir**.
5. Clique em de **executado no emulador do Azure** botão.
6. Depois de inicia a aplicação web de Java no emulador de computação, feche todas as instâncias do seu browser (de modo a que qualquer sessões do browser atual não interferem com o teste de início de sessão de ACS).
7. Execute a sua aplicação, abrindo <http://localhost:8080/MyACSHelloWorld/> no browser (ou <https://localhost:8080/MyACSHelloWorld/> se tiver selecionado **ligações HTTPS requerem**). Deve ser-lhe pedido para um início de sessão do Windows Live ID, em seguida, deve ser direcionado para o URL de regresso especificado para a sua aplicação da entidade confiadora.
8. Quando terminar de visualizar a sua aplicação, clique em de **repor o emulador de Azure** botão.

## <a name="deploy-to-azure"></a>Implementar no Azure
Para implementar no Azure, terá de alterar o realm de entidade confiadora e devolver o URL para o espaço de nomes do ACS.

1. No Portal de gestão do Azure, no **editar aplicação da entidade Confiadora** página, modifique **Realm** para ser o URL do seu site implementado. Substitua **exemplo** com o nome DNS especificado para a sua implementação.
   
    ![Realm de entidade confiadora para utilização em produção][relying_party_realm_production]
2. Modificar **devolver URL** para ser o URL da aplicação. Substitua **exemplo** com o nome DNS especificado para a sua implementação.
   
    ![Entidade confiadora URL de retorno de terceiros para utilização em produção][relying_party_return_url_production]
3. Clique em **guardar** para guardar a parte das realm de terceiros atualizado e voltar a alterações de URL.
4. Manter o **integração da página de início de sessão** página aberto no seu browser, será necessário copiá-los em breve.
5. No Explorador de projeto do Eclipse, clique com botão direito **MyACSHelloWorld**, clique em **criar caminho**e, em seguida, clique em **configurar criar caminho**.
6. Clique em de **bibliotecas** separador, clique em **filtrar de serviços de controlo de acesso do Azure**e, em seguida, clique em **editar**.
7. Utilizando um browser aberta para o **integração da página de início de sessão** página do Portal de gestão, copiar o URL listado no **opção 1: ligação para uma página de início de sessão alojadas em ACS** campo e cole-o para o **ponto final de autenticação de ACS** campo da caixa de diálogo Eclipse.
8. Utilizando um browser aberta para o **editar aplicação da entidade Confiadora** página do Portal de gestão, copiar o URL listado no **Realm** campo e cole-o para o **Realm de entidade Confiadora** campo da caixa de diálogo Eclipse.
9. Dentro do **segurança** secção da caixa de diálogo Eclipse, se pretender utilizar um certificado existente, clique em **procurar**, navegue para o certificado que pretende utilizar, selecione-o e clique em **abra**. Ou, se pretender criar um novo certificado, clique em **novo** para apresentar o **novo certificado** caixa de diálogo, em seguida, especifique a palavra-passe, o nome do ficheiro. cer e o nome do ficheiro. pfx para o novo certificado.
10. Manter **incorporar o certificado com o ficheiro WAR** opção estiver marcada, partindo do princípio de que pretende incorporar o certificado com o ficheiro WAR.
11. [Opcional] Manter **ligações HTTPS requerem** marcada. Se definir esta opção, terá de aceder à aplicação utilizando o protocolo HTTPS. Se não quiser que necessite de ligações de HTTPS, desmarque esta opção.
12. Para uma implementação para o Azure, as definições de filtro de ACS de Azure terá um aspeto semelhantes ao seguinte.
    
    ![Definições de filtro de ACS do Azure para uma implementação de produção][add_acs_filter_lib_production]
13. Clique em **concluir** para fechar o **editar biblioteca** caixa de diálogo.
14. Clique em **OK** para fechar o **propriedades MyACSHelloWorld** caixa de diálogo.
15. No Eclipse, clique em de **publicar para a nuvem do Azure** botão. Responder a pedidos, semelhantes como feito **para implementar a aplicação no Azure** secção o [criação de uma aplicação de Olá mundo do Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) tópico. 

Após ter sido implementada a sua aplicação web, fechar as sessões do browser abrir, execute a sua aplicação web e deve ser-lhe pedido para iniciar sessão com credenciais do Windows Live ID, seguidas de que está a ser enviados para o URL de retorno da sua aplicação da entidade confiadora.

Quando tiver concluído a utilizar a sua aplicação de ACS Olá, mundo, lembre-se ao eliminar a implementação (pode aprender como eliminar uma implementação no [criação de uma aplicação de Olá mundo do Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) tópico).

## <a name="next_steps"></a>Passos seguintes
Para um serem examinados de Security Assertion Markup Language (SAML) devolvido por ACS à sua aplicação, consulte [como ver SAML devolvido pelo serviço de controlo de acesso do Azure][How to view SAML returned by the Azure Access Control Service]. Para obter mais explorar a funcionalidade do ACS e para testar cenários mais sofisticados, consulte [2.0 de serviço de controlo de acesso][Access Control Service 2.0].

Além disso, este exemplo utilizado o **incorporar o certificado com o ficheiro WAR** opção. Esta opção faz com que simples de implementar o certificado. Se em vez disso, pretende manter o seu certificado de assinatura separado do ficheiro WAR, pode utilizar o seguinte método:

1. Dentro do **segurança** secção o **filtrar de serviços de controlo de acesso do Azure** caixa de diálogo, escreva **${env. JAVA_HOME}/MyCert.cer** e desmarque **incorporar o certificado com o ficheiro WAR**. (Ajustar mycert.cer se o nome de ficheiro de certificado for diferente.) Clique em **concluir** para fechar a caixa de diálogo.
2. Copiar o certificado como um componente na sua implementação: no Explorador de projeto do Eclipse, expanda **MyAzureACSProject**, faça duplo clique **WorkerRole1**, clique em **propriedades**, expanda **funções do Azure**e clique em **componentes**.
3. Clique em **Adicionar**.
4. Dentro do **adicionar componentes** diálogo:
   
   1. No **importação** secção:
      1. Utilize o **ficheiro** botão para navegar para o certificado que pretende utilizar. 
      2. Para **método**, selecione **cópia**.
   2. Para **como nome**, clique na caixa de texto e aceite o nome predefinido.
   3. No **implementar** secção:
      1. Para **método**, selecione **cópia**.
      2. Para **ao diretório**, tipo **% JAVA_HOME %**.
   4. O **adicionar componentes** diálogo deve ter um aspeto semelhante ao seguinte.
      
       ![Adicionar componente de certificado][add_cert_component]
   5. Clique em **OK**.

Neste momento, o certificado deverá ser incluído na sua implementação. Tenha em atenção que independentemente se o certificado de incorporação no ficheiro WAR ou adicioná-lo como um componente à sua implementação, terá de carregar o certificado para o espaço de nomes, conforme descrito no [carregue um certificado para o seu espaço de nomes do ACS] [ Upload a certificate to your ACS namespace] secção.

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: active-directory-java-view-saml-returned-by-access-control.md
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png


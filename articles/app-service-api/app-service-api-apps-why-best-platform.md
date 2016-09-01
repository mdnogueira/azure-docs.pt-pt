<properties 
    pageTitle="Introdução às API Apps | Microsoft Azure" 
    description="Saiba como o App Service do Azure o pode ajudar a desenvolver, alojar e consumir APIs RESTful." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/23/2016" 
    ms.author="rachelap"/>

# Descrição geral de API Apps

As API Apps no App Service do Azure oferecem funcionalidades que tornam mais fácil desenvolver, alojar e consumir APIs na nuvem e no local. Com as API Apps obtém segurança de nível empresarial, controlo de acesso simples, conectividade híbrida, geração automática do SDK e integração otimizada com as [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

O [App Service do Azure](../app-service/app-service-value-prop-what-is.md) é uma plataforma completamente gerida para cenários Web, móveis e de integração. As API Apps é um dos quatro tipos de aplicação fornecidos pelo [App Service do Azure](../app-service/app-service-value-prop-what-is.md).

![Tipos de aplicação no App Service do Azure](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## Porquê utilizar API Apps?

Seguem-se algumas funcionalidades-chave das API Apps:

- **Colocar a sua API existente como está** - não é necessário alterar qualquer um dos códigos na suas APIs existentes para tirar partido das API Apps – basta implementar o seu código numa aplicação API. A API pode utilizar qualquer linguagem ou arquitetura suportada pelo App Service, incluindo ASP.NET e c#, Java, PHP, Node.js e Python.

- **Consumo fácil** – o suporte integrado para [metadados API Swagger](http://swagger.io/) torna as suas APIs facilmente consumíveis por uma variedade de clientes.  Gere automaticamente o código de cliente para as suas APIs em diversas linguagens, incluindo c#, Java e Javascript. Configure facilmente [CORS](app-service-api-cors-consume-javascript.md) sem alterar o seu código. Para obter mais informações, consulte o artigo [Metadados de API Apps do App Service para geração de código e deteção de API](app-service-api-metadata.md) e [Consumir uma aplicação API da JavaScript utilizando CORS](app-service-api-cors-consume-javascript.md). 

- **Controlo de acesso simples** - proteja uma aplicação API contra o acesso não autenticado sem alterações do seu código. Os serviços de autenticação incorporados protegem as APIs do acesso por outros serviços ou por clientes que representam os utilizadores. Os fornecedores de identidade suportados incluem o Azure Active Directory, Facebook, Twitter, Google e Conta Microsoft. Os clientes podem utilizar a Biblioteca de Autenticação do Active Directory (ADAL) ou o SDK de aplicações móveis. Para obter mais informações, consulte o artigo [Autenticação e autorização para API Apps no App Service do Azure](app-service-api-authentication.md).

- **Integração do Visual Studio** – as ferramentas dedicadas do Visual Studio simplificam o trabalho de criar, implementar, consumir, depurar e gerir API Apps. Para obter mais informações, consulte o artigo [Anúncio do Azure SDK 2.8.1 para .NET](/blog/announcing-azure-sdk-2-8-1-for-net/).

- **Integração com Logic Apps** – as API Apps que criar podem ser consumidas pelas [Logic Apps do App Service](../app-service-logic/app-service-logic-what-are-logic-apps.md).  Para obter mais informações, consulte o artigo [Utilizar a API personalizada alojada no App Service com as Logic Apps](../app-service-logic/app-service-logic-custom-hosted-api.md) e [Nova versão do esquema de pré-visualização 2015-08-01](../app-service-logic/app-service-logic-schema-2015-08-01.md).

Além disso, uma aplicação API pode tirar partido das funcionalidades oferecidas pelas [Web Apps](../app-service-web/app-service-web-overview.md) e [Aplicações Móveis](../app-service-mobile/app-service-mobile-value-prop.md). Também se aplica a reversão: se utilizar uma aplicação Web ou aplicação móvel para alojar uma API, pode tirar partido das funcionalidades das API Apps, tais como, os metadados Swagger para geração de código do cliente e o CORS para acesso ao browser entre domínios. A única diferença entre os três tipos de aplicações (API, Web, móvel) é o nome e o ícone utilizado para as mesmos no Portal do Azure.

## Qual é a diferença entre as API Apps e a API Management do Azure?

As API Apps e a [API Management do Azure](../api-management/api-management-key-concepts.md) são serviços complementares:

* A API Management está relacionada com a gestão de APIs. Coloca um front-end de API Management numa API para monitorizar e limitar a utilização, manipular a entrada e saída, consolidar várias APIs num ponto final e assim sucessivamente. As APIs que estão a ser geridas podem ser alojadas em qualquer lugar.
* API Apps refere-se ao alojamento de APIs. O serviço inclui funcionalidades que facilitam o desenvolvimento e o consumo de APIs, mas não faz os tipos de monitorização, limitação, manipulação ou consolidação que a API Management faz. Se não precisa das funcionalidades da API Management, pode alojar APIs nas API Apps sem utilizar a API Management.

Vejamos um diagrama que ilustra a API Management utilizada para APIs alojadas nas API Apps e noutro local.

![API Management e API Apps do Azure](./media/app-service-api-apps-why-best-platform/apia-apim.png)

Algumas funcionalidades da API Management e das API Apps tem funções semelhantes.  Por exemplo, ambas podem automatizar o suporte CORS. Quando utiliza os dois serviços em conjunto, teria de utilizar a API Management para CORS, uma vez que funciona como front-end para as suas API Apps. 

## Introdução

Para começar a utilizar as API Apps ao implementar o código de exemplo para um, consulte o tutorial para qualquer arquitetura que preferir:

* [ASP.NET](app-service-api-dotnet-get-started.md) 
* [Node.js](app-service-api-nodejs-api-app.md) 
* [Java](app-service-api-java-api-app.md) 

Para colocar questões sobre API Apps, inicie um thread no [Fórum das API Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps). 



<!--HONumber=ago16_HO4-->



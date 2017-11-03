---
title: "Descrição geral de conectores personalizado - Azure Logic Apps | Microsoft Docs"
description: "Descrição geral sobre como criar conectores personalizados para suportar e cenários de integração de expansão"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>Descrição geral de conectores personalizado

Sem escrever qualquer código, pode criar fluxos de trabalho ou aplicações com [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com), ou [Microsoft PowerApps](https://powerapps.microsoft.com). Para ajudar a integrar os seus processos de negócio e de dados, estes serviços oferecem [100 + conectores](../connectors/apis-list.md), não apenas para serviços da Microsoft e produtos como o Azure e SQL Server, mas outros serviços, como o GitHub, Salesforce, Twitter e muito mais . 

Por vezes, embora, pode querer chamar APIs, serviços e sistemas que não estão disponíveis como conectores prebuilt. Para suportar mais personalizáveis cenários para empresas e necessidades de produtividade dos seus utilizadores, pode criar *conectores personalizados* com os seus próprios acionadores e ações.
Conectores personalizados expandir o integrações, o alcance, a capacidade de deteção e a utilização para o seu serviço ou produto, o que pode ajudar a aumentar e acelerar a adoção de cliente.

Por exemplo, este diagrama apresenta a interação entre uma API Web, um conector Logic Apps personalizado criado para essa API e uma aplicação lógica que funciona com essa API utilizando o conector personalizado:

![Descrição geral conceptual de uma Web API, conector personalizado e aplicação lógica](./media/custom-connector-overview/custom-connector-conceptual.png)

Esta descrição geral descreve as tarefas gerais de alto nível para criar, proteger, registar e utilizar plus opcionalmente partilha ou a certificar os conectores:

![Passos de criação de conetor personalizado](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>Pré-requisitos

Para criar o conector do início ao fim, terá destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode começar com uma [conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, inscreva-se um [subscrição pay as you go](https://azure.microsoft.com/pricing/purchase-options/).

* Uma API RESTful com algum tipo de acesso autenticado. Para obter mais informações, consulte [criar conectores personalizados a partir de APIs da Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Para os padrões que pode utilizar para acionadores e ações do conector, consulte [criar APIs personalizadas que pode chamar a partir de fluxos de trabalho de aplicação de lógica](../logic-apps/logic-apps-create-api-app.md).

* Qualquer item aqui:

  * Um [OpenAPI 2.0 ficheiro](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md), anteriormente conhecido como Swagger
  * Um URL para uma definição de OpenAPI
  * A [coleção Postman](../logic-apps/custom-connector-api-postman-collection.md) para a API 

  Se não tiver qualquer um destes itens, iremos fornecer orientações para si.

* Opcional: Uma imagem a utilizar como um ícone para o conetor personalizado

## <a name="1-build-your-restful-api"></a>1. Criar a sua API RESTful

Tecnicamente, um conector é um wrapper em torno de uma API de REST baseada num OpenAPI (anteriormente Swagger) especificação e permite que o serviço subjacente fale com a Logic Apps, fluxo ou PowerApps. Por isso, primeiro, precisa de uma API ao funcionamento integral antes de criar o conector personalizado. 

Pode utilizar qualquer idioma e a plataforma para a API. Para tecnologias da Microsoft, recomendamos um estas plataformas:

* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [Aplicações Web do Azure](https://azure.microsoft.com/services/app-service/web/)
* [Aplicações API do Azure](https://azure.microsoft.com/services/app-service/api/)

Por exemplo, este tutorial mostra [como criar um conetor personalizado a partir de uma API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Para os padrões que pode utilizar para acionadores e ações do conector, consulte [criar APIs personalizadas que pode chamar a partir de fluxos de trabalho de aplicação de lógica](../logic-apps/logic-apps-create-api-app.md).

## <a name="2-secure-your-api"></a>2. Proteger a API

Pode utilizar estes padrões de autenticação para as suas APIs e conectores:

   * [OAuth 2.0](https://oauth.net/2/), incluindo [do Azure Active Directory](https://azure.microsoft.com/develop/identity/) ou serviços específicos, como o Dropbox, GitHub e SalesForce
   * Genérico OAuth 2.0
   * [Autenticação básica](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [Chave de API](https://swagger.io/docs/specification/authentication/api-keys/)

Pode configurar a autenticação do Azure Active Directory (Azure AD) para a API no portal do Azure para que não tem de implementar a autenticação através de código. Em alternativa, pode exigir e impor a autenticação através de código da API. 

Para obter mais informações, siga os tutoriais adequados:

* [As Logic Apps: Proteger o seu conector personalizado](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Fluxo: Proteger o seu conector personalizado](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps: Proteger o seu conector personalizado](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. Descrevem a API 

Partindo do princípio de que a API tem algum tipo de acesso autenticado, terá de descrevem a API interface operações de e para que as Logic Apps, fluxo ou PowerApps possa comunicar com a API.
Utilize um destas definições padrão da indústria:

* Um [OpenAPI 2.0 ficheiro](https://swagger.io/) pode começar por compilar com um ficheiro OpenAPI existente.

  Se estiver familiarizado com OpenAPI, visite [introdução Swagger](http://swagger.io/getting-started/) no swagger.io site.
  Para um ficheiro de OpenAPI de exemplo, consulte o [documentação da API de análise de texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure). 

* Uma coleção de Postman, gera um ficheiro de OpenAPI automaticamente para si. Quando ainda não tiver um ficheiro de OpenAPI e não pretender criar um, pode criar um conetor personalizado ainda facilmente através de uma coleção de Postman.

  Se estiver familiarizado com o Postman, [instalar a aplicação do Postman](https://www.getpostman.com/apps) do respetivo site. Para obter mais informações, consulte [descrevem conectores personalizados com Postman](../logic-apps/custom-connector-api-postman-collection.md).

> [!IMPORTANT]
> O tamanho do ficheiro tem de ser inferior a 1 MB.

Nos bastidores, as Logic Apps, PowerApps e fluxo, em última análise utilizar OpenAPI, analisa uma coleção de Postman e traduz a coleção a um ficheiro de definição de OpenAPI. Apesar de coleções OpenAPI 2.0 e Postman utilizam formatos diferentes, ambos são linguagem, machine-readable documentos que descrevem a API operações e parâmetros. Pode gerar estes documentos a partir de várias ferramentas, com base no idioma e plataforma utilizada a API. Também pode criar um ficheiro de OpenAPI quando registar o conector.

Por exemplo, pode criar um ficheiro de OpenAPI ou uma coleção de Postman de qualquer ponto final da REST API, incluindo:

* Conectores publicamente disponíveis, por exemplo, [Spotify](https://developer.spotify.com/), [Slack](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/), e assim sucessivamente

* Uma API que é criada e implementada em qualquer fornecedor de alojamento de nuvem, tais como o Azure, Heroku, Google Cloud e muito mais

* Uma API de linha de negócio personalizada que tenha implementado a sua rede, mas apenas se essa API está exposta na internet pública

## <a name="4-register-your-connector"></a>4. Registar o conector

O processo de registo ajuda Logic Apps, fluxo, ou PowerApps compreender características da sua API, incluindo a descrição, a autenticação necessária e o operações, incluindo os parâmetros e as saídas para cada operação. Quando inicia o processo de registo, pode fornecer um ficheiro de OpenAPI ou uma coleção de Postman que preenche automaticamente campos no Assistente de registo de metadados. Pode editar os valores destes campos em qualquer altura.

![Descrevem a API](./media/custom-connector-overview/choose-api-definition-file.png)

Para registar o conector, siga o tutorial adequado:

* [As Logic Apps: Registar o conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Fluxo: Registar o conector](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: Registar o conector](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. Utilize o conector numa aplicação lógica, o fluxo ou a aplicação 

Pode utilizar o conector da mesma forma que utilize conectores gerida pela Microsoft. Por exemplo, um fluxo de trabalho de aplicação lógica, adicione o conector personalizado para que possa criar uma ligação à sua API e chame quaisquer operações que fornece a API da mesma forma que chamam as operações para conectores gerida pela Microsoft.

## <a name="6-share-your-connector"></a>6. Partilhar o seu conector 

Pode partilhar o conector com utilizadores na sua organização da mesma forma que partilham recursos em Logic Apps, fluxo ou PowerApps. Apesar de partilha é opcional, poderá ter cenários em que pretende partilhar os conectores com outros utilizadores.

Registado mas uncertified conectores personalizados funcionam como conectores gerida pela Microsoft, mas estão visíveis e disponível *apenas* para o conector autor e os utilizadores que têm o mesmo inquilino do Azure Active Directory e a subscrição do Azure para aplicações lógicas na região onde as aplicações são implementadas. O passo seguinte descreve como pode partilhar o conector com utilizadores externos fora estes limites, por exemplo, com as Logic Apps, fluxo e PowerApps todos os utilizadores.

> [!IMPORTANT]
> Se partilhar um conector, outras poderão começar a dependem esse conector. 
> ***A eliminar o conector elimina todas as ligações para esse conector.***

* [As Logic Apps: Partilhar o seu conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Fluxo: Partilhar o seu conector](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: Partilhar o seu conector](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. Confirme do seu conector

Opcionalmente, partilhar o seu conector com todos os utilizadores no Logic Apps, PowerApps e fluxo, submeta o conector de certificação da Microsoft. Este processo revê o conector, verifica a existência de conformidade técnica e conteúda e valida funcionalidade para as Logic Apps, PowerApps e fluxo, antes de publicar o conector Microsoft. Saiba [como submeter o seu conector para certificação Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Obter suporte

* Para obter suporte com a integração e desenvolvimento, e-mail [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). A Microsoft ativamente monitoriza esta conta para perguntas para programadores e os problemas e encaminha-os para a equipa adequada. 

* Para perguntas mais frequentes, consulte o [conector personalizado FAQ](../logic-apps/custom-connector-faq.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar um conetor personalizado a partir de uma API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Configurar a autenticação para os conectores personalizados](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Descrevem conectores personalizados com coleções Postman](../logic-apps/custom-connector-api-postman-collection.md)
* [Submeter conectores personalizados para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md)

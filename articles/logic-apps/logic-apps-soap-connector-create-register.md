---
title: Crie e registe os conectores SOAP - Azure Logic Apps | Microsoft Docs
description: "Configurar conectores SOAP para utilização em Logic Apps do Azure"
author: divyaswarnkar
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
ms.date: 10/24/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 0323b0f7ee03dce209d5a71c6711988a34ba7633
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Crie e registe os conectores SOAP no Azure Logic Apps

Para integrar os serviços de SOAP nos seus fluxos de trabalho de aplicação lógica, pode criar e registar um conetor personalizado do protocolo de acesso de objeto simples (SOAP) utilizando Web Services Description Language (WSDL) que descreve o seu serviço SOAP. Os conectores SOAP funcionam como prebuilt conectores, pelo que pode utilizá-los da mesma forma que os outros conectores nas suas logic apps.


## <a name="prerequisites"></a>Pré-requisitos

Para registar o conector SOAP, é necessário estes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode começar com uma [conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, inscreva-se um [subscrição pay as you go](https://azure.microsoft.com/pricing/purchase-options/).

* Qualquer item aqui:
  * Um URL para um WSDL que define o serviço SOAP e as APIs
  * Um ficheiro WSDL que define o serviço SOAP e as APIs

  Para este tutorial, pode utilizar o nosso exemplo [as ordens de SOAP serviço](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl).

* Opcional: Uma imagem a utilizar como um ícone para o conetor personalizado


## <a name="1-create-your-connector"></a>1. Criar o conector

1. No portal do Azure, no menu principal do Azure, escolha **novo**. Na caixa de pesquisa, introduza "logic apps o conector" como o filtro e prima Enter.

   ![Nova, procure "logic apps o conector"](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. A partir da lista de resultados, escolha **Logic Apps conector** > **criar**.

   ![Criar conector das Logic Apps](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Forneça detalhes para registar o conector, conforme descrito na tabela. Quando tiver terminado, escolha **afixar ao dashboard** > **criar**.

   ![Detalhes de conetor personalizado da aplicação de lógica](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Propriedade | Valor sugerido | Descrição | 
   | -------- | --------------- | ----------- | 
   | **Nome** | *nome de conector de SOAP* | Forneça um nome para o conector. | 
   | **Subscrição** | *Nome da subscrição do Azure* | Selecione a sua subscrição do Azure. | 
   | **Grupo de recursos** | *Azure-resource--nome do grupo* | Crie ou selecione um grupo do Azure para organizar os recursos do Azure. | 
   | **Localização** | *implementação-região* | Selecione uma região de implementação para o conector. | 
   |||| 

   Depois de Azure implementa o conector, o menu de conector de aplicações lógica abre-se automaticamente. 
   Caso contrário, escolha o conector de soap a partir do dashboard do Azure.

## <a name="2-define-your-connector"></a>2. Definir o conector

Agora especificar o ficheiro WSDL ou o URL para criar o conector, a autenticação, que utiliza o conector, e as ações e acionadores que fornece o conector de soap


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Especifique o ficheiro WSDL ou o URL para o conector

1. No menu do seu conector, se ainda não estiver selecionado, escolher **Logic Apps conector**. Na barra de ferramentas, escolha **editar**.

   ![Editar conector personalizado](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Escolha **geral** para que possa fornecer os detalhes nestas tabelas para a criação, proteger e definir as ações e acionadores para o conector SOAP.

   1. Para **conectores personalizada**, selecione **SOAP** para sua **ponto final de API** para que possam fornecer o ficheiro WSDL que descreve a sua API.

      ![Disponibilize o ficheiro WSDL para a API](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Opção | Formato |Descrição | 
      | ------ | ------ | ----------- | 
      | **Carregar WSDL do ficheiro** | *Ficheiro WSDL* | Navegue para a localização do ficheiro WSDL e selecione esse ficheiro. | 
      | **Carregar WSDL a partir do URL** | http://*caminho no wsdl ficheiro* | Forneça o URL para o ficheiro WSDL do seu serviço. | 
      | **SOAP resto** |   | Transforme APIs no serviço SOAP em REST APIs. | 
      |||| 

   2. Para **informações gerais**, carregue um ícone para o conector. 
   Normalmente, o **Descrição**, **anfitrião**, e **Base URL** campos são preenchidos automaticamente do ficheiro WSDL. 
   Mas se não estiver, adicionar esta informação, tal como descrito na tabela e escolha **continuar**. 

      ![Detalhes do conector](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | Opção ou definição | Formato | Descrição | 
      | ----------------- | ------ | ----------- | 
      | **Carregue ícone** | *PNG-or-jpg-file-under-1-MB* | Um ícone que representa o conector <p>Cor: Preferencialmente, um logótipo sobre um fundo de cor branco. <p>Dimensões: Um pixel ~ 160 logótipo no interior de um quadrado 230 pixel | 
      | **Cor de fundo de ícone** | *ícone de marca-cor-hexadecimal-código* | <p>A cor atrás o ícone que corresponda a cor de fundo no seu ficheiro de ícone. <p>Formato: Hexadecimal. Por exemplo, #007ee5 representa a cor azul. | 
      | **Descrição** | *Descrição do conector* | Forneça uma descrição breve para o conector. | 
      | **Anfitrião** | *anfitrião do conector* | Forneça o domínio do anfitrião para o seu serviço SOAP. | 
      | **URL de base** | *URL de base de conector* | Forneça o URL de base para o seu serviço SOAP. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Descrevem a autenticação, que utiliza o conector

1. Agora escolher **segurança** para rever ou descrevem a autenticação que o conector utiliza. Autenticação certifica-se de que as identidades dos utilizadores adequadamente circular entre o serviço e quaisquer clientes.

   Por predefinição, o conector **tipo de autenticação** está definido como **sem autenticação**.
   
   ![Tipo de autenticação](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   Para alterar o tipo de autenticação, escolha **editar**. Pode selecionar **autenticação básica**. Para utilizar etiquetas de parâmetro que não sejam os valores predefinidos, atualizá-las em **etiqueta de parâmetro**.

   ![Autenticação básica](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Para guardar o seu conector depois de introduzir as informações de segurança, na parte superior da página, escolha **atualizar conector**, em seguida, escolha **continuar**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Reveja, atualizar ou definir ações e acionadores para o conector

1. Agora escolher **definição** para analisar, editar ou definir novas ações e acionadores que os utilizadores podem adicionar os seus fluxos de trabalho.

   Ações e acionadores baseiam-se nas operações definidas no seu ficheiro WSDL, preencher automaticamente o **definição** página e incluir os valores de pedido e resposta. Portanto, se as operações necessárias já aparecerem aqui, pode ir para o passo seguinte do processo de registo sem efetuar alterações nesta página.

   ![Definição de conector](./media/logic-apps-soap-connector-create-register/definition.png)

2. Opcionalmente, se pretender editar ações existentes e acionadores ou adicionar novos, [continuar com estes passos](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Conclua a criação do conector

Quando estiver pronto, selecione **atualização conector** pelo que pode implementar o conector. 

Parabéns! Agora quando criar uma aplicação lógica, pode encontrar o conector no Designer de aplicações lógicas e adicione esse conector à sua aplicação lógica.

![Lógica Designer de aplicações, localize o conector](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Partilhar o seu conector com outros utilizadores das Logic Apps

Registado mas uncertified conectores personalizados funcionam como conectores gerida pela Microsoft, mas estão visíveis e disponível *apenas* para o conector autor e os utilizadores que têm o mesmo inquilino do Azure Active Directory e a subscrição do Azure para aplicações lógicas na região onde as aplicações são implementadas. Apesar de partilha é opcional, poderá ter cenários em que pretende partilhar os conectores com outros utilizadores. 

> [!IMPORTANT]
> Se partilhar um conector, outras poderão começar a dependem esse conector. 
> ***A eliminar o conector elimina todas as ligações para esse conector.***
 
Para partilhar o seu conector com utilizadores externos fora estes limites, por exemplo, com todos os utilizadores das Logic Apps, [submeter o seu conector para certificação Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>FAQ

**P:** é o conector SOAP geralmente disponível (GA)? </br>
**R:** SOAP o conector está no **pré-visualização**, e ainda não é um serviço GA.

**P:** existem quaisquer restrições e os problemas conhecidos para o conector SOAP? </br>
**R:** Sim, consulte o [restrições de conector SOAP e os problemas conhecidos](../api-management/api-management-api-import-restrictions.md#wsdl).

**P:** existem limites para os conectores personalizados? </br>
**R:** Sim, consulte o [conetor personalizado aqui limita](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Obter suporte

* Para obter suporte com o desenvolvimento e de integração ou a pedido funcionalidades que não estão disponíveis no Assistente de registo, contacte [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). A Microsoft monitoriza esta conta para perguntas para programadores e os problemas e encaminha-os para a equipa adequada.

* Para pedir ou responder a perguntas, ou consulte o artigo que outros utilizadores do Azure Logic Apps estão a fazer, visite o [fórum de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar as Logic Apps, votar em ou submeter ideias no [site de comentários do utilizador Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passos seguintes

* [Opcional: Confirme do seu conector](../logic-apps/custom-connector-submit-certification.md)
* [FAQ sobre o conetor personalizado](../logic-apps/custom-connector-faq.md)
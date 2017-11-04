---
title: Registar conectores personalizados - Azure Logic Apps | Microsoft Docs
description: "Configurar conectores personalizados para utilização em Logic Apps do Azure"
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
ms.openlocfilehash: 9e3d88abe751b37700590cc68c458f208d5868d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Registar conectores personalizados no Azure Logic Apps

Depois de criar a API REST, configurar a autenticação e obter o ficheiro de definição de OpenAPI ou uma coleção de Postman, está pronto para registar o conector. 

## <a name="prerequisites"></a>Pré-requisitos

Para registar o conector personalizado, terá destes itens:

* Detalhes para registar o conector no Azure, tais como o nome, a subscrição do Azure, o grupo de recursos do Azure e a localização que pretende utilizar

* Um ficheiro de OpenAPI (Swagger) ou uma coleção de Postman que descrevem a API

  Para este tutorial, pode utilizar o [ficheiro OpenAPI de Gestor de recursos do Azure de exemplo](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* Um ícone que representa o conector

* Uma descrição breve para o conector

* A localização de anfitrião para a API

## <a name="1-create-your-connector"></a>1. Criar o conector

1. No portal do Azure, no menu principal do Azure, escolha **novo**. Na caixa de pesquisa, introduza "logic apps o conector" como o filtro e prima Enter.

   ![Nova, procure "logic apps o conector"](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. A partir da lista de resultados, escolha **Logic Apps conector** > **criar**.

   ![Criar conector das Logic Apps](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Forneça detalhes para registar o conector, conforme descrito na tabela. Quando tiver terminado, escolha **afixar ao dashboard** > **criar**.

   ![Detalhes de conetor personalizado da aplicação de lógica](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Propriedade | Valor sugerido | Descrição | 
   | -------- | --------------- | ----------- | 
   | **Nome** | *nome de conector personalizado* | Forneça um nome para o conector. | 
   | **Subscrição** | *Nome da subscrição do Azure* | Selecione a sua subscrição do Azure. | 
   | **Grupo de recursos** | *Azure-resource--nome do grupo* | Crie ou selecione um grupo do Azure para organizar os recursos do Azure. | 
   | **Localização** | *implementação-região* | Selecione uma região de implementação para o conector. | 
   |||| 

   Depois de Azure implementa o conector, o menu de conetor personalizado abre-se automaticamente. 
   Caso contrário, escolha o conetor personalizado a partir do dashboard do Azure.

## <a name="2-define-your-connector"></a>2. Definir o conector

Agora pode Especifica o ficheiro de OpenAPI ou coleção de Postman para criar o conector, a autenticação, que utiliza o conector, ações e acionadores que fornece o conetor personalizado e os parâmetros que pode utilizar as ações e é acionado.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Especifique o ficheiro de OpenAPI ou coleção de Postman para o conector

1. No menu do seu conector, se ainda não estiver selecionado, escolher **Logic Apps conector**. Na barra de ferramentas, escolha **editar**.

   ![Editar conector personalizado](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Escolha **geral** para que possa fornecer os detalhes nestas tabelas para a criação, proteger e definir as ações e acionadores para o conetor personalizado.

   1. Para **conectores personalizada**, selecione uma opção para que possam fornecer o ficheiro de OpenAPI (Swagger) que descrevem a API.

      ![Disponibilize o ficheiro de OpenAPI para a API](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Opção | formato |Descrição | 
      | ------ | ------ | ----------- | 
      | **Carregar um ficheiro de OpenAPI** | *OpenAPI (Swagger) - json - ficheiro* | Navegue para a localização para o ficheiro OpenAPI e selecione esse ficheiro. | 
      | **Utilizar um URL de OpenAPI** | http://*caminho-para-swagger--ficheiro json* | Forneça o URL para o ficheiro de OpenAPI da sua API. | 
      | **Carregar a coleção de Postman V1** | *Exportar Postman-coleção-V1-ficheiro* | Navegue para a localização para uma coleção de Postman exportada no formato V1. | 
      |||| 

   2. Para **informações gerais**, carregue um ícone para o conector. 
   Normalmente, o **Descrição**, **anfitrião**, e **Base URL** campos serão preenchidos automaticamente a partir do seu ficheiro OpenAPI. 
   Mas se não estiver, adicionar esta informação, tal como descrito na tabela e escolha **continuar**. 

      ![Detalhes do conector](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | Opção ou definição | formato | Descrição | 
      | ----------------- | ------ | ----------- | 
      | **Carregue ícone** | *PNG-or-jpg-file-under-1-MB* | Um ícone que representa o conector <p>Cor: Preferencialmente, um logótipo sobre um fundo de cor branco. <p>Dimensões: Um pixel ~ 160 logótipo no interior de um quadrado 230 pixel | 
      | **Cor de fundo de ícone** | *ícone de marca-cor-hexadecimal-código* | <p>A cor atrás o ícone que corresponda a cor de fundo no seu ficheiro de ícone. <p>Formato: Hexadecimal. Por exemplo, #007ee5 representa a cor azul. | 
      | **Descrição** | *Descrição do conector* | Forneça uma descrição breve para o conector. | 
      | **Anfitrião** | *anfitrião do conector* | Forneça o domínio do anfitrião para a API Web. | 
      | **URL de base** | *URL de base de conector* | Forneça o URL de base para a API Web. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Descrevem a autenticação, que utiliza o conector

1. Agora escolher **segurança** para rever ou descrevem a autenticação que o conector utiliza. Autenticação certifica-se de que as identidades dos utilizadores adequadamente circular entre o serviço e quaisquer clientes.

   ![Tipo de autenticação](./media/logic-apps-custom-connector-register/security.png)

   * Se carregar um ficheiro de OpenAPI, o Assistente de registo Deteta automaticamente o tipo de autenticação que utiliza a API Web, e preenche automaticamente a **segurança** secção no assistente, com base no `securityDefinitions` objeto que ficheiro. Por exemplo, aqui está uma secção que especifica OAuth2.0 a utilizar:

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * Se o ficheiro OpenAPI não preencher as propriedades e o tipo de autenticação, escolha **editar** pelo que pode adicionar esta informação. 
   
     Por exemplo, se anteriormente [configurar a autenticação do Azure AD neste tutorial](../logic-apps/custom-connector-azure-active-directory-authentication.md), criar aplicações do Azure AD para proteger a sua API Web e o conector. 
     Por isso, agora pode fornecer a chave de cliente e o ID de aplicação que guardou anteriormente:
    
     | Definição | Valor sugerido | Descrição | 
     | ------- | --------------- | ----------- | 
     | **Tipo de autenticação** | OAuth 2.0 | | 
     | **Fornecedor de identidade** | Azure Active Directory | | 
     | **Id de cliente** | *application-ID-for-connector-Azure-AD-app* | O ID da aplicação que guardou anteriormente para a aplicação do seu conector do Azure AD | 
     | **Segredo do cliente** | *client-key-for-connector-Azure-AD-app* | A chave de cliente para a aplicação do seu conector Azure AD | 
     | **URL de início de sessão** | `https://login.windows.net` | | 
     | **URL de recurso** | `https://management.core.windows.net/` | Certifique-se de que adiciona o URL exatamente conforme especificado, incluindo a barra no final. | 
     |||| 

   * Uma coleção de Postman, o que gera automaticamente um ficheiro de OpenAPI para si, preenche automaticamente o tipo de autenticação *apenas* ao utilizar os tipos de autenticação suportadas, tais como o OAuth 2.0 ou básico.

2. Para guardar o seu conector depois de introduzir as informações de segurança, na parte superior da página, escolha **atualizar conector**, em seguida, escolha **continuar**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Reveja, atualizar ou definir ações e acionadores para o conector

1. Agora escolher **definição** para analisar, editar ou definir novas ações e acionadores que os utilizadores podem adicionar os seus fluxos de trabalho.

   Ações e acionadores baseiam-se nas operações definidas no seu ficheiro OpenAPI ou coleção de Postman preencher automaticamente o **definição** página e incluir os valores de pedido e resposta. Portanto, se as operações necessárias já aparecerem aqui, pode ir para o passo seguinte do processo de registo sem efetuar alterações nesta página.

   ![Definição de conector](./media/logic-apps-custom-connector-register/definition.png)

2. Opcionalmente, se pretender editar ações existentes e acionadores ou adicionar novos, continue com estes passos.

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>Editar ou adicionar ações para o conector

1. Para editar uma ação existente, escolha o número de ação. Para adicionar uma ação que não existe no seu ficheiro OpenAPI ou coleção de Postman em **ações**, escolha **nova ação**.

2. Em **geral**, fornecer ou editar estas informações para a ação:
   
   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Resumo** | *nome de operação* | O título para esta ação | 
   | **Descrição** | *Descrição de operação* | A descrição para esta ação. <p>**Sugestão**: Certifique-se de que a descrição do seu termina com um período. |
   | **ID de operação** | *Identificador da operação* | Um nome exclusivo para identificar esta ação. Utilizar camel maiúsculas e minúsculas, por exemplo: "GetPullRequest" | 
   |**Visibilidade**| **nenhum**, **avançadas**, **interno**, ou **importantes** | A visibilidade destinada ao utilizador para esta ação. Para obter mais informações, consulte [OpenAPI extensões](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   |||| 

3. Agora defina o pedido para a ação.
  
   1. No **pedido** secção, escolha **importação da amostra**. 

   2. No **importação da amostra** página, cole um exemplo de pedido. 

      Normalmente, pedidos de exemplo estão disponíveis na documentação de API, onde pode obter informações para o **verbo**, **URL**, **cabeçalhos**, e **corpo**campos. Por exemplo, consulte o [documentação da API de análise de texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Exemplo de pedido de importação](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Se criar um conector de uma coleção de Postman, certifique-se de que remove o `Content-type` cabeçalho de ações e é acionado. As Logic Apps adiciona automaticamente este cabeçalho. Além disso, remover os cabeçalhos de autenticação que definiu no `Security` secção de ações e é acionado.

      Para a funcionalidade de OpenAPI avançada, consulte [OpenAPI extensões para conectores personalizados](../logic-apps/custom-connector-openapi-extensions.md).

   3. Para concluir a definição de pedido, escolha **importação**.

4. Agora, defina a resposta para a ação.

   1. Em **resposta**, pode especificar uma resposta de predefinição. 
   Escolha **adicionar resposta predefinido**.

   2. No **importação da amostra** página, cole uma resposta de amostra, em seguida, escolha **importação**.

5. Por fim, em **validação**, reveja e corrija quaisquer problemas potenciais identificados para a ação.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Editar ou adicionar acionadores para o conector

1. Para editar um acionador existente, escolha o número de para este acionador. Para adicionar um acionador que não existe no seu ficheiro OpenAPI ou coleção de Postman em **Acionadores**, escolha **novo acionador**.

2. Em **geral**, fornecer ou editar estas informações para o acionador:

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Resumo** | *nome de operação* | O título para este acionador | 
   | **Descrição** | *Descrição de operação* | A descrição para este acionador. <p>**Sugestão**: Certifique-se de que a descrição do seu termina com um período. | 
   | **ID de operação** | *Identificador da operação* | Um nome exclusivo para identificar este acionador. Utilizar camel maiúsculas e minúsculas, por exemplo: "TriggerOnGitHubPushEvent" | 
   |**Visibilidade**| **nenhum**, **avançadas**, **interno**, ou **importantes** | A visibilidade destinada ao utilizador para este acionador. Para obter mais informações, consulte [OpenAPI extensões](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   | **Tipo de Acionador** | **Webhook** ou **de consulta** | O tipo para este acionador. Por exemplo, um acionador de webhook aguarda que um evento específico ocorrer antes de acionadas. Um acionador de consulta verifica regularmente um ponto final de serviço com base num intervalo especificado e a frequência. <p>Para obter mais informações sobre o webhook e padrões de Acionador de consulta, consulte [criar APIs personalizadas](../logic-apps/logic-apps-create-api-app.md). | 
   |||| 

3. Agora defina o pedido que cria o acionador. 

   1. No **pedido** secção, escolha **importação da amostra**.

   2. No **importação da amostra** página, cole um exemplo de pedido. 

      Normalmente, pedidos de exemplo estão disponíveis na documentação de API, onde pode obter informações para o **verbo**, **URL**, **cabeçalhos**, e **corpo**campos. Por exemplo, consulte o [documentação da API de análise de texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Exemplo de pedido de importação](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Se criar um conector de uma coleção de Postman, certifique-se de que remove o `Content-type` cabeçalho de ações e é acionado. As Logic Apps adiciona automaticamente este cabeçalho. Além disso, remover os cabeçalhos de autenticação que definiu no `Security` secção de ações e é acionado.

      Para a funcionalidade de OpenAPI avançada, consulte [OpenAPI extensões para conectores personalizados](../logic-apps/custom-connector-openapi-extensions.md).

   3. Para concluir a definição de pedido, escolha **importação**. 

4. Agora defina resposta o acionador. No **resposta** secção, com base no tipo o acionador, siga estes passos:

   **Webhook acionador**
   1. No **Webhook resposta**, escolha **importação da amostra**. 

   2. No **importação da amostra** página, cole uma resposta de amostra, em seguida, escolha **importação**. Para ver um exemplo de resposta, consulte o [referência da API do GitHub para criar um webhook](https://developer.github.com/v3/repos/hooks/#create-a-hook).

   3. Em **configuração acionador**, selecione um parâmetro a utilizar para o pedido de criação do webhook. As Logic Apps utiliza este valor de parâmetro para povoar o URL de chamada de retorno utilizado pelo seu serviço para comunicar com o acionador.

   **Acionador da consulta**
   1. Em **resposta**, pode especificar uma resposta de predefinição. 
   Escolha **adicionar resposta predefinido**.

   2. No **importação da amostra** página, cole uma resposta de amostra, em seguida, escolha **importação**.

   3. Em **configuração acionador**, especifique o parâmetro de consulta, o valor para passar para o parâmetro e uma *sugestão de Acionador* que especifica um intervalo de consulta adequada para o pedido seguinte.

5. Por fim, em **validação**, reveja e corrija quaisquer problemas potenciais identificados para o acionador.

#### <a name="review-reference-definitions-for-your-connector"></a>Reveja as definições de referência para o conector

O **referências** secção automaticamente preenche da descrição do seu API e descreve os campos de parâmetro que pode referenciar ações e é acionado. 

1. Em **referências**, escolha o número para a definição de referência que pretende rever.

2. Em **nome**, analisar ou atualizar o nome de definição de referência.

3. Em **validação**, reveja e corrija quaisquer problemas potenciais identificados para a definição de referência.

## <a name="3-finish-creating-your-connector"></a>3. Conclua a criação do conector

Quando estiver pronto, selecione **criar** pelo que pode implementar o conector. Se estiver a atualizar um conetor existente, escolha **atualizar conector**.

Parabéns! Agora quando criar uma aplicação lógica, pode encontrar o conector no Designer de aplicações lógicas e adicione esse conector à sua aplicação lógica.

![Lógica Designer de aplicações, localize o conector](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Partilhar o seu conector com outros utilizadores das Logic Apps

Registado mas uncertified conectores personalizados funcionam como conectores gerida pela Microsoft, mas estão visíveis e disponível *apenas* para o conector autor e os utilizadores que têm o mesmo inquilino do Azure Active Directory e a subscrição do Azure para aplicações lógicas na região onde as aplicações são implementadas. Apesar de partilha é opcional, poderá ter cenários em que pretende partilhar os conectores com outros utilizadores. 

> [!IMPORTANT]
> Se partilhar um conector, outras poderão começar a dependem esse conector. 
> ***A eliminar o conector elimina todas as ligações para esse conector.***
 
Para partilhar o seu conector com utilizadores externos fora estes limites, por exemplo, com as Logic Apps, fluxo e PowerApps todos os utilizadores, [submeter o seu conector para certificação Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>FAQ

**P:** existem limites para os conectores personalizados? </br>
**R:** Sim, consulte o [conetor personalizado aqui limita](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Obter suporte

* Para obter suporte com o desenvolvimento e de integração ou a pedido funcionalidades que não estão disponíveis no Assistente de registo, contacte [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). A Microsoft monitoriza esta conta para perguntas para programadores e os problemas e encaminha-os para a equipa adequada.

* Para pedir ou responder a perguntas, ou consulte o artigo que outros utilizadores do Azure Logic Apps estão a fazer, visite o [fórum de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar as Logic Apps, votar em ou submeter ideias no [site de comentários do utilizador Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passos seguintes

* [Opcional: Confirme do seu conector](../logic-apps/custom-connector-submit-certification.md)
* [FAQ sobre o conetor personalizado](../logic-apps/custom-connector-faq.md)
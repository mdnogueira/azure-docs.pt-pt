---
title: Exportar uma API alojado no Azure PowerApps e Microsoft fluxo | Microsoft Docs
description: "Descrição geral de como expõe uma API alojada no App Service para PowerApps e Microsoft Flow"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/06/2017
ms.author: mahender; mblythe
ms.openlocfilehash: efa5a50564d94dbecd4bc7fcb4082b01d16f680d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportar uma API alojado no Azure PowerApps e fluxo da Microsoft

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) é um serviço para criar e utilizar aplicações empresariais personalizadas que se ligar aos seus dados e a trabalham entre plataformas. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) torna mais fácil automatizar fluxos de trabalho e os processos de negócios entre os seus serviços e aplicações favoritas. PowerApps e Microsoft Flow vêm com uma variedade de conectores incorporados para origens de dados, tais como o Office 365, Dynamics 365, Salesforce e muito mais. Em alguns casos, construtores de aplicação e fluxo também pretendem ligar a origens de dados e APIs criadas pela respetiva organização.

Da mesma forma, os programadores que pretendem expor das respetivas APIs mais amplamente dentro de uma organização podem disponibilizar das respetivas APIs para construtores de aplicação e o fluxo. Este tópico mostra como exportar uma API criada com [das funções do Azure](../azure-functions/functions-overview.md) ou [App Service do Azure](../app-service/app-service-web-overview.md). A API exportada torna-se um *conetor personalizado*, que é utilizada no PowerApps e Flow Microsoft, tal como um conector incorporado.

## <a name="create-and-export-an-api-definition"></a>Criar e exportar uma definição de API
Antes de exportar uma API, tem de descrever a API utilizando uma definição de OpenAPI (anteriormente conhecido como um [Swagger](http://swagger.io/) ficheiro). Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados. PowerApps e Microsoft Flow podem criar conectores personalizados para qualquer definição OpenAPI 2.0. Funções do Azure e o App Service do Azure têm suporte incorporado para criar, alojar e gerir definições de OpenAPI. Para obter mais informações, consulte [criar uma API RESTful nas Web Apps do Azure](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Também pode criar conectores personalizados na PowerApps e IU de fluxo da Microsoft, sem utilizar uma definição de OpenAPI. Para obter mais informações, consulte [registar e utilizar um conetor personalizado (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) e [registar e utilizar um conetor personalizado (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Para exportar a definição da API, siga estes passos:

1. No [portal do Azure](https://portal.azure.com), navegue para a aplicação das funções do Azure ou do serviço de aplicações.

    Se utilizar as funções do Azure, selecione a sua aplicação de função, escolha **funcionalidades da plataforma**e, em seguida, **definição da API**.

    ![Definição da API de funções do Azure](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Se utilizar o App Service do Azure, selecione **definição da API** na lista de definições.

    ![Definição de API do serviço de aplicações](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. O **exportar para PowerApps + Microsoft Flow** botão deve estar disponível (se não, primeiro tem de criar uma definição de OpenAPI). Clique neste botão para iniciar o processo de exportação.

    ![Exportar para PowerApps + Microsoft Flow botão](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selecione o **exportação modo**:

    **Express** permite criar o conector personalizado a partir do portal do Azure. Requer que iniciou a sessão na PowerApps ou Flow Microsoft e que têm permissão para criar conectores no ambiente de destino. Esta é a abordagem recomendada se este requisito pode ser cumprido. Se utilizar este modo, siga o [utilizar a exportação rápida](#express) instruções abaixo.

    **Manual** permite exportar a definição da API, que, em seguida, importar com os portais PowerApps ou Flow Microsoft. Esta é a abordagem recomendada se o utilizador do Azure e o utilizador com permissão para criar conectores são diferentes pessoas ou se o conector tem de ser criado no outro inquilino. Se utilizar este modo, siga o [utilizar a exportação manual](#manual) instruções abaixo.

    ![Modo de exportação](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> O conector personalizado utiliza um *cópia* da definição de API, pelo que PowerApps e Microsoft Flow não imediatamente saberá se efetuar alterações para a aplicação e a respetiva definição de API. Se efetuar alterações, repita os passos de exportação para a nova versão.

<a name="express"></a>
## <a name="use-express-export"></a>Utilizar a exportação rápida

Para concluir a exportação no **Express** modo, siga estes passos:

1. Certifique-se de que tem sessão iniciada ao inquilino PowerApps ou Flow Microsoft à qual pretende exportar. 

2. Utilize as definições conforme especificado na tabela.

    |Definição|Descrição|
    |--------|------------|
    |**Ambiente**|Selecione o ambiente em que o conector personalizado deve ser guardado. Para obter mais informações, consulte [descrição geral de ambientes](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nome da API personalizada**|Introduza um nome, que PowerApps e Microsoft Flow construtores Verão na respetiva lista de conector.|
    |**Preparar a configuração de segurança**|Se necessário, forneça os detalhes de configuração de segurança necessários para conceder aos utilizadores acesso à sua API. Este exemplo mostra uma chave de API. Para obter mais informações, consulte [especificar o tipo de autenticação](#auth) abaixo.|
 
    ![Exportação rápida PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Clique em **OK**. O conector personalizado é agora incorporado e adicionado ao ambiente que especificou.

Para obter exemplos de utilização **Express** modo com as funções do Azure, consulte [chamar uma função a partir do PowerApps](functions-powerapps-scenario.md) e [chamar uma função do Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Utilizar a exportação manual

Para concluir a exportação no **Manual** modo, siga estes passos:

1. Clique em **transferir** e guarde o ficheiro, ou clique no botão Copiar e guardar o URL. Irá utilizar o ficheiro de transferência ou o URL durante a importação.
 
    ![Exportação manual para PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Se a definição da API inclui quaisquer definições de segurança, estes são realçadas no passo #2. Durante a importação, PowerApps e Microsoft Flow Deteta estes e pede-lhe as informações de segurança. Recolha as credenciais relacionadas com a cada definição para utilização na secção seguinte. Para obter mais informações, consulte [especificar o tipo de autenticação](#auth) abaixo.

    ![Segurança para exportação manual](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Este exemplo mostra a definição de segurança de chave de API que foi incluída na definição do OpenAPI.

Agora que tenha exportado a definição da API, importe-o para criar um conetor personalizado no PowerApps e Flow Microsoft. O exemplo seguinte utiliza PowerApps, mas conectores personalizados são partilhados entre os dois serviços, por isso apenas terá de importar a definição de uma vez.

Para importar a definição da API para PowerApps e Microsoft Flow, siga estes passos:

1. Inicie sessão no [web.powerapps.com](https://web.powerapps.com) ou [flow.microsoft.com](https://flow.microsoft.com/). 

2. Clique em de **definições** botão (o ícone engrenagem) no canto superior direito da página e selecione **conectores personalizada**.

    ![conectores personalizados](media/app-service-export-api-to-powerapps-and-flow/custom-connectors.png)

3. Clique em **criar conector personalizado**.

4. No **geral** separador, forneça um nome para a API e, em seguida, carregue a definição de OpenAPI ou cole o URL de metadados. Clique em **carregar**, em seguida, **continuar**.

    ![Separador Geral](media/app-service-export-api-to-powerapps-and-flow/tab-general.png)

5. No **segurança** separador, se lhe for pedido para fornecer detalhes de autenticação, introduza os valores adequados para o tipo de autenticação. Clique em **continuar**.

    ![Separador Segurança](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Este exemplo mostra os campos obrigatórios para autenticação de chave de API. Os campos diferem consoante o tipo de autenticação.

6. No **definições** separador, todas as operações definidas no ficheiro de OpenAPI são povoadas de automática. Se todas as operações necessárias são definidas, pode ir para o passo seguinte. Caso contrário, pode adicionar e modificar operações aqui.

    ![Separador de definições](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Neste exemplo tem uma operação, com o nome `CalculateCosts`. Os metadados, como **Descrição**, todos os provém do ficheiro OpenAPI.

7. Clique em **criar conector** na parte superior da página.

Pode agora ligar para o conector personalizado no PowerApps e Flow Microsoft. Para obter mais informações sobre a criação de conectores no portais PowerApps e Flow Microsoft, consulte [registar o conector personalizado (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) e [registar o conector personalizado (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Especifique o tipo de autenticação

PowerApps e Flow Microsoft suportam uma coleção de fornecedores de identidade que fornecer autenticação para conectores personalizados. Se a sua API requer autenticação, certifique-se de que é capturado como uma _definição de segurança_ no seu documento OpenAPI, semelhante ao seguinte exemplo:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Durante a exportação, forneça valores de configuração que lhe permitem PowerApps e Flow Microsoft autenticar os utilizadores.

Esta secção abrange os tipos de autenticação que são suportados em **Express** modo: chave de API, Azure Active Directory e genérico OAuth 2.0. PowerApps e Microsoft Flow também suportam a autenticação básica e OAuth 2.0 para serviços específicos, como o Dropbox, Facebook e da SalesForce.

### <a name="api-key"></a>Chave de API
Quando utilizar uma chave de API, os utilizadores do seu conector for pedido que forneça a chave quando criam uma ligação. Especifique um nome de chave de API para ajudar a compreender o que é necessária. No exemplo anterior, utilizamos o nome `API Key (contact meganb@contoso.com)` para pessoas saber onde obter informações sobre a chave de API. Para as funções do Azure, a chave é, geralmente, uma das chaves de anfitrião, que abrangem várias funções dentro da aplicação de função.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Quando utilizar o Azure AD, terá dois registos de aplicações do Azure AD: uma para a API de si próprio e outra para o conector personalizado:

- Para configurar o registo para a API, utilize o [aplicação serviço de autenticação/autorização](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md) funcionalidade.

- Para configurar o registo para o conector, siga os passos no [adicionar uma aplicação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). O registo tem de ter delegado acesso à sua API e um URL de resposta de `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Para obter mais informações, consulte os exemplos de registo do Azure AD para [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) e [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Estes exemplos utilizam o Azure Resource Manager como a API; Se seguir os passos, substitua a API.

Os seguintes valores de configuração são necessários:
- **ID de cliente** -o ID de cliente do seu conector registo do Azure AD
- **Segredo do cliente** -o segredo do cliente do seu conector registo do Azure AD
- **URL de início de sessão** -o URL de base para o Azure AD. No Azure, este é normalmente `https://login.windows.net`.
- **ID de inquilino** -o ID do inquilino a ser utilizada para o início de sessão. Esta situação deve ser "comum" ou o ID do inquilino no qual o conector é criado.
- **URL de recurso** -o URL de recurso do registo do Azure AD para a API

> [!IMPORTANT]
> Se alguém irá importar a definição da API para PowerApps e Flow Microsoft como parte do fluxo manual, terá de fornecê-los com o ID de cliente e o segredo do cliente do *registo do conetor*, bem como o URL de recurso da sua API. Certifique-se de que estes segredos são geridos de forma segura. **Não partilhe as credenciais de segurança da API em si.**

### <a name="generic-oauth-20"></a>Genérico OAuth 2.0
Ao utilizar o genérico OAuth 2.0, pode integrar com qualquer fornecedor de OAuth 2.0. Isto permite-lhe trabalhar com fornecedores personalizados que não são suportados nativamente.

Os seguintes valores de configuração são necessários:
- **ID de cliente** -o ID de cliente OAuth 2.0
- **Segredo do cliente** -o segredo de cliente OAuth 2.0
- **URL de autorização** -o URL de autorização do OAuth 2.0
- **URL do token** -o URL de token de OAuth 2.0
- **Atualizar o URL** -o URL de atualização de OAuth 2.0



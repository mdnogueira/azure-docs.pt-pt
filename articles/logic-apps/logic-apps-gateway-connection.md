---
title: Aceder a origens de dados no local para Azure Logic Apps | Microsoft Docs
description: Configurar o gateway de dados no local para que possa aceder a origens de dados no local a partir das logic apps
keywords: "aceder a dados no local, a transferência de dados, a encriptação e origens de dados"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: f385d832deed2eaf8ea21eb75d62944cbbf3d13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>Ligar às origens de dados no local a partir das logic apps com o gateway de dados no local

Para aceder a origens de dados no local das logic apps, configure um gateway de dados no local que podem utilizar aplicações lógicas com conectores suportados. O gateway funciona como uma ponte que fornece a transferência de dados rápida e encriptação entre origens de dados no local e as logic apps. O gateway transmite dados a partir de origens do local em canais encriptados através do Service Bus do Azure. Todo o tráfego origina como tráfego de saída seguro do agente de gateway. Saiba mais sobre [como funciona o gateway de dados](logic-apps-gateway-install.md#gateway-cloud-service). 

O gateway suporta ligações para estas origens de dados no local:

*   BizTalk Server 2016
*   DB2  
*   Sistema de Ficheiros
*   Informix
*   MQ
*   MySQL
*   Base de dados Oracle
*   PostgreSQL
*   Servidor de aplicações SAP 
*   Servidor de mensagens SAP
*   SharePoint
*   SQL Server
*   Teradata

Estes passos mostram como configurar o gateway de dados no local para trabalhar com as logic apps. Para obter mais informações sobre conectores suportados, consulte [conectores para o Azure Logic Apps](../connectors/apis-list.md). 

Para obter informações sobre como utilizar o gateway com outros serviços, consulte estes artigos:

*   [Gateway de dados do Microsoft Power BI no local](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Gateway de dados no local do Analysis Services do Azure](../analysis-services/analysis-services-gateway.md)
*   [Gateway de dados do Microsoft Flow no local](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Gateway de dados do Microsoft PowerApps no local](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>Requisitos

* Tem de ter já [instalado o gateway de dados num computador local](logic-apps-gateway-install.md).

* Quando inicia sessão no portal do Azure, tem de utilizar a mesma conta escolar ou profissional que foi utilizada para [instalar o gateway de dados no local](logic-apps-gateway-install.md#requirements). A conta de início de sessão também tem de ter uma subscrição do Azure para utilizar quando criar um recurso de gateway no portal do Azure para a sua instalação do gateway.

* A instalação do gateway já não pode ser reclamada por um recurso de gateway do Azure. Pode associar a instalação do gateway para apenas um recurso de gateway do Azure. Afirmação acontece quando criar o recurso de gateway para que a instalação não está disponível para outros recursos.

* O gateway de dados no local é executado como um serviço do Windows e está configurado para utilizar `NT SERVICE\PBIEgwService` para o Windows service credenciais de início de sessão. Para criar e manter o recurso de gateway no portal do Azure, o [conta de serviço do Windows](../logic-apps/logic-apps-gateway-install.md) tem de ter, pelo menos, **contribuinte** permissões. 

  > [!NOTE]
  > A conta de serviço do Windows é diferente da conta utilizada para ligar aos dados no local origens e do Azure conta escolar ou profissional utilizada para iniciar sessão em serviços em nuvem.

## <a name="set-up-the-data-gateway-connection"></a>Configurar a ligação de gateway de dados

### <a name="1-install-the-on-premises-data-gateway"></a>1. Instalar o gateway de dados no local

Se ainda não o fez, siga o [passos para instalar o gateway de dados no local](logic-apps-gateway-install.md). Antes de continuar com os outros passos, certifique-se de que instalou o gateway de dados num computador local.

<a name="create-gateway-resource"></a>

### <a name="2-create-an-azure-resource-for-the-on-premises-data-gateway"></a>2. Criar um recurso do Azure para o gateway de dados no local

Depois de instalar o gateway num computador local, tem de criar o gateway de dados como um recurso no Azure. Este passo também associa o seu recurso de gateway com a sua subscrição do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure"). Certifique-se ao utilizar o mesmo Azure trabalho ou escola endereço de correio eletrónico utilizado para instalar o gateway.

2. No menu principal do Azure, escolha **novo** > **integração empresarial com** > **gateway de dados no local** conforme mostrado aqui:

   ![Localizar "gateway de dados no local"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. No **criar gateway de ligação** página, forneça estes detalhes para criar o recurso do gateway de dados:

    * **Nome**: introduza um nome para o recurso do gateway. 

    * **Subscrição**: selecione a subscrição do Azure para associar o recurso do gateway. 
    Esta subscrição deve ser a mesma subscrição que a sua aplicação lógica.
   
      A subscrição predefinida baseia-se a conta do Azure que utilizou para iniciar sessão.

    * **Grupo de recursos**: criar um grupo de recursos ou selecione um grupo de recursos existente para implementar o recurso do gateway. 
    Grupos de recursos ajudam a gerir recursos do Azure relacionados como uma coleção.

    * **Localização**: Azure restringe esta localização à mesma região que foi selecionada para o serviço de nuvem do gateway durante [instalação do gateway](logic-apps-gateway-install.md). 

      > [!NOTE]
      > Certifique-se de que a localização do recurso de gateway corresponde a localização de serviço de nuvem do gateway. Caso contrário, a instalação do gateway pode não aparecer na lista de gateways instalado para que possa selecioná no próximo passo.
      > 
      > Pode utilizar regiões diferentes para o seu recurso de gateway e para a sua aplicação lógica.

    * **Nome de instalação**: se a instalação do gateway não estiver selecionada, selecione o gateway instalado anteriormente. 

    Para adicionar o recurso de gateway ao dashboard do Azure, escolha **afixar ao dashboard**. 
    Quando tiver terminado, escolha **criar**.

    Por exemplo:

    ![Forneça detalhes para criar o gateway de dados no local](./media/logic-apps-gateway-connection/createblade.png)

    Para encontrar ou ver o seu gateway de dados em qualquer altura, no menu principal do Azure, aceda a **mais serviços** > **integração empresarial com** > **Gateways de dados no local** .

    ![Aceda a "Mais serviços", "Integração empresarial com o", "Gateways de dados no local"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

### <a name="3-connect-your-logic-app-to-the-on-premises-data-gateway"></a>3. Ligar a sua aplicação lógica para o gateway de dados no local

Agora que já criou o seu recurso de gateway de dados e associado à sua subscrição do Azure com esse recurso, crie uma ligação entre a sua aplicação lógica e o gateway de dados.

> [!NOTE]
> A localização de ligação do gateway tem de existir na mesma região que a sua aplicação lógica, mas pode utilizar um gateway de dados existe numa região diferente.

1. No portal do Azure, criar ou abrir a sua aplicação lógica no Designer de aplicação lógica.

2. Adicione um conector que suporte ligações no local, como o SQL Server.

3. Seguir ordem apresentada, selecione **ligar através do gateway de dados no local**, forneça um nome de ligação exclusiva e as informações necessárias e selecione o recurso de gateway de dados que pretende utilizar. Quando tiver terminado, escolha **criar**.

   > [!TIP]
   > Um nome de ligação exclusiva ajuda-o a identificar facilmente essa ligação mais tarde, especialmente quando cria várias ligações. Se aplicável, incluem também o domínio qualificado para o seu nome de utilizador. 

   ![Criar ligação entre o gateway de dados e aplicação lógica](./media/logic-apps-gateway-connection/blankconnection.png)

Parabéns, a ligação de gateway está agora pronta para a sua aplicação lógica utilizar.

## <a name="edit-your-gateway-connection-settings"></a>Editar as definições de ligação de gateway

Depois de criar uma ligação de gateway para a sua aplicação lógica, pode querer mais tarde para atualizar as definições dessa ligação específica.

1. Para localizar a ligação de gateway:

   * No menu de aplicação lógica, sob **ferramentas de desenvolvimento**, selecione **API ligações**. 
   
     O **API ligações** painel mostra todas as ligações de API associadas com a sua aplicação lógica, incluindo ligações de gateway.

     ![Aceda à sua aplicação lógica, selecione "API ligações"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Ou, no menu principal do Azure, aceda a **mais serviços** > **Web + móvel** > **API ligações** para todas as ligações de API, incluindo o gateway ligações, que estão associadas a sua subscrição do Azure. 

   * Ou, no menu principal do Azure, aceda a **todos os recursos** para todas as ligações de API, incluindo ligações de gateway, que estão associadas a sua subscrição do Azure.

2. Selecione a ligação de gateway que pretende ver ou editar e escolha **ligação editar API**.

   > [!TIP]
   > Se as atualizações não entre em vigor, tente [parando e reiniciando o serviço Windows do gateway](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>
## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>Comutador ou eliminar o recurso de gateway de dados no local

Para criar um recurso de gateway diferentes, associar o seu gateway um recurso diferente ou remova o recurso de gateway, pode eliminar o recurso de gateway sem afetar a instalação do gateway. 

1. No menu principal do Azure, aceda a **todos os recursos**. 
2. Localize e selecione o recurso de gateway de dados.
3. Escolha **Gateway de dados no local**e na barra de ferramentas recursos, escolha **eliminar**.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passos seguintes

* [Proteger as suas aplicações lógicas](./logic-apps-securing-a-logic-app.md)
* [Exemplos e cenários para aplicações lógicas comuns](./logic-apps-examples-and-scenarios.md)

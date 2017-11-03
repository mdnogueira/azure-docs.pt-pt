---
title: Ligar a um sistema SAP no local no Azure Logic Apps | Microsoft Docs
description: "Ligar a um sistema SAP no local a partir do fluxo de trabalho de aplicação lógica através do gateway de dados no local"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3fea93f558d5a4ef62550fd1f6486903cb812930
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>Ligar a um sistema SAP no local a partir das logic apps com o conector do SAP 

O gateway de dados no local permite-lhe gerir os dados e aceder de forma segura a recursos que estão no local. Este tópico mostra como pode ligar as logic apps para um sistema SAP no local. Neste exemplo, a sua aplicação lógica solicita um IDOC através de HTTP e envia a resposta de volta.    

> [!NOTE]
> Limitações atuais: 
> - A aplicação lógica exceder o tempo limite se todos os passos necessários para a resposta não concluída dentro do [limite de tempo limite do pedido](./logic-apps-limits-and-config.md). Neste cenário, poderão obter bloqueados pedidos. 
> - O Seletor de ficheiros não apresenta todos os campos disponíveis. Neste cenário, pode adicionar manualmente os caminhos.

## <a name="prerequisites"></a>Pré-requisitos

- Instalar e configurar a versão mais recente [gateway de dados no local](https://www.microsoft.com/download/details.aspx?id=53127) versão 1.15.6150.1 ou mais recente. [Como ligar para o gateway de dados no local de uma aplicação lógica](http://aka.ms/logicapps-gateway) apresenta os passos. O gateway tem de ser instalado numa máquina no local para poder continuar.

- Transferir e instalar a biblioteca de cliente mais recente do SAP no mesmo computador onde instalou o gateway de dados. Utilize qualquer uma das seguintes versões do SAP: 
    - Servidor do SAP
        - Qualquer servidor SAP que suportam o conector do .NET (NCo) 3.0
 
    - Cliente SAP
        - Conector de .NET de SAP (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>Adicionar acionadores e ações para ligação ao sistema de SAP

O conector do SAP tem ações, mas não a acionadores. Por isso, temos de utilizar outra acionador no início do fluxo de trabalho. 

1. Adicionar o acionador pedido/resposta e, em seguida, selecione **novo passo**.

2. Selecione **adicionar uma ação**e, em seguida, seleccione o conector do SAP, escrevendo `SAP` no campo de pesquisa:    

     ![Selecione o servidor de aplicações SAP ou servidor de mensagens SAP](media/logic-apps-using-sap-connector/sap-action.png)

3. Selecione [ **servidor de aplicações SAP** ](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [ **SAP mensagem de servidor**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm), com base na sua configuração SAP. Se não tiver uma ligação existente, lhe for pedido para criar um.

   1. Selecione **ligar através do gateway de dados no local**e introduza os detalhes para o seu sistema SAP:   

       ![Adicionar cadeia de ligação para SAP](media/logic-apps-using-sap-connector/picture2.png)  

   2. Em **Gateway**, selecione um gateway existente ou para instalar um novo gateway, selecione **instalar Gateway**.

        ![Instalar um novo gateway](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. Depois de introduzir todos os detalhes, selecione **criar**. 
   As Logic Apps configura e testa a ligação, certificando-se de que a ligação funciona corretamente.

4. Introduza um nome para a sua ligação de SAP.

5. As diferentes opções de SAP estão agora disponíveis. Para localizar a categoria IDOC, selecione na lista. Ou manualmente, escreva o caminho e selecione a resposta HTTP no **corpo** campo:

     ![Ação de SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Adicionar a ação para criar um **resposta de HTTP**. A mensagem de resposta deve ser a partir da saída SAP.

7. Guarde a sua aplicação lógica. Testá-lo através do envio de uma IDOC através do URL de Acionador HTTP. Depois do IDOC é enviada, aguarde que a resposta da aplicação lógica:   

     > [!TIP]
     > Verificar como [monitorizar as Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Agora que o conector do SAP é adicionado à sua aplicação lógica, começar a explorar as outras funcionalidades:

- BAPI
- RFC

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e ver o que os outros utilizadores do Azure Logic Apps estão a fazer, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar o Azure Logic Apps e os conectores, vote ou submeta ideais no [site de comentários dos utilizadores do Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

- Saiba como validar, transformar e outras funções como o BizTalk no [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- [Ligar aos dados no local](../logic-apps/logic-apps-gateway-connection.md) partir das logic apps

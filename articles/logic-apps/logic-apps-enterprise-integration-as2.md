---
title: "Mensagens de AS2 para a integração do enterprise B2B - Azure Logic Apps | Microsoft Docs"
description: "Trocar mensagens AS2 para a integração do enterprise B2B com Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 91b2f16611b88aa4b9395ca301d88042065ad9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="exchange-as2-messages-for-enterprise-integration-with-logic-apps"></a>Trocar mensagens AS2 para a integração empresarial com logic apps

Podem trocar mensagens AS2 para Azure Logic Apps, tem de criar um contrato de AS2 e armazenar esse contrato na sua conta de integração. Eis os passos sobre como criar um contrato de AS2.

## <a name="before-you-start"></a>Antes de começar

Segue-se os itens que precisa de:

* Um [conta integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) que já foi definida e associados à subscrição do Azure
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração e configurada com o qualificador AS2 em **identidades de negócio**

> [!NOTE]
> Quando cria um contrato, o conteúdo no ficheiro do contrato tem de corresponder ao tipo de contrato.    

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) e [adicionar parceiros](logic-apps-enterprise-integration-partners.md), pode criar um contrato de AS2, seguindo estes passos.

## <a name="create-an-as2-agreement"></a>Crie um contrato de AS2

1.  Inicie sessão no [portal do Azure](http://portal.azure.com "portal do Azure").  

2.  No menu à esquerda, selecione **mais serviços**. Na caixa de pesquisa, introduza **integração** como o filtro. Na lista de resultados, selecione **contas de automatização**.

    > [!TIP]
    > Se não vir **mais serviços**, poderá ter de expanda o menu primeiro. Na parte superior do menu fechado, selecione **Mostrar menu**.

    ![Mais serviços, o filtro "integração", selecione "Contas de automatização"](./media/logic-apps-enterprise-integration-agreements/overview-1.png)

3. No **contas de automatização** painel que abre, selecione a conta de integração onde pretende criar o contrato.
Se não vir quaisquer contas de automatização, [criar um primeiro](../logic-apps/logic-apps-enterprise-integration-accounts.md "tudo sobre contas de automatização").  

    ![Selecione a conta de integração onde pretende criar o contrato](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Escolha o **contratos** mosaico. Se não tiver um mosaico de contratos, adicione primeiro o mosaico.

    ![Escolha o que mosaico "Contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

5. No painel contratos que se abre, escolha **adicionar**.

    ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

6. Em **adicionar**, introduza um **nome** para o contrato. Para **tipo de contrato**, selecione **AS2**. Selecione o **anfitrião parceiro**, **identidade do anfitrião**, **convidado parceiro**, e **identidade de convidado** para o contrato.

    ![Forneça os detalhes do contrato](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

    | Propriedade | Descrição |
    | --- | --- |
    | Nome |Nome do contrato |
    | Tipo de contrato | Deve ser AS2 |
    | Parceiro de anfitrião |Um contrato tem de parceiro de um anfitrião e convidado. O parceiro de anfitrião representa a organização que configura o contrato. |
    | Identidade do anfitrião |Um identificador para o parceiro de anfitrião |
    | Parceiro de convidado |Um contrato tem de parceiro de um anfitrião e convidado. O parceiro de convidado representa a organização que está a decorrer empresas com o parceiro de anfitrião. |
    | Identidade de convidado |Um identificador para o parceiro de convidado |
    | Receber definições |Estas propriedades aplicam-se a todas as mensagens recebidas por um contrato. |
    | Enviar definições |Estas propriedades aplicam-se a todas as mensagens enviadas por um contrato. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar a forma como os identificadores de contrato receberam mensagens

Agora que definiu as propriedades de contrato, pode configurar como o presente contrato identifica e processa mensagens a receber foi recebidas pelo seu parceiro através deste contrato.

1.  Em **adicionar**, selecione **receber definições**.
Configure estas propriedades com base no seu contrato com o parceiro que trocas de mensagens consigo. Para descrições das propriedades, consulte a tabela nesta secção.

    ![Configurar "Receber definições"](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)

2. Opcionalmente, pode substituir as propriedades de mensagens a receber selecionando **substituir as propriedades da mensagem**.

3. Para exigir todas as mensagens recebidas sejam assinados, selecione **mensagem deve estar assinada**. Do **certificado** lista, selecione um existente [certificado público de parceiro de convidado](../logic-apps/logic-apps-enterprise-integration-certificates.md) para validar a assinatura nas mensagens. Ou criar o certificado, se não tiver uma.

4.  Para exigir todas as mensagens recebidas sejam encriptados, selecione **mensagem deve ser encriptada**. Do **certificado** lista, selecione um existente [certificado privado de parceiro de anfitrião](../logic-apps/logic-apps-enterprise-integration-certificates.md) para desencriptar mensagens a receber. Ou criar o certificado, se não tiver uma.

5. Para exigir mensagens para ser comprimido, selecione **mensagem deve ser comprimida**.

6. Para enviar uma notificação de disposição mensagem síncrona (MDN) para mensagens recebidas, selecione **enviar MDN**.

7. Para enviar MDNs assinados para mensagens recebidas, selecione **Send iniciada MDN**.

8. Para enviar MDNs assíncronas para mensagens recebidas, selecione **enviar MDN assíncrona**.

9. Depois de terminar, certifique-se guardar as definições ao escolher **OK**.

O contrato está agora pronto para processar mensagens a receber em conformidade com as definições selecionadas.

| Propriedade | Descrição |
| --- | --- |
| Substituir as propriedades da mensagem |Indica se as propriedades de mensagens recebidas podem ser substituídas. |
| Mensagem deve estar assinada |Necessita de ser assinados digitalmente de mensagens. Configure o certificado público de parceiro de convidado para a verificação de assinatura.  |
| Mensagem deve ser encriptada |Requer que as mensagens sejam encriptados. Mensagens encriptadas não são rejeitadas. Para configurar o anfitrião parceiro privada para desencriptar as mensagens.  |
| Mensagem deve ser comprimida |Necessita de mensagens para ser comprimido. Mensagens de compressão não são rejeitadas. |
| Texto MDN |A predefinição disposição notificação de mensagem (MDN) sejam enviados para o remetente da mensagem. |
| Enviar MDN |Requer MDNs para serem enviados. |
| Enviar MDN assinado |Requer MDNs sejam assinadas. |
| Algoritmo de Dinâmicas |Selecione o algoritmo a utilizar para assinar mensagens. |
| Enviar MDN assíncrona | Necessita de mensagens de modo assíncrono. |
| URL | Especifique o URL para onde enviar os MDNs. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar como o seu contrato envia mensagens

Pode configurar a forma como este contrato identifica e processa mensagens de saída que enviam para os parceiros através deste contrato.

1.  Em **adicionar**, selecione **enviar definições**.
Configure estas propriedades com base no seu contrato com o parceiro que trocas de mensagens consigo. Para descrições das propriedades, consulte a tabela nesta secção.

    ![Definir as propriedades de "Definições de envio"](./media/logic-apps-enterprise-integration-agreements/agreement-51.png)

2. Para enviar mensagens assinadas para o seu parceiro, selecione **ativar a assinatura das mensagens**. Para assinar as mensagens no **MIC algoritmo** lista, selecione o *anfitrião parceiro privada no certificado MIC algoritmo*. E o **certificado** lista, selecione um existente [certificado privado de parceiro de anfitrião](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. Para enviar mensagens encriptadas para o parceiro, selecione **ativar a encriptação de mensagens**. Para encriptar as mensagens no **algoritmo de encriptação** lista, selecione o *algoritmos de certificado público de parceiro de convidado*.
E o **certificado** lista, selecione um existente [certificado público de parceiro de convidado](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Para comprimir a mensagem, selecione **ativar a compressão de mensagem**.

5. Para unfold o cabeçalho content-type HTTP para uma única linha, selecione **cabeçalhos de Unfold HTTP**.

6. Para receber MDNs síncronas para mensagens enviadas, selecione **pedido MDN**.

7. Para receber MDNs assinados para as mensagens enviadas, selecione **pedido assinado MDN**.

8. Para receber MDNs assíncronas para as mensagens enviadas, selecione **pedido assíncrono MDN**. Se selecionar esta opção, introduza o URL para onde enviar os MDNs.

9. Para exigir a não rejeição de receção, selecione **ativar NRR**.  

10. Para especificar o formato de algoritmo a utilizar o MIC ou assinatura nos cabeçalhos do enviados da mensagem AS2 ou MDN, selecione **formato SHA2 algoritmo**.  

11. Depois de terminar, certifique-se guardar as definições ao escolher **OK**.

O contrato está agora pronto para processar mensagens de saída que está em conformidade com as definições selecionadas.

| Propriedade | Descrição |
| --- | --- |
| Ativar a assinatura das mensagens |Requer que todas as mensagens que são enviadas pelo contrato sejam assinadas. |
| Algoritmo de Dinâmicas |O algoritmo a utilizar para assinar mensagens. Configura o anfitrião parceiro privada certificado algoritmo Dinâmicas para as mensagens de assinatura. |
| Certificado |Selecione o certificado a utilizar para assinar mensagens. Configura o anfitrião parceiro privada o certificado para assinar as mensagens. |
| Ativar a encriptação de mensagens |Necessita de encriptação de todas as mensagens que são enviadas do presente contrato. Configura o algoritmo de certificado público de parceiro de convidado para encriptar mensagens. |
| Algoritmo de encriptação |O algoritmo de encriptação a utilizar para encriptação de mensagens. Configura o certificado público de parceiro de convidado para encriptar mensagens. |
| Certificado |O certificado a utilizar para encriptar mensagens. Configura o certificado privado de parceiro de convidado para encriptar mensagens. |
| Ativar a compressão de mensagem |Necessita de compressão de todas as mensagens que são enviadas do presente contrato. |
| Unfold cabeçalhos de HTTP |Coloca o cabeçalho content-type HTTP para uma única linha. |
| Pedido MDN |Requer um MDN para todas as mensagens que são enviadas do presente contrato. |
| Pedido assinado MDN |Requer que todos os MDNs que são enviados para este contrato sejam assinados. |
| MDN assíncrona do pedido |Requer MDNs assíncronas sejam enviados para este contrato. |
| URL |Especifique o URL para onde enviar os MDNs. |
| Ativar NRR |Não rejeição de receção (NRR), um atributo de comunicação que fornece uma prova de requer que os dados foram recebidos como resolvido. |
| Formato de algoritmo de SHA2 |Selecione o formato de algoritmo a utilizar o MIC ou assinatura nos cabeçalhos do enviados da mensagem AS2 ou MDN |

## <a name="find-your-created-agreement"></a>Encontrar o contrato criado

1.  Depois de concluir a definição em todas as propriedades de contrato, o **adicionar** painel, escolha **OK** para concluir a criação do seu contrato e regressar ao painel de conta da integração.

    O contrato recém-adicionado agora aparece na sua **contratos** lista.

2.  Também pode ver os contratos na descrição geral da sua integração conta. No painel da conta de integração, escolha **descrição geral**, em seguida, selecione o **contratos** mosaico. 

    ![Escolha o que mosaico "Contratos" para ver todos os contratos](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="view-the-swagger"></a>Ver o swagger
Consulte o [swagger detalhes](/connectors/as2/). 

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise")  

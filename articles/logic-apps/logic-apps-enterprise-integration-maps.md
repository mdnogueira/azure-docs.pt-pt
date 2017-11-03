---
title: "Transformação XML com o maps XSLT - Azure Logic Apps | Microsoft Docs"
description: "Adicionar que XSLT mapeia para transformar dados XML com Azure Logic Apps e o pacote de integração do Enterprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4445a84a6c6425110e7d705019a28b5cc5447046
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-maps-for-xml-data-transform"></a>Adicionar mapeamentos para a transformação de dados XML

Integração empresarial com utiliza os mapas para transformar dados XML entre formatos. Um mapa de é um documento XML que define os dados num documento que deve ser transformado para outro formato. 

## <a name="why-use-maps"></a>Porquê utilizar o maps?

Suponha que regularmente recebe as ordens de B2B ou faturas de um cliente que utiliza o formato YYYMMDD de datas. No entanto, na sua organização, que armazena as datas no formato MMDDYYY. Pode utilizar um mapa para *transformar* no formato de data YYYMMDD para MMDDYYY antes dos detalhes de ordem ou fatura o armazenamento na sua base de dados de atividade do cliente.

## <a name="how-do-i-create-a-map"></a>Como criar um mapa?

Pode criar projetos de BizTalk integração com o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do enterprise") para Visual Studio 2015. Em seguida, pode criar um ficheiro de mapa de integração permite-lhe mapear visualmente itens entre dois ficheiros de esquema XML. Depois de criar este projeto, terá um documento XSLT.

## <a name="how-do-i-add-a-map"></a>Como adicionar um mapa?

1. No portal do Azure, selecione **procurar**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Na caixa de filtro de pesquisa, introduza **integração**, em seguida, selecione **contas de automatização** na lista de resultados.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Selecione a conta de integração em que pretende adicionar o mapeamento.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Selecione o **Maps** mosaico.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Depois de abre o painel de Maps, escolha **adicionar**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Introduza um **nome** para o mapa. Para carregar o ficheiro de mapa, selecione o ícone de pasta no lado direito do **mapa** caixa de texto. Depois de concluir o processo de carregamento, escolha **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Depois de Azure adiciona o mapa para a sua conta de integração, receber uma mensagem de onscreen mostra se o ficheiro de mapa foi adicionado ou não. Após receber esta mensagem, escolha o **Maps** mosaico para que possa vê o mapa adicionado recentemente.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)

## <a name="how-do-i-edit-a-map"></a>Como posso editar um mapa?

Tem de carregar um novo ficheiro de mapa com as alterações que quiser. Primeiro, pode transferir o mapa para edição.

Para carregar um mapa novo que substitui o mapa existente, siga estes passos.

1. Escolha o **Maps** mosaico.

2. Depois de abre o painel de Maps, selecione o mapa de que pretende editar.

3. No **Maps** painel, escolha **atualização**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. No Seletor de ficheiros, selecione o ficheiro de mapa que pretende carregar, em seguida, selecione **abra**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Como eliminar um mapa?

1. Escolha o **Maps** mosaico.

2. Depois de abre o painel de Maps, selecione o mapa de que pretende eliminar.

3. Escolha **eliminar**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Certifique-se de que pretende eliminar o mapa.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise")  
* [Saiba mais sobre contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre contratos de integração do enterprise")  
* [Saiba mais sobre transformações](logic-apps-enterprise-integration-transform.md "Saiba mais sobre transformações de integração empresarial")  


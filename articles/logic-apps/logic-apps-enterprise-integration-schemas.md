---
title: "Esquemas para a validação de XML - Azure Logic Apps | Microsoft Docs"
description: Validar documentos XML com esquemas para o Azure Logic Apps e Enterprise Integration Pack
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4f58a587c1f10aea1cee89e46fa9ec340e0d21c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Validar o XML com esquemas para o Azure Logic Apps e o pacote de integração do Enterprise

Esquemas confirmar que os documentos XML que receberá são válidos e possuem os dados esperados num formato predefinido. Esquemas também ajudam a validar as mensagens que são trocadas num cenário de B2B.

## <a name="add-a-schema"></a>Adicionar um esquema

1. No portal do Azure, selecione **mais serviços**.

    ![Portal do Azure, "Mais serviços"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. Na caixa de filtro de pesquisa, introduza **integração**e selecione **contas de automatização** na lista de resultados.

    ![Caixa de pesquisa de filtro](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Selecione o **conta integração** onde pretende adicionar o esquema.

    ![Lista de contas de automatização](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Escolha o **esquemas** mosaico.

    ![Conta de integração de exemplo, "Esquemas"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Adicionar um ficheiro de esquema inferior a 2 MB

1. No **esquemas** painel que abre (a partir dos passos anteriores), escolha **adicionar**.

    ![Painel de esquemas, "Adicionar"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Introduza um nome para o esquema. Carregue o ficheiro de esquema, selecionando o ícone de pasta junto a **esquema** caixa. Depois de concluir o processo de carregamento, selecione **OK**.

    ![Captura de ecrã de "Adicionar Schema", com "Ficheiro pequeno" realçado](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Adicione um ficheiro de esquema com mais de 2 MB (até 8 MB máximo)

Estes passos divergir com base no nível de acesso de contentor do blob: **pública** ou **sem acesso anónimo**.

**Para determinar este nível de acesso**

1.  Abra **Explorador de armazenamento do Azure**. 

2.  Em **contentores de BLOBs**, selecione o contentor de blob que pretende. 

3.  Selecione **segurança**, **nível de acesso**.

Se o nível de acesso de segurança do blob é **pública**, siga estes passos.

![Explorador de armazenamento do Azure, com "Contentores de BLOBs", "Segurança" e "Público" realçado](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Carregar o esquema para a sua conta de armazenamento e copie o URI.

    ![Conta do Storage, com o URI realçado](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. No **Adicionar esquema**, selecione **ficheiros grandes**e forneça o URI no **URI conteúdo** caixa de texto.

    ![Esquemas, com o botão "Adicionar" e "Ficheiros grandes" realçado](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Se o nível de acesso de segurança do blob é **sem acesso anónimo**, siga estes passos.

![Explorador de armazenamento do Azure, com "Contentores de BLOBs", "Segurança" e "Sem acesso anónimo" realçado](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Carregar o esquema para a sua conta de armazenamento.

    ![Conta de armazenamento](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Gere uma assinatura de acesso partilhado para o esquema.

    ![Conta do Storage, com o separador de assinaturas de acesso partilhado realçado](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. No **Adicionar esquema**, selecione **ficheiros grandes**e forneça a URI de assinatura de acesso partilhado no **URI conteúdo** caixa de texto.

    ![Esquemas, com o botão "Adicionar" e "Ficheiros grandes" realçado](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. No **esquemas** painel da sua conta de integração, o esquema recém-adicionado deve aparecer.

    ![A conta de integração, com "Esquemas" e o novo esquema realçado](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Editar esquemas

1. Escolha o **esquemas** mosaico.

2. Depois do **esquemas** é aberto o painel, selecione o esquema que pretende editar.

3. No **esquemas** painel, escolha **editar**.

    ![Painel de esquemas](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Selecione o ficheiro de esquema que pretende editar, em seguida, selecione **abra**.

    ![Ficheiro de esquema aberta para o editar](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure mostra uma mensagem que o esquema carregada com êxito.

## <a name="delete-schemas"></a>Eliminar esquemas

1. Escolha o **esquemas** mosaico.

2. Depois do **esquemas** é aberto o painel, selecione o esquema que pretende eliminar.

3. No **esquemas** painel, escolha **eliminar**.

    ![Painel de esquemas](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Para confirmar que pretende eliminar o esquema selecionado, escolher **Sim**.

    ![Mensagem de confirmação "Esquema eliminar"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    No **esquemas** painel, a lista de esquema é atualizada e já não inclui o esquema que tenha eliminado.

    ![A integração de conta, com "Esquemas" realçado](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do enterprise").  


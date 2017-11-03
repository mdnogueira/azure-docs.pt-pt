---
title: "Utilize as revisões para atualizar a sua API na API Management do Azure | Microsoft Docs"
description: "Siga os passos deste tutorial para saber como tornar a alterações sem quebra utilizando revisões na API Management."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="make-non-breaking-changes-safely-using-revisions"></a>Efetuar alterações de sem quebra em segurança a utilizar as revisões
Este tutorial descreve como efetuar alterações à sua API com segurança e comunicar as alterações para os programadores.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, terá de criar um serviço de gestão de API e ter uma API existente, pode alterar (em vez de conferência API).

## <a name="about-revisions"></a>Sobre revisões
Quando a API está pronta para começar e começa a ser utilizado pelos programadores, normalmente terá asseguramos no efetuar alterações a essa API -, por isso, não como para interromper os chamadores da sua API. Também é útil permitir que os programadores de saber sobre as alterações que efetuou. Podemos fazer isto na API Management do Azure utilizando **revisões**.

## <a name="walkthrough"></a>Instruções
Esta explicação passo a passo iremos adicionar uma nova revisão, adicione-lhe uma operação e certifique-se revisão atual - criar uma entrada de registo de alterações se.

## <a name="add-a-new-revision"></a>Adicionar uma nova revisão
1. Navegue para o **APIs** página no âmbito do seu serviço de API Management no portal do Azure.
2. Selecione **conferência API** na lista de API, em seguida, selecione o **revisões** separador no menu de perto da parte superior da página.
3. Selecione **+ adicionar revisão**

    > [!TIP]
    > Também pode optar por **adicionar revisão** no menu de contexto (**...** ) na API.
![Menu de revisões perto da parte superior do ecrã](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. Indique uma descrição para a nova revisão, para ajudar a lembrar-se de que será utilizado para.
5. Selecione **criar**
6. Agora é criada a sua nova revisão.

    > [!NOTE]
    > Original API permanece no **revisão 1**. Esta é a revisão que os seus utilizadores irão continuar a chamar, até que optar por fazer uma revisão diferentes atual.

## <a name="make-non-breaking-changes-to-your-revision"></a>Altera sem quebra a revisão
1. Selecione o **Design** separador perto da parte superior do ecrã.
2. Tenha em atenção que o **Seletor de revisão** (diretamente acima separador estrutura) mostra a revisão atual como **revisão 2**.

    > [!TIP]
    > Utilize o Seletor de revisão para alternar entre revisões de que pretende trabalhar.

3. Selecione **+ adicionar operação**.
4. Definir a operação de novo a ser **POST**e o nome & o nome a apresentar da operação como **comentários**
5. **Guardar** a operação de novo.
6. Iremos agora efetuou uma alteração ao **revisão 2**. Utilize o **Seletor de revisão** perto da parte superior da página para voltar ao painel **revisão 1**.
7. Tenha em atenção que a operação de nova não aparece na **revisão 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Efetue a revisão atual e adicionar uma entrada de registo de alteração
1. Selecione o **revisões** separador no menu de perto da parte superior da página.
![O menu de revisão no ecrã de revisão.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. Abra o menu de contexto (**...** ) para **revisão 2**.
3. Selecione **tornar atual**.
![Efetue a revisão atual e publique ao alterar o registo](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. Selecione **Post para o registo de alterações públicos para esta API**
5. Indique uma descrição para a sua alteração que os programadores vê, por exemplo **"Adicionar nova operação da comentários".**
6. **Revisão 2** está agora atual.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Procurar o portal do programador para ver as alterações e alterar o registo
1. Selecione **Portal do programador** no menu superior.
2. Selecione **APIs**e, em seguida, selecione **conferência API**.
3. Tenha em atenção a nova **comentários** está agora disponível a operação.
4. Selecione **histórico de alterações de API** partir abaixo o nome da API.
5. Tenha em atenção que a entrada de registo de alterações for apresentada nesta lista.
![Registo de alterações no Portal de programador](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>Passos seguintes
[Publicar versões de API com a API Management do Azure](#api-management-getstarted-publish-versions.md)
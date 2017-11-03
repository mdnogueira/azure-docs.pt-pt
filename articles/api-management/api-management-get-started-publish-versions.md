---
title: "Publicar versões da sua API utilizando a API Management do Azure | Microsoft Docs"
description: "Siga os passos deste tutorial para saber como publicar as várias versões na API Management."
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
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>Publicar várias versões da sua API de forma previsível
Este tutorial descreve como configurar as versões da sua API e escolha a forma como são denominados pelos programadores de API.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, terá de criar um serviço de gestão de API e ter uma API existente, pode alterar (em vez de conferência API).

## <a name="about-versions"></a>Sobre as versões
Por vezes é impractical ter todos os chamadores à sua API utilizar exatamente a mesma versão. Por vezes, que pretende publicar novas ou diferentes funcionalidades de API a alguns utilizadores, enquanto outros pretendem stick com a API atualmente funciona para os mesmos. Quando os chamadores pretendem atualizar para uma versão posterior, pretende efetuar esta utilizando uma fácil de compreender a abordagem.  Podemos fazer isto utilizando **versões** na API Management do Azure.

## <a name="walkthrough"></a>Instruções
Esta explicação passo a passo, adicione uma nova versão para uma API existente, escolha um identificador de esquema e a versão do controlo de versões.

## <a name="add-a-new-version"></a>Adicionar uma nova versão
![Menu de contexto de API - adicionar versão](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Navegue para o **APIs** página no âmbito do seu serviço de API Management no portal do Azure.
2. Selecione **conferência API** na lista de API, em seguida, selecione o menu de contexto (**...** ) junto ao mesmo.
3. Selecione **+ adicionar versão**.

    > [!TIP]
    > Também podem ser ativadas a versões ao primeiro criar uma nova API - selecione **versão esta API?** no **adicionar API** ecrã.

## <a name="choose-a-versioning-scheme"></a>Escolha um esquema de controlo de versões
Gestão de API do Azure permite-lhe escolher a forma nos quais permite os chamadores especificar que versão de API pretendem. Fazê-lo ao escolher um **esquema de controlo de versões**. Este esquema pode ser **cadeia de caminho, consulta ou cabeçalho**. No nosso exemplo, utilizamos o caminho.
![Adicionar o ecrã de versão](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. Deixe **caminho** selecionado como o **esquema de controlo de versões**.
2. Adicionar **v1** como seu **identificador da versão**.

    > [!TIP]
    > Se selecionar **cabeçalho** ou **cadeia de consulta** como um esquema de controlo de versões, terá de fornecer um valor adicional - o nome do cabeçalho ou parâmetro de cadeia de consulta.

3. Forneça uma descrição se desejar.
4. Selecione **criar** para configurar a nova versão.
5. Underneath **grande conferência API** na lista de API, agora, ver APIs distintas dois - **Original**, e **v1**.
![Versões listadas sob uma API no portal do Azure](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Se adicionar uma versão de uma API não com a versão, criamos sempre um **Original** para si - a responder no URL predefinido. Isto garante que qualquer os chamadores existentes não são divididos pelo processo de adição de uma versão. Se criar uma nova API com versões ativadas no início, não é criado um Original.

6. Agora pode editar e configurar **v1** como uma API que é completamente independente para **Original**. As alterações a uma versão não afetam a outra.

## <a name="add-the-version-to-a-product"></a>Adicionar a versão a um produto
Para os chamadores ver a nova versão, tem de ser adicionado a um **produto** (produtos não são herdados de versões de principal).

1. Selecione **produtos** da página de gestão de serviço.
2. Selecione **ilimitados**.
3. Selecione **APIs**.
4. Selecione **Adicionar**.
5. Selecione **conferência API, versão v1**.
6. Regressar à página de gestão de serviço e selecionar **APIs**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Navegue até ao portal de programador para ver a versão
1. Selecione **Portal do programador** no menu superior.
2. Selecione **APIs**, repare que **conferência API** mostra **Original** e **v1** versões.
3. Selecione **v1**.
4. Tenha em atenção o **URL pedido** da operação primeiro na lista. Indica que o caminho de URL de API inclui **v1**.
![Versão apresentada no portal do Programador](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)

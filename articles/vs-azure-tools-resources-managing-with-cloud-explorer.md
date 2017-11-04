---
title: Gerir recursos do Azure com o Explorador de nuvem | Microsoft Docs
description: Saiba como utilizar o Explorador de nuvem para procurar e gerir recursos do Azure no Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/25/2017
ms.author: kraigb
ms.openlocfilehash: 6e6d8d559f0251b71bfa6d529ead82a246cb3235
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Gerir os recursos associados as contas do Azure no Visual Studio Cloud Explorer
Explorador de nuvem permite-lhe ver os recursos do Azure e os grupos de recursos, inspecionar as respetivas propriedades e efetuar ações de diagnóstico de chave para programadores no Visual Studio. 

Como o [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), Cloud Explorer baseia-se na pilha do Azure Resource Manager. Por conseguinte, o Cloud Explorer compreende recursos, tais como grupos de recursos do Azure e serviços do Azure, tais como aplicações lógicas e API apps e suporta [controlo de acesso baseado em funções](active-directory/role-based-access-control-configure.md) (RBAC). 

## <a name="prerequisites"></a>Pré-requisitos
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) com o **carga de trabalho do Azure** selecionado, ou uma versão anterior do Visual Studio com o [Microsoft Azure SDK para .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Conta do Microsoft Azure - se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=623901) ou [ativar os benefícios de subscritor do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Para ver Cloud Explorer, selecione **vista** > **Cloud Explorer** na barra de menus.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Adicionar um Azure conta Cloud Explorer
Para ver os recursos associados a uma conta do Azure, primeiro tem de adicionar a conta para Cloud Explorer. 

1. No **Cloud Explorer**, selecione **as definições da conta do Azure**.

    ![Ícone de definições de conta do cloud Explorer Azure](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Selecione **Adicionar nova conta**. 

    ![Ligação de adicionar a conta do Explorador de nuvem](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Início de sessão para a conta do Azure cujos recursos que pretende procurar. 

1. Assim que tiver sessão iniciada conta do Azure, apresentam as subscrições associadas com essa conta. Selecione as caixas de verificação para as subscrições da conta que pretende procurar e, em seguida, selecione **aplicar**. 
 
    ![Explorador de nuvem: selecione as subscrições do Azure para apresentar](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Depois de selecionar as subscrições cujos recursos que pretende procurar, apresentam essas subscrições e recursos no Explorador de nuvem.

    ![Nuvem recursos Explorer listagem numa conta do Azure](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Remover uma conta do Azure a partir do Explorador de nuvem 

1. No **Cloud Explorer**, selecione **as definições da conta do Azure**.

    ![Ícone de definições de conta do cloud Explorer Azure](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Junto a conta que pretende remover, selecione **remover**.

    ![Ícone de definições de conta do cloud Explorer Azure](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Ver os tipos de recurso ou grupos de recursos
Para ver os recursos do Azure, pode escolher uma **tipos de recursos** ou **grupos de recursos** vista.

1. No **Cloud Explorer**, selecione a lista pendente de vista de recursos.

    ![Cloud Explorer na lista pendente para selecionar a vista de recursos pretendida](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. No menu de contexto, selecione a vista pretendida: 

    - **Tipos de recursos** visualizar - a vista comuns utilizada no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), mostra os recursos do Azure categorizados pelo respetivo tipo, tais como web apps, contas de armazenamento e máquinas virtuais. 
    - **Grupos de recursos** ver - recursos do Azure categoriza pelo grupo de recursos do Azure com a qual está associados. Um grupo de recursos é um pacote de recursos do Azure, normalmente utilizadas por uma aplicação específica. Para obter mais informações sobre grupos de recursos do Azure, consulte [descrição geral do Azure Resource Manager](./azure-resource-manager/resource-group-overview.md).

    A imagem seguinte mostra uma comparação das vistas dois recursos:

    ![Comparação de vistas de recursos do Explorador da nuvem](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Ver e navegue recursos no Cloud Explorer
Para navegar para um recurso do Azure e ver a informação no Cloud Explorer, expanda o item tipo ou grupo de recursos associados e, em seguida, selecione o recurso. Quando seleciona um recurso, informações aparecem na dois separadores - **ações** e **propriedades** - na parte inferior da Cloud Explorer. 

- **Ações** separador - lista as ações que pode tomar no Cloud Explorer para o recurso selecionado. Também pode ver estas opções clicando com o recurso para visualizar o menu de contexto.

- **Propriedades** separador - mostra as propriedades de recursos, tais como o respetivo grupo de recursos, região e tipo a que está associada.

A imagem seguinte mostra uma comparação de exemplo de o que vê em cada separador, para um serviço de aplicações:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Cada recurso tem a ação **aberto no portal**. Ao escolher esta ação, Cloud Explorer apresenta o recurso selecionado no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). O **aberto no portal** funcionalidade é útil para navegar para recursos profundamente aninhados.

Ações adicionais e valores de propriedade também podem aparecer com base no recurso do Azure. Por exemplo, as web apps e logic apps também de ter as ações **abertos no browser** e **anexar depurador** além **aberto no portal**. Ações para abrir editores aparecem quando seleciona um blob de conta de armazenamento, uma fila ou uma tabela. As aplicações do Azure têm **URL** e **estado** propriedades, enquanto os recursos de armazenamento ter propriedades de cadeia de ligação e a chave.

## <a name="find-resources-in-cloud-explorer"></a>Localizar recursos no Cloud Explorer
Para localizar recursos com um nome específico nas suas subscrições de conta do Azure, introduza o nome no **pesquisa** caixa no Cloud Explorer.

![Localizar recursos na Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

Como a introduzir carateres da **pesquisa** apresentada a caixa, apenas os recursos que correspondam a esses carateres na árvore de recursos.

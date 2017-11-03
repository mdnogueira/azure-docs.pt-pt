---
title: "Guardar pesquisas e afixar recursos de dados no catálogo de dados do Azure | Microsoft Docs"
description: "Artigo procedimentos realce capacidades do catálogo de dados do Azure para guardar as origens de dados e recursos de dados para utilização posterior."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 8c319d0dcbe8b95af11b8be2368a9348b260446c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Guardar pesquisas e os recursos de dados de pin no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
Catálogo de dados do Azure fornece capacidades de deteção de origem de dados. Pode, rapidamente, procurar e filtrar o catálogo para localizar as origens de dados e compreender o respetivo objetivo pretendido, tornando mais fácil localizar os dados adequados para a tarefa em execução.

Mas e se tem de trabalhar regularmente com os mesmos dados? E e se lhe e outros utilizadores regularmente contribuem o conhecimento do utilizador para os mesmo origens de dados no catálogo? Nestas situações, a necessidade de emitir repetidamente as mesmas pesquisas pode ser ineficaz. Este é onde podem ajudar a pesquisa guardada e recursos de dados afixados.

## <a name="saved-searches"></a>Pesquisas guardadas
Uma pesquisa guardada no catálogo de dados é uma definição de procura por utilizador reutilizáveis. Pode definir uma pesquisa, incluindo os termos da procura, etiquetas e outros filtros e, em seguida, guardá-lo. Pode voltar a executar a definição de pesquisa guardada mais tarde para voltar a quaisquer recursos de dados que correspondem aos seus critérios de pesquisa.

### <a name="create-a-saved-search"></a>Criar uma procura guardada
Para criar uma procura guardada, efetue o seguinte:
1. No portal do catálogo de dados do Azure, no **pesquisa atual** janela, clique em **guardar**. 

    ![Ligação de gravação de definições de pesquisa atual](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Introduza os critérios de pesquisa que pretende reutilizar e, em seguida, clique em **guardar**.

    ![As definições atuais de pesquisa guardadas nome da pesquisa](./media/data-catalog-how-to-save-pin/02-name.png)

3. Quando lhe for pedido, introduza um nome para a pesquisa guardada. Escolha um nome que faça sentido e que descreve os recursos de dados que serão devolvidos pela pesquisa.

### <a name="manage-saved-searches"></a>Gerir as pesquisas guardadas
Depois de guardar pesquisas de um ou mais, um **pesquisas guardadas** opção é apresentada abaixo o **pesquisa atual** caixa. Quando a lista está expandida, todas as pesquisas guardadas são apresentadas.

 ![Lista de pesquisas guardadas](./media/data-catalog-how-to-save-pin/03-list.png)

Efetue qualquer uma das seguintes:

* Para executar uma pesquisa, selecione uma procura guardada na lista.

* Para ver uma lista de opções de gestão para uma procura guardada, clique na seta para baixo junto ao nome da pesquisa.

    ![Opções para gerir as pesquisas guardadas](./media/data-catalog-how-to-save-pin/04-managing.png)

* Introduza um novo nome para a pesquisa guardada, selecione **mudar o nome**. A definição de pesquisa não é alterada.

* Para remover a pesquisa guardada na sua lista, selecione **eliminar**e, em seguida, confirme a eliminação.

* Para marcar a pesquisa guardada como a pesquisa de predefinido, selecione **guardar como predefinição**. Se efetuar uma pesquisa de "vazia" da home page do catálogo de dados do Azure, é executada a pesquisa de predefinição. Além disso, a pesquisa que está marcada como a pesquisa de predefinida é apresentada na parte superior do **pesquisas guardadas** lista.

### <a name="organizational-saved-searches"></a>Organizacionais procuras guardadas
Todos os utilizadores na sua organização podem guardar pesquisas para as seus próprios utilização. Os administradores do catálogo de dados também podem guardar pesquisas para todos os utilizadores dentro da organização. Quando os administradores guarde uma pesquisa, estes apresentado com uma **partilha dentro da empresa** opção. A seleção desta opção partilha a pesquisa guardada para todos os utilizadores na organização.

 ![Organizacionais procuras guardadas](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Recursos de dados afixado
Com pesquisas guardadas, pode guardar e reutilizar as definições de pesquisa. Poderão alterar os recursos de dados que são devolvidos pelas procuras ao longo do tempo, como o conteúdo da alteração de catálogo. Quando o pin recursos de dados, pode identificar recursos de dados específicos para os tornar mais fácil de acesso sem necessidade de utilizar uma procura explicitamente.

Um recurso de dados de afixação é simples. Para adicionar o elemento de dados à sua lista de afixado, basta clicar no **pin** ícone. O ícone é apresentado no canto do mosaico asset na vista em mosaico e na coluna mais à esquerda na vista de lista no portal do catálogo de dados do Azure.

![O ícone de pin de recurso de dados](./media/data-catalog-how-to-save-pin/05-pinning.png)

Anulação da fixação um recurso de dados é igualmente simples. Basta clicar o **remover** ícone para ativar/desativar a definição para o elemento selecionado.

![Ícone de remover o recurso de dados](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A secção os meus recursos
A home page de portal do catálogo de dados inclui um **os meus recursos** secção que apresenta os ativos de interesse para o utilizador atual. Esta secção inclui os dois recursos afixados e pesquisas guardadas.

![A secção os meus recursos na home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumo
Catálogo de dados do Azure fornece capacidades que facilitam a detetar as origens de dados que tem de, pelo que é e por outros membros da organização podem gaste menos tempo a procurar dados e a hora mais trabalhar com o mesmo. Pesquisas guardadas e afixado dados ativos tirar partido destas capacidades de núcleo para que os utilizadores podem identificar facilmente origens de dados que funcionam com repetidamente.

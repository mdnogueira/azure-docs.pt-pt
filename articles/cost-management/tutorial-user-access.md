---
title: "Atribuir acesso na gestão de custo do Azure | Microsoft Docs"
description: "Atribua acesso a dados de gestão com contas de utilizador que definem os níveis de acesso para entidades de custos."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a42f3b51bf6d888d0d5602887ed317c6164391ef
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="assign-access-to-cost-management-data"></a>Atribuir acesso a dados de gestão de custos

Acesso aos dados de gestão de custos é fornecido pelo utilizador ou a gestão de entidade. Contas de utilizador Cloudyn determinam o acesso ao *entidades* e funções administrativas. Tipos de existem dois de acesso: administrador e utilizador. A menos que modificadas por utilizador, o acesso de administrador permite uma utilização de utilizador sem restrições de todas as funções no portal do Cloudyn, incluindo: gestão de utilizadores, a gestão de apresenta uma lista de destinatários e a entidade de raiz de acesso a todos os dados de entidade. Acesso de utilizadores destina-se de que os utilizadores finais ver relatórios e criar relatórios utilizando o acesso têm de dados de entidade.

Entidades são utilizadas para refletir a estrutura hierárquica da sua organização de negócio. Estes identificam departamentos, divisões e equipas na sua organização em Cloudyn. A hierarquia de entidades ajuda a monitorizar eficazmente os gastos pelas entidades.

Quando se registou-se ao seu contrato do Azure ou a conta, uma conta com permissões de administrador foi criada num Cloudyn, pelo que pode executar todos os passos neste tutorial. Este tutorial abrange o acesso aos dados de gestão de custo, incluindo gestão de utilizador e de entidade. Saiba como:

> [!div class="checklist"]
> * Criar um utilizador com acesso de administrador
> * Criar um utilizador com acesso de utilizador
> * Criar entidades



## <a name="create-a-user-with-admin-access"></a>Criar um utilizador com acesso de administrador

Apesar de já ter acesso de administrador, seus colegas na sua organização poderão também tem de ter acesso de administrador. No portal do Cloudyn, clique o símbolo de equipamento no canto superior direito e selecione **gestão de utilizadores**. Clique em **adicionar novo utilizador** para adicionar um novo utilizador.

Introduza as informações necessárias sobre o utilizador. Pode deixar o campo de palavra-passe vazia para que o utilizador pode definir uma nova palavra-passe no primeiro início de sessão. Uma ligação com informações de início de sessão é enviada para o utilizador por correio electrónico do Cloudyn Quando seleciona **notificar por utilizador por correio eletrónico**. Escolha as permissões para permitir a gestão de utilizadores para que o utilizador pode criar e modificar a outros utilizadores. Apresenta uma lista de gestão de destinatários para permitir ao utilizador editar apresenta uma lista de destinatário.

Em **utilizador tem acesso de administrador**, a entidade de raiz da sua organização estiver selecionada. Deixe selecionada raiz e, em seguida, guarde as informações de utilizador. Selecionar a entidade de raiz permite que o utilizador tem permissão de administrador não só para a entidade de raiz na árvore, mas também para todas as entidades que residem abaixo do mesmo.  
  ![Adicionar novo utilizador com acesso de administrador](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Criar um utilizador com acesso de utilizador
Utilizadores típicos que precisam de acesso a dados de gestão de custos, como os dashboards e relatórios devem ter acesso de utilizador a visualizá-las. Crie um novo utilizador com acesso de utilizador semelhantes às que criou com acesso de administrador, com as seguintes diferenças:

- Limpar **permitir a gestão de utilizadores**, **permitir destinatário apresenta uma lista de gestão**e desmarque tudo no **utilizador tem acesso de administrador** lista.
- Selecione as entidades que o utilizador precisa de acesso no **utilizador tem acesso de utilizador** lista.
- Pode também autorizar administrador de acesso a entidades específicas, conforme necessário.

![Adicionar novo utilizador com acesso de utilizador](.\media\tutorial-user-access\new-user-access.png)

Para ver um vídeo tutorial sobre como adicionar utilizadores, consulte o artigo [adicionar utilizadores para o Azure custo Management Cloudyn](https://youtu.be/Nzn7GLahx30).

## <a name="create-entities"></a>Criar entidades

Quando definir a hierarquia de entidades de custo, é uma melhor prática identificar a estrutura da sua organização.

À medida que cria a árvore, considere como pretende ou precisa ver os seus custos segregados pela custo centros, ambientes e departamentos de vendas, unidades de negócio. A árvore de entidade no Cloudyn é flexível devido a herança de entidade. Subscrições individuais para as contas de nuvem estão ligadas para entidades específicas. Por isso, as entidades são multi-inquilinos. Pode atribuir utilizadores específicos acesso a apenas os respetivos segmento da sua empresa através de entidades. Se o fizer, mantém os dados isolados, mesmo em grandes partes de uma empresa como subsidiárias. Além disso, ajuda-o isolamento de dados com governação.  

Quando registar o seu contrato do Azure ou a conta com Cloudyn, os dados de recursos do Azure, incluindo a utilização, desempenho, faturação e dados da etiqueta do seu subscrições foi copiados para a sua conta Cloudyn. No entanto, tem de criar manualmente a árvore de entidade. Caso tenha ignorado o registo do Azure Resource Manager, em seguida, apenas dados de faturação e alguns relatórios do asset estão disponíveis no portal do Cloudyn.

No portal do Cloudyn, clique em **definições** no canto superior direito e selecione **contas na nuvem**. Começar com uma única entidade (root) e criar a árvore de entidade em raiz. Eis um exemplo de uma hierarquia de entidades que poderá assemelhar-se as organizações de TI muitos após a conclusão da árvore:

![árvore de entidade](.\media\tutorial-user-access\entity-tree.png)

Junto a **entidades**, clique em **adicionar entidade**. Introduza as informações relativas a pessoa ou o departamento de que pretende adicionar. O **nome completo** e **E-Mail** campos para tem de corresponder ao utilizadores existentes. Se pretender ver uma lista dos níveis de acesso, procure em Ajuda para *adicionar uma entidade*.

![Adicionar entidade](.\media\tutorial-user-access\add-entity.png)

Quando tiver terminado, **guardar** a entidade.


Para ver um vídeo tutorial sobre a criação de uma hierarquia de entidades de custo, consulte o artigo [criação de uma hierarquia de entidades de custo no Azure custo Management Cloudyn](https://youtu.be/dAd9G7u0FmU).

Se for um utilizador do Azure Enterprise Agreement, ver um vídeo tutorial sobre a associação de contas e as subscrições para entidades em [ligar para o Azure Resource Manager com o Azure custo Management Cloudyn](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um utilizador com acesso de administrador
> * Criar um utilizador com acesso de utilizador
> * Criar entidades

Avançar para o próximo tutorial para aprender a prever despesas com os dados históricos.

> [!div class="nextstepaction"]
> [Prever despesas futuras](tutorial-forecast-spending.md)

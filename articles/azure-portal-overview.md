---
title: "Descrição geral do portal do Azure | Microsoft Docs"
description: Saiba como utilizar o portal do Microsoft Azure.
services: 
documentationcenter: 
author: davidwrede
manager: erikre
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/16/2015
ms.author: dwrede
ms.openlocfilehash: 71820306716c6297085a29f3ceab89b55396bfe6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-portal-overview"></a>Descrição geral do portal do Microsoft Azure
O portal do Microsoft Azure é um local central onde pode aprovisionar e gerir os recursos do Azure.  Este tutorial irá familiarizá-lo com o portal e mostrar-lhe como utilizar algumas dessas capacidades essenciais:

* Um **mercado abrangente** que lhe permite navegar em milhares de itens da Microsoft e de outros fornecedores que podem ser comprados e/ou aprovisionados.
* Uma **experiência de browser unificada e dimensionável** que torna mais fácil encontrar os recursos que lhe interessam e desempenhar várias operações de gestão.
* **Páginas de gestão consistentes** (ou painéis) que lhe permitem gerir a ampla variedade de serviços do Azure através de uma forma consistente de exposição de definições, ações, informações de faturação, dados de utilização e de monitorização do estado de funcionamento e muito mais.
* Uma **experiência pessoal** que lhe permite criar um ecrã inicial personalizado que mostra as informações que pretende ver sempre que inicia sessão.  Também pode personalizar qualquer um dos painéis de gestão que contêm os mosaicos.
  
  ![Orientação da IU do Portal do Azure][UIOrientation]

## <a name="before-you-get-started"></a>Antes de começar
Necessitará de uma subscrição do Azure válida par avançar neste tutorial.  Se não tiver uma, [inscreva-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) hoje.  Assim que tiver uma subscrição, pode aceder ao portal, em <https://portal.azure.com>.

## <a name="how-to-create-a-resource"></a>Como criar um recurso
O Azure tem um mercado com milhares de itens que pode criar a partir de um único local.  Digamos que pretende criar uma nova VM do Windows Server 2012.  O hub +NEW é o seu ponto de entrada para um conjunto organizado de categorias em destaque do mercado.  Cada categoria tem um pequeno conjunto de itens em destaque juntamente com uma ligação para o mercado completo que mostra todas as categorias e a pesquisa. Para criar essa nova VM do Windows Server 2012, execute as seguintes ações:  

1. O Windows Server 2012 está incluído, pelo que o pode selecionar na categoria Computação.  
2. Preencha algumas entradas básicas num formulário.
3. Clique em “Criar” e a VM começará a aprovisionar imediatamente.

O hub de notificações alerta-o quando o recurso tiver sido criado e abre-se um painel de gestão (pode sempre navegar até aos recursos mais tarde).

![Categorias do Portal][PortalCategories]

## <a name="how-to-find-your-resources"></a>Como encontrar os seus recursos
Pode sempre afixar ao Startboard os recursos a que acede com frequência, mas é provável que tenha de procurar algo a que não acede frequentemente.  O hub de navegação mostrado abaixo é a forma de aceder a todos os seus recursos.  Pode filtrar por subscrição, escolher/redimensionar colunas e navegar até aos painéis de gestão ao clicar em itens individuais.

![Hub de Navegação][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Como gerir e delegar o acesso a um recurso
A partir deste painel, pode ligar-se à máquina virtual utilizando o ambiente de trabalho remoto, monitorizar métricas de desempenho essenciais, controlar o acesso a esta VM com o acesso baseado em funções (RBAC), configurar a VM e efetuar outras tarefas de gestão importantes.  Delegar o acesso baseado na função é fundamental para a gestão à escala.  Clique [aqui](active-directory/role-based-access-control-configure.md) para saber mais acerca do mesmo. Para delegar o acesso a um recurso, execute as seguintes ações:

1. Navegue até ao recurso.
2. Clique em “Todas as definições” na secção Essentials.
3. Na lista de definições, clique em “Utilizadores”.
4. Na barra de comando, clique em “Adicionar”.
5. Escolha um utilizador e uma função.

![Gerir um recurso][ManageResource]

## <a name="how-to-get-help"></a>Como obter ajuda
Se tiver algum problema, estamos à sua disposição.  O portal tem uma página de ajuda e suporte que o pode orientar na direção certa.  Dependendo do seu [plano de suporte](https://azure.microsoft.com/support/plans/), também pode criar pedidos de suporte diretamente no portal.  Depois de criar um pedido de suporte, pode gerir o ciclo de vida do mesmo no portal. Pode aceder à página de ajuda e suporte ao navegar até Procurar -> Ajuda + suporte.  

![Ajuda e suporte][HelpSupport]

## <a name="summary"></a>Resumo
Vamos rever o que aprendeu neste tutorial:

* Aprendeu a inscrever-se, obter uma subscrição e navegar até ao portal
* Obteve orientação em relação à IU do portal e aprendeu a criar e a procurar recursos
* Aprendeu a criar um recurso e a procurar recursos
* Obteve informações sobre os painéis da estrutura ou de gestão e como pode gerir consistentemente diferentes tipos de recursos
* Aprendeu a personalizar o portal para trazer as informações do seu interesse para a frente e para o centro
* Aprendeu como controlar o acesso aos recursos através de acesso baseado em funções (RBAC)
* Aprendeu como obter ajuda e suporte

O portal do Microsoft Azure simplifica de forma radical a criação e a gestão das suas aplicações na nuvem.  Consulte o [blogue de gestão](https://azure.microsoft.com/blog/topics/management/) para se manter atualizado, pois estamos constantemente a [receber comentários](https://feedback.azure.com/forums/223579-azure-preview-portal/) e a efetuar melhoramentos.  O [blogue do ScottGu](http://weblogs.asp.net/scottgu) é outro local excelente para ver todas as atualizações do Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png

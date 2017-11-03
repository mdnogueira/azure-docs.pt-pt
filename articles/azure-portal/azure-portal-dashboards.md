---
title: Criar e partilhar dashboards de portais do Azure | Microsoft Docs
description: Este artigo explica como criar e editar dashboards no portal do Azure.
services: azure-portal
documentationcenter: 
author: sewatson
manager: timlt
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: multiple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: sewatson
ms.openlocfilehash: 5429e68723448ff5db6ef0ed8da1b927e97e6dd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e partilhar os dashboards no portal do Azure
Pode criar dashboards vários e partilhá-los com outras pessoas que têm acesso às suas subscrições do Azure.  Este artigo abordar as noções básicas de criação, edição, publicar e gerir o acesso aos dashboards.

## <a name="create-a-dashboard"></a>Criar um dashboard
Para criar um dashboard, selecione o **novo dashboard** botão junto ao nome do dashboard atual.  

![criar o dashboard](./media/azure-portal-dashboards/new-dashboard.png)

Esta ação cria um dashboard novo, vazio, privado e coloca-o no modo de personalização onde pode nome o dashboard e adicionar ou reorganizar os mosaicos.  Neste modo, a galeria do mosaico expansível assume o menu de navegação esquerdo.  A galeria do mosaico permite-lhe localizar mosaicos para os seus recursos do Azure de várias formas: pode procurar por [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), por tipo de recurso, por [tag](../azure-resource-manager/resource-group-using-tags.md), ou ao pesquisar para o seu recurso por nome.  

![Personalizar o dashboard](./media/azure-portal-dashboards/customize-dashboard.png)

Adicione peças de mosaicos e largue-os para a superfície de dashboard onde quiser.

Há uma nova categoria designada **geral** para mosaicos não estão associados um recurso específico.  Neste exemplo, vamos afixe o mosaico de Markdown.  Utilize este mosaico para adicionar conteúdo personalizado ao seu dashboard.  O mosaico suporta texto simples, [sintaxe de Markdown](https://daringfireball.net/projects/markdown/syntax)e um conjunto limitado de HTML.  (Para segurança, que não pode fazer coisas como injetar `<script>` etiquetas ou utilizar determinado elemento estilo CSS pode interferir com o portal.) 

![adicionar o markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Editar um dashboard
Depois de criar o dashboard, pode afixar mosaicos da galeria do mosaico ou a representação do mosaico de painéis. Vamos afixar a representação do nosso grupo de recursos. Pode um pin ao procurar o item ou a partir do painel do grupo de recursos. Ambas as abordagens resultam em afixação a representação de mosaico do grupo de recursos.

![Afixar ao dashboard](./media/azure-portal-dashboards/pin-to-dashboard.png)

Após da afixação o item, a mesma aparece no dashboard.

![Veja o dashboard](./media/azure-portal-dashboards/view-dashboard.png)

Agora que temos um mosaico de Markdown e um grupo de recursos afixado ao dashboard, mas pode redimensionar e reorganizar os mosaicos para um esquema adequado.

Por posicionado e selecionando "..." ou clicar num mosaico, pode ver todos os comandos contextuais para este mosaico. Por predefinição, existem dois itens:

1. **Remover a partir do dashboard** – remove o mosaico do dashboard
2. **Personalizar** – entra modo de personalização

![Personalizar o mosaico](./media/azure-portal-dashboards/customize-tile.png)

Selecionando personalizar, pode redimensionar e reordenar os mosaicos. Para redimensionar um mosaico, selecione o novo tamanho do menu de nível contextual das, conforme mostrado na imagem seguinte.

![redimensionar o mosaico](./media/azure-portal-dashboards/resize-tile.png)

Em alternativa, se o mosaico suporta qualquer tamanho, pode arrastar canto inferior direito para o tamanho pretendido.

![redimensionar o mosaico](./media/azure-portal-dashboards/resize-corner.png)

Depois de o redimensionamento de mosaicos, ver o dashboard.

![mosaico de vista](./media/azure-portal-dashboards/view-tile.png)

Quando tiver terminado de personalizar um dashboard, basta selecionar a **feito personalizar** para sair personalizar modo ou com o botão direito e selecione **feito personalizar** no menu de contexto.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publicar um dashboard e gerir o controlo de acesso
Quando cria um dashboard, é privado por predefinição, o que significa que é o único utilizador possa vê-la.  Para torná-lo visível a outras pessoas, utilize o **partilha** botão que é apresentada juntamente com outros comandos do dashboard.

![Partilhe o dashboard](./media/azure-portal-dashboards/share-dashboard.png)

É-lhe pedido para escolher uma subscrição e o grupo de recursos para o seu dashboard para ser publicado. Para perfeitamente integrar dashboards do ecossistema, iremos tiver implementado dashboards partilhados como recursos do Azure (para que não é possível partilhar, escrevendo um endereço de e-mail).  Acesso para as informações apresentadas pela maioria dos mosaicos no portal são regidos pelas [controlo de acesso baseado em do Azure funções](../active-directory/role-based-access-control-configure.md). De uma perspetiva de controlo de acesso partilhados dashboards são não diferentes de uma máquina virtual ou uma conta de armazenamento.  

Vamos supor que tiver uma subscrição do Azure e os membros da sua equipa foram atribuídos as funções de **proprietário**, **contribuinte**, ou **leitor** da subscrição.  Os utilizadores que são proprietários ou contribuintes são capazes de lista, ver, criar, modificar ou eliminar dashboards dentro dessa subscrição.  Os utilizadores que são leitores são capazes de lista e vista dashboards, mas não é possível modificar ou eliminar.  Os utilizadores com acesso de leitor são capazes de efetuar edições de locais para um dashboard partilhado, mas não conseguem publicar as alterações no servidor.  No entanto, fazer uma cópia do dashboard para os seus próprios utilização privada.  Como sempre, individuais mosaicos no dashboard impor as suas próprias regras de controlo de acesso baseadas nos recursos que correspondem a.  

Para sua comodidade, o portal da publicação de guias de experiência, para um padrão onde colocar dashboards num grupo de recursos chamado **dashboards**.  

![Publicar o dashboard](./media/azure-portal-dashboards/publish-dashboard.png)

Também pode optar por publicar um dashboard a um grupo de recurso específico.  O controlo de acesso para esse dashboard corresponde o controlo de acesso para o grupo de recursos.  Os utilizadores que podem gerir os recursos nesse grupo de recursos também tem acesso para os dashboards.

![publicar o dashboard para o grupo de recursos](./media/azure-portal-dashboards/publish-to-resource-group.png)

Depois do dashboard é publicado, a **partilha + acesso** painel de controlo irá atualizar e mostrar-lhe informações sobre o dashboard publicado, incluindo uma ligação para gerir o acesso de utilizador para o dashboard.  Esta ligação inicia o painel de controlo de acesso baseado em funções padrão utilizado para gerir o acesso para qualquer recurso do Azure.  Pode sempre regressar a esta vista selecionando **partilha**.

![Gerir o controlo de acesso](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Passos seguintes
* Para gerir recursos, consulte [recursos do Azure de gerir através do portal](../azure-resource-manager/resource-group-portal.md).
* Para implementar os recursos, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).


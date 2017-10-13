---
title: "Introdução ao Modelos privados | Microsoft Docs"
description: Adicionar, gerir e partilhar os seus modelos privados com o Portal do Azure, o Azure CLI ou PowerShell.
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Introdução ao Modelos privados no Portal do Azure
Um modelo do [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) é um modelo declarativo utilizado para definir a implementação. Pode definir os recursos a implementar para uma solução e especificar os parâmetros e as variáveis que permitem introduzir os valores para vários ambientes. O modelo é constituído por JSON e expressões que pode utilizar para construir valores para a implementação.

Pode utilizar a nova capacidade **Modelos** no [Portal do Azure](https://portal.azure.com), juntamente com o fornecedor de recursos **Microsoft.Gallery** como uma extensão do [Azure Marketplace](https://azure.microsoft.com/marketplace/), para permitir que os utilizadores criem, giram e implementem modelos privados a partir de uma biblioteca pessoal.

Este documento explica como utilizar o Portal do Azure para adicionar, gerir e partilhar um **Modelo** privado.

## <a name="guidance"></a>Orientação
As seguintes sugestões ajudá-lo-ão a tirar o máximo partido dos **Modelos** ao trabalhar com as suas soluções:

* Um **Modelo** é um recurso encapsulado que contém um modelo do Resource Manager e metadados adicionais. Este comporta-se de uma forma muito semelhante a um item no Marketplace. A principal diferença é que é um item privado em vez de um item público do Marketplace.
* A biblioteca de **Modelos** funciona bem para utilizadores que necessitam de personalizar as respetivas implementações.
* Os **Modelos** funcionam bem para utilizadores que necessitam de um repositório simples no Azure.
* Comece com um modelo do Resource Manager existente. Encontre modelos no [github](https://github.com/Azure/azure-quickstart-templates) ou escolha a opção [Exportar modelo](../azure-resource-manager/resource-manager-export-template.md) a partir de um grupo de recursos existente.
* Os **Modelos** estão ligados ao utilizador que os publica. O nome do publicador é visível para todas as pessoas que têm acesso de leitura ao mesmo.
* Os **Modelos** são recursos do Resource Manager e não é possível mudar o nome uma vez publicado.

## <a name="add-a-template-resource"></a>Adicionar um recurso de Modelo
Existem duas formas de criar um recurso de **Modelo** no Portal do Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Método 1: Criar um novo recurso de Modelo a partir de um grupo de recursos em execução
1. Navegue para um grupo de recursos existente no Portal do Azure. Selecione **Exportar modelo** em **Definições**.
2. Assim que o modelo do Resource Manager seja exportado, utilize o botão **Guardar Modelo** para guardá-lo no repositório de **Modelos**. Encontre detalhes completos sobre a Exportação do modelo [aqui](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Exportar grupo de recursos](media/rg-export-portal1.PNG)  <br />
3. Selecione o botão de comando **Guardar para modelo**.
   <br /><br />
4. Introduza as seguintes informações:
   
   * Nome – O nome do objeto de modelo (NOTA: Este é um nome baseado no Azure Resource Manager. Todas as restrições de nomenclatura aplicam-se e não pode ser alterada depois de criada).
   * Descrição – Resumo rápido sobre o modelo.
     
     ![Guardar Modelo](media/save-template-portal1.PNG)  <br />
5. Clique em **Guardar**.
   
   > [!NOTE]
   > O painel Exportar modelo mostra notificações quando o modelo do Resource Manager exportado apresenta erros, mas continuará a poder guardar este modelo do Resource Manager nos Modelos. Certifique-se de que verifica e corrige quaisquer problemas do modelo do Resource Manager antes de voltar a implementar o modelo do Resource Manager exportado.
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>Método 2: Adicionar um novo recurso de Modelo por pesquisa
Também pode adicionar um novo **Modelo** em branco utilizando o botão de comando +Adicionar em **Procurar > Modelos**. Terá de fornecer um Nome, Descrição e o modelo do Resource Manager JSON.

![Adicionar Modelo](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> O Microsoft.Gallery é que um Inquilino baseado no fornecedor de recursos Azure. O recurso de Modelo está associado ao utilizador que o criou. Este não está associado a qualquer subscrição específica. Uma subscrição tem de ser escolhida apenas quando implementar um Modelo.
> 
> 

## <a name="view-template-resources"></a>Ver os recursos do Modelo
Todos os **Modelos** disponíveis podem ser vistos em **Procurar > Modelos**. Isto inclui os **Modelos** que tenha criado, bem como aqueles que tenham sido partilhados consigo com diferentes níveis de permissões. Obter mais detalhes na secção [controlo de acesso](#access-control-for-a-tenant-resource-provider) abaixo.

![Ver Modelo](media/view-template-portal1.PNG)  <br />

Pode ver os detalhes de um **Modelo** ao clicar num item na lista.

![Ver Modelo](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Editar um recurso de Modelo
Pode iniciar o fluxo de edição de um **Modelo** clicando com o botão direito no item da lista Procurar ou escolhendo o botão de comando Editar.

![Editar Modelo](media/edit-template-portal1a.PNG)  <br />

Pode editar a descrição ou o texto do modelo do Resource Manager. Não é possível editar o nome, uma vez que é um nome de recurso do Resource Manager. Ao editar o modelo do Resource Manager JSON, iremos validar para garantir de que é JSON válido. Escolha **OK** e, em seguida, **Guardar** para guardar o modelo atualizado.

![Editar Modelo](media/edit-template-portal2a.PNG)  <br />

Uma vez que o **Modelo** é guardado, verá uma notificação de confirmação.

![Editar Modelo](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Implementar um recurso de Modelo
Pode implementar qualquer **Modelo** no qual possua permissão de **Leitura**. O fluxo de implementação inicia o painel de implementação standard do Modelo do Azure. Preencha os valores para os parâmetros do modelo do Resource Manager para continuar com a implementação.

![Implementar Modelo](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Partilhar um recurso de Modelo
Um recurso de **Modelo** pode ser partilhado com os seus parceiros. A partilha comporta-se de forma semelhante à [atribuição de funções para qualquer recurso no Azure](../active-directory/role-based-access-control-configure.md). O proprietário do **Modelo** concede permissões a outros utilizadores para que possam interagir com um recurso de Modelo. A pessoa ou grupo de pessoas com quem partilha o **Modelo** serão capazes de ver o modelo do Resource Manager e as propriedades da sua galeria.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Controlo de acesso para os recursos do Microsoft.Gallery
| Função | Permissões |
| --- | --- |
| Proprietário |Permite controlo total sobre o recurso Modelo, incluindo Partilhar |
| Leitor |Permite a Leitura e Execução (Implementação) no recurso do Modelo |
| Contribuinte |Concede permissão para Editar e Eliminar o recurso de Modelo. O utilizador não pode Partilhar o Modelo com outras pessoas |

Selecione **Partilhar** no item de procura ao clicar com o botão direito em ou no painel de vista de um item específico. Isto inicia uma experiência de Partilha.

![Partilhar Modelo](media/share-template-portal1a.png)  <br />

 Agora, pode escolher uma função e um utilizador ou grupo para fornecer acesso a um determinado **Modelo**. As funções disponíveis são Proprietário, Leitor e Contribuinte. Obtenha mais detalhes na secção [controlo de acesso](#access-control-for-a-tenant-resource-provider) acima.

![Partilhar Modelo](media/share-template-portal2b.png)  <br />

![Partilhar Modelo](media/share-template-portal3b.png)  <br />

Clique em **Selecionar** e **Ok**. Agora, pode ver os utilizadores ou grupos que adicionou ao recurso.

![Partilhar Modelo](media/share-template-portal4b.png)  <br />

> [!NOTE]
> Um Modelo pode ser partilhado apenas com utilizadores e grupos no mesmo inquilino do Azure Active Directory. Se partilhar um Modelo com um endereço de correio eletrónico que não se encontra no seu inquilino, será enviado um convite a pedir ao utilizador que se junte ao inquilino como convidado.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como criar modelo do Resource Manager, consulte [Autorizar modelos](../azure-resource-manager/resource-group-authoring-templates.md).
* Para compreender as funções que pode utilizar num modelo do Resource Manager, consulte [Funções de modelo](../azure-resource-manager/resource-group-template-functions.md)
* Para obter orientações sobre a conceção dos seus modelos, consulte [Melhores práticas para criar modelos do Azure Resource Manager](../azure-resource-manager/best-practices-resource-manager-design-templates.md)


---
title: Utilize o portal do Azure para implementar os recursos do Azure | Microsoft Docs
description: Utilize o portal do Azure e o Azure Resource Manager para implementar os recursos.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: ea91fdd58dd3b5c118fe390afe1eb355e3c26570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementar recursos com modelos do Resource Manager e do Portal do Azure

Este tópico mostra como utilizar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para implementar os recursos do Azure. Para saber mais sobre como gerir os recursos, consulte o artigo [recursos do Azure de gerir através do portal](resource-group-portal.md).

## <a name="create-resource-group"></a>Criar grupo de recursos

1. Para criar um grupo de recursos vazio, selecione **grupos de recursos**.

   ![Selecione os grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Em grupos de recursos, selecione **adicionar**.

   ![Adicionar grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Atribua um nome e localização e, se necessário, selecione uma subscrição. Tem de fornecer uma localização para o grupo de recursos porque o grupo de recursos armazena os metadados sobre os recursos. Por motivos de conformidade, pode pretender especificar armazenar esses metadados. Em geral, recomendamos que especifique uma localização onde a maioria dos seus recursos irá residir. Utilizar a mesma localização, pode simplificar o modelo.

   ![conjunto de valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Quando tiver concluído a definição das propriedades, selecionar **criar**.

1. Para ver o novo grupo de recursos, selecione **atualizar**.

   ![Atualizar os grupos de recursos](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Implementar recursos do Marketplace

Depois de criar um grupo de recursos, pode implementar recursos no mesmo do Marketplace. O Marketplace fornece soluções previamente definidas para cenários comuns.

1. Para iniciar uma implementação, selecione **novo**.

   ![Novo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Localize o tipo de recurso que pretende implementar.

   ![Selecione o tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Se não vir a solução específica que pretende implementar, pode procurar no Marketplace-lo. Por exemplo, para encontrar uma solução do Wordpress, comece a escrever **Wordpress** e selecione a opção que pretende.

   ![marketplace de pesquisa](./media/resource-group-template-deploy-portal/search-resource.png)

1. Dependendo do tipo de recursos selecionado, terá de uma coleção de propriedades relevantes para definir antes da implementação. Para todos os tipos, tem de selecionar um grupo de recursos de destino. A imagem seguinte mostra como criar uma aplicação web e implementá-la para o grupo de recursos que criou.

   ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Em alternativa, pode optar por criar um grupo de recursos quando implementar os recursos. Selecione **criar nova** e atribua um nome de grupo de recursos.

   ![Criar novo grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Começa a sua implementação. A implementação pode demorar alguns minutos. Quando a implementação estiver concluída, verá uma notificação.

   ![Vista de notificação](./media/resource-group-template-deploy-portal/view-notification.png)

1. Depois de implementar os recursos, pode adicionar mais recursos para o grupo de recursos, selecionando **adicionar**.

   ![Adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementar recursos do modelo personalizado

Se pretender executar uma implementação, mas não utilizar qualquer um dos modelos no Marketplace, pode criar um modelo personalizado que define a infraestrutura para a sua solução. Para saber mais sobre a criação de modelos, consulte [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).

1. Para implementar um modelo personalizado através do portal, selecione **novo**e procure **modelo de implementação** até que o pode selecionar as opções.

   ![implementação do modelo de pesquisa](./media/resource-group-template-deploy-portal/search-template.png)

1. Selecione **Criar**.

   ![Selecione criar](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Pode ver várias opções para criar um modelo. Selecione **construir o seu próprio modelo no editor de**.

   ![Opções de vista](./media/resource-group-template-deploy-portal/see-options.png)

1. Tem um modelo em branco que está disponível para personalizar.

   ![Criar o modelo](./media/resource-group-template-deploy-portal/blank-template.png)

1. Pode editar manualmente a sintaxe JSON ou selecione um modelo de pré-criadas o [Galeria de modelo de início rápido](https://azure.microsoft.com/resources/templates/). No entanto, para este artigo utiliza o **adicionar recurso** opção.

   ![Editar modelo](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Selecione **conta de armazenamento** e forneça um nome. Quando terminar fornecendo valores, selecione **OK**.

   ![Selecionar a conta de armazenamento](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. O editor de adiciona automaticamente o JSON para o tipo de recurso. Tenha em atenção que inclua um parâmetro para definir o tipo de conta de armazenamento. Selecione **Guardar**.

   ![Mostrar o modelo](./media/resource-group-template-deploy-portal/show-json.png)

1. Agora, tem a opção para implementar os recursos definidos no modelo. Para implementar, concorda com os termos e condições e selecione **Compra**.

   ![Implementar o modelo](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implementar recursos a partir de um modelo guardadas na sua conta

O portal permite-lhe guardar um modelo para a sua conta do Azure e volte a implementá-la mais tarde. Para obter mais informações sobre como trabalhar com estes guardar modelos, [introdução ao modelos privados no portal do Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Para localizar os modelos guardados, selecione **mais serviços**.

   ![Mais serviços](./media/resource-group-template-deploy-portal/more-services.png)

1. Procurar **modelos** e selecionar essa opção.

   ![Procurar modelos](./media/resource-group-template-deploy-portal/find-templates.png)

1. Na lista de modelos guardadas na sua conta, selecione aquele que pretende trabalhar.

   ![modelos guardados](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Selecione **implementar** para voltar a implementar este modelo guardado.

   ![implementar a modelo guardado](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Passos seguintes
* Para ver os registos de auditoria, consulte [auditar operações com o Resource Manager](resource-group-audit.md).
* Para resolver erros de implementação, consulte [ver as operações de implementação](resource-manager-deployment-operations.md).
* Para obter um modelo a partir de uma implementação ou o grupo de recursos, consulte [modelo de exportar o Azure Resource Manager a partir dos recursos existentes](resource-manager-export-template.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

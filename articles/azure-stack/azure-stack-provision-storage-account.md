---
title: Contas de armazenamento na pilha do Azure | Microsoft Docs
description: Saiba como criar uma conta de armazenamento de pilha do Azure.
services: azure-stack
documentationcenter: 
author: vhorne
manager: byronr
editor: 
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/1/2017
ms.author: victorh
ms.openlocfilehash: 41c9ee37c43d4ad41c51ea2ed023d3b47d460dd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storage-accounts-in-azure-stack"></a>Contas de armazenamento no Azure Stack
As contas de armazenamento incluem os serviços Blob e Tabela e o espaço de nomes exclusivo dos seus objetos de dados de armazenamento. Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta de armazenamento.

1. No computador POC de pilha do Azure, inicie sessão no `https://adminportal.local.azurestack.external` como [administrador](azure-stack-connect-azure-stack.md)e, em seguida, clique em **novo** > **dados + armazenamento**  >  **Conta de armazenamento**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. No **criar conta de armazenamento** painel, escreva um nome para a sua conta de armazenamento. Crie um novo **grupo de recursos**, ou selecione um existente, em seguida, clique em **criar** para criar a conta de armazenamento.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Para ver a sua nova conta de armazenamento, clique em **todos os recursos**, em seguida, procure a conta de armazenamento e clique no respetivo nome.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Passos seguintes
[Utilizar modelos do Azure Resource Manager](user/azure-stack-arm-templates.md)

[Saiba mais sobre as contas do storage do Azure](../storage/common/storage-create-storage-account.md)

[Transferir o guia de validação de armazenamento do Azure consistente de pilha do Azure](http://aka.ms/azurestacktp1doc)

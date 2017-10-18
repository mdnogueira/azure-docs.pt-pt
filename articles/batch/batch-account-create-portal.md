---
title: Criar uma conta do Batch no portal do Azure | Microsoft Docs
description: Saiba como criar uma conta do Azure Batch no portal do Azure a executar cargas de trabalho paralelas em grande escala na nuvem
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7629e496f2d73798b94acdc611014a8b3afead7
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Batch com portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [Gestão de Batch .NET](batch-management-dotnet.md)
>
>

Saiba como criar uma conta do Azure Batch no [portal do Azure][azure_portal] e escolha as propriedades da conta que se adequam ao seu cenário de computação. Saiba onde encontrar as propriedades da conta importantes, como as teclas de acesso e os URLs de conta.

Para informações sobre contas do Batch e cenários, consulte a [descrição geral da funcionalidade](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Criar uma conta do Batch



1. Inicie sessão no [Portal do Azure][azure_portal].
2. Clique em **Novo** e pesquise **Serviço Batch** no Marketplace.

    ![Batch no Marketplace][marketplace_portal]
3. Selecione **Serviço Batch**, clique em **Criar** e introduza as definições em **Nova conta do Batch** . Veja os detalhes seguintes.

    ![Criar uma conta do Batch][account_portal]

    a. **Nome da conta**: o nome que escolher deve ser único na região do Azure na qual é criada a conta (veja a **Localização** abaixo). O nome da conta pode conter apenas carateres em minúsculas ou números e tem de ter 3 a 24 carateres de comprimento.

    b. **Subscrição**: a subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.

    c. **Modo de atribuição de conjuntos**: se esta definição for apresentada, aceite o **serviço Batch** predefinido.

    c. **Grupo de recursos**: selecione um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.

    d. **Localização**: a região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos são apresentadas como opções.

    e. **Conta de armazenamento** (opcional): uma conta de Armazenamento do Azure para fins gerais que associa à conta do Batch. Esta opção é recomendada para a maioria das contas do Batch. Veja [Conta do Armazenamento do Azure Ligada](#linked-azure-storage-account) mais tarde neste artigo para obter mais informações.

4. Clique em **Criar** para criar a conta.



## <a name="view-batch-account-properties"></a>Ver propriedades da conta do Batch
Depois de a conta ter sido criada, clique na mesma para aceder às respetivas definições e propriedades. Pode aceder a todas as definições e propriedades da conta através do menu à esquerda.

![Painel Conta do Batch no portal do Azure][account_blade]

* **URL da conta do Batch**: quando desenvolver uma aplicação com as [APIs do Batch](batch-apis-tools.md#azure-accounts-for-batch-development), precisa de um URL de conta para aceder aos recursos do Batch. Os URLs de conta do Batch têm o formato seguinte:

    `https://<account_name>.<region>.batch.azure.com`

![URL da conta do Batch no portal][account_url]

* **Chaves de acesso**: para autenticar o acesso à conta do Batch a partir da aplicação, pode utilizar uma chave de acesso de conta. (O Batch também suporta a autenticação do Azure Active Directory.)

    Para ver ou voltar a gerar as chaves de acesso, selecione **Chaves**.

    ![Chaves da conta do Batch no portal do Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Conta do Armazenamento do Azure Ligada

Pode ligar uma conta de Armazenamento do Azure para fins gerais à conta do Batch, o que é útil para inúmeros cenários. A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do Batch utiliza o armazenamento de Blobs, tal como o faz a biblioteca [.NET de Convenções de Ficheiros do Batch](batch-task-output.md). Estas funcionalidades opcionais ajudam-no a implementar as aplicações nas quais as tarefas do Batch são executadas e a manter os dados que aquelas produzem.

Recomendamos que crie uma nova conta de Armazenamento para utilização exclusiva por parte da sua conta do Batch. O Azure Batch, atualmente, suporta apenas o tipo de conta de Armazenamento para fins gerais. Este tipo de conta está descrito no passo 5, [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) em [Acerca das contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

![Criar uma conta de armazenamento para fins gerais][storage_account]

> [!NOTE]
> Tenha cuidado ao regenerar as chaves de acesso das contas de Armazenamento ligadas. Regenere apenas uma chave de conta de Armazenamento e clique em **Sincronizar Chaves**, no painel Conta de Armazenamento ligada. Aguarde cinco minutos para que as chaves se propaguem pelos nós de computação dos seus conjuntos e, depois, regenere e sincronize a outra chave, se necessário. Se regenerar ambas as chaves ao mesmo tempo, os nós de computação não conseguirão sincronizar nenhuma das chaves, que perdem o acesso à conta de Armazenamento.
>
>

![Regenerar chaves de conta de armazenamento][4]

## <a name="batch-service-quotas-and-limits"></a>Quotas e limites do serviço Batch
Tal como sucede com a subscrição do Azure e outros serviços do Azure, aplicam-se algumas [quotas e limites](batch-quota-limit.md) às contas do Batch. As quotas atuais para uma conta do Batch são apresentadas em **Quotas**.

![Quotas das contas do Batch no portal do Azure][quotas]



Além disso, é possível aumentar muitas destas quotas ao submeter um pedido de suporte de produto gratuito no portal do Azure. Veja [Quotas e limites do serviço Batch](batch-quota-limit.md) para obter detalhes sobre como pedir aumentos de quotas.

## <a name="other-batch-account-management-options"></a>Outras opções de gestão de contas do Batch
Além do portal do Azure, também pode criar e gerir contas do Batch com o seguinte:

* [Cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](batch-cli-get-started.md)
* [Gestão de Batch .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Passos seguintes
* Consulte a [Batch feature overview (Descrição geral da funcionalidade do Batch)](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo aborda os recursos do Batch principais como conjuntos, nós de computação e tarefas e fornece uma descrição geral das funcionalidades do serviço para cargas de trabalho de computação em grande escala.
* Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](batch-dotnet-get-started.md) ou [Python](batch-python-tutorial.md). Estes artigos introdutórios orientam-no numa aplicação em funcionamento que utiliza o serviço do Batch para executar uma carga de trabalho em vário nós de computação e inclui a utilização do Armazenamento do Azure para o teste e obtenção do ficheiro de carga de trabalho.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regenerar chaves de conta de armazenamento"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png

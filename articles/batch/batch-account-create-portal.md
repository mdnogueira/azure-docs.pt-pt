---
title: Criar uma conta do Azure Batch | Microsoft Docs
description: Saiba como criar uma conta do Azure Batch no portal do Azure a executar cargas de trabalho paralelas em grande escala na nuvem
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 6f05aa3f06c8ec86d58cafd406c2752ddb2eecc1
ms.openlocfilehash: cf5b560028f3f80e3ba46ef96ae7a157dad7847d


---
# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Criar uma conta do Azure Batch no portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [Gestão de Batch .NET](batch-management-dotnet.md)
> 
> 

Saiba como criar uma conta do Azure Batch no [portal do Azure][azure_portal] e onde encontrar propriedades importantes da conta, como chaves de acesso e URLs da conta. Também abordamos os preços do Batch e mostramos como associar uma conta do Armazenamento do Azure à sua conta do Batch, para que possa utilizar [pacotes de aplicações](batch-application-packages.md) e [manter resultados de trabalhos e tarefas](batch-task-output.md).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch
1. Inicie sessão no [Portal do Azure][azure_portal].
2. Clique em **Novo** > **Computação** > **Serviço Batch**.
   
    ![Batch no Marketplace][marketplace_portal]
3. O painel **Nova Conta do Batch** é apresentado. Veja os itens do *a* ao *e* abaixo para obter descrições de cada elemento do painel.
   
    ![Criar uma conta do Batch][account_portal]
   
    a. **Nome da Conta**: o nome exclusivo para a conta do Batch. O nome que escolher deve ser único na região do Azure na qual será criada a nova conta (veja a **Localização** abaixo). O nome da conta pode conter apenas carateres em minúsculas ou números e tem de ter 3 a 24 carateres de comprimento.
   
    b. **Subscrição**: a subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.
   
    c. **Grupo de recursos**: selecione um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.
   
    d. **Localização**: a região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos são apresentadas como opções.
   
    e. **Conta de Armazenamento** (opcional): uma conta de Armazenamento do Azure para fins gerais que associa à nova conta do Batch. Veja [Conta do Armazenamento do Azure Ligada](#linked-azure-storage-account), abaixo, para obter mais informações.

4. Clique em **Criar** para criar a conta.
   
   O portal indica que está a **Implementar** a conta e, após a conclusão, é apresentada uma notificação que diz **Implementações concluídas com êxito**, em *Notificações*.

## <a name="view-batch-account-properties"></a>Ver propriedades da conta do Batch
Depois de a conta ter sido criada, pode abrir o **painel Conta do Batch** para aceder às respetivas definições e propriedades. Pode aceder a todas as definições e propriedades da conta através do menu à esquerda do painel Conta do Batch.

![Painel Conta do Batch no portal do Azure][account_blade]

* **URL da conta do Batch**: quando desenvolver uma aplicação com as [APIs Batch](batch-technical-overview.md#batch-development-apis), irá precisar de um URL de conta para aceder aos recursos do Batch. Os URLs de conta do Batch têm o formato seguinte:
  
    `https://<account_name>.<region>.batch.azure.com`

![URL da conta do Batch no portal][account_url]

* **Chaves de acesso**: para autenticar o acesso à conta do Batch a partir da aplicação, necessitará uma chave de acesso de conta. Para ver ou regenerar as chaves de acesso da sua conta do Batch, introduza `keys` na caixa **Pesquisar** do menu à esquerda do painel Conta do Batch selecione **Chaves**.
  
    ![Chaves da conta do Batch no portal do Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Conta do Armazenamento do Azure Ligada

Como já foi dito, opcionalmente, pode ligar uma conta de Armazenamento do Azure para fins gerais à conta do Batch. A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do Batch utiliza o armazenamento de Blobs, tal como o faz a biblioteca [.NET de Convenções de Ficheiros do Batch](batch-task-output.md). Estas funcionalidades opcionais ajudam-no a implementar as aplicações nas quais as tarefas do Batch são executadas e a manter os dados que aquelas produzem.

Recomendamos que crie uma nova conta de Armazenamento para utilização exclusiva por parte da sua conta do Batch.

![Criar uma conta de armazenamento para “Fins gerais"][storage_account]

> [!NOTE] 
> O Azure Batch, atualmente, suporta apenas o tipo de conta de Armazenamento para fins gerais. Este tipo de conta é descrito no passo 5, [Criar uma conta de armazenamento] (../storage/storage-create-storage-account.md#create-a-storage-account), em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).
>
>

> [!WARNING]
> Tenha cuidado ao regenerar as chaves de acesso das contas de Armazenamento ligadas. Regenere apenas uma chave de conta de Armazenamento e clique em **Sincronizar Chaves**, no painel Conta de Armazenamento ligada. Aguarde cinco minutos para que as chaves se propaguem pelos nós de computação dos seus conjuntos e, depois, regenere e sincronize a outra chave, se necessário. Se regenerar ambas as chaves ao mesmo tempo, os nós de computação não conseguirão sincronizar nenhuma das chaves, que perdem o acesso à conta de Armazenamento.
> 
> 

![Regenerar chaves de conta de armazenamento][4]

## <a name="batch-service-quotas-and-limits"></a>Quotas e limites do serviço Batch
Tenha em atenção que, tal como sucede com a subscrição do Azure e outros serviços do Azure, aplicam-se algumas [quotas e limites](batch-quota-limit.md) às contas do Batch. As quotas anuais das contas do Batch aparecem no portal, nas **Propriedades** da conta.

![Quotas das contas do Batch no portal do Azure][quotas]

Tenha presentes estas quotas quando conceber e dimensionar as cargas de trabalho do Batch. Por exemplo, se o conjunto não está a atingir o número de destino de nós de computação que especificou, poderá ter atingido o limite de quota de núcleos da sua conta do Batch.

A quota para as contas do Batch é efetuada por região por subscrição, pelo que pode ter mais do que uma conta do Batch por predefinição, desde que estejam em regiões diferentes. Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

Para além disso, é possível aumentar muitas destas quotas simplesmente ao submeter um pedido de suporte de produto gratuito no portal do Azure. Veja [Quotas e limites do serviço Batch](batch-quota-limit.md) para obter detalhes sobre como pedir aumentos de quotas.

## <a name="other-batch-account-management-options"></a>Outras opções de gestão de contas do Batch
Para além do portal do Azure, também pode criar e gerir contas do Batch com o seguinte:

* [Cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](../xplat-cli-install.md)
* [Gestão de Batch .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Passos seguintes
* Consulte a [Descrição geral da funcionalidade do Azure Batch](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo aborda os recursos do Batch principais como conjuntos, nós de computação e tarefas e fornece uma descrição geral das funcionalidades do serviço que permitem a execução de cargas de trabalho de computação em grande escala.
* Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](batch-dotnet-get-started.md). O [artigo introdutório](batch-dotnet-get-started.md) orienta-o numa aplicação em funcionamento que utiliza o serviço do Batch para executar uma carga de trabalho em vário nós de computação e inclui a utilização do Armazenamento do Azure para o teste e obtenção do ficheiro de carga de trabalho.

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



<!--HONumber=Dec16_HO5-->



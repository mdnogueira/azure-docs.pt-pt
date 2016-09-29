<properties
    pageTitle="Criar uma conta do Azure Batch | Microsoft Azure"
    description="Saiba como criar uma conta do Azure Batch no portal do Azure a executar cargas de trabalho paralelas em grande escala na nuvem"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>


# Criar uma conta do Azure Batch no portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](batch-account-create-portal.md)
- [Gestão de Batch .NET](batch-management-dotnet.md)

Saiba como criar uma conta do Azure Batch no [portal do Azure][azure_portal] e onde encontrar propriedades importantes da conta, como chaves de acesso e URLs da conta. Também abordamos os preços do Batch e mostramos como associar uma conta do Armazenamento do Azure à sua conta do Batch, para que possa utilizar [pacotes de aplicações](batch-application-packages.md) e [manter resultados de trabalhos e tarefas](batch-task-output.md).

## Criar uma conta do Batch

1. Inicie sessão no [portal do Azure][azure_portal].

2. Clique em **Novo** > **Computação** > **Serviço Batch**.

    ![Batch no Marketplace][marketplace_portal]

3. O painel **Nova Conta do Batch** é apresentado. Veja os itens do *a* ao *e* abaixo para obter descrições de cada elemento do painel.

    ![Criar uma conta do Batch][account_portal]

    a. **Nome da Conta**: um nome exclusivo para a sua conta do Batch. Este nome tem de ser exclusivo dentro da região do Azure onde a conta está criada (consulte *Localização* abaixo). Pode conter apenas carateres em minúsculas, números e tem de ter 3 a 24 carateres de comprimento.

    b. **Subscrição**: uma subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.

    c. **Grupo de recursos**: um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.

    d. **Localização**: uma região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos são apresentadas como opções.

    e. **Conta de Armazenamento** (opcional): uma conta de armazenamento **Para fins gerais** que associa (liga) à sua nova conta do Batch. Veja [Conta do Armazenamento do Azure Ligada](#linked-azure-storage-account), abaixo, para obter mais informações.

4. Clique em **Criar** para criar a conta.

  O portal indica que está a **Implementar** a conta e, após a conclusão, é apresentada uma notificação que diz **Implementações concluídas com êxito**, em *Notificações*.

## Ver propriedades da conta do Batch

Depois de a conta ter sido criada, pode abrir o **painel Conta do Batch** para aceder às respetivas definições e propriedades. Pode aceder a todas as definições e propriedades da conta através do menu à esquerda do painel Conta do Batch.

![Painel Conta do Batch no portal do Azure][account_blade]

* **URL da conta do Batch**: as aplicações que criar com as [APIs de programação do Batch](batch-technical-overview.md#batch-development-apis) precisam de um URL da conta para gerir recursos e executar trabalhos na conta. Os URLs de conta do Batch têm o formato seguinte:

    `https://<account_name>.<region>.batch.azure.com`

![URL da conta do Batch no portal][account_url]

* **Chaves de acesso**: as suas aplicações também precisam de uma chave de acesso quando trabalham com recursos da sua conta do Batch. Para ver ou regenerar as chaves de acesso da sua conta do Batch, introduza `keys` na caixa **Pesquisar** do menu à esquerda do painel Conta do Batch selecione **Chaves**.

    ![Chaves da conta do Batch no portal do Azure][account_keys]

## Preços

As contas do Batch só são oferecidas no “Escalão Gratuito”, o que significa que a conta do Batch propriamente dita não lhe é faturada. São-lhe faturados os recursos de computação do Azure subjacentes que as suas soluções do Batch consomem, bem como os recursos consumidos por outros serviços, quando as cargas de trabalho são executadas. Por exemplo, são-lhe faturados os nós de computação dos seus conjuntos e os dados que armazenar no Armazenamento do Azure como entradas ou resultados das suas tarefas. Do mesmo modo, se utilizar a funcionalidade de [pacotes de aplicações](batch-application-packages.md) do Batch, são-lhe faturados os recursos do Armazenamento do Azure utilizados para armazenar os pacotes de aplicações. Consulte [Preços do Batch][batch_pricing] para obter mais informações.

## Conta do Armazenamento do Azure Ligada

Como já foi dito, pode (opcionalmente) ligar uma conta de Armazenamento **Para Fins Gerais** à conta do Batch. A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do Batch utiliza o armazenamento de blobs numa conta de Armazenamento para Fins Gerais, tal como o faz a biblioteca [.NET de Convenções de Ficheiros do Batch](batch-task-output.md). Estas funcionalidades opcionais ajudam-no a implementar as aplicações nas quais as suas tarefas do Batch são executadas e a manter os dados que aquelas produzem.

Atualmente, o Batch suporta *apenas* o tipo de conta de armazenamento para **Fins gerais**, conforme descrito no passo 5, [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account), em [Acerca das contas de armazenamento do Azure](../storage/storage-create-storage-account.md). Quando ligar uma conta de Armazenamento do Azure à sua conta do Batch, confirme que liga *apenas* uma conta de armazenamento para **Fins gerais**.

![Criar uma conta de armazenamento para “Fins gerais"][storage_account]

Recomendamos que crie uma conta de Armazenamento para utilização exclusiva por parte da sua conta do Batch.

>[AZURE.WARNING] Tenha cuidado ao regenerar as chaves de acesso das contas de Armazenamento ligadas. Regenere apenas uma chave de conta de Armazenamento e clique em **Sincronizar Chaves**, no painel Conta de Armazenamento ligada. Aguarde cinco minutos para que as chaves se propaguem pelos nós de computação dos seus conjuntos e, depois, regenere e sincronize a outra chave, se necessário. Se regenerar ambas as chaves ao mesmo tempo, os nós de computação não conseguirão sincronizar nenhuma das chaves, que perdem o acesso à conta de Armazenamento.

  ![Regenerar chaves de conta de armazenamento][4]

## Quotas e limites do serviço Batch

Tenha em atenção que, tal como sucede com a subscrição do Azure e outros serviços do Azure, aplicam-se algumas [quotas e limites](batch-quota-limit.md) às contas do Batch. As quotas anuais das contas do Batch aparecem no portal, nas **Propriedades** da conta.

![Quotas das contas do Batch no portal do Azure][quotas]

Tenha presentes estas quotas quando conceber e dimensionar as cargas de trabalho do Batch. Por exemplo, se o conjunto não está a atingir o número de destino de nós de computação que especificou, poderá ter atingido o limite de quota de núcleos da sua conta do Batch.

Tenha também em atenção que a sua subscrição do Azure não está limitada a uma única conta do Batch. Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch da mesma subscrição, mas em diferentes regiões do Azure.

É possível aumentar muitas destas quotas simplesmente ao submeter um pedido de suporte de produto gratuito no portal do Azure. Veja [Quotas e limites do serviço Batch](batch-quota-limit.md) para obter detalhes sobre como pedir aumentos de quotas.

## Outras opções de gestão de contas do Batch

Para além do portal do Azure, também pode criar e gerir contas do Batch com o seguinte:

* [Cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](../xplat-cli-install.md)
* [Gestão de Batch .NET](batch-management-dotnet.md)

## Passos seguintes

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



<!--HONumber=Sep16_HO3-->



---
title: Criar uma conta do Batch no portal do Azure | Microsoft Docs
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
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 053e2ae7f382bc55a6638ccd612289ab2ba64e52
ms.contentlocale: pt-pt
ms.lasthandoff: 06/20/2017


---
<a id="create-a-batch-account-with-the-azure-portal" class="xliff"></a>

# Criar uma conta do Batch com portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [Gestão de Batch .NET](batch-management-dotnet.md)
>
>

Saiba como criar uma conta do Azure Batch no [portal do Azure][azure_portal] e escolha as propriedades da conta que se adequam ao seu cenário de computação. Saiba onde encontrar as propriedades da conta importantes, como as teclas de acesso e os URLs de conta.

Para informações sobre contas do Batch e cenários, consulte a [descrição geral da funcionalidade](batch-api-basics.md).



<a id="create-a-batch-account" class="xliff"></a>

## Criar uma conta do Batch

Utilize o portal para criar uma conta do Batch de um dos dois *modos de alocação de conjunto*: modo de **serviço do Batch** ou o mais recente modo de **subscrição de utilizador**, o que necessitar de mais de configuração. Para obter informações sobre estes dois modos, consulte a [descrição geral da funcionalidade](batch-api-basics.md#account). Para funcionalidades do modo de subscrição do utilizador, consulte também a [publicação do blogue](https://blogs.technet.microsoft.com/windowshpc/2017/03/17/azure-batch-vnet-and-custom-image-support-for-virtual-machine-pools/).

<a id="batch-service-mode" class="xliff"></a>

## Modo de serviço do Batch



1. Inicie sessão no [Portal do Azure][azure_portal].
2. Clique em **Novo** > **Computação** > **Serviço Batch**.

    ![Batch no Marketplace][marketplace_portal]
3. O painel **Nova Conta do Batch** é apresentado. Consulte as descrições abaixo de cada elemento do painel.

    ![Criar uma conta do Batch][account_portal]

    a. **Nome da conta**: o nome da conta Batch que escolher deve ser único na região do Azure na qual é criada a conta (veja a **Localização** abaixo). O nome da conta pode conter apenas carateres em minúsculas ou números e tem de ter 3 a 24 carateres de comprimento.

    b. **Subscrição**: a subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.

    c. **Modo de alocação do conjunto**: selecione **serviço do Batch**.

    c. **Grupo de recursos**: selecione um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.

    d. **Localização**: a região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos são apresentadas como opções.

    e. **Conta de armazenamento** (opcional): uma conta de Armazenamento do Azure para fins gerais que associa à conta do Batch. Esta opção é recomendada para a maioria das contas do Batch. Veja [Linked Azure Storage account (Conta do Armazenamento do Azure Ligada)](#linked-azure-storage-account) mais tarde neste artigo para obter mais informações.

4. Clique em **Criar** para criar a conta.

   O portal indica que a implementação está em curso. Após a conclusão, aparece a notificação **Implementações concluídas com êxito** em **Notificações**.

<a id="user-subscription-mode" class="xliff"></a>

## Modo de subscrição do utilizador

<a id="allow-azure-batch-to-access-the-subscription-one-time-operation" class="xliff"></a>

### Permitir que o Azure Batch aceda à subscrição (operação única)
Quando criar a sua primeira conta do Batch no modo de subscrição do utilizador, execute os seguintes passos para registar a sua subscrição com o Batch. (Se anteriormente efetuou este procedimento, avance para a secção seguinte.)

1. Inicie sessão no [Portal do Azure][azure_portal].

2. Clique em **Mais Serviços** > **Subscrições** e clique na subscrição que pretende utilizar para a conta do Batch.

3. No painel **Subscrição**, clique em **Controlo de acesso (IAM)** > **Adicionar**.

    ![Controlo de acesso da subscrição][subscription_access]

4. No painel **Adicionar permissões**, selecione a função **Contribuinte** e procure a API do Batch. Procure para cada uma destas cadeias até encontrar a API:
    1. **MicrosoftAzureBatch**.
    2. **Batch do Microsoft Azure**. Os inquilinos mais recentes podem utilizar este nome.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch. 

5. Depois de encontrar a API do Batch, selecione-a e clique em **Guardar**.

    ![Adicionar permissões do Batch][add_permission]

<a id="create-a-key-vault" class="xliff"></a>

### Criar um cofre de chaves
No modo de subscrição de utilizador, é necessário um cofre de chaves do Azure que pertence ao mesmo grupo de recursos que a conta do Batch a ser criada. Certifique-se de que o grupo de recursos está numa região onde o Batch está [disponível](https://azure.microsoft.com/regions/services/) e que a sua subscrição suporta.

1. No [portal do Azure][azure_portal], clique em **Novo** > **Segurança + Identidade** > **Key Vault**.

2. No painel **Criar Key Vault**, introduza um nome para o cofre de chaves e crie um grupo de recursos na região em que pretende a sua conta do Batch. Deixe as definições restantes nos valores predefinidos e clique em **Criar**.

<a id="create-a-batch-account" class="xliff"></a>

### Criar uma conta do Batch

1. No [portal do Azure][azure_portal], clique em **Novo** > **Computação** > **Serviço do Batch**.

    ![Batch no Marketplace][marketplace_portal]
3. O painel **Nova Conta do Batch** é apresentado. Consulte as descrições abaixo de cada elemento do painel.

    ![Criar uma conta do Batch][account_portal_byos]

    a. **Nome da conta**: o nome da conta Batch que escolher deve ser único na região do Azure na qual é criada a conta (veja a **Localização** abaixo). O nome da conta pode conter apenas carateres em minúsculas ou números e tem de ter 3 a 24 carateres de comprimento.

    b. **Subscrição**: se tiver mais do que uma subscrição, selecione a subscrição que registou com o serviço do Batch.

    c. **Modo de alocação do conjunto**: selecione **Subscrição do utilizador**.

    d. **Cofre de chaves**: selecione o cofre de chaves que criou para a sua conta do Batch na secção anterior. Opcionalmente, crie um novo cofre de chaves. Depois de selecionar o cofre, selecione a caixa de verificação para conceder acesso do Azure Batch ao cofre de chaves.

    c. **Grupo de recursos**: selecione o grupo de recursos em que criou o cofre de chaves.

    d. **Localização**: a região do Azure na qual criou o cofre de chaves para a conta do Batch.

    e. **Conta de armazenamento** (opcional): uma conta de Armazenamento do Azure para fins gerais que associa à conta do Batch. Esta opção é recomendada para a maioria das contas do Batch. Veja [Conta do Armazenamento do Azure Ligada](#linked-azure-storage-account), abaixo, para obter mais informações.

4. Clique em **Criar** para criar a conta.

   O portal indica que a implementação está em curso. Após a conclusão, aparece a notificação **Implementações concluídas com êxito** em **Notificações**.



<a id="view-batch-account-properties" class="xliff"></a>

## Ver propriedades da conta do Batch
Depois de a conta ter sido criada, pode abrir o **painel Conta do Batch** para aceder às respetivas definições e propriedades. Pode aceder a todas as definições e propriedades da conta através do menu à esquerda do painel Conta do Batch.

![Painel Conta do Batch no portal do Azure][account_blade]

* **URL da conta do Batch**: quando desenvolver uma aplicação com as [APIs Batch](batch-apis-tools.md#azure-accounts-for-batch-development), irá precisar de um URL de conta para aceder aos recursos do Batch. Os URLs de conta do Batch têm o formato seguinte:

    `https://<account_name>.<region>.batch.azure.com`

![URL da conta do Batch no portal][account_url]

* **Chaves de acesso** (modo de serviço do Batch): para autenticar o acesso à conta do Batch a partir da aplicação, necessitará uma chave de acesso de conta. (Esta definição não está disponível no modo de subscrição de utilizador, onde utiliza a autenticação do Azure Active Directory.)

    Para ver ou regenerar as chaves de acesso da sua conta do Batch, introduza `keys` na caixa **Pesquisar** do menu à esquerda do painel Conta do Batch selecione **Chaves**.

    ![Chaves da conta do Batch no portal do Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

<a id="linked-azure-storage-account" class="xliff"></a>

## Conta do Armazenamento do Azure Ligada

Opcionalmente, pode ligar uma conta de Armazenamento do Azure para fins gerais à conta do Batch. A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do Batch utiliza o armazenamento de Blobs, tal como o faz a biblioteca [.NET de Convenções de Ficheiros do Batch](batch-task-output.md). Estas funcionalidades opcionais ajudam-no a implementar as aplicações nas quais as tarefas do Batch são executadas e a manter os dados que aquelas produzem.

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

<a id="batch-service-quotas-and-limits" class="xliff"></a>

## Quotas e limites do serviço Batch
Tenha em atenção que, tal como sucede com a subscrição do Azure e outros serviços do Azure, aplicam-se algumas [quotas e limites](batch-quota-limit.md) às contas do Batch. As quotas anuais das contas do Batch aparecem no portal, nas **Propriedades** da conta.

![Quotas das contas do Batch no portal do Azure][quotas]



Para além disso, é possível aumentar muitas destas quotas simplesmente ao submeter um pedido de suporte de produto gratuito no portal do Azure. Veja [Quotas e limites do serviço Batch](batch-quota-limit.md) para obter detalhes sobre como pedir aumentos de quotas.

<a id="other-batch-account-management-options" class="xliff"></a>

## Outras opções de gestão de contas do Batch
Para além do portal do Azure, também pode criar e gerir contas do Batch com o seguinte:

* [Cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](batch-cli-get-started.md)
* [Gestão de Batch .NET](batch-management-dotnet.md)

<a id="next-steps" class="xliff"></a>

## Passos seguintes
* Consulte a [Batch feature overview (Descrição geral da funcionalidade do Batch)](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo aborda os recursos do Batch principais como conjuntos, nós de computação e tarefas e fornece uma descrição geral das funcionalidades do serviço que permitem a execução de cargas de trabalho de computação em grande escala.
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


---
title: Gerir recursos de conta do Batch com a biblioteca de cliente para .NET - Azure | Microsoft Docs
description: "Criar, eliminar e modificar recursos da conta do Azure Batch com a biblioteca de gestão de lotes .NET."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eafde9258222a2ab09ade2e366f9cc595a303dec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gerir contas do Batch e quotas com a biblioteca de cliente de gestão do Batch para .NET

> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [Gestão de Batch .NET](batch-management-dotnet.md)
> 
> 

Pode reduzir manutenção sobrecarga nas suas aplicações do Azure Batch, utilizando o [gestão de lotes .NET] [ api_mgmt_net] biblioteca para automatizar a criação de contas do Batch, eliminação, gestão de chaves e deteção de quota.

* **Criar e eliminar contas do Batch** qualquer região. Se, como um fabricante independente de software (ISV) por exemplo, fornecer um serviço para os clientes que cada um é atribuída a uma conta separada do Batch para fins de faturação, pode adicionar capacidades de criação e eliminação de conta para o portal de cliente.
* **Obter e voltar a gerar chaves de conta** através de programação para qualquer uma das suas contas do Batch. Isto pode ajudá-lo está em conformidade com as políticas de segurança que impõem rollover periódico ou expiração de chaves de conta. Quando tiver várias contas do Batch em várias regiões do Azure, o automatização deste processo de rollover aumenta a eficiência da sua solução.
* **Verifique as quotas de conta** e tomar guesswork a versão de avaliação de erro e fora de determinar que contas do Batch têm os limites. Ao verificar a sua quotas de conta antes de iniciar trabalhos, criar agrupamentos, ou adicionar nós de computação, pode ajustar proativamente onde ou quando estes computação recursos são criados. Pode determinar quais as contas que necessitam de quota aumenta antes da recursos adicionais nessas contas.
* **Combinar as funcionalidades de outros serviços do Azure** para uma experiência de gestão completo – utilizando a gestão de lotes .NET, [do Azure Active Directory][aad_about]e o [do Azure Resource Manager] [ resman_overview] em conjunto na mesma aplicação. Ao utilizar estas funcionalidades e das respetivas APIs, pode fornecer uma experiência de autenticação sem incómodos, a capacidade de criar e eliminar grupos de recursos e as capacidades que são descritas acima para uma solução de gestão de ponto a ponto.

> [!NOTE]
> Embora este artigo incida na gestão programática de contas, chaves e quotas do Batch, pode efetuar muitas destas atividades utilizando o [portal do Azure][azure_portal]. Para obter mais informações, consulte [criar uma conta do Azure Batch no portal do Azure](batch-account-create-portal.md) e [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Criar e eliminar contas do Batch
Conforme mencionado, uma das principais funcionalidades de API de gestão do Batch está a criar e eliminar contas do Batch numa região do Azure. Para tal, utilize [BatchManagementClient.Account.CreateAsync] [ net_create] e [DeleteAsync][net_delete], ou os seus homólogos síncronos.

O fragmento de código seguinte cria uma conta, obtém a conta recentemente criada do serviço Batch e elimina-a em seguida. Este fragmento e a outros utilizadores neste artigo, `batchManagementClient` é uma instância de completamente inicializada [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> As aplicações que utilizam a biblioteca de gestão de lotes .NET e classe BatchManagementClient requerem **administrador de serviço** ou **coadministrador** acesso à subscrição que é proprietário da conta do Batch para serem geridos. Para obter mais informações, consulte o [do Azure Active Directory](#azure-active-directory) secção e [AccountManagement] [ acct_mgmt_sample] exemplo de código.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Obter e voltar a gerar chaves de conta
Obter chaves de conta primária e secundária de qualquer conta de Batch na sua subscrição utilizando [ListKeysAsync][net_list_keys]. Pode voltar a gerar as chaves utilizando [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Pode criar um fluxo de trabalho de ligação simplificada para as suas aplicações de gestão. Em primeiro lugar, obter uma chave de conta para a conta de Batch que pretende gerir com [ListKeysAsync][net_list_keys]. Em seguida, utilize esta chave ao inicializar a biblioteca .NET do Batch [BatchSharedKeyCredentials] [ net_sharedkeycred] classe, que é utilizada quando a inicializar [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verifique a subscrição do Azure e quotas de conta do Batch
As subscrições do Azure e os serviços do Azure individuais, como o lote de todas as tem quotas predefinidas que limitam o número de determinadas entidades dentro delas. Para as quotas predefinidas para as subscrições do Azure, consulte [subscrição do Azure e limites de serviço, quotas e restrições](../azure-subscription-service-limits.md). Para as quotas predefinidas do serviço Batch, consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md). Ao utilizar a gestão biblioteca .NET do Batch, pode verificar destas quotas nas suas aplicações. Isto permite-lhe efetuar decisões de alocação antes de adicionar contas ou computação recursos como conjuntos e nós de computação.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificar uma subscrição do Azure para quotas de conta do Batch
Antes de criar uma conta do Batch numa região, pode verificar a sua subscrição do Azure para ver se conseguir adicionar uma conta nessa região.

No fragmento de código abaixo, utilizamos primeiro [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] para obter uma coleção de todas as contas do Batch que estejam dentro de uma subscrição. Assim que tiver adquirido desta coleção, podemos determinar quantos contas na região de destino. Em seguida, vamos utilizar [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] para obter a quota de conta do Batch e determinar quantos contas (se existirem) podem ser criadas nessa região.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

No fragmento acima, `creds` é uma instância de [TokenCloudCredentials][azure_tokencreds]. Para ver um exemplo de criação deste objeto, consulte o [AccountManagement] [ acct_mgmt_sample] exemplo de código no GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verificação de uma conta de Batch para quotas de recursos de computação
Antes de aumentar os recursos de computação na sua solução do Batch, pode verificar garantir que os recursos que pretende alocar não excede quotas da conta. No fragmento de código abaixo, estamos a informações sobre a quota para a conta do Batch com o nome de impressão `mybatchaccount`. Na sua própria aplicação, pode utilizar essas informações para determinar se a conta pode processar os recursos adicionais que sejam criados.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Enquanto existirem quotas predefinidas de serviços e subscrições do Azure, podem ser geradas ao emitir um pedido no muitas destes limites de [portal do Azure][azure_portal]. Por exemplo, consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter instruções sobre como aumentar as quotas de conta do Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Utilizar o Azure AD com o Batch Management .NET

Gestão biblioteca .NET do Batch é um cliente do fornecedor de recursos do Azure e é utilizada em conjunto com [do Azure Resource Manager] [ resman_overview] para gerir os recursos da conta através de programação. Azure AD é necessário para autenticar pedidos efetuados através de qualquer cliente de fornecedor de recursos do Azure, incluindo gestão biblioteca .NET do Batch e através de [do Azure Resource Manager][resman_overview]. Para obter informações sobre como utilizar o Azure AD com a biblioteca de gestão de lotes .NET, consulte [utilização do Azure Active Directory para autenticar as soluções do Batch](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Projeto de exemplo no GitHub

Para ver a gestão de lotes .NET em ação, consulte o [AccountManagment] [ acct_mgmt_sample] projeto de exemplo no GitHub. A aplicação de exemplo AccountManagment demonstra as seguintes operações:

1. Adquirir um token de segurança do Azure AD utilizando [ADAL][aad_adal]. Se o utilizador não está já iniciou sessão, estes recebem um pedido de credenciais do Azure.
2. Com o token de segurança obtido a partir do Azure AD, criar um [SubscriptionClient] [ resman_subclient] a consulta do Azure para obter uma lista de subscrições associadas à conta. O utilizador pode selecionar uma subscrição na lista se tiver mais do que uma subscrição.
3. Obter as credenciais associadas à subscrição selecionada.
4. Criar um [o ResourceManagementClient] [ resman_client] objeto utilizando as credenciais.
5. Utilize um [o ResourceManagementClient] [ resman_client] objeto para criar um grupo de recursos.
6. Utilize um [BatchManagementClient] [ net_mgmt_client] objeto para efetuar várias operações de conta do Batch:
   * Crie uma conta do Batch no novo grupo de recursos.
   * Obter a conta recentemente criada do serviço Batch.
   * As chaves de conta para a nova conta de impressão.
   * Voltar a gerar uma nova chave primária para a conta.
   * A informações sobre a quota para a conta de impressão.
   * Imprima a informações sobre a quota para a subscrição.
   * Todas as contas de dentro da subscrição de impressão.
   * Elimine conta recentemente criada.
7. Elimine o grupo de recursos.

Antes de eliminar o grupo de recursos e conta de Batch recentemente criado, pode visualizá-las no [portal do Azure][azure_portal]:

Para executar a aplicação de exemplo com êxito, primeiro tem de registá-lo no seu inquilino do Azure AD no portal do Azure e conceder permissões para a API do Azure Resource Manager. Siga os passos fornecidos na [soluções de gestão de Batch de autenticar com o Active Directory](batch-aad-auth-management.md).


[aad_about]: ../active-directory/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação para o Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrar aplicações com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

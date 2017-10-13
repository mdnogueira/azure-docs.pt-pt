---
title: "Gerir áreas de trabalho no Azure Log Analytics | Microsoft Docs"
description: "Pode gerir áreas de trabalho no Azure Log Analytics com várias tarefas administrativas em utilizadores, contas, áreas de trabalho e contas do Azure."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2017
ms.author: magoedte
ms.openlocfilehash: d9f86ac19044fd13e77d35d6c3dd9964c3852001
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-workspaces"></a>Gerir áreas de trabalho

Para gerir o acesso ao Log Analytics, tem de realizar diversas tarefas administrativas relacionadas com áreas de trabalho. Este artigo fornece conselhos sobre as melhores práticas e procedimentos para gerir áreas de trabalho. Uma área de trabalho é essencialmente um contentor que inclui informações da conta e informações de configuração simples para a conta. O utilizador ou outros membros da sua organização podem utilizar várias áreas de trabalho para gerir diferentes conjuntos de dados recolhidos da totalidade ou de partes da sua infraestrutura de TI.

Para criar uma área de trabalho, terá de:

1. Ter uma subscrição do Azure.
2. Escolher um nome de área de trabalho.
3. Associar a área de trabalho à sua subscrição.
4. Escolher uma localização geográfica.

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar o número de áreas de trabalho necessárias
Uma área de trabalho é um recurso do Azure e é um contentor onde os dados são recolhidos, agregados, analisados e apresentados no portal do Azure.

Pode ter múltiplas áreas de trabalho por subscrição do Azure e ter acesso a mais do que uma área de trabalho. Minimizar o número de áreas de trabalho permite-lhe consultar e correlacionar na maior parte dos dados, porque não é possível consultar em múltiplas áreas de trabalho. Esta secção descreve quando pode ser útil criar mais do que uma área de trabalho.

Atualmente, uma área de trabalho fornece:

* Uma localização geográfica para armazenamento de dados
* Granularidade para faturação
* Isolamento de dados
* Âmbito de configuração

Com base nas características anteriores, pode pretender criar várias áreas de trabalho se:

* For uma empresa global e precisar de dados armazenados em regiões específicas por motivos de soberania ou conformidade dos dados.
* Estiver a utilizar o Azure e pretender evitar custos de transferência de dados de saída ao ter uma área de trabalho na mesma região dos recursos do Azure que gere.
* Pretender atribuir custos a diferentes departamentos ou grupos empresariais com base na respetiva utilização. Quando cria uma área de trabalho para cada departamento ou grupo empresarial, a fatura e a declaração de utilização do Azure mostra os custos para cada área de trabalho em separado.
* For um fornecedor de serviços geridos e precisar de manter os dados do Log Analytics de cada cliente que gere isolados dos dados de outros clientes.
* Gerir vários clientes e pretender que cada cliente, departamento ou grupo empresarial veja os seus próprios dados, mas não os dados de outros.

Quando utilizar agentes para recolher dados, pode [configurar cada agente para reportar a uma ou mais áreas de trabalho](log-analytics-windows-agents.md).

Se estiver a utilizar o System Center Operations Manager, cada grupo de gestão do Operations Manager só pode ser ligado a uma área de trabalho. No entanto, o Microsoft Monitoring Agent no computador pode ser configurado para reportar para o Operations Manager e para uma área de trabalho do Log Analytics diferente.  

### <a name="workspace-information"></a>Informações da área de trabalho

Pode ver detalhes sobre a sua área de trabalho no portal do Azure. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Ver informações da área de trabalho no portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Mais serviços** que se encontra no canto inferior esquerdo do portal do Azure.  Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Clique em **Log Analytics**.  
    ![Hub do Azure](./media/log-analytics-manage-access/hub.png)  
3. No painel de subscrições do Log Analytics, selecione uma área de trabalho.
4. O painel da área de trabalho apresenta detalhes sobre a área de trabalho e ligações para informações adicionais.  
    ![detalhes da área de trabalho](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Gerir contas e utilizadores
Cada área de trabalho pode ter múltiplas contas associadas e cada conta (conta Microsoft ou conta da Organização) pode ter acesso a múltiplas áreas de trabalho.

Por predefinição, a conta Microsoft ou da Organização que cria a área de trabalho torna-se o Administrador da área de trabalho.

Existem dois modelos de permissões que controlam o acesso a uma área de trabalho do Log Analytics:

1. Funções de utilizador do Log Analytics legadas
2. [Acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md)

A tabela seguinte resume o acesso que pode ser definido com cada modelo de permissões:

|                          | Portal do Log Analytics | Portal do Azure | API (incluindo o PowerShell) |
|--------------------------|----------------------|--------------|----------------------------|
| Funções de utilizador do Log Analytics | Sim                  | Não           | Não                         |
| Acesso baseado em funções do Azure  | Sim                  | Sim          | Sim                        |

> [!NOTE]
> O Log Analytics está a mudar para utilizar o acesso baseado em funções do Azure como modelo de permissões e substitui as funções de utilizador do Log Analytics.
>
>

As funções de utilizador do Log Analytics legadas apenas controlam o acesso às atividades executadas no [portal do Log Analytics](https://mms.microsoft.com).

As atividades seguintes também necessitam de permissões do Azure:

| Ação                                                          | Permissões do Azure Necessárias | Notas |
|-----------------------------------------------------------------|--------------------------|-------|
| Adicionar e remover soluções de gestão                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
| Alterar o escalão de preço                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Ver dados nos mosaicos de solução *Backup* e *Site Recovery* | Administrador/Coadministrador | Acede aos recursos implementados com o modelo de implementação clássica |
| Criar um espaço de trabalho no portal do Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Gerir o acesso ao Log Analytics com permissões do Azure
Para conceder acesso à área de trabalho do Log Analytics com permissões do Azure, siga os passos em [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../active-directory/role-based-access-control-configure.md).

O Azure tem duas funções de utilizador incorporadas para o Log Analytics:
- Leitor do Log Analytics
- Contribuidor do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:
- Ver e procurar todos os dados de monitorização 
- Ver e monitorizar as definições, incluindo ver a configuração dos diagnósticos do Azure em todos os recursos do Azure.

| Tipo    | Permissão | Descrição |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade para ver todos os recursos e configuração dos mesmos. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> todas as propriedades e definições de todos os recursos |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Capacidade para executar consultas Log Search v2 |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Capacidade para executar consultas Log Search v1 |
| Ação | `Microsoft.Support/*` | Capacidade para abrir pedidos de suporte |
|Ação Não | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave da área de trabalho necessária para utilizar a API de coleção de dados e instalar o agente |


Os membros da função *Contribuidor do Log Analytics* podem:
- Ler todos os dados de monitorização 
- Criar e configurar contas de Automatização
- Adicionar e remover soluções de gestão
- Ler as chaves das contas de armazenamento 
- Configurar a recolha de registos a partir do Armazenamento do Azure
- Editar as definições de monitorização para recursos do Azure, incluindo
  - Adicionar a extensão de VM a VMs
  - Configurar os diagnósticos do Azure em todos os recursos do Azure

> [!NOTE] 
> Pode utilizar a capacidade de adicionar uma extensão de máquina virtual a máquinas virtuais para obter o controlo total das mesmas.

| Permissão | Descrição |
| ---------- | ----------- |
| `*/read`     | Capacidade para ver todos os recursos e configuração dos mesmos. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> todas as propriedades e definições de todos os recursos |
| `Microsoft.Automation/automationAccounts/*` | Capacidade para criar, e configurar contas de Automatização do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover extensões de máquinas virtuais, incluindo a extensão Microsoft Monitoring Agent e o Agente do OMS para a extensão do Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Ver a chave da conta de armazenamento. Necessária para configurar o Log Analytics para ler registos a partir das contas de Armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover regras de alertas |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover as definições de diagnósticos em recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicionar, atualizar e remover a configuração de áreas de trabalho do Log Analytics |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gestão |
| `Microsoft.Resources/deployments/*` | Criar e eliminar as implementações. Necessário para adicionar e remover soluções, áreas de trabalho e contas de automatização |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Criar e eliminar as implementações. Necessário para adicionar e remover soluções, áreas de trabalho e contas de automatização |

Para adicionar e remover utilizadores de uma função de utilizador, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Utilize estas funções para conceder aos utilizadores acesso em âmbitos diferentes:
- Subscrição - acesso a todas as áreas de trabalho da subscrição
- Grupo de Recursos - acesso a todas as áreas de trabalho no grupo de recursos
- Recurso - acesso apenas à área de trabalho especificada

Utilize [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="azure-user-roles-and-log-analytics-portal-user-roles"></a>Funções de utilizador do Azure e funções de utilizador do portal do Log Analytics
Se tiver, pelo menos, permissão de leitura do Azure na área de trabalho do Log Analytics, pode abrir o portal do OMS ao clicar na tarefa **Portal do OMS** quando visualizar a área de trabalho do Log Analytics.

Quando abrir o portal do OMS, mude para utilizar as funções de utilizador do Log Analytics legadas. Se não tiver uma atribuição de funções no portal do Log Analytics, o serviço [verifica as permissões do Azure que tem na área de trabalho](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
A atribuição de funções no portal do OMS é determinada da seguinte forma:

| Condições                                                   | Funções de utilizador do Log Analytics atribuídas | Notas |
|--------------------------------------------------------------|----------------------------------|-------|
| A sua conta pertence a uma função de utilizador do Log Analytics legada     | A função de utilizador do Log Analytics especificada | |
| A sua conta não pertence a uma função de utilizador do Log Analytics legada <br> Permissões do Azure completas para a área de trabalho (`*` permissão <sup>1</sup>) | Administrador ||
| A sua conta não pertence a uma função de utilizador do Log Analytics legada <br> Permissões do Azure completas para a área de trabalho (`*` permissão <sup>1</sup>) <br> *e não ações* de `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write` | Contribuinte ||
| A sua conta não pertence a uma função de utilizador do Log Analytics legada <br> Permissão de leitura do Azure | Só de Leitura ||
| A sua conta não pertence a uma função de utilizador do Log Analytics legada <br> As permissões do Azure não são compreendidas | Só de Leitura ||
| Para subscrições geridas para o Fornecedor de Soluções em Nuvem (CSP) <br> A conta em que tiver sessão iniciada está no Azure Active Directory associado à área de trabalho | Administrador | Normalmente, o cliente de um CSP |
| Para subscrições geridas para o Fornecedor de Soluções em Nuvem (CSP) <br> A conta em que tiver sessão iniciada não está no Azure Active Directory associado à área de trabalho | Contribuinte | Normalmente, o CSP |

<sup>1</sup> Consulte as [permissões do Azure](../active-directory/role-based-access-control-custom-roles.md) para obter mais informações sobre as definições de funções. Quando avaliar funções, uma ação de `*` não é equivalente a `Microsoft.OperationalInsights/workspaces/*`.

Alguns pontos a ter em conta sobre o portal do Azure:

* Quando inicia sessão no portal do OMS através de http://mms.microsoft.com, vê a lista **Selecionar uma área de trabalho**. Esta lista contém apenas as áreas de trabalho em que tem uma função de utilizador do Log Analytics. Para ver as áreas de trabalho às quais tem acesso com subscrições do Azure, tem de especificar um inquilino como parte do URL. Por exemplo: `mms.microsoft.com/?tenant=contoso.com`. O identificador do inquilino é, muitas vezes, a última parte do endereço de e-mail que utiliza para iniciar sessão.
* Se pretender navegar diretamente para um portal ao qual tem acesso utilizando as permissões do Azure, tem de especificar o recurso como parte do URL. É possível obter este URL através do PowerShell.

  Por exemplo, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  O URL é semelhante a `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Gestão de utilizadores no portal do OMS
Gere utilizadores e grupos no separador **Gerir Utilizadores** no separador **Contas** da página Definições.   

![gerir utilizadores](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Adicionar um utilizador a uma área de trabalho existente
Siga os passos seguintes para adicionar um utilizador ou grupo a uma área de trabalho.

1. No portal do OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Gerir Utilizadores**.
3. Na secção **Gerir Utilizadores**, escolha o tipo de conta a adicionar: **Conta da Organização**, **Conta Microsoft**, **Suporte da Microsoft**.

   * Se escolher Conta Microsoft, escreva o endereço de e-mail do utilizador associado à Conta Microsoft.
   * Se escolher Conta da Organização, pode introduzir parte do nome ou o alias de e-mail do utilizador/grupo e será apresentada uma lista de utilizadores e grupos correspondentes numa caixa pendente. Selecione um utilizador ou grupo.
   * Utilize o Suporte da Microsoft para conceder a um engenheiro do Suporte da Microsoft ou a outro empregado da Microsoft acesso temporário à sua área de trabalho para ajudar na resolução de problemas.

     > [!NOTE]
     > Para obter o melhor desempenho, limite o número de grupos do Active Directory associados a uma única conta do OMS a três: um para os administradores, um para os contribuintes e um para os utilizadores só de leitura. A utilização de mais grupos pode afetar o desempenho do Log Analytics.
     >
     >
4. Escolha o tipo de utilizador ou grupo a adicionar: **Administrador**, **Contribuinte** ou **Utilizador com Acesso Só de Leitura**.  
5. Clique em **Adicionar**.

   Se estiver a adicionar uma conta Microsoft, é enviado para o e-mail fornecido um convite para aderir à área de trabalho. Depois de o utilizador seguir as instruções no convite para aderir ao OMS, pode aceder à área de trabalho.
   Se estiver a adicionar uma conta empresarial, o utilizador pode aceder imediatamente ao Log Analytics.  

#### <a name="edit-an-existing-user-type"></a>Editar um tipo de utilizador existente
Pode alterar a função de conta para um utilizador associado à conta do OMS. Existem as seguintes opções de função:

* *Administrador*: pode gerir utilizadores, ver e agir sobre todos os alertas e adicionar e remover servidores
* *Contribuinte*: pode ver e agir sobre todos os alertas e adicionar e remover servidores
* *Utilizador com Acesso Só de Leitura*: os utilizadores marcados como só de leitura não podem:

  1. Adicionar/remover soluções. A galeria de soluções está oculta.
  2. Adicionar/modificar/remover mosaicos em **O Meu Dashboard**.
  3. Ver as páginas **Definições**. As páginas estão ocultas.
  4. Na vista Pesquisa, as tarefas Configuração do Power BI, Pesquisas Guardadas e Alertas estão ocultas.

#### <a name="to-edit-an-account"></a>Para editar uma conta
1. No portal do OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Gerir Utilizadores**.
3. Selecione a função do utilizador que pretende alterar.
4. Na caixa de diálogo de confirmação, clique em **Sim**.

### <a name="remove-a-user-from-a-workspace"></a>Remover um utilizador de uma área de trabalho
Utilize os passos seguintes para remover um utilizador de uma área de trabalho. Remover o utilizador não fecha a área de trabalho. Em vez disso, remove a associação entre esse utilizador e a área de trabalho. Se um utilizador estiver associado a várias áreas de trabalho, ainda pode iniciar sessão no OMS e ver as outras áreas de trabalho dele.

1. No portal do OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Gerir Utilizadores**.
3. Clique em **Remover** junto ao nome do utilizador que pretende remover.
4. Na caixa de diálogo de confirmação, clique em **Sim**.

### <a name="add-a-group-to-an-existing-workspace"></a>Adicionar um grupo a uma área de trabalho existente
1. Na secção anterior, "Adicionar um utilizador a uma área de trabalho existente", siga os passos 1 a 4.
2. Em **Escolher Utilizador/Grupo**, selecione **Grupo**.  
   ![adicionar um grupo a uma área de trabalho existente](./media/log-analytics-manage-access/add-group.png)
3. Introduza o Nome a Apresentar ou o endereço de E-mail para o grupo que pretende adicionar.
4. Selecione o grupo nos resultados da lista e, em seguida, clique em **Adicionar**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Ligar uma área de trabalho existente a uma subscrição do Azure
Todas as áreas de trabalho criadas depois de 26 de setembro de 2016 têm de ser associadas a uma subscrição do Azure no momento da criação. As áreas de trabalho criadas antes desta data têm de ser associadas a uma área de trabalho quando iniciar sessão. Ao criar a área de trabalho a partir do portal do Azure ou ao ligar a sua área de trabalho a uma subscrição do Azure, o Azure Active Directory é ligado como a sua conta da organização.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Para ligar uma área de trabalho a uma subscrição do Azure no portal do OMS

- Quando inicia de sessão no portal do OMS, é-lhe pedido que selecione uma subscrição do Azure. Selecione a subscrição que pretende ligar à sua área de trabalho e, em seguida, clique em **Ligar**.  
    ![ligar a subscrição do Azure](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > Para ligar uma área de trabalho, a sua conta do Azure tem de ter acesso à área de trabalho que pretende ligar.  Por outras palavras, a conta que utiliza para aceder ao portal do Azure tem de ser **a mesma** conta utilizada para aceder à área de trabalho. Se não for, veja [Adicionar um utilizador a uma área de trabalho existente](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Para ligar uma área de trabalho a uma subscrição do Azure no portal do Azure
1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
2. Procure o **Log Analytics** e selecione-o.
3. Verá a lista de áreas de trabalho existentes. Clique em **Adicionar**.  
   ![lista de áreas de trabalho](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. Em **Área de Trabalho do OMS**, clique em **Ou ligação existente**.  
   ![ligação existente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Clique em **Configurar definições necessárias**.  
   ![configurar definições necessárias](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Verá a lista de áreas de trabalho que ainda não estão ligadas à sua conta do Azure. Selecione uma área de trabalho.  
   ![selecionar áreas de trabalho](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Se for necessário, pode alterar os valores dos seguintes itens:
   * Subscrição
   * Grupo de recursos
   * Localização
   * Escalão de preço  
     ![alterar valores](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Clique em **OK**. A área de trabalho está agora ligada à sua conta do Azure.

> [!NOTE]
> Se não visualizar a área de trabalho que pretende ligar, a sua subscrição do Azure não tem acesso à área de trabalho que criou com o portal do OMS.  Para conceder acesso a esta conta a partir do portal do OMS, consulte [Add a user to an existing workspace (Adicionar um utilizador a uma área de trabalho existente)](#add-a-user-to-an-existing-workspace).
>
>

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Alterar uma Organização do Azure Active Directory de uma área de trabalho

Pode alterar a organização do Azure Active Directory de uma área de trabalho. Alterar a Organização do Azure Active Directory permite-lhe adicionar utilizadores e grupos desse diretório à área de trabalho.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Para alterar a Organização do Azure Active Directory de uma área de trabalho

1. Na página Definições no portal do OMS, clique em **Contas** e, em seguida, clique no separador **Gerir Utilizadores**.  
2. Reveja as informações sobre contas de organização e, em seguida, clique em **Alterar Organização**.  
    ![alterar organização](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Introduza as informações de identidade do administrador do seu domínio do Azure Active Directory. Depois, verá uma confirmação a indicar que a área de trabalho está ligada ao seu domínio do Azure Active Directory.  
    ![confirmação da área de trabalho ligada](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

## <a name="next-steps"></a>Passos seguintes
* Veja [Compreender a utilização de dados](log-analytics-usage.md) para saber como analisar o volume de dados recolhidos por soluções e enviados a partir de computadores.
* [Adicionar soluções de gestão do Log Analytics a partir do Azure marketplace](log-analytics-add-solutions.md) para adicionar funcionalidade e recolher dados.
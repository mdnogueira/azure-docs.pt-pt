---
title: "Gerir áreas de trabalho | Microsoft Docs"
description: "Faça a gestão de áreas de trabalho no Log Analytics com uma variedade de tarefas administrativas em utilizadores, contas, áreas de trabalho e contas do Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/06/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6862723b774951fe4cca0303ee2a39a0d5f2089d
ms.openlocfilehash: eec688e33ff55334ebe0c1bc6d08e4753aadb85c


---
# <a name="manage-workspaces"></a>Gerir áreas de trabalho

Para gerir o acesso ao Log Analytics, tem de realizar diversas tarefas administrativas relacionadas com áreas de trabalho. Este artigo fornece conselhos sobre as melhores práticas e procedimentos que irá utilizar para gerir áreas de trabalho com vários tipos de conta. Uma área de trabalho é essencialmente um contentor que inclui informações da conta e informações de configuração simples para a conta. O utilizador ou outros membros da sua organização podem utilizar várias áreas de trabalho para gerir diferentes conjuntos de dados recolhidos da totalidade ou de partes da sua infraestrutura de TI.

Para criar uma área de trabalho, terá de:

1. Ter uma subscrição do Azure.
2. Escolher um nome de área de trabalho.
3. Associar a área de trabalho à sua subscrição.
4. Escolher uma localização geográfica.

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar o número de áreas de trabalho necessárias
Uma área de trabalho é um recurso do Azure e é um contentor onde os dados são recolhidos, agregados, analisados e apresentados no portal do Azure.

É possível criar várias áreas de trabalho para os utilizadores terem acesso a uma ou mais áreas de trabalho. Minimizar o número de áreas de trabalho permite-lhe consultar e correlacionar entre a maior parte dos dados. Esta secção descreve quando pode ser útil criar mais do que uma área de trabalho.

Atualmente, uma área de trabalho fornece:

* Uma localização geográfica para armazenamento de dados
* Granularidade para faturação
* Isolamento de dados

Com base nas características acima, pode pretender criar várias áreas de trabalho se:

* For uma empresa global e precisar de dados armazenados em regiões específicas por motivos de soberania ou conformidade dos dados.
* Estiver a utilizar o Azure e pretender evitar custos de transferência de dados de saída ao ter uma área de trabalho na mesma região dos recursos do Azure que gere.
* Pretender atribuir custos a diferentes departamentos ou grupos empresariais com base na respetiva utilização. Quando cria uma área de trabalho para cada departamento ou grupo empresarial, a fatura e a declaração de utilização do Azure mostra os custos para cada área de trabalho em separado.
* For um fornecedor de serviços gerido e precisar de manter os dados de análise de registo de cada cliente isolados relativamente aos dos outros clientes.
* Gerir vários clientes e pretender que cada cliente, departamento ou grupo empresarial veja os seus próprios dados, mas não os dados de outros.

Quando utilizar agentes para recolher dados, pode configurar cada agente para reportar a uma ou mais áreas de trabalho.

Se estiver a utilizar o System Center Operations Manager, cada grupo de gestão do Operations Manager só pode ser ligado a uma área de trabalho. Pode instalar o Microsoft Monitoring Agent em computadores geridos pelo Operations Manager e fazer com que o agente reporte ao Operations Manager e a uma área de trabalho do Log Analytics diferente.

### <a name="workspace-information"></a>Informações da área de trabalho

Pode ver detalhes sobre a sua área de trabalho no portal do Azure. Também pode ver detalhes no portal do OMS.

#### <a name="view-workspace-information-the-azure-portal"></a>Ver informações da área de trabalho no portal do Azure

1. Se ainda não o fez, inicie sessão no [portal do Azure](https://portal.azure.com) através da sua subscrição do Azure.
2. No menu **Hub**, clique em **Mais serviços** e, na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Clique em **Log Analytics**.  
    ![Hub do Azure](./media/log-analytics-manage-access/hub.png)  
3. No painel de subscrições do Log Analytics, selecione uma área de trabalho.
4. O painel da área de trabalho apresenta detalhes sobre a área de trabalho e ligações para informações adicionais.  
    ![detalhes da área de trabalho](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Gerir contas e utilizadores
Cada área de trabalho pode ter várias contas de utilizador associadas e cada conta de utilizador (conta Microsoft ou conta da Organização) pode ter acesso a várias áreas de trabalho.

Por predefinição, a conta Microsoft ou da Organização utilizada para criar a área de trabalho torna-se o Administrador da área de trabalho. Em seguida, o administrador pode convidar contas Microsoft adicionais ou selecionar os utilizadores a partir do Azure Active Directory.

O acesso à área de trabalho que é concedido às pessoas é controlado em dois locais:

* No Azure, pode utilizar o controlo de acesso baseado em funções para fornecer acesso à subscrição do Azure e aos recursos do Azure associados. Estas permissões também são utilizadas para acesso ao PowerShell e à API REST.
* No portal do OMS, o acesso apenas ao portal do OMS e não à subscrição do Azure associada.

Os utilizadores não veem os dados nos mosaicos das soluções de Backup e Site Recovery se só lhes der acesso ao portal do OMS e não à subscrição do Azure à qual está ligado.
Para permitir que todos os utilizadores vejam os dados nestas soluções, confirme que têm, pelo menos, acesso de **leitor** ao Backup Vault e ao cofre do Site Recovery que está ligado à área de trabalho.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Gerir o acesso ao Log Analytics utilizando o portal do Azure
Se conceder às pessoas acesso à área de trabalho do Log Analytics utilizando as permissões do Azure, no portal do Azure, por exemplo, os mesmos utilizadores podem aceder ao portal do Log Analytics. Se os utilizadores estiverem no portal do Azure, podem navegar para o portal do OMS clicando na tarefa **Portal do OMS** quando visualizarem o recurso da área de trabalho do Log Analytics.

Alguns pontos a ter em conta sobre o portal do Azure:

* Isto não é o *Controlo de Acesso Baseado em Funções*. Se tiver permissões de acesso de *Leitor* no portal do Azure para a área de trabalho do Log Analytics, pode efetuar alterações através do portal do OMS. O portal do OMS tem um conceito de Administrador, Contribuinte e Utilizador com Acesso Só de Leitura. Se a conta na qual tem sessão iniciada estiver no Azure Active Directory associado à área de trabalho, significa que é Administrador no portal do OMS; caso contrário, é Contribuinte.
* Quando inicia sessão no portal do OMS através de http://mms.microsoft.com, por predefinição, vê a lista **Selecionar uma área de trabalho**. Contém apenas as áreas de trabalho adicionadas através do portal do OMS. Para ver as áreas de trabalho às quais tem acesso com subscrições do Azure, tem de especificar um inquilino como parte do URL. Por exemplo:

  `mms.microsoft.com/?tenant=contoso.com` O identificador do inquilino é, muitas vezes, a última parte do endereço de e-mail que utiliza para iniciar sessão.
* Se a conta que utilizar para iniciar sessão for uma conta no Azure Active Directory inquilino, significa que é *Administrador* no portal do OMS. Normalmente, é este o caso, a menos que esteja a iniciar sessão como CSP.  Se a sua conta não estiver no Azure Active Directory inquilino, significa que é *Utilizador* no portal do OMS.
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
   * Se escolher Conta Profissional, pode introduzir parte do nome ou do alias de e-mail do utilizador ou grupo e será apresentada uma lista de utilizadores e grupos correspondentes numa caixa pendente. Selecione um utilizador ou grupo.
   * Utilize o Suporte da Microsoft para conceder a um engenheiro do Suporte da Microsoft ou a outro empregado da Microsoft acesso temporário à sua área de trabalho para ajudar na resolução de problemas.

     > [!NOTE]
     > Para obter melhores resultados de desempenho, limite o número de grupos do Active Directory associados a uma única conta do OMS a três: um para os administradores, um para os contribuintes e um para os utilizadores só de leitura. A utilização de mais grupos pode afetar o desempenho do Log Analytics.
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
1. Siga os passos 1 a 4 em "Adicionar um utilizador a uma área de trabalho existente", acima.
2. Em **Escolher Utilizador/Grupo**, selecione **Grupo**.  
   ![adicionar um grupo a uma área de trabalho existente](./media/log-analytics-manage-access/add-group.png)
3. Introduza o Nome a Apresentar ou o endereço de E-mail para o grupo que pretende adicionar.
4. Selecione o grupo nos resultados da lista e, em seguida, clique em **Adicionar**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Ligar uma área de trabalho existente a uma subscrição do Azure
Todas as áreas de trabalho criadas depois de 26 de setembro de 2016 têm de ser associadas a uma subscrição do Azure no momento da criação. As áreas de trabalho criadas antes desta data têm de ser associadas a uma área de trabalho da próxima vez que iniciar sessão. Ao criar a área de trabalho a partir do portal do Azure ou ao ligar a sua área de trabalho a uma subscrição do Azure, o Azure Active Directory é ligado como a sua conta da organização.

![ligar a subscrição do Azure](./media/log-analytics-manage-access/required-link.png)

> [!IMPORTANT]
> Para ligar uma área de trabalho, a sua conta do Azure tem de ter acesso à área de trabalho que pretende ligar.  Por outras palavras, a conta que utiliza para aceder ao portal do Azure tem de ser **a mesma** conta utilizada para aceder à área de trabalho. Se não for, veja [Adicionar um utilizador a uma área de trabalho existente](#add-a-user-to-an-existing-workspace).
>
>

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Para ligar uma área de trabalho a uma subscrição do Azure no portal do OMS
Para ligar uma área de trabalho a uma subscrição do Azure no portal do OMS, o utilizador com sessão iniciada tem de ter uma conta do Azure paga.

1. No portal do OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Subscrição e Plano de Dados do Azure**.
3. Clique no plano de dados que pretende utilizar.
4. Clique em **Guardar**.  
   ![subscrição e planos de dados](./media/log-analytics-manage-access/subscription-tab.png)

O novo plano de dados é apresentado no friso do portal do OMS na parte superior da página Web.

![Friso do OMS](./media/log-analytics-manage-access/data-plan-changed.png)

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
> Se não visualizar a área de trabalho que pretende ligar, a sua subscrição do Azure não tem acesso à área de trabalho que criou com o site do OMS.  Tem de conceder acesso a esta conta a partir do portal da OMS. Para tal, veja [Adicionar um utilizador a uma área de trabalho existente](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Atualizar uma área de trabalho para um plano pago
Existem três tipos de planos de área de trabalho para o OMS: **Gratuito**, **Standard** e **OMS**.  Se estiver no plano *Gratuito*, existe um limite de 500 MB de dados por dia enviados para o Log Analytics.  Se exceder este montante, terá de alterar a sua área de trabalho para um plano pago para evitar deixar de recolher dados para lá deste limite. Pode alterar o tipo de plano em qualquer momento.  Para obter mais informações sobre os preços do OMS, veja [Detalhes dos Preços](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Utilizar a elegibilidade de uma subscrição do OMS
Para utilizar a elegibilidade incluída na compra de OMS E1, OMS E2 OMS ou OMS Add-On for System Center, escolha o plano *OMS* do OMS Log Analytics.

Quando compra uma subscrição do OMS, as elegibilidades são adicionadas ao seu Contrato Enterprise. Qualquer subscrição do Azure criada ao abrigo deste contrato pode utilizar as elegibilidades. Desta forma, pode, por exemplo, ter várias áreas de trabalho que utilizem a elegibilidade das subscrições do OMS.

Para garantir que a utilização de uma área de trabalho é aplicada às suas elegibilidades da subscrição do OMS, tem de:

1. Criar a área de trabalho numa subscrição do Azure que faça parte do Contrato Enterprise que inclui a subscrição do OMS
2. Selecionar o plano *OMS* para a área de trabalho

> [!NOTE]
> Se a área de trabalho tiver sido criada antes de 26 de setembro de 2016 e o seu plano de preços do Log Analytics for o *Premium*, utilizará as elegibilidades do OMS Add-On for System Center. Também pode alterar para o escalão de preços *OMS* para utilizar as elegibilidades.
>
>

As elegibilidades da subscrição do OMS não estão visíveis no portal do Azure nem do OMS. Pode ver as elegibilidades e a utilização no Portal da Empresa.  

Se precisar de alterar a subscrição do Azure à qual está ligada a área de trabalho, pode utilizar o cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) do Azure PowerShell.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Utilizar o Azure Commitment a partir de um Contrato Enterprise
Se não tiver uma subscrição do OMS, paga cada componente do OMS em separado e a utilização aparece na fatura do Azure.

Se tiver uma alocação monetária do Azure na inscrição empresarial à qual as subscrições do Azure estão ligadas, qualquer utilização do Log Analytics será debitada automaticamente na alocação monetária restante.

Se precisar de alterar a subscrição do Azure à qual está ligada a área de trabalho, pode utilizar o cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) do Azure PowerShell.  

### <a name="change-a-workspace-to-a-paid-data-plan"></a>Alterar uma área de trabalho para um plano de dados pago
1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
2. Procure o **Log Analytics** e selecione-o.
3. Verá a lista de áreas de trabalho existentes. Selecione uma área de trabalho.  
4. No painel da área de trabalho, em **Geral**, clique em **Escalão de preço**.  
5. Em **Escalão de preço**, selecione um plano de dados e, em seguida, clique em **Selecionar**.  
    ![selecionar plano](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Quando atualizar a vista no portal do Azure, verá o **Escalão de preço** atualizado com o plano que selecionou.  
    ![plano atualizado](./media/log-analytics-manage-access/manage-access-change-plan04.png)

## <a name="change-how-long-log-analytics-stores-data"></a>Alterar o tempo durante o qual o Log Analytics armazena dados

No escalão de preço Gratuito, o Log Analytics disponibiliza os últimos sete dias de dados.
No escalão de preço Standard, o Log Analytics disponibiliza os últimos 30 dias de dados.
No escalão de preço Premium, o Log Analytics disponibiliza os últimos 365 dias de dados.
Nos escalões de preço Autónomo e OMS, por predefinição, o Log Analytics disponibiliza os últimos 31 dias de dados.

Quando utiliza os escalões de preço Autónomo e OMS, pode manter um máximo de dois anos de dados (730 dias). Os dados armazenados por mais tempo do que a predefinição de 31 dias incorrem numa cobrança de retenção de dados. Para obter mais informações sobre preços, veja o artigo [Custos da utilização excessiva](https://azure.microsoft.com/pricing/details/log-analytics/).

Para alterar o período da retenção de dados:

1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
2. Procure o **Log Analytics** e selecione-o.
3. Verá a lista de áreas de trabalho existentes. Selecione uma área de trabalho.  
4. No painel da área de trabalho, em **Geral**, clique em **Retenção**.  
5. Utilize o controlo de deslize para aumentar ou diminuir o número de dias do período de retenção e, em seguida, clique em **Guardar**
![alterar o período de retenção](./media/log-analytics-manage-access/manage-access-change-retention01.png)

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Alterar uma Organização do Azure Active Directory de uma área de trabalho

Pode alterar a organização do Azure Active Directory de uma área de trabalho. Alterar a Organização do Azure Active Directory permite-lhe adicionar utilizadores e grupos desse diretório à área de trabalho.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Para alterar a Organização do Azure Active Directory de uma área de trabalho

1. Na página Definições no portal do OMS, clique em **Contas** e, em seguida, clique no separador **Gerir Utilizadores**.  
2. Reveja as informações sobre contas de organização e, em seguida, clique em **Alterar Organização**.  
    ![alterar organização](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Introduza as informações de identidade do administrador do seu domínio do Azure Active Directory. Depois, verá uma confirmação a indicar que a área de trabalho está ligada ao seu domínio do Azure Active Directory.  
    ![confirmação da área de trabalho ligada](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## <a name="delete-a-log-analytics-workspace"></a>Eliminar uma área de trabalho do Log Analytics
Ao eliminar uma área de trabalho do Log Analytics, todos os dados relacionados com a mesma são eliminados do serviço do OMS num prazo de 30 dias.

Se for um administrador e existirem vários utilizadores associados à área de trabalho, a associação entre esses utilizadores e a área de trabalho é interrompida. Se os utilizadores estiverem associados a outras áreas de trabalho, podem continuar a utilizar o OMS com essas áreas de trabalho. No entanto, se não estiverem associados a outras áreas de trabalho, terão de criar uma área de trabalho para utilizar o OMS.

### <a name="to-delete-a-workspace"></a>Para eliminar uma área de trabalho
1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
2. Procure o **Log Analytics** e selecione-o.
3. Verá a lista de áreas de trabalho existentes. Selecione a área de trabalho que pretende eliminar.
4. No painel da área de trabalho, clique em **Eliminar**.  
    ![eliminar](./media/log-analytics-manage-access/delete-workspace01.png)
5. No diálogo de confirmação de eliminação da área de trabalho, clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes
* Veja [Ligar computadores Windows ao Log Analytics](log-analytics-windows-agents.md) para adicionar agentes e recolher dados.
* [Adicionar soluções do Log Analytics a partir da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e recolher dados.
* [Configure definições de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md) se a organização utilizar um servidor proxy ou firewall para que os agentes possam comunicar com o serviço Log Analytics.



<!--HONumber=Jan17_HO1-->


